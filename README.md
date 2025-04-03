# Auto Update Bluesky Handle GitHub Action

This GitHub Action updates your [Bluesky](https://bsky.app/) handle based on your GitHub commit activity. For example:

- `1.commit.today.example.com`
- `30.commits.today.example.com`
- `1.day.since.last.commit.example.com`
- `42.days.since.last.commit.example.com`

It does this by:

1. Checking GitHub API for recent commits (also checks private repositories if a GitHub token is provided)
2. Getting current Bluesky handle via Bluesky API and comparing against proposed change -> Skips remaining steps if no change required
3. Creating DNS TXT record via Cloudflare API
4. Pushing changed handle back to Bluesky via API
5. (Optionally) cleaning up old TXT records

**Warning: This is probably a bad idea, and created as a learning exercise! @ mentions to the root domain still seem to work, and visits to `bsky.app/profile/<old handles>` still seem to work if you don't cleanup old TXT records, but still: Use at your own risk. There's an option to cleanup unused records if you a) want to be tidy, and b) don't want anyone to find you.**

Issues/PRs welcome, but I have no plans to spend more time on this. If you use this to do something cool let me know [@syslog.sh](https://bsky.app/profile/syslog.sh) _...if I haven't broken the link..._

---

## Repository Secrets

`From this forked repo -> Settings -> Secrets and variables -> Actions -> New repository secret`

| Name                   | Purpose                                            |
| ---------------------- | -------------------------------------------------- |
| `GH_TOKEN`             | **Optional**. GitHub token to access private repos |
| `BLUESKY_EMAIL`        | Your Bluesky login email                           |
| `BLUESKY_PASSWORD`     | Your Bluesky app password                          |
| `CLOUDFLARE_API_TOKEN` | Cloudflare API token with DNS edit access          |
| `CLOUDFLARE_ZONE_ID`   | Cloudflare Zone ID for your domain                 |

Important: Scope tokens as narrowly as possible.

GitHub (optional) Token (Settings -> Developer Settings -> Personal access tokens):

- For Classic token: `Repo` & `Read:User`
- For Fine-grained token: Specify repos, or select all, then: `Contents: Read-only` (`Metadata: Read-only` is required but always set)

CloudFlare Token (Profile -> API Tokens -> Create Tokens):

- If using a template, choose `Edit zone DNS`
- Ensure you set only the domain you are editing for the handle

CloudFlare Zone ID: In CF dashboard, choose your domain, and zone ID should be listed on the page, under API

---

## Repository Variables

`From this forked repo -> Settings -> Secrets and variables -> Actions -> Variables (tab) -> New repository variable`

| Name                                    | Example                | Description                                                                                                                                                                          |
| --------------------------------------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `MY_DOMAIN`                             | `example.com`          | Your domain name                                                                                                                                                                     |
| `GH_USERNAME`                           | `your-github-username` | GitHub username to check activity for                                                                                                                                                |
| `KEEP_ATPROTO_RECORDS`                  | `_atproto.example.com` | **Optional**. Comma-separated list of `_atproto.*` records to preserve. If you're setting `CLEANUP_TXT_RECORDS` to `true`, you probably want to at least add `_atproto.<yourdomain>` |
| `TEST_RUN` **Default:True**             | `true` or `false`      | **Optional (technically...)**. If `true` or not set, prints actions without making changes. Run this in test mode first, to ensure there are no auth/similar errors!                 |
| `CLEANUP_TXT_RECORDS` **Default:False** | `true` or `false`      | **Optional**. If `true`, deletes unused `_atproto.*` TXT records                                                                                                                     |

---

## Schedule

- A cron schedule (default: every 12 hours)
- Manual dispatch via the Actions tab

---

## MIT License

MIT License

Copyright (c) 2025 sec-ml

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
