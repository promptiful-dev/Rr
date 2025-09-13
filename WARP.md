# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

Bengali Image Analyzer is a full-stack web application that analyzes uploaded images to generate production-ready prompts for Gemini 2.5 PRO image generation. It features a mobile-first React frontend and an Express.js backend with Google Generative AI integration.

## Development Commands

### Environment Setup
```bash
# Install all dependencies (frontend + backend + root)
npm run install:all

# Set up environment variables
cp backend/.env.example backend/.env
# Edit backend/.env and add your GOOGLE_STUDIO_KEY
```

### Development Workflow
```bash
# Start both frontend and backend concurrently
npm run dev

# Start services individually
npm run start:backend    # Express server on port 3000
npm run start:frontend   # Vite dev server on port 5173

# Production build
npm run build           # Builds frontend for production
npm start              # Start production server
```

### Individual Service Commands

**Frontend (Vite + React):**
```bash
cd frontend
npm run dev      # Development server
npm run build    # Production build
npm run preview  # Preview production build
```

**Backend (Express.js):**
```bash
cd backend
npm run dev      # Development with nodemon
npm start        # Production mode
```

## Architecture Overview

### High-Level Architecture
- **Monorepo Structure**: Frontend and backend are separate packages within a single repository
- **API-First Design**: Backend provides RESTful API; frontend is a client-side SPA
- **Google AI Integration**: Uses Google Generative AI (Gemini 1.5 Pro) for image analysis
- **Security-Focused**: Comprehensive logging, validation, rate limiting, and API key management

### Frontend Architecture (`frontend/`)
- **Framework**: React 18 with functional components and hooks
- **Build Tool**: Vite for fast development and optimized production builds
- **Styling**: TailwindCSS with mobile-first responsive design
- **State Management**: React hooks for local state, no external state management
- **Screen Flow**: Landing → Upload → Processing → Results (with modals for settings)
- **API Client**: Custom class-based client with error handling and localStorage integration

**Key Frontend Components:**
- `App.jsx`: Main application with screen routing and state management
- `Header.jsx`: Palestinian flag-styled navigation with Bengali text
- `UploadZone.jsx`: Drag-and-drop file upload and URL input
- `ProcessingModal.jsx`: Real-time progress indication during analysis
- `ResultPanel.jsx`: Structured display of analysis results with copy functionality
- `SettingsModal.jsx`: API key management with local storage

### Backend Architecture (`backend/`)
- **Framework**: Express.js with comprehensive middleware stack
- **Security**: Helmet, CORS, rate limiting, input validation
- **File Handling**: Multer for multipart uploads (10MB limit, JPG/PNG/WebP only)
- **Logging**: Winston with daily rotation, sanitized output, and environment-aware configuration
- **Error Handling**: Centralized error handling with sanitization

**Key Backend Components:**
- `index.js`: Main server setup with middleware configuration and static file serving
- `controllers/analyzeController.js`: Core image analysis logic with Google AI integration
- `utils/logger.js`: Security-focused logging with API key sanitization
- `utils/validation.js`: Input validation and sanitization utilities
- `utils/security.js`: Security utilities (if present)

### Data Flow Architecture
1. **Image Input**: User uploads file or provides URL
2. **Validation**: Frontend validates file type/size, backend re-validates
3. **Processing**: Image sent to Google Generative AI with structured prompt
4. **Analysis**: AI returns detailed JSON analysis with confidence scores
5. **Response**: Structured data displayed with copy-to-clipboard functionality

### API Integration Pattern
- **Primary API Key**: Server-side Google Studio key in environment variables
- **Custom Keys**: Optional user-provided keys via `x-google-studio-key` header
- **Fallback Handling**: Graceful degradation with confidence scoring
- **Error Recovery**: Comprehensive error handling with user-friendly messages

## Environment Configuration

### Required Environment Variables
```bash
# Backend (.env in backend/)
GOOGLE_STUDIO_KEY=AIza...          # Required: Google Studio API key
PORT=3000                          # Optional: Server port
NODE_ENV=production                # Optional: Environment mode
RATE_LIMIT_MAX=60                  # Optional: Requests per window
RATE_LIMIT_WINDOW_MS=60000         # Optional: Rate limit window (ms)
LOG_LEVEL=info                     # Optional: Logging level
FRONTEND_URL=http://localhost:5173 # Optional: CORS origin
```

### Replit Deployment
The project includes Replit-specific configurations:
- `.replit`: Replit project configuration
- `replit.nix`: Nix dependencies for Replit environment
- Production build serves static frontend from backend

## Development Patterns

### Component Architecture
- **Functional Components**: All React components use hooks
- **Props-Down Pattern**: State flows down through props
- **Event Bubbling**: User actions bubble up through callback props
- **Screen-Based Routing**: Manual screen state management rather than React Router

### API Client Pattern
- **Singleton Instance**: Single API client instance exported with convenience functions
- **Custom Error Handling**: `ApiError` class with status code categorization
- **Header Management**: Automatic API key injection from localStorage
- **Type Flexibility**: Supports file uploads, base64, and URL-based image input

### Security Patterns
- **API Key Masking**: All logging masks sensitive information
- **Input Sanitization**: Comprehensive validation on both client and server
- **Rate Limiting**: Global and endpoint-specific rate limits
- **CORS Configuration**: Environment-aware origin configuration

### Logging Strategy
- **Environment-Aware**: File logging disabled in Replit environments
- **Security-First**: Automatic sanitization of sensitive data
- **Structured Logging**: JSON format for production, human-readable for development
- **Daily Rotation**: Log files rotate daily with size limits

## Testing Strategy

Currently no automated tests are configured. When adding tests:
- Use Jest for backend unit tests
- Use React Testing Library for frontend component tests
- Consider Playwright or Cypress for end-to-end testing
- Test the full image analysis flow with mock Google AI responses

## Common Development Tasks

### Adding New Analysis Fields
1. Update the analysis prompt in `backend/controllers/analyzeController.js`
2. Add fallback handling in the response processing
3. Update frontend `ResultPanel.jsx` to display new fields
4. Consider adding confidence score display and copy functionality

### Modifying Image Processing
1. Update validation in `backend/utils/validation.js` and `frontend/src/utils/apiClient.js`
2. Modify Multer configuration in `backend/index.js` if changing file limits
3. Update error messages in both frontend and backend

### Adding New API Endpoints
1. Create controller in `backend/controllers/`
2. Add route in `backend/index.js`
3. Update rate limiting if necessary
4. Add corresponding client method in `frontend/src/utils/apiClient.js`

## Deployment Notes

### Local Development
- Backend runs on port 3000, frontend on port 5173
- CORS configured for cross-origin development
- Hot reloading enabled for both services

### Production Deployment
- Frontend builds to `frontend/dist/`
- Backend serves static files in production mode
- Single port deployment with API routes under `/api/`
- Environment variables required for Google AI integration

### Security Considerations
- API keys never logged in plain text
- Rate limiting prevents abuse
- File upload restrictions prevent malicious files
- CORS properly configured for production domains