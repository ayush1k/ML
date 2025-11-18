graph TD
    %% --- STYLES ---
    classDef input fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef process fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef storage fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef output fill:#fce4ec,stroke:#880e4f,stroke-width:2px;
    classDef feedback fill:#f3e5f5,stroke:#4a148c,stroke-width:2px,stroke-dasharray: 5 5;

    %% --- INPUT LAYER ---
    subgraph Inputs ["1. Input Layer"]
        RawFiles[("📄 Raw Text Files\n(.txt)")]:::input
        RulesConfig[("⚙️ Rules Config\n(rules1.json)")]:::input
        StreamlitUpload[("📤 Streamlit UI\nUpload")]:::input
    end

    %% --- PROCESSING PIPELINE ---
    subgraph Pipeline ["2. Parallel Processing Engine"]
        Loader["text_loader.py\n(File Reader)"]:::process
        Breaker["text_breaker.py\n(Cleaner & Chunker)"]:::process
        
        subgraph ParallelCore ["⚡ Parallel Execution (ThreadPool)"]
            Orchestrator["parallel_break_loader2.py\n(Task Manager)"]:::process
            Worker1["checker2.py\n(Worker Thread 1)"]:::process
            Worker2["checker2.py\n(Worker Thread 2)"]:::process
            Worker3["checker2.py\n(Worker Thread 3)"]:::process
        end
  https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcQIPP6YQGUnzBzA81ru1pXUBmWAGfuzgkHJjc9GF8Gfp-yrbOFKI5NtvsLa8pggUEB-vMuHe_7DPW4rho0BPQuGR9XcZ0NPg3Ny-zPyDFZClhtHxYw$0  end

    %https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcQIPP6YQGUnzBzA81ru1pXUBmWAGfuzgkHJjc9GF8Gfp-yrbOFKI5NtvsLa8pggUEB-vMuHe_7DPW4rho0BPQuGR9XcZ0NPg3Ny-zPyDFZClhtHxYw$0% --- STORAGE LAYER ---
    subgraph Warehouse ["3. Storage & Deduplication"]
        StorageMgr["storage2.py\n(Data Manager)"]:::storage
        HashCheck{"Duplicate Check\n(SHA256 Hash)"}:::storage
        DB[("🗄️ SQLite Database\n(checks.db)")]:::storage
    end

    %% --- OPTIMIZATION LAYER ---
    subgraph Optimizer ["4. Feedback Loop"]
        Improver["storage_improver2.py\n(Pattern Analyzer)"]:::feedback
    end

    %% --- OUTPUT LAYER ---
    subgraph Interface ["5. User Interface & Output"]
        RunScript["run.py\n(Automated CLI)"]:::output
        Dashboard["streamlit_app3.py\n(Interactive UI)"]:::output
        Emailer["emailer.py\n(SMTP Alerts)"]:::output
        CSVExport["search_save.py\n(CSV Exporter)"]:::output
    end

    %% --- CONNECTIONS ---
    
    %% Input flows
    RawFiles --> |Load Folder| Loader
    StreamlitUpload --> |Load File| Loader
    Loader --> |Raw String| Breaker
    Breaker --> |List of Chunks| Orchestrator
    RulesConfig -.-> |Load Logic| Orchestrator

    %% Processing flows
    Orchestrator --> |Assign Task| Worker1
    Orchestrator --> |Assign Task| Worker2
    Orchestrator --> |Assign Task| Worker3
    
    %% Scoring flows
    Worker1 & Worker2 & Worker3 --> |Scored Chunk| StorageMgr

    %% Storage Logic
    StorageMgr --> HashCheck
    HashCheck --> |Yes (Exists)| Discard[🚫 Skip Save]:::storage
    HashCheck --> |No (New)| DB

    %% Outputs
    RunScript --> |Trigger| Loader
    Dashboard --> |Trigger| Loader
    Dashboard --> |View/Edit| RulesConfig
    Dashboard --> |Query| DB
    RunScript --> |Query| DB
    
    %% Reporting
    RunScript --> |Generate| Emailer
    RunScript --> |Export| CSVExport
    Dashboard --> |Download| CSVExport

    %% Feedback Loop
    Improver --> |Read History| DB
    Improver --> |Suggest New Rules| RulesConfig
