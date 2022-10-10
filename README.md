# image_compressor

## Install

```pip install Pillow```

### utils.py

```
from io import BytesIO
from PIL import Image
from django.core.files import File


def compress(image):
    if not image:
        return image
    if '.svg' in image.name:
        return image
    img = Image.open(image)
    img_size = len(img.fp.read())

    if img_size / (1024*1024) > 1:
        if image.name.split(".")[1] == "png":
            img = img.convert("RGB", palette=Image.ADAPTIVE, colors=256)
            thumb_io = BytesIO()
            img.save(thumb_io, 'jpeg', quality=70, optimize=True)
            new_image = File(thumb_io, name=image.name.split(".")[0] + ".jpg")
        else:
            thumb_io = BytesIO()
            img.save(thumb_io, 'jpeg', quality=20, optimize=True)
            new_image = File(thumb_io, name=image.name)
        return new_image
    else:
        return image
```

### model.py
```
class ModelName(model.Model):
    ...

    def save(self, *args, **kwargs):
        self.image = compress(self.image)
        return super(ModelName, self).save(*args, **kwargs)
```
