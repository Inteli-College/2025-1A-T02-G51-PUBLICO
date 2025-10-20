# Structuring and Business Foundation
**Objective:** Establish the conceptual and structural foundation of the business plan, aligning vision, mission, and product scope.

---

## Executive Summary

### TO-DO: DEFINIR NOME DA APLICAÇÃO
**Solution Name:**

**Overview:**
The project proposes an application designed to automate the hospital triage process using a Large Language Model (LLM) to interact with patients and collect information through a conversational interface. The system conducts an adaptive questionnaire — starting with general questions and then progressing to more specific ones based on patient responses — ultimately producing a detailed report for the attending physician. This automation reduces the time spent by nurses in manual data collection and enhances operational efficiency, while keeping healthcare professionals central to the process.

**Purpose:**
To optimize hospital triage by automating the information-gathering phase through AI-based conversational interaction, increasing efficiency without compromising data accuracy or patient safety.

**Mission:**
To improve the speed, accuracy, and efficiency of hospital triage through intelligent automation that supports, but not replace, healthcare professionals.

**Vision:**
To significantly improve intelligent healthcare automation in Brazil, aiming to reduce patient waiting time and health risks, while improving operational flow within hospitals.

**Values:**
- Ethics and patient safety above all.
- Transparency in the handling and protection of medical data.
- Collaboration with medical professionals as partners in care delivery.
- Technological innovation focused on practical and social impact.

**Competitive Advantages:**
- Integration with WhatsApp for easy patient access.
- Adaptive questioning powered by LLMs, enabling personalized triage.
- Automatic generation of a clinical summary for medics.
- Compliance with LGPD and strong emphasis on privacy and data security.
- Reduction in triage time and relief of nursing workload without workforce reduction.

### TO-DO: VALIDAR COMO SERÁ O MODELO DE NEGÓCIOS
**Business Model:**
B2B SaaS (Software as a Service) solution offered to hospitals and clinics. The model involves subscription-based licensing, with pricing that may vary according to the volume of patient triages or hospital size. Future revenue opportunities include integration with hospital management systems (EHR) and enterprise customizations.

---

## Problem Statement
The current triage process in hospitals is highly manual and depends heavily on nursing staff. This dependence creates bottlenecks that delay diagnosis and patient care, especially in emergency departments. Overloaded professionals and increasing patient flow exacerbate inefficiencies, leading to longer waiting times and inconsistent prioritization of cases.

The proposed solution addresses these inefficiencies by automating a large portion of the triage process through a conversational AI capable of collecting and structuring patient information before medical evaluation. This process optimizes resource allocation and accelerates the overall care workflow.

---

## Business Description
The system acts as an intelligent triage assistant integrated into the hospital environment. Using a Large Language Model, it interacts with patients via WhatsApp, conducting structured dialogues that collect relevant clinical information. Based on these responses, the AI dynamically adapts its line of questioning, producing a personalized triage summary for the hospital use.

The platform operates as a support tool — it does not diagnose or prescribe treatment — but offers healthcare professionals an organized overview of the patient’s condition, enabling quicker and more informed decision-making.

From a technical standpoint, the system uses a modular architecture composed of several specialized services:
- **Form Builder and Flow Engine:** Allows clinicians to design and publish adaptive triage flows.
- **LLM Parsing Service:** Converts patient free-text responses into structured medical data with confidence scoring.
- **Summary Service:** Generates clinical summaries and operational metrics for hospital staff.
- **WhatsApp Integration Adapter:** Ensures a seamless conversational experience for patients.
- **IBM Cloud Infrastructure**: Ensures high availability and reliability by using IBM Cloud services to host the application.

The design prioritizes strict compliance with privacy standards (LGPD), ensuring traceability of all interactions while safeguarding personal health information.
