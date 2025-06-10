# Quick Start

## Tested on

```sh
% fastfetch
morikura@Morikura-MacBook-Pro
---------------
OS: macOS Ventura 13.2.1 arm64
Host: MacBook Pro (14-inch, 2023)
Kernel: Darwin 22.3.0
Uptime: 4 days, 47 mins
Packages: 36 (brew), 483 (macports)
Shell: zsh 5.8.1
Display (Color LCD): 3024x1964 @ 120 Hz (as 1512x982) in 14" [Built-in]
DE: Aqua
WM: Quartz Compositor
WM Theme: Multicolor (Dark)
Font: .AppleSystemUIFont [System], Helvetica [User]
Cursor: Fill - Black, Outline - White (32px)
Terminal: Apple Terminal 447
Terminal Font: Monaco (10pt)
CPU: Apple M2 Max (12) @ 3.50 GHz
GPU: Apple M2 Max (30) @ 1.40 GHz [Integrated]
Memory: 26.78 GiB / 32.00 GiB (84%)
Swap: Disabled
Disk (/): 1.60 TiB / 1.81 TiB (88%) - apfs [Read-only]
```

## Installation

It's strongly recommended to setup a fresh virtual environment by typing

```sh
## Create conda environments
% conda --version  # check conda installation
'conda 25.5.1'
% conda create -n env_aifeynman python=3.10.0  # create conda environments
% conda activate env_aifeynman # activate conda environments

## Prepare conda environments
(env_aifeynman)% which python  # check python path
'<path_to_conda>/envs/env_aifeynman/bin/python'
(env_aifeynman)% python --version  # check python version
'Python 3.10.0'
(env_aifeynman)% conda install conda-forge::gfortran==11.3.0  # install gfortran
(env_aifeynman)% conda install conda-forge::pytorch  # install pytorch

## clone repo
(env_aifeynman)% cd ~/git
(env_aifeynman)% git clone https://github.com/funasoul/AI-Feynman.git
(env_aifeynman)% cd AI-Feynman
(env_aifeynman)% python3.10 -m venv feyn
(env_aifeynman)% source ./feyn/bin/activate

## install!
## do not execute 'pip install --upgrade pip setuptools'!!
(feyn)(env_aifeynman)% pip install setuptools==52.0.0 # downgrade setuptools
(feyn)(env_aifeynman)% pip install numpy
(feyn)(env_aifeynman)% python setup.py sdist  # install source
(feyn)(env_aifeynman)% pip install dist/aifeynman-2.0.7.tar.gz  # install aifeynman from source

## test
(feyn)% python
```

```ipython
>>> import aifeynman
>>> aifeynman.__version__
'2.0.0'
```

Note that for now, AI Feynman is supported only for Linux and Mac environments.

## First example

Move into a clean directory and run the following Python commands:

    import aifeynman

    aifeynman.get_demos("example_data") # Download examples from server
    aifeynman.run_aifeynman("./example_data/", "example1.txt", 60, "14ops.txt", polyfit_deg=3, NN_epochs=500)
    
This example will get solved in about 10-30 minutes depending on what computer you have and whether you have a GPU.

Here ‘example.txt’ contains the data table to perform symbolic regression on, with columns separated by spaces, commas or tabs. The other parameters control the search: here the brute-force modules tries combinations of the 14 basic operations in ‘14ops.txt’ for up to 60 seconds, polynomial fits are tried up to degree 3, and the interpolating neural network is trained for up to 500 epochs.

# AI-Feynman

This code is an improved implementation of AI Feynman: a Physics-Inspired Method for Symbolic Regression, Silviu-Marian Udrescu and Max Tegmark (2019) [[Science Advances](https://advances.sciencemag.org/content/6/16/eaay2631/tab-pdf)] and AI Feynman 2.0: Pareto-optimal symbolic regression exploiting graph modularity, Udrescu S.M. et al. (2020) [[arXiv](https://arxiv.org/abs/2006.10782)].

Please check [this Medium article](https://towardsdatascience.com/ai-feynman-2-0-learning-regression-equations-from-data-3232151bd929) for a more detailed eplanation of how to get the code running.

In order to get started, run compile.sh to compile the fortran files used for the brute force code.

ai_feynman_example.py contains an example of running the code on some examples (found in the example_data directory). The examples correspond to the equations I.8.14, I.10.7 and I.50.26 in Table 4 in the paper. More data files on which the code can be tested on can be found in the [Feynman Symbolic Regression Database](https://space.mit.edu/home/tegmark/aifeynman.html).

The main function of the code, called by the user, has the following parameters:

* pathdir - path to the directory containing the data file
* filename - the name of the file containing the data
* BF_try_time - time limit for each brute force call (set by default to 60 seconds)
* BF_ops_file_type - file containing the symbols to be used in the brute force code (set by default to "14ops.txt")
* polyfit_deg - maximum degree of the polynomial tried by the polynomial fit routine (set be default to 4)
* NN_epochs - number of epochs for the training (set by default to 4000)
* vars_name - name of the variables appearing in the equation (inluding the name ofthe output variable). This should be passed as a list of strings, with the name of the variables appearing in the same order as they are in the file containing the data
* test_percentage - percentage of the input data to be kept aside and used as the test set

The data file to be analyzed should be a text file with each column containing the numerical values of each (dependent and independent) variable. The solution file will be saved in the directory called "results" under the name solution_{filename}. The solution file will contain several rows (corresponding to each point on the Pareto frontier), each row showing:

* the mean logarithm in based 2 of the error of the discovered equation applied to the input data (this can be though of as the average error in bits)
* the cummulative logarithm in based 2 of the error of the discovered equation applied to the input data (this can be though of as the cummulative error in bits)
* the complexity of the discovered equation (in bits)
* the error of the discovered equation applied to the input data
* the symbolic expression of the discovered equation

If test_percentage is different than zero, one more number is added in the beginning of each row, showing the error of the discovered equation on the test set.

ai_feynman_terminal_example.py allows calling the aiFeynman function from the command line.
(e.g. python ai_feynman_terminal_example.py --pathdir=../example_data/ --filename=example1.txt). Use python ai_feynman_terminal_example.py --help to display all the available parameters that can be passed to the function.

# Citation

If you compare with, build on, or use aspects of the AI Feynman work, please cite the following:

```
@article{udrescu2020ai,
  title={AI Feynman: A physics-inspired method for symbolic regression},
  author={Udrescu, Silviu-Marian and Tegmark, Max},
  journal={Science Advances},
  volume={6},
  number={16},
  pages={eaay2631},
  year={2020},
  publisher={American Association for the Advancement of Science}
}
```

```
@article{udrescu2020ai,
  title={AI Feynman 2.0: Pareto-optimal symbolic regression exploiting graph modularity},
  author={Udrescu, Silviu-Marian and Tan, Andrew and Feng, Jiahai and Neto, Orisvaldo and Wu, Tailin and Tegmark, Max},
  journal={arXiv preprint arXiv:2006.10782},
  year={2020}
}
```
