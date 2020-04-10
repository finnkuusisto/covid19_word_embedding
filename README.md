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

#### Word vector averaging

See biowordvec_covid19_approved_wvavg_match.ipynb.

So again, we get word vectors for the drug names by averaging the individual
token vectors and then ranking them by their similarity to the SARS treatment
vector. Here are the top 100 hits, along with cosine similarity. Hits related to
current COVID-19 investigation have been bolded (kind of positive controls).

1. gilteritinib fumarate \[0.560\]
   * Gliterinib is an anti-cancer (AML) drug and tyrosine kinase inhibitor (AXL and FLT3)
2. peramivir \[0.557\]
   * An antiviral for the flu and neuraminidase inhibitor
3. zanamivir \[0.547\]
   * Another antiviral for influenza A and B, and neuraminidase inhibitor
4. erdafitinib \[0.529\]
   * Another tyrosine kinase inhibitor (FGFR), for treatment of bladder cancer
5. __atovaquone and proguanil hydrochloride__ \[0.527\]
   * Antimalarial, including for chloroquine resistant malaria
   * Interesting due to the interest in (hydroxy)chloroquine
6. rimantadine hydrochloride \[0.525\]
   * Antiviral for influenza A and NMDA receptor antagonist
7. delavirdine mesylate \[0.524\]
   * Reverse-transcriptase inhibitor used to treat HIV, also a CYP3A4 inhibitor
   * Skeptical of this hit as SARS-CoV-2 is not a retrovirus, though other antiretrovirals are under investigation
8. __atazanavir sulfate and ritonavir__ \[0.522\]
   * Atazanavir is another antiretroviral with mechanism of action specific to HIV protease
   * Ritonavir is another antiretroviral, but acts generally as a protease inhibitor (CYP3A4 inhibitor)
   * Note that a combination Lopinavir/Ritonavir was found ineffective in severe COVID-19
9. cobimetinib fumarate \[0.520\]
   * A MEK inhibitor often used along with BRAF inhibitor (Vemurafinib) to treat melanoma
10. niclosamide \[0.520\]
    * Anti-tapeworm treatment that inhibits glucose uptake, oxidative phosphorylation, and anaerobic metabolism
    * Also under investigation to treat cancers and MRSA
11. __lopinavir and ritonavir__ \[0.519\]
    * This combination has been tested to treat COVID-19 and has so far been found ineffective
12. temsirolimus \[0.515\]
    * Prodrug of Sirolimus (Rapamycin) and mTOR inhibitor, used to treat renal cell carcinoma
    * Sirolimus is used to prevent organ transplant rejection and lymphangioleiomyomatosis (lung diesease)
13. rilpivirine hydrochloride \[0.511\]
    * Antiretroviral for HIV
14. alectinib hydrochloride \[0.509\]
    * Inhibitor of anaplastic lymphoma kinase (ALK) used to treat non-small-cell lung cancer
    * Subsequent inhibition of STAT3 and mTOR
15. lefamulin acetate \[0.508\]
    * Antibiotic for MRSA
16. perphenazine and amitriptyline hydrochloride \[0.507\]
    * Perphenazine is an antipsychotic, hits a lot of receptors
    * Amitriptyline is a tricyclic antidepressant, also hits a lot of receptors
17. alogliptin and metformin hydrochloride \[0.507\]
    * Alogliptin is an antidiabetic DPP-4 inhibitor
    * Metformin is also an antidiabetic - perhaps a result of using Metformin for my analogy?
    * Interesting relationship of note is that Metformin is a biguanide, some of which are antimalarials
18. tamiflu \[0.507\]
    * Oseltamivir is an antiviral for influenza A and B
    * Neuraminidase inhibitor
19. selinexor \[0.505\]
    * An anticancer drug and selective inhibitor of nuclear export
    * It stops transport from the nucleus to the cytoplasm, causing apoptosis
20. amprenavir \[0.504\]
    * Another protease inhibitor for HIV, like Lopinavir and Ritonavir
    * Tangential relation through renin inhibition, in the same system as angiotensin
21. ibuprofen and diphenhydramine citrate \[0.504\]
    * Ibuprofen is an NSAID
    * Diphenhydramine citrate is an antihistamine
    * Seeing a lot of these anticholinergics (ACh) showing up
22. olanzapine and fluoxetine hydrochloride \[0.503\]
    * Olanzapine is an atypical antipsychotic for schizophrenia and bipolar disorder
    * Fluoxetine (Prozac) is an SSRI (antidepressant) - hits a lot of targets
23. probenecid and colchicine \[0.502\]
    * Combination drug to treat gout
    * Probenecid increases uric acid excretion in the urine
    * Colchine is an anti-inflammatory - maybe helpful for cytokine storms?
24. erlotinib hydrochloride \[0.502\]
    * An EGFR tyrosine kinase inhibitor used to treat non-small-cell lung cancer
25. bicalutamide \[0.502\]
    * A nonsteroidal antiandrogen used to treat prostate cancer
    * Has a lot of off-label uses
26. alomide \[0.501\]
    * An antiallergic that works as a mast cell stabilizer
27. amantadine hydrochloride \[0.501\]
    * Nicotinic antagonist and NMDA antagonist used to treat dyskinesia from Parkinson's and influenza A
    * Prevents influenza A viral shedding
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
39. __chloroquine hydrochloride__ \[0.496\]
    * Antimalarial under investigation for COVID-19 treatment
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


#### String matching approach

See biowordvec_covid19_approved_name_match.ipynb.

Unlike the word vector averaging approach, I here just use string matching
against hits from the analogy search. I break all of the FDA drug names into
a pool of tokens and then compare every hit against all of those tokens. If
the analogy hit matches at least one drug token, I keep it as a hit.

1. peramivir \[peramivir\]
2. 2-rimantadine \[rimantadine\]
3. zanamivir \[zanamivir\]
4. oseltamivir-conjugated \[oseltamivir\]
5. rimantadine \[rimantadine\]
6. -oseltamivir \[oseltamivir\]
7. oseltamivir \[oseltamivir\]
8. acetylamoxapine \[amoxapine\]
9. rimantadine-resistant \[rimantadine\]
10. acetylamantadine \[amantadine\]
11. archazolids \[azolid\]
12. oseltamivir-zanamivir \[oseltamivir, zanamivir\]
13. bpei-niclosamide \[niclosamide\]
14. delavirdine \[delavirdine\]
15. gilteritinib \[gilteritinib\]
16. cyproheptadine-mediated \[cyproheptadine\]
17. erdafitinib \[erdafitinib\]
18. gefitinib-sensitizing \[gefitinib\]
19. cisplatin-inhibited \[cisplatin\]
20. oseltamivir-sensitive \[oseltamivir\]
21. ritonavir-mediated \[ritonavir\]
22. pronetupitant \[netupitant\]
23. niclosamide \[niclosamide\]
24. oseltamivir-peramivir \[oseltamivir, peramivir\]
25. rilpivirine-resistant \[rilpivirine\]
26. delavirdine-resistant \[delavirdine\]
27. tamiflu-resistant \[tamiflu\]
28. archazolid \[azolid\]
29. oseltamivir-induced \[oseltamivir\]
30. r-bicalutamide \[bicalutamide\]
31. saquinavir-resistant \[saquinavir\]
32. gefitinib-mediated \[gefitinib\]
33. tivantinib \[vantin\]
34. amprenavir-resistant \[amprenavir\]
35. amantadine-resistant \[amantadine\]
36. ritonavir-resistant \[ritonavir\]
37. tacrine-like \[tacrine\]
38. zanamivir-loaded \[zanamivir\]
39. amantadine \[amantadine\]
40. nelfinavir-mediated \[nelfinavir\]
41. etravirine-vrx-480773 \[etravirine\]
42. selinexor \[selinexor\]
43. promethins \[prometh\]
44. n-acetylamoxapine \[amoxapine\]
45. tamiflu \[tamiflu\]
46. atovaquone-sensitive \[atovaquone\]
47. nelfinavir-resistant \[nelfinavir\]
48. gefitinib-sensitive \[gefitinib\]
49. n-delavirdine \[delavirdine\]
50. zanamivir-selected \[zanamivir\]
51. tacrine-silibinin \[tacrine\]
52. fulvestrant-mediated \[fulvestrant\]
53. calanolide \[calan\]
54. peramivir-treated \[peramivir\]
55. tularine \[larin\]
56. tacrine-based \[tacrine\]
57. amantadines \[amantadine\]
58. nebularine \[larin\]
59. crizotinib-mediated \[crizotinib\]
60. precimetidine \[cimetidine\]
61. eupolauridine \[uridine\]
62. antihistamine-like \[histamine\]
63. amprenavir \[amprenavir\]
64. rilpivirine-based \[rilpivirine\]
65. thiazolidinone \[azolid\]
66. etravirine-based \[etravirine\]
67. ammonia-activated \[ammonia\]
68. -bicalutamide \[bicalutamide\]
69. amantadine-resistance \[amantadine\]
70. gefitinib-resistant \[gefitinib\]
71. temsirolimus \[sirolimus, temsirolimus\]
72. selenazolidine \[azolid\]
73. virazole \[virazole\]
74. chlorpromazine-blocked \[chlorpromazine, promazine\]
75. afuresertib \[urese\]
76. cimetidine-like \[cimetidine\]
77. zanamivir-resistant \[zanamivir\]
78. doravirine \[doravirine\]
79. bicalutamide-resistant \[bicalutamide\]
80. mefloquine-mediated \[mefloquine\]
81. gefitinib-interfering \[gefitinib\]
82. lefamulin \[lefamulin\]
83. oseltamivir-resistant \[oseltamivir\]
84. theophyllinacetate \[acetate, theophyl\]
85. microseiramide \[micro\]
86. saquinavir-no \[saquinavir\]
87. mefloquine-oxazolidine \[azolid, mefloquine\]
88. cisplatin-activated \[cisplatin\]
89. zanamivir-treated \[zanamivir\]
90. nilutamide \[nilutamide\]
91. revefenacin \[revefenacin\]
92. cell-killing \[cell\]
93. erlotinib-sensitizing \[erlotinib\]
94. desipramine-like \[desipramine, pramine\]
95. chlorazicin \[loraz\]
96. oseltamivir-resistance \[oseltamivir\]
97. imipramine-like \[imipramine, pramine\]
98. chlorpromazine-like \[chlorpromazine, promazine\]
99. alomide \[alomide\]
100. fulvestrant-induced \[fulvestrant\]
101. chloroquine-dependent \[chloroquine\]
102. regorafenib-mediated \[regorafenib\]
103. theophylline-cimetidine \[cimetidine, theophyl, theophylline\]
104. bicalutamide \[bicalutamide\]
105. rimantadine-sensitive \[rimantadine\]
106. impentamine \[pentam\]
107. colchicine-dependent \[colchicine\]
108. cimetidine-resistant \[cimetidine\]
109. metrodinazole \[metrodin\]
110. promethion \[prometh\]
111. oseltamivir-treated \[oseltamivir\]
112. pralidoxime-compounds \[compound, pralidoxime\]
113. mefloquine-binding \[mefloquine\]
114. mefloquine-resistant \[mefloquine\]
115. chlor-promazine \[promazine\]
116. taribavirin \[ribavirin\]
117. cholinesterase-inhibitor \[choline\]
118. amantadine-bound \[amantadine\]
119. radafaxine \[afaxin\]
120. ps-15-atovaquone \[atovaquone\]
121. promazines \[promazine\]
122. sorafenib-sensitive \[sorafenib\]
123. enzastaurin-pemetrexed \[pemetrexed\]
124. niclosamide- \[niclosamide\]
125. methanesulfonamides \[sulfonamides\]
126. stiripentol \[stiripentol\]
127. ceritinib \[ceritinib\]
128. pentamines \[pentam\]
129. osimertinib-resistant \[osimertinib\]
130. thioxothiazolidinone \[azolid\]
131. aminophylline-resistant \[aminophyllin, aminophylline\]
132. fluoxetine-resistant \[fluoxetine\]
133. nitazoxanide-based \[nitazoxanide\]
134. 7-methoxycryptopleurine \[methoxy\]
135. crizotinib-sensitive \[crizotinib\]
136. cyanonilutamide \[nilutamide\]
137. decitabine-vorinostat \[decitabine, vorinostat\]
138. salmeterol-mediated \[salmeterol\]
139. oxazolidinone-resistant \[azolid\]
140. calanolides \[calan\]
141. methopromazine \[promazine\]
142. exposure-activity \[exposure\]
143. tidoxil \[doxil\]
144. pyridostigmine-inhibited \[pyridostigmine\]
145. desmethyl-gefitinib \[gefitinib\]
146. gefitinib \[gefitinib\]
147. tranylcipromine \[cipro\]
148. citalopram-mediated \[citalopram\]
149. gefitinib- \[gefitinib\]
150. 8-methoxyquinolones \[methoxy\]
151. deschloroketamine \[ketamine\]
152. atovaquone-resistant \[atovaquone\]
153. aminophyllines \[aminophyllin, aminophylline\]
154. s-fluoxetine \[fluoxetine\]