# Implementation Plan

- [x] 1. Set up project structure and dependencies





  - Create project directory structure (src, tests, public)
  - Initialize package.json with required dependencies
  - Set up Vite build configuration
  - Install core dependencies: fast-check for property testing, Jest for unit testing
  - Create basic HTML template with semantic structure
  - _Requirements: 3.1, 3.4_

- [ ] 2. Implement core data models and validation
  - [x] 2.1 Create TypeScript interfaces for EmailParams, GeneratedEmail, ValidationResult


    - Define all type definitions in types.ts
    - Include proper JSDoc comments
    - _Requirements: 1.1, 2.1_

  - [x] 2.2 Implement input validation logic


    - Create validateInput function to check main content
    - Validate main content is not empty or whitespace-only
    - Parse and validate keywords from comma or space-separated input
    - _Requirements: 1.2, 1.4, 2.4_

  - [ ]* 2.3 Write property test for input validation
    - **Property 1: Input acceptance and storage**
    - **Validates: Requirements 1.1**

  - [ ]* 2.4 Write property test for keyword parsing
    - **Property 3: Keyword format parsing**
    - **Validates: Requirements 2.4**

  - [ ]* 2.5 Write unit tests for edge cases
    - Test empty string rejection
    - Test whitespace-only string rejection
    - Test special characters handling
    - _Requirements: 1.2, 1.4_

- [ ] 3. Build AI integration service
  - [x] 3.1 Create AIService class with prompt construction


    - Implement constructPrompt method that builds prompts from EmailParams
    - Include tone, length, recipient context, and keywords in prompt
    - Format prompt for structured output (subject + body)
    - _Requirements: 1.3, 2.1, 7.2, 8.1, 9.2, 9.3, 9.4, 11.2_

  - [x] 3.2 Implement API communication with OpenAI/Anthropic

    - Create callAI method with error handling
    - Implement timeout and retry logic
    - Handle rate limiting and API errors
    - _Requirements: 12.4_

  - [x] 3.3 Implement response parsing

    - Parse AI response to extract subject and body
    - Validate response structure
    - Handle malformed responses
    - _Requirements: 4.1, 8.1, 8.2_

  - [ ]* 3.4 Write property test for subject line generation
    - **Property 10: Subject line generation**
    - **Validates: Requirements 8.1**

  - [ ]* 3.5 Write property test for email structure
    - **Property 4: Email structure completeness**
    - **Validates: Requirements 4.1**

  - [ ]* 3.6 Write unit tests for AI service
    - Test prompt construction with various parameters
    - Test response parsing with mock responses
    - Test error handling for API failures
    - _Requirements: 7.2, 8.1, 12.4_

- [ ] 4. Implement email generator service
  - [x] 4.1 Create EmailGeneratorService class


    - Implement generateEmail method that orchestrates validation and AI calls
    - Integrate validation and AI service
    - Add timestamp to generated emails
    - Generate unique IDs for each email
    - _Requirements: 1.1, 1.3, 4.1_

  - [x] 4.2 Implement keyword incorporation verification

    - Add logic to verify keywords appear in generated content
    - Handle cases where AI doesn't include keywords
    - _Requirements: 2.1, 2.3_

  - [ ]* 4.3 Write property test for keyword incorporation
    - **Property 2: Keyword incorporation**
    - **Validates: Requirements 2.1, 2.3**

  - [ ]* 4.4 Write property test for tone parameter propagation
    - **Property 9: Tone parameter propagation**
    - **Validates: Requirements 7.4**

  - [ ]* 4.5 Write property test for length constraints
    - **Property 11: Length constraints**
    - **Validates: Requirements 9.2, 9.3, 9.4**

- [ ] 5. Build history management service
  - [x] 5.1 Create HistoryService class with localStorage integration


    - Implement addToHistory method
    - Implement getHistory method with size limit (10 items)
    - Implement getEmailById method
    - Implement clearHistory method
    - _Requirements: 10.1, 10.2, 10.3_

  - [x] 5.2 Implement history size limiting and ordering

    - Keep only 10 most recent emails
    - Order by timestamp descending
    - Handle localStorage quota errors
    - _Requirements: 10.2_

  - [ ]* 5.3 Write property test for history storage
    - **Property 12: History storage**
    - **Validates: Requirements 10.1**

  - [ ]* 5.4 Write property test for history size limit
    - **Property 13: History size limit**
    - **Validates: Requirements 10.2**

  - [ ]* 5.5 Write property test for history retrieval
    - **Property 14: History retrieval accuracy**
    - **Validates: Requirements 10.3**

  - [ ]* 5.6 Write unit tests for history service
    - Test adding single email
    - Test history at exactly 10 items
    - Test history overflow (11+ items)
    - Test retrieval of non-existent ID
    - _Requirements: 10.1, 10.2, 10.3_

- [ ] 6. Implement clipboard service
  - [x] 6.1 Create ClipboardService class


    - Implement copyToClipboard method using Clipboard API
    - Handle clipboard permission errors
    - Implement copyEmail method for full email with/without subject
    - _Requirements: 5.2, 8.4_

  - [ ]* 6.2 Write property test for clipboard copy accuracy
    - **Property 5: Clipboard copy accuracy**
    - **Validates: Requirements 5.2**

  - [ ]* 6.3 Write property test for history clipboard copy
    - **Property 15: History clipboard copy**
    - **Validates: Requirements 10.4**

  - [ ]* 6.4 Write unit tests for clipboard service
    - Test successful copy operation
    - Test clipboard permission denied
    - Test copy with and without subject
    - _Requirements: 5.2, 5.3, 8.4_

- [ ] 7. Build UI input form component
  - [x] 7.1 Create HTML structure for input form

    - Add textarea for main content with label
    - Add text input for keywords with label
    - Add dropdown for tone selection with label
    - Add dropdown for length selection with label
    - Add dropdown for recipient context with label
    - Add generate button
    - Add reset button
    - _Requirements: 3.1, 3.2, 3.4, 7.1, 9.1, 11.1_

  - [x] 7.2 Implement form state management

    - Capture all input values
    - Handle form submission
    - Implement reset functionality
    - Provide loading state during generation
    - _Requirements: 1.1, 6.1, 6.2_

  - [x] 7.3 Add form validation and error display

    - Show validation errors inline
    - Highlight problematic fields
    - Preserve input on error
    - _Requirements: 1.2, 12.2, 12.3_

  - [ ]* 7.4 Write property test for reset functionality
    - **Property 6: Reset clears all fields**
    - **Validates: Requirements 6.2**

  - [ ]* 7.5 Write unit tests for form component
    - Test all required fields are present
    - Test generate button exists
    - Test reset clears all inputs
    - Test error display for empty content
    - _Requirements: 3.1, 3.2, 6.2, 12.2_

- [ ] 8. Build output display component
  - [x] 8.1 Create HTML structure for output display

    - Add section for subject line display
    - Add section for email body display
    - Add copy button for full email
    - Add copy button for subject only
    - Add copy button for body only
    - _Requirements: 4.4, 5.1, 8.2, 8.4_

  - [x] 8.2 Implement output rendering

    - Display subject line separately
    - Display email body with formatting preserved
    - Show/hide output based on generation state
    - _Requirements: 4.1, 4.4, 8.2_

  - [x] 8.3 Wire up copy functionality

    - Connect copy buttons to ClipboardService
    - Show visual confirmation on successful copy
    - Handle copy errors gracefully
    - _Requirements: 5.2, 5.3, 8.4_

  - [ ]* 8.4 Write property test for output replacement
    - **Property 8: Output replacement**
    - **Validates: Requirements 6.4**

  - [ ]* 8.5 Write unit tests for output component
    - Test subject displays separately from body
    - Test copy buttons are present
    - Test output updates on new generation
    - _Requirements: 5.1, 8.2, 8.4_

- [ ] 9. Build history panel component
  - [x] 9.1 Create HTML structure for history panel

    - Add container for history list
    - Add history item template with timestamp and preview
    - Add empty state message
    - _Requirements: 10.2_

  - [x] 9.2 Implement history rendering

    - Display up to 10 most recent emails
    - Show timestamp and preview for each
    - Order by most recent first
    - _Requirements: 10.2_

  - [x] 9.3 Implement history item selection

    - Handle click on history item
    - Load selected email into output display
    - Highlight selected item
    - _Requirements: 10.3_

  - [x] 9.4 Add copy from history functionality

    - Add copy button to each history item
    - Connect to ClipboardService
    - Show confirmation on copy
    - _Requirements: 10.4_

  - [ ]* 9.5 Write unit tests for history panel
    - Test history items render with timestamps
    - Test clicking item loads email
    - Test copy from history works
    - Test empty state displays when no history
    - _Requirements: 10.2, 10.3, 10.4_

- [ ] 10. Implement error handling and display
  - [x] 10.1 Create ErrorDisplay component

    - Add container for error messages
    - Style for visibility and clarity
    - Add dismiss functionality
    - _Requirements: 12.1_

  - [x] 10.2 Implement error handling for all services

    - Catch and format validation errors
    - Catch and format AI service errors
    - Catch and format clipboard errors
    - Catch and format localStorage errors
    - _Requirements: 12.1, 12.2, 12.4_

  - [ ]* 10.3 Write property test for error message display
    - **Property 16: Error message display**
    - **Validates: Requirements 12.1**

  - [ ]* 10.4 Write property test for field-specific errors
    - **Property 17: Field-specific validation errors**
    - **Validates: Requirements 12.2**

  - [ ]* 10.5 Write property test for input preservation on error
    - **Property 18: Input preservation on error**
    - **Validates: Requirements 12.3**

  - [ ]* 10.6 Write property test for service error handling
    - **Property 19: Service unavailability handling**
    - **Validates: Requirements 12.4**

  - [ ]* 10.7 Write unit tests for error handling
    - Test error display for empty input
    - Test error display for API failure
    - Test error display for clipboard failure
    - Test input preservation after error
    - _Requirements: 12.1, 12.2, 12.3, 12.4_

- [ ] 11. Integrate all components and wire up application
  - [x] 11.1 Create main application controller

    - Initialize all services
    - Connect form to EmailGeneratorService
    - Connect output to display component
    - Connect history panel to HistoryService
    - Wire up all event handlers
    - _Requirements: 6.1, 6.3_

  - [x] 11.2 Implement regeneration with modified inputs

    - Allow input modification after generation
    - Trigger new generation with updated params
    - Update output and history
    - _Requirements: 6.3_

  - [ ]* 11.3 Write property test for regeneration
    - **Property 7: Regeneration with modified inputs**
    - **Validates: Requirements 6.3**

  - [ ]* 11.4 Write integration tests
    - Test full flow: input → generate → display → copy
    - Test full flow: generate → modify → regenerate
    - Test full flow: generate → view history → copy from history
    - Test error recovery flow
    - _Requirements: 1.1, 1.3, 4.1, 5.2, 6.3, 10.3, 10.4_

- [ ] 12. Add styling and polish
  - [x] 12.1 Create CSS for clean, professional interface

    - Style form inputs with clear labels
    - Style buttons with appropriate hierarchy
    - Style output display for readability
    - Style history panel
    - Style error messages
    - Add loading states and animations
    - _Requirements: 3.1, 3.3, 4.4_

  - [x] 12.2 Implement responsive design

    - Ensure mobile compatibility
    - Test on different screen sizes
    - Adjust layout for small screens
    - _Requirements: 3.4_

  - [x] 12.3 Add accessibility features

    - Add ARIA labels to all controls
    - Ensure keyboard navigation works
    - Test with screen reader
    - Add focus indicators
    - _Requirements: 3.1_

- [x] 13. Final checkpoint - Ensure all tests pass


  - Ensure all tests pass, ask the user if questions arise.
