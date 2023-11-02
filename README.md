# Multi-stage Dockerfile dev setup

This is an example on how to leverage _multi-stage Dockerfile_ with _docker-compose.dev.yml_ to improve DX with balena.

## Principles

- The dockerfile is split in 3 main stages :

  - `build` (all the preparation steps, including the compilations steps, etc.)
  - `prod` (imports the important files from build and leave out the scaffolding. Then adds the configurations and commands for production)
  - `dev` (imports all things needed for test and dev from the build stage. Then adds the configurations and commands for development)

- It's important for `dev` to be the latest step as we'll want to be able to use balena's `live-reload` when pushing in `local mode` and it only works if we run the full Dockerfile (all steps).
- If we were to add a `test` stage it should go between `prod` and `dev`.

- To build a certain stage we use `balena build --target _step_ -t _imagename:tag_ .` ( i.e. `balena build --target prod -t hello:world .` )

- We set the target in the `docker-compose.yml` with `target: prod`
- Then we set the `docker-compose.dev.yml` with `target: dev` so it's overwritten when using `local mode` push.

i.e. :

```
services:
  hello:
    build:
      context: .
      target: dev
```

## Limitations

- For live update to work in `local mode`, the `dev` step must be the latest step
- `docker-compose.dev.yml` won't be merged when doing a `build` (currently only works when pushing in local mode). See [this PR](https://github.com/balena-io/balena-cli/pull/2177) for information about how it actually works.
