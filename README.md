# AnsibleForms base images

[![CI](https://img.shields.io/github/actions/workflow/status/ansibleforms/base-images/build.yml?branch=main&label=CI)](https://github.com/ansibleforms/base-images/actions/workflows/build.yml)
[![License](https://img.shields.io/badge/license-GPL--3.0-blue)](LICENSE)
[![Docs](https://img.shields.io/badge/docs-ansibleforms.com-informational)](https://ansibleforms.com)

The base container images the [AnsibleForms](https://github.com/ansibleforms/ansibleforms) images are built on. They hold
the runtime and tooling AnsibleForms needs, so the application images only add the application itself and build in minutes.
How to run AnsibleForms, and everything else about it, is documented at [ansibleforms.com](https://ansibleforms.com).

## Images

Each image is published as `ghcr.io/ansibleforms/<name>`, with a README in its directory listing everything in it.

| Image | Base for |
|---|---|
| [`base-server`](base-server) | `ghcr.io/ansibleforms/ansibleforms` |

`base-server` is Debian with everything the server and its playbooks need:

- Node.js
- A Python virtual environment with pandas, PyMySQL, boto3, pyvmomi, the NetApp libraries and more
- Ansible with Galaxy collections for NetApp, Amazon AWS, community.general and community.mysql
- The tools the server calls: git, ssh, sshpass, the MariaDB client and ytt

## Tags

The images are versioned by build date, independently of AnsibleForms:

| Tag | Points to |
|---|---|
| `<yyyy.mm.dd>` | the build of that day (a second build on the same day gets `-<run number>`) |
| `latest` | the newest build |

## How an image is built and used

An image only reaches AnsibleForms after it has been built, published and then tested inside the application:

1. A pull request builds the image without publishing it.
2. A merge to `main` that changes the image publishes it to ghcr.io; Actions › Build › Run workflow rebuilds it.
3. [AnsibleForms](https://github.com/ansibleforms/ansibleforms) pins `base-server` by digest; a Dependabot PR there moves the pin and tests the app on it.

## Contributing

Contributions are welcome, for example a Python library or an Ansible collection added to an image's `Dockerfile`. Start with these files:

- [CONTRIBUTING.md](CONTRIBUTING.md): how to change an image and open a pull request against `main`
- [SECURITY.md](SECURITY.md): how to report a security issue

## License

[GPL-3.0](LICENSE).
