# AI-900: Computer Vision Workloads on Azure

## Introduction to Computer Vision
  - Computer Vision: analyze images, and return information about them
    + Image captions (i.e. describe image): text + confidence (may have multiple captions with various confident levels)
      - Dense captions: multiple captions for various region of the image
    + Tagging: key terms for the image, each with a confident levels
    + Object detections: object name + bounding box + confident
    + Facial detections: face + bounding box of face + confident score
    + Optical character recognition (OCR)
  - Azure AI Custom Vision (a.k. Computer Vision): API endpoint with key
  - Azure AI services: support Azure AI Vision, speech, translation, etc. --> also API endpoint with key
    + Prefer services if use more than one AI services, and need to simplify admin

## Making our own models
  - Image classification: Each model need at least 5 sample data with label, and at least 2 two categories of labels
  - Object detection: min. 15 images per tag
  - Can use Custom Vision portal on Azure to build model
    + Need training resource and prediction resource when starting (may create both at start)
    + Separate training & prediction to track utilization
    + Once deploy, access via API endpoint + key

## Analyzing Faces
  -  Azure AI Face: to detect & analyze face on images or video streams (or use Azure AI Services)
    + adding face-specific features (vs. AI Custom Vision): recognition, detailed facial analysis
  - Facial Analysis --> for face attributes: 
    + wearing glasses/sunglasses, how blurry the image, over/under-exposed, visual noise (e.g. grainy)
    + head pose: coordinates match faces pitch, roll and yaw
    + objects blocking the face
    + wearing mask ?
  - Facial Recognition: model to recognize someone based on their features --> detect someone in a group
    + still work somewhat for faces with extreme angles, being blocked
    + Find similar faces/group faces together
    + **NOTE**: Need an intake form to get approval

## Reading Text from Images

## Analyzing Documents and Forms

## Analyzing Video
