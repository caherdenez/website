# Website Repository and Deployment Audit

Audit date: 2026-08-12

## Remediation Status

Updated: 2026-08-12

- Resolved: deleted the remote `gh-pages` branch containing the historical CV.
- Resolved: deleted the remote `feat/astro` branch with unverified third-party
  assets.
- Resolved locally: added and validated a pinned Hugo GitHub Pages workflow on
  `feat/hugo`.
- Pending: merge `feat/hugo` into `master`, enable GitHub Pages, configure the
  custom domain, update DNS, provision HTTPS, and validate production.
- External follow-up: request removal of cached views of the sensitive historical
  commit and path from GitHub Support.

The findings below describe the state observed during the audit. A deleted branch
can remain available in clones, forks, caches, pull-request refs, or direct commit
views, so branch deletion alone does not guarantee complete erasure.

## Scope

This audit reviewed the locally reachable Git history and branches of
`caherdenez/website`, the repository's GitHub Pages configuration, historical
deployments, custom-domain records, and current HTTP behavior.

Branches included in the review:

- `master`
- `feat/hugo`
- `feat/astro`
- `gh-pages`

The review looked for:

- Credentials, tokens, private keys, and secret-bearing files.
- Personal or private information.
- Generated artifacts and unexpectedly large files.
- Third-party asset and licensing risks.
- GitHub Pages workflows and settings.
- `CNAME` history and current DNS configuration.
- HTTP and HTTPS behavior for the production domain.

## Executive Summary

The repository is already public. No recognizable credentials, access tokens,
private keys, `.env` files, or database connection strings were found in the
reachable history. However, the historical `gh-pages` branch contained an old
generated CV with sensitive personal contact and location information at the time
of the audit.

The website is currently unavailable because GitHub Pages has no active
configuration and `cesarh.co` still points to legacy GitHub Pages addresses.
The Hugo implementation is valid and now has a locally validated deployment
workflow, but it has not yet been promoted to the default branch.

The historical deployment branch has now been removed. The repository should not
be considered fully sanitized until cached sensitive content is addressed.

## Findings

### Critical: Personal Information in the Historical Deployment Branch

At the time of the audit, the tip of `origin/gh-pages` contained a generated CV
with sensitive personal information, including a residential address, telephone
numbers, a personal email address, and a legacy messaging identifier.

Evidence:

```text
Branch: origin/gh-pages
Commit: af24c54afb45c628d7b4bb965418372b15590526
Path: cv/index.html
First affected commit: 38360f0bfce869e7e3e27d604ac0310b75e2c922
```

The information exists at the branch tip and in earlier commits. Replacing the
deployed page alone would not remove it from the public Git history.

Recommended action:

1. Preserve a private copy only if needed.
2. Delete or rewrite the public `gh-pages` history.
3. Request cache removal from GitHub Support for the affected commit and path.
4. Confirm that the content is no longer reachable through branches, commit
   pages, or GitHub Pages.

### High: GitHub Pages Is Disabled

The repository currently has no active GitHub Pages site.

Evidence:

```text
GitHub Pages API: 404 Not Found
https://caherdenez.github.io/website/: GitHub 404
http://cesarh.co/: GitHub 404
GitHub Actions workflows: 0
```

Historical deployment records exist, but the most recent successful Pages
deployment was in 2020 and used the old 2016 `gh-pages` content.

Recommended action:

- Configure GitHub Pages to use GitHub Actions.
- Add a Hugo build and Pages deployment workflow.
- Deploy only after the sensitive historical branch has been addressed.

### High: Apex DNS Uses Legacy GitHub Pages Addresses

The apex domain currently resolves to old GitHub Pages IP addresses:

```text
cesarh.co A 192.30.252.153
cesarh.co A 192.30.252.154
```

GitHub's current documented apex records are:

```text
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

Recommended action:

- Configure and verify the custom domain in GitHub first.
- Replace the legacy apex records with the current records.
- Avoid wildcard DNS records.

### High: HTTPS Is Not Valid for the Custom Domain

HTTPS currently does not present a valid certificate for `cesarh.co`. This is
consistent with Pages being disabled or the custom domain being detached from
the repository.

Recommended action:

- Restore Pages and its custom-domain configuration.
- Wait for GitHub to provision the certificate after DNS validation.
- Enable **Enforce HTTPS** only after the certificate is available.

### Medium: The `www` Subdomain Is Not Configured

`www.cesarh.co` has no `A`, `AAAA`, or `CNAME` record.

Recommended action:

```text
CNAME  www  caherdenez.github.io
```

With the apex domain configured as canonical in GitHub Pages, GitHub can redirect
the `www` variant to `cesarh.co`.

### Medium: The Hugo Site Has No Deployment Workflow

The `feat/hugo` branch contains a valid minimal Hugo site but no GitHub Actions
workflow. Its generated `public/` directory is intentionally ignored, so the
source branch cannot be served directly as a finished static site.

Relevant files:

```text
hugo.toml
layouts/home.html
static/css/main.css
```

Recommended action:

- Add `.github/workflows/hugo.yaml`.
- Build the site with a pinned Hugo version.
- Upload `public/` using `actions/upload-pages-artifact`.
- Deploy using `actions/deploy-pages`.
- Grant only `contents: read`, `pages: write`, and `id-token: write`.

### Medium: The Production Source Is Not on the Default Branch

The default branch remains `master`, while the Hugo implementation is on
`feat/hugo`.

Evidence:

```text
Default branch: master
master: a25212b94e083309fab21eb21524433388733d03
feat/hugo: ddc32ca28fc54754ebec120c9ddf87ee64b0638a
```

Recommended action:

- Review and merge `feat/hugo` into `master` before enabling the production
  deployment trigger.

### Medium: Third-Party Asset and Repository-Size Risk on `feat/astro`

The historical Astro branch adds approximately 81 MB across more than 180 files.
It includes a large font, template images, screenshots, logos, and other imported
assets. Some repository metadata attributes copyright to a third party.

The largest tracked file is approximately 25 MB:

```text
feat/astro: public/fonts/LXGWWenKai-Regular.ttf
```

No file exceeds GitHub's 100 MB hard limit, but the ownership and redistribution
rights for imported assets should be verified before retaining the branch in a
public repository.

Recommended action:

- Verify licenses and provenance for the template, font, images, and logos.
- Remove the remote branch if it is no longer needed.
- Do not copy its assets into the Hugo implementation without verification.

### Medium: Personal Email in Commit Metadata

Human-authored commits expose a personal email address in author and committer
metadata. This is not a credential, but it is permanently visible wherever those
commits remain reachable.

Recommended action:

- Configure a GitHub-provided `noreply` address for future commits if the personal
  address should not be public.
- Rewrite historical authors only if reducing that exposure justifies changing
  published commit hashes.

### Low: Old Generated Frontend Assets

The historical `gh-pages` branch contains generated HTML, Bootstrap assets,
fonts, images, and legacy JavaScript libraries. These files are stale and should
not be reused for the Hugo deployment.

The current Hugo branch correctly ignores generated output:

```text
public/
resources/_gen/
.hugo_build.lock
```

## Secret Scan Results

No reachable tracked content matched recognizable patterns for:

- AWS access keys.
- GitHub, GitLab, Slack, Stripe, or OpenAI tokens.
- Private keys or certificates.
- Embedded basic-auth credentials.
- Database connection strings.
- Password assignments.
- `.env` files.
- Package registry credentials.
- Terraform state.
- Kubernetes configuration.
- Database dumps.

No installed dependency directories such as `node_modules/` or Python virtual
environments are tracked.

This result reduces risk but does not prove that arbitrary custom-format or
high-entropy secrets are absent.

## Historical Deployment Model

The old website was built with Lektor and pushed as generated output to an
orphan-style `gh-pages` branch. Its commits were authored by:

```text
Lektor Bot <bot@getlektor.com>
```

The branch contains a root `CNAME` with:

```text
cesarh.co
```

There is no corresponding Lektor source project or deployment script in the
reachable repository history. The current Hugo deployment should not reproduce
this manual generated-branch model.

## Recommended Target Architecture

- Source and default branch: `master`.
- Site generator: Hugo.
- Build trigger: push to `master` or manual workflow dispatch.
- Build output: ephemeral `public/` directory.
- Artifact: `actions/upload-pages-artifact`.
- Deployment: `actions/deploy-pages`.
- Custom domain: configured in GitHub Pages settings as `cesarh.co`.
- DNS: current GitHub Pages apex addresses plus `www` CNAME.
- Generated output: excluded from Git.

## Remediation Priority

1. Remove or rewrite the sensitive `gh-pages` branch.
2. Request removal of cached sensitive content.
3. Decide whether to retain `feat/astro` after its license review.
4. Add and validate the Hugo Pages workflow.
5. Merge `feat/hugo` into `master`.
6. Verify `cesarh.co` in GitHub.
7. Enable Pages with GitHub Actions.
8. Update apex and `www` DNS records.
9. Configure the custom domain and wait for its certificate.
10. Enable HTTPS and validate production behavior.

The implementation sequence and verification commands are documented in
`plan.md`.

## Audit Limitations

- The audit covers branches and commits reachable from the current clone and
  remote references. Deleted branches, force-pushed history, forks, pull request
  refs, Actions artifacts, caches, and releases may contain additional material.
- Dedicated tools such as Gitleaks, TruffleHog, or detect-secrets were not
  available. The secret review used Git-history searches and manual inspection.
- Binary files were assessed through path, size, repository context, and visible
  metadata. Hidden binary content cannot be ruled out.
- Removing a branch does not remove copies already cloned, forked, indexed, or
  cached by third parties.

## Conclusion

The minimal Hugo implementation is suitable for a public professional website,
but the repository's historical deployment branch creates a material privacy
risk that should be resolved before restoring GitHub Pages. The domain outage is
explained by disabled Pages configuration and stale DNS, not by the Hugo site
itself.
