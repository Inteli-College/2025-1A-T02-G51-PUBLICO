[![Inteli Logo](./imgs/inteli_logo.png)](./imgs/inteli_logo.png)

# Project Plan - Entrepreneurship

## Project Team Members

- **Alberto Da Rocha Miranda**
- **Beatriz Hirasaki Leite**
- **Gabriel Caetano Nhoncanse**

---

## Executive Summary

An application that allows patients to perform hospital triage remotely, directing them to the most appropriate facility in the hospital network for their care. The solution aims to reduce the number of unnecessary trips to healthcare centers and avoid redundant triages in different units of the network.

---
## Problem Statement

The proposed solution seeks to address the high number of trips made by patients to hospital centers, as well as the repetition of triages for the same health issue of the same patient.

Frequently, for example, a patient is seen at an outpatient clinic, goes through an initial triage, and is then referred to another specific hospital, where they need to travel again and undergo a new triage upon arrival — often on different days or weeks — which further delays the start of treatment.

---

## Business Description

The system proposes the automation of the initial hospital triage stage, with the goal of reducing the number of repeated triages and prior visits to medical care. The solution will be made available through a mobile app or chatbot via WhatsApp, making it easier for the patient to access the service.

Using a large language model (LLM), the system interacts directly with the patient, starting with general questions and, based on the responses provided, conducting more specific questions focused on the main clinical suspicions.

With the information collected, the system is able to classify the severity of the condition and direct the patient to the most appropriate hospital for their care.

> **Important:** The system **does not provide diagnoses or offer treatments**. Its role is to generate a detailed report that will serve as a basis for the physician to understand the patient's clinical scenario, as well as guide the patient more efficiently within the hospital network.

---

## Business Objectives

The project is configured as an innovative technological solution aimed at the healthcare sector, with the objective of optimizing the hospital triage process through artificial intelligence.

The proposal is to offer an initial automated service to the patient, accessible via mobile app or WhatsApp chatbot, reducing the number of repeated triages, speeding up proper referrals, and promoting greater efficiency in hospital care.

Although it operates before the patient arrives at the hospital, the solution will be integrated into the hospital network systems, enabling healthcare professionals to access the report generated during the remote triage. This contributes to a more effective organization of medical demand and to the continuity of care based on the information already collected.

### Main Objectives:

- Develop an accessible and intuitive interface for patients of different profiles, prioritizing practicality and clear language — which can be via mobile app or through the implementation of a WhatsApp chatbot, increasing the solution's reach and contributing to its democratization, especially among people with low familiarity with digital technologies;
- Ensure data security, privacy, and confidentiality, in compliance with medical information protection regulations (such as the LGPD);
- Create a dynamic flow of questions, based on a large language model (LLM), capable of adapting to the patient’s answers and offering personalized triage;
- Generate a detailed clinical report to assist healthcare professionals in the care process, without providing diagnoses or prescriptions;
- Facilitate integration with hospital systems, ensuring that the collected information is used effectively in in-person care.

---

## Work Schedule

# Module Planning: 5 Sprint Roadmap (2 Weeks Each)

## Sprint 1: Backend Foundations & Authentication

### Develop Authentication Microservice
Implement a dedicated microservice to handle user registration, login, and token-based authentication using JWT. Ensure secure password handling and user role management.

### Structure MongoDB for Questionnaires
Design and implement MongoDB collections to store questionnaire templates, individual questions, patient answers, and metadata such as timestamps and doctor IDs.

## Sprint 2: Chat System & Patient Interaction

### Develop Chat System Backend
Create a backend service to manage chats between the system and patients. This includes endpoints to start a chat, send and receive messages, and associate them with patient records.

### Store Patient Responses
Ensure that each patient’s answers during the chat are stored in the appropriate MongoDB structure with timestamps, message types, and related question IDs.

## Sprint 3: Doctor Interface & Questionnaire Builder

### Create Doctor Questionnaire Interface
Build a web interface that allows doctors to create and manage questionnaires. This includes adding, editing, reordering, and deleting questions.

### Containerize Application with Docker
Use Docker to create containers for each service (auth, backend, frontend) to ensure a consistent development and deployment environment.

## Sprint 4: Patient Insights & UI for Doctors

### Interface for Patient Triage History
Develop a user-friendly dashboard for doctors to view patient answers across multiple triage sessions, organized by date and highlighting key symptoms.

### IBM Watson Integration
Integrate IBM Watson NLP to analyze and summarize patient responses. Display key synthesized insights for easier triage and pattern recognition.

## Sprint 5: Infrastructure & Deployment Automation

### Automate Infrastructure with Terraform
Write Terraform scripts to automate the provisioning of cloud infrastructure (e.g., VMs, MongoDB instances, load balancers) for staging and production.

### Finalize Deployment Pipeline
Ensure all services are deployed in containers, integrated, and accessible. Validate infrastructure with end-to-end tests and document the deployment process.
## Additional Steps: AI-Based Severity and Specialty Recommendation

### Train AI Model for Severity and Specialty Prediction
Using anonymized patient response data, train a machine learning model to classify the severity of the condition and recommend the most relevant medical specialty. This model will assist doctors by highlighting urgent cases and suggesting referrals based on symptom patterns.

### Integrate AI Predictions into Doctor Interface
Display AI-generated recommendations in the doctor’s dashboard alongside patient triage responses. Ensure transparency by providing a brief explanation of the model's reasoning for each prediction.

## Scope

This module covers the development of a web-based triage system for doctors to manage and analyze patient questionnaires. It includes:

- A secure authentication microservice.
- Backend for chat-based patient interactions with answer storage.
- MongoDB schema for questionnaires and responses.
- Doctor interfaces to create questionnaires and view patient history.
- Integration with IBM Watson to summarize patient answers.
- Initial AI model to assess severity and recommend specialties.
- Docker-based containerization and Terraform-based infrastructure setup.

Patient-facing UI, real-time chat, and regulatory compliance are out of scope for this module.

## General Observations

### Main Challenges:

- Validating ideas with professionals in the field and gathering information about the processes currently used;
- Difficulty in adoption of the solution by patients;
- Collection of physical patient data, such as temperature, heart rate, among others.
