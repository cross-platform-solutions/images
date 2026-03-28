# images

Public Docker images repository maintained by [cross-platform-solutions](https://github.com/cross-platform-solutions).

All images published here are **publicly available** and free to use by anyone on the internet.

---

## 📋 Table of Contents

- [Available Images](#available-images)
- [Guidelines for Contributing](#guidelines-for-contributing)
- [Adding a New Image](#adding-a-new-image)
- [Workflow Convention](#workflow-convention)

---

## 🐳 Available Images

| Image | Registry | Description |
|-------|----------|-------------|
| [github-actions-buildkit-job](./github-actions-buildkit-job/) | `ghcr.io/cross-platform-solutions/github-actions-buildkit-job` | Rootless BuildKit image with extra tooling for use in GitHub Actions CI jobs |

---

## 📐 Guidelines for Contributing

> **Important:** This repository must only contain **public (open-source) images**. Do **not** add proprietary, private, or closed-source software to any image in this repository. Every image published here must be freely accessible by anyone on the internet.

### Rules

1. **Open Source only** – All software installed inside any image must be open-source and publicly licensed (MIT, Apache-2.0, GPL, BSD, etc.).
2. **Public availability** – Every image pushed to the registry must be publicly readable without authentication.
3. **One directory per image** – Each image lives in its own top-level directory named after the image (e.g., `my-image/`).
4. **One workflow per image** – Each image directory must have a corresponding GitHub Actions workflow file in `.github/workflows/` named `<image-directory-name>.yml`.
5. **Documentation** – Each image directory should contain a `README.md` describing what the image does, how to use it, and any relevant configuration.
6. **Minimal & purposeful** – Keep images small and purposeful. Avoid installing unnecessary packages.
7. **No secrets** – Never embed credentials, tokens, or private keys in a Dockerfile or workflow.

---

## ➕ Adding a New Image

Follow these steps to add a new image to this repository:

### 1. Create the image directory

```
mkdir <your-image-name>
```

The directory name becomes the image name. Use lowercase letters, numbers, and hyphens only (e.g., `my-cool-tool`).

### 2. Add a `Dockerfile`

Create `<your-image-name>/Dockerfile` with the image definition.

Example structure:

```
your-image-name/
└── Dockerfile
└── README.md       # optional but recommended
```

### 3. Create the GitHub Actions workflow

Create `.github/workflows/<your-image-name>.yml` using the template below, replacing `<your-image-name>` with your directory name:

```yaml
name: <your-image-name>

on:
  push:
    branches:
      - main
    paths:
      - '<your-image-name>/**'
  pull_request:
    paths:
      - '<your-image-name>/**'

env:
  IMAGE_NAME: ghcr.io/cross-platform-solutions/<your-image-name>

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build and push image
        uses: docker/build-push-action@v6
        with:
          context: <your-image-name>
          push: ${{ github.ref == 'refs/heads/main' }}
          tags: |
            ${{ env.IMAGE_NAME }}:latest
            ${{ env.IMAGE_NAME }}:${{ github.sha }}
```

### 4. Open a Pull Request

- The workflow runs automatically on pull requests to validate the build.
- On merge to `main`, the image is published to `ghcr.io` with both `latest` and the commit SHA tags.

---

## ⚙️ Workflow Convention

| Convention | Rule |
|------------|------|
| Workflow file name | Must match the image directory name: `.github/workflows/<image-name>.yml` |
| Workflow `name:` | Must match the image directory name |
| Registry | `ghcr.io/cross-platform-solutions/<image-name>` |
| Tags published | `latest` and the full commit SHA (`${{ github.sha }}`) |
| Trigger paths | Scoped to `<image-name>/**` to avoid unnecessary builds |
| Push condition | Only on pushes to `main` branch |

---

## 📄 License

This project is licensed under the [Apache License 2.0](./LICENSE).
