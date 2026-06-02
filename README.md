```mermaid
flowchart TD
  subgraph BE["Backend (this service)"]
    A[Caller: GraphQL / REST\nnot yet wired] --> B[HandleRequest]
    B --> C{request type}
    C -->|Process / ProcessUpload| D[FrameworksMappingWrapper.Process\nor ProcessUpload]
    C -->|Resume| E[FrameworksMappingWrapper.Resume]
    C -->|JobStatus| F[FrameworksMappingWrapper.GetJobStatus]
    C -->|ApproveRequirement| G[db.ManageRequirements\nCustomRequirementToAdd]
  end

  subgraph Ext["External Frameworks Mapping Service"]
    D --> H[POST /api/v1/frameworks/process\nor /process-upload]
    E --> I[POST /api/v1/frameworks/resume]
    F --> J[GET /api/v1/jobs/{run_id}]
  end

  subgraph DB["Postgres"]
    G --> K[audit_controls / universal_requirements]
  end

  H --> L[ProcessResult\nMappedControls + GeneratedURs]
  I --> L
  J --> M[JobStatusResult\nProgress 0–100, Done bool]
  G --> N[ApproveRequirementResponse\nUniversalRequirementID, Deduplicated]
```
