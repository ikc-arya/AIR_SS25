# Task 4 Scientific Web Discourse

## Steps to the best result with BM25


### Seventh iteration of changes (Including the authors of the document)
As previously stated, mentions are very rare in the query set which could indicate that the authors behind the papers are rarely mentioned. The authors could however still be namned in the query, but not by mentions. For this reason another strategy used was to include the name of the authors in the tokens representing the document.
This offered now improvment in MRR-score on the development set of queries (as shown below), and also introduced the problem of potiential false matches, where a authors could share a first name with authors of the actual article that is referenced by the tweet, without having anything to do with the article.
![bild](https://github.com/user-attachments/assets/37f26884-6fbe-4148-898f-4146e2e97bb8)

Too reduce the risk of false matches the last names are extracted from the authors name before they are appened to the tokens representing the document. Only a small improvment was seen in the MRR-score but is still kept since in theory it should improve matching, the small improvment on the MRR-score could just be the effect of few author names being mentioned in training or test data, while it could still be a factor in other query-sets
![bild](https://github.com/user-attachments/assets/15659fa8-6d3d-439a-aa18-0c2dc104806f)





### Sixth iteration of changes (Synonym lookups)
Previous versions have been using the spaCy library for namned entity recognition, entities that where then appended to the tokenized string of the document. The spaCy library is quite general, and in some cases the namned entities identified didn't offer improvment in retriving the documents. Focusing on more topic specific ways of improving the results could offer an advantage. Since the queries come from social media, in the form of tweets which a lot of times are not written with a scientific language in mind, one option is to use a thesaurus to look up synonyms for terms that are commonly used when talking about the COVID-19 pandemic.
One such thesaurus has been created by Patricia Fener and is available at: https://loterre.istex.fr/C0X/en/ with the following description:
"This bilingual thesaurus (French-English), developed at Inist-CNRS, covers the concepts from the emerging COVID-19 outbreak which reminds the past SARS coronavirus outbreak and Middle East coronavirus outbreak. This thesaurus is based on the vocabulary used in scientific publications for SARS-CoV-2 and other coronaviruses, like SARS-CoV and MERS-CoV. It provides a support to explore the coronavirus infectious diseases."

This thesaurus is used as a lookup when normalizing all the tokens in the corpus, as well as the queries, to one specific key. Too achive this the file was filtered to only contain a word that acted as the key, which was the word that all synonyms was converted to, and values which where all synonyms to the key. 

The first strategy applied was too do a synonym normalization before other pre-processing and before spaCy was used for namned entity recognition.

The score for this strategy was as shown below, and offered no improvment over using only spaCy  
![bild](https://github.com/user-attachments/assets/b760974f-97bc-43c5-97bd-63fbf5295b17)

However without using the spaCy library for namned entity recognition, the results where improved, as shown below. One reason for this improvment could be that the way spaCy was implemented meant that the namned entities where added too the end of the string that was tokenized, and no differentiation was made between tokens from the title, abstract or if they came from the namned entities. A potential problem that could arrise from this is that words that where not significant to the topic of the document or the tweet where given more significance since they appeared an extra time because of identified namned entities being appended to the document string. 
![bild](https://github.com/user-attachments/assets/2ccaeda7-c6b8-478e-b20b-dd2c5839a2fc)


### Fifth iteration of changes (extract synonyms before stemming + keep numbers)
- Tried to extract synonyms before stemming to see if it would help improve the results but it didn't
- Decided to keep the numbers in the text as they are important for the scientific discourse, results were slightly better:

| Set   | Top-K | Score     |
|--------|--------|------------|
| Train | 1     | 0.5573 |
| Train | 5     | 0.6095 |
| Train | 10     | 0.6095 |
| Dev   | 1     | 0.5479 |
| Dev   | 5     | 0.5992 |
| Dev   | 10     | 0.5992 |


### Fourth iteration of changes (comparing different BM25 scoring models)

Compared different BM25 scoring models available in the rank_bm25 library.

1. Created a function `compare_bm25_models` that can evaluate different BM25 model variants
2. Added a `sample_size` parameter to control how much data is used for evaluation
3. Compared three BM25 models with default parameters:
   - BM25Okapi: The original BM25 algorithm
   - BM25L: A variant that addresses the issue with long documents
   - BM25Plus: Another variant with improved handling of term frequency saturation

The comparison was performed on the full training dataset to ensure statistically significant and reliable results that better represent real-world performance.

#### Results:

##### Results on the train set:
| Model | Top-1 | Top-5 | Top-10 |
|-------|-------|-------|--------|
| BM25Okapi | 0.5269 | 0.5814 | 0.5814 |
| BM25L | 0.1989 | 0.2753 | 0.2753 |
| BM25Plus | 0.5312 | 0.5863 | 0.5863 |


##### Results on the dev set:
| Model | Top-1 | Top-5 | Top-10 |
|-------|-------|-------|--------|
| BM25Okapi | 0.5107 | 0.5703 | 0.5703 |
| BM25L | 0.2114 | 0.2816 | 0.2816 |
| BM25Plus | 0.5186 | 0.5769 | 0.5769 |


#### Analysis:

BM25Okapi and BM25Plus performed similarly well, with BM25Plus slightly outperforming BM25Okapi in both the training and development sets. BM25L, on the other hand, significantly underperformed compared to the other two models.\
This is due to the fact that BM25L is designed to handle long documents better, but in our case, the documents are relatively short and do not require this adjustment.\
We will stick with BM25Plus for the final model as it provides a good balance between performance and complexity.

Final results with BM25Plus:

| Set   | Top-K | Score     |
|--------|--------|------------|
| Train | 1     | 0.5312 |
| Train | 5     | 0.5863 |
| Train | 10     | 0.5863 |
| Dev   | 1     | 0.5186 |
| Dev   | 5     | 0.5769 |
| Dev   | 10     | 0.5769 |


### Third iteration of changes (trying to extract more useful data from the training set)
Analyzed empirically other features of the training set:
1. mentions: are very rare in the training set (only 4/12853 queries in the training set have mentions) 
   - decided to ignore them
2. authors: often mentioned in the tweets without preceding @ - tried to extract them and move them through the same 
    preprocessing pipeline as the rest of the text on both queries and documents side but the achieved results were slightly worse:\

| Set   | Top-K | Score     |
|-------|-------|-----------|
| Train | 1     | 0.5269    |
| Train | 5     | 0.5814    |
| Train | 10    | 0.5814    |
| Dev   | 1     | 0.5107    |
| Dev   | 5     | 0.5703    |
| Dev   | 10    | 0.5703    |

This can probably be explained by the fact that the authors are not always mentioned in the same way in the tweets and the documents.\
This could due to misspellings, different forms of the name (e.g. first name + last name vs. last name + first name), or even different names altogether (e.g. a nickname).
This leads to minimal to no improvement in the BM25 technique that relies on exact matching of the terms.
- decided to ignore them
3. journals: the journal titles are very specific and rarely mentioned in the tweets of the training set. 
   - decided to ignore them 
4. hashtags: are very specific and usually combined of multiple words, meaning they wouldn't contribute match to plain words matching that bm25 is based on 
   - decided to ignore them


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

| Set   | Top-K | Score     |
|--------|--------|------------|
| Train | 1     | 0.5299 |
| Train | 5     | 0.5845 |
| Train | 10     | 0.5845 |
| Dev   | 1     | 0.5243 |
| Dev   | 5     | 0.5785 |
| Dev   | 10     | 0.5785 |


## Noor's original results:
| Set   | Top-K | Score     |
|--------|--------|-----------|
| Train | 1     | 0.5221 |
| Train | 5     | 0.5747 |
| Train | 10    | 0.5747 |
| Dev   | 1     | 0.5186 |
| Dev   | 5     | 0.5740 |
| Dev   | 10    | 0.5740 |

## Original baseline results:
| Set   | Top-K | Score     |
|-------|-------|-----------|
| Train | 1     | 0.5081    |
| Train | 5     | 0.5510    |
| Train | 10    | 0.5560    |
| Dev   | 1     | 0.5057    |
| Dev   | 5     | 0.5523    |
| Dev   | 10    | 0.5577    |
