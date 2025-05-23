# Triage Overview

Triage has evolved over time to meet the needs of different healthcare fields, resulting in various models adapted to each scenario:

**Hospital Triage**: Occurs in hospitals and emergency rooms, prioritizing patients based on urgency to optimize resources and professionals' time.

**Clinical Triage**: Assesses the patient's overall health, which may include preventive exams and the selection of organ, bone marrow, and blood donors.

**Psychological Triage**: Analyzes patients seeking psychological or psychiatric care, identifying symptoms, causes, and the best treatment approach.

**Ambulatory Triage**: Applied in low-complexity care, organizing consultations and directing patients to the appropriate professionals.

---

## Project Focus: Hospital Triage

The primary goal of triage is to prioritize patient care based on the severity of their condition, ensuring that the most urgent cases receive treatment first.

### Frequently Asked Questions and Key Information Collected in Current Hospital Triage

Hospital triage is currently conducted through manual interaction between a pharmacist and the patient.

### Personal Information and Identification
Both personal data, such as name, CPF (Brazilian ID number), etc., and health insurance details, including a registration number. With the registration number, other data could be retrieved from the patient’s account.
*(Idea: The first input field should be the registration number, and personal data should be retrieved and displayed automatically.)*

### Chief Complaint
- What is the reason for your visit to the hospital?
- How long have the symptoms been present?
- How did the symptoms initially appear?

### Medical History
- Do you have any pre-existing medical conditions?
- Are you currently taking any medication?
- Do you have any known allergies?
- Have you ever been hospitalized or undergone surgery?

### Current Symptoms
- Where is the pain located?
- How would you describe the pain (burning, stabbing, continuous)?
- What is the intensity of the pain on a scale of 0 to 10?
- Does the pain radiate to other parts of the body?
- What relieves or worsens the pain?

### Vital Signs
- Blood pressure
- Heart rate
- Body temperature
- Respiratory rate

### Other Relevant Aspects
- Are you pregnant? *(for women)*
- Do you use substances such as alcohol, tobacco, or other drugs?
- Have you recently been in contact with a sick person or traveled to areas with disease outbreaks?

---

## General Questions for the Project

A filter of the most important questions to be asked to all patients before personalized questions are generated by the model.
Each question has its own possible alternatives.

### 1. What is the main reason for your visit to the hospital?
- Pain
- Fever
- Difficulty breathing
- Nausea or vomiting
- Other

### 2. How long have the symptoms been present?
- Less than 2 hours
- Between 2 and 24 hours
- Between 1 and 7 days
- More than a week

### 3. How intense is your pain right now?
- No pain
- Mild pain
- Moderate pain
- Severe pain
- Unbearable pain

### 4. Do you have any pre-existing medical conditions?
- Diabetes
- Hypertension
- Heart problems
- None of the above

### 5. Are you currently taking any medication?
- Yes (If yes, please list the medications)
- No

### 6. Do you have any known allergies?
- None
- Medications (If yes, please list the medications)
- Foods (If yes, please list the foods)
- Other (If other, please specify)

### 7. Are you pregnant or suspect you might be?
- Yes
- No
- Not applicable

### 8. Do you use any of the following substances?
- Alcohol
- Tobacco
- Recreational drugs
- None of the above

### 9. Have you recently been in contact with a sick person or traveled to areas with disease outbreaks?
- Yes
- No
