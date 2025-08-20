# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

**Start the application:**
```bash
# Quick start with the provided script
chmod +x run.sh
./run.sh

# Or manual start
cd backend
uv run uvicorn app:app --reload --port 8000
```

**Install dependencies:**
```bash
uv sync
```

**Environment setup:**
Create a `.env` file in the root directory with:
```
ANTHROPIC_API_KEY=your_anthropic_api_key_here
```

The application runs at `http://localhost:8000` with API documentation at `http://localhost:8000/docs`.

## Architecture Overview

This is a full-stack RAG (Retrieval-Augmented Generation) chatbot system for querying course materials:

### Backend Architecture (Python/FastAPI)
- **RAGSystem** (`rag_system.py`) - Main orchestrator coordinating all components
- **VectorStore** (`vector_store.py`) - ChromaDB-based vector storage with two collections:
  - `course_catalog`: Course metadata for semantic search
  - `course_content`: Chunked course content for retrieval
- **AIGenerator** (`ai_generator.py`) - Anthropic Claude API integration with tool support
- **DocumentProcessor** (`document_processor.py`) - Text chunking and course parsing
- **SessionManager** (`session_manager.py`) - Conversation history management
- **ToolManager** (`search_tools.py`) - Tool-based search system for AI function calls

### Key Components Integration
1. **Document Processing Flow**: Raw documents → courses/lessons → chunks → vector embeddings
2. **Query Processing**: User query → tool-based search → context retrieval → AI generation
3. **Two-Stage Search**: Course name resolution via catalog, then content search with filters
4. **Session Management**: Conversation context maintained across exchanges

### Frontend (Vanilla JS/HTML/CSS)
- Single-page application with chat interface
- Real-time course statistics display
- Markdown rendering for AI responses
- Session-based conversation flow

### Configuration
All settings centralized in `config.py` with environment variable support:
- Model settings (Claude Sonnet 4, MiniLM embeddings)
- Chunk sizes (800 chars, 100 overlap)
- Database paths and API keys

### Data Models
Key entities defined in `models.py`:
- **Course**: Title, instructor, lessons, links
- **Lesson**: Number, title, lesson-specific links
- **CourseChunk**: Content pieces with course/lesson metadata

## Key Development Notes

- The system uses tool-based search where Claude AI calls search functions rather than direct RAG retrieval
- ChromaDB persistence at `./chroma_db` 
- Documents are automatically loaded from `../docs` on startup
- FastAPI serves both API endpoints (`/api/*`) and static frontend files
- Session management tracks conversation history with configurable limits
- Vector search includes both fuzzy course name matching and filtered content search
- always run uv to run the server do not use pip directly
- make sure you use uv to manage all the dependencies
- use uv to run any python file