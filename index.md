---
layout: default
---

**ACE** is an Fully Homomorphic Encryption (FHE) Compiler Framework for automating Neural Network (NN) Inference. ACE takes pre-trained ONNX model as input and direcltly generating C/C++ programs to perform NN inference on encrypted data.

# Introduce ACE

**FHE** stands as a revolutionary cryptographic technology that allows computations to be directly carried out on encrypted data without ever requiring decryption. This powerful technique facilitates the manipulation of secret data to ensure the computing party remains blind to the actual information, yet can deliver a valuable encrypted output.

_Decrypt(Homo_Add(Encrypt(a), Encrypt(b))) == Add(a, b)_

_Decrypt(Homo_Mult(Encrypt(a), Encrypt(b))) == Mult(a, b)_

ACE is designed for Privacy-Preserving Machine Learning (PPML) Inference Applications. In this scenario, the ML inference is deployed in the cloud and clients upload their input data to cloud and receive the inference output from the service. In conventional ML inference services, data and results are often transferred in plaintext. This practice leaves sensitive information vulnerable to privacy breaches. Symmetric Encryption can protect the privacy during data transmission. But it can't stop privacy leaks within the cloud infrastructure, where the service provider might access the data inadvertently or with malicious intent. With homomorphic encryption, ML inference can be performed directly on encrypted user data. This ensures that sensitive user data is protected against unauthorized access at all stages of the cloud-based inference service.

<p align="center"><img src="assets/ace-ppml.png" width="40%"></p>

ACE takes pre-trained ML model as input and compile it into FHE program directly for both server side and client side. This makes ACE can be easily integrated into any existing ML framework like ONNX, PyTorch, TensorFlow ans others. In this way, the development of FHE application is greatly simplified. Developer won't need to study the mathematics foundations of FHE, APIs of FHE libraries and get rid of tedious details of parameter selection, FHE specific operation insertion, noise and scale management, bootstrapping insertion, etc.

<p align="center"><img src="assets/ace-ml-integ.png" width="80%"></p>

ACE has a 5-levels of IR to compile the pre-trained ML model with Tensor types and operations into low-level Polynomial type and operations. Each phase takes input from predecessor, translate types and operations specific to higher level into current level. Analyzations and optimizations may be taken place both before and after the translation pass.

<p align="center"><img src="assets/ace-arch.png" width="90%"></p>

# Evaluate ACE

We evaluated ACE with ResNet, a typical DNN network for image recognition. The test suite is CIFAR. Pre-trained models like ResNet20/32/44/56, were directly downloaded from GitHUB. ResNet110 is trained from scratch with CIFAR training set.

Compile time is measured on different size of ResNet model:

<p align="center"><img src="assets/ace-ct-perf.png" width="50%"></p>

Runtime performance to infer 1 image with single thread and compared with ICML'22 FHE-MP-CNN:

<p align="center"><img align="center" src="assets/ace-rt-perf.png" width="50%"></p>

Runtime memory consumption to infer 1 image with single thread and compared witn ICML'22 FHE-MP-CNN:

<p align="center"><img src="assets/ace-rt-mem.png" width="50%"></p>

Inference accuracy on first 1000 images in CIFAR-10 or CIFAR-100 test suite:

| Model             | Unencrypted   | Encrypted  |
| :---------------- | :------------ | :--------- |
| ResNet20          | 90.9%         | x          |
| ResNet32          | 92.8%         | x          |
| ResNet32-CIFAR100 | 66.4%         | x          |
| ResNet44          | 92.5%         | x          |
| ResNet56          | 93.9%         | x          |
| ResNet110         | 93.9%         | x          |

# Try ACE

## Hardware and software prerequisite
To compile ACE, x86-64 CPU with 4 cores or above, 8 GB or above memory, 10 GB or above disk space is required.

To run ResNet compiled by ACE, x86-64 with 4 cores or above, 128 GB or above memory, 40 GB or above disk space is required. To speed up the accuracy test, CPU with 32 or above cores, 512 GB or above  memory are highly recommended.

We provide Dockerfile to simplify setting up the software developing and testing environment. Docker must be supported by host OS. Ubuntu 20.04 LTS is recommended.


## Prepare docker environment

We provided a Dockerfile to build the image to develop and test ACE compiler framework. The Dockerfile is located under the root directory of the repo. Run the Shell command below to build the docker image. Typically this step takes tens of minutes depends on the network bandwidth.

```shell
$ docker build -t ace:latest .
```

Once the docker image was built, enter the docker environment:

```shell
$ docker run -it --name ace --privileged ace:latest bash
```

## Build the compiler for single-thread performance testing:

Inside the docker environment, run the command below to build the compiler. This step takes a few minutes depends on the number and speed of cores.

```shell
# cd /app
# ./scripts/build-cmplr.sh Release
```

Once the command finished successfully, the compiler executable is built in release/driver/fhe_cmplr.

Inside the docker environment, run the command below to evaluate single-thread performance of ACE. Given hardward setups, it would take around 5 hours to complete ACE only tests in single thread.

```shell
# python3 /app/scripts/perf.py -a
```

## Build the compiler for inference accuracy testing:

OpenMP is used in this testing to enable inferring multiple images simultaneously. Inside the docker environment, run the command below to build the compiler with OpenMP support. This step takes a few minutes.

```shell
# cd /app
# ./scripts/build-cmplr-omp.sh Release
```

Once the command finished successfully, the compiler executable is built in release_openmp/driver/fhe_cmplr.

We provide a script named 'accuracy.sh' to test the inference accuracy with different models and number of images. 'accuracy.sh' takes 3 parameters, first one is the model name, which can be:

*   resnet20_cifar10
*   resnet32_cifar10
*   resnet32_cifar100
*   resnet44_cifar10
*   resnet56_cifar10
*   resnet110_cifar10

The second parameter is the index of image to start the test. The third parameter is the index of image to end the testing. The last image index is excluded, which means 'accuracy resnet20_cifar10 0 10' will test 10 images from 0 to 9 with ResNet20 model.

Inside the docker environment, run the command below to evaluate ResNet inference accuracy generated by ACE.

```shell
# ./scripts/accuracy.sh resnet20_cifar10 0 10
INFO: infer images [0, 9] from cifar-10 ../cifar/test_batch.bin.
...
```

Enjoy!

