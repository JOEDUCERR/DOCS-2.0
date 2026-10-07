### CodeSoft — What I’ve Done So Far

- Built the frontend using **vanilla HTML, CSS, and JavaScript ES modules**.
    
- Added login/register flow with **JWT authentication** and **Argon2 password hashing**.
    
- Added **user/admin roles** and protected admin problem management.
    
- Moved application data from frontend/localStorage to **PostgreSQL**.
    
- Added PostgreSQL models for **users, problems, and submissions**.
    
- Added **FastAPI backend APIs** for authentication, problems, submissions, profiles, and admin operations.
    
- Added **Redis-based asynchronous job queue** for code execution.
    
- Built a dedicated **worker** to consume execution jobs.
    
- Implemented **Docker-based isolated code execution**.
    
- Added support for **Python, C++, and Java** execution.
    
- Added execution statuses such as **Queued, Running, Accepted, Wrong Answer, Compilation Error, Runtime Error, and Time Limit Exceeded**.
    
- Added **CPU, memory, network, and non-root restrictions** for execution containers.
    
- Fixed the **Docker sandbox path/bind-mount issue**.
    
- Fixed the **Python execution harness issue** that was causing false runtime errors.
    
- Added persistent execution results and **submission history**.
    
- Added **Run vs Submit** behavior: sample tests vs full test suite.
    
- Added **real-time-ish result updates** on the problem page using 750 ms polling.
    
- Added display of **test cases, stdout, stderr, runtime, memory, and result status**.
    
- Added **local draft persistence** so problem code survives page refresh.
    
- Added protection against **stale polling results/race conditions**.
    
- Added automated API tests and Docker/Redis/PostgreSQL development setup.
    
- Documented the implementation in **README.md, agent.md, and GitHub Wiki**.
    
- Consolidated the backend work into the **`backend-works`** branch while keeping `main` as the frontend/main branch.