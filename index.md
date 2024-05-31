---
layout: default
---

**ACE** is a Fully Homomorphic Encryption (FHE) Compiler Framework for automating Neural Network (NN) Inference. ACE takes pre-trained ONNX model as input and direcltly generates C/C++ programs to perform NN inference on encrypted data.

# Introduce ACE

FHE stands as a revolutionary cryptographic technology that allows computations to be directly carried out on encrypted data without ever requiring decryption. This powerful technique enables the manipulation of sensitive data, ensuring that the computing party remains unaware of the actual information while still delivering valuable encrypted output.

<div align="center"><i>Decrypt(Homo_Add(Encrypt(a), Encrypt(b))) == Add(a, b)</i></div>
<div align="center"><i>Decrypt(Homo_Mult(Encrypt(a), Encrypt(b))) == Mult(a, b)</i></div>

ACE is designed for Privacy-Preserving Machine Learning (PPML) Inference Applications. In this scenario, ML inference is deployed in the cloud, where clients upload their input data and receive the inference output from the service.. In conventional ML inference services, data and results are often transferred in plaintext. This practice leaves sensitive information vulnerable to privacy breaches. Symmetric encryption can protect privacy during data transmission, but it cannot prevent privacy leaks within the cloud infrastructure. The service provider might access the data inadvertently or with malicious intent. With homomorphic encryption, ML inference can be performed directly on encrypted user data. This ensures that sensitive user data is protected against unauthorized access at all stages of the cloud-based inference service.

<p align="center"><img src="assets/ace-ppml.png" width="40%"></p>

ACE takes a pre-trained ML model as input and compiles it into an FHE program directly for both the server side and client side. This makes ACE easily integrable into any existing ML framework, such as ONNX, PyTorch, TensorFlow, and others. In this way, the development of FHE applications is greatly simplified. As a result, developers won't need to understand the sophisticated mathematical foundations behind FHE, grasp the intricacies of effectively using FHE libraries, or manually manage trade-offs between correctness, security, and performance involving security parameter selection and complex optimizations regarding homomorphic operations such as noise and scale management. This significantly simplifies the development process for FHE applications.

<p align="center"><img src="assets/ace-ml-integ.png" width="80%"></p>

Currently, ACE is designed with a compiler infrastructure that supports five levels of abstraction (i.e., five IRs) to compile pre-trained ML models operating on multi-dimensional tensors into low-level polynomial operations. These five phases successively translate pre-trained models into C/C++ programs by automatically performing various analyses and optimizations to make trade-offs between security, correctness, and performance.

<p align="center"><img src="assets/ace-arch.png" width="90%"></p>

# Evaluate ACE

We have evaluated ACE with ResNet models, which are widely used for image recognition. The experiments were done in a Docker container (version 25.0.1) on a Linux server (Ubuntu Linux 20.04 LTS) with an Intel Xeon Platinum 8369B CPU @2.70GHz and 512 GB memory.

Compile times were measured for handling ResNet models of different sizes:

<p align="center"><img src="assets/ace-ct-perf.png" width="50%"></p>

Runtime performance for performing per-image encrypted inference on a single CPU (left bars) was compared with <a href="https://github.com/snu-ccl/FHE-MP-CNN">FHE-MP-CNN</a> (right bar):

<p align="center"><img align="center" src="assets/ace-rt-perf.png" width="50%"></p>

Runtime memory consumption for performing per-image encrypted inference on a single CPU (left bars) was compared with <a href="https://github.com/snu-ccl/FHE-MP-CNN">FHE-MP-CNN</a> (righr bar):

<p align="center"><img src="assets/ace-rt-mem.png" width="50%"></p>

Accuracy rates for both unencrypted and encrypted inference were compared, tested with 1,000 images.

<div style="margin-left: auto; margin-right: auto; width: 50%">
  <table>
    <tr><th>Model</th><th>Unencrypted</th><th>Encrypted</th></tr>
    <tr><td>ResNet20</td><td>90.9%</td><td>91.2%</td></tr>
    <tr><td>ResNet32</td><td>92.8%</td><td>92.8%</td></tr>
    <tr><td>ResNet32-CIFAR100</td><td>66.4%</td><td>65.9%</td></tr>
    <tr><td>ResNet44</td><td>92.5%</td><td>90.7%</td></tr>
    <tr><td>ResNet56</td><td>93.9%</td><td>93.8%</td></tr>
    <tr><td>ResNet110</td><td>94.0%</td><td>93.4%</td></tr>
  </table>
</div>

# Try ACE

## Hardware and software prerequisite

To compile ACE, an x86-64 based system with the following configuration is required:
*    x86_64 CPU with 4 cores or above
*    8 GB or above memory
*    10 GB or more disk space

To run each ResNet model compiled by ACE, an x86-64 based system with the following configuration is required.
*    x86_64 CPU with 4 cores or above
*    128 GB or above memory
*    40 GB or more disk space

To speed up the experiment on measuring accuracy under encrypted inference, a CPU with 32 or more cores and 256 GB or more memory is highly recommended.

We provide a Dockerfile to simplify setting up the software development and testing environment. Docker must be supported by the host OS. Ubuntu Linux 20.04 LTS is recommended for running the Docker to try the ACE compiler.

## Build and test ACE

Follow the steps listed in <a href="https://github.com/ace-compiler/ace-compiler/blob/main/README.md">README.md</a> to build and test the ACE Compiler.

Enjoy!

