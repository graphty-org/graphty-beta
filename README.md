# graphty-beta

The staging copy of [graphty.app](https://graphty.app), served at
[beta.graphty.app](https://beta.graphty.app): the graphty app, the documentation and the Storybooks
as of the newest green build of
[graphty-monorepo](https://github.com/graphty-org/graphty-monorepo)'s master branch.

This repository holds no source. Each [release](https://github.com/graphty-org/graphty-beta/releases)
is one build, tagged `build-<commit>` after the graphty-monorepo commit it was built from, with the
site as two assets: `site.tar.gz` (everything but the hosted datasets) and `data.tar.gz`
(`/data/graph-samples/v1/`). The newest 30 builds are kept, plus the one graphty.app serves (the last one promoted).

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
4. **The GitHub App graphty-monorepo writes with.** graphty-monorepo makes the releases here, asks
   this repository to deploy them, and records which build graphty.app serves in its own Actions
   variable `PROMOTED_BUILD`, all with an installation token of one GitHub App. App tokens are made
   fresh for every run, so nothing expires.
   1. In the organization's settings, Developer settings, GitHub Apps, New GitHub App: any name (for
      example `graphty-pages`), any homepage URL, Webhook off, and two repository permissions:
      "Contents: Read and write" and "Variables: Read and write". Only this organization can install it.
   2. On the app's page, Generate a private key (a `.pem` file downloads), and note the App ID.
   3. Install it on the organization, "Only select repositories": `graphty-org/graphty-beta` and
      `graphty-org/graphty-monorepo`.
   4. Store both in graphty-monorepo as Actions secrets, then delete the `.pem` file:
      `gh secret set GRAPHTY_PAGES_APP_ID -R graphty-org/graphty-monorepo` (the App ID) and
      `gh secret set GRAPHTY_PAGES_APP_KEY -R graphty-org/graphty-monorepo < the-key.pem`.

   Until both secrets exist, graphty-monorepo's "Deploy to beta.graphty.app" runs fail and say so.
5. **The first promotion.** Until a build has been promoted, `PROMOTED_BUILD` is unset: no build is
   deleted, and graphty.app keeps what it served before. Promote one (above) to start.
