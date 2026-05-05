# podman-install

This composite action installs Podman on GitHub-hosted runners for both Linux (Ubuntu) and Windows. It's designed to be a simple, one-stop solution for setting up Podman in your CI workflows.

## Features

The action is cross-platform, working for both ubuntu-latest and windows-latest runners.

On Windows, it uses the official Podman MSI installer, automatically fetching the latest version. You can specify a specific Podman version if your CI needs to be pinned. After installation, the action automatically runs podman machine init --now to ensure the Podman machine is running and ready for use in subsequent steps.

On Linux, the action installs Podman v5.x and its key dependencies (like CRIU) from the openSUSE Kubic repository or from another Ubuntu distros, ensuring you get more modern version into GH runners.

### Inputs

This action accepts three inputs to customize its behavior:

**podman-version-input**: This input is used only on Windows runners. You can set it to 'latest' (which is the default) to automatically install the most recent Podman release, or provide a specific version string (e.g., '5.6.2') to install that exact version.

**ubuntu-version**: This input is used only on Linux runners. It specifies the Ubuntu version codename (like '23.10' or '22.04') needed to construct the correct URL for the Kubic repository. The default value is '23.10'.

**ubuntu-repository**: This input is used only on Linux. It offers new option how to install a podman. With the parameter, the particular repositories can be used to install podman and its dependencies. The input is a choice type and it offers to choose from `questing` (default), `resolute`, `noble` or `kubic` repository. If `kubic` is used, then installation will also consider *ubuntu-version* input parameter and use Kubic repositories. Note: If `resolute` is picked up, it will lead to a system upgrade due to incompatible `libc6` library dependency between distros.

**github-token**: (Optional) A GitHub token used to make authenticated API requests when fetching the latest Podman version on Windows. Providing this token helps avoid GitHub API rate limiting, especially in workflows that run frequently. If not provided, the action will make unauthenticated requests which are subject to stricter rate limits. Example: `github-token: ${{ secrets.GITHUB_TOKEN }}`.

## Usage

### Basic Usage

```yaml
- name: Install Podman
  uses: redhat-actions/podman-install@main
```

### With GitHub Token (Recommended for Windows)

```yaml
- name: Install Podman
  uses: redhat-actions/podman-install@main
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Install Specific Podman Version on Windows

```yaml
- name: Install Podman 5.6.2
  uses: redhat-actions/podman-install@main
  with:
    podman-version-input: '5.6.2'
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Install Podman 5.2.5 on Ubuntu from Kubic repositories 23.10
```yaml
# Use kubic repository 23.10 (contains podman 5.2.5)
- name: Install Podman on Ubuntu 24.04 from Kubic
  uses: redhat-actions/podman-install@main
  with:
    ubuntu-version: '23.10'
    ubuntu-repository: 'kubic'
```

### Install Podman 5.4.2 on Ubuntu from Questing distribution repository
```yaml
# Use kubic repository 23.10 (contains podman 5.2.5)
- name: Install Podman on Ubuntu from Questing repository
  uses: redhat-actions/podman-install@main
  with:
    ubuntu-repository: 'questing'
```