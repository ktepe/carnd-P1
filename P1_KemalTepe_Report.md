### Finding Lane Lines on the Road Project 1

**Kemal Tepe**
**ketepe@gmail.com**
**ket**
---
**Objective: to develop a software to identify and mark the the road lanes using lane lines.**

In this project, techniques and algorithms introduced in the first module of CarND are utilised to develop the software to find lanes on the road. In order to do this, images are processed in sequence with number of algorithms. The following pipeline is constructed using test images:

### Reflection

Jupyter notebook files are: 

P1_KemalTepe_Movie.ipynb is the final notebook file which processes the clips.

P1_KemalTepe_SingleFrame.ipynb is used to test and develop the algorithms for a static frame.

#### The  pipeline in the program:
Following steps are implemented in the program in the P1_KemalTepe_Movie.ipynb file:
--- init: read the image and set up the necessary parameters.
1. Gray level conversion: in order to prepare image for Canny edge detection. of the image
 ![Alt text](./test_images/grayscale.png?raw=true "Gray Scale Image")

2. Gaussian blurring: in order to improve edge detection algorithm's performance, some blurring is needed to smooth the image to have a better differentiation.
 ![Alt text](./test_images/grayscale_gaussian.png?raw=true "Gray Scale and Gaussian image, kernel size =5")

3. Apply Canny algorithm: Canny algorithm is applied to detect edges. The thresholds of 50 for low and 150 for high are used.
 ![Alt text](./test_images/canny.png?raw=true "After Canny Edge Detection, low=50, high=150 thresholds")

4. Region masking: In order to focus on region of interest a quadrilateral region is identified and masked using Opencv utilities.
  ![Alt text](./test_images/masked_canny.png?raw=true "After Masking Canny Edge Detection")
 
5. Hough transformation: In order to identify lanes from edges, Hough transformation is applied to masked image.
 ![Alt text](./test_images/masked_canny_hough.png?raw=true "After Hough transform and lines on the Canny Edge Detection")
 
6. Combine everything: Using weighted image and Hough lanes, lanes are marked on the original image.
  ![Alt text](./test_images/original_houghmasked_weighted.png?raw=true "After Hough transform and lines on the original image")

7. Using some heuristic technique, solid staright lines are identified and marked on the image using OpenCV utility (cv2.line()).
 ![Alt text](./test_images/left_rigth_lines.png?raw=true "After making singleleft and right line for lane detection.")

The process_image function is created to process movie clips.

```python
def process_image(image):
    #system parameters for number of functions
    gaus_kernel_size=5
    #Canny edge detection parameters
    canny_low_th= 50
    canny_high_th = 150
    #Define masking region
    #image dimentions y=540, x=960
    imshape=[540, 960]
    left_xbottom=0
    left_ybottom=imshape[0]
    
    left_xtop=450
    left_ytop=318
    
    right_xtop=490
    right_ytop=left_ytop
    
    right_xbottom=imshape[1]
    right_ybottom=left_ybottom
    im_vertices = np.array([[(left_xbottom,left_ybottom),(left_xtop, left_ytop), (right_xtop, right_ytop), (right_xbottom,right_ybottom)]], dtype=np.int32)
    # Make a blank the same size as our image to draw on
    hgh_rho = 2 # 1 distance resolution in pixels of the Hough grid
    hgh_theta = np.pi/180 # angular resolution in radians of the Hough grid
    hgh_th = 10     # minimum number of votes (intersections in Hough grid cell)
    hgh_min_line_l = 15 # 5 minimum number of pixels making up a line
    hgh_max_line_gap = 10    # maximum gap in pixels between connectable line segments
    #line_image = np.copy(image)*0 # creating a blank to draw lines on
    #    im_cp= np.copy(image)
    #printing out some stats and plotting
    #print('This image is:', type(image), 'with dimesions:', image.shape)
    #use gray image
    gray_im=grayscale(image)
    #gaussian blur
    gray_im=gaussian_blur(gray_im, gaus_kernel_size)
    #find edges
    gray_im=canny(gray_im, canny_low_th, canny_high_th)
    #mask the region of interest                   
    gray_im=region_of_interest(gray_im, im_vertices)
    #find the hough line and apply to the image
    gray_im,hgh_lines=hough_lines(gray_im, hgh_rho, hgh_theta, hgh_th, hgh_min_line_l, hgh_max_line_gap)
    # compbine everything
    gray_im=weighted_img(image, gray_im, α=0.7, β=1., λ=0.)
   
    return gray_im 
```  

### 2. Identify potential shortcomings with your current pipeline

Number of improvements can be made to the pipeline to improve accuracy of the program in detecting the lanes. 

1. Parameters used in the Canny edge detection can be optimized for images, such as threshold values from statistical analysis or even made dynamically (adaptively) adjusted at every certain number of frames. 

2. Hough transform has number of parameters, we initialized these for the entire movie. However, they can be adjusted adaptively to get the best result. Also, masking region identification can be  done better to return better fitting lines. 

3. My heuristic to draw lines for left and right road lane paints needs improvement too. The heuristic partially failed in the challenge clip because in this clip, the lines are more curvier than the other images, straight line approximation failed in that case. However, more time is needed to work on this part. Especially, an algorithm to better stitch lines returned from Hough transformation would yield much better results.

4. In this project run time of the program and algorithms are not the focus, however, the optimization of the flow is needed to yield a more efficient code.

### 3. Suggest possible improvements to your pipeline

Shortcomings outlines in Section 2 would be addressed in the continuation of the work. 

