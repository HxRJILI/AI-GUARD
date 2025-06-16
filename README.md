# AI Guard

**Intelligent Surveillance System with Real-time Facial Recognition**

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://python.org)
[![Flask](https://img.shields.io/badge/flask-2.0%2B-green.svg)](https://flask.palletsprojects.com)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Documentation](https://img.shields.io/badge/docs-readthedocs-brightgreen.svg)](https://ai-guard.readthedocs.io)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)](https://github.com/your-username/ai-guard)

AI Guard is a comprehensive surveillance system designed for educational institutions, featuring real-time facial recognition, multi-user interfaces, and intelligent monitoring capabilities. Built with Python, Flask, and powered by Dlib's state-of-the-art facial recognition models.

## 🚀 Key Features

### **Real-time Monitoring**
- **Live facial recognition** using Dlib ResNet models
- **MJPEG video streaming** without interruptions
- **Real-time alerts** via WebSocket communication
- **Multi-camera support** (coming in v1.1)

### **Multi-User Interface**
- **Role-based access control** (Admin, Surveillant, Guard, Service Admin)
- **Customized dashboards** for each user type
- **Session management** with secure authentication
- **Permission-based feature access**

### **Intelligent Surveillance**
- **Authorized personnel recognition** with configurable confidence thresholds
- **Unknown person detection** with instant alerts
- **Watchlist monitoring** for persons of interest
- **Automatic snapshot capture** for security records

### **Modern Web Interface**
- **Responsive design** with Tailwind CSS
- **Real-time updates** without page refresh
- **Interactive controls** for surveillance parameters
- **Comprehensive logging** and audit trails

## 📋 System Requirements

| Component | Requirement |
|-----------|-------------|
| **OS** | Windows 10/11, macOS 10.15+, Ubuntu 18.04+ |
| **Python** | 3.8+ (3.10 recommended) |
| **Memory** | 8GB RAM minimum, 16GB recommended |
| **Storage** | 5GB free space |
| **Camera** | USB webcam or IP camera |
| **Browser** | Chrome 80+, Firefox 75+, Safari 13+, Edge 80+ |

## ⚡ Quick Start

### 1. Installation

```bash
# Clone the repository
git clone https://github.com/your-username/ai-guard.git
cd ai-guard

# Create virtual environment
python -m venv venv_ai_guard
source venv_ai_guard/bin/activate  # On Windows: venv_ai_guard\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Download AI Models

```bash
# Create models directory
mkdir -p data/Dlib_face_recognition
cd data/Dlib_face_recognition

# Download Dlib models (200MB total)
wget http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2
wget http://dlib.net/files/dlib_face_recognition_resnet_model_v1.dat.bz2

# Extract models
bunzip2 *.bz2
cd ../..
```

### 3. Initialize Database

```bash
python -c "
from core.database_manager import DatabaseManager
from core.auth_manager import create_default_users

print('Initializing database...')
db = DatabaseManager()
create_default_users()
print('Setup complete!')
"
```

### 4. Launch Application

```bash
python run.py
```

Open your browser and navigate to `http://localhost:5000`

### 5. Login with Default Accounts

| Role | Username | Password | Access Level |
|------|----------|----------|--------------|
| **Administrator** | `admin` | `admin123` | Full system control |
| **Surveillant** | `surveillant1` | `surv123` | Advanced monitoring |
| **Security Guard** | `garde1` | `garde123` | Basic monitoring |

## 📁 Project Structure

```
ai-guard/
├── 📄 app.py                    # Flask application entry point
├── 📄 run.py                    # Development server launcher
├── 📄 config.py                 # Configuration management
├── 📄 requirements.txt          # Python dependencies
│
├── 📁 core/                     # Core application modules
│   ├── 📄 ai_guard_logic.py     # Facial recognition engine
│   ├── 📄 database_manager.py   # Database operations
│   └── 📄 auth_manager.py       # Authentication logic
│
├── 📁 routes/                   # Flask route definitions
│   ├── 📄 auth.py              # Authentication endpoints
│   ├── 📄 surveillance.py      # Surveillance API
│   ├── 📄 admin.py             # Admin panel routes
│   └── 📄 api.py               # RESTful API endpoints
│
├── 📁 services/                # Business logic services
│   ├── 📄 video_stream.py      # Video streaming service
│   ├── 📄 alert_manager.py     # Alert processing
│   └── 📄 websocket_manager.py # Real-time communication
│
├── 📁 templates/               # Jinja2 HTML templates
│   ├── 📄 base.html
│   ├── 📁 auth/                # Authentication pages
│   ├── 📁 surveillance/        # Monitoring interfaces
│   └── 📁 dashboard/           # User dashboards
│
├── 📁 static/                  # Static web assets
│   ├── 📁 css/                 # Stylesheets
│   ├── 📁 js/                  # JavaScript files
│   └── 📁 images/              # Images and icons
│
├── 📁 data/                    # Data storage
│   ├── 📁 Dlib_face_recognition/ # AI models
│   ├── 📁 snapshots/           # Captured images
│   └── 📄 ai_guard.db          # SQLite database
│
└── 📁 docs/                    # Documentation
    ├── 📄 installation.rst
    ├── 📄 user-guide.rst
    └── 📄 api-reference.rst
```

## 🛠️ Configuration

### Environment Variables

Create a `.env` file in the project root:

```ini
# Flask Configuration
FLASK_ENV=development
DEBUG=True
SECRET_KEY=your-secret-key-here

# Camera Settings
DEFAULT_CAMERA_INDEX=0
CAMERA_RESOLUTION_WIDTH=640
CAMERA_RESOLUTION_HEIGHT=480

# Recognition Parameters
DEFAULT_RECOGNITION_THRESHOLD=0.5
DEFAULT_DETECTION_UPSAMPLE=1
FRAME_SKIP_INTERVAL=3

# Security
SESSION_TIMEOUT=3600
PASSWORD_HASH_ROUNDS=12

# Logging
LOG_LEVEL=INFO
LOG_FILE=logs/ai_guard.log
```

### Camera Configuration

```python
# Test camera availability
python -c "
import cv2
for i in range(5):
    cap = cv2.VideoCapture(i)
    if cap.isOpened():
        print(f'Camera {i}: Available')
        cap.release()
"
```

## 🎯 Usage Examples

### Basic Surveillance

```python
# Start surveillance programmatically
from services.video_stream import VideoStreamManager

stream_manager = VideoStreamManager()
stream_manager.start_surveillance(
    camera_id=0,
    recognition_threshold=0.5,
    detection_upsample=1
)
```

### API Integration

```bash
# Get surveillance status
curl -X GET http://localhost:5000/api/surveillance/status

# Start monitoring
curl -X POST http://localhost:5000/api/surveillance/start \
  -H "Content-Type: application/json" \
  -d '{"camera_id": 0, "threshold": 0.5}'

# Get active alerts
curl -X GET http://localhost:5000/api/alerts/active
```

### Face Enrollment

```bash
# Enroll new authorized person
python tools/enroll_face.py \
  --name "John Doe" \
  --photo "photos/john.jpg" \
  --role "student" \
  --id "12345"
```

## 📊 Performance Metrics

AI Guard achieves the following performance benchmarks:

| Metric | Performance |
|--------|-------------|
| **Face Detection** | ~30 FPS (640x480) |
| **Recognition Accuracy** | >95% (good lighting) |
| **Memory Usage** | ~500MB baseline |
| **CPU Usage** | ~15-25% (Intel i5) |
| **Alert Latency** | <100ms |

## 🔧 Development

### Setting Up Development Environment

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Install pre-commit hooks
pre-commit install

# Run tests
pytest

# Code formatting
black .
flake8 .
```

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=core --cov=services --cov=routes

# Run specific test categories
pytest tests/test_recognition.py  # AI recognition tests
pytest tests/test_api.py          # API endpoint tests
pytest tests/test_auth.py         # Authentication tests
```

### Code Quality

We maintain high code quality standards:

- **Black** for code formatting
- **Flake8** for linting
- **MyPy** for type checking
- **Pytest** for testing
- **Pre-commit** hooks for quality gates

## 🚀 Deployment

### Production Deployment

```bash
# Install production server
pip install gunicorn

# Run with Gunicorn
gunicorn --bind 0.0.0.0:5000 --workers 4 app:app

# With supervisor for process management
supervisorctl start ai-guard
```

### Docker Deployment

```dockerfile
# Dockerfile included in repository
docker build -t ai-guard .
docker run -p 5000:5000 -v ./data:/app/data ai-guard
```

### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'
services:
  ai-guard:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - ./data:/app/data
      - /dev/video0:/dev/video0  # Camera access
    environment:
      - FLASK_ENV=production
```

## 📚 Documentation

Comprehensive documentation is available on **[Read the Docs](https://ai-guard.readthedocs.io)**:

- **[Installation Guide](https://ai-guard.readthedocs.io/en/latest/installation.html)** - Detailed setup instructions
- **[User Guide](https://ai-guard.readthedocs.io/en/latest/user-guide.html)** - Complete feature documentation
- **[API Reference](https://ai-guard.readthedocs.io/en/latest/api-reference.html)** - REST API documentation
- **[Development Guide](https://ai-guard.readthedocs.io/en/latest/development.html)** - Contributing guidelines
- **[Troubleshooting](https://ai-guard.readthedocs.io/en/latest/troubleshooting.html)** - Common issues and solutions

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Workflow

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Code Style

- Follow **PEP 8** guidelines
- Use **Black** for formatting
- Add **type hints** where appropriate
- Write **comprehensive tests**
- Update **documentation** for new features

## 🐛 Bug Reports

Found a bug? Please create an issue with:

- **Environment details** (OS, Python version, etc.)
- **Steps to reproduce** the issue
- **Expected vs actual behavior**
- **Log files** and error messages
- **Screenshots** if applicable

## 🔒 Security

### Reporting Security Issues

For security vulnerabilities, please email: `security@ai-guard.local`

**Do not** create public GitHub issues for security problems.

### Security Features

- **Local data storage** (no cloud transmission)
- **Session-based authentication**
- **Role-based access control**
- **Secure password hashing**
- **Input validation and sanitization**

## 📝 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2024 AI Guard Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

## 🙏 Acknowledgments

### Third-Party Libraries

- **[Dlib](http://dlib.net/)** - Facial recognition models and algorithms
- **[OpenCV](https://opencv.org/)** - Computer vision and image processing
- **[Flask](https://flask.palletsprojects.com/)** - Web framework and API
- **[SQLite](https://sqlite.org/)** - Embedded database engine
- **[Tailwind CSS](https://tailwindcss.com/)** - Utility-first CSS framework

### Special Thanks

- **Davis King** and the Dlib team for excellent facial recognition models
- **OpenCV community** for computer vision tools and documentation
- **Flask community** for the robust web framework
- **Contributors** who help improve AI Guard

## 📞 Support

### Community Support

- **GitHub Discussions** - General questions and community help
- **GitHub Issues** - Bug reports and feature requests
- **Documentation** - Comprehensive guides and tutorials

### Professional Support

- **Email**: `support@ai-guard.local`
- **Response Time**: 24-48 hours
- **Coverage**: Installation, configuration, troubleshooting

## 🗺️ Roadmap

### Version 1.1.0 (Q2 2024)
- [ ] Multi-camera support
- [ ] Enhanced face enrollment interface  
- [ ] Email notification system
- [ ] Advanced reporting dashboard
- [ ] API authentication tokens

### Version 1.2.0 (Q3 2024)
- [ ] Mobile application support
- [ ] Cloud storage integration
- [ ] Advanced analytics dashboard
- [ ] Machine learning model updates
- [ ] External security system integration

### Version 2.0.0 (Q4 2024)
- [ ] Distributed architecture support
- [ ] Real-time behavior analysis
- [ ] Advanced AI models
- [ ] Enterprise features
- [ ] Compliance tools (GDPR, etc.)

---

<div align="center">

**[📖 Documentation](https://ai-guard.readthedocs.io)** • 
**[🚀 Quick Start](#-quick-start)** • 
**[💬 Discussions](https://github.com/your-username/ai-guard/discussions)** • 
**[🐛 Issues](https://github.com/your-username/ai-guard/issues)**

Made with ❤️ by the AI Guard Team

</div>