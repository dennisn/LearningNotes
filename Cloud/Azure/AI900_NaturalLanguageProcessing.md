# Microsoft Azure AI Fundamentals (AI-900): Natural Language Processing (NLP) Workloads on Azure
  - How to utilize NLP on Azure to analyze text, sentiment and make AI understand people

## Text Analytics

### NLP: 
  - Interpret spoken language & synthesize speech (speech-to-text and text-to-speech)
  - Analyze & interpret text
    + Sentiment determination
    + Key phrases --> summarize text
  - Translation between language
  - Language understanding: Interpret command and determine the appropriate actions to take
  - Available Azure services:
    + Azure AI Language Service: analyze & interpret text, also language understanding
    + Azure AI Translator: translate text
    + Azure Speech: recognize & synthesize speech, translate spoken language

### Text analytics
  - Text analytics: to get the content of the text
    + Language detection: return the main language of given text, language code & confidence score
      - Unable to identify language will be "Unknown" and confidence NaN
    + Sentiment analysis: rate from 0->1 if the text is positive, neutral or negative
    + Extract key phrases
    + Entity recognition: returns a list of entities, category & confidence score for each
      - Entity linking: if an entity is recognized, return a URL for its relevant Wikipedia article
    + PII detection: identify, categorize and redact PII (Personally Identifiable Information)
  - Text analytics process: Tokenization
    + Text normalization: remove punctuation & lower case
    + Stop word removal: excluding common words without much meaning (e.g. the, it, etc.)
    + N-grams (multi-term phrases) --> group words into phrases
    + Stemming: consolidate similar words (e.g. benefactor, benevolen and beneficial)
  - Azure AI Language vs Azure AI Services: single specialize service vs. multiple

## Speech Recognition
  - Speech recognition: taking spoken language and converting it into data or text (from live audio or audio file)
    + Using acoustic model to convert audio signal into something Azure can work with
  - Speech-to-Text API: live/batch transcription of audio into text
    + Pre-built models is optimized for conversation & dictation --> option for train our own model
  - Speech Synthesis: convert text into speech
    + Process: tokenize the text into words, then synthesize them with particular voice, tone, pitch, etc.
  - Azure AI Speech vs. Azure AI Service

## Translation
  - Text translation: translate documents/text from one language to another --> only focus on text-to-text translation
    + Can translate a document into multiple languages
    + Custom translation ?
  - Extra features:
    + Automatic language detection
    + Dictionaries --> alternative translations for things like fugures of speech/phrases
    + Profanity filters: automatically delete/mark profanity as texts are translated
  - Speech translation:
    + Can translate directly (i.e. speech-to-speech) --> translation during presentation
    + Translate spoken language into text (i.e. speech-to-text)
  - Azure AI translator (text-to-text only) vs. Azure AI Speech vs Azure AI service

## Language Understanding
  - Language understanding: 
    + Utterances: a phrase we way to the AI
    + Entities: an item the phrase refers to
    + Intent: purpose/goal of the phrase
      - Non-intent: fallback to provide a generic response if the application doesn't understand
  - Performance Metrics
    + Precision: percentage of correct "positive predictions" vs. all "positive predictions" made (e.g. true positives / true + false positive)
    + Recall: percentage of correct "positive predictions" vs. all "positive cases" (e.g. true positive / # of positive)
    + F1 Score: a balanced measure of a model's precision and recall: `F1 = 2 * (precision * recall) / (precision + recall)` 
  - Azure AI Language vs Azure AI Services
  - Knowledge base: used to return answer for questions
    + Database of question & answer pairs
    + Create from Azuire AI Language through REST API, SDK or the language studio
    + The knowledge base data: generate from FAQ, chit-chat data source or input manually
    + Using: deploy to be used with client application, or Azure AI bot service