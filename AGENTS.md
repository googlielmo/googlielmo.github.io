# Repository Guidelines

## Project Structure & Module Organization
- Core site config is in `_config.yml`; theme behavior is provided by `jekyll-theme-chirpy` (see `Gemfile`).
- Content lives in `_posts/` (blog posts) and `_tabs/` (top-level pages like `about.md`, `tags.md`).
- Draft content lives in `_drafts/` and is only visible when Jekyll runs with `--drafts`.
- Site data and metadata live in `_data/`; custom Ruby hooks live in `_plugins/`.
- Frontend assets are under `assets/` (including `assets/lib/`).
- Local theme overrides live under `_layouts/` and `_includes/`.
- Tooling scripts are in `tools/`:
  - `tools/run.sh` for local serving
  - `tools/test.sh` for production build + link checks
  - `tools/new-draft.sh` for creating a draft with starter front matter
  - `tools/publish-draft.sh` for promoting a draft into `_posts/` with a valid Jekyll filename

## Build, Test, and Development Commands
- `bundle install` installs Ruby dependencies from `Gemfile`.
- `bash tools/run.sh` starts local Jekyll server with live reload (`127.0.0.1` by default).
- `bash tools/run.sh -H 0.0.0.0` serves on all interfaces (useful in containers).
- `bash tools/run.sh -d` includes `_drafts/` in local preview and shows draft markers added by the local layout overrides.
- `bash tools/run.sh -p` runs with `JEKYLL_ENV=production`.
- `bash tools/new-draft.sh "My New Post"` creates `_drafts/my-new-post.md` with starter front matter.
- `bash tools/publish-draft.sh my-new-post` moves `_drafts/my-new-post.md` to `_posts/YYYY-MM-DD-my-new-post.md` and updates the publish timestamp in front matter.
- `bash tools/test.sh` builds into `_site` and runs `htmlproofer` (external links disabled).

## Coding Style & Naming Conventions
- Follow `.editorconfig`: UTF-8, LF endings, trailing newline, 2-space indentation.
- Keep Markdown concise and structured with clear headings.
- Use Jekyll post naming: `_posts/YYYY-MM-DD-title.md`.
- Use draft naming without the date under `_drafts/` (example: `_drafts/my-new-post.md`).
- Prefer lowercase, hyphenated filenames for pages/posts (example: `my-new-post.md`).

## Testing Guidelines
- Primary validation is `bash tools/test.sh` (build + `htmlproofer`).
- Run tests before opening PRs, especially after config/plugin/layout changes.
- For content-only changes, at minimum run a local preview via `bash tools/run.sh`.

## Remote and Publication Authorization
- Treat analysis, editing, committing, remote writes, and publication as separate authorization tiers. A request to organize, review, draft, prepare, or implement a plan does not authorize a push or publication.
- A local draft ends in `_drafts/<slug>.md` by default. Preview it with `--drafts` and leave it there until the user has reviewed the exact copy.
- "Reviewed" means reviewed by the user in its exact final form. Agent review, tests, CI, or approval to use a tool does not count as user review.
- Any content or front-matter change after review invalidates that review. This includes changes made by `tools/publish-draft.sh`, such as its timestamp and draft-state updates; show the resulting `_posts/` file for review before any deployment-triggering push.
- First-person copy that speaks as the user (for example, "I released") requires explicit user approval of the exact wording before publication.
- Running `tools/publish-draft.sh` is a local file operation, not authorization to publish. Moving a draft to `_posts/` requires a fresh, explicit "clear to publish" confirmation, and the resulting exact post requires review before requesting confirmation to deploy it.
- A push to the current private upstream requires an explicit push request and verification that the checked-out branch tracks that exact remote and branch.
- Treat an `origin/main` push containing any path not ignored by `.github/workflows/pages-deploy.yml` as a public-deployment-triggering write: it builds the private source and force-pushes generated output to `public/main`. Immediately before such a push, obtain fresh confirmation showing the exact command, remote URL, branch, commit range, and downstream public deployment effect.
- A push containing only workflow-ignored paths (currently `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `.gitignore`, `README.md`, and `LICENSE`) is still a remote write and still requires an explicit push request, even though it does not trigger the Pages workflow.
- Public, cross-repository, workflow-dispatching, tag, release, or other deployment-triggering writes always require fresh confirmation immediately before execution. Plans can describe these operations but never authorize crossing their manual confirmation gates.
- Never push to a public repository unless repository instructions explicitly designate it and the checked-out branch tracks that exact public remote and branch. Do not bypass this safeguard with an ad hoc refspec, `HEAD:<branch>`, `--set-upstream`, or `pushRemote` override.
- Sandbox, credential, connector, or tool approval grants technical capability only; it never supplies user authorization for a remote write or publication.

## Git Remotes, Branches, and Draft Publishing
- This repo uses two remotes:
  - `origin` (`googlielmo/gwz-blog`): private development remote.
  - `public` (`googlielmo/googlielmo.github.io`): GitHub Pages publish repo containing built site output.
- `_drafts/` is tracked in this private repository, but drafts stay out of the public site because Jekyll only includes them when run with `--drafts`.
- `.git/info/exclude` includes `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md` for local/private workflow metadata.
- These three files may be present in private `origin/*`, but they MUST NOT be pushed to `public/*`.
- Use branch/remotes intentionally:
  - Push private source updates: `git push origin main`
  - Normal publishing: push to `origin/main` and let `.github/workflows/pages-deploy.yml` build this repo and force-push `_site/` as a single fresh commit to `public/main`
  - Manual publishing is not a plain `git push public ...`; if you need an emergency manual publish, sync built `_site/` output into the public repo and replace `public/main`
- GitHub Pages is served from the separate `googlielmo/googlielmo.github.io` repository. This repo is the private source of truth.
- The Actions workflow requires a `PAGES_DEPLOY_KEY` secret containing the private half of a write-enabled deploy key registered on `googlielmo/googlielmo.github.io`.
- The public repo should contain publishable static output only. Do not sync private workflow metadata there, and do not publish drafts unless you intentionally build with `--drafts`.
- Always verify target remote and branch before pushing and apply the authorization gates above. Do not push draft content or private workflow files to `public`.

## Content Notes
- The About page content is edited in `_tabs/about.md`.
- Draft posts are marked with `draft: true` by `_config.yml` defaults, and the local `_layouts/` and `_includes/` overrides render visible `DRAFT` markers during draft preview.
- The standard draft workflow is:
  - create with `bash tools/new-draft.sh "My New Post"` to get `_drafts/my-new-post.md`
  - preview with `bash tools/run.sh -d`
  - obtain exact-copy user review and explicit authorization before promotion
  - promote locally with `bash tools/publish-draft.sh my-new-post` (this does not authorize a push)
  - show the resulting `_posts/` copy for review and obtain fresh confirmation before any deployment-triggering push
  - validate with `bash tools/test.sh`
- The draft helper derives the slug from the title unless `--slug` is provided, then writes starter front matter into `_drafts/<slug>.md`.
- The publish helper updates `date:` to the current local timestamp, removes explicit `draft: true`, and preserves the rest of the file content.

## Commit & Pull Request Guidelines
- Current history uses short, imperative commit subjects that end with a period (example: `Update polling workflow for Dea API documentation.`).
- Keep commit titles focused and specific; one logical change per commit.
- PRs should include:
  - What changed and why
  - Any config/content migration notes
  - Screenshots for visible UI/layout changes
  - Confirmation that `bash tools/test.sh` passed locally
