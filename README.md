# MVDGS

This organization provides documentation on the trial and error process experienced during the MVDGS project, as well as related baseline models such as SV3D, Stable-DreamFusion, Gaussian-Splatting, their inference processes, and Docker files.

**Project Overview:**  
Novel View Synthesis (NVS) is a technology that generates images from new, unseen camera poses based on images captured from multiple viewpoints. Traditionally, most approaches fill occluded areas using Diffusion models specialized for 2D image generation. However, such 2D-based methods have limitations in maintaining perfect consistency between viewpoints, making it difficult to achieve natural view transitions in real 3D environments. Recently, research has shifted to Video Diffusion models that maintain temporal continuity and consistency across viewpoints, solving NVS problems with continuous frames instead of single images. In particular, the CameraCtrl paper succeeded in significantly improving inter-frame view consistency by conditioning video generation on camera poses, directly suggesting the potential for extension to 3D. Based on these trends, this project not only focuses on generating videos while controlling camera poses, but also aims to develop unique view content that can be realistically reproduced in 3D space. 🤖✨


## Repository List

---

### [sv3d](https://github.com/MVDGS/sv3d)
- **Description:**  
  SV3D leverages Latent Video Diffusion to perform multi-view (3D) generation and NVS from a single image.  
  - Paper: [arXiv:2403.12008](https://arxiv.org/abs/2403.12008)
  - Provides Conda-based environment setup, HuggingFace model downloads, and scripts to generate videos with various orbits/poses from a single input image.
  - Example codes, command-line instructions, and BibTeX citation info are detailed in the [README](https://github.com/MVDGS/sv3d/blob/main/README.md).

---

### [stable-dreamfusion](https://github.com/MVDGS/stable-dreamfusion)
- **Description:**  
  Stable-DreamFusion is a PyTorch implementation of the DreamFusion text-to-3D model, which uses Stable Diffusion as its backbone.  
  - Capable of generating 3D objects from text prompts or reconstructing 3D from a single image.
  - Supports NeRF and DMTet backbones, mixed prompt/image input, GUI and mesh export, and Docker-based environments.
  - Example usage, setup instructions, training/testing options, and more are available in the [README](https://github.com/MVDGS/stable-dreamfusion/blob/main/README.md).

---

### [camctrl-dreamgaussian](https://github.com/MVDGS/camctrl-dreamgaussian)
- **Description:**  
  When generating 3D objects with the Dream Gaussian model, the CameraCtrl model is used as input guidance to produce 3D meshes optimized for specific camera poses. The resulting meshes are further refined with SDS Loss for 3D optimization.
  - Implements a full 3D mesh generation pipeline: CameraCtrl-based pose conditioning, mixed view optimization with Zero123, camera rotation/pose file automation, and more.
  - Detailed instructions for environment setup, dependencies, checkpoint downloads, and the full workflow (preparing condition images and pose files → process.py → training and mesh refinement) are provided.
  - Key implementation details, code structure, and training/inference steps can be found in the [README](https://github.com/MVDGS/camctrl-dreamgaussian/blob/main/README.md).
  - This project references CameraCtrl ([arXiv:2404.02101](https://arxiv.org/abs/2404.02101)) and DreamGaussian ([arXiv:2309.16653](https://arxiv.org/abs/2309.16653)) papers.

---

### [gaussian-splatting](https://github.com/MVDGS/gaussian-splatting)
- **Description:**  
  This project aims for real-time radiance field rendering using 3D Gaussian Splatting technology ([paper](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/3d_gaussian_splatting_high.pdf)).
  - Supports training and evaluation with COLMAP or NeRF Synthetic datasets, and offers various command-line options.
  - Instructions for environment setup, execution, evaluation, and metric computation are detailed in the [README](https://github.com/MVDGS/gaussian-splatting/blob/main/README.md).

---

## Introduction

This research aims to generate high-quality 3D objects from a single image while also producing videos that smoothly follow user specified camera trajectories. While existing image-to-3D models excel at static 360 degree views, they face limitations in applying dynamic camera movements or consistently restoring detailed shapes (such as faces) from multiple angles. In this project, we propose a novel paradigm that combines video oriented Diffusion models with Gaussian Splatting based 3D reconstruction. This enables the preservation of complex geometry including facial structures while achieving smooth transitions along user defined camera paths.

## Problem Statement

First, the system must support dynamic camera movements required in film or animation production. For example, techniques like dolly zoom or tracking shots where the camera moves smoothly around an object—should be achievable in virtual 3D scenes generated from a single image. Second, when estimating 3D structure from a 2D input, facial identity must remain consistent along the trajectory. Existing pose-aware one-shot 3D generation methods often suffer from shape collapse or generate entirely different faces with slight changes in camera angle. Third, the system should generate both the 3D asset incorporating the camera path and the corresponding view sequence using only a single input image. In other words, our goal is to unify 2D-to-3D and 2D-to-video generation into a single pipeline, ultimately producing a controllable 3D asset.

## Preliminary

This project is based on three main prior techniques.  
First, 3D Gaussian Splatting (3DGS) allows fast, high quality 3D mesh generation from a single image or a set of images by representing density and color as Gaussians, enabling real time rendering.  
Second, DreamGaussian is a scene optimization method that produces high-resolution 3D objects from a single image, but it is limited in camera control.  
Third, CameraCtrl is a video generation model that injects camera conditions into a U-Net to create 2D view sequences. However, it struggles to maintain subtle parallax and facial shape under rapid pose changes.  
After examining the strengths and weaknesses of these approaches, we hypothesized that by combining the high quality reconstruction of 3DGS with the pose-awareness of CameraCtrl, it would be possible to generate both 3D models and rendered videos that respond consistently to camera motion using only a single image as input.

## Approach 1

The first approach improves CameraCtrl through a multi-stage process. In Trial 1, we attempted to reconstruct a 3D mesh by directly feeding 2D view sequences generated by CameraCtrl into 3DGS; however, this resulted in severe facial distortion and lack of temporal consistency between frames. To address this, Trial 2 constructed an initial Gaussian Splatting mesh based on CameraCtrl’s view sequence, then introduced a post-processing pipeline to refine facial shapes. Specifically, we used texture maps and depth information to extract facial contours and albedo maps, assigning precise color and position information to each Gaussian cell in 3DGS. This post-processing significantly improved facial identity preservation over Trial 1, resulting in smoother shape transitions along camera trajectories.

## Approach 2

The second approach integrates DreamGaussian and CameraCtrl into an end-to-end framework. Here, an initial 3D structure is generated using DreamGaussian from the input image, while camera condition vectors from CameraCtrl are directly injected into the 3D optimization stage. During NeRF style optimization, we apply Score Distillation Sampling (SDS) Loss while leveraging CameraCtrl’s cross-attention module to infuse both facial identity and camera pose information into the Gaussian Splatting process. This enables the generated 3D mesh to maintain facial structure while being renderable along user specified camera tracks. Additionally, CameraCtrl information is integrated into the albedo map generation, ensuring that textures deform naturally with camera rotation. Thanks to this integrated training process, Approach 2 achieves near real time, high quality 3D outputs without the need for multi-stage post-processing.

## Results

The results of Approach 1 showed the most significant improvements after post-processing in Trial 2. Before post-processing, outputs suffered from blurred facial contours and identity changes with even minor camera angle shifts. After post-processing, the facial skeletal structure was consistently preserved during close-up, distant, and side-view sequences. The meshes generated by Gaussian Splatting showed realistic surface textures, and the color information combined with albedo maps responded well to lighting changes. Approach 2 produced even more impressive results: the end-to-end model generated stable 3D renderings with smooth transitions across various camera views, whether given a 45° side view or a frontal image as input. Visualizing the albedo map revealed a dramatic reduction in facial color and texture distortion when CameraCtrl guidance was applied. Qualitative evaluation confirmed temporal consistency by comparing original and generated camera tracks, and feature consistency was validated by analyzing rendered facial images from multiple angles. Blur, distortion, and temporal noise were minimized.

## Conclusion, Future Work & Limitations

This study is the first to address the identity issues of CameraCtrl and proposes new directions for the 2D-to-3D paradigm using multi-stage post-processing (Approach 1) and an end-to-end integrated framework (Approach 2).  
In Approach 1, a multi-stage SDS strategy partially restored temporal consistency, while Approach 2 combined DreamGaussian and CameraCtrl guidance to produce high quality, controllable 3D assets without post-processing.  
However, several limitations remain. First, extreme facial poses, complex clothing, or high-resolution input images can still cause shape collapse, necessitating additional self-supervised learning on unlabeled video data. Second, the current Gaussian Splatting structure is not well suited for dynamic lighting changes or transparent/reflective objects; integrating differentiable rendering techniques is a future goal. Third, the system’s high computational cost requires research into lightweight network structures and efficient memory management for real time applications.  
Future work will focus on introducing extended texture representation models, enhancing transparency/reflection handling and lighting modeling, and relaxing constraints on user defined camera trajectories to generate more diverse and cinematic visuals.


## Etc.

For more detailed instructions and usage, please refer to the README of each repository.
