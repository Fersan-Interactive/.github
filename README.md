# Fersan Interactive organisation defaults

Default community health files and shared workflows for every repository of the `Fersan-Interactive` organisation.
GitHub uses each file for any repository that has none of its own of the same type. Nothing is copied into the
repositories.

| File                                       | Role                                                                                                   |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `CONTRIBUTING.md`                          | How work moves from a branch to `main` and on to a release: branches, commits, pull requests, labels, CI/CD, versions |
| `SECURITY.md`                              | How to report a vulnerability                                                                          |
| `pull_request_template.md`                 | The description every pull request starts from                                                         |
| `.github/workflows/pull-request-title.yml` | Checks that a pull request title is a Conventional Commit header; the organisation ruleset `main` requires it in every repository |
| `.github/CODEOWNERS`                       | Review requests for this repository                                                                    |

Changes to this repository follow [CONTRIBUTING.md](CONTRIBUTING.md) like those to any other repository.
