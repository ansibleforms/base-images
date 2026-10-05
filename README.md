# AnsibleForms base images

The base container images the AnsibleForms images are built on. They hold the runtime and
tooling AnsibleForms needs, so the application images only add the application itself and
build in minutes.

## Images

| Image | Directory | Used by |
|---|---|---|
| `ghcr.io/ansibleforms/base-server` | [base-server](base-server) | the AnsibleForms server image, `ghcr.io/ansibleforms/ansibleforms` |

### base-server

Debian with Node.js 24, a Python virtual environment with the libraries playbooks commonly
need (pandas, PyMySQL, boto3, pyvmomi, the NetApp libraries and more), Ansible with a set of
Galaxy collections (NetApp, Amazon AWS, community.general, community.mysql), and the tools
the server calls: git, ssh, sshpass, the MariaDB client and ytt.

## Tags

The images are versioned by build date, independently of AnsibleForms:

| Tag | Points to |
|---|---|
| `2026.10.05` | the build of that day (a second build on the same day gets `-<run number>`) |
| `latest` | the newest build |

## How an image is built and used

1. A pull request builds the image without publishing it.
2. A merge to `main` that changes the image builds it again and publishes it to ghcr.io. A
   rebuild can also be started by hand: Actions, Build, Run workflow.
3. The AnsibleForms `Dockerfile` pins `base-server` by digest, so a new build changes nothing
   on its own. Dependabot in [ansibleforms/ansibleforms](https://github.com/ansibleforms/ansibleforms)
   opens a pull request that moves the pin, where the application is built and tested on the
   new base before it is released.

## Changing an image

Edit the image's `Dockerfile`, for example to add a Python library or an Ansible collection,
and open a pull request against `main`. Dependabot keeps the `FROM` line and the workflow
actions up to date.
