# graphty-site

[graphty.app](https://graphty.app): the graphty app, the documentation, the Storybooks and the hosted
datasets that the published `@graphty` packages download, as of the build the owner last promoted.

This repository holds no source. `site/` is the whole site, and each commit is one change to what
graphty.app serves, so the history is the history of promotions:

- **Promote a build.** Every green build of
  [graphty-monorepo](https://github.com/graphty-org/graphty-monorepo)'s master branch is deployed to
  [beta.graphty.app](https://beta.graphty.app), graphty-monorepo's own GitHub Pages site. To put one on
  graphty.app: in graphty-monorepo, Actions, "Promote a beta build to graphty.app", Run workflow, with
  the build's commit (or `newest`). The builds of the last 90 days can be promoted. It commits that
  build to `site/` here, keeping the hosted datasets graphty.app already has, and `deploy.yml` deploys it.
- **Roll back.** Promote an earlier build, or revert a commit here.
- **Hosted datasets.** graphty-monorepo's release train commits the datasets of the commit it is about
  to publish to `site/data/` before it publishes, so a released package never names a dataset
  graphty.app lacks. They are append-only: nothing ever removes one.

## Setup (once, by an organization owner)

1. **The GitHub App graphty-monorepo writes with.** graphty-monorepo commits here with an installation
   token of one GitHub App, made fresh for every run, so nothing expires.
   1. In the organization's settings, Developer settings, GitHub Apps, New GitHub App: any name (for
      example `graphty-pages`), any homepage URL, Webhook off, and the repository permission
      "Contents: Read and write". Only this organization can install it.
   2. On the app's page, Generate a private key (a `.pem` file downloads), and note the App ID.
   3. Install it on the organization, "Only select repositories": `graphty-org/graphty-site`.
   4. Store both in graphty-monorepo as Actions secrets, then delete the `.pem` file:
      `gh secret set GRAPHTY_PAGES_APP_ID -R graphty-org/graphty-monorepo` (the App ID) and
      `gh secret set GRAPHTY_PAGES_APP_KEY -R graphty-org/graphty-monorepo < the-key.pem`.
2. **The first promotion**, then **the domain move**: the steps are on
   [graphty-monorepo issue #3267](https://github.com/graphty-org/graphty-monorepo/issues/3267). Their
   order keeps graphty.app serving throughout.
