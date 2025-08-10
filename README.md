# Geospatial Data Preparation for Deep Learning
This is a collection of Python scripts for preprocessing and postprocessing geospatial raster data, specifically designed to prepare satellite and aerial imagery for deep learning models. These tools cover essential tasks such as clipping, merging, splitting, reprojecting, compressing, and converting raster images, as well as vectorizing segmentation outputs.

This toolkit bridges Remote Sensing and Computer Vision workflows by providing robust, easy-to-use utilities that help transform large, complex geospatial datasets into standardized inputs for machine learning pipelines.

---

## Clip.py - Center Cropping GeoTIFF Images

### Description
`Clip.py` is a Python script designed to crop GeoTIFF raster images from a folder by extracting a centered subset with specified width and height. The cropped images are saved in a separate output folder.

This tool helps preprocess large geospatial images by creating uniform, smaller tiles for easier handling and downstream applications like computer vision or remote sensing tasks.

### Requirements
- Python 3.x
- rasterio library (`pip install rasterio`)

### Usage

1. Place your `.tif` images in the input folder (default: `Test`).
2. Set your desired output folder path and crop size (`new_width`, `new_height`) in the script.
3. Run the script:

```bash
python Clip.py
```
### Example
```bash
folder_path = "Test"
output_folder = "Test/Output"
new_width = 20224
new_height = 20224

clip_images(folder_path, output_folder, new_width, new_height)
```
  The script will process each .tif image in the input folder, cropping the center area and saving the result in the output folder.

**Notes**
- The crop window is centered on the original image.
- Output images maintain the same coordinate reference system (CRS) and metadata.
---
## Clip_Two_raster.py - Clip an Image Using a Mask Raster's Extent

## Description
This script clips a GeoTIFF image based on the spatial extent of a mask raster file using GDAL.  
The output image is cropped to the mask's bounding box with a specified pixel size and coordinate reference system, maintaining high image quality through cubic resampling.

### Requirements
- Python 3.x
- GDAL library (`pip install GDAL`)

### Usage

1. Set the paths for:
   - Input image (`image_path`)
   - Mask raster (`mask_path`)
   - Output clipped image (`clipped_output_path`)

2. Adjust pixel resolution (`xRes`, `yRes`) and CRS (`dstSRS`) if needed.

3. Run the script:

```bash
python Clip_Two_raster.py
```
**Notes**
- The mask raster defines the geographic clipping area.
- The output image preserves georeferencing and is cropped precisely to the mask’s coverage.
- Resampling is done with cubic interpolation to ensure smooth results.

---
## Compressing Tif file.py - TIFF Compression and Resizing Script

### Description
This script compresses and resizes TIFF raster files to reduce file size and improve processing efficiency. It leverages the GDAL command-line tool `gdal_translate` with options for compression and scaling.
This Python script reduces the size of a TIFF raster file by applying compression and resizing using the gdal_translate command-line tool via subprocess.

- It supports different compression methods such as `LZW` (default), `JPEG`, `DEFLATE`, or `none`.
- The image dimensions can be scaled down by a factor `(resize_factor)`, e.g., 0.5 reduces width and height by half.
- The output is a smaller TIFF file saved with the specified compression, useful for saving storage space and speeding up subsequent processing.

### Requirements
- Python 3.x
- GDAL installed on your system and accessible in your PATH

### Usage

1. Set the input TIFF file path (`input_file`),  
   output path (`output_file`),  
   compression method (`compression`), and  
   resize factor (`resize_factor`) in the script.

2. Run the script:

```bash
python Compressing\ Tif\ file.py
```
### Parameters
- compression: Compression type; options include 'LZW' (lossless), 'JPEG' (lossy), 'DEFLATE', or 'NONE'.

- resize_factor: Fractional scale for image dimensions (e.g., 0.5 halves width and height).

### Example
```bash
input_file = 'Test/1.tif'
output_file = 'Test/1_compressed.tif'
compression_method = 'LZW'
resize_factor = 0.5
reduce_tiff_size(input_file, output_file, compression_method, resize_factor)
```
**Notes**
- Requires GDAL installed with command-line tools.
- Compression choice affects image quality and file size.
- Resizing reduces both spatial resolution and file size.

---

