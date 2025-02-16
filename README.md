![GitHub last commit](https://img.shields.io/github/last-commit/czbiohub/GenoPrimer)

# GenoPrimer
Automated primer design for genotyping CRISPR edited cells via amplicon sequencing   
GenoPrimer is described in the protoSpaceJAM [preprint](https://www.biorxiv.org/content/10.1101/2023.10.04.560793v1)

## Features
- Two modes: short (250 bp amplicon, MiSeq) and long (~3000 bp amplicon, PacBio)
- Automatically relaxes the criteria if no primers are found initially
- Invokes primer3 to perform thermodynamics calculation
- Use Bowtie (alternatively BLAST) to check unintended PCR products
  - Autodetects OS and use matching executables for Linux, MacOS, Windows
  - Autodetects CPU number and multi-threads Blast search ( saves 2 CPUs for the user)
- Automatically downloads and uses the human genome by default

## Inputs

- A csv file containing minimumlly three columns (with the exact names), each row is a separate design:
  - **ref**  
      The genome/build version, takes two possible values: ensembl_GRCh38_latest or NCBI_refseq_GRCh38.p14  
  - **chr**  
      e.g. 2  
  - **coordinate**  
      Center position of the amplicon, in the form of coordinates on the chromosome, e.g. 45389323

### [Helper script]
If you only have gRNA sequences but not their cutsites coordinates in the genome or the Ensemble IDs,
there is a helper script "get_gRNA_cutsite.py" that can obtain cutsite coordinates by mapping gRNA to the genome
See the usage section for more details

## Outputs:
- A csv file with the input information + new columns: 
  -  Up to three pairs of primers for each gene/row, including Tm and expected product size.
  -  A numeric number indicating how many rounds of criteria relaxation before yielding primers (column "Rounds_relax_of_primer_criteria")

## Automated workflow (for one site) 
![image](https://github.com/czbiohub-sf/GenoPrimer/assets/4129442/e82970ee-bcef-409e-84f4-0b8507dd5040)


&nbsp;
## Usage:
clone the repository
```
git clone https://github.com/czbiohub/GenoPrimer.git
```
Go the repository directory, switch he branch if running branch other than master:
```
cd GenoPrimer
git checkout <branch you'd like to run>
```

Create conda environment
```
conda env create -f environment.yml
```

You are ready to run GenoPrimer
```
conda activate GenoPrimer
python GenoPrimer.py --csv input/example.csv --type "short"
```
Notes:  
(1) During first-time run, the program will download the human genome and generate Bowtie databases  
(2) In some OS, It may be required to grant permission to Bowtie executables, for example:
```
chmod a+xX bin/bowtie-1.3.1-linux-x86_64/*
```

### Helper script
Input:
- A csv file containing minimumlly two columns (with the exact names):
  - "ENST" or "gene_name" (e.g., ENSG00000068784 or SRBD1) Note: "ENST" is preferred over "gene_name" 
  - gRNA_protospacer (The sequence of the protospacer, not including the PAM, e.g., GGGCTCTCCCTGGGCGGCCA)  
  - ref (currently one of the two options: "ensembl_GRCh38_latest", "NCBI_refseq_GRCh38.p14"
Usage:
```
python get_gRNA_cutsite.py --csv gRNA.csv
```


