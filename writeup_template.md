**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on the work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Description of the pipeline. As part of the description, explaination of modified draw_lines() function.

#### Summary of steps
My pipeline consisted of 7 steps. 

1) resized the image to a standard (960, 540) shape using a helper function
2) converted the image to grayscale
3) defined a kernel size of 3 and applied Gaussian smoothing
4) defined our parameters for Canny as

    low_threshold = 0
    
    high_threshold = 255 
    
    and applied Canny Algorithm
5) created a masked edges image by defining a four sided polygon to mask

   ```python
   vertices = np.array([[(0,imshape[0]),(430, 310), (530, 310), (imshape[1],imshape[0])]], dtype=np.int32)
   ```
   
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
  
#### Modification of draw_lines() function  
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by implementing a helper function lane_lines() which returns two lane lines by splitting the lines into two slope categories ,one positive and one negative 
    and then extrapolate the line sets
```python
lanes = lane_lines(lines) # find the lane lines
    for lane in lanes:
        for x1, y1, x2, y2 in lane:
            cv2.line(img, (x1, y1), (x2, y2), color, thickness) # draw the lane lines
```
##### lane_lines(img, width, height) function:
```python
def lane_lines(lines):
    """return two lane lines by splitting the lines into two slope categories
    and extrapolating the line sets
    """
    
    # splits lines into two sets depending on positive and negative slopes
    xLeft = []
    yLeft = []
    
    xRight = []
    yRight = []
    
    for line in lines: 
        for x1,y1,x2,y2 in line:
            slp = (y2-y1)/(x2-x1)
            if abs(slp) > 0.5: # filter the nearly horizontal lines
                if slp < 0:
                    xLeft += [x1, x2]
                    yLeft += [y1, y2]
                else:
                    xRight += [x1, x2]
                    yRight += [y1, y2]

    if (len(xLeft) > 0) and (len(yLeft) > 0) and (len(xRight) > 0) and (len(yRight) > 0):
        zLeft = np.polyfit(xLeft, yLeft, 1) # fits the points with positive slope to a single line
        zRight = np.polyfit(xRight, yRight, 1) # fits the points with negative slope to a single line
        
        # extracts a function for each of the lane lines
        fLeft = np.poly1d(zLeft) 
        fRight = np.poly1d(zRight)
        
        # Calculate the positions for each of the defining points of the lane lines 
        # depending on y = 330 and y = image.shape[0]
        xLeftTop = int((fLeft-330).roots[0])
        xLeftBottom = int((fLeft-image.shape[0]).roots[0])
        yLeftTop = 330
        yLeftBottom = image.shape[0]

        xRightTop = int((fRight-330).roots[0])
        xRightBottom = int((fRight-image.shape[0]).roots[0])
        yRightTop = 330
        yRightBottom = image.shape[0]
        return np.array([[[xLeftTop, yLeftTop, xLeftBottom, yLeftBottom]], 
                         [[xRightTop, yRightTop, xRightBottom, yRightBottom]]])
    else:
        return np.array([[]])
```

One importand feature here is the discarding the nearly horizontal lines by filtering slope values between -0.5 to 0.5
which helps to avoid outliers and jitterness

```python
if abs(slp) > 0.5: # filter the nearly horizontal lines
    if slp < 0:
        xLeft += [x1, x2]
        yLeft += [y1, y2]
    else:
        xRight += [x1, x2]
        yRight += [y1, y2]
```


![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
