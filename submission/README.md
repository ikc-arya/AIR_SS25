## What does this repository contain?
#### `bm25_best_final.ipynb` 
is the notebook creating and running our enhanced BM25 version and outputs preranked lists of documents for each query.
`Thesaurus_covid19_formatted_flattend.csv` is the thesaurus used in the preprocessing for our BM25 version.

#### `df_query_[datasplit]_top100.pkl`
`df_query_train_top100.pkl`, `df_query_dev_top100.pkl` and `df_query_test_top100.pkl` contain these preranked lists of 100 documents per query for the train, dev and test data

#### `ColBERT_reranking.ipynb` 
reads the preranked lists and performs a ColBERT reranking after being finetuned on the train data

#### `predictions.tsv`
are the final output predicitions / ranked lists per query from the test dataset of our ranking pipeline which were submitted to the challenge.
