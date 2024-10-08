# Eva's analysis of the GSK longitudinal dataset

This dataset was generated by GSK using an UPLC system and a hybrid
electrospray LTQ/Orbitrap mass spectrometric platform
(UPLC-LTQ/Orbitrap-MS) to determine the human metabolic profiles
associated with food intake and exercise.

## Materials and methods

Nine healthy male subjects provided plasma samples. They underwent 4
regimens:

1. Regimen A: food+, exercise-
2. Regimen B: food-, exercise+
3. Regimen C: food+, exercise+
4. Regimen D: food-, exercise-

The subjects' plasma were taken at 8 time points (TP) during each
regimen. All volunteers were placed on a controlled diet 24 hours prior
to the start of each regimen. The first set of plasma samples (TP 0)
were taken from individuals in the morning at 8 am after overnight
fasting. Thereafter, two groups of volunteers received breakfast
(volunteers following regimens A and C; food+ intervention), whilst the
other two groups did not consume food at this time (volunteers following
regimens B and D; food- intervention). The next set of plasma samples
were taken at 9 am (TP 1) and 12 noon (TP 4). Volunteers following the
exercise+ intervention (regimens B and C) then exercised for 30 min at
60% of maximum heart rate level, whilst the volunteers following
exercise- intervention (regimens A and D) did not undertake any physical
activity. The fourth set of plasma samples was taken at 1 pm (TP 5).
After this time point, volunteers of all regimens followed the same
schedule for food and drink intake, and additional samples were taken at
4 pm (TP 8), 8 pm (TP 12), 11 pm (TP 16) and at 8 am the following day
(TP 24).

After completing the first regimen, each volunteer followed a controlled
diet for the next 24 hours before starting another randomly selected
regimen. After completion of this second regimen, all volunteers were
discharged and then re-admitted after two weeks to undergo the remaining
two regimens. Each of the regimens was again preceded by a 24 hour
controlled diet.

### Metabolic profiling

Two identical sets of plasma samples were prepared in randomized order
from a total of 288 samples which were collected from the 9 volunteers
undergoing 4 regimens at 8 time points. Two 100 μl aliquots were taken
from each sample for the preparation of duplicate samples for analysis
by the UPLC-LTQ/Orbitrap-MS instrument in ESI+ and ESI- modes. In
addition, 40 μl from each sample were used to prepare pooled QC samples.
All samples were produced in accordance to the standard preparation
protocol described in Section 2.3.

Each identical set of plasma samples was split into 4 analytical blocks and
analysed together with a standard metabolite mix, blank and pooled QC
samples by the UPLC-LTQ/Orbitrap-MS system (Section 2.5.2). The first
set of samples was analysed in ESI- mode, whilst ESI+ mode was used in
the analysis of the second set of samples. Ten pooled QC samples were
analysed at the beginning of each analytical block. Thereafter, a pooled
QC sample was analysed after every fourth plasma sample (Appendix 4).

To avoid any possible bias introduced by the order in which the samples
were collected, prepared or analysed, three independent randomizations
were performed for choosing the regimen sequence for each volunteer as
well as sample preparation and analysis sequence.

### Data pre-processing

All data files were transformed into netCDF format and deconvolved using
XCMS software (Section 2.7).

This was the R code used by Eva:

```
xset <- xcmsSet(step=0.02,snthresh=3,mzdiff = 0.05)
grp <- group(xset,bw=10,mzwid = 0.05)
pc.tmp = ""
an <- annotate(grp, cor_eic_th=0, cor_exp_th=0,na.ok=TRUE)
write.csv(an$annotated,'data0210mzwid05diff05.csv')
```

**How to organise the data files in directories for xcms processing?**


### Data pre-treatment

1. The data produced from the four analytical blocks of plasma within
each ionization mode were combined and normalised to the peak areas of
pooled QC samples.
2. Features showing low reproducibility were removed from each data set 
(Section 2.8).
3. Both normalized data matrices originating from the two ionization
modes were joined into a single matrix and the data normalized to TP 0.
This normalization procedure enables to partially compensate for the
metabolic differences among the volunteers and to identify the changes
in metabolome that are relative to the baseline.

**This is done using the MATLAB scripts written by Dave Broadhurst**

A two-step process was used for the pre-treatment of data originating
from the analysis of QC samples. Firstly, any feature vector of UPLC-MS
feature responses for all QC samples with more than 40% of its data
consisting of missing values was removed. This step was undertaken due
to the fact that vectors which are missing over 40% of values show poor
repeatability across the experiment and would not represent the actual
distribution in normal samples. QC samples are biologically identical
and therefore it would be expected that identical results (within an
expected error range) would be observed. Secondly, for all the UPLC-MS
feature vectors that pass through the first step of pre-treatment, the
relative standard deviation (RSD) is calculated as a ratio of population
standard deviation to population mean of intensity responses. Feature
vectors with RSDs higher than 20% in more than 60% of the samples were
considered to show poor repeatability for biologically identical samples
and were removed.

Features from QC samples that passed the pre-treatment process are
expected to be highly reproducible. Analogous features were taken from
data originating from normal samples of serum/plasma and the measured
intensity response was normalised to the intensities of corresponding QC
features. The process of normalisation involved the division of the
median of feature intensity responses measured for QC samples with the
intensity response of each feature for a normal sample. These
pre-treated data were then submitted for further statistical analysis.

**Does the MATLAB scripts written by Dave B do the above?**

**Is there a R script equivalent?**

### Data analysis

The subsequent process of data analysis is summarized in Figure 6.1.
Statistical analyses using multivariate and univariate methods on
pre-treated data were performed using the agents provided in the MATLAB
software (version 7.0). The statistical analyses were defined in the
MATLAB programming language and these scripts were provided by Dr David
Broadhurst. Principal Component Analysis (PCA) (Section 1.4.2.3.5.2) was
applied for the reduction of data dimensionality and the first 30
Principal Components were used for Canonical Variate Analysis (Section
1.4.2.3.5.3) The optimal number of Principal Components was selected by
random permutation cross- validation, where the model was repeatedly
built on 70% of the total sample set (training set) and tested by
projection of remaining 30% of samples through the model (test set). For
each iteration of the cross-validation process, training/test samples
were randomly assigned after stratification by regimen class.


The entire data matrix was then split on the basis of time points to
produce four subsets of data corresponding to time points 1, 4, 5 and 8.
Furthermore, data from TP 5 and TP 8 were further split on food
intervention resulting in a further four subsets of data; TP 5 and TP 8
subsets for food+ intervention and TP 5 and TP 8 subsets for food-
intervention. Kruskal-Wallis analysis (Section 1.4.2.3.5.1) together
with the calculation of the area under ROC curve (Section 1.4.2.3.3)
were performed on each of the six data subsets (TP 1, TP 4, TP 5 food+,
TP 8 food+, TP 5 food- and TP 8 food-). The influence of food was
examined by undertaking two sets of univariate non-parametric analysis
on data from TP 1 and TP 4. A null hypothesis was postulated for the TP
1 and 4 univariate tests by presuming there is no difference in the
metabolic profiles of samples from volunteers with or without food
intake prior to TP 1 sample collection. The effects of exercise were
examined by performing univariate tests on the remaining four sets of
data from TP 5 and TP 8. For these cases, a null hypothesis was
postulated which presumed there is no difference between samples from
volunteers from exercise+ versus exercise- interventions

Putative metabolite annotation was obtained for features with p<0.01
significance levels from the six Kruskal-Wallis tests using the process
described in Section 4.2.4. As discussed in detail in Chapter 4, a
single metabolite separated by the UPLC system into a single
chromatographic peak can be detected by MS as several features
corresponding to various adducts, fragments and/or isotopes. Such
features originating from one compound were grouped together based on tR
similarities, Pearson correlation coefficient values (Section 1.4.2.2.4)
and adduct/fragment/isotope annotations. The p-values for all features
that passed the data pre-treatment step were added to the results from
the automated part of putative metabolite annotation. The semi-automated
part of the putative identification process was performed by calculation
of Pearson correlation coefficient values and manual retrieval of
putative metabolite identification.