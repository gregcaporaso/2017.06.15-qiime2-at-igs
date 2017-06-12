Load the interactive QIIME 2 environment (commands provided by Josh Orvis).
```bash
git clone https://github.com/IGS/Chiron.git
./Chiron/bin/qiime2_interactive
```

This command enables tab-completion of QIIME 2 commands.
```
source tab-qiime
```

Test that QIIME 2 is available, and explore the available plugins.
```bash
qiime info
```

Change to the `output` directory. All commands will be run in this directory so that we can access them outside of Docker.
```
cd /output
```

Download relevant files.

```bash
curl -sL "https://data.qiime2.org/2017.5/tutorials/moving-pictures/sample_metadata.tsv" > "sample-metadata.tsv"
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/table.qza" > table.qza
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/rep-seqs.qza" > rep-seqs.qza
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/rooted-tree.qza" > rooted-tree.qza
curl -sL "https://docs.qiime2.org/2017.5/data/tutorials/moving-pictures/taxonomy.qza" > taxonomy.qza
```

QIIME 2 analyses, derived from the [QIIME 2 "Moving Pictues tutorial"](https://docs.qiime2.org/2017.5/tutorials/moving-pictures/). Each of these steps will generate a `.qzv` file, which you can view at https://view.qiime2.org.

```bash
qiime feature-table summarize \
  --i-table table.qza \
  --o-visualization table.qzv \
  --m-sample-metadata-file sample-metadata.tsv

qiime feature-table tabulate-seqs \
  --i-data rep-seqs.qza \
  --o-visualization rep-seqs.qzv
  
qiime diversity core-metrics \
  --i-phylogeny rooted-tree.qza \
  --i-table table.qza \
  --p-sampling-depth 1080 \
  --output-dir core-metrics-results

qiime diversity alpha-group-significance \
  --i-alpha-diversity core-metrics-results/faith_pd_vector.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization core-metrics-results/faith-pd-group-significance.qzv

qiime emperor plot \
  --i-pcoa core-metrics-results/unweighted_unifrac_pcoa_results.qza \
  --m-metadata-file sample-metadata.tsv \
  --p-custom-axis DaysSinceExperimentStart \
  --o-visualization core-metrics-results/unweighted-unifrac-emperor.qzv
  
qiime taxa barplot \
  --i-table table.qza \
  --i-taxonomy taxonomy.qza \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization taxa-bar-plots.qzv
```