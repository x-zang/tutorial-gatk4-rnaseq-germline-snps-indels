# Tutorial: RNA-seq short variant calling using GATK



This repo is a tutorial of the workflow for RNA-seq short variant calling (SNPs & indels) using GATK4. The workflow is available at [gatk-workflows](https://github.com/gatk-workflows)/**[gatk4-rnaseq-germline-snps-indels](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels)**, developed by the GATK Team.  This repo is made for my personal interest and record to make it easier to run GATK workflow.

The tutorial is made based on the [GATK4 workflow repo](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels), [its best practice](https://gatk.broadinstitute.org/hc/en-us/articles/360035531192-RNAseq-short-variant-discovery-SNPs-Indels-), and the tutorial on [how to run GATK workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035530952?id=12521). 

Last updated: Jun 2020.

# Install GATK4

# Prepare the GATK4 workflow

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
java -jar cromwell-33.1.jar run ./the-workflow-cloned/the-file.wdl --inputs ./the-workflow-cloned/the-file.json
```



# References

[1]:https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels
[2]: https://gatk.broadinstitute.org/hc/en-us/articles/360035531192-RNAseq-short-variant-discovery-SNPs-Indels-
[3]:https://gatk.broadinstitute.org/hc/en-us/articles/360035530952?id=12521