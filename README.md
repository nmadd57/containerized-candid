# containerized-candid

Container image for [Candid](https://github.com/canonical/candid), Canonical's
macaroon-based identity broker, built from upstream source on `ubuntu:24.04`.

Images are published to `ghcr.io/nmadd57/containerized-candid`.

## Build

```sh
docker build -t containerized-candid ./candid
```

## Run

The image expects a `config.yaml` to be supplied at `/root/config.yaml`. See the
[upstream documentation](https://github.com/canonical/candid) for the configuration
schema.

```sh
docker run --rm -p 8081:8081 -v ./config.yaml:/root/config.yaml containerized-candid
```
