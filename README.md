# DoG---Script_for_automated_feature_quantification_from_image_data
#
#
# An automated FIJI-script, based on the "Difference of Gaussians" (DoG) method, was developed for an unbiased 
# identification and quantification of SG-related features within these images. 
# Briefly, Gaussian blur works by replacing all pixel values by the weighted average of its neighbouring pixels. 
# The original pixel's value receives the heaviest weight and neighbouring pixels receive progressively smaller weights 
# as their distance from the original pixel increases. Consequently, pixels in areas between low and high pixel intensities
# will receive a greater degree of blurring. Applying this filter to two identical images using two different sigma values
# (standard deviation of the gaussian distribution), result in two images with different degree of blurring. Subtracting these 
# two images results in an image predominantly composed of values corresponding to regions with steep intensity-change gradients, 
# effectively removing everything else. This enables the efficient identification of high-signal cellular features, such as stress 
# granules, through a straightforward thresholding process.
#
#
################################################################
// # Script works by first subtracting background locally (Rolling ball + sliding paraboloid approach).
// # Hereafter DoG (difference of gaussians) is performed to find regions of interest.
// # These regions of interest (ROI's) are then measured (e.g. Area, Mean, ect.) on the original image and exported to a .csv file for each image. 

// # To use the script you manually need to do following:
// # Open FIJI -> press cmd+shift+n (to get script executer).
// # Provide the path to the directory holding your .czi files (NOTE!!! NO spaces in dir_path or file_names (e.g. "/Users/antonhomilius/Desktop/U2OS images" must be "/Users/antonhomilius/Desktop/U2OS_images")).
// # Adjust threshold, particle size and particle circularity (e.g. turn off "run("Close All");"  while finding the right settings (so you can sanity check output)). 

// ############################################################
// # Define directory:
dir_path = "/Full_path_to_dir_containing_images";
dir_path = dir_path+"/"

// # List all files in the directory:
list = getFileList(dir_path);

// # Process each .czi file:
for (i=0; i<list.length; i++) {
    if (endsWith(list[i], ".czi")) {
        // Get prefix from file name:
        prefix = replace(list[i], ".czi", "");
        suffix = ".czi";
        file_name = prefix + suffix;
        full_path = dir_path + file_name;

        // # Open file:
        open(full_path);
        run("Grays");
        run("Enhance Contrast...", "saturated=0.20");
        run("Duplicate...", "title=Original");
        selectWindow("Original");
        run("Duplicate...", "title=clone");
        

        // # Duplicate and adjust:
        selectWindow("clone");
        run("Subtract Background...", "rolling=10 sliding");
        run("Duplicate...", "title=1");
        selectWindow("clone");
        run("Duplicate...", "title=2");

        // # Perform DoG (difference of gaussians):
        selectWindow(1); 
        run("Gaussian Blur...", "sigma=1");
        selectWindow(2); 
        run("Gaussian Blur...", "sigma=2");
        imageCalculator("Subtract create", "1", "2");

        // # Define and measure particles: 
        selectWindow("Result of 1");
        run("Gaussian Blur...", "sigma=1");
        run("Enhance Contrast...", "saturated=0.20");
        setAutoThreshold("Otsu dark");
        //setThreshold(25, 100000, "raw");
        run("Set Measurements...", "area mean perimeter fit shape feret's integrated median display" + " redirect=" + "Original" + " decimal=2");
        run("Analyze Particles...", "size=0.3-20 circularity=0.3-1.0 show=Outlines display clear add");

        // # Save data:
        saveAs("Results", dir_path + prefix + "_RESULTS.csv");
        // # Close all windows:
        run("Close All");
    }
}

################################################################


