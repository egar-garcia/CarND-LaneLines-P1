# **Finding Lane Lines on the Road** 

## Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[Example of found lanes]: ./test_images_output/solidWhiteCurve.jpg "Example of found lanes"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.


My pipeline consisted of the following steps:

1. Image is converted to grayscale.

2. A gaussian blur is applied to the grayscale image.

3. A Canny transform is applied to identify the points of high contrast.

4. A region of interest is filtered from the resulting image applying a mask. In this case a vanishing point was identified to create the region following the perspective from the point of view of the driver, it uses a horizontal top marging close to the vanishing point. When facing the optional challenge also it was decided to eliminate part of the bottom that was showing the hood, this is because it was presinting noice due to the reflection of objects.

5. The Hough lines are calculated from the filtered Canny transformed immage.

6. Some Hough lines are selected if they are likely to belong a lane, for this purpose two criteria are taken: first the absolute value of the slope must be in a certan range, second it's verified if the lane is directed to the vanishing point (with certain tolerance). The lines are classified to be in the left or right lane, depending on if their slope is negative or possitive.

7. The lines classified for each lane are extrapolated to one big line that will match with the lane on the road, to do the extrapolation some sample points are calculated from the classified lines, this has the purpose that the larger lines are accounted the most when doing the extrapolation, the extrapolation is doing using 'polyfit' which calculates the most suitable line given a set of points. The bottom of the image is taken as the begining of the lane, for the end of the line is not taken a specific position, instead the detected Hough lines determine it, that seems to produce better results on curves and slopes rather than forcing the line to have a specific end.

8. Finaly the image containing the extrapolated lane lines is combined with the initial image in order to overlay the lanes.
 

In order to draw a single line on the left and right lanes, in fact, I refactored the draw_lines() function to just draw an array of lines, I also broke down othe one for the Hough transformation and instead create a new one called 'drawLaneLines' which calls other small functions for specific tasks like clissifying the Hough lines in left and right lines or extrapolating a line. It's also worth to mention that the paremeters for the diverse funtcions are set up using ratios (or percentages) of the image size, this was specially useful when addressing the optional challenge (because the image size is different). 

Here are some examples of how the pipeline works in the different steps: 

Step                       | Image
-------------------------- | --------------------------
0. Initial Image           | ![Initial image][./pipeline_example/00-initial.jpg]
1. Grayscale               | ![Grayscale][./pipeline_example/01-grayscale.jpg]
2. Gaussian Blur           | ![Gaussian Blur][./pipeline_example/02-gaussian_blur.jpg]
3. Canny Transform         | ![Canny Transform][./pipeline_example/03-canny.jpg]
4. Region of Interest      | ![Region of Interest][./pipeline_example/04-region_of_interest.jpg]
5. Hough Lines             | ![Hough Lines][./pipeline_example/05-hough_lines.jpg]
6a. Left Lines             | ![Left Lines][./pipeline_example/06-a-left_lines.jpg]
6b. Right Lines            | ![Right Lines][./pipeline_example/06-b-right_lines.jpg]
7. Extrapolated Line Lanes | ![Extrapolated Lanes][./pipeline_example/07-extrapolated_lane_lines.jpg]
8. Output Image            | ![Output Image][./pipeline_example/08-weighted.jpg]



### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming I detected when addressing the optional challenge, is that when the image present some areas whith shadows, glare or pavement of different tones, Canny and Hough identify lines that doesn't belong to a lane or actual lane lines are not detected because they are lost in the shadow.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to implement a mechanism that highlighs the region of the image where the lanes are potentially located, for example highlight the areas on white or yellow, in order to reduce the effect of shadows.

