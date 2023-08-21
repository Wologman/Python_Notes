

### Fast Loading with PyTurbojpeg

This seems to work on windows, and can be installed with Conda.  Not much documentation but [see here]( # https://learnopencv.com/efficient-image-loading/)
```shell
conda install  libjpeg-turbo
conda install pyturbojpeg
```

```python
from turbojpeg import TurboJPEG

jpeg = TurboJPEG()
with open(image_path, 'rb') as in_file:
	jpeg_buf = in_file.read()
image = jpeg.decode(jpeg_buf)
```

This hasn't helped the project I was working on as the loading I suspect is limited by the read speed from storage.