# Design Document: Corporate Email Generator

## Overview

The Corporate Email Generator is a web-based application that helps corporate employees quickly generate professional emails using AI. The system provides a simple, intuitive interface where users input their main message content along with optional parameters (tone, length, recipient context, keywords) and receive a fully formatted email with subject line. The application maintains session history for easy access to recently generated emails.

The system will be built as a single-page application (SPA) using modern web technologies, with a clean separation between the UI layer, business logic, and AI integration layer.

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        User Interface                        │
│  (HTML/CSS/JavaScript - React or Vanilla JS)                │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│                   Application Logic Layer                    │
│  - Input Validation                                          │
│  - Email Generation Orchestration                            │
│  - History Management                                        │
│  - Clipboard Operations                                      │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│                    AI Integration Layer                      │
│  - Prompt Construction                                       │
│  - API Communication (OpenAI/Anthropic/Local LLM)           │
│  - Response Parsing                                          │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack

- **Frontend**: HTML5, CSS3, JavaScript (ES6+) with optional React for component management
- **AI Integration**: OpenAI API (GPT-3.5/GPT-4) or Anthropic Claude API
- **Storage**: Browser localStorage for session history
- **Build Tools**: Vite or Webpack for bundling (optional, can be vanilla)
- **Testing**: Jest for unit tests, fast-check for property-based testing

## Components and Interfaces

### 1. UI Components

#### InputForm Component
- Manages all user inputs
- Fields:
  - Main content (textarea, required)
  - Additional keywords (text input, optional)
  - Tone selector (dropdown: formal, friendly, urgent)
  - Length selector (dropdown: brief, standard, detailed)
  - Recipient context (dropdown: manager, colleague, client, team)
- Generate button
- Clear/Reset button

#### OutputDisplay Component
- Shows generated email with subject line
- Displays subject line separately
- Shows email body with proper formatting
- Copy buttons (copy all, copy subject, copy body)
- Visual feedback for copy actions

#### HistoryPanel Component
- Lists recent generations (up to 10)
- Shows timestamp and preview for each
- Click to view full email
- Copy from history functionality

#### ErrorDisplay Component
- Shows user-friendly error messages
- Highlights problematic fields
- Provides actionable guidance

### 2. Core Services

#### EmailGeneratorService
```javascript
interface EmailGeneratorService {
  generateEmail(params: EmailParams): Promise<GeneratedEmail>
  validateInput(params: EmailParams): ValidationResult
}

interface EmailParams {
  mainContent: string
  keywords?: string[]
  tone: 'formal' | 'friendly' | 'urgent' | 'default'
  length: 'brief' | 'standard' | 'detailed'
  recipientContext?: 'manager' | 'colleague' | 'client' | 'team'
}

interface GeneratedEmail {
  subject: string
  body: string
  timestamp: Date
}
```

#### HistoryService
```javascript
interface HistoryService {
  addToHistory(email: GeneratedEmail): void
  getHistory(): GeneratedEmail[]
  getEmailById(id: string): GeneratedEmail | null
  clearHistory(): void
}
```

#### ClipboardService
```javascript
interface ClipboardService {
  copyToClipboard(text: string): Promise<boolean>
  copyEmail(email: GeneratedEmail, includeSubject: boolean): Promise<boolean>
}
```

#### AIService
```javascript
interface AIService {
  callAI(prompt: string): Promise<string>
  constructPrompt(params: EmailParams): string
  parseResponse(response: string): { subject: string, body: string }
}
```

## Data Models

### EmailParams
```typescript
type Tone = 'formal' | 'friendly' | 'urgent' | 'default'
type Length = 'brief' | 'standard' | 'detailed'
type RecipientContext = 'manager' | 'colleague' | 'client' | 'team' | 'default'

interface EmailParams {
  mainContent: string          // Required, min 1 word
  keywords: string[]           // Optional array of keywords
  tone: Tone                   // Default: 'default'
  length: Length               // Default: 'standard'
  recipientContext: RecipientContext  // Default: 'default'
}
```

### GeneratedEmail
```typescript
interface GeneratedEmail {
  id: string                   // Unique identifier (UUID)
  subject: string              // Generated subject line
  body: string                 // Generated email body
  timestamp: Date              // When generated
  params: EmailParams          // Original parameters used
}
```

### ValidationResult
```typescript
interface ValidationResult {
  isValid: boolean
  errors: ValidationError[]
}

interface ValidationError {
  field: string
  message: string
}
```

### HistoryItem
```typescript
interface HistoryItem {
  id: string
  subject: string
  preview: string              // First 50 chars of body
  timestamp: Date
  fullEmail: GeneratedEmail
}
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*


### Property 1: Input acceptance and storage
*For any* valid text string with at least one word, when provided as main content, the system should accept and store it without error.
**Validates: Requirements 1.1**

### Property 2: Keyword incorporation
*For any* set of keywords provided, each keyword should appear at least once in the generated email body or subject line.
**Validates: Requirements 2.1, 2.3**

### Property 3: Keyword format parsing
*For any* list of keywords, whether comma-separated or space-separated, the system should correctly parse and extract all individual keywords.
**Validates: Requirements 2.4**

### Property 4: Email structure completeness
*For any* generated email, the output should contain all required structural elements: greeting, body content, and closing.
**Validates: Requirements 4.1**

### Property 5: Clipboard copy accuracy
*For any* generated email, when copied to clipboard, the clipboard content should exactly match the displayed email text.
**Validates: Requirements 5.2**

### Property 6: Reset clears all fields
*For any* application state with populated inputs and generated output, triggering reset should clear all input fields and remove the generated output.
**Validates: Requirements 6.2**

### Property 7: Regeneration with modified inputs
*For any* set of initial inputs and any modification to those inputs, regenerating should produce output based on the modified inputs, not the original ones.
**Validates: Requirements 6.3**

### Property 8: Output replacement
*For any* sequence of email generations, only the most recently generated email should be displayed in the output area.
**Validates: Requirements 6.4**

### Property 9: Tone parameter propagation
*For any* selected tone option, when generation is triggered, the tone parameter should be correctly passed to the AI service.
**Validates: Requirements 7.4**

### Property 10: Subject line generation
*For any* valid email generation request, the system should always produce both a subject line and a body, never just one or the other.
**Validates: Requirements 8.1**

### Property 11: Length constraints
*For any* selected length option (brief, standard, or detailed), the generated email body should contain a sentence count within the specified range for that length.
**Validates: Requirements 9.2, 9.3, 9.4**

### Property 12: History storage
*For any* successfully generated email, the email should be added to the session history with a timestamp.
**Validates: Requirements 10.1**

### Property 13: History size limit
*For any* sequence of more than 10 email generations, the history should contain only the 10 most recent emails, ordered by timestamp descending.
**Validates: Requirements 10.2**

### Property 14: History retrieval accuracy
*For any* email stored in history, retrieving it by ID should return an email with identical subject, body, and parameters to what was originally stored.
**Validates: Requirements 10.3**

### Property 15: History clipboard copy
*For any* email in the history, copying it to clipboard should result in clipboard content that exactly matches the stored email.
**Validates: Requirements 10.4**

### Property 16: Error message display
*For any* error condition during generation, the system should display a user-friendly error message to the user.
**Validates: Requirements 12.1**

### Property 17: Field-specific validation errors
*For any* invalid input in a specific field, the error message should clearly identify which field has the issue.
**Validates: Requirements 12.2**

### Property 18: Input preservation on error
*For any* error condition, all user inputs should remain unchanged and available for correction.
**Validates: Requirements 12.3**

### Property 19: Service unavailability handling
*For any* AI service failure or unavailability, the system should display an appropriate error message and suggest retry actions.
**Validates: Requirements 12.4**

## Error Handling

### Input Validation Errors
- Empty main content: "Please enter the main content for your email."
- Whitespace-only content: "Main content cannot be empty or contain only spaces."
- Invalid characters: Sanitize input to prevent XSS attacks

### AI Service Errors
- API timeout: "The email generation is taking longer than expected. Please try again."
- API error response: "Unable to generate email at this time. Please check your connection and try again."
- Rate limiting: "Too many requests. Please wait a moment and try again."
- Invalid API key: "Service configuration error. Please contact support."

### Browser/Client Errors
- Clipboard access denied: "Unable to copy to clipboard. Please copy manually."
- localStorage full: "Unable to save history. Your browser storage may be full."
- Network offline: "No internet connection. Please check your connection and try again."

### Error Recovery
- All errors should preserve user input
- Errors should be dismissible
- System should remain functional after errors
- Retry mechanisms for transient failures

## Testing Strategy

### Unit Testing Approach

Unit tests will verify specific examples, edge cases, and integration points:

**Input Validation Tests:**
- Test empty string rejection
- Test whitespace-only string rejection
- Test valid input acceptance
- Test keyword parsing for various formats

**UI Component Tests:**
- Test that all required form fields render
- Test that buttons are present and enabled/disabled correctly
- Test that error messages display in correct locations
- Test that history panel renders items correctly

**Service Integration Tests:**
- Test AI service prompt construction
- Test response parsing
- Test error handling for various API responses
- Test localStorage operations

**Edge Cases:**
- Empty keywords array
- Very long main content (>10,000 characters)
- Special characters in input
- Rapid successive generations
- History at exactly 10 items

### Property-Based Testing Approach

Property-based tests will verify universal properties across many randomly generated inputs using **fast-check** library for JavaScript:

**Configuration:**
- Each property test should run a minimum of 100 iterations
- Use appropriate generators for different input types
- Tag each test with the format: `**Feature: corporate-email-generator, Property {number}: {property_text}**`

**Test Generators:**
- `mainContentGenerator`: Generates random strings with 1-500 words
- `keywordsGenerator`: Generates arrays of 0-10 random keywords
- `toneGenerator`: Randomly selects from valid tone options
- `lengthGenerator`: Randomly selects from valid length options
- `recipientContextGenerator`: Randomly selects from valid recipient contexts
- `emailParamsGenerator`: Combines all above generators for complete EmailParams objects

**Property Test Coverage:**
- Input acceptance and storage (Property 1)
- Keyword incorporation (Property 2)
- Keyword parsing (Property 3)
- Email structure (Property 4)
- Clipboard operations (Properties 5, 15)
- Reset functionality (Property 6)
- Regeneration (Properties 7, 8)
- Parameter propagation (Property 9)
- Subject line generation (Property 10)
- Length constraints (Property 11)
- History operations (Properties 12, 13, 14)
- Error handling (Properties 16, 17, 18, 19)

**Mocking Strategy:**
- Mock AI service responses for deterministic testing
- Mock clipboard API for testing copy operations
- Mock localStorage for testing history operations
- Use real validation and business logic (no mocking)

## Implementation Considerations

### AI Prompt Engineering

The system will construct prompts that include:
1. Base instruction for professional email generation
2. Main content from user
3. Tone modifier based on selection
4. Length constraint based on selection
5. Recipient context for formality adjustment
6. Keywords to incorporate
7. Request for structured output (subject + body)

Example prompt structure:
```
You are a professional email writing assistant. Generate a corporate email with the following requirements:

Main Content: [user's main content]
Tone: [formal/friendly/urgent]
Length: [brief: 3-5 sentences / standard: 6-10 sentences / detailed: 11-15 sentences]
Recipient: [manager/colleague/client/team - adjust formality accordingly]
Keywords to include: [keyword1, keyword2, ...]

Provide the output in this format:
Subject: [subject line]

Body:
[email body with greeting, content, and closing]
```

### Performance Considerations

- Debounce input changes to avoid excessive re-renders
- Show loading state during AI generation (typically 2-5 seconds)
- Cache AI responses if same parameters are used (optional optimization)
- Lazy load history items if list grows large
- Implement request cancellation if user navigates away

### Security Considerations

- Sanitize all user inputs to prevent XSS attacks
- Use HTTPS for all API communications
- Store API keys securely (environment variables, not in code)
- Implement rate limiting to prevent abuse
- Validate all inputs on both client and server side
- Don't log sensitive email content

### Accessibility

- Proper ARIA labels for all form controls
- Keyboard navigation support
- Screen reader announcements for generated content
- High contrast mode support
- Focus management for modal dialogs and errors
- Semantic HTML structure

### Browser Compatibility

- Target modern browsers (Chrome, Firefox, Safari, Edge - last 2 versions)
- Graceful degradation for older browsers
- Polyfills for clipboard API if needed
- localStorage fallback if unavailable

## Future Enhancements

- Email templates library (meeting request, follow-up, etc.)
- Multi-language support
- Export history to file
- Email signature integration
- Collaborative features (share generated emails)
- Analytics on usage patterns
- Custom tone definitions
- Integration with email clients (Gmail, Outlook plugins)
