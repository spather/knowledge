# Jupyter Notebook Image/Plot Cheatsheet

### Set up Matplotlib to display inline in notebook 

```python
%matplotlib inline 
from matplotlib import pyplot as plt
```

### Load an image
```python
import cv2

img = cv2.imread(filename)
```

This will load the image in BGR format. To convert to RGB:

```python

# img_rgb is copy of the data
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

# img_rgb is a read-only view of the original data in RGB format
# If you pass this to operations that mutate the image you'll get
# an exception.
img_rgb = img[:, :, ::-1]
```

### Shear an image
```python 
def shear(img, shx, shy):
    M = np.float32([[1  , shx, 0],
                    [shy, 1  , 0],
                    [0  , 0  , 1]])
    h, w = img.shape[:2]
    return cv2.warpPerspective(img, M, (w + int(shx*h), h + int(shy*w)))
```

### Warp an image

```python
def warp_matrix(img, magnitude):
    h, w = img.shape[:2]
    src_points = np.float32(
        [[0  , 0  ],
         [0  , h-1],
         [w-1, 0  ],
         [w-1, h-1]]
    )
    dst_points = np.float32(
        [[magnitude  , magnitude  ],
         [0          , h-magnitude],
         [w-1        , 0          ],
         [w-magnitude,h-magnitude ]]
    )
    return cv2.getPerspectiveTransform(src_points, dst_points)

# Apply it
m_warp = warp_matrix(img, warp_magnitude)
warped = cv2.warpPerspective(img, m_warp, size)
```

### Display one image

```python
plt.figure(figsize=(10,6))
plt.imshow(some_gray_image, cmap="gray")
plt.title('my title here')
plt.axis("off")
```

### Display a bunch of images

```python
fig = plt.figure(figsize=(16, 4))
fig.suptitle("Overall Title", fontsize=12)

# Next three arrays should all be the same length:
#  titles: the title strings to display above each image
#  cmaps: the color map to use (None means use the default color map, which works for color images)
#  images: the image data for each image (like that's returned from cv2.imread())

titles = ["Original", "Crop", "Gray", "Blur", "Sobel Y 5"]
cmaps = [None, None, "gray", "gray", "gray"]
images = [img_rgb, rgb_crop, gray_crop, blur_crop, sobely5_crop]

for i, (title, cmap, image) in enumerate(zip(titles, cmaps, images)):
    ax = fig.add_subplot(1, len(images), i+1)
    ax.imshow(image, cmap=cmap)
    ax.set_title(title, fontsize=10)
    ax.set_xticks([])
    ax.set_yticks([])
```

### Plot some lines

```python
fig = plt.figure(figsize=(16, 10))
ax = fig.add_subplot(nrows, ncols, 1)
ax.set(
    title="Plot Title"
)
ax.plot([...x data ... ], [... y data ... ], label="Line label")
ax.plot([...x data ... ], [... y data ... ], label="Line label 2")
ax.legend()
```

### Plot KDE

```python
import seaborn as sns

plt.figure()
ax = sns.kdeplot([...data...])
ax.set(title="Plot title")
```

### Set the white grid style
If using Seaborn

```python
sns.set_style("whitegrid")
```

otherwise
```python
plt.style.use("seaborn-whitegrid")
```

### Find indices of non-zero elements in a numpy array
```python
np.transpose(somearray.nonzero())
```

### Rotation
[Good explanation](https://theailearner.com/tag/cv2-getrotationmatrix2d/) of the elements of the transform matrix returned by [`cv::getRotationMatrix2D()`](https://docs.opencv.org/3.4/da/d54/group__imgproc__transform.html#gafbbc470ce83812914a70abfb604f4326).

Rotate without clipping the edges:

```python
def rotation_size(w, h, rotation_angle_degrees):
    rotation_angle = math.radians(rotation_angle_degrees)
    return (
        int(w * abs(math.cos(rotation_angle)) + h * abs(math.sin(rotation_angle))),
        int(w * abs(math.sin(rotation_angle)) + h * abs(math.cos(rotation_angle)))
    )

def rotate(img, angle):
    h, w = img.shape[:2]
    rot_matrix = cv2.getRotationMatrix2D((w // 2, h // 2), angle, 1.0)

    nW, nH = rotation_size(w, h, angle)

    tx = (nW / 2) - (w / 2) 
    ty = (nH / 2) - (h / 2)

    rot_matrix[0, 2] += tx 
    rot_matrix[1, 2] += ty 
    
    return cv2.warpAffine(img, rot_matrix, (nW, nH))
```
