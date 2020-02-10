# MAXHiC

MaxHiC is a background correcting model for General and Capture Hi-C experiments. The model assigns significance level to the realness of the recorded interactions based on a predictive statistical model for the read-count of random interactions, the ones observed due to the Brownian Motion of fragments in the experiment. The model works on the interactions matrix of fixed binned DNA of any resolution. The model considers a negative binomial distribution for read-count of each interaction with two parameters of dispersion factor and mean. Dispersion factor is considered the same for all interactions but the mean parameter is calculated for each interaction separately and is a function of two factors: 1. Genomic distance between two interacting bins, which increases the expectation for read-count when decreased as it increases the probability of random collisions due to Brownian Motion. 2. Bias factors of the two interacting bins as different bins have different properties and different tendencies to show up in the experiment. The model is trained in iterations and in each iteration the interactions identified as significant according to user defined p-value are eliminated so the model would be trained based on insignificant interactions to be more representative of the random interactions and to avoid the biases that real interactions have e.g. their ordinal genomic distances.


## Installation

You can download the package using [this link](https://github.com/Rassa-Gvm/MAXHiC/archive/master.zip). Use the following command to extract the downloaded file.

```
unzip [Dir]/MAXHiC-master -d MAXHiC
```

In which [Dir] must be replaced with the directory containing the downloaded file. This will result in a MAXHiC directory in your current working directory. Alternatively you can install git and use the following command:

```
git clone https://github.com/Rassa-Gvm/MAXHiC.git
```

Now you have downloaded the latest version of MAXHiC in a directory named MAXHiC in your working directory. You may run the tool by running Main.py script in MAXHiC directory. For more help run the following command:

```
python MAXHiC/Main.py -h
```

## Requirements

You will need the following packages for running MAXHiC:
* Python 3.+
* Numpy 1.14.+
* Scipy 1.1.+
* Pandas 0.24.+
* Tensorflow 1.13.+ < 2.

## Running MAXHiC

You can run MAXHiC with the following command:

```
python [Dir_to_MAXHiC]/Main.py [Arguments] base_directory save_directory 
```
In which [Dir_to_MAXHiC] must be filled with the directory to the MAXHiC folder you created in the installation section.



