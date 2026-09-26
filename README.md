# 🎓 Course Video RAG Assistant

A **Retrieval-Augmented Generation (RAG)** based AI assistant that helps students find **where a particular topic is taught in a video course**.

Instead of manually searching through long course videos, users can ask a question such as:

> **"Where is the CSS Box Model taught?"**

The system searches the course transcripts, finds the most relevant video segments, and uses a local LLM to generate an answer containing the **video number and relevant timestamp**.

---

## 🚀 Project Overview

The project combines:

* 🎥 Video-to-audio conversion
* 🎙️ Speech-to-text transcription
* 🌐 Hindi-to-English translation
* 🧠 Text embeddings
* 🔎 Semantic similarity search
* 🤖 Local LLM-based answer generation

### Overall Pipeline

```text
Course Videos
      │
      ▼
   FFmpeg
      │
      ▼
  MP3 Audio
      │
      ▼
   Whisper
      │
      ▼
Timestamped Transcripts
      │
      ▼
   BGE-M3
      │
      ▼
 Text Embeddings
      │
      ▼
Cosine Similarity Search
      │
      ▼
Top Relevant Chunks
      │
      ▼
  Llama 3.2
      │
      ▼
Answer + Video + Timestamp
```

---

## ✨ Features

* 🎥 Converts course videos into MP3 audio
* 🎙️ Transcribes audio using **Whisper**
* 🌐 Translates Hindi speech into English
* ⏱️ Preserves timestamps for transcript segments
* 🧠 Generates semantic embeddings using **BGE-M3**
* 🔍 Retrieves relevant content using **cosine similarity**
* 🤖 Generates answers using **Llama 3.2**
* 📍 Identifies the relevant video and timestamp
* 💻 Uses Ollama for local AI inference
* 💾 Saves processed embeddings using Joblib

---

## 🛠️ Technologies Used

| Technology   | Purpose                            |
| ------------ | ---------------------------------- |
| Python       | Main programming language          |
| FFmpeg       | Video to MP3 conversion            |
| Whisper      | Speech recognition and translation |
| Ollama       | Local model inference              |
| BGE-M3       | Text embeddings                    |
| Llama 3.2    | Answer generation                  |
| NumPy        | Numerical computation              |
| Pandas       | Data processing                    |
| Scikit-learn | Cosine similarity                  |
| Joblib       | Saving/loading embeddings          |

---

## 📁 Project Structure

```text
course-video-rag/
│
├── video_to_mp3.py
├── mp3_to_json.py
├── preprocess_json.py
├── process_incoming.py
│
├── requirements.txt
├── .gitignore
├── README.md
│
├── videos/             # Course videos - ignored by Git
├── audios/             # Generated MP3 files - ignored by Git
├── jsons/              # Generated transcripts - ignored by Git
│
├── embeddings.joblib   # Generated embeddings - ignored by Git
├── prompt.txt          # Generated prompt - ignored by Git
└── response.txt        # Generated response - ignored by Git
```

---

# ⚙️ How It Works

## 1. Video to MP3

The `video_to_mp3.py` script reads the course videos from the `videos` folder and converts them into MP3 files using FFmpeg.

```bash
python video_to_mp3.py
```

The generated audio files are stored inside the `audios` folder.

The script also extracts the tutorial number and video title from the original filename.

---

## 2. Speech-to-Text

The `mp3_to_json.py` script uses the Whisper `large-v2` model to process the generated audio files.

```bash
python mp3_to_json.py
```

The transcription is configured with:

```python
language="hi"
task="translate"
```

This means the source audio is treated as Hindi and translated into English.

The transcription is divided into timestamped segments containing information such as:

```json
{
    "number": "18",
    "title": "CSS Box Model - Margin, Padding & Borders",
    "start": 102.64,
    "end": 107.16,
    "text": "..."
}
```

The resulting transcript data is saved as JSON files.

---

## 3. Generate Embeddings

The `preprocess_json.py` script reads the generated JSON files and creates embeddings using the **BGE-M3** embedding model through Ollama.

```bash
python preprocess_json.py
```

The project communicates with Ollama through:

```text
http://localhost:11434
```

Each transcript chunk receives:

* A unique `chunk_id`
* Its corresponding embedding

The processed data is then stored in:

```text
embeddings.joblib
```

---

## 4. Ask a Question

After generating the embeddings, run:

```bash
python process_incoming.py
```

The program asks:

```text
Ask a Question:
```

For example:

```text
Ask a Question: Where is box model taught?
```

The user's question is converted into an embedding using BGE-M3.

---

## 5. Semantic Search

The question embedding is compared with the stored transcript embeddings using **cosine similarity**.

```text
User Question
      │
      ▼
Question Embedding
      │
      ▼
Cosine Similarity
      │
      ▼
Top 5 Relevant Chunks
```

The system retrieves the five most relevant transcript chunks.

Each retrieved chunk contains information such as:

* Video title
* Video number
* Start timestamp
* End timestamp
* Transcript text

---

## 6. LLM Answer Generation

The retrieved transcript chunks are passed to **Llama 3.2** through Ollama.

The model is instructed to answer the user's question in a natural way and explain:

* Which video contains the topic
* Where the topic appears
* The relevant timestamp
* Where the student should go to learn the topic

If the question is unrelated to the course, the system is instructed to restrict the response to course-related questions.

---

# 💡 Example

### User Question

```text
Where is box model taught?
```

### Retrieved Content

The system can identify relevant sections from:

```text
Video 18
CSS Box Model - Margin, Padding & Borders
```

with relevant timestamps from the transcript.

For example:

```text
Video 18
00:00 - Introduction
01:42 - Explanation of the box model
...
```

The LLM then converts this retrieved information into a natural-language answer for the student.

---

# 🧠 RAG Architecture

This project follows the basic architecture of **Retrieval-Augmented Generation**.

### Retrieval Stage

```text
Course Transcript
       │
       ▼
   BGE-M3
       │
       ▼
  Embeddings
       │
       ▼
 Stored locally
```

When a user asks a question:

```text
User Question
       │
       ▼
   BGE-M3
       │
       ▼
Question Embedding
       │
       ▼
Cosine Similarity
       │
       ▼
Top 5 Chunks
```

### Generation Stage

```text
Top Relevant Chunks
        +
 User Question
        │
        ▼
    Llama 3.2
        │
        ▼
    Final Answer
```

The LLM therefore receives relevant course content as context before generating its response.

---

# 🔧 Installation

## 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/course-video-rag.git
cd course-video-rag
```

---

## 2. Create a Virtual Environment

Windows:

```powershell
python -m venv venv
```

Activate it:

```powershell
venv\Scripts\activate
```

---

## 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

## 4. Install FFmpeg

Check whether FFmpeg is installed:

```bash
ffmpeg -version
```

If the command is not recognized, install FFmpeg and add it to your system PATH.

---

# 🤖 Ollama Setup

This project uses Ollama for local embeddings and LLM inference.

Install Ollama and download the required models:

```bash
ollama pull bge-m3
ollama pull llama3.2
```

Check the installed models:

```bash
ollama list
```

Make sure Ollama is running locally.

The Python code expects the Ollama API at:

```text
http://localhost:11434
```

---

# ▶️ Running the Project

Run the scripts in this order:

### Step 1 — Convert videos to MP3

```bash
python video_to_mp3.py
```

### Step 2 — Generate transcripts

```bash
python mp3_to_json.py
```

### Step 3 — Generate embeddings

```bash
python preprocess_json.py
```

### Step 4 — Ask questions

```bash
python process_incoming.py
```

---

# 📌 Important Notes

### Computational Requirements

The Whisper `large-v2` model can require significant computational resources, especially when processing many or long videos.

### Storage

Video, audio, transcript, and embedding files can become very large.

For this reason, generated files are excluded from the Git repository using `.gitignore`.

### Local Models

Both the embedding model and LLM are intended to run locally through Ollama.

---

# 🔮 Future Improvements

Possible improvements for the project include:

* [ ] Build a Streamlit web interface
* [ ] Add a proper vector database such as ChromaDB or FAISS
* [ ] Add clickable video timestamps
* [ ] Allow users to upload their own courses
* [ ] Support multiple courses
* [ ] Add conversation history
* [ ] Add source citations to answers
* [ ] Improve transcript chunking
* [ ] Add a reranking model
* [ ] Support additional languages
* [ ] Add GPU acceleration
* [ ] Deploy the application as a web application

---

# 🎯 Use Cases

This system can be used for:

* 📚 Searching long educational video courses
* 👨‍💻 Programming tutorials
* 🎓 AI-powered learning assistants
* 🔎 Semantic search over video transcripts
* 🤖 Educational RAG applications
* 🧠 Exploring local LLM applications

---

# 📖 Concepts Demonstrated

This project demonstrates practical implementation of:

* Retrieval-Augmented Generation (RAG)
* Large Language Models (LLMs)
* Text Embeddings
* Semantic Search
* Cosine Similarity
* Speech-to-Text
* Natural Language Processing
* Prompt Engineering
* Local LLM Inference
* Information Retrieval

---

# 👩‍💻 Author

**Snehanshi Chaudhury**

B.Tech in Information Technology

Interested in **AI, Machine Learning, Generative AI, RAG, and NLP**.

---

## ⭐ If you find this project useful

Feel free to star ⭐ the repository and explore the code.

