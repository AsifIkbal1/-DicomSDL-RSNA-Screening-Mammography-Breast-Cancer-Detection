# -DicomSDL-RSNA-Screening-Mammography-Breast-Cancer-Detection
 DicomSDL, RSNA Screening Mammography Breast Cancer Detection
DicomSDL, RSNA Screening Mammography Breast Cancer Detection

# Why this notebook?

After reading this post about [Pydicom vs DicomSDL (Fast dicom export and processing (1.6-2x faster) 💪)](https://www.kaggle.com/competitions/rsna-breast-cancer-detection/discussion/371033), I checked whether DicomSDL had the option to apply a VOI lookup table or windowing operation like Pydicom.<br>
It turns out, they don't. They only have this function in their source code 😅

    def apply_window_center():
        print("Hello")
        

## Apply_voi_lut

Next, I dug into the source code of Pydicom to see what they do exactly inside the apply_voi_lut method.<br>
The code checks several things, of which many don't apply for the images/datasets in the dicom images in this competition.<br>
For example, **none** of the datasets in this competition have a valid **VOILUTSequence**.<br>
This means the **apply_voi_lut function** will always redirect to the **apply_windowing**.<br>
Therefore, the next step was to check this function inside Pydicom's code.

## Apply_windowing

Here, the most important thing the code checks is which VOILUTFunction is used in the dataset.<br>
In this competition, 22% of the datasets (12139 of 54706) have the Sigmoid function, the rest uses the LINEAR function.<br>
As the code is very different for each of these functions, I recreated/copied the necessary code for both functions.<br>
Please note that I removed some code for speed, as many checks in the pydicom code is not not applicable in this competition.
For example:
- Pixelrepresentation and ModalityLUTSequence is always NaN
- Window width/center is always set correctly
- RescaleSlope & RescaleIntercept is always the same


## Results/conclusion

As you can see in the results below, using the VOILUTFunction is quite important.<br>
Around 22% of the images uses SIGMOID instead of LINEAR, and the images/colors for Sigmoid are very different, a lot lighter/more yellow.<br>
But once you apply the windowing function, they seem completely similar to images with the Linear VOILUTFunction.<br>
**Therefore**, if you use DicomSDL instead of Pydicom, you should definitely create your own apply_windowing function like in Pydicom!
