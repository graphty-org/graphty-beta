# graphty-beta

The staging copy of [graphty.app](https://graphty.app), served at
[beta.graphty.app](https://beta.graphty.app): the graphty app, the documentation and the Storybooks
as of the newest green build of
[graphty-monorepo](https://github.com/graphty-org/graphty-monorepo)'s master branch.

This repository holds no source. Each [release](https://github.com/graphty-org/graphty-beta/releases)
is one build, tagged `build-<commit>` after the graphty-monorepo commit it was built from, with the
site as two assets: `site.tar.gz` (everything but the hosted datasets) and `data.tar.gz`
(`/data/graph-samples/v1/`). The newest 30 builds are kept, plus the one graphty.app serves.

- **Deploys to beta.graphty.app** happen on their own: graphty-monorepo's `deploy-pages.yml` makes the
  release and asks this repository's `deploy.yml` to publish it. To put an earlier build back on
  beta, run `deploy.yml` by hand (Actions, "Deploy beta.graphty.app", Run workflow) with its tag.
- **graphty.app** changes only when someone promotes a build: in graphty-monorepo, Actions, "Promote
  a beta build to graphty.app", Run workflow, with the build's commit (or `newest`). Rolling back is
  promoting an earlier build.
