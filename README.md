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

### Arguments

The full command is as follows:

```
python Main.py [-h] [-general T/F] [-pvl significance_limit] [-device device] [-p cores_number]  [-r training_rounds_number] [-rv T/F] [-mind Minimum_distance] [-maxd Maximum_distance] [-minread Minimum_read_count] [-silent T/F] [-full_output T/F] [-bait_ratio_lim bait_ratio_limit] [-bait_len_lim bait_length_limit] [-bait_overhangs bait_overhangs] [-targets_dir targets_dir] base_directory save_directory 
```
#### Positional Arguments

**base_directory**: 
*Description*: This must be replaced with the directory containing the raw data you want to analyze. It should have a .matrix file containing information about interactions and a .bed file containing information about bins. The formats are explained in the formats section.  

**save_directory**  
*Description*: The directory to save the results in.

#### Options

**-h**  
*Description*: Prints a help message explaining about usage and arguments.  
*Accepts*: No argument  

**-capture**  
*Description*: Whether the capture model should be run. In the case of capture data, this should be set to true.  
*Accepts*: T/F (T for True and F for False)  
*Default*: F  

**-pvl**  
*Description*: The p-value limit for significance of interactions.  
*Accepts*: A real number between 0 and 1.  
*Default*: 0.001  

**-device**  
*Description*: The device to be used for training the model. The list of available devices would be printed by -h option.  
*Accepts*: CPU:[d]/GPU:[d], [d] must be replaced with the number of the device.  
*Default*: CPU:0  

**-p**  
*Description*: The number of threads to train the model using them. Ineffective in the case of using a GPU for training the model.  
*Accepts*: A natural number.  
*Default*: 24  

**-r**  
*Description*: The number of iterations used for filtering significant interactions and retraining the model. Strong recommendation: Do not use 1 as the model parameters would not be trained properly in this case.  
*Accepts*: A natural number.  
*Default*: 4  

**-rv**  
*Description*: Whether significant interactions should be replaced by their expected value for calculating the bias factors of bins.  
*Accepts*: T/F  
*Default*: True  

**-mind**  
*Description*: Interactions with genomic distance equal to or larger than the given value would be used in training the model.  
*Accepts*: An integer number >= 0  
*Default*: 0  

**-maxd**  
*Description*: Interactions with genomic distance equal to or less than the given value would be used in training the model.  
*Accepts*: An integer number >= 0 or -1 (for having no limit)  
*Default*: -1  

**-minread**  
*Description*: Interactions with read-count equal to or larger than the given value would be used in training the model.  
*Accepts*: A natural number.  
*Default*: 1  

**-silent**  
*Description*: Whether to print messages in the middle of training the model.  
*Accepts*: T/F  
*Default*: True  

**-full_output**  
*Description*: Whether to output fully detailed files for interactions or just with minimum required information. Output format is explained in the formats section.  
*Accepts*: T/F  
*Default*: F  

**-bait_ratio_lim**
*Description*: The minimum ratio of overlap between a bin and target regions w.r.t. the bin’s length to know the bin as a target bin.  
*Accepts*: A real number between 0 and 1  
*Default*: 0  

**-bait_len_lim**
*Description*: The minimum number of overlapping base-pairs between a bin and target regions to know the bin as a target bin.  
*Accepts*: An integer number >= 0  
*Default*: 1  

**-bait_overhangs**
*Description*: The extra number of base-pairs from each side of a target region that will also be considered as target region.  
*Accepts*: An integer number >= 0  
*Default*: 0  

**-targets_dir** *(Required for the capture model)*
*Description*: The directory of the file containing the list of target regions. The format is explained in the formats section.
*Accepts*: A valid directory

## Formats

 : . It should be a tab delimited file with header in its 1st line with the following columns:
Chromosome, Annotation, Start, End
Both files should be tab delimited with no header containing the following columns:
The .bed file:
Chromosome, Start, End, BinID
The .matrix file:
bin1ID, bin2ID, read_count
 
 

The header in the case of not having the full details:
'bin1ID', 'bin2ID', 'read_count', 'neg_log_p_val', 'neg_log_q_val', 'exp_read_count', 'b1_bias', 'b2_bias'
In which neg_log_pval stands for -ln(p-value) and q_val stands for the corrected p-value for multiple testing using Benjamini-Hochberg method.
In the case of full details:
'bin1ID', 'bin2ID', 'read_count', 'exp_read_count', 'neg_ln_p_val', 'neg_ln_q_val', 'b1_bias', 'b2_bias', 'b1_read_sum', 'b2_read_sum', 'b1_selfless_read_sum', 'b2_selfless_read_sum'
In which read_sum stands for the total number of reads recorded for bin and selfless_read_sum stands for the total number of reads other than self-interactions recorded for bin.





