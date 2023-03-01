# Adaptive Region Aggregation for the Multi-View Stereo Matching using Deformable Convolutional Networks

Reference implementation:

- [CasMVSNet_pl](https://github.com/kwea123/CasMVSNet_pl): unofficial implementation of [Cascade Cost Volume for High-Resolution Multi-View Stereo and Stereo Matching](https://arxiv.org/pdf/1912.06378.pdf) using [pytorch-lightning](https://github.com/PyTorchLightning/pytorch-lightning)
- [TransMVSNet](https://github.com/megvii-research/TransMVSNet): official implementation of [TransMVSNet: Global Context-aware Multi-view Stereo Network with Transformers](https://arxiv.org/abs/2111.14600)

## :alarm_clock:Logs

- 20230224: upload the *pre-trained models* for test

## :wave:Introduction

In this paper, we propose an adaptive region aggregation approach (DFPN) that employs a deformable kernel and offset regularization to address the issue of imprecise depth maps in occlusion and parallax break regions. The proposed DFPN method can adaptively aggregate neighboring features around a single pixel, and the offset regularization accelerates the offset convergence in MVS while enhancing attention to local region features. Notably, DFPN can be seamlessly integrated into existing coarse-to-fine MVS methods and yields considerable performance gains, as demonstrated by our experiments with CasMVSNet and TransMVSNet. Our results demonstrate that our method not only improves the accuracy of depth maps in large outdoor scenes but also achieves excellent 3D reconstruction quality. In addition, because the training and evaluation datasets are different, DFPN also demonstrate good generalization ability.

![image-20230301144926711](https://raw.githubusercontent.com/YueyueBird-su/Pitcture_Git/main/images/image-20230301144926711.png)



## :raised_hand_with_fingers_splayed:Installation

- Ubuntu18.04 with NVIDIA GeForce RTX 3090

- Cuda 11.1

- Python==3.6/3.8

- Python libraries

  ```cmd
  git colne https://github.com/YueyueBird-su/DFPN_Module_in_MVS.git
  cd Cas./Trans.
  conda env create -f acasmvsnet/atransmvsnet.yaml
  conda activate acasmvsnet/atransmvsnet
  ```

- We also used [Inplace-ABN](https://github.com/mapillary/inplace_abn) in our method, you can install it by

  ```cmd
  pip install inplace-abn
  ```

## :point_down:Training&Evaluation

In order to verify the effectiveness of this method, we trained it on two public datasets [DTU](https://roboimagedata.compute.dtu.dk/?page_id=36) and [BlendedMVS](https://github.com/YoYo000/BlendedMVS), and compared it with the original method on [DTU](https://roboimagedata.compute.dtu.dk/?page_id=36)，[BlendedMVS](https://github.com/YoYo000/BlendedMVS) and [Tanks and Temples](https://www.tanksandtemples.org/) benchmarks. We kept the same parameter settings as in the original method. For specific parameters, please refer to:

[CasMVSNet+Ours](CasMVSNet+Ours)

[TransMVSNet+Ours](TransMVSNet+Ours)

## :+1:Quality Testing

To evaluate the effectiveness of the model after the training, we need to test the accuracy of the results. The asterisk symbol (*) denotes our ports of the implementation, that may not re-produce the original results due to different post-processing and parameter tuning.

### DTU

- Download [SampleSet](http://roboimagedata.compute.dtu.dk/?page_id=36) and [Points](http://roboimagedata.compute.dtu.dk/?page_id=36), and unzip them and place `Points` folder in `SampleSet/MVS Data/`

  ```
  SampleSet
  ├──MVS Data
        └──Points
  ```

-  Set `dataPath` as path to `SampleSet/MVS Data/`, `plyPath` as directory that stores *the reconstructed point clouds*, `resultsPath` as directory to store *the evaluation results* in `BaseEvalMain_web.m`

- Final run `BaseEvalMain_web.m` in matlab

- The results in different method are:

| **Method**       | **Acc.(mm)** :arrow_down: | **Comp.(mm)**  :arrow_down: | **Overall(mm)**  :arrow_down: |
| :--------------- | :-----------------------: | :-------------------------: | :---------------------------: |
| CasMVSNet        |           0.325           |            0.385            |             0.355             |
| CasMVSNet(*)     |           0.361           |            0.359            |             0.360             |
| +Ours(258_limit) |         **0.374**         |          **0.342**          |           **0.358**           |
| TransMVSNet      |           0.321           |            0.289            |             0.305             |
| TransMVSNet(*)   |           0.367           |            0.285            |             0.326             |
| +Ours(258_limit) |         **0.371**         |          **0.277**          |           **0.324**           |

### BlendedMVS

- As BlendedMVS does not provide a point cloud as a measure of accuracy, the evaluation metric is based solely on the accuracy of the depth map, where `epe` denotes the average endpoint error and `e1` denotes the percentage of points with `epe` exceeding 1 mm.
- The results in different method are:

| **Method**       | epe(mm):arrow_down: | e1(%):arrow_down: | e3(%):arrow_down: |
| :--------------- | :-----------------: | :---------------: | :---------------: |
| CasMVSNet (*)    |        2.34         |       25.49       |       9.44        |
| CasMVSNet+DFPN   |      **2.24**       |     **24.77**     |     **9.16**      |
| TransMVSNet (*)  |        1.16         |       15.23       |       6.35        |
| TransMVSNet+DFPN |      **1.05**       |     **15.05**     |     **5.85**      |

### Tanks and Temples

- Upload points clouds in Tanks and Temples benchmark](https://www.tanksandtemples.org/).

| Method                                                       | Mean:arrow_up: | Fam.  :arrow_up: | Fra.  :arrow_up: | Hor. :arrow_up: | Lig.  :arrow_up: | M60  :arrow_up: | Pan  :arrow_up: | Pla.  :arrow_up: | Tra.:arrow_up: |
| :----------------------------------------------------------- | -------------- | ---------------- | ---------------- | --------------- | ---------------- | --------------- | --------------- | ---------------- | :------------: |
| CasMVSNet (2020)                                             | 56.84          | 76.37            | 58.45            | 46.26           | 55.81            | 56.11           | 54.06           | 58.18            |     49.51      |
| [CasMVSNet (*)](https://www.tanksandtemples.org/details/5943/) | 56.47          | 74.80            | 56.18            | 48.13           | 53.75            | 56.35           | 52.80           | 57.86            |     51.89      |
| [CasMVSNet+DFPN](https://www.tanksandtemples.org/details/5949/) | **56.51**      | **72.84**        | **58.30**        | **45.16**       | **55.61**        | **57.80**       | **55.08**       | **59.48**        |   **47.79**    |
| TranMVSNet (2022)                                            | 63.52          | 80.92            | 65.83            | 56.94           | 62.54            | 63.06           | 60.00           | 60.20            |     58.67      |
| [TranMVSNet (*)](https://www.tanksandtemples.org/details/5958/) | 59.57          | 79.65            | 59.84            | 51.25           | 54.41            | 60.79           | 57.68           | 57.18            |     55.80      |
| [TransMVSNet+DFPN](https://www.tanksandtemples.org/details/6016) | **60.01**      | **79.55**        | **59.70**        | **53.87**       | **53.94**        | **61.41**       | **58.31**       | **56.93**        |   **56.38**    |

## :crossed_fingers: Acknowledgments

In the course of this study, we benefited from the code implementations of two open source projects, [CasMVSNet_pl](https://github.com/kwea123/CasMVSNet_pl) and [TransMVSNet](https://github.com/megvii-research/TransMVSNet), and we express our sincere gratitude to the authors of these two projects and respect their contributions in the related fields. We also thank all the dataset providers and volunteers who participated in the experiments and evaluations of this paper, for their valuable support and feedback.