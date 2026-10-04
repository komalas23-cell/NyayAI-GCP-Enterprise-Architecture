# NyayAI-GCP-Enterprise-Architecture
graph TD
    %% Styling Definitions
    classDef userStyle fill:#f9f9f9,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5;
    classDef frontendStyle fill:#e8f0fe,stroke:#1a73e8,stroke-width:2px;
    classDef perimeterStyle fill:#fff5f5,stroke:#ea4335,stroke-width:3px,stroke-dasharray: 5 5;
    classDef layerStyle fill:#ffffff,stroke:#5f6368,stroke-width:2px;
    classDef securityStyle fill:#f1f3f4,stroke:#5f6368,stroke-width:1px;

    %% User Node
    User(["👤 User / Judicial Clerk"])
    class User userStyle;

    %% Ingestion Layer
    subgraph FrontendLayer ["Frontend & Ingestion Layer"]
        WAF["🛡️ Google Cloud Armor <br>(WAF, DDoS, Rate Limiting)"]
        Portal["🌐 Cloud Run / Firebase <br>(Web Portal)"]
        WAF --> Portal
    end
    class FrontendLayer frontendStyle;

    %% Secure Traffic Flow
    User -- "1. Secure HTTPS / TLS 1.3" --> WAF
    Portal -- "2. Private Service Connect" --> ProcessingLayer

    %% VPC Service Controls Boundary
    subgraph VPCPerimeter ["🔒 VPC Service Controls Perimeter (Secure Boundary)"]
        
        %% Processing Layer
        subgraph ProcessingLayer ["Processing & Document AI Layer"]
            DocAI["📄 GCP Document AI <br>(Specialized Legal OCR / Handwriting)"]
            Tasks["⏳ Cloud Tasks <br>(Asynchronous 1000+ Page Batch Processing)"]
            DocAI <--> Tasks
        end
        class ProcessingLayer layerStyle;

        %% Gen AI & RAG
        subgraph GenAILayer ["Gen AI Engine & RAG Pipeline"]
            Gemini["🤖 Vertex AI Gemini 1.5 Pro <br>(2M Context Window)"]
            VecSearch["🔍 Vertex AI Vector Search <br>(Semantic Precedent Retrieval)"]
            Embeddings["📐 Vertex AI Embeddings API <br>(Legal Embeddings)"]
            Gemini --> VecSearch
            Gemini --> Embeddings
        end
        class GenAILayer layerStyle;

        %% Storage Layer
        subgraph StorageLayer ["Secure Storage & Persistence Layer"]
            GCS["🪣 Cloud Storage <br>(Case Files / Dossiers Bucket)"]
            DB["🗄️ Cloud Spanner / Firestore <br>(Structured Legal Metadata)"]
        end
        class StorageLayer layerStyle;

        %% Security & Governance (Cross-Cutting representation)
        subgraph GovLayer ["Security & Governance Layer (Cross-Cutting)"]
            KMS["🔑 Cloud KMS (CMEK)"]
            IAM["🆔 Cloud IAM (RBAC / Least Privilege)"]
            Audit["📋 Cloud Logging & Audit Logs (Immutable Trails)"]
        end
        class GovLayer securityStyle;

        %% Data Flow Connections within Perimeter
        ProcessingLayer -- "3. Clean Extracted Legal Text" --> GenAILayer
        GenAILayer -- "4. Encrypted Data Persistence" --> StorageLayer

    end
    class VPCPerimeter perimeterStyle;
