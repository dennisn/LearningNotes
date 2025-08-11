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
  - "Read OCR engine": an advanced machine‑learning‑based model that can extract text for us
    + Two edition: one for non-document images, the other for text-heavy scans
  - Text from image: extracting pages (how many pages of text), lines (line of text & its bounding-box), and words (each words & bounding-box)

## Analyzing Documents and Forms
  - Read text using Azure AI Vision
  - Also use "**Document Intelligence**" to automate the extracting, understanding & saving of the text
    + can interpret different fields via prebuilt models for receipts, invoices, ID document, etc.
    + Custom models, or general document analysis for structured data for new docs/forms

## Analyzing Video
  - Using "**Azure AI Video Indexer**" to extract insights from videos using its array of video and audio models
    + Face detection: detect celebrities, thumbnail for faces found
    + OCR: extract text from video
    + Content moderation: detect adult or racy visuals
    + Scene changes 
    + People tracking: observe/track people in video using bounding box, when they appear/disappear from frame
    + Label items in video --> identify items
  - Audio:
    + Transcription
    + Emotion detection: mood of the speaker based on their speech
    + Automatic language detection
    + Translation
    + Keyword & Named-entity extraction: brands, location, people --> video content
  - Usage
    + Accessibility: translate/transribe the video
    + Content moderation: black/warning of inappropriate videos
    + Deep search: example: search for specific people

## Exam Tips
  - Facial detection vs. facial recognition
  - Azure AI Services mutli-service account resources (aka Cognitive Services)
  - Object detection vs. image classification
  - Azure AI Face vs. Azure AI Vision: facial feature analysis vs. face coordinate
  - Small amounts of text --> Azure AI Vision and the Read OCR image edition
    + Word-heavy pictures or documents --> Azure Document Intelligence
  - Azure AI Video Indexer: analyze video