# Tutorial: RNA-seq short variant calling using GATK



This repo is a tutorial of the workflow for RNA-seq short variant calling (SNPs & indels) using GATK4. The workflow is available at [gatk-workflows](https://github.com/gatk-workflows)/**[gatk4-rnaseq-germline-snps-indels](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels)**, developed by the GATK Team.  This repo is made for my personal interest and record to make it easier to run GATK workflow. Root is not required if this tutorial is followed.

The tutorial is made based on the [GATK4 workflow repo](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels), [its best practice](https://gatk.broadinstitute.org/hc/en-us/articles/360035531192-RNAseq-short-variant-discovery-SNPs-Indels-), and the tutorial on [how to run GATK workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035530952?id=12521). 

**Last updated: Jun 2020.**

# Install GATK4

Download latest release (https://github.com/broadinstitute/gatk/releases)

```
wget https://github.com/broadinstitute/gatk/releases/download/4.1.7.0/gatk-4.1.7.0.zip
unzip gatk-4.1.7.0.zip
```





[Install Conda environment for GATK](https://gatk.broadinstitute.org/hc/en-us/articles/360035889851--How-to-Install-and-use-Conda-for-GATK4). In [this page](https://gatk.broadinstitute.org/hc/en-us/articles/360035889851--How-to-Install-and-use-Conda-for-GATK4), it is stated that the bioconda GATK installation does not configure the environment correctly.

Create a conda environment to install GATK dependencies.

```
# creat a conda environment
conda env create -n gatk -f gatkcondaenv.yml
# or 
conda env create --prefix ./path/to/directory/ -f gatkcondaenv.yml
# activate conda environment
conda activate gatk
# or
conda activate ./path/to/directory/

```

```

```

This is a convenient way of installing required dependencies of GATK. Sometimes it doesn't work, e.g. conflicts. In case of conda not working, manually install the packages described in this file with conda and pip.



Docker is need to run GATK4 workflow. Root is needed, but you can also [install from binary](https://docs.docker.com/engine/install/binaries/) and [run docker daemon without sudo](https://docs.docker.com/engine/security/rootless/).

Download a binary from https://download.docker.com/linux/static/stable/ 

```
tar xzvf FILE.tar.gz
# copy the executable files to a PATH directory, so that they can be called without specifying 
cp docker/* /usr/bin/

curl -fsSL https://get.docker.com/rootless | sh
systemctl --user start docker
```



# Download the GATK4 workflow

Set up the working directory.

```
mkdir gatk-workflows
cd gatk-workflows
```

Download the latest releases of the workflow from [here](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels/releases).

```
# replace the following link with the latest release
wget https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels/archive/1.0.0.tar.gz 
# change the .tar.gz file name if needed
tar -zxvf 1.0.0.tar.gz
```

Download the [cromwell](https://github.com/broadinstitute/cromwell/releases) program which will execute the GATK4 workflow.

```
wget https://github.com/broadinstitute/cromwell/releases/download/33.1/cromwell-33.1.jar
```



# Prepare input files

Set up inputs directory, and put all necessary input files in this directory .

```
mkdir inputs
```

Download necessary file from Google Cloud Bucket of Broad Institute https://console.cloud.google.com/storage/browser/gcp-public-data--broad-references/ using browser or [gsutil](https://cloud.google.com/storage/docs/gsutil_install#linux).

```
# This is an example of how to download with gsutil
gsutil -m cp gs://gcp-public-data--broad-references/hg38/v0/Homo_sapiens_assembly38.dbsnp138.vcf ./inputs/
```

Then modify the json file to replace the file/paths with your local files.



# Execute the workflow

```
java -jar cromwell-33.1.jar run gatk4-rnaseq-germline-snps-indels-1.0.0/gatk4-rna-best-practices.wdl --inputs gatk4-rnaseq-germline-snps-indels-1.0.0/gatk4-rna-germline-variant-calling.inputs.json
```



# References

[1]:https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels
[2]: https://gatk.broadinstitute.org/hc/en-us/articles/360035531192-RNAseq-short-variant-discovery-SNPs-Indels-
[3]:https://gatk.broadinstitute.org/hc/en-us/articles/360035530952?id=12521