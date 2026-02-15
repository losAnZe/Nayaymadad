# Requirements Document: NyayMadad Legal OS

## Introduction

NyayMadad (न्यायमदद - "Justice Help") is a voice-first, multilingual AI-powered Legal Operating System designed to empower Indian citizens with accessible, understandable, and actionable legal guidance. The system addresses the critical gap where 70%+ of Indian citizens interact with the legal system without fully understanding it, by providing voice-based legal assistance in 5 Indian languages (Hindi, English, Tamil, Telugu, Malayalam).

The application is a full-stack mobile solution built with React Native + Expo frontend and Python FastAPI backend, powered by Sarvam AI's sovereign Indian language models for speech-to-text, text-to-speech, and natural language understanding.

## Glossary

- **NyayMadad_App**: The mobile application (React Native + Expo) that users interact with
- **Backend_API**: The FastAPI Python server that processes requests and orchestrates AI services
- **Sarvam_STT**: Sarvam AI Saaras V3 speech-to-text service supporting 22 Indian languages
- **Sarvam_TTS**: Sarvam AI Bulbul V3 text-to-speech service with 30+ voices in 11 Indian languages
- **Sarvam_LLM**: Sarvam-M large language model for legal reasoning and response generation
- **Legal_Corpus**: Vector database (Qdrant) containing embeddings of Indian laws, acts, and legal documents
- **RAG_Pipeline**: Retrieval-Augmented Generation system that retrieves relevant legal context before generating responses
- **User_Profile**: Stored user information including language preference, location, and demographic data
- **Conversation_Cache**: Local MMKV storage of previous chat sessions for offline access
- **Legal_Notice**: Any official legal document (court notice, eviction notice, tax notice, etc.)
- **Contract**: Legal agreement document (employment contract, rental agreement, service agreement, etc.)
- **Compliance_Task**: Required business registration or tax filing obligation
- **Government_Scheme**: Welfare program offered by central or state government
- **Legal_Aid_Center**: Physical location providing free or subsidized legal assistance
- **Mitigation_Option**: Structured solution path (Option A, B, or C) for addressing a legal problem
- **Risk_Level**: Categorization of legal notice urgency (Low, Medium, High, Critical)
- **Deadline**: Time-sensitive date extracted from legal documents requiring action
- **Citation**: Reference to specific law, section, or legal precedent supporting an answer

## Requirements

### Requirement 1: User Authentication and Onboarding

**User Story:** As a new user, I want to authenticate using my phone number and complete a simple onboarding process, so that I can start using the app with personalized settings.

#### Acceptance Criteria

1. WHEN a user opens the app for the first time, THE NyayMadad_App SHALL display a language selection screen with 5 options (Hindi, English, Tamil, Telugu, Malayalam)
2. WHEN a user selects a language, THE NyayMadad_App SHALL persist the language preference and apply it to all UI elements
3. WHEN a user enters their phone number, THE Backend_API SHALL send an OTP via Firebase Auth
4. WHEN a user enters a valid OTP, THE Backend_API SHALL create or retrieve the User_Profile and return an authentication token
5. WHEN a user completes authentication, THE NyayMadad_App SHALL collect basic profile information (name, state, occupation category) through voice or text input
6. WHEN a user completes onboarding, THE NyayMadad_App SHALL navigate to the home screen and cache the User_Profile locally

### Requirement 2: Voice-First Chat Interface

**User Story:** As a user, I want to speak my legal questions in my preferred language and receive spoken answers, so that I can get legal guidance without typing or reading.

#### Acceptance Criteria

1. WHEN a user taps the microphone button, THE NyayMadad_App SHALL start recording audio and display a visual recording indicator
2. WHEN a user finishes speaking and releases the button, THE NyayMadad_App SHALL send the audio to Backend_API for processing
3. WHEN the Backend_API receives audio, THE Backend_API SHALL transcribe it using Sarvam_STT and return the text
4. WHEN transcription completes, THE NyayMadad_App SHALL display the transcribed text in a message bubble
5. WHEN the Backend_API processes a query, THE Backend_API SHALL generate a response using Sarvam_LLM with RAG_Pipeline context from Legal_Corpus
6. WHEN a response is generated, THE Backend_API SHALL convert it to speech using Sarvam_TTS in the user's preferred language
7. WHEN the NyayMadad_App receives the response, THE NyayMadad_App SHALL display the text and automatically play the audio
8. WHEN a user taps a message bubble, THE NyayMadad_App SHALL replay the audio for that message
9. WHEN the app is offline, THE NyayMadad_App SHALL display a clear "No Internet" indicator and disable voice input
10. WHEN the app is offline, THE NyayMadad_App SHALL allow users to view cached conversations from Conversation_Cache

### Requirement 3: Legal Literacy Engine

**User Story:** As a user, I want to ask questions about laws, rights, and legal procedures in simple language, so that I can understand my legal situation without consulting a lawyer.

#### Acceptance Criteria

1. WHEN a user asks a legal question, THE Backend_API SHALL retrieve relevant context from Legal_Corpus using semantic search
2. WHEN generating a response, THE Backend_API SHALL use Sarvam_LLM with a legal assistant system prompt that emphasizes simple language
3. WHEN a response references specific laws, THE Backend_API SHALL include Citation information with act name, section number, and brief description
4. WHEN a response is generated, THE Backend_API SHALL ensure it avoids legal jargon and uses examples relevant to Indian context
5. WHEN a user asks a follow-up question, THE Backend_API SHALL maintain conversation context for up to 10 previous messages
6. WHEN a response cannot be confidently generated, THE Backend_API SHALL acknowledge uncertainty and suggest consulting a legal professional
7. WHEN a response is delivered, THE NyayMadad_App SHALL display citations as tappable chips that show full legal text when selected

### Requirement 4: Legal Notice Interpreter

**User Story:** As a user, I want to upload or photograph a legal notice and get an AI analysis with summary, deadlines, and suggested actions, so that I can understand what the notice means and what I need to do.

#### Acceptance Criteria

1. WHEN a user taps "Analyze Notice", THE NyayMadad_App SHALL present options to capture photo or select from gallery
2. WHEN a user captures or selects an image, THE NyayMadad_App SHALL upload it to Backend_API
3. WHEN the Backend_API receives a document image, THE Backend_API SHALL extract text using Google Cloud Vision OCR
4. WHEN OCR completes, THE Backend_API SHALL analyze the extracted text using Sarvam_LLM to identify notice type, issuing authority, and key information
5. WHEN analysis completes, THE Backend_API SHALL extract all Deadline dates and calculate days remaining
6. WHEN analysis completes, THE Backend_API SHALL assign a Risk_Level based on deadline urgency and notice severity
7. WHEN analysis completes, THE Backend_API SHALL generate a plain-language summary in the user's preferred language
8. WHEN analysis completes, THE Backend_API SHALL generate 3 Mitigation_Option paths (Option A, B, C) with pros, cons, and estimated costs
9. WHEN the NyayMadad_App receives analysis results, THE NyayMadad_App SHALL display them in a structured card format with risk indicator, deadlines, summary, and options
10. WHEN analysis results are displayed, THE NyayMadad_App SHALL cache them locally for offline viewing
11. WHEN the app is offline, THE NyayMadad_App SHALL prevent document upload and display "Internet required for analysis" message

### Requirement 5: Missing Clause Detector

**User Story:** As a user, I want to upload a contract and detect risky or missing clauses, so that I can understand potential problems before signing.

#### Acceptance Criteria

1. WHEN a user uploads a Contract document, THE Backend_API SHALL extract text using Google Cloud Vision OCR
2. WHEN OCR completes, THE Backend_API SHALL analyze the contract using Sarvam_LLM with a contract analysis system prompt
3. WHEN analyzing a contract, THE Backend_API SHALL identify the contract type (employment, rental, service, etc.)
4. WHEN analyzing a contract, THE Backend_API SHALL check for presence of standard protective clauses based on contract type
5. WHEN analysis completes, THE Backend_API SHALL generate a list of missing clauses with explanations of why each is important
6. WHEN analysis completes, THE Backend_API SHALL identify risky clauses that favor one party unfairly
7. WHEN analysis completes, THE Backend_API SHALL provide a risk assessment score (0-100) for the overall contract
8. WHEN the NyayMadad_App receives contract analysis, THE NyayMadad_App SHALL display results as a checklist with green (present), yellow (unclear), and red (missing/risky) indicators
9. WHEN a user taps a clause item, THE NyayMadad_App SHALL show detailed explanation and suggested alternative language

### Requirement 6: What-If Scenario Simulator

**User Story:** As a user, I want to simulate different legal decision outcomes, so that I can understand the consequences before taking action.

#### Acceptance Criteria

1. WHEN a user describes a legal situation, THE NyayMadad_App SHALL offer to run a scenario simulation
2. WHEN a user accepts simulation, THE Backend_API SHALL use Sarvam_LLM to generate 2-3 decision paths based on the situation
3. WHEN generating scenarios, THE Backend_API SHALL simulate outcomes for each path including timeline, costs, and likelihood of success
4. WHEN scenarios are generated, THE Backend_API SHALL include relevant legal precedents and typical outcomes from Legal_Corpus
5. WHEN the NyayMadad_App receives scenarios, THE NyayMadad_App SHALL display them as a decision tree with expandable outcome nodes
6. WHEN a user taps an outcome node, THE NyayMadad_App SHALL show detailed explanation with supporting legal context

### Requirement 7: Business Compliance Navigator

**User Story:** As a small business owner, I want step-by-step guidance on required registrations and tax filings, so that I can stay compliant without hiring expensive consultants.

#### Acceptance Criteria

1. WHEN a user selects "Business Compliance", THE NyayMadad_App SHALL collect business type, state, and annual revenue through a guided form
2. WHEN business information is submitted, THE Backend_API SHALL identify all applicable Compliance_Task items (GST, MSME, Shops Act, etc.)
3. WHEN compliance tasks are identified, THE Backend_API SHALL generate a prioritized checklist with deadlines and estimated costs
4. WHEN a user selects a Compliance_Task, THE Backend_API SHALL provide step-by-step instructions with required documents and online portal links
5. WHEN the NyayMadad_App displays compliance tasks, THE NyayMadad_App SHALL show completion status and send reminders for upcoming deadlines
6. WHEN a user marks a task complete, THE NyayMadad_App SHALL update the checklist and cache the status locally

### Requirement 8: Government Scheme Finder

**User Story:** As a citizen, I want to discover government welfare schemes I'm eligible for, so that I can access benefits I didn't know existed.

#### Acceptance Criteria

1. WHEN a user selects "Find Schemes", THE Backend_API SHALL match User_Profile data against a database of Government_Scheme eligibility criteria
2. WHEN matching schemes, THE Backend_API SHALL consider age, gender, income, occupation, state, and caste/category
3. WHEN matches are found, THE Backend_API SHALL rank schemes by relevance and benefit amount
4. WHEN the NyayMadad_App displays schemes, THE NyayMadad_App SHALL show scheme name, benefit description, eligibility criteria, and application process
5. WHEN a user taps a scheme, THE NyayMadad_App SHALL show detailed information including required documents and application deadlines
6. WHEN a user asks questions about a scheme, THE Backend_API SHALL answer using Sarvam_LLM with scheme-specific context

### Requirement 9: Geo-Based Legal Aid Connector

**User Story:** As a user, I want to find nearby lawyers and legal aid centers on a map, so that I can get in-person help when needed.

#### Acceptance Criteria

1. WHEN a user selects "Find Legal Aid", THE NyayMadad_App SHALL request location permission
2. WHEN location permission is granted, THE NyayMadad_App SHALL retrieve the user's current coordinates
3. WHEN coordinates are obtained, THE Backend_API SHALL query a database of Legal_Aid_Center locations within 50km radius
4. WHEN legal aid centers are found, THE NyayMadad_App SHALL display them on a map using react-native-maps with Google Maps
5. WHEN a user taps a map marker, THE NyayMadad_App SHALL show center details including name, address, phone number, services offered, and operating hours
6. WHEN a user taps "Get Directions", THE NyayMadad_App SHALL open Google Maps with navigation to the selected center
7. WHEN location permission is denied, THE NyayMadad_App SHALL allow manual city/district selection for legal aid search

### Requirement 10: Multilingual Translation

**User Story:** As a user, I want all app content automatically translated to my preferred language, so that I can use the app comfortably in my native language.

#### Acceptance Criteria

1. WHEN a user changes language in settings, THE NyayMadad_App SHALL update all UI text using react-i18next translation keys
2. WHEN the Backend_API generates responses, THE Backend_API SHALL use Sarvam_LLM to generate content directly in the user's preferred language
3. WHEN legal documents contain English text and user prefers another language, THE Backend_API SHALL use Sarvam_Translate to translate summaries and explanations
4. WHEN displaying citations, THE NyayMadad_App SHALL show law names in both English and translated language
5. WHEN the user's preferred language is not supported for a specific feature, THE NyayMadad_App SHALL fall back to English with a notification

### Requirement 11: Smart Caching and Offline Access

**User Story:** As a user with intermittent internet connectivity, I want to access my previous conversations and analysis results offline, so that I can review information without needing constant internet access.

#### Acceptance Criteria

1. WHEN a conversation completes, THE NyayMadad_App SHALL save the full conversation to Conversation_Cache using MMKV
2. WHEN a document analysis completes, THE NyayMadad_App SHALL save the analysis results and original image to local storage
3. WHEN the app is offline, THE NyayMadad_App SHALL display all cached conversations and analyses with an "Offline" badge
4. WHEN the app is offline and user attempts to use AI features, THE NyayMadad_App SHALL display a clear "Internet required" message with explanation
5. WHEN the app regains connectivity, THE NyayMadad_App SHALL sync any pending analytics or usage data to Backend_API
6. WHEN cached data exceeds 100MB, THE NyayMadad_App SHALL prompt user to clear old conversations while preserving recent ones

### Requirement 12: Performance and Accessibility

**User Story:** As a user with a low-end device and slow internet, I want the app to work smoothly on 2G networks, so that I can access legal help despite connectivity constraints.

#### Acceptance Criteria

1. WHEN the app launches, THE NyayMadad_App SHALL display the home screen within 3 seconds on devices with 2GB RAM
2. WHEN uploading images, THE NyayMadad_App SHALL compress images to under 1MB before sending to Backend_API
3. WHEN streaming audio responses, THE Backend_API SHALL use adaptive bitrate to optimize for network speed
4. WHEN network speed is below 100kbps, THE NyayMadad_App SHALL display a warning and offer text-only mode
5. WHEN the Backend_API processes requests, THE Backend_API SHALL return responses within 10 seconds for 95% of queries
6. WHEN the app displays text, THE NyayMadad_App SHALL use minimum 16sp font size for readability
7. WHEN the app uses colors, THE NyayMadad_App SHALL maintain WCAG AA contrast ratios for accessibility

### Requirement 13: Data Privacy and Security

**User Story:** As a user sharing sensitive legal information, I want my data to be secure and private, so that I can trust the app with confidential matters.

#### Acceptance Criteria

1. WHEN a user authenticates, THE Backend_API SHALL use Firebase Auth with secure token-based authentication
2. WHEN transmitting data, THE Backend_API SHALL enforce HTTPS/TLS 1.3 for all API communications
3. WHEN storing user data, THE Backend_API SHALL encrypt sensitive fields (phone number, documents) at rest in PostgreSQL
4. WHEN a user uploads documents, THE Backend_API SHALL store them in Google Cloud Storage with private access controls
5. WHEN processing conversations, THE Backend_API SHALL not log or store personally identifiable information in application logs
6. WHEN a user deletes their account, THE Backend_API SHALL permanently delete all associated data within 30 days
7. WHEN the app caches data locally, THE NyayMadad_App SHALL use MMKV encryption for sensitive cached content

### Requirement 14: Error Handling and Guardrails

**User Story:** As a user, I want the app to handle errors gracefully and prevent me from receiving harmful legal advice, so that I can trust the information provided.

#### Acceptance Criteria

1. WHEN Sarvam_STT fails to transcribe audio, THE NyayMadad_App SHALL display an error message and offer to retry
2. WHEN the Backend_API cannot generate a confident response, THE Backend_API SHALL acknowledge limitations and suggest consulting a legal professional
3. WHEN a user asks about criminal law or court representation, THE Backend_API SHALL include a disclaimer that NyayMadad does not replace lawyers
4. WHEN OCR extraction quality is low (confidence < 70%), THE Backend_API SHALL warn the user and request a clearer image
5. WHEN the Backend_API detects potentially harmful advice being generated, THE Backend_API SHALL apply guardrails to filter or rephrase the response
6. WHEN API rate limits are exceeded, THE Backend_API SHALL return a 429 status with retry-after header
7. WHEN the Backend_API encounters an internal error, THE Backend_API SHALL log the error to Sentry and return a user-friendly error message

### Requirement 15: Analytics and Monitoring

**User Story:** As a product team, we want to track app usage and performance metrics, so that we can improve the product and measure success.

#### Acceptance Criteria

1. WHEN a user completes onboarding, THE NyayMadad_App SHALL send an analytics event to Backend_API with language and state
2. WHEN a user sends a voice query, THE Backend_API SHALL log query type, language, and response time
3. WHEN a document analysis completes, THE Backend_API SHALL log document type, OCR confidence, and analysis success rate
4. WHEN errors occur, THE Backend_API SHALL send error details to Sentry with context and stack trace
5. WHEN the Backend_API processes requests, THE Backend_API SHALL track API endpoint latency and throughput in Redis
6. WHEN analytics data is collected, THE Backend_API SHALL anonymize personally identifiable information before storage
7. WHEN the product team queries metrics, THE Backend_API SHALL provide aggregated statistics without exposing individual user data
