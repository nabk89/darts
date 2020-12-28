# Differentiable Architecture Search (Customized)
Paper (Code base: [here](https://github.com/quark0/darts))
> [DARTS: Differentiable Architecture Search](https://arxiv.org/abs/1806.09055)\
> Hanxiao Liu, Karen Simonyan, Yiming Yang. (ICLR20)

## Requirements
```
Make an environment using Anaconda (conda 4.6.11)
Python = 3.6.10, PyTorch = 1.4.0, torchvision == 0.5.0, graphviz = 0.13.2
```
NOTE: PyTorch 0.4 is not supported at this moment and would lead to OOM.

## Datasets
Although RNN can be searched on [origianl DARTS code](https://github.com/quark0/darts), this customized repository focuses on CNN search.

CIFAR-10
CIFAR-100
SVHN

## Architecture search (using small proxy models)
To carry out architecture search, run
```
cd cnn && python train_search.py                # for conv cells on CIFAR-10 (using 1st-order approximation)
cd cnn && python train_search.py --unrolled     # for conv cells on CIFAR-10 (using 2nd-order approximation)
```
Note the _validation performance in this step does not indicate the final performance of the architecture_. One must train the obtained genotype/architecture from scratch using full-sized models, as described in the next section.

Also be aware that different runs would end up with different local minimum. To get the best result, it is crucial to repeat the search process with different seeds and select the best cell(s) based on validation performance (obtained by training the derived cell from scratch for a small number of epochs). 

## Architecture evaluation (using full-sized models)
To evaluate our best cells by training from scratch, run
```
cd cnn && python train.py --auxiliary --cutout            # CIFAR-10
cd cnn && python train_imagenet.py --auxiliary            # ImageNet
```
Customized architectures are supported through the `--arch` flag once specified in `genotypes.py`.

The CIFAR-10 result at the end of training is subject to variance due to the non-determinism of cuDNN back-prop kernels. _It would be misleading to report the result of only a single run_. By training the best cell (DARTS-2nd) from scratch, one should expect the average test error of 10 independent runs to fall in the range of 2.76 +/- 0.09% with high probability.

## Visualization
Package [graphviz](https://graphviz.readthedocs.io/en/stable/index.html) is required to visualize the learned cells
```
python visualize.py DARTS
```
where `DARTS` can be replaced by any customized architectures in `genotypes.py`.

