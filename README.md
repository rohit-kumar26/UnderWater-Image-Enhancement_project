# 🌊 Enhancing Underwater Images: A Comparative Analysis
### Classical Fusion vs. Deep Learning (CycleGAN)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-orange)
![OpenCV](https://img.shields.io/badge/OpenCV-Image%20Processing-green)
![Status](https://img.shields.io/badge/Status-Research%20Complete-brightgreen)

## 📄 Project Overview
[cite_start]Underwater image enhancement is challenging due to the physical properties of the aquatic medium—specifically **light absorption** (which causes bluish/greenish tints) and **scattering** (which causes blur)[cite: 88, 100].

This project implements and compares two distinct paradigms to solve this:
1.  **Classical Image Processing:** Uses color compensation and fusion strategies to mathematically correct illumination.
2.  **Generative Deep Learning:** Uses GANs, DCGANs, and CycleGANs to learn the mapping between "degraded" and "clear" image domains without paired data.

[cite_start]Our results demonstrate that **CycleGAN (trained for 300 epochs)** offers the most robust balance between structural fidelity and perceptual quality[cite: 480].

---

## 🔬 Methodology: How It Works

### 1. Classical Pipeline (The Physics-Based Approach)
Since red light creates the longest wavelength, it is absorbed first underwater. [cite_start]We use the **Green channel** (which suffers the least degradation) to compensate for the Red and Blue channels[cite: 147, 148].

* **Step 1: Color Compensation:** We adjust the Red channel using the difference between mean Green and mean Red values.
    * [cite_start]*Formula:* $I_{rc}(x) = I_r(x) + (g-r) \cdot (1-I_r(x)) \cdot I_g(x)$[cite: 166].
* [cite_start]**Step 2: White Balancing:** Applied using the **Gray World Algorithm** to normalize color distribution[cite: 170].
* **Step 3: Fusion:** We fuse multiple versions of the image using:
    * [cite_start]**Average Fusion:** Simple averaging of inputs $F(i,j) = (A(i,j) + B(i,j)) / 2$[cite: 218].
    * [cite_start]**PCA Fusion:** Uses Principal Component Analysis to calculate optimal weights based on covariance matrices and eigenvalues[cite: 221, 225].

### 2. Deep Learning Pipeline (The Generative Approach)
We experimented with three architectures on the **UIEB Dataset**:

#### A. Baseline GAN & DCGAN
* [cite_start]**Generator:** Uses an Encoder-Residual-Decoder structure with 9 residual blocks to capture detailed transformations[cite: 286].
* [cite_start]**Discriminator:** Implements **PatchGAN**, which classifies local $N \times N$ patches as real or fake rather than the whole image[cite: 292].
* [cite_start]**Limitation:** We found DCGAN improved MSE but failed significantly in **Structural Similarity (SSIM: 0.1725)**, leading to distorted outputs[cite: 460].

#### B. CycleGAN (The Proposed Solution)
[cite_start]CycleGAN learns mappings between two domains (X: Underwater $\to$ Y: Clear) without requiring paired training data[cite: 372].

* **Architecture:**
    * [cite_start]**Two Generators:** $G: X \to Y$ and $F: Y \to X$[cite: 381].
    * [cite_start]**ResNet-Based Generator:** Uses reflection padding and 9 ResNet blocks to preserve spatial features[cite: 386].
* **Loss Functions:**
    1.  [cite_start]**Adversarial Loss:** Forces the generator to create realistic images[cite: 398].
    2.  [cite_start]**Cycle-Consistency Loss:** Ensures that if we translate an image to the target domain and back, we get the original image ($X \to Y \to X$)[cite: 399].
    3.  [cite_start]**Identity Loss:** Prevents the model from changing colors if the image is already clear[cite: 400].

---

## 📊 Experiments & Results

We evaluated models using **MSE** (Mean Squared Error), **PSNR** (Peak Signal-to-Noise Ratio), **SSIM** (Structural Similarity), **FID** (Fréchet Inception Distance), and **UIQM** (Underwater Image Quality Measure).

### Key Findings
1.  **Epoch Selection Matters:** CycleGAN performance peaked at **300 epochs** (Learning Rate: 0.0001). [cite_start]Training to 350 epochs caused overfitting, degrading the FID score[cite: 410, 411].
2.  [cite_start]**Trade-off:** Classical Average Fusion achieved the highest UIQM (245.28) [cite: 442][cite_start], making it visually pleasing, but it lacks the adaptability of Deep Learning methods for diverse water types[cite: 437].
3.  [cite_start]**Best DL Model:** CycleGAN achieved a low **FID score of 17.99**, indicating generated images were statistically very close to real clear images[cite: 466].

### Performance Table
| Model | Epochs | MSE | PSNR | SSIM | FID (Lower is better) | UIQM |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **GAN** | 200 | 0.1024 | 9.90 | 0.4894 | 59.76 | 15.76 |
| **DCGAN** | 200 | 0.0647 | 11.89 | 0.1725 | 238.73 | 85.53 |
| **CycleGAN** | 100 | 0.0968 | 10.14 | 0.6308 | 25.92 | 52.17 |
| **CycleGAN** | **300** | **0.0923** | **10.35** | **0.6652** | **17.99** | **75.04** |

---

## 🔧 Setup & Installation

### Prerequisites
* Python 3.8+
* PyTorch (with CUDA support recommended)
* OpenCV, NumPy, Matplotlib
