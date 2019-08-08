**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on the work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Description of the pipeline. As part of the description, explaination of modified draw_lines() function.

My pipeline consisted of 7 steps. 

1) resized the image to a standard (960, 540) shape using a helper function
2) converted the image to grayscale
3) defined a kernel size of 3 and applied Gaussian smoothing
4) defined our parameters for Canny as

    low_threshold = 0
    
    high_threshold = 255 
    
    and applied Canny Algorithm
5) created a masked edges image by defining a four sided polygon to mask

   with vertices = [[(0,imshape[0]),(430, 310), (530, 310), (imshape[1],imshape[0])]
   
6) defined the Hough transform parameters as

  ```python
    rho = 2 # distance resolution in pixels of the Hough grid
    theta = np.pi/180 # angular resolution in radians of the Hough grid
    threshold = 50     # minimum number of votes (intersections in Hough grid cell)
    min_line_length = 100 #minimum number of pixels making up a line
    max_line_gap = 200 # maximum gap in pixels between connectable line segments
  ```
  then applied hough transform
  
7) Combined the lines' image with the color image
  
  
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by implementing a helper function lane_lines() which returns two lane lines by splitting the lines into two slope categories ,one positive and one negative 
    and then extrapolate the line sets
    
One importand feature here is the discarding the nearly horizontal lines by filtering slope values between -0.5 to 0.5
which helps to avoid outliers and jitterness

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
