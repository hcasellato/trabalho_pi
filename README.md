# Trabalho Final de Processamento de Imagens 2025

Step-by-Step Implementation Guide
1. Environment Setup

    Tools: Python, OpenCV (image processing), NumPy/SciPy (matrix operations), Matplotlib (visualization).

    Datasets: Download the Urban 100 and BSD 100 datasets (or your own dataset).

2. Preprocessing

    Input: Load the low-resolution (LR) image II.

    Generate Downsampled Image IDID​:

        Apply Gaussian blur (σ=1.2) to II.

        Subsample by the SR factor (e.g., 2x: reduce resolution by half).

3. Plane Detection (Optional for Structured Scenes)

    Use vanishing point detection (e.g., via line detection and RANSAC) to identify dominant planes in the image.

    Compute plane parameters (homography matrices) for perspective transformation.
    Note: If plane detection fails, default to affine transformations.

4. Nearest Neighbor Field (NNF) Estimation

    Modified PatchMatch Algorithm:

        Initialization: Start with identity transformations (zero displacement, scale=SR factor).

        Propagation: Propagate good matches to neighboring patches.

        Randomization: Perturb affine parameters (scale, rotation, shear) and sample plane indices.

        Cost Function:

            Appearance Cost: Gaussian-weighted SSD between patches.

            Plane Compatibility Cost: Penalize mismatched plane labels.

            Scale Cost: Encourage larger source patches (avoid trivial matches).

    Output: For each patch in II, find the best transformation TiTi​ mapping to IDID​.

5. Compositional Transformation Model

    Combine affine (S,AS,A) and perspective (HH) transformations:
    Ti=H⋅S⋅A
    Ti​=H⋅S⋅A

    Affine Parameters: Scale (ssss), rotation (sθsθ), shear (sa,sbsa,sb).

    Perspective Parameters: Derived from detected planes.

6. High-Resolution (HR) Reconstruction

    For each LR patch PP in II:

        Extract the HR patch QHQH​ from II (upsampled location).

        Apply inverse transformation Ti−1Ti−1​ to QHQH​.

        Place the warped HR patch into the HR image IHIH​.

    Blending: Average overlapping regions to reduce artifacts.

7. Iterative Backprojection

    Refine IHIH​ by enforcing consistency with II:

        Downsample IHIH​ and compare to II.

        Adjust IHIH​ iteratively (20 iterations with Gaussian blur).

8. Multi-Scale Processing (Optional)

    For large SR factors (e.g., 4x), apply the algorithm progressively (e.g., 2x → 3x → 4x).