## Image Registration

`ImageRegistrator` is a class that registrater a target image based on user selected landmark pixels on reference and 
target images. 

*class* **plantcv.transform.ImageRegistrator(img_ref, img_tar, figsize=(12, 6))**

To initialize an instance of `ImageRegistrator` class, two required parameters are `img_ref` and `img_tar`, represent 
for target image and reference image, respectively.

Another optional parameter is the desired figure size `figsize`, by default `figsize=(12,6)`.

### Attributes
**img_ref** (`ndarray`, datatype: uint8, required): input reference image.
**img_tar** (`ndarray`, datatype: uint8, required): input target image.
**points** (`list`): list of coordinates of selected pixels on reference image and target image.
**model** (`numpy.ndarray`): tranformation matrix of size (3,3) that register the target image to the reference image. 
**img_registered** (`numpy.ndarray`, datatype: uint8, required): registered target image.

### Class methods
**display_coords()**

Display user selected coordinates for both reference image and target image

**regist()** 

Register the target image to the reference image based on user selected landmark points.

**save_model(model_file="model")**

Save the transformation matrix used for image registration.

```python

from plantcv import plantcv as pcv
# Initialize an image registrator
img_registrator = ImageRegistrator(img_ref, img_tar, figsize=(12, 6))

## 
# collecting land mark points
##

# Display user selected coordinates on both reference image and target image
img_registrator.display_coords()

# Register the target image to the reference image based on the model calculated from selected points
img_registrator.regist()

# Save
img_registrator.save_model(model_file="my_model")

```

Reference image (a thermal image):

![thermal_ref](img/documentation_images/transform_img_registration/ref_therm.png)

Target image (a RGB image):

![thermal_ref](img/documentation_images/transform_img_registration/tar_rgb.png)

Overlay these two images:

![overlay](img/documentation_images/transform_img_registration/overlay_before.png)

Overlay two images after image registration:

![overlay_after](img/documentation_images/transform_img_registration/overlay_after.png)


Check out this video for how this interactive tool works!
<iframe src="https://player.vimeo.com/video/522809945" width="640" height="360" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>


**Source Code:** [Here](https://github.com/danforthcenter/plantcv/blob/master/plantcv/plantcv/transform/img_registration.py)
