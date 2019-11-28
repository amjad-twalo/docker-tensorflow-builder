# Compile Tensorflow on Docker

Docker images to compile TensorFlow yourself.

Tensorflow only provide a limited set of build and it can be challenging to compile yourself on certain configuration. With this `Dockerfile`, you should be able to compile TensorFlow on any Linux platform that run Docker.

Compilation images are provided for Ubuntu 18.10.

## Requirements

- `docker`
- `docker-compose`

## Usage

- Clone this repository:

```bash
git clone https://github.com/hadim/docker-tensorflow-builder.git
```

### TensoFlow CPU

- Edit the `build.sh` file to modify TensorFlow compilation parameters. 
```bash
export CC_OPT_FLAGS="-march=native -mno-avx -mno-avx2 -mno-avx512f -mno-fma -msse4.1 -msse4.2"
```

- To get the appropriate compile optimization flags for **your CPU**, you can run the following command
```bash
grep flags -m1 /proc/cpuinfo | cut -d ":" -f 2 | tr '[:upper:]' '[:lower:]' | { read FLAGS; OPT="-march=native"; for flag in $FLAGS; do case "$flag" in "sse4_1" | "sse4_2" | "ssse3" | "fma" | "cx16" | "popcnt" | "avx" | "avx2") OPT+=" -m$flag";; esac; done; MODOPT=${OPT//_/\.}; echo "$MODOPT"; }
```

- Set the required environment variables in the `tf_build.env` file
```bash
TF_VERSION_GIT_TAG=v2.0.0
PYTHON_VERSION=3.7
BAZEL_VERSION=0.26.1
USE_GPU=0
```

- Then launch the build:
```bash
# Build the Docker image
docker-compose build

# Start the compilation
docker-compose run tf

# You can also do:
# docker-compose run tf bash
# bash build.sh
```


---

- Refer to [tested build configurations](https://www.tensorflow.org/install/source#tested_build_configurations) to know which `BAZEL_VERSION` you need.
- Be patient, the compilation can be long.
- Enjoy your Python wheels in the `wheels/` folder.
- *Don't forget to remove the container to free the space after the build: `docker-compose rm --force`.*

## Builds

| Tensorflow | Python | Distribution | Bazel | CUDA | cuDNN | NCCL | Comment |
| --- | --- | --- | --- | --- | --- | --- | --- |
| v2.0.0-alpha0 | 3.6 | Ubuntu 18.10 | 0.20 | 10.0 | 7.5 | 2.4 | seg fault error  |
| v2.0.0-alpha0 | 3.6 | Ubuntu 18.10 | 0.20 | - | - | - | OK |
| v2.0.0-alpha0 | 3.6 | Ubuntu 16.04 | 0.20 | 10.0 | 7.5 | 2.4 | TODO |
| v2.0.0-alpha0 | 3.6 | Ubuntu 16.04 | 0.20 | - | - | - | TODO |
| 1.9.0 | 3.6 | Ubuntu 16.04 | - | - | 0.19 | - | OK |
| 1.9.0 | 3.6 | Ubuntu 16.04 | 9.0 | 0.19 | 7.1 | - | OK |
| 1.9.0 | 3.6 | Ubuntu 16.04 | 9.1 | 0.19 | 7.1 | - | OK |
| 1.9.0 | 3.6 | Ubuntu 16.04 | 9.2 | 0.19 | 7.1 | - | OK |
| 1.9.0 | 3.6 | CentOS 6.6 | - | - | 0.19 | - | OK |
| 1.9.0 | 3.6 | CentOS 6.6 | 9.0 | 0.19 | 7.1 | - | OK |
| 1.9.0 | 3.6 | CentOS 6.6 | 9.1 | 0.19 | 7.1 | - | OK |
| 1.9.0 | 3.6 | CentOS 6.6 | 9.2 | 0.19 | 7.1 | - | OK |

## Authors

- Hadrien Mary <hadrien.mary@gmail.com>

## License

MIT License. See [LICENSE](LICENSE).
