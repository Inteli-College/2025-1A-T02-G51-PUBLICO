# Marketing Plan and Commercial Strategy
**Objective:** Define market positioning, commercial approach, pricing strategy, and customer acquisition methods for FastTriage.

## Marketing Plan

### Value Proposition
FastTriage automates part of the hospital screening process, reducing bottlenecks in emergency care and improving patient flow. By converting patient responses into structured clinical reports, the system decreases the workload of healthcare professionals, supports faster prioritization of critical cases, and improves the patient experience through a faster and more organized initial interaction.

### Market Positioning
FastTriage is positioned primarily as an operational efficiency solution for hospitals. Its focus is to reduce screening time, organize preliminary clinical information with higher quality, and optimize medical decision-making.
The core benefits are:

1. Reduced screening time and faster patient flow
2. Structured clinical reports that improve prioritization and treatment
3. Improved patient experience during emergency care

The solution operates as a support tool for nurses and physicians, not as a diagnostic or medical decision system.

### Comparative Advantages
To illustrate the operational impact of the solution, the table below compares key aspects of the current screening model versus screening supported by FastTriage:

| Aspect | Current Manual Screening | Screening with FastTriage |
|--------|--------------------------|---------------------------|
| Data collection | Conducted manually by nurses | Automated via WhatsApp |
| Time required | Dependent on staff availability | Can occur before patient arrival or while waiting |
| Information format | Notes taken manually | Structured clinical report |
| Human workload | High demand on nursing staff | Reduced workload during initial screening |
| Patient experience | Long waiting time | Faster initial interaction |
| Prioritization | Based on manual clinical judgment | Structured symptoms help highlight critical cases |

### Customer Acquisition and Distribution Channels
Initial commercial actions will focus on direct contact with hospital decision-makers, including medical directors, emergency care coordinators, and IT management teams. To strengthen market visibility, the strategy includes:

- Participation in healthcare and health-tech events
- Demonstrations and technical presentations to hospitals
- Academic legitimacy through validation with healthcare professionals
- Potential future integration with hospital management platforms (e.g., MV, Tasy)

FastTriage is delivered as a web-based solution for hospitals, while patients interact through WhatsApp, simplifying adoption without requiring a dedicated mobile app.

## Commercial Strategy

### Market Entry Strategy
The entry strategy prioritizes a **pilot phase** in one or more hospitals. This stage aims to validate usability, measure the reduction of screening time, and collect structured feedback from nurses and physicians. The pilot will also generate practical evidence and metrics to support future commercial negotiations.

### Pricing Strategy
The pricing model will follow a phased approach:

**1. Pilot Phase (initial stage)**
To lower adoption barriers, FastTriage will offer a pilot period with reduced or waived cost. This allows hospitals to test the platform, validate usability, and measure operational impact before committing to a full contract.

**2. Commercial Phase (post-pilot)**
After validation, the pricing model combines two components:

- **Implementation and configuration fee**
  Covers setup, onboarding, adaptation of screening forms, and alignment with hospital processes.

- **Monthly license + usage-based billing**
  Hospitals pay a fixed monthly fee for system access, plus a variable cost per screening processed.
  This keeps pricing proportional to real usage, making the solution accessible for hospitals of different sizes and workloads.

The table below compares viable pricing models:

| Pricing Model | Advantages | Disadvantages | Suitability for FastTriage |
|---------------|------------|---------------|-----------------------------|
| Monthly fixed license | Predictable revenue, simple for clients | High-risk without usage metrics | Suitable only after product maturity |
| Usage-based billing | Costs aligned with real volume, scalable | Higher financial variability | Works for hospitals with fluctuating demand |
| Implementation + monthly license + variable usage | Covers setup cost and scales with usage | Requires usage monitoring | **Ideal post-pilot model** |
| Free or subsidized pilot | Eases adoption and generates real data | No immediate revenue | **Ideal for entry and validation stage** |

### Customer Relationship and Support
During the pilot stage, the team will provide onboarding, training, and continuous monitoring. Feedback from healthcare professionals will guide improvements and future releases.

In later stages, support evolves into:
- Training and documentation
- Periodic updates
- On-demand technical assistance

---
# Operational Plan and Technical Structure

**Objective:** Detail the business operations and resources required for implementation.

---

## 1. Complete Description of the Operational Flow

The FastTriage operational flow is designed to be intuitive for the patient and seamlessly integrated into the hospital routine, from the first contact to the delivery of the medical report.

1.  **Interaction Start (Patient):** The patient, upon arriving at the hospital or from home, initiates the interaction with FastTriage via WhatsApp (e.g., by scanning a QR Code at the reception or accessing a link sent by the hospital).
2.  **Conversational Triage (System):** The system, using the **WhatsApp Integration Adapter**, greets the patient and begins the adaptive questionnaire, which was previously configured by the hospital in the **Form Builder**.
3.  **Response Processing (System):** Each free-text response from the patient is sent to the **LLM Parsing Service**. The IBM language model (LLM) interprets the intent, extracts medical entities (symptoms, duration, intensity), and structures them.
4.  **Flow Adaptation (System):** Based on the structured data, the **Flow Engine** determines the next most relevant question, delving deeper where necessary (e.g., if the patient reports "chest pain," the system asks specific questions about that symptom).
5.  **Report Generation (System):** At the end of the conversation, the **Summary Service** compiles all structured information into a concise, objective, and standardized clinical summary.
6.  **Delivery (Hospital):** The report is made available in real-time on the hospital's dashboard (or integrated into the management system, such as MV Tasy, in a future phase) for the nursing staff and doctors.
7.  **Validation and Care (Hospital):** The healthcare professional reviews the FastTriage report, validates the information, and proceeds with the consultation, already equipped with a complete history, thereby optimizing diagnosis and decision-making time.

## 2. Brief Mention of Technological Infrastructure

The infrastructure is based on a modular microservices architecture, ensuring scalability, security, and compliance (LGPD).

* **Cloud Hosting:** The entire solution is hosted on **IBM Cloud**, ensuring high availability, robust security (essential for health data), and scalability.
* **Communication Integration:** The patient interface is managed by the **Official WhatsApp Business API**, ensuring message delivery reliability.
* **AI Processing:** The core intelligence is the **LLM Parsing Service**, which uses a large language model (LLM) from IBM (e.g., the Watsonx family) for natural language processing.
* **Core Applications:** The microservices architecture includes:
    * **Form Builder and Flow Engine:** A web application where hospital managers create and manage triage flows.
    * **Summary Service:** The service responsible for aggregating LLM data and generating final reports.
    * **WhatsApp Integration Adapter:** Manages the conversation state and communication between the patient and the AI services.
* **Database:** Use of secure, encrypted databases on IBM Cloud, adhering to all LGPD regulations for storing sensitive health data.

## 3. Definition of Team Roles and Responsibilities

The initial team combines product, technology, and compliance competencies.

* **Product (Management and Business):** Responsible for defining the product roadmap, managing relationships with hospitals (B2B clients), and ensuring that features meet market needs and generate operational value.
* **Development (Infrastructure and Backend):** Focused on the development and maintenance of microservices, managing the IBM Cloud infrastructure, and ensuring system uptime, scalability, and performance.
* **AI (LLM and Data):** Responsible for the integration with the IBM LLM, prompt engineering for the adaptive questionnaire, and ensuring accuracy in the extraction and structuring of medical data.
* **Compliance & Medical Liaison:** Ensures the platform is in strict compliance with LGPD and health regulations. Acts as the bridge to medical consultants (such as the professional from USP) for the continuous clinical validation of triage flows.

## 4. Description of Internal Processes

Internal processes are focused on agility, security, and continuous customer support.

* **Service Maintenance:** Continuous (24/7) monitoring of the IBM Cloud infrastructure, application logs, and API latency (LLM and WhatsApp) to ensure service stability.
* **Updates:**
    * **Triage Flows:** Hospitals can update their own flows via the **Form Builder**.
    * **System:** Security updates and new features are deployed via CI/CD with minimal downtime during scheduled maintenance windows.
* **Support (Pilot Phase):** High-touch support as described in the commercial plan. Includes onboarding, training for the nursing staff, and proactive feedback monitoring.
* **Support (Commercial Phase):** Evolution to a scalable support model, including robust documentation, periodic updates, and on-demand technical assistance.

## 5. Development of an Operational Timeline

The implementation schedule follows the phases defined in the commercial strategy.

* **Phase 1: MVP and Clinical Validation (End of 2025):**
    * Finalization of the core modules (Form Builder, LLM Parsing, Summary Service).
    * Technical validation of the integration with IBM Cloud and the WhatsApp API.
    * Validation of triage flows with medical consultancy (USP) to ensure clinical safety and relevance.
* **Phase 2: Pilot Project (Early 2026):**
    * Implementation of FastTriage in 1 or 2 partner hospitals (as per the "Market Entry Strategy").
    * Execution of the "Free or subsidized pilot" model to collect real-world data, measure the reduction in triage time, and gather direct feedback from nurses and doctors.
* **Phase 3: Commercial Expansion (From Q2 2026):**
    * Based on pilot metrics, begin the transition to the commercial pricing model ("Implementation + monthly license + variable usage").
    * Initiate client acquisition activities (events, demonstrations) and scale the support and onboarding infrastructure.
