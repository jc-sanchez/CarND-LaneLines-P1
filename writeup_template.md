**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

## Reflection

### My Pipeline 

My pipeline consists of the following steps:
1. Convert the images to grayscale.
2. Apply the Canny transform to find the edges.
3. Create a Region of Interest and filter out edges that are outside of this region.
4. Identify lines by using a Hough Transform.
5. Use each line's slope to determine if it is part of the left line or right line.
6. Remove outlier lines and find the average y = mx + b parameters.
7. Add parameters to a class that averages the last n parameters to have smoother lines.
8. Finally, use the averaged y = mx + b parameters to draw the appropriate lines in the video.

The first 4 steps were covered in lesson 2.  I had to come up with steps 5 - 8 to accomplish the remaining specifications of the project.  The first thing that I had to do was determine if a line was part of the left line or right line.  I decided to classify them based on their slopes.  The left line had a negative slope and the right line had a positive slope.

I then averaged all the lines so that I could get an averaged y = mx + b equation.  When I first ran the pipeline, the lines weren't as accurate as I would like.  They were also somewhat shaky.  I decided to filter out outlier lines before averaging, based off of their slopes.  This helped reduce the shakyness and improve the accuracy.

I will come back to step 7 later, because this was one of the last things implemented. Using the averaged parameters for y = mx + b, I was able to draw the appropriate lines to overlay the video. I simply gave the equation a min and max y, and solved for the corresponding x.  I tested my pipline on the first two sample videos and it seemed to perform well.  But when I tested it on the challenge video, it failed spectacularly.  This is when I decided to implement step 7.

Step 7 saves the previous n line equation parameters (default is 3) and uses the average of these to draw the lines.  This reduces the effect that noise classified as lines has on the pipeline.

### Potential Shortcomings with the Current Pipeline

The current pipeline has some shortcomings.  It currently can't hand noise very well.  I tested it on the challenge video and it did not perform so well when it encountered the tree's shadow or the change in color in the road.  I was able to reduce the effect of the noise on my pipeline by averaging the line equation parameters of the current frame with the previous 30 parameters.  The downside to this approach, however, is that the pipeline will be slower to react to whenever a drastic change does occur.

Another shortcoming is that whenever the pipeline doesn't detect at least one line that represents the left or right line in a frame, it uses the average paramters of the previous n frames to estimate the missing line. This works in the videos because the pipeline doesn't go more than 1 or 2 frames not detecting one of the lines.  But this approach could cause a major issue if there where many consecutive frames where one of the two lines weren't represented. This is because the pipeline would use parameters of the last n frames that detected that line.  If the line happened to change drastically while not being detected, the estimate would be way off.


### Possible Improvements

I think the most important improvement is to handle noise.  I can think of a couple of ways that this may be accomplished.  One way is to have more criteria that a potential line must meet before it is considered to represent one of the two lines.  The current implementation filters out outliers by the difference of the potential line's slope to the median slope.  If there is a lot of noise in a frame, then an inaccurate median slope may be selected.

One possible improvement that I saw on the slack channel is to use weights when averaging the lines.  The longer the line, the more weight that it would have.  If noise usually creates short lines, this would reduce the effect it has on the final lines.