# DiBT-Notes
Learning notes for DBiT-Notes, credit to Mingyu Yang https://github.com/MingyuYang-Yale/DBiT-seq.

## DBiT work flow
  1. Experiments on tissue samples;
     - Sample thickness < 10 um;
     - 50 * 50 barcodes;
     - Extract the Sequences using Illumina kit;
  2. Sequencing by Novogene;
     - Check the library QC report.
       - Copy the ![image](https://user-images.githubusercontent.com/25277637/152657283-475b4575-fe49-41ac-8979-a2cfa375c31b.png) into the excel file named "Sample QC Overall"
     - Batch download the data to HPC folder;
       - Make new project folder and put raw data in it with a name of **0.raw_data**
        ```
        wget -r -c ftpxxx
        ```
       - Make shortcuts for the raw data folder if necessary, the folder name will be the same as hC2 (see examples)
        ```
        ln -s /gpfs/ysm/project/fan/sb2723/01.Spatial_hCortex/01.rawdata/usftp21.novogene.com/raw_data/hC2
        ```
       - Uzip and zip the raw data from Novogene using _gzip.sh_
   3. Set up the ST Pipeline environment (conda) on HPC
      - Follow the instruction of ST Pipeline https://github.com/jfnavarro/st_pipeline
      - Current procedure
        ```
        module load miniconda
        conda create -n st-pipeline python=3.7
        conda activate st-pipeline
        conda install PySam
        conda install Numpy
        conda install Cython
        pip install taggd
        pip install stpipeline
        ```
      - Test the installment
        ```
        st_pipeline_run.py -h
        ```
   4. Set up Perl environment on HPC, and run _effective.sh_ afterwards
        ```
        wget https://cpan.metacpan.org/authors/id/N/NW/NWCLARK/PerlIO-gzip-0.20.tar.gz
        module avail Perl
        module load Perl/5.28.0-GCCcore-7.3.0
        tar -zxvf PerlIO-gzip-0.20.tar.gz 
        cd PerlIO-gzip-0.20/
        mkdir mybuild
        perl Makefile.PL PREFIX=/gpfs/ysm/project/fan/sb2723/01.Spatial_hCortex/00.bin/PerlIO-gzip-0.20/mybuild
        make
        make install
        ```
## HPC Data Processing
   1. Make the index of the reference
      - Once it's settled up, no need to change unless there's new sample or new updates
        - Current version of human gene reference
   2. Filter the raw data and rearrange read format to be compatible with ST Pipeline using _effective.sh_
      - Perl file is used for the processing, _1-effective.pl_
        ```
        sbatch hC1.stpipeline.sh
        ```
   3. Run st-pipeline
      - Remember to get into the miniconda environment
        ```
        module load miniconda
        conda activate st-pipeline
        ```
      - Run ST pipeline.
        - _stpipeline.sh_ is in /00.sh, just run _hC1.stpipeline.sh_ because it's running the _stpipeline.sh_
        ```
        sbatch hC1.stpipeline.sh
        ```
   4. Change ID using _changeid.sh_
      - Run the _changeid.sh_ for samples.
        ```
        module load miniconda
        conda activate st-pipeline
        srun --pty -p interactive --mem=16g bash
        sh changeid.sh
        ```

## Other stuffs
### HPC Work Commands
   1. Request for interactive job memory
   
       ```
       srun --pty -p interactive -c 1 --mem=6g bash
       ```
   2. search in the history
     
       ```
       history | grep interactive
       ```
   3. Substitute something in batch
     
       ```
       :%s/(original)/(replaced)/g
       ```
   4. Save history
     
       ```
       history > logxxx
       ```
