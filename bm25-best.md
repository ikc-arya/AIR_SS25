# Task 4 Scientific Web Discourse

## Steps to the best result with BM25

### Second iteration of changes (evaluating different spacy models)
- Added argument `terms_extractor` to the function `preprocess_text` to control the terms extraction model
- Installed the following models: [
    "en_core_web_sm",
    "en_core_web_md",
    "en_core_web_lg",
    "en_core_sci_md",
]
- Created function `compare_spacy_models` that runs bm25 on small sample for each model and outputs a DF with the results:

| Model | Top-1 | Top-5 | Top-10 |
|-------|-------|-------|--------|
| en_core_web_sm | 0.57 | 0.6215 | 0.6215 |
| en_core_web_md | 0.58 | 0.6217 | 0.6217 |
| en_core_web_lg | 0.58 | 0.6115 | 0.6115 |
| en_core_sci_md | 0.55 | 0.6162 | 0.6162 |
- `en_core_web_md` seemed to be more stable for different samples so it was decided to take it as the default model
- Reran the entire process with `en_core_web_md` and the results were as follows:

Results on the train set: {1: 0.5258694468217536, 5: 0.581183381311756, 10: 0.581183381311756}
Results on the dev set: {1: 0.5185714285714286, 5: 0.5752261904761904, 10: 0.5752261904761904}

| Set   | Top-K | Score     |
|--------|--------|------------|
| Train | 1     | 0.5259 |
| Train | 5     | 0.5812 |
| Train | 10     | 0.5812 |
| Dev   | 1     | 0.5186 |
| Dev   | 5     | 0.5752 |
| Dev   | 10     | 0.5752 |

The result show that the model does not have a big impact on the final result. 
Other parameters should be considered for further tuning.

### First iteration of changes (cleaning up the code, adding parallelization, caching and minor bugs fixing)
- Took Noor's function for text processing
- Pulled the .lower up before extracting important terms because they're in lowercase from the beginning
- Unescaped html before removing punctuations to transform html characters like &amp; into &
- Introduced pandarallel to distribute the workload of pandas apply across multiple CPU cores
- Extracted the dict used as cache of terms - text2bm25top from get_top_cord_uids function to make it actually cache something
- Fixed preprocess_text to extract extra terms into whitespace separated string instead of list
- Cleaned up unused blocks of code, comments, installs and imports

Results on the train set: {1: 0.5299151948961331, 5: 0.5845055629036022, 10: 0.5845055629036022}
Results on the dev set: {1: 0.5242857142857142, 5: 0.5785357142857143, 10: 0.5785357142857143}

| Set   | Top-K | Score     |
|--------|--------|------------|
| Train | 1     | 0.5299 |
| Train | 5     | 0.5845 |
| Train | 10     | 0.5845 |
| Dev   | 1     | 0.5243 |
| Dev   | 5     | 0.5785 |
| Dev   | 10     | 0.5785 |


## Noor's original results:
Results on the train set: {1: 0.5221349101377111, 5: 0.5746868435384734, 10: 0.5746868435384734)}
Results on the dev set: {1: 0.5185714285714286, 5: 0.5740119047619048, 10: 0.5740119047619048)}

| Set   | Top-K | Score     |
|--------|--------|-----------|
| Train | 1     | 0.5221 |
| Train | 5     | 0.5747 |
| Train | 10    | 0.5747 |
| Dev   | 1     | 0.5186 |
| Dev   | 5     | 0.5740 |
| Dev   | 10    | 0.5740 |

## Original baseline results:
Results on the train set: {1: 0.5081303975725512, 5: 0.5509777224513084, 10: np.0.5559614579512657}
Results on the dev set: {1: 0.5057142857142857, 5: 0.5522738095238094, 10: 0.557658163265306}


