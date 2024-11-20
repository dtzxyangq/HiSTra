# HiSTra


### Installation

Linux OS

```shell
pip install HiSTra
```
Dependency
```shell
conda create -n HiSTra python=3.8 
conda install numpy scipy pandas=1.3.5 matplotlib seaborn h5py
conda install -c conda-forge -c bioconda cooler=0.8.11
pip install matplotlib-venn 
```

### Preparation

Download [juicer_tool](https://github.com/aidenlab/juicer/wiki/Juicer-Tools-Quick-Start) and [deDoc](https://github.com/yinxc/structural-information-minimisation). Because of the update of the two softwares, ***we recommend that you download them from this repo.*** You can find relevant jar files in the HiSTra/juice and HiSTra/deDoc, respectively.

Make sure chromosome.sizes file is exactly the file used in generating test(and control) sample(.hic or .mcool). Or the error will occer at the early step.

### Directory tree

A recommended work directory looks like:

```shell
mkdir work_dir
cd work_dir
mkdir hic_input
# Then move corresponding hic file here.
mkdir TL_output
ln -s deDoc_dir_path .
ln -s juice_dir_path .
```

Finally, the directory tree is:

```
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

### Example

You can download test case from GSE63525. The test sample [hicfile](https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE63525&format=file&file=GSE63525%5FK562%5Fcombined%5F30%2Ehic)  is K562 and control sample [hicfile](https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE63525&format=file&file=GSE63525%5FIMR90%5Fcombined%5F30%2Ehic) is IMR90.

And you can choose the test sample and control sample by yourself.

A trick here is the hicfile should contain 100k and 500k resolution matrix data.

```shell
# Assume you are in the work_dir,a standard command is for hic format file
HiST -t hic_input/Test_GSE63525_K562_combined_30.hic -c hic_input/Control_GSE63525_IMR90_combined_30.hic -o TL_output/ -d deDoc/deDoc.jar -j juice/juicer_tools_2.09.00.jar
# or mcool format file
HiST -t hic_input/Test_GSE63525_K562_combined_30.mcool -c hic_input/Control_GSE63525_IMR90_combined_30.mcool -o TL_output/ -d deDoc/deDoc.jar
# or mixed format file
HiST -t hic_input/Test_GSE63525_K562_combined_30.mcool -c hic_input/Control_GSE63525_IMR90_combined_30.hic -o TL_output/ -d deDoc/deDoc.jar -j juice/juicer_tools_2.09.00.jar

```

Then you can find the result in folder TL_output/SV_result.
An example of TL result with ![heatmap](./example_pic/0_Combine_chr1_chr7.png)

### FAQ
#### If you meet "Resource temporarily unavailable" or "error: too many open files" or "ValueError: cannot convert float NaN to integer" or "EmptyDataError: No columns to parse from file"?

If your workstation is configured with more than 128 GB of memory and the number of threads is more than 48, you can try the following operations: 

1. You can try command ```ulimit -u 381152``` . Here 381152 could be replaced by any big number.
2. You only need to run HiST command a few more times. 

These errors usually occur when the input data is mcool. We will fix them in the next version.





