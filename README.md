[![Gitpod Ready-to-Code](https://img.shields.io/badge/Gitpod-Ready--to--Code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/aws-containers/appmeshworkshop)

# appmeshworkshop

**End of support notice**: On September 30, 2026, AWS will discontinue support
for AWS App Mesh. After September 30, 2026, you will no longer be able to access
the AWS App Mesh console or AWS App Mesh resources. For more information, visit
this [blog post](https://aws.amazon.com/blogs/containers/migrating-from-aws-app-mesh-to-amazon-ecs-service-connect/).

**Warning**: Please note that this GitHub repository has been archived. As a
result, no further issues or pull requests will be reviewed or addressed.

---

https://appmeshworkshop.com

This is how I set up my environment:
(I am using gitpod.io for editing)

1. fork the repo to your own github account
2. prepend `gitpod.io#` to the beginning of your github url. Mine becomes: `https://gitpod.io#github.com/aws-containers/appmeshworkshop`
3. once gitpod has started, in the terminal, run `npm install && npm run theme`
   This will install the dependencies and clone the theme submodule.

From here, you can use the online IDE to edit /content/chapter/filename.md...
If you want to preview your edits, in the terminal, run:
`npx hugo server`.
That will start the local hugo server.

A dialog box will pop up telling you "A service is listening on port 1313, but is not
exposed." -- press the expose button. After that, choose "open browser" to get a new
tab with your preview site. As you save edits, this tab should refresh.

When you're happy with your edits, commit, push, and open a pull request to the upstream
repo's main branch. Once merged, the preview site (linked above) will be refreshed.
