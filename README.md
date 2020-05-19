# Word Embedding Mining for SARS-CoV-2 and COVID-19 Drug Repurposing
The rapid spread of illness and death caused by the severe respiratory syndrome coronavirus 2 (SARS-CoV-2) and its associated coronavirus disease 2019 (COVID-19) demands a rapid response in treatment development.
Limitations of de novo drug development, however, suggest that drug repurposing is best suited to meet this demand.
Due to the difficulty of accessing electronic health record (EHR) data in general and in the midst of a global pandemic, and due to the similarity between SARS-CoV-2 and SARS-CoV, we propose mining the extensive biomedical literature for treatments to SARS that may also then be appropriate for COVID-19.
In particular, we propose a method of mining a large biomedical word embedding for FDA approved drugs based on drug-disease treatment analogies.
We find several drugs that have been suggested or are currently in clinical trials for COVID-19 in our top hits and present the rest as promising leads for further experimental investigation.
We thus find our approach promising and present it, along with suggestions for future work, to the computational drug repurposing community at large as another tool to help fight the pandemic.


Using the [BioWordVec](https://github.com/ncbi-nlp/BioWordVec) prebuilt embeddings and the [FDA's approved drugs database](https://www.fda.gov/drugs/drug-approvals-and-databases/drugsfda-data-files).
See `covid19_word_embed_mining.ipynb` for the main analysis.
See `process_fda_approved_names.ipynb` for FDA approved drug name preprocessing.

Licensed under CC0-1.0.