# action-setup-docker

> Setup docker and login

## Usage

```yml
      - name: Docker setup
        uses: seepine/action-setup-docker@v1
        id: meta
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }} # if has password, it will run docker login

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          platforms: linux/amd64,linux/arm64
          push: true
          tags: ${{ steps.meta.outputs.tags }}

```

The tags auto generate from `refs`.Eg the repo is `github.com/seepine/demo`, with registry is `ghcr.io` and with username is `seepine`.

- trigger push branch eg `main`
  - `ghcr.io/seepine/demo:main`
  - `ghcr.io/seepine/demo:latest`
- trigger push tag eg `v1.2.3`
  - `ghcr.io/seepine/demo:1.2.3`
  - `ghcr.io/seepine/demo:latest`

## Custom tags

```yml
      - name: Docker setup
        uses: seepine/action-setup-docker@v1
        id: meta
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          platforms: linux/amd64,linux/arm64
          push: true
          # you can set any tags
          tags: |
            docker.io/zhangsan/demo:latest
            docker.io/zhangsan/demo:1.2.3
            ghcr.io/${{ github.actor }}/demo:latest

```

## Set Cache

```yml
      - name: Docker setup
        uses: seepine/action-setup-docker@v1
        id: meta
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          platforms: linux/amd64,linux/arm64
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          cache-to: ${{ steps.meta.outputs.cache-to }}
          cache-from: ${{ steps.meta.outputs.cache-from }}
```

## Set proxy


```yml

# Set into act runner config.yml also
env:
  HTTP_PROXY: http://192.168.2.4:7890/
  HTTPS_PROXY: http://192.168.2.4:7890/

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 1

      - name: Docker setup
        uses: seepine/action-setup-docker@v1
        id: meta
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          context: .
          file: Dockerfile
          platforms: linux/amd64,linux/arm64
          push: true
          cache-from: ${{ steps.meta.outputs.cache-from }}
          cache-to: ${{ steps.meta.outputs.cache-to }}
          tags: ${{ steps.meta.outputs.tags }}
          build-args: |
              "http_proxy='${{ env.http_proxy }}'"
              "https_proxy='${{ env.https_proxy }}'"
```
