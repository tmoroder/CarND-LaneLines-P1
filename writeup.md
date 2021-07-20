# Finding Lane Lines on the Road

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

___

[img_pipeline_example]: ./examples/pipeline_example.jpg "Line segments of lane"
[img_parameter_tuning]: ./examples/parameter_tuning.png "Parameter tuning with Jupyter widgets"
[img_pipeline_example_result]: ./examples/pipeline_example_result.jpg "Pipeline example result"


# Reflection

## 1. Pipeline

The pipeline that finds lane lines on the road consists of two integral parts. First, one detects the individual line segments of the lanes in an image. Afterwards, this collection of segments is processed to unify and to extend the line segments over the full visible lane.

### 1.1. Line segments of the lane

In order to identify the line segments of the lanes in an image I follow the logic as presented in the course material. It consists of the following steps:
1. Edge detection
   - Convert image to grayscale
   - Optionally Gaussian blur
   - Canny edge detection
2. Selection of region of interest 
3. Line detection using Hough transformation

An example of the individual steps is given in the following figure. It displays the original image (original), after edge detection (edges), applying region of interest selection (roi_edges), and finally after the line detection using Hough transformation (lines). The last two images show the found line segments superimposed onto the original image (result) and only on the region of interest (roi_result).

![][img_pipeline_example]

As emphasized in the course, a large portion consists in parameter tuning. To this end, I employed Juyter widgets to interactively carry out this optimization in the notebbok. Since this is not visible in the static version of the notebook, let me include a screenshot here:

![][img_parameter_tuning]


### 1.2. Left and right lane 

After the initial line segments are detected, one likes to extend them over the full visible lane. For this I finally employed the following method:

1. Divide line segments into left and right lane segments, for which the slope of the line segment is used.
2. Average slope and intercept of all line segments per side
3. Compute end points at region of interest heights for each side
4. Draw those lines to the image

Let me note, that I used a weighted average using line length so that longer lines have a stronger effect. Additionally, I only use line segments with a slope corresponding to an (absolute) angle in [20, 70] for averaging. A line segment outside this interval is either too flat or too steep to correspond to the lane line. 

An example of the final result can be found here:

![][img_pipeline_example_result]


## 2. Shortcomings of current pipeline

Currently, I see the following shortcomings of the pipeline:

- The current pipeline is not color selecting lane makers. 
- So far the algorithm only focuses on straight lane lines.
- Other _straight lines_ in the region of interest are creating disturbances.
- The pipeline parameters must be tuned to scenario.
- The performance of the algorithm in several other scenarios is unclear, e.g.,
  * rainy weather,
  * car in front of the car,
  * lane change.
  
  
## 3. Possible improvements of pipeline

In my opinion, the pipeline could be enhanced with:

- Color selecting should be given a serious try.
- One should check if curves are being detected if one fits a quadratic (or low order) polynomial to the detected line segments.
- For videos a lane smoothing over multiple image frames could increase stability and robustness.
