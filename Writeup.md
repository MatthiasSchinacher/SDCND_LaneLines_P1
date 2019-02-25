# __Finding Lane Lines on the Road__
## Project Writeup
This document is my writeup/ report for the first
project assignment within udacity's Self Driving Car Nano Degree
(henceforce SDCND).

The stated goal was to implement a pipeline for lane finding on
a road for videos shot from the front of the car.

The implementation in the actual [notebook can be found here](P1.ipynb)

### Reflection
The structure of the code deliverable was fixed to be a python- jupyter
notebook, where the basic flow was provided. The actual coding required
was an adjustment to a "draw_lines"- function and the implementation of a
pipeline- function; the latter was to find lanes in single image.

#### The Pipeline
My pipeline was based on the quiz regarding the "Hough Transform" and guided by
the helper functions already present/ provided with the initial jupyter notebook.

The pipeline uses following steps:
* transform the image to greyscale and apply a gaussian blur
* apply Canny- edge detection to the image
* define a "region of interest" and apply to the image
* apply a Hough- transformation to the image
* as the last step, the resulting image is combined with the original source image

##### Region of interest
I experimented quite a while with the regions of interest, first with a simple
triangle and then with more complex areas. I ended up using a polygon with
6 edges.

##### Modifications to "draw_lines()"
The required basic structure of the modification was to draw (at most) 2 lines,
one left and one right, to mark the 2 edges of the lane the car is on, instead of
all the lines given.

I initially played with a simple approach, where I used some basic criteria to
determine, whether a given line was "part of" the left of right edge/ border.
For each given line I computed the respective x-value for the y- values of
ymax (bottom of image) and yT (the y value that would be used to define the
region of interest, where y>yT is a required condition), averaged these seperatly
for left and right and used these values as the endpoints for the resulting 2 lines.

This did not work very well, thus I ended up using a more complex approach.
Again I use criteria to decide whether a given input- line is a "left" or "right"
line; but now I'm using linear regression for the end- points of the input- lines
to compute the 2 output lines.
To compensate for partial lines of different length, the values are weighted
according to the length of the lines regarding the computation of the
linear regression parameters.

###### Criteria for "left" or "right"
An input- line must have a minimum slope (absolute value)
of '''mina''' (=0.1) to be considered at all. For a "left" input- line the slope
must be negative and the average x- value of the two endpoints must be smaller
than xmax/2 (xmax being the x- value for the right image border).
Likewise, a "right" input-line must have a positive slope and the x-values
of the endpoints must average > xmax/2.
Additionally the 2 endpoints of the input-line must differ a minimum number
of ticks in each direction, x and y, to be considered.

### Potential shortcomings with the current pipeline
The current pipeline has some difficulties with lane lines that are not
solid, especially when very small partial lines - almost dots or small lines
that are orthogonal to the actual lane line - are present.

I guess this might be due to imperfect pipeline parameters or might be a result
of the linear regression approach.

Also, I got the impression, that sometimes the actual lane line was not really
a mathematical line and thus the computed line did not really "fit".

### Possible improvements
What one could always do of course is to experiment with additional combinations
of parameter values.
But what I personally really be interested in is 2 improvements:
* using a higher order regression; of course this would not be strictly identify a lane "line"
* using somehow information from the respective previous picture as input; this would of course require a major code refactoring :-)  
