# MVDGS

MVDGS 프로젝트 진행 시 겪었던 Trial And Error 그리고 관련된 baseline 모델 SV3D, Stable-DreamFusion, Gaussian-Splatting Inference 과정 및 docker 파일 등을 제공하고 있습니다. 

**프로젝트 개요:**  
NVS (Novel View Synthesis)는 여러 카메라 포즈에서 촬영된 이미지를 기반으로, 촬영되지 않은 새로운 카메라 포즈의 이미지를 생성하는 기술입니다. 전통적으로 2D 이미지 생성에 강점을 가진 Diffusion 모델을 활용해 가려진 영역을 메우는 방식이 주를 이루었으나, 이러한 2D 기반 접근법은 시점 간 일관성을 완벽히 보장하기 어렵습니다. 이에 따라 최근에는 시간 축상의 연속성과 시점 간 일관성을 유지할 수 있는 Video Diffusion 모델을 도입하여, 단일 이미지가 아닌 연속적인 프레임을 통해 NVS 문제를 해결하는 연구가 진행되고 있습니다. 특히 CameraCtrl 논문에서는 카메라 포즈를 조건으로 한 비디오 생성 방식을 통해, 프레임 간 뷰 일관성을 크게 향상시키는 데 성공하였고, 이 결과는 3D 환경으로의 확장 가능성을 시사합니다. 이러한 배경 하에, 본 프로젝트는 카메라 포즈 컨트롤을 유지하면서 단순 비디오 생성 단계를 넘어 실제 3D 공간에서 재현 가능한 고유한 view 콘텐츠를 개발하고자 합니다. 🤖✨


## 레포지토리 목록

---

### [sv3d](https://github.com/MVDGS/sv3d)
- **설명:**  
  SV3D는 단일 이미지로부터 Latent Video Diffusion을 활용해 다각도의 뷰(3D) 생성 및 NVS를 수행합니다.  
  - 논문: [arXiv:2403.12008](https://arxiv.org/abs/2403.12008)
  - Conda 기반 환경 설치, HuggingFace 모델 다운로드, 단일 이미지 입력 시 다양한 오빗/포즈의 영상을 생성하는 스크립트 제공  
  - 예시 코드, 명령어 및 BibTeX 인용 정보는 [README](https://github.com/MVDGS/sv3d/blob/main/README.md)에 상세히 안내되어 있습니다.

---

### [stable-dreamfusion](https://github.com/MVDGS/stable-dreamfusion)
- **설명:**  
  Stable-DreamFusion은 Stable Diffusion을 백본으로 사용하는 텍스트-투-3D 생성 모델 DreamFusion의 PyTorch 구현체입니다.  
  - 텍스트 프롬프트로 3D 오브젝트를 생성하거나, 단일 이미지로부터 3D 복원 가능  
  - NeRF 및 DMTet 백본, 다양한 프롬프트·이미지 혼합 입력, GUI 및 mesh export 기능, Docker 환경 지원  
  - 예시 코드 및 설치법, 학습·테스트 옵션 등은 [README](https://github.com/MVDGS/stable-dreamfusion/blob/main/README.md)에 자세히 정리되어 있습니다.

---

### [camctrl-dreamgaussian](https://github.com/MVDGS/camctrl-dreamgaussian)
- **설명:**  
  Dream Gaussian 모델을 사용해 3D 객체를 생성할 때, CameraCtrl 모델을 입력 가이던스로 제공하여 특정 카메라 포즈에 최적화된 3D 메쉬를 생성했습니다. 이후 생성된 메쉬에 대해 SDS Loss를 적용하여 3D 최적화 과정을 수행했습니다.  
  - CameraCtrl 기반 포즈 조건, Zero123와의 혼합 뷰 최적화, 카메라 회전·Pose 파일 자동화 등 실제 3D 메쉬 생성 파이프라인을 구현  
  - 환경 설치, 의존성, 체크포인트 정보 및 전체 파이프라인(조건 이미지, pose 파일 준비 → process.py → 학습 및 메쉬 refinement 등) 상세 안내  
  - 주요 구현 및 코드 구조, training/inference 단계, 실 사용 예시는 [README](https://github.com/MVDGS/camctrl-dreamgaussian/blob/main/README.md)에서 확인할 수 있습니다.  
  - 본 프로젝트는 CameraCtrl 논문([arXiv:2404.02101](https://arxiv.org/abs/2404.02101)), DreamGaussian([arXiv:2309.16653](https://arxiv.org/abs/2309.16653))를 참고합니다.

---

### [gaussian-splatting](https://github.com/MVDGS/gaussian-splatting)
- **설명:**  
  3D Gaussian Splatting 기술([논문](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/3d_gaussian_splatting_high.pdf))을 활용, Real-Time Radiance Field Rendering을 목표로 하는 프로젝트입니다.  
  - COLMAP 또는 NeRF Synthetic 데이터셋 기반 학습 및 평가, 다양한 커맨드라인 옵션 지원  
  - 환경 구축, 실행법, 평가 및 지표 측정 등은 [README](https://github.com/MVDGS/gaussian-splatting/blob/main/README.md)에 자세히 안내되어 있습니다.

---

## 기타

각 프로젝트에 대한 자세한 정보와 사용법은 해당 레포지토리의 README를 참고해 주세요.
