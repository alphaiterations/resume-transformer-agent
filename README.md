# 🚀 Building an Intelligent Resume Transformer with LangGraph and GPT-4o-mini

*Transform unstructured resumes into structured, searchable data using AI-powered agents*

---

## The Problem: Resume Chaos in Modern Hiring

Every recruiter knows the pain: hundreds of resumes in different formats (PDF, DOCX, TXT), inconsistent layouts, varying structures, and no easy way to search through them. You need to find "Python developers with 5+ years experience" but you're stuck manually reading through each resume.

**What if we could automatically transform any resume into a structured, searchable database?**

That's exactly what this Resume Transformer Agent does.

---

## 🎯 What This Project Does

This is an **intelligent resume processing pipeline** built with **LangGraph** and **OpenAI's GPT-4o-mini** that:

1. **Parses** resumes from multiple formats (PDF, DOCX, TXT) — even image-based PDFs with OCR
2. **Extracts** structured data using AI (contact info, experience, education, skills)
3. **Validates & Enriches** the data (calculates years of experience, standardizes skills)
4. **Stores** everything in a queryable SQLite database

The result? A fully automated Applicant Tracking System (ATS) that can process resumes at scale.

---

## 📁 Project Structure

```
resume-transformer-agent/
│
├── README.md                          # This file - comprehensive project documentation
├── requirements.txt                   # Python dependencies
├── .env                              # Environment variables (API keys) - not in repo
├── .gitignore                        # Git ignore rules
│
├── resume-transformer-agent.ipynb    # Main Jupyter notebook with complete workflow
│
├── data/                             # Sample resume files for testing
│   ├── *.pdf                        # PDF resume samples
│   ├── *.docx                       # DOCX resume samples
│   └── *.txt                        # Text resume samples
│
└── resume_ats.db                     # SQLite database (auto-generated)
    ├── candidates table              # Main candidate information
    ├── experience table              # Work history entries
    ├── education table               # Educational background
    ├── skills table                  # Normalized skills
    └── certifications table          # Professional certifications
```

### Key Files

- **`resume-transformer-agent.ipynb`**: The main notebook containing all four agents (Parse, Extract, Validate & Enrich, Store), workflow orchestration, and example usage
- **`requirements.txt`**: All Python dependencies including LangGraph, OpenAI, PyPDF2, python-docx, and OCR libraries
- **`.env`**: Store your OpenAI API key here (create this file locally)
- **`data/`**: Drop your resume files here for processing
- **`resume_ats.db`**: Auto-generated SQLite database with structured candidate data

---

## ✨ Key Features

### 🔍 Multi-Format Resume Parsing
- **PDF Support**: Extract text from regular PDFs
- **OCR Support**: Process image-based PDFs using Tesseract
- **DOCX & TXT**: Handle Microsoft Word documents and plain text files

### 🤖 AI-Powered Data Extraction
- Uses **GPT-4o-mini** to intelligently extract structured data
- Handles various resume formats and layouts
- Normalizes data to consistent schema

### 🛠️ Intelligent Data Processing
- **Automatic Experience Calculation**: Computes total years of experience
- **Skill Standardization**: Maps skills to company taxonomy (Python, React, AWS, etc.)
- **Data Validation**: Cleans contact info, validates emails, formats dates

### 💾 Structured Database Storage
- **SQLite Database**: Efficient, portable storage
- **Normalized Schema**: Separate tables for candidates, experience, education, skills
- **Query Support**: Search by skills, experience, location, and more

### 📊 Batch Processing
- Process multiple resumes at once
- Progress tracking and error handling
- Detailed reporting of successes and failures

---

## 🏗️ Architecture: The Four-Agent Workflow

This project uses **LangGraph** to orchestrate four specialized agents in a sequential workflow:

```
┌──────────────┐
│   START      │
└──────┬───────┘
       │
       ▼
┌─────────────────┐
│  1. PARSE       │  ← Extract raw text from file
│  Agent          │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  2. EXTRACT     │  ← Use GPT-4o-mini to structure data
│  Agent          │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  3. VALIDATE    │  ← Clean, enrich, standardize
│  & ENRICH       │
│  Agent          │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  4. STORE       │  ← Insert into database
│  Agent          │
└────────┬────────┘
         │
         ▼
┌──────────────┐
│    END       │
└──────────────┘
```

### Agent 1: Parse Agent 📄
**Responsibility**: Extract raw text from resume files

- Handles PDF, DOCX, and TXT formats
- Falls back to OCR for image-based PDFs
- Robust error handling

### Agent 2: Extract Agent 🤖
**Responsibility**: Transform raw text into structured JSON

- Uses GPT-4o-mini for intelligent extraction
- Follows strict JSON schema
- Extracts: contact info, summary, experience, education, skills, certifications

### Agent 3: Validate & Enrich Agent ✨
**Responsibility**: Clean and enhance the data

- Validates email addresses and phone numbers
- Calculates total years of experience
- Standardizes skills to company taxonomy
- Adds metadata (processing date, version)

### Agent 4: Store Agent 💾
**Responsibility**: Persist data to database

- Creates SQLite database if it doesn't exist
- Inserts candidate data across normalized tables
- Returns database ID for reference

---

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- OpenAI API key
- (Optional) Tesseract for OCR support

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/resume-transformer-agent.git
cd resume-transformer-agent
```

2. **Install dependencies**
```bash
pip install -r requirements.txt
```

3. **Set up your OpenAI API key**
```bash
# Create a .env file
echo "OPENAI_API_KEY=your-api-key-here" > .env
```

4. **(Optional) Install OCR support**
```bash
# macOS
brew install tesseract

# Ubuntu/Debian
sudo apt-get install tesseract-ocr

# Then install Python packages
pip install pytesseract pdf2image pillow
```

### Quick Start

Open the Jupyter notebook and run:

```python
from pathlib import Path

# Process a single resume
result = process_resume("data/sample_resume.pdf")

if result['success']:
    print(f"✅ Processed: {result['candidate_name']}")
    print(f"📊 Database ID: {result['database_id']}")
```

---

## 📖 Usage Examples

### Process a Single Resume

```python
result = process_resume("path/to/resume.pdf")

if result['success']:
    validated_data = result['state']['validated_data']
    print(f"Candidate: {validated_data['contact']['name']}")
    print(f"Experience: {validated_data['total_years_experience']} years")
    print(f"Skills: {', '.join(validated_data['skills'][:5])}")
```

### Batch Process Multiple Resumes

```python
# Process first 10 resumes from data folder
results = batch_process_resumes("data", limit=10)

print(f"✅ Successful: {results['successful']}")
print(f"❌ Failed: {results['failed']}")
```

### Query the Database

```python
# Find Python developers
search_by_skill("Python")

# Find candidates with 5+ years experience
search_by_experience(5.0)

# View all candidates
query_database()
```

---

## 🗄️ Database Schema

The system uses a normalized SQLite schema:

### `candidates` table
- Basic info: name, email, phone, location
- Links: LinkedIn, GitHub
- Metadata: total experience, processing date

### `experience` table
- Work history entries
- Company, title, dates, description
- Foreign key to candidates

### `education` table
- Educational background
- Institution, degree, field, graduation year
- Foreign key to candidates

### `skills` table
- Individual skills (normalized)
- Foreign key to candidates

### `certifications` table
- Professional certifications
- Foreign key to candidates

---

## 🎨 Advanced Features

### Skill Standardization

The system automatically maps skills to a standard taxonomy:

```python
'python' → 'Python'
'reactjs' → 'React'
'k8s' → 'Kubernetes'
'ml' → 'Machine Learning'
```

This ensures consistent skill matching across resumes.

### Experience Calculation

Automatically calculates total years of experience from work history:

```python
Job 1: 2020-01 to 2022-06  → 2.5 years
Job 2: 2022-07 to Present   → 2.5 years
Total: 5.0 years
```

### OCR Support

For image-based PDFs (scanned resumes), the system automatically:
1. Detects no text extraction
2. Converts PDF pages to images
3. Applies Tesseract OCR
4. Extracts text from images

---

## 🔧 Configuration

### Customize Skill Taxonomy

Edit the `STANDARD_SKILLS` dictionary in the notebook to add your company's skill taxonomy:

```python
STANDARD_SKILLS = {
    'your-skill': 'Your Skill',
    'another-skill': 'Another Skill',
    # ... add more
}
```

### Adjust LLM Parameters

Modify the GPT-4o-mini parameters in `extract_agent()`:

```python
response = client.chat.completions.create(
    model="gpt-4o-mini",
    temperature=0.1,  # Lower = more deterministic
    # ... other parameters
)
```

---

## 📊 Example Output

```
🚀 RESUME TRANSFORMER WORKFLOW
================================================================================
🔍 PARSE AGENT: Processing data/resume.pdf
   ✓ Extracted 2847 characters

🤖 EXTRACT AGENT: Analyzing resume with gpt-4o-mini
   ✓ Extracted data for: John Doe

✨ VALIDATE & ENRICH AGENT: Processing data
   ✓ Calculated 7.5 years of experience
   ✓ Standardized 18 skills to 15 unique skills

💾 STORE AGENT: Saving to database
   ✓ Stored candidate with ID: 42

================================================================================
📊 WORKFLOW SUMMARY
================================================================================
  ✓ Successfully parsed 2847 characters from PDF
  ✓ Successfully extracted structured data using GPT-4o-mini
  ✓ Successfully validated and enriched data
  ✓ Successfully stored candidate with ID 42

  Final Status: COMPLETED
  Database ID: 42
================================================================================
```

---

## 🧪 Testing

The notebook includes test cells for:
- Single resume processing
- Batch processing
- Database queries
- Skill searches
- Experience filters

Run all cells to test the complete workflow.

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| **Orchestration** | LangGraph |
| **LLM** | OpenAI GPT-4o-mini |
| **PDF Parsing** | PyPDF2 |
| **DOCX Parsing** | python-docx |
| **OCR** | Tesseract + pdf2image |
| **Database** | SQLite3 |
| **Environment** | Python 3.8+ |

---

## 🚦 Workflow States

Each resume passes through these states:

1. **processing** → Initial state
2. **parsed** → Text extracted
3. **extracted** → Structured data created
4. **validated** → Data cleaned and enriched
5. **completed** → Stored in database

Any errors transition to **failed** state with detailed error messages.

---

## 🎯 Use Cases

### Recruiting & HR
- Automated resume screening
- Build searchable candidate database
- Quick skill-based filtering

### Job Boards
- Standardize resume data
- Improve search accuracy
- Better candidate matching

### Career Services
- Resume analysis at scale
- Track alumni career paths
- Generate insights on skill trends

### Research
- Labor market analysis
- Skill demand trends
- Career progression patterns

---

## 📈 Performance Considerations

- **Processing Time**: ~5-15 seconds per resume (depending on LLM latency)
- **Cost**: ~$0.001-0.003 per resume (GPT-4o-mini pricing)
- **Accuracy**: ~95% for structured resumes, ~85% for creative layouts
- **OCR**: Adds 10-30 seconds per resume for image-based PDFs

---

## 🔮 Future Enhancements

- [ ] Support for more file formats (RTF, HTML)
- [ ] Enhanced skill matching with embeddings
- [ ] Resume scoring and ranking
- [ ] Duplicate detection
- [ ] RESTful API wrapper
- [ ] Web interface
- [ ] Support for other LLMs (Claude, Llama)
- [ ] Resume comparison and analytics

---

## 🤝 Contributing

Contributions are welcome! Areas for improvement:

1. **Parser enhancements**: Better OCR, more formats
2. **Schema improvements**: Additional fields, relationships
3. **Query capabilities**: More search options
4. **Performance**: Parallel processing, caching
5. **Testing**: Unit tests, integration tests

---

## 📝 License

MIT License - feel free to use this project for your own applications!

---

## 🙏 Acknowledgments

Built with:
- [LangGraph](https://github.com/langchain-ai/langgraph) for workflow orchestration
- [OpenAI](https://openai.com/) for GPT-4o-mini
- [PyPDF2](https://pypdf2.readthedocs.io/) for PDF parsing
- [Tesseract](https://github.com/tesseract-ocr/tesseract) for OCR

---

## 📧 Contact

Questions? Issues? Reach out or open an issue on GitHub!

---

**Happy Resume Processing! 🎉**

*Transform chaos into structure, one resume at a time.*
