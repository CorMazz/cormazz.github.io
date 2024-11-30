
# Crack Image Recognition

## 11/30/2024

Decided to use the Segment Anything Model since it was listed as SOTA. Used Google CoLab because my laptop was weak and didn't have a GPU, and I was having trouble installing pytorch and didn't want to deal with it. Found a [CoLab Tutorial](https://colab.research.google.com/github/roboflow-ai/notebooks/blob/main/notebooks/how-to-segment-anything-with-sam.ipynb#scrollTo=WYyhnP4xFO5_) that goes over an example of how to segment using the model.

Used these 3 images with permission from GE.

![Img](test_images.png)

Tried to just use the default SAM model with auto mask to see if it would identify the crack.

![Img](auto_mask_test.png)

The Colab example had bounding box prompting built in, so I tested that as well.

![Bounding Box Segmentation Test](bounding_box_test.png)

It didn't look like it worked too well, but if you look at the individual masks, it gets close, so there is hope.

![Bounding Box Segmentation Masks](bounding_box_test_masks.png)

Tested out the [Segment Anything Demo](https://segment-anything.com/demo#) to see if point prompting would help the model do better.

![Img](point_prompted_test.png)

The model arguably did do better.