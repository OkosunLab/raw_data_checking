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
find -name "PRIZM*fastq.gz" | xargs -I{} mv {} FASTQ_Raw/
```
