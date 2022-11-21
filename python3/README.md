## Docker/Podman build 

``` bash
PYTHON_VERSION=3.11.0 && docker build --build-arg PYTHON_VERSION=${PYTHON_VERSION} -t python3:${PYTHON_VERSION%.*}-rocky . && unset PYTHON_VERSION

# OR 

PYTHON_VERSION=3.11.0 && podman build --build-arg PYTHON_VERSION=${PYTHON_VERSION} -t python3:${PYTHON_VERSION%.*}-rocky . && unset PYTHON_VERSION
```