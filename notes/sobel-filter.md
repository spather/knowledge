# Sobel Filter
OpenCV implementation:

```python
    import cv2 
    
    img = cv2.imread(filename)
    img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    img_blur = cv2.GaussianBlur(img_gray, (3, 3), 0) # Gaussian blur helps reduce noise
    
    sobely = cv2.Sobel(src=img_blur, ddepth=cv2.CV_64F, dx=0, dy=1, ksize=5) 
```

## Kernel values
[Fantastic StackOverflow answer explaining how kernel values are calculated for arbitrary kernel sizes](https://stackoverflow.com/a/41065243).

Get the kernel values for a given size (based on [StackOverflow answer](https://stackoverflow.com/a/31730106):

```python
def get_sobel_kernel(dx, dy, ksize):
    kernel = cv2.getDerivKernels(dx, dy, ksize)
    return np.outer(kernel[0], kernel[1])
```
