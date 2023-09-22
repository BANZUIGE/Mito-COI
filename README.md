# Mito-COI
  **Mito-COI database**

This is a non-redundant database based on NCBI that contains all eukaryotic mitochondria and COI genes. It contains almost all mitochondrial sequences and COI sequences as of _**December 2021**_.

# The database components provided by this GitHub Repository
  See pdf for the composition of the Venn diagram: venn.pdf
* Mito-COI  
  A non-redundant reference database of all mitochondrial and COI sequences for eukaryotes.
* Mitochondrial  
  A non-redundant reference database containing only mitochondrial-related sequences for eukaryotes.
* COI  
  A non-redundant reference database containing only COI-related sequences for eukaryotes.
* 3xia.genus  
  A characteristic database created with 57 common zooplankton genus recorded in the Three Gorges Reservoir area from historical records (not used in the article), for **tutorials** and **testing**.
  Smaller files are suitable for uploading to Github.

# Abbreviations used in this GitHub Repository and their meanings
* mito/mitochondrial  
  Mainly focus on keywords "Mitochondrion" and "Mitochondrial"
* COI  
  Mainly focus on keywords "COI" and "COX1"
* 3xia.genus  
  More than 50 common genus in the Three Gorges Reservoir area
* Euka/Eu  
  eukaryotic/eukaryotes
* acc  
  NCBI accession ID
* tax  
 taxonomy information
* taxid  
  The classification number (ID) corresponding to each accession ID on NCBI
* .qza  
  qza files used for qiime2 analysis, including classification information files and reference sequence files
* ref  
  reference
* refseq  
 reference sequences in the Database
* rmdup  
  Remove duplicate sequences based on 100% sequence similarity
* lab  
  Title corresponding to each sequence on NCBI
* fasta  
  fasta format file

# Tutorial on how to use and build a custom database
Please check out Folder **"TutorialOnHowToUseAndBuildACustomDatabase"** to learn how to use these databases we created and create your own unique database.
