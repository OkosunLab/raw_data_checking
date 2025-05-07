# Data integrity checking

## md5sum

### What is MD5 and why is it helpful?

MD5 is a 128-bit hash, commonly used as a digital fingerprint. The hashing algorithm takes a file and returns the hash. Whilst possible that different files *could* have the same hash this is quite unlikely. In addition any changes to the file (e.g. data loss/corruption during download etc ...) will likely lead to dramatically different hashes. As such it is a very hand tool to verify that the files you have recieved are the ones that were sent.

595f44fec1e92a71d3e9e77456ba80d1 original_file.txt
595f44fec1e92a71d3e9e77456ba80d1 downloaded_file.txt
71f920fa275127a7b60fa4d4d41432a3 corrupted_downloaded_file.txt

### Real world use case

I actually had WGS data sent to us. R1.fastq.gz and R2.fastq.gz had differing hashes which matched those that were sent along with the data. However the QC looked odd and it turned out that the gunzip files (R1.fastq and R2.fastq) had the same hash value. This allowed us to ask them to resend the correct R2 files and save a lot of hastle

### How to use MD5

Find the md5sum file attached to your data (if this isn't sent, ask the providers for one).

They might have just one file for the tar archive or md5 values for each fastq.

Either way you can get the md5 for a file like this:

```
md5sum $NewArchive.tar.gz
md5sum $NewFile.fastq.gz
```

You can also rum md5sum on a whole batch of files like this:

```
md5sum *
md5sum FASTQ_Raw/*
```

If you are running it on multiple files at once you can store it in a file

```
md5sum * > local.md5.txt
```

You can even use diff to compare a file of local and remote md5s to mostly automate the checks.

**NB** the file names need to be the same in both files otherwise diff will pick these up as differences. You also need the files to be in the same order. 

```
diff <(remote.md5.txt | sort) <(local.md5.txt | sort)
```

## untaring the archive

```
tar -zxvf $NewArchive.tar.gz
```


## Move FASTQ files into FASTQ_Raw folder

First you need to make a new directory in the /data/BCI_OkosunLab/Raw_Data folder to store the data.

```
mkdir /data/BCI_OkosunLab/Raw_Data/Project_folder/
```

Find all of the raw fastq files and move them into a fastq folder inside this project folder.

**NB** Depending on who sends you the data you may also have "subsample" fastq files which makes this trickier. Often your full fastq files will start with a project ID you can use to get them specifically. 

```
mkdir /data/BCI_OkosunLab/Raw_Data/Project_folder/FASTQ_Raw
find -name "$PROJECT*fastq.gz" | xargs -I{} mv {} /data/BCI_OkosunLab/Raw_Data/Project_folder/FASTQ_Raw
```

## Create symbolic links for fastq files back to the raw data folder

symbolic links are a great way to store a local link to your data inside of the project folder. Multiple people can symlink the same file, so this helps keep disk usage down, compared to copying the files you want to work with.

From inside the FASTQ_Raw folder of the project directory (NOT Raw_Data folder)

```
for file in $(find /data/BCI_OkosunLab/Raw_Data/Project_folder/ -name "*fastq.gz"); 
  do ln -s $file $(basename $file | sed 's/-/_/g');
done
```

sed 's/-/_/g' will replace any - with _ as they can cause issues down the line.

## Sample sheet generation

```
echo "sample" > SampleSheet.txt;
  for file in $(find FASTQ_Raw/ -name "*R1*");
  do sample=$(basename $file | sed 's/_S[0-9].*//');
  echo $sample; done  >> SampleSheet.txt
```
