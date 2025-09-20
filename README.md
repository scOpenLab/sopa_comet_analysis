# COMET IMAGE ANALYSIS WITH SOPA

## Setup and Installation
Assumes a GPU is available for the segmentation, and that the cpsam model fro cellprofiler4 is present at: `~/.cellpose/models/cpsam `
The cpsam model can be downloaded from: https://huggingface.co/mouseland/cellpose-sam/resolve/main/cpsam?download=true)

The following packages are used, see [requirements.txt](https://github.com/scOpenLab/sopa_comet_analysis/blob/main/requirements.txt):
```
sopa
spatialdata_plot
spatialdata
matplotlib
scanpy
numpy
```
Tested on Python 3.11.2

## Workflow

### Preprocesing
1) Load the COMET image.
2) Set the chanenl names.
3) Segment the tissue on a lower resolution pyramid level, to identify the areas to perform cell segmentation on.
4) Divide the tissue in patches, to perform the segmentation on.
5) Plots: DAPI Image, tissue to be segmented, and segmentation patches to check the areas that will be segmented


### Segmentation
This step needs to be performed on a GPU node.
Assumes that the cpsam model fro cellprofiler4 is present at: `~/.cellpose/models/cpsam `
The cpsam model can be downloaded from: https://huggingface.co/mouseland/cellpose-sam/resolve/main/cpsam?download=true)
1) Cellpose segmentation with the cpsam model
2) Make ann AnnData object with the mean pixel value of each marker in each cell

### Analysis
If the preprocessing and segmentation have been performed with the [nf-core version of sopa](https://nf-co.re/sopa/dev/),
the SpatialData out folder in zarr format can be opened and the analysis started from here.
1) Produces an expression table withou DAPI for the UMAP plotting
2) Generate a NN neighbour grap and the UMAP embeddings
3) Assign the cells to specific celltypes using manual thresholds
5) Plots:
    - UMAPs 
    - Violinplot
    - Dotplot
    - Heatmap   


### Output
Extract the bounding boxes of the 3 largest pieces of tissue:
1) Calculate the scaling factor between the pyramid level used for tissue segmentation and the full resolution
2) Get the bounding boxes from the regions_of_interest ROIs polygons we obtained from the tissue segmentation
3) Scale the bounding boxes to match the full resolution image
4) Calculate the area to find the 3 kargest bounding boxes
5) Use the bounding boxes to crop the spatialdata object
6) Write out zarrs for visualization with napari

## Output and visualization
The output consists of separate spatialdat objects, one for each sample.
The spatialdata objects contain:
- Image
- Cell plygons
- Annotated expression table

These objects can be visualized with [napari](https://napari.org/stable/) using the [napari-spatialdata plugin](https://spatialdata.scverse.org/projects/napari/en/latest/index.html)




