# Reproducible builds

Public, credential-free verification that Nova's deployed artifacts are built
from their public source. If you want to check what actually runs, this is the
place to start.

## What is verified here

**Installer site (`novaproxy.online`, including `/install`)**
[`verify-installer.yml`](.github/workflows/verify-installer.yml) checks out the
public source at [`iiviirv/irnova-site`](https://github.com/iiviirv/irnova-site),
runs the same `npm run build` that Cloudflare Pages runs on deploy, and records a
SHA-256 of the build output. Cloudflare Pages deploys that same build from the
connected Git repo, so the site you load is produced from public source.

Reproduce it yourself:

```bash
git clone https://github.com/iiviirv/irnova-site
cd irnova-site
npm ci
npm run build
# hash the dist tree and compare with the run summary above
find dist -type f -print0 | sort -z | xargs -0 sha256sum | sha256sum
```

**Edge worker (`Nova-Proxy`)**
The Cloudflare Worker source lives in
[`IRNova/Nova-Proxy`](https://github.com/IRNova/Nova-Proxy) as a single complete,
unminified `worker.js`. That repository runs its own
[`verify.yml`](https://github.com/IRNova/Nova-Proxy/blob/main/.github/workflows/verify.yml)
which publishes the SHA-256 of `worker.js` on every commit, so you can pin a
version and confirm the file you review is the file that is deployed.

## Why it lives in this repo

These checks run on the IRNova organization's runners so the verification stays
reliable and green, independent of any one contributor's account. None of the
workflows here use secrets or tokens. They only read public source and publish
hashes.

## Reporting a security issue

Please report privately first via [@irnova_proxy](https://t.me/irnova_proxy) on
Telegram, or open a GitHub security advisory on the relevant repository. We read
every report and we are glad to fix real issues. We do not condone harassment of
users, contributors, or reporters.

Built by the [Nova Proxy group](https://github.com/IRNova).
