# Implementation Plan: NyayMadad Legal OS

## Overview

This implementation plan breaks down the NyayMadad Legal Operating System into discrete, incremental coding tasks. The plan follows a bottom-up approach, starting with foundational infrastructure, then building core services, and finally integrating all components. Each task builds on previous work, ensuring no orphaned code.

The implementation covers both the React Native mobile frontend and Python FastAPI backend, with emphasis on the voice-first, multilingual AI capabilities powered by Sarvam AI.

## Tasks

- [ ] 1. Set up project infrastructure and development environment
  - Create monorepo structure with `mobile/` and `backend/` directories
  - Initialize React Native + Expo project with TypeScript
  - Initialize FastAPI project with Python 3.12+
  - Set up Docker Compose for local development (PostgreSQL, Redis, Qdrant)
  - Configure environment variables and secrets management
  - Set up linting and formatting (ESLint, Prettier, Black, isort)
  - Create initial CI/CD pipeline configuration (GitHub Actions)
  - _Requirements: Infrastructure setup for all features_

- [ ] 2. Implement backend core infrastructure
  - [ ] 2.1 Set up FastAPI application structure
    - Create main.py with FastAPI app initialization
    - Configure CORS, middleware, and exception handlers
    - Set up Pydantic settings management (app/config.py)
    - Create health check endpoint
    - _Requirements: 12.5, 14.6, 14.7_

  - [ ] 2.2 Set up database connections and models
    - Configure PostgreSQL connection with SQLAlchemy
    - Create Alembic migration setup
    - Implement base database models (users, conversations, messages, documents)
    - Create database session management and dependency injection
    - _Requirements: 1.4, 13.3_

  - [ ] 2.3 Set up Redis caching layer
    - Configure Redis connection
    - Implement caching utilities for API responses
    - Set up rate limiting with Redis
    - _Requirements: 14.6, 15.5_

  - [ ] 2.4 Set up Qdrant vector database
    - Configure Qdrant client connection
    - Create legal_corpus collection with schema
    - Implement embedding generation utilities
    - _Requirements: 2.5, 3.1_

  - [ ]* 2.5 Write unit tests for database operations
    - Test database connection and session management
    - Test model CRUD operations
    - Test Redis caching and rate limiting
    - _Requirements: 2.1, 13.3, 14.6_

- [ ] 3. Implement authentication system
  - [ ] 3.1 Set up Firebase Auth integration
    - Configure Firebase Admin SDK in backend
    - Implement phone OTP sending endpoint
    - Implement OTP verification endpoint
    - Create JWT token generation and validation
    - _Requirements: 1.3, 1.4, 13.1_

  - [ ] 3.2 Create user profile management
    - Implement user creation and retrieval service
    - Create user profile update endpoint
    - Implement user deletion with data cleanup
    - _Requirements: 1.4, 1.5, 13.6_

  - [ ]* 3.3 Write property test for authentication token generation
    - **Property 2: Authentication Token Generation**
    - **Validates: Requirements 1.4**

  - [ ]* 3.4 Write unit tests for auth flows
    - Test OTP sending and verification
    - Test token generation and validation
    - Test user profile CRUD operations
    - _Requirements: 1.3, 1.4, 13.1_

- [ ] 4. Implement AI service integrations
  - [ ] 4.1 Set up Sarvam AI client
    - Install and configure sarvam-ai SDK
    - Implement LLM client wrapper (app/ai/llm.py)
    - Implement STT client wrapper for Saaras V3
    - Implement TTS client wrapper for Bulbul V3
    - Implement translation client wrapper for Sarvam-Translate
    - Add error handling and retry logic
    - _Requirements: 2.3, 2.6, 10.2, 10.3_

  - [ ] 4.2 Set up Google Cloud Vision OCR
    - Configure Google Cloud Vision API client
    - Implement OCR text extraction service
    - Add confidence score handling
    - _Requirements: 4.3, 5.1, 14.4_

  - [ ] 4.3 Set up Google Cloud Storage
    - Configure GCS client for document storage
    - Implement file upload service with private access controls
    - Implement file retrieval service
    - _Requirements: 4.2, 13.4_

  - [ ]* 4.4 Write property test for STT transcription completeness
    - **Property 5: STT Transcription Completeness**
    - **Validates: Requirements 2.3**

  - [ ]* 4.5 Write property test for TTS audio generation
    - **Property 8: TTS Audio Generation**
    - **Validates: Requirements 2.6**

  - [ ]* 4.6 Write property test for OCR text extraction
    - **Property 17: OCR Text Extraction**
    - **Validates: Requirements 4.3, 5.1**

  - [ ]* 4.7 Write unit tests for AI service integrations
    - Test Sarvam AI client methods with mocked responses
    - Test Google Cloud Vision OCR with sample images
    - Test GCS upload and retrieval
    - Test error handling for service failures
    - _Requirements: 2.3, 2.6, 4.3, 14.1_

- [ ] 5. Implement RAG pipeline for legal corpus
  - [ ] 5.1 Create RAG pipeline service
    - Implement semantic search over Qdrant
    - Implement context retrieval with top-k results
    - Create prompt templates for legal assistant
    - Integrate RAG with LLM client
    - _Requirements: 2.5, 3.1_

  - [ ] 5.2 Create legal assistant system prompts
    - Implement LEGAL_ASSISTANT_SYSTEM_PROMPT
    - Implement NOTICE_ANALYZER_PROMPT
    - Implement CONTRACT_ANALYZER_PROMPT
    - Implement SCENARIO_SIMULATOR_PROMPT
    - _Requirements: 3.2, 4.4, 5.2, 6.2_

  - [ ] 5.3 Implement guardrails and safety filters
    - Create SafetyGuardrails class
    - Implement query safety checks
    - Implement response safety checks
    - Add disclaimer injection for sensitive topics
    - _Requirements: 14.2, 14.3, 14.5_

  - [ ]* 5.4 Write property test for RAG context retrieval
    - **Property 7: RAG Context Retrieval**
    - **Validates: Requirements 2.5, 3.1**

  - [ ]* 5.5 Write property test for guardrail activation
    - **Property 56: Guardrail Activation**
    - **Validates: Requirements 14.5**

  - [ ]* 5.6 Write unit tests for RAG pipeline
    - Test semantic search with sample queries
    - Test prompt template rendering
    - Test guardrail checks with various inputs
    - _Requirements: 2.5, 3.1, 14.5_

- [ ] 6. Checkpoint - Ensure backend core services are functional
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 7. Implement chat service and API endpoints
  - [ ] 7.1 Create chat service layer
    - Implement ChatService class with voice query processing
    - Implement text query processing
    - Implement conversation context management (10 message window)
    - Implement citation extraction from LLM responses
    - Add confidence-based disclaimer injection
    - _Requirements: 2.2, 2.3, 2.5, 2.6, 3.3, 3.5, 3.6_

  - [ ] 7.2 Create chat API endpoints
    - Implement POST /api/v1/chat/voice endpoint
    - Implement POST /api/v1/chat/text endpoint
    - Implement GET /api/v1/chat/conversations endpoint
    - Add request validation with Pydantic models
    - _Requirements: 2.2, 2.3, 2.5_

  - [ ]* 7.3 Write property test for conversation context maintenance
    - **Property 13: Conversation Context Maintenance**
    - **Validates: Requirements 3.5**

  - [ ]* 7.4 Write property test for citation inclusion
    - **Property 12: Citation Inclusion for Legal References**
    - **Validates: Requirements 3.3**

  - [ ]* 7.5 Write property test for uncertainty acknowledgment
    - **Property 14: Uncertainty Acknowledgment**
    - **Validates: Requirements 3.6**

  - [ ]* 7.6 Write unit tests for chat service
    - Test voice query end-to-end flow
    - Test text query processing
    - Test conversation context window
    - Test citation extraction
    - Test error handling for AI service failures
    - _Requirements: 2.2, 2.3, 2.5, 3.3, 3.5_

- [ ] 8. Implement document analysis service and API endpoints
  - [ ] 8.1 Create document analysis service
    - Implement DocumentService class
    - Implement legal notice analysis with OCR + LLM
    - Implement deadline extraction and calculation
    - Implement risk level assignment logic
    - Implement mitigation option generation (3 options: A, B, C)
    - Add OCR confidence warning for low quality
    - _Requirements: 4.3, 4.4, 4.5, 4.6, 4.7, 4.8, 14.4_

  - [ ] 8.2 Create contract analysis service
    - Implement contract type identification
    - Implement missing clause detection
    - Implement risky clause identification
    - Implement risk score calculation (0-100)
    - _Requirements: 5.2, 5.3, 5.4, 5.5, 5.6, 5.7_

  - [ ] 8.3 Create document API endpoints
    - Implement POST /api/v1/documents/analyze endpoint
    - Implement POST /api/v1/documents/analyze-contract endpoint
    - Implement GET /api/v1/documents/{document_id} endpoint
    - Add file upload validation and size limits
    - _Requirements: 4.2, 4.3, 5.1_

  - [ ]* 8.4 Write property test for document analysis field completeness
    - **Property 18: Document Analysis Field Completeness**
    - **Validates: Requirements 4.4, 4.5, 4.6, 4.7, 4.8**

  - [ ]* 8.5 Write property test for deadline calculation accuracy
    - **Property 19: Deadline Calculation Accuracy**
    - **Validates: Requirements 4.5**

  - [ ]* 8.6 Write property test for mitigation options count
    - **Property 20: Mitigation Options Count**
    - **Validates: Requirements 4.8**

  - [ ]* 8.7 Write property test for contract risk score range
    - **Property 23: Contract Risk Score Range**
    - **Validates: Requirements 5.7**

  - [ ]* 8.8 Write property test for OCR quality warning
    - **Property 55: OCR Quality Warning**
    - **Validates: Requirements 14.4**

  - [ ]* 8.9 Write unit tests for document analysis
    - Test legal notice analysis with sample documents
    - Test contract analysis with sample contracts
    - Test deadline extraction edge cases
    - Test risk level assignment
    - Test OCR confidence handling
    - _Requirements: 4.3, 4.4, 4.5, 5.2, 5.7, 14.4_

- [ ] 9. Implement scenario simulator and compliance navigator
  - [ ] 9.1 Create scenario simulation service
    - Implement scenario generation (2-3 decision paths)
    - Implement outcome simulation with timeline, costs, likelihood
    - Integrate legal precedents from RAG
    - _Requirements: 6.2, 6.3, 6.4_

  - [ ] 9.2 Create compliance navigator service
    - Implement business profile analysis
    - Implement compliance task identification
    - Implement task prioritization logic
    - Create task completion tracking
    - _Requirements: 7.2, 7.3, 7.4, 7.6_

  - [ ] 9.3 Create compliance API endpoints
    - Implement POST /api/v1/compliance/analyze endpoint
    - Implement PUT /api/v1/compliance/tasks/{task_id} endpoint
    - _Requirements: 7.2, 7.6_

  - [ ]* 9.4 Write property test for scenario generation count
    - **Property 25: Scenario Generation Count**
    - **Validates: Requirements 6.2**

  - [ ]* 9.5 Write property test for compliance task prioritization
    - **Property 28: Compliance Task Prioritization**
    - **Validates: Requirements 7.3**

  - [ ]* 9.6 Write unit tests for scenario and compliance services
    - Test scenario generation with various situations
    - Test compliance task identification
    - Test task prioritization
    - _Requirements: 6.2, 6.3, 7.2, 7.3_

- [ ] 10. Implement scheme finder and legal aid locator
  - [ ] 10.1 Create scheme matching service
    - Implement eligibility criteria matching logic
    - Implement relevance score calculation
    - Implement scheme ranking algorithm
    - _Requirements: 8.1, 8.2, 8.3_

  - [ ] 10.2 Create legal aid locator service
    - Implement geospatial query for nearby centers (50km radius)
    - Implement distance calculation
    - _Requirements: 9.3_

  - [ ] 10.3 Create schemes and legal aid API endpoints
    - Implement GET /api/v1/schemes/match endpoint
    - Implement GET /api/v1/schemes/{scheme_id} endpoint
    - Implement GET /api/v1/legal-aid/nearby endpoint
    - _Requirements: 8.1, 9.3_

  - [ ] 10.4 Populate database with seed data
    - Load government schemes data into PostgreSQL
    - Load legal aid centers data with coordinates
    - Create database indexes for geospatial queries
    - _Requirements: 8.1, 9.3_

  - [ ]* 10.5 Write property test for scheme matching criteria
    - **Property 30: Scheme Matching Criteria**
    - **Validates: Requirements 8.1, 8.2**

  - [ ]* 10.6 Write property test for geospatial query radius
    - **Property 33: Geospatial Query Radius**
    - **Validates: Requirements 9.3**

  - [ ]* 10.7 Write unit tests for scheme and legal aid services
    - Test scheme matching with various profiles
    - Test scheme ranking
    - Test geospatial queries
    - _Requirements: 8.1, 8.2, 9.3_

- [ ] 11. Checkpoint - Ensure all backend services are complete
  - Ensure all tests pass, ask the user if questions arise.
