# Kubeflow Pipelines v2

## Design

[bit.ly/kfp-v2](https://bit.ly/kfp-v2) (You need to join [kubeflow-discuss google group](https://groups.google.com/g/kubeflow-discuss) to get access).

## Developing

### Developing KFP v2

Prerequisites:

* Install a KFP standalone instance on Google Cloud:

    This does not currently work on other envs, because some tests use GCS & GCS client.
    Welcome contributions to make it portable.

* Install go, python, kfp pypi package, docker.

* Install [ko](https://github.com/google/ko) CLI tool:

    ```bash
    make install-ko
    ```

* Configure dev environment by creating a config file called `.env` in this folder,
it should have the following content:

  ```makefile
  export DEV_IMAGE_PREFIX=<an container image registry prefix you own>
  ```

  For example:

  ```makefile
  export DEV_IMAGE_PREFIX=ghcr.io/kubeflow-test/kfp-/kfp-
  ```

  Then after images are built, they will be pushed to locations like
  `ghcr.io/kubeflow-test/kfp-driver`.

  The `.env` file is ignored by git, it's your local development configuration.

  Verify you can push images to the registry:

  ```bash
  # push all built dev images to DEV_IMAGE_PREFIX
  make image-dev
  ```

  set up go environment value

  ```bash
  # .env is a Makefile local config (ignored by git)
  echo "GOOS_VALUE=$(go env GOOS)" >> .env
  echo "GOARCH_VALUE="$(go env GOARCH) >> .env
  ```

* Install sample test python dependencies:

  ```bash
  cd test
  pip install -r requirements.txt
  ```

* [Connecting to Kubeflow Pipelines using the SDK client](https://www.kubeflow.org/docs/components/pipelines/user-guides/core-functions/connect-api/#configure-sdk-client-by-environment-variables).

  Recommend adding the env vars to your .bashrc or .zshrc etc to persist your config.

  Verify your configuration and connectivity:

  ```bash
  kfp experiment list
  ```

  Requirements on the KFP backend installation:

  * Current limitation, this only works for [KFP Standalone](https://www.kubeflow.org/docs/components/pipelines/installation/standalone-deployment/), not tested on full Kubeflow yet.
  * KFP backend version should be at least 1.7.0-rc.2.

  Requirements on the KFP SDK package:

  * KFP v2 components defined using `@component` decorator installs KFP SDK package at runtime.
  To use a compatible KFP SDK, define the following environment variable before running e2e test:

  ```
  export KFP_PACKAGE_PATH="git+https://github.com/kubeflow/pipelines.git@master#subdirectory=sdk/python"
  ```

Instructions:

* Run everything e2e: build images, backend compiler, compile pipelines and run them:

  ```bash
  make dev
  ```

* Run go unit tests:

  ```bash
  make test
  # or watch file changes and rerun automatically
  make test-watch
  ```

* For individual targets, read the Makefile directly.

* Run one sample test:

    ```bash
    cd "${REPO_ROOT}/v2"
    make install-compiler # This needs to be run each time you update compiler code.
    cd "${REPO_ROOT}"
    python -m samples.path.to.sample_test
    ```

    Read [v2 sample test documentation](./test/README.md) for more details.
