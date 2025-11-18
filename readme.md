Python Parallel Text Handling Processor

A high-performance, scalable text processing system designed to handle large volumes of support tickets, logs, and feedback. It uses parallel processing (threading) to clean, score, and store text data efficiently, complete with a Streamlit Dashboard and Automated Feedback Loops.

🏗️ System Architecture

This project follows a modular pipeline architecture. Data flows from raw input through a parallel engine, is deduplicated using hashing, stored in SQLite, and analyzed for rule improvements.

graph TD
    %% --- STYLES ---
    classDef input fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef process fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef storage fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef output fill:#fce4ec,stroke:#880e4f,stroke-width:2px;
    classDef feedback fill:#f3e5f5,stroke:#4a148c,stroke-width:2px,stroke-dasharray: 5 5;

    %% --- INPUT LAYER ---
    subgraph Inputs ["1. Input Layer"]
        RawFiles[("📄 Raw Text Files")]:::input
        RulesConfig[("⚙️ Rules Config")]:::input
        StreamlitUpload[("📤 Streamlit UI")]:::input
    end

    %% --- PROCESSING PIPELINE ---
    subgraph Pipeline ["2. Parallel Processing Engine"]
        Loader["text_loader.py"]:::process
        Breaker["text_breaker.py"]:::process
        
        subgraph ParallelCore ["⚡ Parallel Execution"]
            Orchestrator["parallel_break_loader2.py"]:::process
            Worker1["checker2.py"]:::process
            Worker2["checker2.py"]:::process
        end
    end

    %% --- STORAGE LAYER ---
    subgraph Warehouse ["3. Storage & Deduplication"]
        StorageMgr["storage2.py"]:::storage
        HashCheck{"Duplicate Check"}:::storage
        DB[("🗄️ SQLite Database")]:::storage
    end

    %% --- OPTIMIZATION LAYER ---
    subgraph Optimizer ["4. Feedback Loop"]
        Improver["storage_improver2.py"]:::feedback
    end

    %% --- OUTPUT LAYER ---
    subgraph Interface ["5. User Interface & Output"]
        RunScript["run.py"]:::output
        Dashboard["streamlit_app3.py"]:::output
        Emailer["emailer.py"]:::output
    end

    %% --- CONNECTIONS ---
    RawFiles --> Loader
    StreamlitUpload --> Loader
    Loader --> Breaker
    Breaker --> Orchestrator
    RulesConfig -.-> Orchestrator
    Orchestrator --> Worker1 & Worker2
    Worker1 & Worker2 --> StorageMgr
    StorageMgr --> HashCheck
    HashCheck --> |No (New)| DB
    RunScript --> Loader & Emailer
    Dashboard --> DB & RulesConfig
    Improver --> DB
    Improver --> RulesConfig


🚀 Key Features

Parallel Processing: Uses ThreadPoolExecutor to process multiple text chunks simultaneously.

Rule-Based Scoring: Configurable JSON rules (rules1.json) to score text based on keywords, regex, or phrases.

Smart Deduplication: Calculates SHA256 hashes of text chunks to prevent storing duplicate data.

Interactive Dashboard: A Streamlit app to manage files, view analytics, and edit rules.

Automated Feedback Loop: The StorageImprover analyzes processed data to suggest new rules based on frequent patterns.

Reporting: Automatic CSV exports and SMTP email alerts.

📂 Project Structure

.
├── app/
│   ├── checker/            # Logic for scoring (checker2.py, rules.py)
│   ├── search_export/      # Reporting tools (search_save.py, emailer.py)
│   ├── storage/            # DB management (storage2.py, storage_improver2.py)
│   ├── text_processing/    # Loading & Breaking text (parallel_break_loader2.py)
│   └── utils.py            # Helpers (Logging, Hashing)
├── data/
│   ├── rules1.json         # Configuration file for scoring rules
│   └── support_text_files/ # Folder for raw input text files
├── run.py                  # Main script for automated execution
├── streamlit_app3.py       # Interactive Web Dashboard
└── requirements.txt        # Dependencies


🛠️ Installation & Setup

Clone the repository:

git clone <your-repo-url>
cd ParallelTextProject


Install dependencies:

pip install -r requirements.txt


Prepare Configuration:
Ensure data/rules1.json exists. You can use the default one provided in the repo.

🖥️ Usage

Option 1: Interactive Dashboard (Recommended)

Launch the web interface to upload files, visualize data, and edit rules.

streamlit run streamlit_app3.py


Option 2: Automated Pipeline

Run the full pipeline (Load -> Score -> Save -> Email) via the command line.

python run.py


⚙️ Configuration (Rules)

The logic of the system is defined in data/rules1.json. You can edit this file to change how text is scored.

Example Rule:

{
  "id": 2,
  "label": "refund_request",
  "type": "keyword_any",
  "keywords": ["refund", "money back", "return"],
  "score": -3.0
}


📊 Performance

This system is optimized for speed.

Chunking: Breaks large files into 500-word groups.

Hashing: Checks database existence in O(1) time using hashes.

Threading: scalable to available CPU cores.

Run the benchmark test to check your system's performance:

python app/performance_tests/perfomance_test.py
