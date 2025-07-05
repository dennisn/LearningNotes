# Generative AI in the Cloud 
(from Linkedin-Learning)
  - AI wasn't a new tech, but resource intensive
  - Cloud-computing & Saas --> make using Generative AI possible via economic of scales

## 2. Cloud-Based services for Generative AI
  - Storage: central to all AI system
    + Structure storage for ease of access
    + Quality of data: garbage in, garbage out
  - Cloud service --> better scalability
  - NLP: provide a bridge on how we communicate as human --> computer to understand us
  - API for GenAI: simplify the access, especially for 3rd party software
  - Generative Adversarial Networks (GANs): 2 components: 1 to generate new data and 1 to evaluate the realistics of the generated ata
    + Often used in generating images & videos

## 3. Features of Cloud-Based Generative AI
  - Some usages for text data: language translation, generate content, chatbots & virtual assistants, etc.
  - NLP models: 
    + Rule-based (i.e. predefined grammatical rules)
    + Template-based (e.g. customer support message)
    + Statistical language models: use probabilities of word sequences
    + Recurrent Neural Networks (RNNs): deep learning model to determine the probability of word combinations
    + Transformer model: Generative Pre-trained Transformer (GPT)
  - RNN models: suitable for those of sequence in nature (e.g. music generation)
    + Usage: music for advertising, entertainment, stimulus growth in plants
    + Need huge datasets of music --> transform into suitable format for training
    + Still need inputs from human
    + The output must be processed further --> audio file

## 4. Use Cases
  - Art and music: generate new art works, music compositions from prompts, or tailored to user preference
    + presenting ads with musics similar to what user listening to --> more effective
  - Game development: 
    + Procedural content generation: generate contents using pre-defined rules
    + Art & asset generation: from existing assets, data, model --> create new variation
    + Sound & music generation: create sound effects & music for more immersive soundscapes
    + Game balancing & testing: simulate game play with predefined action to test the game respond
    + Player behavior analysis: learn from player data to optimise the experience --> also recommending new games based on past behavior
  - Text generation:
    + Chatbot: virtual customer support
    + Virtual assistant: Google, Sirri, etc
    + Language translation
  - Image & video generation
    + Generate new image/video content (instead of just text)
    + Virtual worlds & simulation: for gaming, architecture
    + AR (Augmented reality) & VR (virtual reality): generate virtual objects & characters, for both business & entertainment
  - Data augmentation: synthesis new data to increase data size
    + Apply simple adjustments like scaling rotation/translation --> for object detection training
    + In NLP: use paraphrasing, word substitutions or introduction of noise
    + Image classification: flipping, cropping, distortion & zooming --> varying versions
  - Personalization: 
    + Customized recommendations
    + Content personalization: recommend articles, videos, etc.
    + Learning & education: personalized study materials, interactive exercises, tostomizable teaching tools
  - Medical diagnosis: need to be used by medical professional to ensure correctness
    + Medical imaging analysis: help spots abnormalities
    + Disease risk assessment: using medical history, genetic markers & personal health choices --> predictions of potential health issues
    + Clinical decision support: helps in analyse patient data, evaluate symptoms and review of medical literature
  - Robotics: 
    + Object recognition & perception: Improving robotic visualization
    + Motion planning & control --> for optimal performance
    + Adaptive & responsive behaviors --> modified to match the environment
  - Other emerging use cases
    + Virtual fashion design
    + Story telling & narrative generation
    + 3D object generation

## 5. Tools & Solutions
  - Amazon SageMaker: support for data processing; model selection, building and training
    + Offer support for framworks such as TensorFlow, PyTorch
  - Google Cloud AI: similar support to SageMaker
    + pretrained models & transfer learning
  - Microsoft Azure AI
    + Azure Cognitive Services: prebuilt APIs for advanced AI-related functions: computer vision, NLP, text or image generation, etc.
  - IBM Watson AI
    + Watson Studio: help data scientists collaborate with software developer
    + Neuro Network Modeller: visual, drag & drop interface to build NN layers
    + NLP services
  - OpenAI
    + GPT (Generative Pre-Trained Transformer): generating human-like scripts
    + Language understanding
    + API integration

## 6. Advanced consideration
  - Edge computing + cloud-based GenAI: model trained on cloud, then data processing + model running on local --> good for use cases which need low latency like autonomous vehicles, industrial automation, IOT devices, etc.
    + Some case, a summary of data is sent to cloud-based server for further processing or storage
  - Federated learning: multiple clients collaboratively train a model without directly sharing their raw data.
    + improve privacy, compliance with regulation --> expands data sources
  - Security & privacy issues
    + Data privacy: training needs large datasets
    + Model protection: model is tempting target for tampering
    + Adversarial attacks: may try to tweak inputs, for training as well as applied data, to gain benefits