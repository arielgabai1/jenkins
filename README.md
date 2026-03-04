# Jenkins CI/CD

A progression of Jenkins pipelines built around a Node.js Cowsay application. Each project adds a new layer of pipeline sophistication, from a basic build-and-deploy flow to a full multibranch strategy with environment-aware behavior.

---

## Projects

### cowsay-pipeline

Basic declarative pipeline. Establishes the foundation: SCM polling, Docker build, local sanity test, push to AWS ECR, and SSH deployment to EC2 via Docker Compose. Committer-aware email and Slack notifications on success and failure.

**Key concepts:** declarative pipeline syntax, `post { always }` cleanup, `withCredentials` SSH blocks, ECR authentication.

### cowsay-versioned

Extends the basic pipeline with branch-based release logic and semantic versioning. Only `release/x.y` branches trigger a push and release. The pipeline auto-increments the patch version by inspecting existing git tags, then pushes the new tag back to the repository after a successful release.

**Key concepts:** branch detection, semver patch auto-increment from git tags, conditional stages with `when`, git tag push from pipeline.

### cowsay-multibranch

Jenkins Multibranch Pipeline that drives all behavior from a branch configuration map. A single Jenkinsfile handles three different environments: production (`main`), staging, and feature branches. Each environment gets a different port, image push strategy, deploy target, and post-deploy test.

**Key concepts:** Multibranch Pipeline job type, branch config map pattern, `disableConcurrentBuilds`, GitLab Container Registry, staging environment teardown after E2E test.

---

## Progression Summary

| Feature | cowsay-pipeline | cowsay-versioned | cowsay-multibranch |
|---|---|---|---|
| Declarative pipeline | yes | yes | yes |
| SCM polling | yes | yes | n/a (Multibranch) |
| Docker build and test | yes | yes | yes |
| Push to registry | ECR | ECR | GitLab Registry |
| SSH deployment | yes | no | yes |
| Semver versioning | no | yes | yes |
| Git tag on release | no | yes | yes |
| Branch-aware behavior | no | yes | yes |
| Multibranch job type | no | no | yes |
| Staging environment | no | no | yes |
| E2E test post-deploy | no | no | yes |
| Slack notifications | yes | no | yes |

---

## Repository Structure

Each subdirectory is a git submodule pointing to its own repository:

```
jenkins-cicd/
├── cowsay-pipeline/      # Basic pipeline
├── cowsay-versioned/     # Semver + branch-based releases
└── cowsay-multibranch/   # Multibranch with environment config map
```

---

## Setup

Clone with submodules:

```bash
git clone --recurse-submodules git@github.com:arielgabai1/jenkins-cicd.git
```

Or initialize submodules after cloning:

```bash
git submodule update --init --recursive
```

---

## Jenkins Requirements

All pipelines require a Jenkins instance with:
- Docker available on the Jenkins agent
- AWS CLI configured (for ECR-based pipelines)
- The credentials listed in each project's README

Each project contains a `Jenkinsfile` at the repository root that Jenkins reads automatically when the job is configured to point at the repo.
