# Deep InfoMax (DIM)

This work has been accepted as an oral presentation at ICLR 2019.
We are gradually updating the repository to reflect experiments in the camera-ready version.

**Note**: we have found some differences in performance based on the Pytorch version used, notably 0.4.1 and 1.0. 
We have found these differences independently with a separate codebase, and these may effect experiment outcomes with the NCE loss and NDM.
Please report any discrepancies you find, as we are attempting to resolve these.

Learning Deep Representations by Mutual Information Estimation and Maximization
Sample code to do the local-only objective in 
https://openreview.net/forum?id=Bklr3j0cKX
https://arxiv.org/abs/1808.06670

### Completed
[Updated 4/12/2019]
* Latest code for dot-product style scoring function for local DIM (single or multiple globals).
* JSD / NCE / DV losses (In addition, f-divergences: KL, reverse KL, squared Hellinger, chi squared).
* Convnet and folded convnet (strided crops) architectures. 
* Resnet and folded resnet architectures.
* Training classifiers keeping the encoder fixed (evaluation).
* Global DIM and prior matching.
* Neural Dependency Measure (NDM, for evaluation)
* Faster training for multi-GPU
* MS-SSIM reconstruction (evaluation
* Semi-supervised learning for classifiers.
* Coordinate tasks

### TODO
* MINE and SVM.
* Occlusion tasks.
* Other baselines (VAE, AAE, BiGAN, NAT, CPC).
* Add nearest neighbor analysis.

### Installation / requirements

##### Cortex

You must install the dev branch cortex: https://github.com/rdevon/cortex from source, which requires Python 3.5+ (Not tested on higher than 3.7). Note that cortex is in early beta stages, but it is usable for this demo. 

cortex optionally requires visdom: https://github.com/pytorch/vision

After installing cortex, you will need to do:

    $ cortex setup

See the cortex README for more info or email us (or submit an issue for legitimate bugs).

Then install this package using pip:

    $ pip install .

### Usage

To get the full set of commands, try:

    $ python scripts/main.py --help

For CIFAR10 on a DCGAN architecture, try:

    $ python scripts/main.py local classifier --d.source CIFAR10 -n DIM_CIFAR10 --t.epochs 1000
    
You should get over 71-72% in the pretraining step alone (this was included for monitoring purposes only). 
For more info on training options, do:

    $ python scripts/main.py local classifier --help
    
If you want to match the output of the encoder to a prior and add the coordinate prediction task, do (for example)):

    $ python scripts/main.py local classifier prior coordinates --d.source CIFAR10 -n DIM_CIFAR10 --t.epochs 1000 --prior.scale 0.1
    
For classification evaluation, keeping the encoder fixed (the classification numbers for the above script are for monitoring only),
do:

    $ python scripts/main.py classifier --eval --d.source CIFAR10 -n DIM_CIFAR10_cls --t.epochs 1000 -L <path to cortex outs>/DIM_CIFAR10/binaries/DIM_CIFAR10_final.t7
    
You should get 73-74% with this model. 
Note this command assumes that you trained the model to completion. 
If not, just use one of the checkpoints in that same directory.
You can do a variety of other evaluation techniques:

    $ python scripts/main.py ndm msssim --eval --d.source CIFAR10 -n DIM_CIFAR10_cls --t.epochs 1000 -L <path to cortex outs>/DIM_CIFAR10/binaries/DIM_CIFAR10_final.t7

Note that the learning rate schedule in this script isn't precisely what was used across models in the paper.
The rates in the paper were different for different classifiers; if there is any significant classifier overfitting, adjust to use a faster decay rate.

For a folded Resnet (strided crops) and the noise-contrastive estimation (NCE) type loss, one could do:

    $ python scripts/main.py local classifier --d.source CIFAR10 --encoder_config foldresnet19_32x32 --local.mode nce --local.mi_units 1024 -n DIM_CIFAR10_FoldedResnet --t.epochs 1000
    
where the number of units used for estimating mutual information are the same we used for comparisons to CPC. 
For STL-10 on folded 64x64 Alexnet with multiple globals and the NCE-type loss, try:

    $ python scripts/main.py local classifier --d.sources STL10 --data_args.image_size 64 --data_args.flip --data_args.image_size_test 64 --d.n_workers 32 -n DIM_STL --t.epochs 200 --encoder_config foldmultialex64x64 --local.mode nce --output_units 0 --local.mi_units 1024

### Deep Infomax

TODO: visual guide to Deep Infomax.
