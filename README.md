# 🎬 Movie Recommender System

A full-stack movie recommendation application powered by machine learning, combining TF-IDF content-based filtering with TMDB API integration for real-time movie data and genre-based recommendations.

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111.0-green.svg)
![Streamlit](https://img.shields.io/badge/Streamlit-1.36.0-red.svg)
![License](https://img.shields.io/badge/license-MIT-blue.svg)

## 📋 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Documentation](#api-documentation)
- [Machine Learning Model](#machine-learning-model)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

## ✨ Features

### 🎯 Core Functionality
- **Intelligent Search**: Keyword-based movie search with autocomplete suggestions
- **Dual Recommendation Engine**:
  - Content-based filtering using TF-IDF vectorization
  - Genre-based recommendations via TMDB API
- **Real-time Data**: Live movie information including posters, ratings, and metadata
- **Interactive UI**: Clean, responsive Streamlit interface with grid-based movie displays

### 🎨 User Experience
- **Home Feed**: Browse trending, popular, top-rated, and upcoming movies
- **Detailed Movie Pages**: Comprehensive information with posters, backdrops, and overviews
- **Dynamic Navigation**: Seamless transitions between search results and movie details
- **Customizable Grid**: Adjustable column layout for optimal viewing

## 🏗️ Architecture

```
┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│                 │         │                  │         │                 │
│  Streamlit UI   │────────▶│  FastAPI Backend │────────▶│   TMDB API      │
│   (app.py)      │         │    (main.py)     │         │                 │
│                 │         │                  │         └─────────────────┘
└─────────────────┘         └──────────────────┘
                                     │
                                     │
                            ┌────────▼─────────┐
                            │                  │
                            │  ML Model Layer  │
                            │  - TF-IDF Matrix │
                            │  - Indices       │
                            │  - DataFrame     │
                            │                  │
                            └──────────────────┘
```

### Component Overview

1. **Frontend (Streamlit)**: User interface with search, navigation, and display components
2. **Backend (FastAPI)**: RESTful API handling requests, ML inference, and TMDB integration
3. **ML Model**: Pre-trained TF-IDF vectorizer for content-based recommendations
4. **External API**: TMDB integration for real-time movie data and genre recommendations

## 🛠️ Tech Stack

### Backend
- **FastAPI** (0.111.0): High-performance API framework
- **Uvicorn** (0.30.1): ASGI server
- **httpx** (0.27.0): Async HTTP client for TMDB API calls

### Frontend
- **Streamlit** (1.36.0): Interactive web application framework

### Machine Learning
- **scikit-learn** (1.5.1): TF-IDF vectorization and cosine similarity
- **pandas** (2.2.2): Data manipulation
- **numpy** (2.0.1): Numerical operations
- **scipy** (1.13.1): Sparse matrix operations

### Data Source
- **TMDB API**: Real-time movie metadata, posters, and genre information

## 📦 Installation

### Prerequisites
- Python 3.8 or higher
- pip package manager
- TMDB API key ([Get one here](https://www.themoviedb.org/settings/api))

### Step 1: Clone the Repository
```bash
git clone https://github.com/AnishGitFlow/movie-recommender-nlp-fastapi.git
cd movie-recommender
```

### Step 2: Create Virtual Environment
```bash
python -m venv venv

# On Windows
venv\Scripts\activate

# On macOS/Linux
source venv/bin/activate
```

### Step 3: Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 4: Prepare ML Model Files

The application requires pre-trained model files. Generate them using the provided Jupyter notebook:

```bash
jupyter notebook movies.ipynb
```

Run all cells to generate:
- `df.pkl` - Processed movie dataset
- `indices.pkl` - Title-to-index mapping
- `tfidf_matrix.pkl` - TF-IDF feature matrix
- `tfidf.pkl` - Trained vectorizer

Place these files in the project root directory.

## ⚙️ Configuration

### Environment Variables

Create a `.env` file in the project root:

```env
TMDB_API_KEY=your_tmdb_api_key_here
```

### API Base URL (Optional)

In `app.py`, update the API base URL if deploying:

```python
API_BASE = "https://your-api-domain.com"  # Production
# or
API_BASE = "http://127.0.0.1:8000"  # Local development
```

## 🚀 Usage

### Running Locally

#### 1. Start the FastAPI Backend
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`

#### 2. Start the Streamlit Frontend
In a new terminal:
```bash
streamlit run app.py
```

The application will open in your browser at `http://localhost:8501`

### Using the Application

1. **Home Feed**:
   - Select a category (trending, popular, top-rated, etc.)
   - Adjust grid columns using the sidebar slider
   - Click on any movie poster to view details

2. **Search**:
   - Type a keyword in the search box
   - Select from dropdown suggestions or browse grid results
   - Click "Open" to view movie details

3. **Movie Details**:
   - View comprehensive information (poster, overview, genres)
   - Explore TF-IDF recommendations (similar plot/themes)
   - Browse genre-based recommendations (same category)

## 📚 API Documentation

### Endpoints

#### Health Check
```http
GET /health
```

**Response:**
```json
{
  "status": "ok"
}
```

#### Home Feed
```http
GET /home?category=popular&limit=24
```

**Parameters:**
- `category`: `trending` | `popular` | `top_rated` | `upcoming` | `now_playing`
- `limit`: Number of results (1-50)

#### Movie Search
```http
GET /tmdb/search?query=avatar&page=1
```

**Parameters:**
- `query`: Search keyword
- `page`: Page number (1-10)

#### Movie Details
```http
GET /movie/id/{tmdb_id}
```

**Response:**
```json
{
  "tmdb_id": 19995,
  "title": "Avatar",
  "overview": "...",
  "release_date": "2009-12-18",
  "poster_url": "https://image.tmdb.org/t/p/w500/...",
  "backdrop_url": "https://image.tmdb.org/t/p/w500/...",
  "genres": [{"id": 28, "name": "Action"}, ...]
}
```

#### Genre Recommendations
```http
GET /recommend/genre?tmdb_id=19995&limit=18
```

#### TF-IDF Recommendations
```http
GET /recommend/tfidf?title=Avatar&top_n=10
```

#### Bundle (Details + All Recommendations)
```http
GET /movie/search?query=Avatar&tfidf_top_n=12&genre_limit=12
```

Returns movie details, TF-IDF recommendations, and genre recommendations in one response.

### Interactive API Docs

Once the backend is running, visit:
- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`

## 🧠 Machine Learning Model

### Algorithm: TF-IDF Content-Based Filtering

The recommendation system uses **Term Frequency-Inverse Document Frequency (TF-IDF)** vectorization to analyze movie content similarity.

#### Data Processing Pipeline

1. **Feature Engineering** (`movies.ipynb`):
   ```python
   # Combine relevant text features
   df['tags'] = df['overview'] + ' ' + df['genres'] + ' ' + df['keywords']
   ```

2. **TF-IDF Vectorization**:
   ```python
   from sklearn.feature_extraction.text import TfidfVectorizer
   
   tfidf = TfidfVectorizer(
       max_features=50000,
       ngram_range=(1, 2),
       stop_words='english'
   )
   tfidf_matrix = tfidf.fit_transform(df['tags'])
   ```

3. **Similarity Computation**:
   ```python
   from sklearn.metrics.pairwise import cosine_similarity
   
   similarity_scores = cosine_similarity(tfidf_matrix[idx], tfidf_matrix)
   ```

### How It Works

1. **Input**: User selects a movie
2. **Feature Extraction**: Convert movie metadata to TF-IDF vector
3. **Similarity Calculation**: Compute cosine similarity with all other movies
4. **Ranking**: Sort by similarity score (descending)
5. **Output**: Return top N most similar movies

### Hybrid Approach

The system combines two recommendation strategies:

| Method | Basis | Use Case |
|--------|-------|----------|
| **TF-IDF** | Plot, keywords, themes | Find movies with similar content |
| **Genre-based** | TMDB genre classification | Discover popular movies in same category |

## 📁 Project Structure

```
movie-recommender/
│
├── app.py                  # Streamlit frontend application
├── main.py                 # FastAPI backend server
├── movies.ipynb            # Jupyter notebook for model training
├── requirements.txt        # Python dependencies
├── .env                    # Environment variables (create this)
│
├── df.pkl                  # Processed movie dataset (generate via notebook)
├── indices.pkl             # Title-to-index mapping (generate via notebook)
├── tfidf_matrix.pkl        # TF-IDF feature matrix (generate via notebook)
├── tfidf.pkl              # Trained vectorizer (generate via notebook)
│
└── README.md              # Project documentation
```

## 🔧 Deployment

### Deploying to Render (Backend)

1. Create a new Web Service on [Render](https://render.com)
2. Connect your GitHub repository
3. Configure build settings:
   ```
   Build Command: pip install -r requirements.txt
   Start Command: uvicorn main:app --host 0.0.0.0 --port $PORT
   ```
4. Add environment variable: `TMDB_API_KEY`
5. Upload `.pkl` files to persistent disk (if using)

### Deploying Streamlit (Frontend)

1. Deploy via [Streamlit Community Cloud](https://streamlit.io/cloud)
2. Update `API_BASE` in `app.py` to your Render backend URL
3. Connect GitHub repository and deploy

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

### Code Style
- Follow PEP 8 guidelines
- Add docstrings to functions
- Include type hints where applicable
- Write meaningful commit messages

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **TMDB API**: Movie data and images
- **Kaggle**: Dataset source ([Movies Metadata](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset))
- **scikit-learn**: Machine learning algorithms
- **FastAPI & Streamlit**: Excellent frameworks for rapid development

Made with ❤️ by Anish
