# Research Assistant Translator

A professional, scalable web application for multi-language translation using Neural Machine Translation (NMT) models.

## 🎯 Features

- **Scalable Model Routing System** - Dictionary-based routing for 10+ translation directions
- **Google Translate-Inspired UI** - Clean, professional interface with split-screen design
- **Real-time Feedback** - Loading spinners and error handling
- **Language Swap** - Quick swap between source and target languages
- **Copy to Clipboard** - Easy copying of translated text
- **Responsive Design** - Works on desktop and mobile devices

## 📁 Project Structure

```
primal-cassini/
├── main.py           # FastAPI backend with model routing
├── index.html        # Frontend (single-file HTML + Tailwind CSS)
├── README.md         # This file
└── models/           # (Create this folder) Place your trained models here
    ├── eng_marma/
    ├── marma_eng/
    ├── marma_bengali/
    └── ... (other model directories)
```

## 🚀 Getting Started

### 1. Install Dependencies

```bash
pip install fastapi uvicorn pydantic
```

For production with actual models, you'll also need:
```bash
pip install torch transformers
```

### 2. Add Your Trained Models

Edit `main.py` and locate the `load_models()` method in the `ModelRouter` class (around line 55):

```python
def load_models(self):
    """Load all translation models here."""
    
    from transformers import AutoModelForSeq2SeqLM, AutoTokenizer
    
    # Example: Load English -> Marma model
    tokenizer_eng_marma = AutoTokenizer.from_pretrained("./models/eng_marma")
    model_eng_marma = AutoModelForSeq2SeqLM.from_pretrained("./models/eng_marma")
    
    self.MODEL_MAP["English_Marma"] = {
        "model": model_eng_marma,
        "tokenizer": tokenizer_eng_marma
    }
    
    # Repeat for all 10 translation directions...
```

### 3. Update Translation Logic

In the `/translate` endpoint (around line 190), replace the dummy translation logic:

```python
# Replace this dummy logic:
translated_text = f"[{request.target_lang} translation of: '{request.text}']"

# With actual model inference:
tokenizer = model_info["tokenizer"]
model = model_info["model"]
inputs = tokenizer(request.text, return_tensors="pt", padding=True)
outputs = model.generate(**inputs, max_length=512)
translated_text = tokenizer.decode(outputs[0], skip_special_tokens=True)
```

### 4. Run the Backend

```bash
python main.py
```

The API will start at `http://localhost:8000`

### 5. Open the Frontend

Simply open `index.html` in your web browser, or serve it:

```bash
# Using Python's built-in server
python -m http.server 3000
```

Then navigate to `http://localhost:3000`

## 🔧 Configuration

### Adding More Languages

1. **Update `SUPPORTED_LANGUAGES`** in `main.py`:
```python
self.SUPPORTED_LANGUAGES = ["English", "Bengali", "Marma", "Hindi"]
```

2. **Add language options** in `index.html`:
```html
<option value="Hindi">Hindi (हिन्दी)</option>
```

3. **Load the corresponding models** in `load_models()`:
```python
self.MODEL_MAP["Marma_Hindi"] = { ... }
self.MODEL_MAP["Hindi_Marma"] = { ... }
```

### API Endpoints

- `GET /` - Health check
- `GET /languages` - Get supported languages
- `GET /directions` - Get available translation directions
- `POST /translate` - Translate text

### Example API Request

```bash
curl -X POST "http://localhost:8000/translate" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Hello, how are you?",
    "source_lang": "English",
    "target_lang": "Marma"
  }'
```

## 📊 Model Directory Structure

Organize your trained models like this:

```
models/
├── eng_marma/
│   ├── config.json
│   ├── pytorch_model.bin
│   └── tokenizer_config.json
├── marma_eng/
│   ├── config.json
│   ├── pytorch_model.bin
│   └── tokenizer_config.json
└── ... (other models)
```

## 🎨 UI Customization

The frontend uses Tailwind CSS. To customize colors:

1. Edit the `tailwind.config` in `index.html`:
```javascript
tailwind.config = {
    theme: {
        extend: {
            colors: {
                primary: {
                    500: '#YOUR_COLOR',
                    // ... add more shades
                }
            }
        }
    }
}
```

## 🐛 Troubleshooting

### API Connection Error
- Ensure the FastAPI backend is running on port 8000
- Check the console for CORS errors
- Update `API_BASE_URL` in `index.html` if using a different port

### Translation Direction Not Available
- Verify the model is loaded in `MODEL_MAP`
- Check the exact language names match between frontend and backend
- Visit `http://localhost:8000/directions` to see available directions

### Model Loading Errors
- Ensure model paths are correct
- Verify all required model files exist
- Check PyTorch and Transformers versions are compatible

## 📝 License

This project is provided as-is for academic and research purposes.

## 🤝 Contributing

Feel free to extend this system with:
- Additional language pairs
- Model performance metrics
- Translation history
- Batch translation support
- Audio input/output

---

**Built with ❤️ for Low-Resource Language Research**
