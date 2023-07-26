# Automated-Labeling-Sandbox
This is a sandbox and effectively the start of a prototype for automated labeling of egocentric videos of various tasks (e.g., making tea) captured from a HoloLens 2 as a part of the [PTG Angel System](https://github.com/PTG-Kitware/angel_system). The current state of this repository is geared toward building up SAM-Track and HIPIE for an eventual automated labeling prototype. The suggested plan of action, given the individual components (SAM-Track and HIPIE) function as we expect, is leverage HIPIE in tracking across frames using SAM-Track and explore stronger interactions from potential labelers that include setting up labels via referring segmentations (see the HIPIE paper for more details).

## :book: Relevant Literature

SAM-Track: https://arxiv.org/abs/2305.06558

HIPIE: http://people.eecs.berkeley.edu/~xdwang/projects/HIPIE/

Semantic SfM (useful seminal work for future work that could help refine detections and tracking): https://cvgl.stanford.edu/papers/bao_ssfm_cvpr2011.pdf

## :wrench: Setup

Use the below `conda create` command to create a conda environment that should work for setting up both `HIPIE` and `SAM-Track`.

```
conda create -n samtrack python=3.9 cudatoolkit-dev=11.3 pytorch==1.10 torchvision==0.11  -c pytorch -c conda-forge
```

Please navigate to the respective submodule folders, either `HIPIE/` or `Segment-and-Track-Anything/`, and follow their `README` files for further, comprehensive setup details (e.g., installing dependencies).

## :computer: Usage

It should be noted that a fully automated labeling pipeline has not been established yet with any of these components. Regardless, there are promising segmentation and tracking results in `video_examples/` and referring segmentation results in `HIPIE/notebooks/` that are reproducible and should be straight forward to understand based on the notebook cells alone.

The key files of interest are `HIPIE/notebooks/Demo-HIPIE+SAM.ipynb`, `HIPIE/notebooks/Demo-Main.ipynb`, and `Segment-and-Track-Anything/modified_instseg_egocentric_hl2_tasks.ipynb`. I recommend running the latter two notebooks, as the the `HIPIE+SAM` demo appears to be a bit unstable and often crashed due to running out of GPU memory unexpectedly. You can find existing results from the `SAM-Track` notebook, which were intentionally committed to this repo, in `Segment-and-Track-Anything/PTG_example_output/`.

The `SAM-Track` notebook in particular is already in a state where, in addition to ouputting the segmented video, per-frame masks are generated which can be used for the purpose of labeling and eventually generating a [DIVE format](https://kitware.github.io/dive/) annotation file.

## :notebook: Additional Notes

* The `sam_gap` parameter is an important parameter that comes up often in some shape or form across tracking literature that uses SAM. It basically refers to the gap between invoking the SAM foundation model for segmenting new objects of interest. `SAM-Track` appears to be setup in such a way that re-prompting with the same prompt between gaps requires some additional changes to the code, so my recommendation is using the `sam_gap` parameter set in this repo within the `SAM-Track` notebook (`'sam_gap': 9999`).

* Though longer videos (tested up to 10 minutes) can be accomodated by `SAM-Track` in particular, the original PTG recordings should be trimmed such that the first frame of the video serves as a good example of objects to track. You may not need to trim the original recordings if you know which frame would serve as a good exemplar.

## :zap: Real-Time Capabilities

The results generated via the notebooks, as well as in the `video_examples/` folder, were obtained at ~23 FPS using the default `R50-DeAOTL` model for tracking. The `DeAOTT` model was also tested to get ~50 FPS. Note the different models that can be used to increase real-time capabilities [here](https://github.com/yoxu515/aot-benchmark/blob/main/MODEL_ZOO.md). Also note [FastSAM](https://github.com/CASIA-IVA-Lab/FastSAM), which runs at about 25 FPS, and can also be used with some additional code for tracking.