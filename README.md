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

## Training word n-gram model

### Prerequisites

Install [OpenFst](https://www.openfst.org/twiki/bin/view/FST/WebHome) and [OpenGrm NGram](https://www.opengrm.org/twiki/bin/view/GRM/NGramLibrary)
libraries:

Install OpenFst library:

```shell
mkdir -p build && cd build
wget https://www.openfst.org/twiki/pub/FST/FstDownload/openfst-1.8.2.tar.gz
tar xvfz openfst-1.8.2.tar.gz && cd openfst-1.8.2
./configure --enable-far --enable-ngram-fsts
make -j10 && make install
cd ../..
```

Build and install NGram library and tools:

```shell
mkdir -p build && cd build
wget https://www.opengrm.org/twiki/pub/GRM/NGramDownload/ngram-1.3.14.tar.gz
tar xfz ngram-1.3.14.tar.gz && cd ngram-1.3.14
./configure
make -j10 && make install
cd ../..
```

### Training

Make sure the tools installed above are in the path and, in particular, that the
shared objects can be found:

```shell
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

Reconstruct the original file (this takes about five minutes):

```shell
TXTNAME=en_wiki_collection_20200621_pagesplit_shuf.train.20Mline
cat wiki/*.xz.* | unxz -fcv -T 0 > "${TXTNAME}".txt
```

Train the word n-gram model (takes a while):

```shell
farcompilestrings --symbols="wiki/${TXTNAME}".ge20.syms --keep_symbols --unknown_symbol="<UNK>" "${TXTNAME}".txt | ngramcount --order=3 | ngrammake | ngramshrink --target_number_of_ngrams=100000000 > "${TXTNAME}".ge20.100Mng.mod.fst
```
