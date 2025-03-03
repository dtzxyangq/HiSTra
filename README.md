# HiSTra


### Installation
Dependency
```shell
conda create -n HiSTra python=3.8 
conda activate HiSTra
conda install numpy scipy pandas=1.3.5 matplotlib seaborn h5py
conda install -c conda-forge -c bioconda cooler=0.8.11
pip install matplotlib-venn 
```
Linux OS

```shell
pip install HiSTra
```
### Preparation

Download [juicer_tool](https://github.com/aidenlab/juicer/wiki/Juicer-Tools-Quick-Start) and [deDoc](https://github.com/yinxc/structural-information-minimisation). Because of the update of the two softwares, **we recommend that you download them from this repo.** You can find relevant jar files in the HiSTra/juice and HiSTra/deDoc, respectively.

Make sure chromosome.sizes file is exactly the file used in generating test(and control) sample(.hic or .mcool). Or the error will occer at the early step. Make sure that no underscores('\_') is included in the chromosome name.

### Directory tree

For bulk HiC data, a recommended work directory looks like:

```shell
mkdir work_dir
cd work_dir
mkdir hic_input
# Then move corresponding hic file here.
mkdir TL_output
ln -s deDoc_dir_path .
ln -s juice_dir_path .
```
The directory tree is:

```shell
├── deDoc
│   ├── deDoc.jar
├── hic_input
│   ├── Control_GSE63525_IMR90_combined_30.hic
│   ├── Test_GSE63525_K562_combined_30.hic
│   ├── Control_GSE63525_IMR90_combined_30.mcool
│   └── Test_GSE63525_K562_combined_30.mcool
├── juice
│   ├── juicer_tools_2.09.00.jar
└── TL_output
```

For scHiC data, a recommand work directory looks like:
```shell
├── deDoc
│   ├── deDoc.jar
├── hic_input
│   ├── Control_cells_dir
│   │   ├── cell_1
... │   │   ├── raw
    │   │   │   ├── 100000
    │   │   │   │   └── *.matrix
    │   │   │   └── 500000
    │   │   │       └── *.matrix
    │   │   └── iced
...    ...      ├── ...

│   ├── Test_cells_dir
...    ...
└── TL_output
```
**Note: For scHiC, the subdiretory of hic_input MUST be cells_dir/normalization/resolution/*.matrix!!**
Here, normalization could be "raw"/"iced" or any other string you used for path(which is similar to the output format of HiC-Pro), default is "raw". And, resolution is adapted for genome size, e.g. hg19 the resolution should be 100000 and 500000.

### Example

#### Samples

You can download test case from GSE63525. The test sample [hicfile](https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE63525&format=file&file=GSE63525%5FK562%5Fcombined%5F30%2Ehic)  is K562 and control sample [hicfile](https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE63525&format=file&file=GSE63525%5FIMR90%5Fcombined%5F30%2Ehic) is IMR90.

And you can choose the test sample and control sample by yourself.

#### Resolution

For samples of human, the hicfile should contain 100k and 500k resolution matrix data. In general, the appropriate resolution could be calculated as following:
```math
res_{unit} = 10^{len(max(chromosome_{size}))-4}.
```
For example, in the hg.sizes the largest size of chromosome is **chr1(249250621)**, the suggested resolution unit would be 100k, and the lower one is defined as: 
```math
5 \times res_{unit}.
```

#### Command

For bulk HiC data,
```shell
# Assume you are in the work_dir,a standard command is for hic format file
HiST -t hic_input/Test_GSE63525_K562_combined_30.hic \
-c hic_input/Control_GSE63525_IMR90_combined_30.hic \
-o TL_output/ \
-d deDoc/deDoc.jar \
-j juice/juicer_tools_2.09.00.jar \
-s sizes/chrom_hg19.sizes 
# or mcool format file
HiST -t hic_input/Test_GSE63525_K562_combined_30.mcool \
-c hic_input/Control_GSE63525_IMR90_combined_30.mcool \
-o TL_output/ \
-d deDoc/deDoc.jar \
-s sizes/chrom_hg19.sizes
# or mixed format file
HiST -t hic_input/Test_GSE63525_K562_combined_30.mcool 
-c hic_input/Control_GSE63525_IMR90_combined_30.hic \
-o TL_output/ \
-d deDoc/deDoc.jar \
-j juice/juicer_tools_2.09.00.jar \
-s sizes/chrom_hg19.sizes
# Then you can find the result in folder TL_output/SV_result.
```
For scHiC data,
```shell
HiST -t hic_input/Test_cells_dir/ \
-c hic_input/Control_cells_dir  \
-s sizes/chrom_hg19.sizes \
-d deDoc/deDoc.jar \
-o TL_output/
```

#### Figure
An example of TL result with ![heatmap](./example_pic/0_Combine_chr1_chr7.png)

### FAQ
##### If you meet "Resource temporarily unavailable" or "error: too many open files" or "ValueError: cannot convert float NaN to integer" or "EmptyDataError: No columns to parse from file"?

If your workstation is configured with more than 128 GB of memory and the number of threads is more than 48, you can try the following operations: 

1. You can try command ```ulimit -u 381152``` . Here 381152 could be replaced by any big number.
2. You only need to run HiST command a few more times. 

These errors usually occur when the input data is mcool. We will fix them in the next version.

##### If you meet "No such file or directory"...

1. Check matrix_from_hic directory, if no files in sub-directory, check the juicer_log to find error or check cooler package.
2. If juicer_log suggest like "invalid chromosome chr12", you should check the sizes file, a common problem is to tell "chr1" or "1".
