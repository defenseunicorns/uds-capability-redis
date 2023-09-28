# uds-capability-redis
Bigbang [Redis](https://repo1.dso.mil/big-bang/product/packages/redis) deployed via flux by zarf

## Deployment Prerequisites

### Resources
- Minimum compute requirements for single node deployment are at LEAST 64 GB RAM and 32 virtual CPU threads (aws `m6i.8xlarge` instance type should do)
- k3d installed on machine

#### General

- This capability uses the Zarf variable `APP` to create the namespace ${APP}-redis. `APP` defaults to `app`.
- For example, if you want to create a redis instance for gitlab to use you can set the deploy time Zarf variable `APP` to gitlab. This will create the namespace `gitlab-redis` and will create those resources there.
- Look at the [zarf-config.yaml](zarf-config.yaml) in this project for an example of how to set this variable.

## Deploy

### Use zarf to login to the needed registries i.e. registry1.dso.mil

```bash
# Download Zarf
make build/zarf

# Login to the registry
set +o history

# registry1.dso.mil (To access registry1 images needed during build time)
export REGISTRY1_USERNAME="YOUR-USERNAME-HERE"
export REGISTRY1_TOKEN="YOUR-TOKEN-HERE"
echo $REGISTRY1_TOKEN | build/zarf tools registry login registry1.dso.mil --username $REGISTRY1_USERNAME --password-stdin

set -o history
```

### Build and Deploy Everything via Makefile and local package

```bash
# This will run make build/all, make cluster/reset, and make deploy/all. Follow the breadcrumbs in the Makefile to see what and how its doing it.
make all
```

## Declare This Package In Your UDS Bundle
Below is an example of how to use this projects zarf package in your UDS Bundle

```yaml
kind: UDSBundle
metadata:
  name: example-bundle
  description: An Example UDS Bundle
  version: 0.0.1
  architecture: amd64

zarf-packages:
  # Redis
  - name: redis
    repository: ghcr.io/defenseunicorns/uds-capability/redis
    ref: 0.0.1
```