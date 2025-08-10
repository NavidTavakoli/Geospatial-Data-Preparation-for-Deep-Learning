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

### Description
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

## Copy Tif Data To New Tif File.py - Copy Geotransform and Projection Metadata

### Description
This script transfers geospatial metadata — geotransform and projection — from one GeoTIFF file (source) to another (target). This ensures that the target raster has correct spatial referencing matching the source.
- It opens the source TIFF and reads its geotransform matrix (which defines pixel size, rotation, and origin) and its projection (CRS).
- Then, it opens the target TIFF in update mode and sets these geospatial properties to match the source.
- This is useful when a raster dataset lacks georeferencing information or when you want to ensure consistent spatial referencing between datasets.

### Requirements
- Python 3.x
- GDAL Python bindings (`pip install GDAL`)

### Usage

1. Set the file paths for `source_tif` (the reference GeoTIFF with correct geospatial info)  
   and `target_tif` (the GeoTIFF to update).

2. Run the script:

```bash
python Copy\ Tif\ Data\ To\ New\ Tif\ File.py
```
**Notes**
- The target TIFF must be writable (opened in update mode).
- Use this script when the target raster lacks or has incorrect georeferencing.

---
## Merge rasters.py - Merge Multiple GeoTIFFs into a Single Mosaic

### Description
This script merges multiple raster GeoTIFF files in a folder into one single mosaic raster file. It preserves geospatial metadata and compresses the output with LZW compression.
- It first reads all .tif files in the input directory, loading their data and geospatial transforms into memory.
- The script optionally sets up a parallel processing pool (though the example parallel task is a placeholder here).
- It then merges the loaded rasters into one large raster mosaic using Rasterio's merge function.
- Finally, it writes the combined raster to an output file, preserving metadata such as coordinate reference system and compression.

This approach improves efficiency by preloading data before merging and could be extended to parallelize additional processing.

### Requirements
- Python 3.x
- rasterio (`pip install rasterio`)
- numpy (`pip install numpy`)

### Usage

1. Place the input `.tif` raster files into a directory, for example `Test/`.  
2. Modify the script's `merge_rasters_efficiently()` function call with your input directory and desired output file path.  
3. Run the script:

```bash
python Merge\ rasters.py
```
**Notes**
- Currently, multiprocessing is set up but used as a placeholder and can be extended for more complex tasks.
- The output raster uses LZW compression for reduced file size.
- The script assumes all input rasters share the same projection.

---

## Raster to vector.py - Raster to Vector Conversion

### Description
This script converts a color raster (e.g., RGB GeoTIFF) into vector polygons for each unique color. The output is a GeoJSON file with polygons labeled by their RGB color values.
- It reads the raster image data and extracts its geotransform.
- The script identifies all unique colors in the raster by reshaping and scanning the image bands.
- For each unique color, it creates a mask identifying pixels with that color, then vectorizes these masked areas into polygons using Rasterio's shapes function.
- All polygons for each color are stored with their RGB values in a GeoDataFrame.
- Finally, the script exports this GeoDataFrame as a GeoJSON file, preserving spatial reference.

This approach is useful for extracting categorical areas or features from raster images based on color coding.

### Requirements
- Python 3.x
- rasterio (`pip install rasterio`)
- geopandas (`pip install geopandas`)
- shapely (`pip install shapely`)
- numpy (`pip install numpy`)

### Usage
1. Place your input raster file (e.g., `population raster level 1.tif`) in the specified directory.  
2. Run the script.  
3. The output GeoJSON file (`output_vector.geojson`) will contain polygons grouped by raster colors.

**Notes**
- Works best with categorical raster data where distinct colors represent meaningful classes.  
- Output polygons are stored with RGB attributes for further GIS analysis.

---

## Set_CRS_and_Pixel_size.py - Raster Reprojection and Resampling Script

### Description
This script reprojects and resamples an input raster file to a specified coordinate reference system (CRS) and pixel size using GDAL.
- It opens the input raster file in read-only mode.
- Defines the target CRS as EPSG:3003 and sets the desired pixel size to 0.4 units for both X and Y.
- Uses GDAL’s Warp function to perform reprojection and resampling with nearest neighbor interpolation (useful for categorical data like masks).
- Saves the processed raster to a new TIFF file.
- Finally, it closes all open datasets and prints a confirmation message.

This workflow is useful when you need to prepare raster data for consistent spatial reference and resolution for GIS analyses or further processing.

### Requirements
- Python 3.x
- GDAL Python bindings (`pip install gdal` or appropriate installation)

### Usage
1. Set the input raster path (`input_tif_path`).  
2. Define the output path (`output_tif_path`).  
3. Adjust the target CRS (`target_crs`) and pixel size (`pixel_size_x`, `pixel_size_y`) if needed.  
4. Run the script to generate a reprojected and resampled output raster.

## Example
```bash
python reproject_resample.py
```
**Notes**
- The default resampling method is nearest neighbor, ideal for categorical rasters like masks.  
- You can change the resampling method (e.g., bilinear, cubic) by modifying `resampleAlg` in `WarpOptions`.

---

## Smooth polygons(mask).py - Shapefile Geometry Smoothing with Taubin Method

### Description
This script smooths polygon boundaries of a shapefile using the Taubin smoothing algorithm while visualizing the original polygons over a raster image.
- It loads vector geometries from a shapefile using GeoPandas.
- It reads the raster data and extent from a GeoTIFF using Rasterio.
- It plots the raster image in grayscale and overlays the original shapefile geometries outlined in red.
- It applies Taubin smoothing (a method to smooth polygons without shrinking them) to each geometry.
- It creates a new GeoDataFrame with the smoothed geometries and saves it as a new shapefile.

The plot display is set up but commented out; you can enable it for interactive visualization.

This workflow is useful in GIS preprocessing to clean and smooth polygon boundaries, for example in land-use masks or segmented regions.
### Requirements
- Python 3.x  
- geopandas (`pip install geopandas`)  
- rasterio (`pip install rasterio`)  
- matplotlib (`pip install matplotlib`)  
- shapelysmooth (`pip install shapelysmooth`)  

### Usage
1. Modify the shapefile path in the script (`mask new.shp`).  
2. Modify the raster file path (`sample.tif`).  
3. Run the script to generate a smoothed shapefile saved as `smoothed_mask.shp`.  
4. To visualize, uncomment the `plt.show()` line.

### Parameters
- Taubin smoothing uses parameters `factor=0.6`, `mu=-0.3`, and `steps=10`, which control smoothing strength and iterations. Adjust as needed.

### Example
```bash
python smooth_shapefile.py
```
**Output**
- A new shapefile with smoothed geometries, suitable for cleaner spatial analysis or map visualization.

---

## Split_Raster_RGB.py - Raster Tiling Script - Split Large GeoTIFF into Smaller Tiles

### Description
This Python script splits a large GeoTIFF raster into smaller tiles of fixed size, optionally with overlapping edges. Each tile is saved as a separate GeoTIFF file with preserved geospatial metadata.
- It calculates windows (sub-areas) across the raster based on the desired tile size and overlap percentage.
- Using Rasterio, it extracts these tiles from the input raster and saves each as an individual GeoTIFF file.
- The script creates the output directory if it doesn’t exist.
- It uses tqdm to show a progress bar during the tiling process.

This process is useful for preparing large geospatial images for deep learning or GIS analysis workflows that require smaller, uniform input tiles.

### Requirements
- Python 3.x
- rasterio (`pip install rasterio`)
- tqdm (`pip install tqdm`)

### Usage

1. Set the input raster file path and desired output directory.  
2. Specify tile size as (width, height) in pixels and overlap percentage (0-100).  
3. Run the script to generate tiles in the output folder.

###Example:

```python
tile_size = (256, 256)  # 256x256 pixel tiles
overlap_percentage = 0  # No overlap
split_raster("Test/Full Mask Turin.tif", "train/masks", tile_size, overlap_percentage)
```
**Notes**
- Overlap controls how much adjacent tiles overlap as a fraction of tile size.
- Output tiles retain original CRS and geotransform adjusted for each tile.
- The progress bar gives feedback on processing status.

---

## Split_gdal_RGB.py - Raster Splitter with RGB Band Interpretation

### Description
This script sets the color interpretation of the first three bands of a raster file to Red, Green, and Blue. Then, it splits the raster into smaller tiled images of a given size and overlap. This is useful for preparing large raster datasets for analysis or machine learning workflows.
This script processes a raster image in two main steps:

**1. Set Band Color Interpretation:**

   It opens a raster file and assigns the first three bands to Red, Green, and Blue color channels respectively. This ensures that the raster will be interpreted and displayed as a true RGB image.

**2. Split Raster into Tiles:**

   The script splits the input raster into smaller tiles (sub-images) of specified size (e.g., 256x256 pixels). You can also specify an overlap percentage between tiles if needed. This is useful for processing large images in manageable pieces, often needed in machine learning and GIS workflows.

It also shows a progress bar using `tqdm` to track how many tiles have been processed.

### Requirements
- Python 3.x
- GDAL (`pip install GDAL`)
- tqdm (`pip install tqdm`)

### Usage

1. Specify your raster file path, output directory for tiles, tile size, and overlap percentage in the script or via parameters.  
2. Run the script to process the raster:

```bash
python raster_splitter.py
```
The tiles will be saved in the output directory as `tile_0001.tif`, `tile_0002.tif`, etc.

### Parameters
- `tile_size` (tuple): Size of each tile in pixels, e.g., (256, 256).
- `overlap_percentage` (float): Percent overlap between tiles, e.g., 0 for no overlap, 20 for 20% overlap.

**Notes**
- The script assumes the input raster has at least three bands to assign RGB colors.
- Overlap can help with edge artifacts in image analysis.

---

## Contributing

Contributions are welcome! If you find bugs or have feature requests, please open an issue or submit a pull request.

## Support

If you have any questions or need help using this script, feel free to contact me or open an issue on the repository.

## Acknowledgments

This project uses GDAL and rasterio libraries. Many thanks to their maintainers and contributors for their great work.

---

Thank you for using this tool! ] hope it helps streamline your raster processing workflows.

**Author:** Navid




