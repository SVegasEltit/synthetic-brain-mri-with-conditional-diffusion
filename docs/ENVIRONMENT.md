# Experimental Environment

This document describes the computational environment used to develop, train, and evaluate the conditional diffusion models for synthetic brain MRI image–mask generation.

## Hardware

| Component  | Specification              |
| ---------- | -------------------------- |
| CPU        | AMD Ryzen 5 3600           |
| GPU        | NVIDIA GeForce RTX 4060 Ti |
| GPU memory | 16 GB                      |
| System RAM | 32 GB                      |

## Software

| Component                               | Version        |
| --------------------------------------- | -------------- |
| Operating system                        | Windows 10 Pro |
| Python                                  | 3.11.15        |
| PyTorch                                 | 2.11.0+cu128   |
| CUDA (PyTorch build)                    | 12.8           |
| NVIDIA driver                           | 621.01         |
| CUDA UMD version reported by NVIDIA-SMI | 13.5           |
| Environment manager                     | Conda          |

CUDA-enabled PyTorch was successfully detected on the NVIDIA GPU.
