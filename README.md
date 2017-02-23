##Finding Lane Lines on the Road

###The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---
[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg "Solid White Curve - Before"
[image2]: ./lined_test_images/lined_solidWhiteCurve.jpg "Solid White Curve - After"
[image3]: ./test_images/solidWhiteRight.jpg "Solid White Right - Before"
[image4]: ./lined_test_images/lined_solidWhiteRight.jpg "Solid White Right - After"
[image5]: ./test_images/solidYellowCurve.jpg "Solid Yellow Curve - Before"
[image6]: ./lined_test_images/lined_solidYellowCurve.jpg "Solid Yellow Curve - After"
[image7]: ./test_images/solidYellowCurve2.jpg "Solid Yellow Curve 2 - Before"
[image8]: ./lined_test_images/lined_solidYellowCurve2.jpg "Solid Yellow Curve 2 - After"
[image9]: ./test_images/solidYellowLeft.jpg "Solid Yellow Left - Before"
[image10]: ./lined_test_images/lined_solidYellowLeft.jpg "Solid Yellow Left - After"
[image11]: ./test_images/whiteCarLaneSwitch.jpg "White Car Lane Switch - Before"
[image12]: ./lined_test_images/lined_whiteCarLaneSwitch.jpg "White Car Lane Switch - After"
---

### Reflection

###1. The pipeline

My pipeline consisted of 6 steps:
1. Convert image to grayscale
2. Apply a gaussian blur to the grayscaled image
3. Apply Canny edge detection to blurred image
4. Mask the edge detected image with a trapezoid from the bottom left and right corners to _almost_ a point in the lower 40% of the image and in the middle of the  of image's X axis
5. Applied a Hough line transform to the masked image
6. Overlayed the line transformation over the original image

However, in function that performs line transformation a single call to ```draw_lines``` hides a complex sequence that:
1. Divides the lines into left side and right side lane lines
2. Filters the right and left lines
3. Merges the flitered lines into a single line

Dividing the lines is simple process of determining their slope and then categorizing them based on whether the slope is positive or negative.

The filtering process is more complex in that it first checks that the slope is of a reasonable value for a right or left lane line. Secondly it extrapolates the line to bottom of the image and ensures that the line interects the bottom of image at a reasonable value for a left or right lane line (0 to midpoint and midpoint to max respectively).  Finally, it extrapolates the line to the value where it crosses the lower 40% of the image and ensure the x value of that point is in the middle third of the X axis range. If the line satisfies all these conditions, it is considered for further processing.

The final part of process is to merge the lines which is done by taking the average mid-point and slope for each of the remaining left and right lines and then creating a single line that passes through that midpoint with that slope.

That single line is the one overlayed over original image.

Some example of the pipeline on still images are below:
|   Before    |     After      |
|:-----------:|:--------------:|
| ![][image1] |   ![][image2]  |
| ![][image3] |   ![][image4]  |
| ![][image5] |   ![][image6]  |
| ![][image7] |   ![][image8]  |
| ![][image9] |   ![][image10] |

Videos demonstrating the pipeline are included in the project as well ([white](white.mp4), [yellow](yellow.mp4), [extra](extra.mp4)).

###2. Potential shortcomings with your current pipeline

The region of interest is very narrow at the top and as a result would be very sensitive to the camera's pitch relative to the road.

All the example videos were are fairly smooth roads, so even if the camera's pitch remained fixed relative to the car on a bumpy road it could cause issues in the line filtering process due to line angle shifts relatice to the camera's field of view.

###3. Possible improvements to the pipeline

There is a lot of "jitter" from one frame to the next, so a method of smoothing this out would be helpful.  This would likely require keeping references to the lines drawn on 1 or more of the previous frames and then averaging them.

Another area for improvement would be to examine what happens when the car changes lanes. No vides of this were supplied, so I don't know how this process would handle a lane change.
