# Car-License-Plates-Detection-And-Recognition
## Dataset
Source: Kaggle - Indian vehicle license plate dataset by Sai Sirisha N · Pranav Kant Gaur · vikshipt
https://www.kaggle.com/datasets/eswarchandt/amazon-music-reviews/data?select=Musical_instruments_reviews.csv

CV project for car license plates detection and recognition. Implemented YOLOv8n for detecting and pytesseract for plate number recognition. Evaluated number recognition with character-based accuracy.

## Data preparation
Each image has a corresponding annotation (in XML format) that provides the bounding box of the license plate and the plate number text.

Next, I create a dataframe with filename, width, height, box coordinates of the plate and plate text. Splitted the data into training and testing sets.

Then I created specific folders for convenient input into YOLO. YOLO format for one bounding box is: class_id x_center y_center width height (all coordinates normalized between 0 and 1, relative to image width/height). Also made YAML configuration that tells YOLO where the train and val data are and what the class names are.

## YOLOv8n
Now use the Ultralytics library to train a custom detector. Start from a pre-trained YOLOv8 model (yolov8n.pt – the nano version, for speed) and fine-tune it on the license plate dataset. After training, the best model is saved to runs/detect/train/weights/best.pt.

## Test Images
For each test image:
- Use the YOLO model to predict the license plate location.

- Crop the image to the predicted plate bounding box.

- Preprocess the cropped plate image (converting to gray color) for clearer OCR.

- Read the text from the cropped plate with pytesseract. Configure Tesseract to treat the image as a single block using --psm 6 which improves OCR accuracy for license plates.

- Compare the OCR result with the true text and record it.

## Evaluating OCR
Calculate accuracy by comparing each predicted plate string to the actual plate string character by character. A character counts as correct if it matches in the exact position.

## Overall
mAP50 ~ 0.995, mAP50-95 ~ 0.87 for YOLO means the detector not only finds roughly all the plates, but localizes them precisely.

Character-level OCR accuracy: 45.86% (388/846) is a quite decent result for the images dataset. Tesseract struggles with small, noisy, tilted or low-contrast plate images, so the recognition part of the project is far from ideal, but provides a good baseline.