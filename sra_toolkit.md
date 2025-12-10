# Brief
When download the data from ncbi with the accession numbers, sratoolkit is necessary. There is a detailed guide for sratoolkit: 
https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit.
Here are more specific guidelines to start.  
First of all, make a list of SRR numbers like:   
```
SRR10932013
SRR10932014
...
```
# download data to HPC (recommended)
1. make sure download data to the storage:cd /strorage/...
2. the hpc have sratoolkit, but quick configuration is needed. run `vdb-config -i` and use `tab` to select and `enter` to choose. `Remote Access` should be enabled and you have to choose the ideal directory as your data `Cache`. all the files downloaded will be stored in your  `Cache`.
3. to ensure your downloads continue even after you disconnect your SSH session from the HPC, you need to run the process within a persistent session manager or submit it as a background job. `screen` is the effective way:
   ```
   screen -S your_job_name
   ```
5. module needed: `module load sratoolkit` & `module load parallel` and check the module availability: `module list`.
6. to download 4 runs at the same time:
   ```
   parallel -j 4 prefetch ::: $(cat your_SRR_list) 
   ```
   or download all the runs you need in your list at the same time
   ```
   parallel prefetch ::: $(cat your_SRR_list)
   ```
   now you can safely leave the screen by entering `Ctrl + A + D`. do not leave ternimal without leave screen! if you get tired of parallel citation note, enter `parallel --citation`
7. to check your downloading, enter:
   ```
   ps -ef | grep -E "fasterq-dump|prefetch" | grep -v grep
   ```
8. after you have done this, you will see three files for a run in your `Cache` :`.sra.lock`,`.sra.prf`,`.sra.tmp`. when data downloaded successfully, there will be only `.sra`. to log back:
    ```
   screen -ls
    ```
    this shows all your screen sessions
   ```
   screen -r screen_session
   ```
   this enable you reattach to the specific screen session
10. in most situation, your downloading process will not disrupt. but if you disrupt the downloading in screen with `Ctrl + C`, the `.sra.lock` file will not autodelete. relaunching the downloading will make error:
    ```
    warn: lock exists while copying file - Lock file ... exists: download canceled
    ```
    you have to delete all the `.sra.lock` files with `rm *.sra.lock`
# download data to PC  
1. you have to download and install `sratoolkit`.
2. mac maybe activate the software by `sudo spctl --master-disable` and `sudo spctl --master-enable` when finishing.
3. `cd pathway_to_sratookit/bin` and `prefetch SRR10931995`. because local storage is limited, we will not download multiple data in parallel.
# format transformation (optional)
`fasterq-dump` is one tool of sratoolkit package. we use it to transform `.sra` to `.fastq`.  
1. log back or create a new `screen`. make sure `sratoolkit` is available.
2. navigate to the directory where `.sra` files are stored
3. enter the command below:
   ```
   find . -maxdepth 1 -name "*.sra" | parallel fasterq-dump --split-files --outdir path_to_fastq {}
   ```
   `find . -maxdepth 1 -name "*.sra"` will find all the `.sra` files and pipe them to parallel. `--split-files` means the data will be processed as pair-end.
4. when all the `.sra` files are processed, you can cancel the `screen` by
   ```
   exit
   ```
- to check the size of your downloaded data:
  ```
  du -sh /storage/..
  ```
  this shows how much you use under specific dir
- or maybe you want to check all of your quota, especially when you have a large size of data
  ```
  lfs quota -uh $USER /storage/
  ```
  this command show all of your data in the HPC
# conclusion
the logic is download sra file through prefetch, while using parallel to download multiple sra at the same time. transform sra (which can be deleted after) file to fastq file using fasterq-dump.  
- the ultimate command can be:  
```
parallel 'prefetch {} && fasterq-dump {} --gzip --split-files -O ./fastq_out && rm -f "path_to_Cache/{}.sra"' ::: $(cat path_to_SRR_list)
```
