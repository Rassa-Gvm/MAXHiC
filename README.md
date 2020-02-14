# MAXHiC

MaxHiC is a background correcting model for General and Capture Hi-C experiments. The model assigns significance level to the realness of the recorded interactions based on a predictive statistical model for the read-count of random interactions, the ones observed due to the Brownian Motion of fragments in the experiment. The model works on the interactions matrix of fixed binned DNA of any resolution. The model considers a negative binomial distribution for read-count of each interaction with two parameters of dispersion factor and mean. Dispersion factor is considered the same for all interactions but the mean parameter is calculated for each interaction separately and is a function of two factors: 1. Genomic distance between two interacting bins, which increases the expectation for read-count when decreased as it increases the probability of random collisions due to Brownian Motion. 2. Bias factors of the two interacting bins as different bins have different properties and different tendencies to show up in the experiment. The model is trained in iterations and in each iteration the interactions identified as significant according to user defined p-value are eliminated so the model would be trained based on insignificant interactions to be more representative of the random interactions and to avoid the biases that real interactions have e.g. their ordinal genomic distances.

## Table of Contents

- [Installation](#Installation)
- [Requirements](#Requirements)
- [Running MAXHiC](#Running-MAXHiC)
  * [Positional Arguments](#Positional-Arguments)
  * [Informative Arguments](#Informative-Arguments)
  * [Tool-Related Arguments](#Tool-Related-Arguments)
  * [Training-Related Arguments](#Training-Related-Arguments)
  * [Capture-Model-Related Arguments](#Capture-Model-Related-Arguments)
- [Files Formats](#Files-Formats)
  * [Input Files](#Input-Files)
    + [Bins File](#Bins-File)
    + [Interactions File](#Interactions-File)
    + [Targets File](#Targets-File)
  * [Output Files](#Output-Files)
    + [Short Output](#Short-Output)
    + [Detailed Output](#Detailed-Output)
- [Questions about MAXHiC](#Questions-about-MAXHiC)
- [Reporting Issues](#Reporting-Issues)
- [Citing MAXHiC](#Citing-MAXHiC)
- [License](#License)


## Installation

You can download the package using [this link](https://github.com/Rassa-Gvm/MAXHiC/archive/master.zip). For example using wget command you can download as follows:

```
wget https://github.com/Rassa-Gvm/MAXHiC/archive/master.zip
```

Use the following command to extract the downloaded file:

```
unzip MAXHiC-master.zip -d MAXHiC
```

This will result in a MAXHiC directory in your current working directory. Alternatively you can install git and use the following command:

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

In which [Dir_to_MAXHiC] must be filled with the directory to the MAXHiC folder you created in the [installation section](#Installation). The full command is as follows:

```
python Main.py [-h] [-d device] [-t Threads_number] [-s silent_model]   
               [-do detailed_output] [-pvl significance_limit]   
               [-r Training_rounds] [-rs Replacing_significants]   
               [-mind Minimum_distance] [-maxd Maximum_distance]   
               [-minr Minimum_read_count] [-c run_capture]   
               [-brl bait_ratio_limit] [-bll bait_length_limit]   
               [-bo bait_overhangs] [-bd baits_directory] [-v]   
               base_directory save_directory 
```
### Positional Arguments

**base_directory**  
*Description*: This must be replaced with the directory containing the raw data you want to analyze. It should have a .matrix file containing information about interactions and a .bed file containing information about bins. The formats of [bins file](#Bins-File) and [interactions file](#Interactions-File) are explained in the [Files Formats section](#Files-Formats).  

**save_directory**  
*Description*: The directory to save the results in. Output files and their formats are explained in the [formats section](#Output-Files).

### Informative Arguments

**-h, --help**  
*Description*: Prints a help message explaining about usage and arguments.  
*Accepts*: No argument 

**-v, --version**  
*Description*: Prints tool's version.  
*Accepts*: No argument  

### Tool-Related Arguments

**-d, --device**  
*Description*: The device to be used for training the model. The list of available devices would be printed by -h option.  
*Accepts*: CPU:[d]/GPU:[d], [d] must be replaced with the number of the device.  
*Default*: CPU:0  

**-t, --threads**  
*Description*: The number of threads to train the model using them. Ineffective in the case of using a GPU for training the model.  
*Accepts*: A natural number.  
*Default*: 24  

**-s, --silent**  
*Description*: Whether to print messages in the middle of training the model.  
*Accepts*: T/F  
*Default*: True  

**-do, --detailed_output**  
*Description*: Whether to output fully detailed files for interactions or just with minimum required information. [Short](#Short-Output) and [detailed](#Detailed-Output) formats are explained in the [formats section](#Files-Formats).  
*Accepts*: T/F  
*Default*: F  

### Training-Related Arguments

**-pvl, --pval_limit**  
*Description*: The p-value limit for significance of interactions.  
*Accepts*: A real number between 0 and 1.  
*Default*: 0.001  

**-r, --rounds**  
*Description*: The number of iterations used for filtering significant interactions and retraining the model. Strong recommendation: Do not use 1 as the model parameters would not be trained properly in this case.  
*Accepts*: A natural number.  
*Default*: 4  

**-rs, --replace_significants**  
*Description*: Whether significant interactions should be replaced by their expected value for calculating the bias factors of bins.  
*Accepts*: T/F  
*Default*: True  

**-mind, --min_distance**  
*Description*: Interactions with genomic distance equal to or larger than the given value would be used in training the model.  
*Accepts*: An integer number >= 0  
*Default*: 0  

**-maxd, --max_distance**  
*Description*: Interactions with genomic distance equal to or less than the given value would be used in training the model.  
*Accepts*: An integer number >= 0 or -1 (for having no limit)  
*Default*: -1  

**-minr, --min_read**  
*Description*: Interactions with read-count equal to or larger than the given value would be used in training the model.  
*Accepts*: A natural number.  
*Default*: 1  

### Capture-Model-Related Arguments

**-c, --capture**  
*Description*: Whether the capture model should be run. In the case of capture data, this should be set to true.  
*Accepts*: T/F (T for True and F for False)  
*Default*: F  

**-brl, --bait_ratio_lim**  
*Description*: The minimum ratio of overlap between a bin and target regions w.r.t. the bin’s length to know the bin as a target bin.  
*Accepts*: A real number between 0 and 1  
*Default*: 0  

**-bll, --bait_len_lim**  
*Description*: The minimum number of overlapping base-pairs between a bin and target regions to know the bin as a target bin.  
*Accepts*: An integer number >= 0  
*Default*: 1  

**-bo, --bait_overhangs**  
*Description*: The extra number of base-pairs from each side of a target region that will also be considered as target region.  
*Accepts*: An integer number >= 0  
*Default*: 0  

**-bd, --baits_dir** *(Required for the capture model)*  
*Description*: The directory of the file containing the list of target regions. The format is explained in the [formats section](#Baits-File).  
*Accepts*: A valid directory  


## Files Formats

### Input Files

#### Bins File  
This is a file with .bed postfix in its name that contains information about the location of your bins. It must be tab delimited file without any header with the following columns:  


| Chromosome | Start | End | BinID |
| --- | --- | --- | --- |

For example:  

|  |  |  |  |
| --- | --- | --- | --- |
| chr1 | 0 | 1000 | 1 |
| chr1 | 1000 | 2000 | 2 | 
...
| chr2 | 0 | 1000 |  |  
...


**Chromosome**  
Any format is accepted for chromosome, the only important thing is to have an identical name for all the bins in one chromosome. Bins related to one chromosome must be in a continuous range of rows.  

**Start**  
The starting location of the bin in the chromosome.  

**End**  
The end location of the bin in the chromosome. This basepair is not considered as a part of the bin as it is the starting basepair of the next bin.  

**BinID**  
This column must contain sorted unique integers from 1 to whatever number required and it must be added by one in each row.  

#### Interactions File  
This is a file with .matrix postfix in its name that contains information about the interactions. It must be a tab delimited file without any header with the following columns:  

| BinID1 | BinID2 | Read_Count |
| --- | --- | --- |

For example:

|  |  |  |
| --- | --- | --- |
| 1 | 1287 | 1 |
| 1 | 8679 | 117 |  
...


**BinID1**  
The ID of one of the ends of the interaction. It must exist in the ID column of the bins file.  

**BinID1**  
The ID of the other end of the interaction. It must exist in the ID column of the bins file.  

**Read_Count**  
The total number of ligations recorded between the fragments located in the two bins.

#### Baits File
This is a file containing information about the location of baits or targets in Capture Hi-C. The overlap between baits and bins are calculated and bins are flagged as bait or other_end by the given criterion in the optional arguments section. The file must be tab delimited with header in its 1st line containing the following columns:


| Chromosome | Annotation | Start | End |
| --- | --- | --- | --- |

For example:

| Chromosome | Annotation | Start | End |
| chr1 | Gene1Promoter | 4566 | 6723 |
| chr2 |  | 837123 | 837198 |   
...

**Chromosome**  
The chromosome the bait is located in. Chromosome names must be the same names used for chromosomes in the bins file.  

**Annotation**  
The annotation of the bait. This can be left as an empty string and is not used by the tool.  

**Start**  
The starting basepair of the bait in the chromosome.  

**End**  
The last basepair of the bait in the chromosome. Unlike in bins, this basepair is considered as a part of the bait.  

### Output Files
In the case of general model two files, one named cis_interactions.txt and one trans_interactions.txt will be created in the given save_directory. The first one contains information about cis interactions and the other one about trans interactions. In the case of capture model 6 files will be created: bb_cis_interactions.txt, bo_cis_interactions.txt, oo_cis_interactions.txt, bb_trans_interactions.txt, bo_trans_interactions.txt, oo_trans_interactions.txt in which 'b' stands for bait and 'o' stands for other_end (non-bait) and so each file contains information about interactions of one type as specified by name. 
#### Short Output

Output files are tab delimited with the following header in their 1st line:
 
| bin1ID | bin2ID | read_count | neg_log_p_val | neg_log_q_val | exp_read_count | b1_bias | b2_bias |
| --- | --- | --- | --- | --- | --- | --- | --- |

**bin1ID**  
The BinID of the 1st interacting bin. In the case of Capture model for *bo* interactions, this column contains the ID of the bait.  

**bin2ID**  
The BinID of the 2nd interacting bin.  In the case of Capture model for *bo* interactions, this column contains the ID of the other_end.  
**read_count**  
The total number of ligations recorded between the fragments located in the two bins.  

**neg_log_p_val**  
-ln(p-value) calculated for the interaction.  

**neg_log_q_val**  
-ln(q-value) calculated for the interaction using Benjamini-Hochberg method to correct the effect of multiple testing.  

**exp_read_count**  
The expected number of reads calculated for the interaction based on the model.  

**b1_bias**  
The bias factor calculated for bin1.  

**b2_bias**  
The bias factor calculated for bin2.  

#### Detailed Output
 
| bin1ID | bin1Chromosome | bin1Start | bin1End | bin2ID | bin2Chromosome | bin2Start | bin2End | read_count | exp_read_count | neg_ln_p_val | neg_ln_q_val | b1_bias | b2_bias | b1_read_sum | b2_read_sum | b1_selfless_read_sum | b2_selfless_read_sum |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

Apart from the columns explained in Short Output section:  

**bin1Chromosome**  
The chromosome of bin1.  

**bin1Start**  
The starting location of bin1 in the chromosome.  

**bin1End**  
The ending location of bin1 in the chromosome.  

**bin2Chromosome**  
The chromosome of bin2.  

**bin2Start**  
The starting location of bin2 in the chromosome.  

**bin2End**  
The ending location of bin2 in the chromosome.  

**b1_read_sum**:  
The total number of reads recorded for bin1 (The sum of read count of all interactions bin1 is included in).  

**b1_selfless_read_sum**  
The total number of reads recorded for bin1 aparted from its self-interaction (the case in which bin1 is the same as bin2).  

**b2_read_sum**:  
The total number of reads recorded for bin2.  

**b2_selfless_read_sum**  
The total number of reads recorded for bin2 aparted from its self-interaction.  

## Questions about MAXHiC

Please feel free to ask your questions about MAXHiC in its [Google Groups forum](https://groups.google.com/forum/#!forum/maxhic). You can also post in the forum by sending an email to maxhic@googlegroups.com.

## Reporting Issues

If you faced with any problems in using MAXHiC (exceptions, bugs, crashes) you can report it in the [issues section of Github](https://github.com/Rassa-Gvm/MAXHiC/issues).

## Citing MAXHiC

## License




