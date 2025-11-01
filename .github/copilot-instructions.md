# AI Agent Instructions for Computer Vision Prototype

## Project Overview

This is an AI-powered learning platform that analyzes community issues through both visual and text inputs, classifies problems, and generates structured learning mission statements. The system uses Google's Gemini API for vision and text analysis.

## Architecture

The system follows a modular pipeline architecture with these key components:

- `app.py` - Streamlit web interface and entry point
- `integrated_system.py` - Core orchestrator that coordinates the analysis pipeline
- `vision_detector.py` - Handles image analysis using Gemini Vision
- `problem_classifier.py` - Categorizes detected issues
- `mission_generator.py` - Generates structured learning missions
- `config.py` - Environment and configuration management

### Data Flow

1. Input (image/text) → Vision/Text Analysis
2. Analysis Results → Problem Classification
3. Classified Problem → Mission Statement Generation

## Development Setup

1. Python virtual environment is required:

```bash
python -m venv venv
./venv/Scripts/activate  # Windows
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Environment Configuration:

- Create `.env` file with `GEMINI_API_KEY`
- See README.md for API key acquisition

## Key Patterns and Conventions

### Error Handling

Components return structured response dictionaries with:

- `success`: boolean indicating operation status
- `error`: error message when success is False
- `step`: identifier of failing step

Example from `integrated_system.py`:

```python
return {
    'success': False,
    'error': vision_result.get('error', 'Vision detection failed'),
    'step': 'vision_detection'
}
```

### State Management

- Use Streamlit session state for persistence
- Initialize components in session state only once
- See `app.py` for session state pattern

### UI Components

- Custom CSS classes defined in `app.py`
- Use semantic class names: `success-box`, `info-box`, `warning-box`
- Consistent button styling through `.stButton>button` CSS

### Additional Code Patterns

#### Vision Analysis Prompts

Vision detector uses structured prompts with domain-specific examples:

```python
prompt = f"""You are an AI assistant specialized in identifying community issues...
Analyze this image and identify any visible community problems in:
{', '.join(domains)}
...
"""
```

#### Response Formatting

Components use consistent response structures:

```python
{
    'success': True,
    'analysis': str,
    'raw_response': Response,
    'domains_analyzed': List[str]
}
```

## Testing and Validation

### Unit Testing

- Test components in isolation using mock Gemini responses
- Validate error handling paths using invalid inputs
- Test edge cases in image processing (corrupt files, unsupported formats)

### Integration Testing

- End-to-end pipeline tests through `AILearningPlatform` class
- Test cross-component error propagation
- Validate state management in Streamlit UI

## Component Interfaces

### Vision Detector

```python
def detect_issues(self, image_path: str, domains: Optional[List[str]] = None) -> Dict:
    """Analyzes image for community issues in specified domains
    Returns: Dict with analysis results and success status
    """
```

### Problem Classifier

```python
def classify_problem(self, description: str) -> Dict:
    """Categorizes problem description into predefined domains
    Returns: Dict with classification category and confidence
    """
```

### Mission Generator

```python
def generate_mission_statement(self, problem_desc: str, context: str = "") -> Dict:
    """Creates structured learning mission from problem description
    Returns: Dict with mission statement and metadata
    """
```

## Deployment and Running

### Local Development

1. Start development server:

```bash
streamlit run app.py
```

2. Access UI at `http://localhost:8501`

### Production Deployment

1. Set production environment variables
2. Use proper CORS settings for API endpoints
3. Configure Streamlit server:

```bash
streamlit run app.py --server.port $PORT --server.address 0.0.0.0
```

## Integration Points

### Gemini API Integration

- Models: Using Vision-Pro for image analysis
- API Key: Required in `.env` file
- Error handling: Wrap API calls in try-except

### Streamlit UI Framework

- Session State: Persists analysis results
- Custom Components: Use built-in Streamlit widgets
- Styling: Custom CSS in `app.py`

### Image Processing

- PIL/Pillow: Handles image loading and conversion
- Format Support: PNG, JPEG, WebP
- Error Handling: Validates before API calls
