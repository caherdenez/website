# Website Recovery and Deployment Plan

## Goal

Publish the Hugo website at `https://cesarh.co/` using GitHub Pages while
removing exposed personal information from the repository and restoring the
custom-domain configuration safely.

## Current State

- The repository is public.
- `feat/hugo` contains the current minimal Hugo implementation.
- The default branch is `master`, which does not yet contain the Hugo site.
- GitHub Pages is disabled or has no active configuration.
- The remote historical `gh-pages` branch was deleted after an old generated CV
  with private contact information was found in its history.
- The remote `feat/astro` branch was deleted because its third-party asset
  licenses and provenance had not been verified.
- `cesarh.co` points to legacy GitHub Pages IP addresses.
- `www.cesarh.co` has no DNS record.
- A pinned GitHub Actions deployment workflow has been added and validated on
  `feat/hugo`, but has not yet been promoted to `master`.

## Progress

- [x] Delete the remote `gh-pages` branch.
- [x] Delete the remote `feat/astro` branch.
- [x] Add and locally validate the Hugo Pages workflow.
- [ ] Request removal of cached sensitive content from GitHub Support.
- [ ] Merge `feat/hugo` into `master`.
- [ ] Verify the custom domain and enable GitHub Pages.
- [ ] Replace legacy DNS records and configure `www`.
- [ ] Enable HTTPS and validate production.

## Phase 1: Remove Exposed Personal Information

1. Preserve a private copy of the historical site only if it is still needed.
2. Delete the remote `gh-pages` branch because its current and historical
   commits contain a residential address, telephone numbers, and other personal
   contact information. Completed on 2026-08-12.

   ```sh
   git push origin --delete gh-pages
   ```

3. Contact GitHub Support and request removal of cached views for commit
   `af24c54afb45c628d7b4bb965418372b15590526` and its `cv/index.html` file.
4. Delete `feat/astro` unless the licenses and provenance of its imported
   template, font, image, and logo assets can be verified. The remote branch was
   deleted on 2026-08-12.
5. Consider using a GitHub-provided `noreply` address for future commit metadata.
   Rewriting existing commit authors is optional because the email address is
   not a credential.

## Phase 2: Prepare Hugo Deployment

1. Install Hugo locally and verify the site.

   ```sh
   hugo server
   hugo --minify
   ```

2. Add `.github/workflows/hugo.yaml` with a GitHub Pages workflow. Completed and
   validated locally on 2026-08-12. The workflow:

   - Runs on pushes to `master` and supports manual dispatch.
   - Checks out the repository.
   - Installs a pinned Hugo version compatible with `hugo.toml`.
   - Runs `hugo --minify`.
   - Uploads `public/` with `actions/upload-pages-artifact`.
   - Deploys it with `actions/deploy-pages`.
   - Grants only `contents: read`, `pages: write`, and `id-token: write`.

3. Keep `public/` ignored. Generated files should be deployed as an Actions
   artifact rather than committed to Git.
4. Validate the workflow and site changes.

   ```sh
   hugo --minify
   git diff --check
   ```

## Phase 3: Promote Hugo to the Production Branch

1. Open a pull request from `feat/hugo` to `master` and review the complete diff.
2. Merge the pull request after the build succeeds.
3. Confirm that `master` contains the Hugo source and deployment workflow.
4. Do not publish directly from `feat/hugo` or from Hugo's source directory.

## Phase 4: Verify the Domain in GitHub

1. Open the personal GitHub account settings.
2. Go to **Settings > Pages** and add `cesarh.co` as a verified domain.
3. Copy the TXT record provided by GitHub. It will resemble:

   ```text
   Type: TXT
   Host: _github-pages-challenge-caherdenez
   Value: value-provided-by-github
   ```

4. Add the TXT record at the DNS provider.
5. Wait for propagation and complete verification in GitHub.
6. Keep the verification TXT record after verification.

## Phase 5: Enable GitHub Pages

1. Open the repository's **Settings > Pages** page.
2. Set **Build and deployment > Source** to **GitHub Actions**.
3. Run the Hugo workflow or push the deployment commit to `master`.
4. Confirm that the workflow's build and deploy jobs succeed.
5. Set the repository's custom domain to `cesarh.co` and save it.

When deploying with GitHub Actions, GitHub manages the custom domain through
the Pages settings. A tracked `CNAME` file is not required.

## Phase 6: Update DNS

Remove the legacy apex records:

```text
A  @  192.30.252.153
A  @  192.30.252.154
```

Add GitHub's current apex records:

```text
A  @  185.199.108.153
A  @  185.199.109.153
A  @  185.199.110.153
A  @  185.199.111.153
```

Add the `www` alias:

```text
CNAME  www  caherdenez.github.io
```

Optional IPv6 records:

```text
AAAA  @  2606:50c0:8000::153
AAAA  @  2606:50c0:8001::153
AAAA  @  2606:50c0:8002::153
AAAA  @  2606:50c0:8003::153
```

Do not create wildcard DNS records. DNS propagation may take up to 24 hours.

## Phase 7: Validate Production

Check DNS:

```sh
dig +short A cesarh.co
dig +short CNAME www.cesarh.co
dig +short TXT _github-pages-challenge-caherdenez.cesarh.co
```

Check HTTP and HTTPS:

```sh
curl -I http://cesarh.co/
curl -I https://cesarh.co/
curl -I https://www.cesarh.co/
```

Confirm that:

- `https://cesarh.co/` serves the Hugo landing page.
- `www.cesarh.co` redirects to the canonical apex domain.
- CSS and internal links load over HTTPS.
- GitHub Pages reports a healthy DNS check.
- The certificate covers the custom domain.

After certificate provisioning completes, enable **Enforce HTTPS** in the
repository's Pages settings.

## Phase 8: Post-Deployment Checks

1. Confirm that the historical CV is no longer reachable through repository
   branches or GitHub Pages.
2. Enable GitHub secret scanning and push protection if available for the
   repository.
3. Add future professional facts to `../source/` before publishing them on the
   website.
4. Keep deployments reproducible through GitHub Actions and do not restore the
   generated `gh-pages` workflow used by the old Lektor site.

## Completion Criteria

- The public repository has no reachable branch containing the historical CV.
- `master` contains the reviewed Hugo implementation.
- The GitHub Actions deployment completes successfully.
- `cesarh.co` and `www.cesarh.co` resolve through current GitHub Pages records.
- HTTPS is valid and enforced.
- The production site displays the current Hugo landing page.
