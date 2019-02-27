# Haskell Tensorflow Library Setup Guide

Update of this guide: 02/26/2019

## Library Information

### The Haskell Interface to Tensorflow C Library
https://github.com/tensorflow/haskell

### Supported Running Environments
- Stack (covered by this guide)
    - latest TensorFlow Haskell git version
    - TensorFlow C library 1.9.0
    - stack resolver: lts-12.26
    - GHC 8.4.4
- Nix
    - latest TensorFlow Haskell git version
    - TensorFlow C library 1.9.0
    - stack resolver: lts-12.26
    - GHC 8.4.4
- Docker
    - GPU support
    - TensorFlow Haskell version 0.2.0.0
    - TensorFlow C library 1.9.0
    - stack resolver: lts-11.9
    - GHC 8.2.2

## Setup Compiling Environment on Native CentOS with Stack
- CentOS 7.6
- stack 1.9

### Install Protocol Buffers

The rpm version on CentOS 7.6 is too old (2.5.0-8.el7 released in 2015). All
Fedora rpms cannot be installed on CentOS due to broken dependencies. To
compile the latest version of `protobuf` from git:

```
sudo yum install autoconf automake libtool unzip gcc-c++ git -y
git clone https://github.com/google/protobuf.git
cd protobuf
git submodule update --init --recursive
./autogen.sh
./configure --prefix=/usr
make
sudo make install
```

### Install Snappy

`snappy` is usually installed. To ensure the installation:
`sudo yum install snappy -y`

### Install TensorFlow C Library

```
mkdir -p /tmp/libtensorflow
cd /tmp/libtensorflow
wget https://storage.googleapis.com/tensorflow/libtensorflow/libtensorflow-cpu-linux-x86_64-1.9.0.tar.gz
sudo mv lib/* /usr/lib64/
sudo mv include/* /usr/include/
sudo ldconfig
```

## Develop Your Haskell Project with TensorFlow

1. Create the Haskell project
```
stack new my-project
cd my-project
```
2. Edit the `stack.yaml` file
```
resolver: lts-12.26

extra-deps:
- git: https://github.com/tensorflow/haskell.git
  commit: c7426a3f00d959ccff8bd631e34ba4c805f7e654
  subdirs:
      - tensorflow
      - tensorflow-ops
      - tensorflow-core-ops
      - tensorflow-logging
      - tensorflow-proto
      - tensorflow-opgen
      - tensorflow-test
      - tensorflow-mnist
      - tensorflow-mnist-input-data
```
3. Compile
```
stack setup
stack build
```
4. Run the project
```
stack exec my-project-exe
```
