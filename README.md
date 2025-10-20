# actions-sakura-cloud-registry

A reusable GitHub Actions workflow for building and pushing Docker images to [Sakura Cloud Container Registry](https://manual.sakura.ad.jp/cloud/appliance/container-registry/).

## Usage

### 1. Set up Secrets

Add the following secrets to your repository at `Settings` > `Secrets and variables` > `Actions`:

- `SAKURA_CLOUD_REGISTRY_USERNAME`: Your Sakura Cloud Container Registry username
- `SAKURA_CLOUD_REGISTRY_PASSWORD`: Your Sakura Cloud Container Registry password

### 2. Create Workflow

Create `.github/workflows/release.yml` in your repository:

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    uses: zinrai/actions-sakura-cloud-registry/.github/workflows/build-push.yml@v1
    with:
      registry-name: your-registry.sakuracr.jp
    secrets:
      registry-username: ${{ secrets.SAKURA_CLOUD_REGISTRY_USERNAME }}
      registry-password: ${{ secrets.SAKURA_CLOUD_REGISTRY_PASSWORD }}
```

### 3. Push a Tag

```bash
git tag v1.0.0
git push origin v1.0.0
```

Your image will be pushed as `your-registry.sakuracr.jp/owner/repo:v1.0.0`.

## Parameters

### Inputs

| Parameter         | Required | Default             | Description                                                                 |
|-------------------|----------|---------------------|-----------------------------------------------------------------------------|
| `registry-name`   | x        | -                   | Sakura Cloud Container Registry name<br>(e.g., `your-registry.sakuracr.jp`) |
| `image-name`      |          | `github.repository` | Image name                                                                  |
| `context`         |          | `.`                 | Build context path                                                          |
| `dockerfile`      |          | `./Dockerfile`      | Path to Dockerfile                                                          |
| `platforms`       |          | `linux/amd64`       | Target platforms<br>(e.g., `linux/amd64,linux/arm64`)                       |
| `cache-from-type` |          | `gha`               | Cache source type                                                           |
| `cache-to-type`   |          | `gha`               | Cache destination type                                                      |
| `cache-to-mode`   |          | `max`               | Cache mode (`min` or `max`)                                                 |
| `runs-on`         |          | `ubuntu-latest`     | Runner to use                                                               |
| `build-args`      |          | `''`                | Build arguments (multiline)                                                 |

### Secrets

| Secret              | Required | Description                              |
|---------------------|----------|------------------------------------------|
| `registry-username` | x        | Sakura Cloud Container Registry username |
| `registry-password` | x        | Sakura Cloud Container Registry password |

## Examples

### Custom Image Name

```yaml
jobs:
  release:
    uses: zinrai/actions-sakura-cloud-registry/.github/workflows/build-push.yml@v1
    with:
      registry-name: your-registry.sakuracr.jp
      image-name: my-custom-app
    secrets:
      registry-username: ${{ secrets.SAKURA_CLOUD_REGISTRY_USERNAME }}
      registry-password: ${{ secrets.SAKURA_CLOUD_REGISTRY_PASSWORD }}
```

### Multi-Architecture Build

```yaml
jobs:
  release:
    uses: zinrai/sakura-cloud-registry-acton/.github/workflows/build-push.yml@v1
    with:
      registry-name: your-registry.sakuracr.jp
      platforms: linux/amd64,linux/arm64
    secrets:
      registry-username: ${{ secrets.SAKURA_CLOUD_REGISTRY_USERNAME }}
      registry-password: ${{ secrets.SAKURA_CLOUD_REGISTRY_PASSWORD }}
```

### Build Arguments

```yaml
jobs:
  release:
    uses: zinrai/actions-sakura-cloud-registry/.github/workflows/build-push.yml@v1
    with:
      registry-name: your-registry.sakuracr.jp
      build-args: |
        APP_VERSION=${{ github.ref_name }}
        BUILD_DATE=${{ github.event.head_commit.timestamp }}
    secrets:
      registry-username: ${{ secrets.SAKURA_CLOUD_REGISTRY_USERNAME }}
      registry-password: ${{ secrets.SAKURA_CLOUD_REGISTRY_PASSWORD }}
```

### Custom Runner with Multiple Labels

```yaml
jobs:
  release:
    uses: zinrai/actions-sakura-cloud-registry/.github/workflows/build-push.yml@v1
    with:
      registry-name: your-registry.sakuracr.jp
      runs-on: '["self-hosted", "linux", "x64"]'
    secrets:
      registry-username: ${{ secrets.SAKURA_CLOUD_REGISTRY_USERNAME }}
      registry-password: ${{ secrets.SAKURA_CLOUD_REGISTRY_PASSWORD }}
```

## License

This project is licensed under the [MIT License](./LICENSE).
