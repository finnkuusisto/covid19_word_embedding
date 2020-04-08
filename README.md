# Word Embedding Mining for SARS-CoV-2 and COVID-19
Playing around with word vectors for SARS-CoV-2. Using the
[BioWordVec](https://github.com/ncbi-nlp/BioWordVec) prebuilt embeddings and
seeing what might be related to treatments for COVID-19.

## Methods

### Getting potential drug/compound/target hits

I imagine most people who have knowledge of word embeddings are familiar with
the idea that there is semantic structure in the space, such that you can find
meaningful analogies.

The typical example of this is: *Man -> King* as *Woman -> ?* points one to
*Queen*. Effectively, the vector between Man and King represenst some kind of
"monarch/royalty" vector.

Here, I have just taken this same concept and tried to derive a "treats" vector.
At time of writing, I have only use metformin and diabetes as my seed analogy,
and I am using SARS and MERS as my endpoints. Thus, the analogies I am using to
get hits are: *Metformin -> Diabetes* as *? -> SARS* and *? -> MERS*.

I am then manually evaluating the top hits for each analogy. See my notes below.

### Direct drug repurposing

Given the urgency of the situation, drug repurposing is generally considered the
most valuable approach to finding treatments for COVID-19. Approved drugs can be
prescribed off label without the need for extensive clinical trials. That does
not preclude trials for efficacy, but we already know approved drugs have been
tested for safety.

Thus, there is an argument for filtering the potential drug/compound/target hits
to known FDA approved drugs. I downloaded the [FDA's approved drugs database](
https://www.fda.gov/drugs/drug-approvals-and-databases/drugsfda-data-files) and
will be using this to filter hits from my previous step using two methods:
* Direct drug name string matching
* Multi-token vector similarity matching

### Processing FDA approved drug names

I downloaded the [FDA's approved drugs database](
https://www.fda.gov/drugs/drug-approvals-and-databases/drugsfda-data-files)
and pulled out a list of individual approved drug names by the following method:
1. Pulled all entries from the DrugName and ActiveIngredient columns
2. Removed all text following the first '(' from every entry
3. Split all entries into their multiple names and active ingredient by ';'
4. Manually added back in drug/ingredient names lost to step 2
   * ampicillin
   * cycrin
   * hydrocortisone
   * premarin
   * sulfabenzamide
   * sulfacetamide
   * sulfathiazole
   * sulfadiazine
   * sulfamerazine
   * sulfamethazine

See process_fda_approved_names.ipynb.

## Results

Here are notes on unfiltered hits from the SARS treatment analogy, as well as
notes on the hits filtered with FDA approved drug names.

### Potential drug/compound/target hits

These are notes from my manual assessment of hits on the SARS treatment analogy.

**[SARS-3CLPRO](https://en.wikipedia.org/wiki/C30_Endopeptidase)**

A family of enzymes found in coronaviruses. Cleave the coronavirus polyprotein
into multiple functional components for viral replication. Potential target for
inhibition.

"Molecular modeling suggests that available rhinovirus 3Cpro inhibitors may be
modified to make them useful for treating SARS"
https://pubmed.ncbi.nlm.nih.gov/12746549/

Crystal Structure of SARS-CoV-2 Main Protease Provides a Basis for Design of
Improved α-Ketoamide Inhibitors
https://pubmed.ncbi.nlm.nih.gov/32198291/


**[Cantharidin](https://en.wikipedia.org/wiki/Cantharidin)**

A compound secreted by blister beetles. Causes severe chemical burns and
blistering when applied to the skin.

Absorbed by the lipid membranes of epidermal cells (I assume other cells too)
and causes the release of serine proteases, breaking down cell-to-cell
adhesion. Makes sense then that it causes blistering when applied to skin.

There is some evidence that Cantharidin can be used to inhibit Hepatitis B
production, but it's far too toxic to be ingested.
https://www.ncbi.nlm.nih.gov/pubmed/17458779


**[Chelidonine](https://en.wikipedia.org/wiki/Chelidonine)**

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

**[SAR405](https://pubchem.ncbi.nlm.nih.gov/compound/sar405)**

SAR405 is a selective Vps34 inhibitor. Vps34 phosphorylates phosphatidylinositol
to form phosphatidylinositol (3)-phosphate (PtdIns3P). PtdIns3P is a
phospholipid in cell membranes that recruits proteins to the membrane, many of
which are involved in protein trafficking.

### Repurposed drug hits

Filtering the analogy [Metformin->Diabetes as ?->SARS] hits to FDA approved drug
names. As described above, one method using string matching while the other uses
word vector averaging for ranking drug names.

#### String matching approach

See biowordvec_covid19_approved_name_match.ipynb.

Still working on cleaning this up.

#### Word vector averaging

See biowordvec_covid19_approved_wvavg_match.ipynb.

So again, we get word vectors for the drug names by averaging the individual
token vectors and then ranking them by their similarity to the SARS treatment
vector. Here are the top 100 hits, along with cosine similarity.

1. gilteritinib fumarate \[0.560\]
2. peramivir \[0.557\]
3. zanamivir \[0.547\]
4. erdafitinib \[0.529\]
5. atovaquone and proguanil hydrochloride \[0.527\]
6. rimantadine hydrochloride \[0.525\]
7. delavirdine mesylate \[0.524\]
8. atazanavir sulfate and ritonavir \[0.522\]
9. cobimetinib fumarate \[0.520\]
10. niclosamide \[0.520\]
11. lopinavir and ritonavir \[0.519\]
12. temsirolimus \[0.515\]
13. rilpivirine hydrochloride \[0.511\]
14. alectinib hydrochloride \[0.509\]
15. lefamulin acetate \[0.508\]
16. perphenazine and amitriptyline hydrochloride \[0.507\]
17. alogliptin and metformin hydrochloride \[0.507\]
18. tamiflu \[0.507\]
19. selinexor \[0.505\]
20. amprenavir \[0.504\]
21. ibuprofen and diphenhydramine citrate \[0.504\]
22. olanzapine and fluoxetine hydrochloride \[0.503\]
23. probenecid and colchicine \[0.502\]
24. erlotinib hydrochloride \[0.502\]
25. bicalutamide \[0.502\]
26. alomide \[0.501\]
27. amantadine hydrochloride \[0.501\]
28. azelastine hydrochloride and fluticasone propionate \[0.501\]
29. revefenacin \[0.500\]
30. imipramine pamoate \[0.500\]
31. doravirine \[0.500\]
32. rosiglitazone maleate and metformin hydrochloride \[0.499\]
33. nefazodone hydrochloride \[0.498\]
34. mefloquine hydrochloride \[0.498\]
35. abacavir sulfate and lamivudine \[0.498\]
36. carisoprodol compound \[0.497\]
37. triprolidine and pseudoephedrine hydrochlorides w/ codeine \[0.497\]
38. soma compound w/ codeine \[0.497\]
39. chloroquine hydrochloride \[0.496\]
40. saquinavir mesylate \[0.496\]
41. linagliptin and metformin hydrochloride \[0.495\]
42. nilutamide \[0.495\]
43. memantine hydrochloride and donepezil hydrochloride \[0.495\]
44. donepezil hydrochloride and memantine hydrochloride \[0.495\]
45. nelfinavir mesylate \[0.495\]
46. ceritinib \[0.495\]
47. virazole \[0.494\]
48. vorinostat \[0.493\]
49. triprolidine and pseudoephedrine hydrochlorides \[0.493\]
50. fulvestrant \[0.492\]
51. gefitinib \[0.492\]
52. oseltamivir phosphate \[0.492\]
53. ibuprofen and diphenhydramine hydrochloride \[0.492\]
54. formoterol fumarate \[0.491\]
55. bedaquiline fumarate \[0.491\]
56. efavirenz, lamivudine and tenofovir disoproxil fumarate \[0.491\]
57. lamivudine and tenofovir disoproxil fumarate \[0.491\]
58. stiripentol \[0.491\]
59. propoxyphene hydrochloride and acetaminophen \[0.491\]
60. desvenlafaxine fumarate \[0.489\]
61. dolutegravir, lamivudine, and tenofovir disoproxil fumarate \[0.489\]
62. efavirenz, emtricitabine, and tenofovir disoproxil fumarate \[0.489\]
63. efavirenz, lamivudine, and tenofovir disoproxil fumarate \[0.489\]
64. ribociclib succinate \[0.489\]
65. repaglinide and metformin hydrochloride \[0.488\]
66. ponatinib hydrochloride \[0.488\]
67. alpelisib \[0.488\]
68. alosetron hydrochloride \[0.488\]
69. quetiapine fumarate \[0.487\]
70. emtricitabine and tenofovir disoproxil fumarate \[0.487\]
71. belinostat \[0.487\]
72. metformin hydrochloride \[0.487\]
73. olaparib \[0.487\]
74. loratadine and pseudoephedrine sulfate \[0.486\]
75. upadacitinib \[0.486\]
76. fedratinib hydrochloride \[0.486\]
77. advil dual action with acetaminophen \[0.486\]
78. acetaminophen and pentazocine hydrochloride \[0.486\]
79. ixazomib citrate \[0.485\]
80. dextromethorphan hydrobromide and quinidine sulfate \[0.485\]
81. darolutamide \[0.485\]
82. glyburide and metformin hydrochloride \[0.485\]
83. clemastine fumarate \[0.485\]
84. roflumilast \[0.485\]
85. amodiaquine hydrochloride \[0.485\]
86. lamivudine and stavudine \[0.484\]
87. hydrocodone polistirex and chlorpheniramine polistirex \[0.484\]
88. lapatinib ditosylate \[0.484\]
89. fesoterodine fumarate \[0.484\]
90. daclatasvir dihydrochloride \[0.483\]
91. promethazine with codeine \[0.483\]
92. pazopanib hydrochloride \[0.483\]
93. aminophyllin \[0.483\]
94. sulfamethoxazole and trimethoprim and phenazopyridine hydrochloride \[0.483\]
95. ketotifen fumarate \[0.482\]
96. pioglitazone hydrochloride and metformin hydrochloride \[0.482\]
97. norethindrone and ethinyl estradiol and ferrous fumarate \[0.482\]
98. celecoxib \[0.482\]
99. chlordiazepoxide and amitriptyline hydrochloride \[0.482\]
100. abemaciclib \[0.482\]
