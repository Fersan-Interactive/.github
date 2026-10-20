# Contributing to Fersan Interactive repositories

How work moves from a branch to `main` and on to a release, in every repository of the organisation: web, backend,
Unity, shared packages and any kind added later. A repository's own documents add what is specific to it, such as its
gate command, delivery pipeline or release procedure, and take precedence where they are more specific. They do not
restate this guide.

1. [Repositories](#1-repositories)
2. [Branches](#2-branches)
3. [Commits](#3-commits)
4. [Pull requests](#4-pull-requests)
5. [Labels](#5-labels)
6. [CI/CD](#6-cicd)
7. [Versions and releases](#7-versions-and-releases)
8. [Documentation](#8-documentation)
9. [Security](#9-security)

## 1. Repositories

- **Name:** lowercase kebab-case, `<product>-<role>`. The roles are `unity` (game client), `backend` (services),
  `shared` (code shared between client and services) and `web` (sites). Institutional repositories use `corporate-`
  in place of a product. A new kind of repository takes a short noun for its role.
- **Default branch:** `main`. The organisation ruleset `main` protects it in every repository: changes arrive only
  through pull requests merged by squash, the `pull request title` check must pass, history stays linear, and the
  branch cannot be force-pushed or deleted. Each repository adds its own required checks.
- **Entry points:** a README that says what the repository is and how to run, verify and deliver it, and a home for
  its decisions (ADRs or a decision register).
- **Large binaries** (art, audio, builds) go through Git LFS or an artefact store, never as plain Git objects.
- **Secrets** never enter a repository. Configuration comes from the environment or a secret manager.

## 2. Branches

Trunk-based development: `main` is the only long-lived branch and is always releasable.

- Branch from the current `main`, one purpose per branch, and merge within a day or two. Split larger work into
  increments that each leave `main` working. Unfinished work stays on its branch or merges behind a switch.
- **Name:** `<type>/<short-topic>`, with the commit types from §3: `feat/contact-form`, `fix/lane-collision`,
  `ci/preview-channels`. Tools keep their own prefix (`dependabot/…`).
- No `develop`, environment or long-lived feature branches. A `release/<major>.<minor>` branch exists only for a
  shipped version that still receives fixes. A fix lands on `main` first and is cherry-picked to that branch.

## 3. Commits

[Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/), in English:

```text
<type>(<scope>)!: <subject>

<body>

<footers>
```

| Type       | For                                                         |
| ---------- | ----------------------------------------------------------- |
| `feat`     | A capability that players, visitors or consumers can notice |
| `fix`      | A defect correction                                         |
| `docs`     | Documentation only                                          |
| `refactor` | A code change with no change in behaviour                   |
| `perf`     | A performance improvement                                   |
| `test`     | Tests only                                                  |
| `build`    | Build system, packaging and dependencies                    |
| `ci`       | CI/CD configuration and delivery scripts                    |
| `style`    | Formatting with no change in meaning                        |
| `chore`    | Other upkeep that touches none of the above                 |
| `revert`   | Undoes an earlier commit                                    |

- **Scope:** optional. A lowercase noun for the area changed, such as `contact`, `runs`, `api` or `ci`. Omit it when
  the change spans the repository.
- **Subject:** imperative mood, lowercase first word unless it is a proper name, and no final period. Keep the whole
  header within 72 characters where possible; it must not exceed 100.
- **Body:** after a blank line, it says what changed and why, wrapped at 72 columns. The diff already shows how.
- **Footers:** Git trailers after a blank line.
  - `BREAKING CHANGE: <what breaks and how to migrate>`, or a `!` before the colon.
  - `Refs: #<issue>`.
  - `Co-authored-by: Name <email>` for every co-author, AI agents included.
- **Author identity:** commit with your Fersan Interactive account and address (`name@fersaninteractive.com`), set
  as Git's `user.name` and `user.email`. A personal account or address never appears in a commit, a trailer or a pull
  request; bots keep their own identity.
- **Breaking change:** anything that breaks a consumer, such as a public API or package contract, a save or data
  format, or a public URL removed without a redirect.
- **Revert:** `revert: <header of the reverted commit>`, with `This reverts commit <sha>.` in the body.
- **Unit of work:** a commit holds the change together with its tests and the documents it invalidates. History on
  `main` is never rewritten; a mistake is undone by a revert.

```text
feat(contact): route each topic to its own address
fix(runs): keep the lane change inside the course bounds
ci: deploy each pull request to a preview channel
```

## 4. Pull requests

1. **Open** as a draft while the work is in progress, and mark it ready once the checks pass and the description is
   complete.
2. **Title:** a Conventional Commit header. The squash merge makes it the commit subject on `main`, and the
   `pull request title` check enforces the format. After correcting a title, re-run that check or push again.
3. **Description:** fill in the [template](pull_request_template.md).
   - What changes and why.
   - How it was verified, and what was not.
   - What happens on merge, and how to undo it.
4. **Labels:** exactly one kind label, plus any other label from §5 that applies.
5. **Assignee:** the person accountable for landing the pull request. That is normally the author; a pull request
   written by an AI agent is assigned to the person who directed the work. Reviewers are requested automatically by
   the repository's `CODEOWNERS`.
6. **Size:** keep a pull request small and single-purpose; unrelated changes go in separate pull requests.
7. **Merge:**
   - Required checks pass on a branch that is up to date with `main`.
   - Required reviews and acceptance are given.
   - Merge with **Squash and merge**. The commit takes the title as its subject and the branch's commit messages as
     its body.
   - The branch is deleted after the merge.

## 5. Labels

The same labels exist in every repository; new repositories get them as organisation defaults.

| Label           | Group    | Description                                                                         |
| --------------- | -------- | ----------------------------------------------------------------------------------- |
| `enhancement`   | Kind     | feat: a new or improved capability                                                  |
| `bug`           | Kind     | fix: a defect                                                                       |
| `documentation` | Kind     | docs: documentation only                                                            |
| `maintenance`   | Kind     | refactor, perf, test, style, chore: upkeep with no new capability                   |
| `ci`            | Kind     | ci, build: pipelines, build and repository tooling                                  |
| `dependencies`  | Kind     | build(deps), ci(deps): dependency updates, including dependabot's                   |
| `breaking`      | Impact   | \<type>!: a breaking change; a shipped artefact needs a new major version           |
| `accessibility` | Area     | area: affects access for people with disabilities                                   |
| `needs owner`   | Workflow | workflow: waits on a product owner's decision, acceptance or account action         |
| `blocked`       | Workflow | workflow: waits on something outside the team, such as a provider or a store review |
| `duplicate`     | Triage   | triage: already covered by another issue or pull request                            |
| `wontfix`       | Triage   | triage: valid, but will not be done; the reason goes in a comment                   |

A `revert` takes the kind of the change it undoes.

## 6. CI/CD

- **One gate.** Each repository has a single command that runs its complete verification, and CI runs that same
  command. A partial run is labelled as partial and never counts as a pass. Local hooks are a convenience; the
  required checks on the hosted repository are the enforcement.
- **Every pull request** runs the gate, and `main` accepts a merge only with the gate green.
- **Delivery** runs from CI, never from a workstation except for a documented recovery. Each target is a GitHub
  environment, so its deployment history is kept per commit. A deploy job checks, before it changes production, that
  its commit is still the latest commit of `main`.
- **Supply chain**
  - Third-party actions are pinned to a full commit SHA.
  - The `GITHUB_TOKEN` is read-only by default and widened per job.
  - Cloud access uses OIDC federation, so no long-lived keys are stored.
  - Workflows never run pull request code with secrets (`pull_request_target` is not used to build it).
  - Dependabot proposes updates after a cooldown, labelled `dependencies` and with Conventional Commit messages. They
    pass the same gate as any other change.

## 7. Versions and releases

| Repository kind                                         | Release identity                                                                                                                                                                                                                                                          | Rollback                                                                 |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Continuously delivered: sites and services              | The commit on `main`. Each merge is a release, delivered and recorded as a deployment of its environment. No version number unless a consumer needs one, such as a public API version                                                                                     | Redeploy the previous verified version, then fix forward through `main`  |
| Shipped artefacts: game builds, packages, SDKs, clients | [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html). An annotated tag `vX.Y.Z` on the released commit of `main`, a [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/) entry and a GitHub Release from the tag. Store build numbers only increase | A new patch release; a published version or tag is never moved or reused |

`feat` maps to a minor version and `fix` to a patch, and a breaking change maps to a major version. Before `1.0.0`,
a minor version may break.

## 8. Documentation

- **One home per fact.** Other documents link to it instead of copying it.
- **Current truth.** Documents describe the current system and decisions, and Git keeps their history.
- **Decisions** are recorded where the repository keeps them, with context and consequences.
- **Same pull request.** Documentation changes together with the behaviour it describes.
- **Language.** Code, commits, pull requests and technical documentation are in English. Product and user-facing
  documents follow the repository's language policy.

## 9. Security

Report vulnerabilities as described in [SECURITY.md](SECURITY.md), never in a public issue or pull request.

## References

- [DORA: trunk-based development](https://dora.dev/capabilities/trunk-based-development/)
- [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) ·
  [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html) ·
  [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/) ·
  [Git trailers](https://git-scm.com/docs/git-interpret-trailers)
- GitHub Docs:
  - [Pull request templates](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository)
  - [Default community health files](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file)
  - [Merge methods](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github)
  - [Rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets)
  - [Code owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)
  - [Dependabot options](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/dependabot-options-reference)
  - [Secure use of GitHub Actions](https://docs.github.com/en/actions/reference/security/secure-use)
  - [OpenID Connect](https://docs.github.com/en/actions/concepts/security/openid-connect)
