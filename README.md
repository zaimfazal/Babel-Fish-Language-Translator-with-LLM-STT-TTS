# 🌍 Babel Fish Language Translator with LLM, STT & TTS

A comprehensive multilingual translation and conversation system powered by IBM Watson AI services and Mistral LLM, featuring real-time speech-to-text, intelligent language processing, and dynamic text-to-speech synthesis.

## ✨ Features

- **🎤 Speech-to-Text (STT)**: Real-time audio transcription using IBM Watson Speech-to-Text with support for multiple language models
- **🤖 Large Language Model (LLM)**: Powered by Mistral AI (mistral-medium-2505) for intelligent conversation and translation
- **🔊 Text-to-Speech (TTS)**: Natural sounding speech synthesis using IBM Watson Text-to-Speech with multiple voice options
- **🌐 RESTful API**: Flask-based web server with CORS support for seamless integration
- **🐳 Docker Support**: Containerized deployment for easy setup and scaling
- **💬 Multi-language Support**: Process conversations in multiple languages with support for various voice profiles

## 🏗️ Architecture

The system is built with a modular architecture:

```
┌─────────────────────────┐
│   Client Application    │
│   (Web/Mobile)          │
└────────────┬────────────┘
             │
    ┌────────▼────────┐
    │   Flask Server  │
    │   (Port 8000)   │
    └────────┬────────┘
             │
    ┌────────┴─────────────────┐
    │                           │
┌───▼────────┐        ┌────────▼───┐
│   Worker   │        │  External  │
│  Module    │        │  APIs      │
└────────────┘        └────────────┘
    │                       │
    ├─Speech-to-Text    ├─Watson STT
    ├─Text-to-Speech    ├─Watson TTS
    └─LLM Processing    └─Mistral LLM
```

## 🚀 Getting Started

### Prerequisites

- Python 3.11 or higher
- pip (Python package manager)
- Docker (optional, for containerized deployment)
- IBM Watson API credentials
- Internet connection for external API calls

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/zaimfazal/Babel-Fish-Language-Translator-with-LLM-STT-TTS.git
   cd Babel-Fish-Language-Translator-with-LLM-STT-TTS
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv my_env
   source my_env/bin/activate  # On Windows: my_env\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure API credentials**
   
   Update `worker.py` with your IBM Watson API key:
   ```python
   credentials = {
       "url": "https://us-south.ml.cloud.ibm.com",
       "apikey": "YOUR_API_KEY_HERE"
   }
   
   PROJECT_ID = "your-project-id"
   ```

5. **Run the application**
   ```bash
   python server.py
   ```
   
   The server will start on `http://localhost:8000`

### Docker Deployment

1. **Build the Docker image**
   ```bash
   docker build -t babel-fish-translator .
   ```

2. **Run the container**
   ```bash
   docker run -p 8000:8000 babel-fish-translator
   ```

## 📡 API Endpoints

### 1. Speech-to-Text
**Endpoint:** `POST /speech-to-text`

**Description:** Converts audio to text

**Request:**
- **Content-Type:** `audio/wav` or `audio/flac`
- **Body:** Binary audio data

**Response:**
```json
{
  "text": "Hello, how are you today?"
}
```

**Example:**
```bash
curl -X POST http://localhost:8000/speech-to-text \
  -H "Content-Type: audio/wav" \
  --data-binary @example.flac
```

### 2. Process Message (LLM + TTS)
**Endpoint:** `POST /process-message`

**Description:** Processes a user message with LLM and converts response to speech

**Request:**
```json
{
  "userMessage": "What is the capital of France?",
  "voice": "en-US_MichaelVoice"
}
```

**Response:**
```json
{
  "watsonxResponseText": "The capital of France is Paris.",
  "watsonxResponseSpeech": "base64_encoded_audio_data"
}
```

**Available Voices:**
- `en-US_MichaelVoice`
- `en-US_AllisonVoice`
- `en-US_EmilyVoice`
- `en-US_LisaVoice`
- `en-US_KevinVoice`
- `en-GB_KateVoice`
- `en-GB_JamesVoice`
- `en-GB_CharlotteVoice`
- `fr-FR_ReneeVoice`
- `fr-FR_NicolasVoice`
- `fr-CA_LouiseVoice`
- `it-IT_FrancescaVoice`

**Example:**
```bash
curl -X POST http://localhost:8000/process-message \
  -H "Content-Type: application/json" \
  -d '{
    "userMessage": "Translate this to French",
    "voice": "default"
  }'
```

### 3. Index
**Endpoint:** `GET /`

**Description:** Serves the main web interface

## 📦 Dependencies

- **Flask** - Web framework for Python
- **Flask-CORS** - Cross-Origin Resource Sharing support
- **requests** - HTTP library for API calls
- **ibm-watson-machine-learning** - IBM Watson ML SDK for LLM access

See `requirements.txt` for complete list.

## 🔧 Configuration

### Worker Configuration (`worker.py`)

The worker module handles all AI operations:

```python
# LLM Configuration
model_id = "mistralai/mistral-medium-2505"
parameters = {
    GenParams.DECODING_METHOD: DecodingMethods.GREEDY,
    GenParams.MIN_NEW_TOKENS: 1,
    GenParams.MAX_NEW_TOKENS: 1024
}

# Watson API URLs
STT_BASE_URL = 'https://sn-watson-stt.labs.skills.network'
TTS_BASE_URL = 'https://sn-watson-tts.labs.skills.network'
```

### Server Configuration (`server.py`)

- **Port:** 8000 (configurable)
- **Host:** 0.0.0.0 (accessible from all interfaces)
- **CORS:** Enabled for all origins

## 📁 Project Structure

```
Babel-Fish-Language-Translator-with-LLM-STT-TTS/
├── server.py           # Flask application and API routes
├── worker.py           # AI/ML operations (STT, TTS, LLM)
├── requirements.txt    # Python dependencies
├── Dockerfile          # Docker configuration
├── example.flac        # Sample audio file for testing
├── models/             # Model configurations
│   ├── stt/           # Speech-to-Text model
│   └── tts/           # Text-to-Speech models
├── static/            # Static web assets
├── templates/         # HTML templates
└── my_env/            # Python virtual environment
```

## 🧪 Testing

Test the API with the provided example audio file:

```bash
# Start the server
python server.py

# In another terminal, test speech-to-text
curl -X POST http://localhost:8000/speech-to-text \
  --data-binary @example.flac \
  -H "Content-Type: audio/flac"

# Test message processing
curl -X POST http://localhost:8000/process-message \
  -H "Content-Type: application/json" \
  -d '{
    "userMessage": "Tell me a joke",
    "voice": "en-US_AllisonVoice"
  }'
```

## 🔐 Security Considerations

- Never commit API keys to the repository; use environment variables or secure credential management
- Set CORS appropriately for production environments
- Validate and sanitize all user inputs
- Use HTTPS in production
- Implement rate limiting for API endpoints
- Store sensitive credentials in a `.env` file (add to `.gitignore`)

## 🛠️ Troubleshooting

### Connection Issues
- Verify all external API endpoints are accessible
- Check your internet connection
- Ensure firewall allows outbound connections

### Audio Processing Errors
- Verify audio format (WAV, FLAC supported)
- Ensure audio file is not corrupted
- Check audio quality meets minimum requirements

### API Authentication
- Verify API credentials are correct
- Check API key has required permissions
- Confirm project ID is valid
- Ensure credentials haven't expired

## 🚀 Performance Optimization

- **Caching:** Implement response caching for frequently asked questions
- **Async Processing:** Use task queues for long-running operations
- **Connection Pooling:** Reuse HTTP connections for multiple requests
- **Model Optimization:** Use quantized models for faster inference

## 🤝 Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs and feature requests.

## 📄 License

This project is open source. Please see LICENSE file for details.

## 👤 Author

**Zaim Fazal** - [GitHub Profile](https://github.com/zaimfazal)

## 🙏 Acknowledgments

- IBM Watson for Speech-to-Text and Text-to-Speech services
- Mistral AI for the LLM model
- Flask community for the excellent web framework

## 📞 Support

For issues, questions, or suggestions, please open an issue on the [GitHub repository](https://github.com/zaimfazal/Babel-Fish-Language-Translator-with-LLM-STT-TTS/issues).

## 📚 References

- [IBM Watson Speech-to-Text Documentation](https://cloud.ibm.com/docs/speech-to-text)
- [IBM Watson Text-to-Speech Documentation](https://cloud.ibm.com/docs/text-to-speech)
- [Mistral AI Documentation](https://docs.mistral.ai/)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [IBM Watson ML Documentation](https://cloud.ibm.com/docs/watson-machine-learning)

---

**Built with ❤️ for breaking language barriers**
