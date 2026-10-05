# Contributing to the AnsibleForms base images

Thanks for helping out. This repository holds the base images the AnsibleForms images are
built on. The application itself lives in
[ansibleforms/ansibleforms](https://github.com/ansibleforms/ansibleforms).

## Table of contents

- [What lives where](#what-lives-where)
- [Branches and pull requests](#branches-and-pull-requests)
- [Building an image locally](#building-an-image-locally)
- [How a change reaches AnsibleForms](#how-a-change-reaches-ansibleforms)

---

## What lives where

Each image has a directory of its own, named after the image it publishes.

| Directory | Publishes | Holds |
|---|---|---|
| `base-server/` | `ghcr.io/ansibleforms/base-server` | Node.js, Python with common playbook libraries, Ansible and its collections, and the tools the server calls; its README lists everything in it |
| `.github/workflows/build.yml` | | builds every image on a pull request, and builds and publishes on `main` |

---

## Branches and pull requests

`main` is protected: everything reaches it through a pull request, and pull requests are
**squash-merged**.

1. Branch from `main`, named `<type>/<short-description>`, for example
   `build/add-kubernetes-collection`.
2. Give the pull request a [Conventional Commits](https://www.conventionalcommits.org/)
   title, for example `build: add the kubernetes.core collection`.
3. The **Build** check must pass. Merging to `main` publishes the changed image.

The branch is deleted automatically once it is merged.

---

## Building an image locally

From the repository root, build an image the way CI does:

```bash
docker build -t base-server:local base-server
```

To try AnsibleForms on it, build the application's `Dockerfile` with its `FROM` lines pointed
at `base-server:local`.

---

## How a change reaches AnsibleForms

A merged change does not reach AnsibleForms users by itself, which is on purpose:

1. The merge publishes a new `base-server`, tagged with the build date and `latest`.
2. The AnsibleForms `Dockerfile` pins `base-server` by digest. Dependabot in
   ansibleforms/ansibleforms notices the new digest and opens a pull request that moves the pin.
3. That pull request builds the application on the new base. It is reviewed, and can be
   tested as a release candidate, before the next AnsibleForms release ships it.

So a change here that the application depends on, such as a new Python library, is only
usable in AnsibleForms once that pin update is merged.
