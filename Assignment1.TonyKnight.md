## **Finding Lane Lines on the Road** 

## Writeup by Tony Knight - 2017/03/21

---
## Pipeline Description

A pipeline was developed in python using openCV image processing routines to define the appropriate lane boundaries in images based on the line markings on those images.  The pipeline consisted of the following steps.

<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images/vWpwD.jpg" alt="original image of road" width="200">
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/pipeline/mask.jpg" alt="Window image" width="200"> 
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/pipeline/greyscale.jpg" alt="grayscale image" width="200">
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/pipeline/gaussian.jpg" alt="grayscale image" width="200">
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/pipeline/canny.jpg" alt="Canny edge image" width="200">
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/pipeline/masked_canny.jpg" alt="Masked Canny edge image" width="200">
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/pipeline/houghlines.jpg" alt="Aggre Lines image" width="200">
<img src="https://raw.githubusercontent.com/teeekay/CarND-LaneLines-P1/master/test_images_output/vWpwD.jpg" alt="alt text" width="200">


1. First we defined a window on the area to examine. It generally consisted of an isosceles trapezoid (shown in yellow) surrounding the area of the image where we expect the lane lines to be in.

2. The image was then converted to **Grayscale** and reduced to a single dimension (from BGR).

3. A **Gaussian Blur** was applied.  I set a default kernel size of 5, which seemed to work ok.

4. A **Canny edge** detector was applied.  I used default values of 50 and 150 for the low and high thresholds on the 8 bit image (0..255), but also added the ability to calculate and use automatically generated thresholds based on the image median value - I had seen this used [here](http://www.pyimagesearch.com/2015/04/06/zero-parameter-automatic-canny-edge-detection-with-python-and-opencv/).

5. The window from step 1 was used to **Mask**  the Canny edge output to eliminate extraneous features while retaining the lane markings.

6. A **Hough Transform** was run to identify line segments within the masked Canny image.  The transform returned the line segments as pairs of line endpoints.  These line segments were evaluated within a draw_lines() function.  I added a set of tests, eliminating lines with near horizontal slopes, and lines that did not intersect with the base of the region of interest or the top of the region of interest.  The line segments were classified as belonging to the left or right lane marker based on whether their slope was positive or negative.  Weighted averages were calculated for the orientation and intersection points of the left and right lane markers based on the length of each detected line.  These weighted averages were used to plot the lane markers, and I found this simple approach generally worked well. 

7. The line image was then overlaid on the original image and returned from the pipeline


### Potential Shortcomings With the Current Pipeline

Generally I think this code was successful at finding lines in the selected images and videos, although it failed in one region of the challenge video where the Canny edge detector did not detect edges between the yellow line on the bright brown concrete bridge deck.  This area of the pipeline will probably fail under many lighting / and line color / road color combinations.  

The pipeline is also stateless, and no knowledge from earlier images is used to help detect lines in the next image in the video.  This knowledge could also be used to adapt the region of interest mask based on where the lines have been found.

The pipeline would also probably fail in situations like with with grooved concrete surfaces which would have many edges that would be nearly parallel to the expected line orientation.

It does not have a robust method to assess where lines should fall within the field of view, or to compare the left and right lines to see if the combination of the two lines makes sense.

It would probably have lots of trouble with zigzag line markings!

<img src="https://secure.surveymonkey.com/_resources/9362/13989362/c4041e9f-1a62-4503-8041-fb9888b9cc4e.jpg" alt="ZigZag Lines image" width="200">


### Possible Improvements to the Pipeline

Here are some strategies that could be tested to see if they improve the pipeline: 

1) extracting or applying color channels/masks to specifically find white and yellow features at the very start of the pipeline, before running the greyscale and Canny edge detector.

2) Using a template detector to try to match the hough line sets with quadrilateral shapes with perspective distortion (edges converging in distance) that match expected line patterns.

3) Dividing the window into 3 or more horizontal sections, to be able to more easily adapt to curved lines/lanes, and potentially map lanes on either side of the lane the car is in, in upper (more distant) sections.

### Output when using Pipeline on Videos

1) [link to solidWhiteRight video](https://github.com/teeekay/CarND-LaneLines-P1/raw/master/test_videos_output/solidWhiteRight.mp4)

2) [link to solidYellowLeft video](https://github.com/teeekay/CarND-LaneLines-P1/raw/master/test_videos_output/solidYellowLeft.mp4)

3) [link to the challenge video output](https://github.com/teeekay/CarND-LaneLines-P1/raw/master/test_videos_output/challenge.mp4)


