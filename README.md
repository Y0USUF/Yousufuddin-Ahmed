# Yousufuddin-Ahmed
flowchart TD
    %% Define Styles
    classDef env fill:#ffebf0,stroke:#e0004d,stroke-width:2px;
    classDef branch fill:#e3f2fd,stroke:#1e88e5,stroke-width:2px,rx:10;
    classDef action fill:#fff3cd,stroke:#ffb300,stroke-dasharray: 5 5;
    classDef urgent fill:#ffcccc,stroke:#ff0000,stroke-width:2px;

    subgraph STANDARD_WORK [NORMAL DEVELOPMENT CYCLE]
        direction TB
        Start((Start)) --> A[Feature Branch]:::branch
        A -->|Code & Test Local| A
        A -->|Merge Request| B{Code Review}:::action
        B --Approved--> C[Dev Branch]:::branch
        C -.->|Auto Deploy| D[Dev Environment]:::env
        
        D -->|Is it Release Week?| E{Ready for QA?}:::action
        E --Yes--> F[Release Branch]:::branch
        F -.->|Deploy| G[QA / Stage Env]:::env
        
        G --> H{Bugs Found?}:::action
        H --Yes: Fix in Release--> F
        H --No: Approved--> I[MAIN BRANCH]:::branch
        I -.->|Deploy & Tag| J[PRODUCTION ENV]:::env
    end

    subgraph EMERGENCY [HOTFIX / EMERGENCY LOOP]
        direction TB
        bug((CRITICAL BUG!)) -->|Branched from Main| K[Hotfix Branch]:::urgent
        K -.->|Deploy & Test| L[Hotfix Env]:::env
        
        L --> M{Fix Verified?}:::action
        M --No--> K
        M --Yes--> N[Merge to MAIN]:::urgent
        N --> J
        
        %% The Back Sync
        N -.->|Sync Fix Back| C
        N -.->|Sync Fix Back| F
    end

    %% Link Standard to Production
    J -.->|Bug Reports| bug
