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
