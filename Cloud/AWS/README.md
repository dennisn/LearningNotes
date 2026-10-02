# AWS

## Certification Path

### Software Development Engineer
```mermaid
flowchart LR

  subgraph Found [Foundational]
    Cloud[Cloud Practitioner] --> AI_Prac[AI Practitioner]
  end

  Found --> Assoc

  subgraph Assoc [Associate]
    Dev[Developer]
  end

  Assoc --> Prof
  subgraph Prof [Professional]
    DevOps_Eng[DevOps Engineer]
    DevGenAI_Dev[Generative AI Developer]
  end
```

### Data Analytics
```mermaid
flowchart LR
  subgraph Found [Foundational]
    Foundation[Cloud Practitioner] 
  end

  Foundation --> Assoc
  subgraph Assoc [Associate]
    direction LR
    SolutionArch[Solution Architect] --> Opt
    
    subgraph Opt [Optional]
      DataEng[Data Engineer]
      ML_Eng[Machine Learning Engineer]
    end
  end

  Assoc --> Ext
  subgraph Ext [Extra]
    Security[Security - Specialty]
  end
```

### Machine Learning Engineer/ML Ops Engineer
```mermaid
flowchart LR

  subgraph Found [Foundational]
    Cloud[Cloud Practitioner] --> AI_Prac[AI Practitioner]
  end

  Found --> Assoc
  subgraph Assoc [Associate]
    direction LR
    SolutionArch[Solution Architect] --> Opt
    
    subgraph Opt [Optional]
      ML_Eng[Machine Learning Engineer]
      DataEng[Data Engineer]
    end
  end

  Assoc --> Prof
  subgraph Prof [Professional - Dive Deep]
    GenAIDev[Generative AI Developer]
    DevOpsEng[DevOps Engineer]
  end
```


### Data scientist
```mermaid
flowchart LR

  subgraph Found [Foundational]
    Cloud[Cloud Practitioner] --> AI_Prac[AI Practitioner]
  end

  Found --> Assoc
  subgraph Assoc [Associate]
    SolutionArch[Solution Architect] --> ML_Eng[Machine Learning Engineer]
  end

  Assoc --> Prof
  subgraph Prof [Professional - Dive Deep]
    GenAIDev[Generative AI Developer - Professional]
  end
```

## Certificates

### Foundational
- [Cloud Practitioner](./CloudPractitioner/README.md)