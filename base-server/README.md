# base-server

The base of the AnsibleForms server image. The `Dockerfile` in this directory builds
`ghcr.io/ansibleforms/base-server`: an image with everything AnsibleForms needs at runtime
except AnsibleForms itself. The AnsibleForms `Dockerfile` starts from it and only adds the
application, so its builds take minutes instead of reinstalling Python and Ansible each time.

## What the image contains

### Operating system and Node.js

The image starts from the official slim Debian image of Node.js, the runtime of the
AnsibleForms server. The `FROM` line in the `Dockerfile` names the exact image; Node.js
stays on its major version there, and Dependabot proposes updates within it.

### System packages

Installed with apt:

| Package | Why |
|---|---|
| `python3`, `python3-pip`, `python3-venv` | Python, for Ansible and the playbooks it runs |
| `python3-ldap` | LDAP from Python, for playbooks that query a directory |
| `libxslt1.1` | XML transformations, needed by `lxml` |
| `mariadb-client`, `libmariadb3` | the `mariadb` and `mariadb-dump` commands AnsibleForms uses for database backups and restores |
| `openssh-client`, `sshpass` | SSH connections from playbooks and git, with keys or passwords |
| `git` | cloning and pulling the git repositories AnsibleForms syncs forms and playbooks from |
| `curl`, `wget` | downloads, from playbooks and during the build |
| `tzdata` | time zones, for log and job timestamps |
| `vim` | editing files when troubleshooting inside a container |
| `sudo` | for playbooks that need to escalate |

### ytt

[ytt](https://carvel.dev/ytt/) is installed as `/bin/ytt`, at the release the `Dockerfile`
downloads. AnsibleForms can render its configuration through it when `USE_YTT` is enabled.

### Python environment

A virtual environment at `/venv`, first on the `PATH`, so `python`, `pip` and `ansible` all
come from it. It holds Ansible and the libraries playbooks commonly need:

| Area | Packages |
|---|---|
| Ansible | `ansible` |
| Data and documents | `pandas`, `openpyxl`, `PyYAML`, `jinja2`, `lxml`, `beautifulsoup4` |
| Databases | `PyMySQL` |
| Secrets | `hvac` (HashiCorp Vault) |
| VMware | `pyvmomi`, `pyVim` |
| NetApp | `netapp_lib`, `netapp_ontap`, `solidfire-sdk-python` |
| AWS | `boto3`, `boto`, `botocore` |
| General | `requests`, `paramiko`, `six`, `colorama` |

The packages are not pinned: each build installs their current versions, which is how a
rebuild picks up fixes.

### Ansible collections

Installed in `/usr/share/ansible/collections`, where Ansible finds them without any
configuration:

| Collection | For |
|---|---|
| `netapp.ontap` | NetApp ONTAP storage |
| `netapp.elementsw` | NetApp Element (SolidFire) |
| `netapp.um_info` | NetApp Unified Manager |
| `netapp.storagegrid` | NetApp StorageGRID |
| `amazon.aws` | Amazon Web Services |
| `community.general` | the general-purpose community modules |
| `community.mysql` | MySQL and MariaDB |

### Layout

| Path | What |
|---|---|
| `/app` | the working directory, where the AnsibleForms image puts the application |
| `/venv` | the Python environment |
| `/usr/share/ansible/collections` | the Ansible collections |
| `/root/.ssh` | created empty, for the SSH key AnsibleForms generates on first start |

The image sets no command or entrypoint of its own beyond the Node.js image's defaults;
the AnsibleForms image defines how the server starts. It is built for `linux/amd64`.

## Tags

| Tag | Points to |
|---|---|
| `<yyyy.mm.dd>` | the build of that day (a second build on the same day gets `-<run number>`) |
| `latest` | the newest build |

## Building it locally

From the repository root:

```bash
docker build -t base-server:local base-server
```
