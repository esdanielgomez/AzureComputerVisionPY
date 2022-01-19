# **Azure Computer Vision in Python**
## Created by Daniel Gomez / [esdanielgomez.com](link-URL)
![image-alt-text](https://gokan.ms/wp-content/uploads/2017/10/sii-canada-logo-it-microsoft-mvp-most-valuable-professional.png)
## **Computer Vision con Python**

pip install --upgrade azure-cognitiveservices-vision-computervision

pip install --upgrade pillow

### Imports and authenticate

```python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from msrest.authentication import CognitiveServicesCredentials
import time

subscription_key = "a55140a5edd64e7dbcd8c6d581a2ee43"
endpoint = "https://aicomputervision3.cognitiveservices.azure.com/"
computervision_client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(subscription_key))
```

### 1. Read API for OCR 
Optical Character Recognition
![Example](https://i.ibb.co/nj2PvcD/Demo-Letras.jpg)

```python
# Get an image with text
#read_image_url = "https://i.ibb.co/nj2PvcD/Demo-Letras.jpg"
#read_image_url = "https://i.ibb.co/qN6HqxC/Pantalla.png"

# Call API with URL and raw response (allows you to get the operation location)
read_response = computervision_client.read(read_image_url,  raw=True)

# Get the operation location (URL with an ID at the end) from the response
read_operation_location = read_response.headers["Operation-Location"]

# Grab the ID from the URL
operation_id = read_operation_location.split("/")[-1]

# Call the "GET" API and wait for it to retrieve the results 
while True:
    read_result = computervision_client.get_read_result(operation_id)
    if read_result.status not in ['notStarted', 'running']:
        break
    time.sleep(1)

# Print the detected text, line by line
if read_result.status == OperationStatusCodes.succeeded:
    for text_result in read_result.analyze_result.read_results:
        for line in text_result.lines:
            print(line.text)
            #print(line.bounding_box) - Coordinates

print()
```

### 2. Get image description
The following code gets the list of generated captions for the image. See Describe images for more details.

![image-alt-text](https://i.ibb.co/ZcjcVyp/Demo-Descrip.jpg)

```python
print("===== Describe a image ===== \n")
# Call API
remote_image_url = "https://i.ibb.co/ZcjcVyp/Demo-Descrip.jpg"
description_results = computervision_client.describe_image(remote_image_url )

# Get the captions (descriptions) from the response, with confidence level
print("Description of remote image: ")
if (len(description_results.captions) == 0):
    print("No description detected.")
else:
    for caption in description_results.captions:
        print("'{}' with confidence {:.2f}%".format(caption.text, caption.confidence * 100))

print()
```

### 3. Detect objects
The following code detects common objects in the image and prints them to the console. See Object detection for more details.
![image](https://i.ibb.co/2jN7bHr/Obj1.jpg)

```python
# Get URL image with different objects
remote_image_url_objects = "https://i.ibb.co/2jN7bHr/Obj1.jpg"
# Call API with URL
detect_objects_results_remote = computervision_client.detect_objects(remote_image_url_objects)

# Print detected objects results with bounding boxes
print("Detecting objects in remote image:")

if len(detect_objects_results_remote.objects) == 0:
    print("No objects detected.")
else:
    for object in detect_objects_results_remote.objects:
        print("object at location {}, {}, {}, {}".format( \
        object.rectangle.x, object.rectangle.x + object.rectangle.w, \
        object.rectangle.y, object.rectangle.y + object.rectangle.h))
```

# Muchas gracias! 

## **[esdanielgomez.com](esdanielgomez.com)**

[Quickstart: Use the Image Analysis client library or REST API](https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/image-analysis-client-library?tabs=visual-studio&pivots=programming-language-python)
