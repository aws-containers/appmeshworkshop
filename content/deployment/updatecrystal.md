---
title: "Update the Crystal code"
date: 2018-09-18T16:01:14-05:00
weight: 5
---

In a canary release deployment, total traffic is separated at random into a production release and a canary release with a pre-configured ratio. Typically, the canary release receives a small percentage of the traffic and the production release takes up the rest. The updated service features are only visible to the traffic through the canary. You can adjust the canary traffic percentage to optimize test coverage or performance.

Remember, in App Mesh, every version of a service is ultimately backed by actual running code somewhere (Fargate tasks in the case of Crystal), so each service will have it's own virtual node representation in the mesh that provides this conduit.

Additionaly, there is the physical deployment of the application itself to a compute environment. Both Crystal deployments will run on ECS using the Fargate launch type. Our goal is to test with a portion of traffic going to the new version, ultimately increasing to 100% of traffic.

* Create the following patch in your **ecsdemo-crystal** project.

```bash
cat <<-'EOF' > ~/environment/ecsdemo-crystal/add_time_ms.patch
From fc6ce0c76b394928c3008f128efd2fcaf1ff45c3 Mon Sep 17 00:00:00 2001
From: 
Date:
Subject: [PATCH] Add epoch timestamp and canary hash

---
 code_hash.txt | 2 +-
 src/server.cr | 4 +++-
 2 files changed, 4 insertions(+), 2 deletions(-)

diff --git a/code_hash.txt b/code_hash.txt
index 510eef0..dbd1857 100644
--- a/code_hash.txt
+++ b/code_hash.txt
@@ -1 +1 @@
-ed106ce
+CANARY
diff --git a/src/server.cr b/src/server.cr
index c6b1403..2f6e8c0 100644
--- a/src/server.cr
+++ b/src/server.cr
@@ -1,5 +1,6 @@
 require "logger"
 require "http/server"
+require "time"

 log = Logger.new(STDOUT)
 log.level = Logger::DEBUG
@@ -24,8 +25,9 @@ server = HTTP::Server.new(
     HTTP::CompressHandler.new,
     ]) do |context|
       if context.request.path == "/crystal" || context.request.path == "/crystal/"
+        epoch_ms = Time.now.epoch_ms
         context.response.content_type = "text/plain"
-        context.response.print "Crystal backend: Hello! from #{az_message} commit #{code_hash}"
+        context.response.print "Crystal backend: Hello! from #{az_message} commit #{code_hash} at #{epoch_ms}"
       elsif context.request.path == "/crystal/api" || context.request.path == "/crystal/api/"
         context.response.content_type = "application/json"
         context.response.print %Q({"from":"Crystal backend", "message": "#{az_message}", "commit": "#{code_hash.chomp}"})
-- 
2.32.0


EOF
```

* First, take a look at what changes are in the patch.

```bash
git apply --stat ~/environment/ecsdemo-crystal/add_time_ms.patch
```

* Run **git apply** to apply the patch.

```bash
git -C ~/environment/ecsdemo-crystal apply add_time_ms.patch
```

* Build the container

```bash
CRYSTAL_ECR_REPO=$(jq < cfn-output.json -r '.CrystalEcrRepo')

aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin $CRYSTAL_ECR_REPO

docker build -t crystal-service ~/environment/ecsdemo-crystal
docker tag crystal-service:latest $CRYSTAL_ECR_REPO:epoch
docker push $CRYSTAL_ECR_REPO:epoch
```
