# covid19_word_embedding
Playing around with word vectors for SARS-CoV-2.

Nothing to see here really. Just mucking around with the
[BioWordVec](https://github.com/ncbi-nlp/BioWordVec) prebuilt embeddings and
seeing what might be related to treatments for COVID-19.

I don't know a ton about Jupyter Notebooks, so no judging...


## Summary of findings


Some notes on the hits that come up.


#### [SARS-3CLPRO](https://en.wikipedia.org/wiki/C30_Endopeptidase)
A family of enzymes found in coronaviruses. Cleave the coronavirus polyprotein
into multiple functional components for viral replication. Potential target for
inhibition.

"Molecular modeling suggests that available rhinovirus 3Cpro inhibitors may be
modified to make them useful for treating SARS"
https://pubmed.ncbi.nlm.nih.gov/12746549/

Crystal Structure of SARS-CoV-2 Main Protease Provides a Basis for Design of
Improved α-Ketoamide Inhibitors
https://pubmed.ncbi.nlm.nih.gov/32198291/


#### [Cantharidin](https://en.wikipedia.org/wiki/Cantharidin)
A compound secreted by blister beetles. Causes severe chemical burns and
blistering when applied to the skin.

Absorbed by the lipid membranes of epidermal cells (I assume other cells too)
and causes the release of serine proteases, breaking down cell-to-cell
adhesion. Makes sense then that it causes blistering when applied to skin.

There is some evidence that Cantharidin can be used to inhibit Hepatitis B
production, but it's far too toxic to be ingested.
https://www.ncbi.nlm.nih.gov/pubmed/17458779


#### [Chelidonine](https://en.wikipedia.org/wiki/Chelidonine)
This is a tricky one. Chelidonine is an acetylcholinesterase and
butyrylcholinesterase inhibitor. Activity is generally associated with the
central nervous system.

One possible connection is that acetylcholinesterase degrades acetylcholine into
acetate and choline and, according to Wikipedia:
>The esteratic subsite, where acetylcholine is hydrolyzed to acetate and choline,
>contains the catalytic triad of three amino acids: serine 200, histidine 440 and
>glutamate 327. These three amino acids are similar to the triad in other serine
>proteases except that the glutamate is the third member rather than aspartate.

I'm outside my depth at this point, but serine protease TMPRSS2 is important for
SARS-CoV-2 cell entry. It cuts open the spike protein of the virus. Tangential
relation to serine protease inhibition then? Importantly, there are several AChE
inhibitors that are safe and available.

Another possible connection is that chelidonine is the major alkaloid component
of Chelidonium majus which contains various isoquinoline alkaloids.
(Hydroxy)Chloroquine, which is currently being investigated for SARS-CoV-2
treatment, is a derivative of quinoline.

