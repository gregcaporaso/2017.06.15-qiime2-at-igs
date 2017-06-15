In this tutorial, you'll run a subset of the [QIIME 2 "Moving Pictures of the Human Microbiome" tutorial](https://docs.qiime2.org/2017.5/tutorials/moving-pictures/). The specific steps covered here are chosen to introduce you to some of the QIIME 2 interactive visualizations. To learn QIIME 2, you should following the steps in the [_Getting Started_ guide](https://docs.qiime2.org/2017.5/getting-started/).

# Launch and test your QIIME 2 environment

Load the interactive QIIME 2 environment (commands provided by Joshua Orvis). This will activate the QIIME 2 IGS Docker container and will put you in its root (`/`) directory.

```bash
git clone https://github.com/IGS/Chiron.git # You don't need to run this if you ran it during Joshua's session. 
./Chiron/bin/qiime2_interactive
```

First, test that QIIME 2 is available, and explore the available plugins. This tutorial has been designed for QIIME 2 2017.5. What version of QIIME 2 are you running?
```bash
qiime info
```

Next, enable tab-completion of QIIME 2 commands and parameters. This isn't essential, but makes QIIME 2 more convenient to use.
```
source tab-qiime
```

To confirm that tab-completion is working type ``qiime i`` and hit the ``tab``key. The command should auto complete to ``qiime info``.

Change to the `/output` directory. All QIIME 2 commands will be run in this directory so that we can access them outside of Docker.
```
cd /output
```

# Download relevant files

These files downloaded in this section are all part of the [QIIME 2 "Moving Pictures of the Human Microbiome" tutorial](https://docs.qiime2.org/2017.5/tutorials/moving-pictures/) data set. In the full tutorial, you would generate all of these files (with the exception of `sample-metadata.tsv`, which is provided for you).

```bash
curl -sL "https://data.qiime2.org/2017.5/tutorials/moving-pictures/sample_metadata.tsv" > "sample-metadata.tsv"
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/table.qza" > table.qza
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/rep-seqs.qza" > rep-seqs.qza
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/rooted-tree.qza" > rooted-tree.qza
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/taxonomy.qza" > taxonomy.qza
```

# Perform QIIME 2 analyses

In this section you'll run a few QIIME 2 visualizers. Each of these steps will generate a `.qzv` file, which you can view at https://view.qiime2.org. Each of these steps is described in detail in the [QIIME 2 "Moving Pictures of the Human Microbiome" tutorial](https://docs.qiime2.org/2017.5/tutorials/moving-pictures/), and questions are presented that will allow you to test your understanding of the visualizations.

## Generate summaries of the feature table and feature sequences
```bash
qiime feature-table summarize \
  --i-table table.qza \
  --o-visualization table.qzv \
  --m-sample-metadata-file sample-metadata.tsv

qiime feature-table tabulate-seqs \
  --i-data rep-seqs.qza \
  --o-visualization rep-seqs.qzv
```

## Rarefy the feature table and compute diversity metrics

```bash
qiime diversity core-metrics \
  --i-phylogeny rooted-tree.qza \
  --i-table table.qza \
  --p-sampling-depth 1080 \
  --output-dir core-metrics-results
```

## Evaluate differences in community richness across samples

```bash
qiime diversity alpha-group-significance \
  --i-alpha-diversity core-metrics-results/faith_pd_vector.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization core-metrics-results/faith-pd-group-significance.qzv
```

## Compare the relative similarity of samples in a PCoA plot

```bash
qiime emperor plot \
  --i-pcoa core-metrics-results/unweighted_unifrac_pcoa_results.qza \
  --m-metadata-file sample-metadata.tsv \
  --p-custom-axis DaysSinceExperimentStart \
  --o-visualization core-metrics-results/unweighted-unifrac-emperor.qzv
```

## Explore the taxonomic composition of samples

```bash
qiime taxa barplot \
  --i-table table.qza \
  --i-taxonomy taxonomy.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization taxa-bar-plots.qzv
```
