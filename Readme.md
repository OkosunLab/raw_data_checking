# Data integrity checking

## md5sum

Find the md5sum file attached to your data (if this isn't sent, ask the providers for one).

They might have just one file for the tar archive or md5 values for each fastq.

Either way you can get the md5 for a file like this:

```
md5sum $NewArchive.tar.gz
md5sum $NewFile.fastq.gz
```

## untaring the archive

```
tar -zxvf $NewArchive.tar.gz
```

## Move FASTQ files into FASTQ_Raw folder

```
mkdir FASTQ_Raw
find -name "$PROJECT*fastq.gz" | xargs -I{} mv {} FASTQ_Raw/
```

## Create symbolic links for fastq files back to the raw data folder

From inside the FASTQ_Raw folder of the project directory (NOT Raw_Data folder)

```
for file in $(find /data/BCI-OkosunLab/Raw_Data/$Path/$To/FASTQ_Raw/ -name "*fastq.gz"); 
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
