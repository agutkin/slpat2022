# Data for SLPAT 2022

This directory contains Wikipedia data used for the experiments submitted with the paper
for the 9th Workshop on Speech and Language Processing for Assistive Technologies
([SLPAT](http://www.slpat.org/slpat2022/)). The data is used to train large static
n-gram model.

## Corpus

The preprocessed 20M-line Wikipedia text corpus
`en_wiki_collection_20200621_pagesplit_shuf.train.20Mline.txt` was compressed
using `xz` utility into multiple chunks.

1.  Compression:

    ```shell
    xz -e9v -T 0 en_wiki_collection_20200621_pagesplit_shuf.train.20Mline.txt
    ```

1.  Chunking (into chunks of 50Mb):

    ```shell
    cat en_wiki_collection_20200621_pagesplit_shuf.train.20Mline.txt.xz | \
      split -b 50m - en_wiki_collection_20200621_pagesplit_shuf.train.20Mline.txt.xz.
    ```

The symbols file
`wiki/en_wiki_collection_20200621_pagesplit_shuf.train.20Mline.ge20.tsv` contains
symbols that occur 20 times or more in the text.

If for some reason reconstructing the original file is required, please run:

```shell
cat wiki/*.xz.* | unxz -fcv -T 0 > en_wiki_collection_20200621_pagesplit_shuf.train.20Mline.txt
```
