# assembly
after downloading the `.sra` file, next step is to transform `.sra` to `.fastq` which can be processed by most of software. however, `SPAdes genome assembler v4.2.0` can be configured to read NCBI SRA files directly. see the full guideline as below.   
(https://ablab.github.io/spades/installation.html)
## install
1. install SPAdes under your `home` directory
   ```
    wget https://github.com/ablab/spades/releases/download/v4.2.0/SPAdes-4.2.0.tar.gz
    tar -xzf SPAdes-4.2.0.tar.gz
    cd SPAdes-4.2.0
   ```
## acvtivate
   SPAdes requires only Python 3.8+. our HPC provides python from 2.17.2 to 3.12.2, `module load python/3.9.2`.
1. to acvtivate SPAdes, add executables (spades.py, rnaspades.py, etc.) to `PATH` environment variable.
   ```
   vim ~/.bashrc
   ```
   this allows to edit your `PATH`
   ```
   export PATH=$HOME/software/SPAdes-4.2.0/bin:$PATH
   ```
   never change `:$PATH`
   ```
   source ~/.bashrc
   ```
   activate/reload your configuration
3. after this contiguration, enter:
   ```
   spades.py -v
   ```
   if return `SPAdes genome assembler v4.2.0`, then you can successfully use SPAdes.
