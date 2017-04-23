# **Finding Lane Lines on the Road**

## 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.
My pipeline consists of five steps:
1. Convert the input image into grayscale.
2. Apply Gussian smoothing on the grayscale. The size of the Gussian kernel is set to 5.
3. Apply canny edge detection, where the low threshold is set to 50 and the high threshold is set to 150, respecitively.
4. Select a region of interest by defining a four side polygon and setting pixels outside of this region to black. The region is set to [(0.1\*xsize, 0.95\*ysize),(0.4\*xsize, 0.6\*ysize), (0.6\*xsize, 0.6\*ysize), (0.95\*xsize, 0.95\*ysize)], where xsize is the image width and ysize is the image height. The region of interest is shown in 
![region_image][region.jpg].

[region.jpg]: ./examples/region.jpg "region of interest"

5. Do the hough transform. Here are the paremeters I set: (rho = 2, theta = np.pi/180, threshold = 50, min_line_length = 50, max_line_gap = 100). After I got the lines from the hough transformation, the lines are first divided into two groups. For the line with slope < -0.5 and the two endpoints are in the left half side of the image, the line is in the left group, while for the lines with slope > 0.5 and the two endpoints are in the right half side of the image, the line is in the right group. For the lines in each group, a weighted average slope and y-interest is calcuated, where the weight is set to the length of each line. Then, I use the image height and 0.6 * image height on the average line to extrapolate the final line. Finally, an weight image is calculated based on the inital image and the lines just derived.

### 2. Identify potential shortcomings with your current pipeline
One shortcoming happens when drawing lines on the video. Currently, the lines are calculated based on each individual picture frame. Thus, the lines drwan would be bumpy from one picture frame to the other picture frame. 

The other short coming is regarding the yellow line detection. Converting the image into grayscale seems not able to strongly contrast the yellow color line comparing to the white color line, which makes the yellow line and other noises would get similar gradients during canny edge transform, and thus, in my experiment, adusting the threshold doesn't help filter out those noises, as yellow lines are within similar range. So, if there are some objects close to the yellow line in the defined region of interest, it is more likely my pipeline would detect those objects as the land lines.

I also discover two additional issues when working on the challenge video. First, the canny edge detection discovers nothing when the road is all under the sun. Next, since my extrapolation only does the straight line, it won't get the correct land line when the road starts to make turns. 

### 3. Suggest possible improvements to your pipeline
To make the lines smooth on the video, a possible improvement is to store all the lines in the previous picture frame and get the average line with the previous line and the current ones. 

In addition to converting the image to grayscale, a possible improvement is to select white color and yellow color, and black out other pixels. By doing so, it might help detect the yellow lines.

I don't have any idea of improving the pipeline for the challenge video. It seems to me that the canny edge detection is not sufficient for images in the shade or under the sun. Some processing before canny edge detection is desired to make them detectable.