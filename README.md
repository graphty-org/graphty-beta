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

## Setup (once, by an organization owner)

1. **DNS.** At graphty.app's DNS host, add a `CNAME` record: name `beta` (the full name is
   `beta.graphty.app`), value `graphty-org.github.io`. Only the organization name, no repository path.
   This repository's Pages settings already name `beta.graphty.app` as the custom domain.
2. **Domain verification.** In the organization's settings, Pages, "Add a domain": `graphty.app`.
   GitHub shows a `TXT` record named `_github-pages-challenge-graphty-org.graphty.app` with a value;
   add it at the DNS host and press Verify. A verified domain covers its immediate subdomains, so
   `beta.graphty.app` is covered too, and no other account can claim it.
3. **HTTPS.** Once the CNAME resolves and GitHub has issued the certificate (minutes to an hour), turn
   on "Enforce HTTPS" in this repository's Settings, Pages.
4. **The token graphty-monorepo writes releases with.** A fine-grained personal access token
   (github.com, Settings, Developer settings, Fine-grained tokens, Generate new token): resource owner
   `graphty-org`, repository access "Only select repositories", `graphty-org/graphty-beta`, and the
   one permission "Contents: Read and write". Store it in graphty-monorepo as the Actions secret
   `GRAPHTY_BETA_TOKEN`: `gh secret set GRAPHTY_BETA_TOKEN -R graphty-org/graphty-monorepo`. Until it
   exists, graphty-monorepo's "Deploy to beta.graphty.app" runs fail and say so.
