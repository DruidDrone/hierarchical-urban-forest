# Benchmark results

## System

### OS

* Arch Linux

### Hardware

* GPU = NVIDIA GeForce GTX 1060 6GB.
* CPU = Intel(R) Core(TM) i7-8700 CPU @ 3.20GHz

### Environment

* CUDA 10.1
* NumPy v1.16.3
* CuPy v6.0.0
* Chainer v5.4.0
* Python 3.7.3

## Models

* PSPNetGPU - PSPNet pre-trained on cityscapes, applied on GPU
* PSPNetCPU - PSPNet pre-trained on cityscapes, applied on CPU
* RFmask - Bitmap mask learned by Random Forest,
* RF - sklearn.ensemble.RandomForestClassifier(n_estimators=11, max_depth=14, random_state=31337)
* LAB - Lab freshold method (alpha + beta channels)

## Images

* 1 x 256x192 [mapillary](https://www.mapillary.com/dataset/vistas) test image used a number of times.

## Results

| Model     | Samples | MS per image | Images per second |
| ---------:|:------- |:-------------|:------------------|
| RFmask    | 10^6    | 0.1959 < 1ms | 5104.65           | 
| LAB       | 10^6    | 0.2663 < 1ms | 3755.16           |
| RF        | 10^3    | 42ms         | 23.81             |
| PSPNetGPU | 10^2    | 87.8ms       | 11.39             |
| PSPNetCPU | 10^2    | 1921ms       | 0.52              |

## Comments

While RFmask is inferior to PSPNet in terms of segmentation accuracy, it may be
particularly well suited to the
[high-throughput phenotyping](https://www.sciencedirect.com/science/article/abs/pii/S1360138513001994) domain.
It's high throughput, in terms of images per second, is due to the efficient use
of a learned bitmap mask.

Note that both RF and RFmask are exactly the same
model - they both achieve the same segmentation accuracy results. However, the
mask allows for complete de-coupling of the learned model from the random
forest: instead of parsing an `a,b` pair through a learned random forest, the
pair is instead compared against a mask which encodes all possible random forest
results for the lab space: `mask[a, b]`.
