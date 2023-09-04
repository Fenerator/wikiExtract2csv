# wikiExtract tools

## Description

This is a simple python script to extract the content of a Wikipedia dump and save it as a csv file compatible with Label Studio.

- by default, it samples the firest 100 articles from the dump
- it extracts the title, id, url, and text of each article

## Quick Start

Modify the two variables in the shell script [wikiDumpToCSV.sh](wikiDumpToCSV.sh) to your needs. These are marked with `# TODO` in the script. Then, run the script, e.g.:

```bash
./wikiDumpToCSV.sh
```

the script will download the dump file, run Wiki Extractor on it, and then run the script [wikiExtract2csv.py](wikiExtract2csv.py) on the output generated by Wiki Extractor. Details are described in the section below. The two output `.csv` files (`articles_sampled_QA.csv` and `articles_sampled_NER.csv`) will be saved in the directory specified by `OUT_FOLDER` in the script.

### Details

1. Download the [Wikipedia dump](https://dumps.wikimedia.org/) corresponding to the desired languge from here ``https://dumps.wikimedia.org/<ISO CODE OF THE LANGUAGE>wiki/`` [e.g. for ALS](https://dumps.wikimedia.org/alswiki/). Then, select the following variant of the dump file:
<img width="743" alt="image" src="https://github.com/Fenerator/wikiExtract2csv/assets/33670163/01e9561d-0860-46c4-9b7e-6bdd07914b9e">

For ALS, the link is [https://dumps.wikimedia.org/alswiki/20230701/alswiki-20230701-pages-articles.xml.bz2](https://dumps.wikimedia.org/alswiki/20230701/alswiki-20230701-pages-articles.xml.bz2).

1. Run Wiki Extractor on the downloaded dump file, e.g.: `python -m wikiextractor.WikiExtractor alswiki-20230701-pages-articles.xml.bz2 --output ../../Documents/MRL_ST_2023/enwiki-20230420_extracted.`. There is no need to unzip the file before running Wiki Extractor.
2. To convert the data to the required format, run the script [wikiExtract2csv.py](wikiExtract2csv.py) on the output generated in step 2. Make sure the output file's suffix is `.csv`.
3. To generate the question-answer-pairs, run the script [questions2QA.py](questions2QA.py) on the output generated in step 2. Again, make sure the output file's suffix is `.csv`.

## Dependencies

- Python 3.9.12
- other dependencies are listed in [requirements.txt](requirements.txt)

## ``wikiExtract2csv.py``

### Usage

```python
python wikiExtract2csv.py --input INPUT_DIR [--output OUTPUT_DIR] [--sample_size SAMPLE_SIZE] [--min MIN_LENGTH] --split
```

- `INPUT_DIR`: path to the directory containing the output of Wiki Extractor, e.g. first folder in the output folder generated by the Wiki Extractor. E.g., `../../Documents/enwiki/AA/`.
- `OUTPUT_DIR`: path to the directory where the output csv file will be saved, default is `./articles.csv`
- `sample_size`: number of articles to sample from the input, default is `100`
- `min`: minimum number of characters an article needs to have to be included in the output, default is `1000`.
- `split_by`: Choose whether to split the text of each article in sentences or paragraphs. Default is both, creates two seperate cvs output files. If you want to split by sentences, use `--split_by sentence`. If you want to split by paragraphs, use `--split_by paragraph`.

#### Example usage

to generate sentence samples and paragraphs samples:

```python
python wikiExtract2csv.py --input "../../Documents/MRL_ST_2023/enwiki-20230420_extracted/AA/" --output "../../Documents/MRL_ST_2023/enwiki/" --sample_size 100 --min 1000
```

## ``questions2QA.py``

This is a simple python script to create question-answer-pairs from a Label Studio snapshot. It extracts the questions and texts from the snapshot and saves them in a csv file, creating one task per question.

### Usage

```python
python questions2QA.py --input INPUT_FILE [--output OUTPUT_FILE]
```

- `INPUT_FILE`: json file containing LabelStudio snapshot
- `OUTPUT_FILE`: path to the output csv file containing , default is `./article_question_pairs.csv`

#### Example usage

```python
python questions2QA.py --input "../../Documents/alswiki/text/example_QA_export.json" --output "../../Documents/alswiki/article_question_pairs.csv"
```

## ``get_text_from_url.py``

Script to scrape the relevant wikipedia articles from the urls in the language biography csv files. The script extracts the text from the urls and saves it in a csv file containing the id, url, and text of each article. The script also splits the text into sentences and paragraphs and saves the output in seperate csv files.

### Usage

First create language biography by running: wikiExtract2csv/NER/get_wikipedia_url_from_wikidata.py on the `id_list.csv` file. Make sure to change the language codes in the script.

```python
python get_text_from_url.py --input INPUT_FILE_PATH  --output OUTPUT_FOLDER --split_by SPLIT_BY         
```

- `INPUT_FILE_PATH`: path to the language biography csv file containing the urls and ids of the articles, e.g. `NER/lang_biography/en.csv`
- `OUTPUT_FOLDER`: path to the output folder where the processed text files will be saved.
- `SPLIT_BY`: Choose whether to split the text of each article in sentences or paragraphs. Default is both, creates two seperate cvs output files. If you want to split by sentences, use `--split_by sentence`. If you want to split by paragraphs, use `--split_by paragraph`

#### Example Usage

```python
python get_text_from_url.py --input NER/lang_biography/en.csv --output Test_Outputs_NER/ --sample_size 10 --split_by sentence         
```
