# Upload & Copy - Bengali Image Analyzer

🇧🇩 **একটি সহজ ও public ওয়েব অ্যাপ — আপলোড/লিঙ্ক করা ইমেজ বিশ্লেষণ করে production-ready image-generation prompt তৈরি করা।**

A simple, public web app that analyzes uploaded or linked images to generate production-ready prompts for Gemini 2.5 PRO image generation.

## ✨ Features

- 📱 **Mobile-First Design**: Optimized for mobile devices with responsive desktop support
- 🖼️ **Multi-Input Support**: Upload files or paste image URLs
- 🎨 **Detailed Analysis**: Extract colors, poses, lighting, mood, and technical specifications
- 🔍 **Confidence Scoring**: Each analysis element includes accuracy confidence ratings
- 📋 **Copy-Ready Prompts**: Generated prompts optimized for Gemini 2.5 PRO
- 🔐 **Secure API Key Management**: Store custom Google Studio API keys locally
- 🚀 **Production Ready**: Deployed easily on Replit with rate limiting and security
- 🎌 **Bengali Interface**: Palestinian flag-styled header with Bengali text

## 🏗️ Architecture

### Frontend
- **React 18** with functional components and hooks
- **Vite** for fast development and optimized builds
- **TailwindCSS** for mobile-first responsive design
- **Lucide React** for consistent icons
- **React Hot Toast** for user notifications

### Backend
- **Node.js + Express** RESTful API server
- **Google Generative AI** for image analysis
- **Winston** logging with security-focused sanitization
- **Helmet + CORS** for security headers
- **Rate limiting** with express-rate-limit
- **Multer** for secure file upload handling

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ 
- Google Studio API Key ([Get one here](https://makersuite.google.com/app/apikey))

### Local Development

1. **Clone and Install**
   ```bash
   git clone <your-repo-url>
   cd bengali-image-analyzer
   npm run install:all
   ```

2. **Configure Environment**
   ```bash
   # Backend configuration
   cp backend/.env.example backend/.env
   # Edit backend/.env and add your GOOGLE_STUDIO_KEY
   ```

3. **Start Development Servers**
   ```bash
   # Start both frontend and backend
   npm run dev
   
   # Or start individually
   npm run start:backend  # Backend on port 3000
   npm run start:frontend # Frontend on port 5173
   ```

4. **Visit Application**
   Open `http://localhost:5173` in your browser

## 🌐 Replit Deployment

### One-Click Deploy
1. Fork this project to your Replit account
2. Add your Google Studio API key to Replit Secrets:
   - Key: `GOOGLE_STUDIO_KEY`
   - Value: Your API key (starts with `AIza...`)
3. Click "Run" - the app will automatically build and deploy!

### Manual Replit Setup
1. Create a new Node.js Repl
2. Upload project files or connect via GitHub
3. Configure Secrets in Replit:
   ```
   GOOGLE_STUDIO_KEY=AIza_your_actual_key_here
   NODE_ENV=production
   RATE_LIMIT_MAX=60
   RATE_LIMIT_WINDOW_MS=60000
   LOG_LEVEL=info
   ```
4. Run `npm run install:all`
5. Start with `npm start`

## 🔧 Configuration

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `GOOGLE_STUDIO_KEY` | ✅ | - | Google Studio API key |
| `PORT` | ❌ | 3000 | Server port |
| `NODE_ENV` | ❌ | development | Environment mode |
| `RATE_LIMIT_MAX` | ❌ | 60 | Requests per window |
| `RATE_LIMIT_WINDOW_MS` | ❌ | 60000 | Rate limit window (ms) |
| `LOG_LEVEL` | ❌ | info | Logging level |
| `FRONTEND_URL` | ❌ | auto | CORS origin URL |

### Custom API Keys
Users can optionally provide their own Google Studio API keys via the settings modal. These keys are:
- Stored locally in browser localStorage
- Never sent to your servers permanently
- Used per-request via `x-google-studio-key` header
- Validated for format before use

## 📡 API Documentation

### Base URL
- Development: `http://localhost:3000/api`
- Production: `https://your-repl-name.username.repl.co/api`

### Endpoints

#### `GET /api/status`
Health check endpoint.

**Response:**
```json
{
  "status": "ok",
  "version": "1.0.0",
  "google_studio_available": true
}
```

#### `POST /api/analyze`
Analyze an uploaded image or image URL.

**Headers:**
- `Content-Type`: `application/json` or `multipart/form-data`
- `x-google-studio-key`: Optional custom API key

**Body (JSON):**
```json
{
  "image": "base64_string_or_url"
}
```

**Body (Form Data):**
```
image: [File object]
```

**Response:**
```json
{
  "graphics_style": {
    "value": "photorealistic portrait",
    "confidence": 0.92
  },
  "color_palette": {
    "primary": { "hex": "#D62828", "confidence": 0.95 },
    "secondary": { "hex": "#007A3D", "confidence": 0.88 },
    "accent": { "hex": "#000000", "confidence": 0.82 }
  },
  "background_environment": {
    "value": "studio lighting, neutral backdrop",
    "confidence": 0.85
  },
  "subject": {
    "pose": {
      "value": "three-quarter turn, confident stance",
      "confidence": 0.91
    },
    "clothing": {
      "value": "formal blazer, professional attire",
      "confidence": 0.87
    },
    "expression": {
      "value": "warm smile, direct eye contact",
      "confidence": 0.89
    }
  },
  "lighting_camera": {
    "value": "soft studio lighting, 85mm lens, f/2.8",
    "confidence": 0.84
  },
  "mood": {
    "value": "professional, approachable",
    "confidence": 0.86
  },
  "technical_specs": {
    "resolution": "1920x2560",
    "aspect_ratio": "3:4"
  },
  "special_effects": {
    "value": "subtle background blur",
    "confidence": 0.78
  },
  "final_prompt": {
    "value": "A photorealistic portrait in formal blazer, three-quarter turn with confident stance, warm smile and direct eye contact, studio lighting with neutral backdrop, 85mm lens at f/2.8, professional and approachable mood, dominant colors #D62828, #007A3D, #000000, subtle background blur, high detail (1920x2560, 3:4 aspect ratio)",
    "confidence": 0.88
  },
  "confidence": 0.87
}
```

## 🛡️ Security Features

### Rate Limiting
- **Global**: 60 requests per minute per IP
- **Analysis**: 10 requests per minute per IP
- **Custom**: Configurable via environment variables

### Input Validation
- File type validation (JPG, PNG, WebP only)
- File size limits (10MB maximum)
- URL format validation
- API key format validation
- Input sanitization against XSS/injection

### Security Headers
- Content Security Policy (CSP)
- X-Frame-Options: DENY
- X-Content-Type-Options: nosniff
- Referrer-Policy: strict-origin-when-cross-origin
- X-XSS-Protection: 1; mode=block

### API Key Security
- Server API keys stored in environment/secrets only
- Client keys stored in localStorage only
- API keys masked in all logs
- Per-request key forwarding (never stored server-side)
- Format validation before processing

## 🎨 UI Components

### Header
- Palestinian flag-styled badge with Bengali text
- Responsive design for mobile/desktop
- Settings access and developer credit
- Navigation breadcrumbs

### UploadZone  
- Drag-and-drop file upload
- URL input with validation
- Image preview with controls
- Progress feedback and error handling

### ProcessingModal
- Real-time progress indication
- Stage-by-stage feedback
- Cancellation support
- Loading animations

### ResultPanel
- Organized analysis cards with confidence badges
- Color swatches with copy functionality
- Copyable final prompt
- JSON export capability
- Regeneration options

### SettingsModal
- API key management with masking
- Format validation and testing
- Security information
- Local storage management

## 🔍 Analysis Components

The app analyzes these image aspects:

1. **Graphics Style**: Art style classification (photorealistic, illustration, etc.)
2. **Color Palette**: Primary, secondary, and accent colors with hex codes
3. **Background Environment**: Setting and backdrop description
4. **Subject Analysis**:
   - **Pose**: Body position and stance
   - **Clothing**: Detailed garment description  
   - **Expression**: Facial expression and emotion
5. **Lighting & Camera**: Technical photography settings
6. **Mood**: Overall emotional tone
7. **Technical Specs**: Resolution and aspect ratio
8. **Special Effects**: Filters, treatments, or enhancements

Each component includes:
- **Value**: Descriptive text
- **Confidence**: 0.0-1.0 accuracy score
- **Tooltip**: Explanation of detection rationale

## 🏃 Performance

### Frontend Optimization
- Code splitting with Vite
- Lazy loading of heavy components
- Optimized bundle size
- Service worker ready (PWA)

### Backend Optimization
- Express.js with minimal middleware
- Efficient logging with rotation
- Memory usage optimization
- Response compression

### Analysis Performance
- Parallel processing where possible
- Caching for repeated requests
- Timeout handling for long operations
- Graceful degradation on failures

## 📱 Mobile Experience

- **Touch-First**: All interactions optimized for touch
- **Responsive**: Fluid layout across screen sizes
- **Fast Loading**: Optimized for mobile networks
- **Offline-Ready**: Basic functionality without network
- **PWA Features**: Add to home screen, theme colors

## 🌍 Accessibility

- **WCAG 2.1 AA** compliant color contrast
- **Keyboard Navigation** throughout the app
- **Screen Reader** friendly with ARIA labels
- **Focus Management** for modal interactions
- **High Contrast** mode support
- **Reduced Motion** respects user preferences

## 🔧 Development

### Project Structure
```
bengali-image-analyzer/
├── frontend/               # React frontend
│   ├── src/
│   │   ├── components/     # React components
│   │   ├── utils/         # API client & utilities
│   │   └── styles/        # CSS and styling
│   ├── public/            # Static assets
│   └── package.json       # Frontend dependencies
├── backend/               # Express backend
│   ├── controllers/       # Route handlers
│   ├── utils/            # Validation, logging, security
│   ├── services/         # Business logic (future)
│   └── package.json      # Backend dependencies
├── shared/               # Shared utilities (future)
├── .replit              # Replit configuration
├── replit.nix           # Nix dependencies
└── package.json         # Root scripts & meta
```

### Available Scripts

**Root Level:**
- `npm start` - Start production server
- `npm run dev` - Start both frontend and backend in development
- `npm run build` - Build frontend for production  
- `npm run install:all` - Install all dependencies

**Frontend:**
- `npm run dev` - Start Vite dev server (port 5173)
- `npm run build` - Build for production
- `npm run preview` - Preview production build

**Backend:**
- `npm start` - Start production server
- `npm run dev` - Start with nodemon for development

### Code Style
- **ESLint** configuration for consistent code style
- **Prettier** for automated formatting
- **Functional components** with hooks
- **Async/await** for asynchronous operations
- **JSDoc** comments for function documentation

## 🐛 Troubleshooting

### Common Issues

**"Invalid API key format"**
- Ensure your Google Studio API key starts with `AIza` and is at least 30 characters
- Check that there are no extra spaces or characters
- Verify the key is active in Google Studio

**"Network error"**
- Check if the backend server is running (port 3000)
- Verify CORS settings allow frontend origin
- Check rate limiting hasn't been exceeded

**"File too large"**
- Images must be under 10MB
- Use compressed formats (JPG, PNG, WebP)
- Try resizing the image before upload

**"Analysis failed"**  
- Check Google Studio API quotas and billing
- Verify the image is a valid format
- Try with a simpler image first

### Debug Mode
Set `LOG_LEVEL=debug` in your environment to see detailed logs:
```bash
# Development
LOG_LEVEL=debug npm run dev

# Production (Replit Secrets)
LOG_LEVEL=debug
```

## 🤝 Contributing

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** changes (`git commit -m 'Add amazing feature'`)
4. **Push** to branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Development Guidelines
- Follow existing code style and patterns
- Add tests for new functionality
- Update documentation for API changes
- Ensure mobile responsiveness
- Test with various image types and sizes

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## 👨‍💻 Author

**Al Shakil**
- Facebook: [@al.shakil03](https://www.facebook.com/al.shakil03)
- Project: Bengali Image Analyzer

## 🙏 Acknowledgments

- **Google Generative AI** for powerful image analysis capabilities
- **Palestinian flag** design inspiration for the cultural header badge
- **Bengali community** for language and cultural guidance
- **Replit** for easy deployment and hosting
- **Open source libraries** that make this project possible

## 📈 Roadmap

### Version 1.1 (Planned)
- [ ] Batch image processing
- [ ] Analysis history and favorites
- [ ] Custom prompt templates
- [ ] Additional AI model support
- [ ] Dark mode theme

### Version 1.2 (Future)
- [ ] User accounts and cloud sync
- [ ] Advanced editing tools
- [ ] API webhooks and integrations
- [ ] Multi-language support expansion
- [ ] Performance analytics dashboard

---

**Made with ❤️ for the Bengali community**

*যে কোন ছবি আপলোড করো এবং তার মত হয়ে যাও* 🇧🇩