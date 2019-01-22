---
layout: post
title: Tensorflow Compiled and Optimized for CPU (and GPU) on Docker
---
- you will need `nvidia-docker` (of course)
- go to https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tools/docker
- you will need to clone the repository (or whatever but get the given directory)
- see the "Dockerfile.devel-gpu"
- you will need to add the compiling options during the build of Tensorflow
- adding `--copt=-mavx --copt=-mavx2 --copt=-mfma --copt=-mfpmath=both --copt=-msse4.1 --copt=-msse4.2` you might not need all of this, so pick some for your own good.

So that the **bazel** line becomes:

```
bazel build -c opt --config=cuda \
        --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" \
        --copt=-mavx --copt=-mavx2 --copt=-mfma --copt=-mfpmath=both --copt=-msse4.1 --copt=-msse4.2 \
        tensorflow/tools/pip_package:build_pip_package && \
```

- build the docker image with this script:

```
export TF_DOCKER_BUILD_IS_DEVEL=YES
export TF_DOCKER_BUILD_DEVEL_BRANCH=master
export TF_DOCKER_BUILD_TYPE=GPU
export TF_DOCKER_BUILD_PYTHON_VERSION=PYTHON3

export NIGHTLY_VERSION="1.head"
export TF_DOCKER_BUILD_CENTRAL_PIP=$(echo ${TF_DOCKER_BUILD_PYTHON_VERSION} | sed s^PYTHON2^http://ci.tensorflow.org/view/Nightly/job/nightly-matrix-cpu/TF_BUILD_IS_OPT=OPT,TF_BUILD_IS_PIP=PIP,TF_BUILD_PYTHON_VERSION=${TF_DOCKER_BUILD_PYTHON_VERSION},label=cpu-slave/lastSuccessfulBuild/artifact/pip_test/whl/tensorflow-${NIGHTLY_VERSION}-cp27-cp27mu-manylinux1_x86_64.whl^ | sed s^PYTHON3^http://ci.tensorflow.org/view/Nightly/job/nightly-python35-linux-cpu/lastSuccessfulBuild/artifact/pip_test/whl/tensorflow-${NIGHTLY_VERSION}-cp35-cp35m-manylinux1_x86_64.whl^)

./parameterized_docker_build.sh
```

