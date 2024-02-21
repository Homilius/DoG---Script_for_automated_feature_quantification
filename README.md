# Automated DoG - Script for Automated Feature Quantification from Image Data

## Introduction

"Automated DoG" is an automated FIJI script leveraging the "Difference of Gaussians" (DoG) method for unbiased identification and quantification of SG-related features within images. Gaussian blur replaces pixel values with the weighted average of neighboring pixels. This process, applied with two sigma values, highlights regions of steep intensity-change gradients, aiding in the efficient identification of high-signal cellular features like stress granules through thresholding.

## How It Works

1. **Background Subtraction:** Utilizes the Rolling ball and sliding paraboloid approach to subtract background locally.
2. **DoG Application:** Performs the difference of gaussians to identify regions of interest (ROIs).
3. **ROI Measurement:** Measures ROIs (e.g., Area, Mean, etc.) on the original image and exports the data to a .csv file for each image.

## Usage Instructions

To use the script, follow these steps:

1. **Prepare Your Environment:** Open FIJI and press `cmd+shift+n` to access the script executor.
2. **Set Directory Path:** Provide the path to your directory containing .czi files. Ensure there are no spaces in `dir_path` or file names (e.g., use "/Users/yourname/Desktop/U2OS_images").
3. **Adjust Settings:** Modify threshold, particle size, and circularity settings as needed. Disable `run("Close All");` during adjustment to sanity check the output.

## Script Snippet

```javascript
// Define directory:
dir_path = "/Full_path_to_dir_containing_images";
dir_path += "/";

// List all files in the directory:
list = getFileList(dir_path);

// Process each .czi file:
for (i = 0; i < list.length; i++) {
    if (endsWith(list[i], ".czi")) {
        // File preparation:
        prefix = replace(list[i], ".czi", "");
        suffix = ".czi";
        file_name = prefix + suffix;
        full_path = dir_path + file_name;

        // Open and adjust file:
        open(full_path);
        // [Further steps for adjusting and processing the file]

        // Perform DoG and define, measure particles:
        // [Code to apply DoG, measure particles, and save data]

        // Save data and close all windows:
        saveAs("Results", dir_path + prefix + "_RESULTS.csv");
        run("Close All");
    }
}
