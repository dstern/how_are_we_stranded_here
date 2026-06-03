========================
how_are_we_stranded_here
========================

.. image:: https://img.shields.io/pypi/v/how_are_we_stranded_here.svg
        :target: https://pypi.python.org/pypi/how_are_we_stranded_here

Python package for testing strandedness of RNA-Seq fastq files

**This is an improved fork with bug fixes, modern kallisto support, and easier installation.**

Improvements Made
-----------------
✓ Fixed critical bugs in GTF/GFF3 conversion
✓ Support for modern kallisto versions (0.44.0 and newer, including 0.48.0+)
✓ Better error handling and reporting
✓ Modern Python packaging (pyproject.toml)
✓ Conda environment file for easy installation
✓ Version-pinned dependencies for reproducibility

See `INDEX_OF_IMPROVEMENTS.md <INDEX_OF_IMPROVEMENTS.md>`_ for detailed documentation.

Ever get RNA-Seq data where the library prep or strandedness has been omitted in the methods?

This should save some headaches later in your pipeline and analysis when you realise you've used the wrong strandedness setting (RF/fr-firststrand, FR/fr-secondstrand, unstranded)

Requirements
------------
how_are_we_stranded_here requires the following packages be installed:

- **Python** >= 3.6.0
- **kallisto** >= 0.44.0 (not locked to 0.44 anymore - modern versions work!)
- **RSeQC** >= 4.0.0
- **numpy** >= 1.16.0
- **pandas** >= 0.24.0

It also requires a transcriptome annotation (.fasta file - e.g. ensembl's .cdna.fasta, or a prebuilt kallisto index), and a corresponding gtf.

Installation
------------

**Option 1: Using Mamba or Conda (Recommended)**

Use mamba for fastest installation:

.. code-block:: console

        mamba env create -f https://raw.githubusercontent.com/dstern/how_are_we_stranded_here/main/environment.yml
        conda activate stranded_here

Or with conda (slower but works):

.. code-block:: console

        conda env create -f https://raw.githubusercontent.com/dstern/how_are_we_stranded_here/main/environment.yml
        conda activate stranded_here

**Option 2: Manual Installation**

For development or if you have dependencies already:

.. code-block:: console

        # Install Python dependencies
        pip install numpy pandas
        
        # Install RSeQC and kallisto from bioconda
        mamba install -c bioconda rseqc kallisto
        
        # Install this package
        pip install git+https://github.com/dstern/how_are_we_stranded_here.git

Usage
-----
For basic usage, run check_strandedness with a gtf transcript annotation, transcripts fasta file and fastq read files from one sample.

.. code-block:: console

        check_strandedness --gtf Yeast.gtf --transcripts Yeast_cdna.fasta --reads_1 Sample_A_1.fq.gz --reads_2 Sample_A_2.fq.gz

Output
------
check_strandedness will print to console the results of infer_experiment.py (http://rseqc.sourceforge.net/#infer-experiment-py), along with an interpretation.

.. code-block:: console

        checking strandedness
        Reading reference gene model stranded_test_WT_yeast_rep1_1_val_1_1/Saccharomyces_cerevisiae.R64-1-1.98.bed ... Done
        Loading SAM/BAM file ...  Total 20000 usable reads were sampled
        This is PairEnd Data
        Fraction of reads failed to determine: 0.0595
        Fraction of reads explained by "1++,1--,2+-,2-+": 0.0073 (0.8% of explainable reads)
        Fraction of reads explained by "1+-,1-+,2++,2--": 0.9332 (99.2% of explainable reads)
        Over 90% of reads explained by "1+-,1-+,2++,2--"
        Data is likely RF/fr-firststrand

Any intermediate files are written to a folder in your current working directory derived from the name of the reads_1 file.

How it Works
------------
check_strandedness.py runs a series of commands to check which direction reads align once mapped in transcripts.

It first creates a kallisto index (or uses a pre-made index) of your organisms transcriptome.

It then maps a small subset of reads (default 200000) to the transcriptome, and uses kallisto's --genomebam argument to project pseudoalignments to genome sorted BAM file.

It finally runs RSeQC's infer_experiment.py to check which direction reads from the first and second pairs are aligned in relation to the transcript strand, and provides output with the likely strandedness of your data.

Documentation
--------------
For more details about the improvements and bug fixes, see:

- `INDEX_OF_IMPROVEMENTS.md <INDEX_OF_IMPROVEMENTS.md>`_ - Quick navigation guide
- `ROBUSTNESS_IMPROVEMENTS.md <ROBUSTNESS_IMPROVEMENTS.md>`_ - Detailed documentation of all fixes
- `VERIFICATION_REPORT.txt <VERIFICATION_REPORT.txt>`_ - Verification and testing results
