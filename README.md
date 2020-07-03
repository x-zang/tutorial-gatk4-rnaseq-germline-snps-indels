# Tutorial: RNA-seq short variant calling using GATK4

GATK is powerful. However, running it may not be as easy. People, especially bioinformatics beginners are often overwhelmed by its powerfulness and complexity. 

This repo is a tutorial of the workflow for RNA-seq short variant calling (SNPs & indels) using GATK4. The original workflow is available at [gatk-workflows](https://github.com/gatk-workflows)/**[gatk4-rnaseq-germline-snps-indels](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels)**, developed by the GATK Team.  This repo is made for my personal interest and record to make it easier to run GATK workflow. Root is not required if this tutorial is followed. The tutorial is made based on the [GATK4 workflow repo](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels), [its best practice](https://gatk.broadinstitute.org/hc/en-us/articles/360035531192-RNAseq-short-variant-discovery-SNPs-Indels-), and the tutorial on [how to run GATK workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035530952?id=12521). 

Note: This is for GATK4 and may not be compatible with GATK3.8.

**Last updated: Jul 2, 2020.**



# Install GATK4

**Download latest release** from GATK4 repository https://github.com/broadinstitute/gatk/releases. Downloading the pre-compiled binary file is the easiest installation. Requirements for GATK4 can be found form https://github.com/broadinstitute/gatk.

```sh
wget https://github.com/broadinstitute/gatk/releases/download/4.1.8.0/gatk-4.1.8.0.zip
unzip gatk-4.1.8.0.zip
```



**Install conda environment for GATK**. In [this page](https://gatk.broadinstitute.org/hc/en-us/articles/360035889851--How-to-Install-and-use-Conda-for-GATK4), it is stated that the bioconda GATK installation does not configure the environment correctly. Then, it is necessary to manually create a conda environment and install GATK dependencies.

```shell
# creat a conda environment
conda env create -n gatk -f gatkcondaenv.yml
# or 
conda env create --prefix ./path/to/directory/ -f gatkcondaenv.yml
# activate conda environment
conda activate gatk
# or
conda activate ./path/to/directory/
```

Also, have STAR installed in this environment.

This is a convenient way of installing required conda dependencies of GATK. Sometimes it doesn't work, e.g. conflicts. In case of conda not working, manually install the packages described in this file with `conda` and `pip`.



# Run Docker without root

Docker is required to run GATK4 workflow. Root is needed, but you can also [run docker daemon without sudo](https://docs.docker.com/engine/security/rootless/) (see this page for more details).

```sh
curl -fsSL https://get.docker.com/rootless | sh

# At the end of the script, some environment variables are required to be set.
# They would be displayed like the below examples, C&P to run them.
export DOCKER_HOST=unix:///run/user/1001/docker.sock

# start/stop/restart docker without root using the following
systemctl --user (start|stop|restart) docker
```



### Change docker root directory

When running docker, I found it uses `$home/.local/share` as the root dir. As this directory is in my `$home`, which is limited in space. I changed docker root dir follwoing [this](https://medium.com/@hsadanuwan/how-to-change-docker-default-data-directory-f884dac76c1f), otherwise for my case, the disk can be full quickly and process fails.

Run `docker info  `. The line starting with `Docker Root Dir` states the root directory of docker. I intend to change it to another directory in the disk.

```shell
# find the docker.service file
locate docker.service
```

Edit`docker.service` file. Find the `ExecStart` and add `-g /customized/root/dir` to the end of this line. 

Now for me, this line looks like `ExecStart=/home/userid/bin/dockerd-rootless.sh --experimental --storage-driver=overlay2 -g /disk/userid/tools/docker-tmp`

```shell
# reload dockerd and restart docker
systemctl --user daemon-reload
systemctl --user restart docker
```

Run `docker info  ` again. I can see `Docker Root Dir: /disk/userid/tools/docker-tmp`. Then all's set.



# Download the GATK4 workflow

Set up a working directory.

```shell
mkdir gatk-workflows
cd gatk-workflows
```

Download the latest releases of the workflow from [here](https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels/releases).

```shell
# replace the following link with the latest release
wget https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels/archive/1.0.0.tar.gz 
# change the .tar.gz file name if needed
tar -zxvf 1.0.0.tar.gz
```



# Prepare input files

Set up inputs directory, and put all necessary input files in this directory .

```shell
mkdir inputs
```

Download necessary file from Google Cloud Bucket of Broad Institute https://console.cloud.google.com/storage/browser/gcp-public-data--broad-references/ using browser or [gsutil](https://cloud.google.com/storage/docs/gsutil_install#linux).

```shell
# This is an example of how to download with gsutil
gsutil -m cp gs://gcp-public-data--broad-references/hg38/v0/Homo_sapiens_assembly38.dbsnp138.vcf ./inputs/
```

Then modify the json file to replace the file/paths with your local files.





**Edit `gatk4-rna-best-practices.wdl` file**

This step may not be necessary but it worked for me. 

Search `BedToIntervalList ` in the `gatk4-rna-best-practices.wdl` file. You can see a block of code like the following.

```task gtfToCallingIntervals {
        ${gatk_path} \
            BedToIntervalList \
            -I=exome.fixed.bed \
            -O=${output_name} \
            -SD=${ref_dict}
```

This looks more like GATK3.8 commands but not GATK4, so I deleted the `=` sign behind `-I`, `-O`, `-SD` arguments. Then this block of code looks like:

        ${gatk_path} \
            BedToIntervalList \
            -I exome.fixed.bed \
            -O ${output_name} \
            -SD ${ref_dict}


# Execute the workflow

Everything should be all set so far. Download the [cromwell](https://github.com/broadinstitute/cromwell/releases) program which will execute the GATK4 workflow.

```shell
wget https://github.com/broadinstitute/cromwell/releases/download/51/cromwell-51.jar
# execute GATK4 workflow
java -jar cromwell-51.jar run gatk4-rnaseq-germline-snps-indels-1.0.0/gatk4-rna-best-practices.wdl --inputs gatk4-rnaseq-germline-snps-indels-1.0.0/gatk4-rna-germline-variant-calling.inputs.json
```





# References

[1]:https://github.com/gatk-workflows/gatk4-rnaseq-germline-snps-indels
[2]: https://gatk.broadinstitute.org/hc/en-us/articles/360035531192-RNAseq-short-variant-discovery-SNPs-Indels-
[3]:https://gatk.broadinstitute.org/hc/en-us/articles/360035530952?id=12521
