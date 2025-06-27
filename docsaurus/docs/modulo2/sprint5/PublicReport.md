[![Inteli Logo](./imgs/inteli_logo.png)](./imgs/inteli_logo.png)

## Summary

1. [Introduction](#1-introduction)  
2. [Sprint 1](#2-sprint-1)
3. [Sprint 2](#3-sprint-2)  
4. [Sprint 3](#4-sprint-3)  
5. [Sprint 4](#5-sprint-4)  
6. [Sprint 5](#6-sprint-5)  
7. [Conclusion](#7-conclusion) 

---------------------------------------------------------------------------------------------------------------------------------------

### 1. Introduction

In the previous module, our team concentrated primarily on the business aspects of the project, including problem validation, value proposition, user personas, and risk analysis. These activities were crucial in building a solid foundation and ensuring that the product vision aligned with real user needs and market opportunities. Throughout that phase, we maintained a close collaboration with our medical partner, who provided essential insights and validated our assumptions.

Building on that foundation, this module focused on translating business ideas into functional technical components. We concentrated on backend development, system integration, and communication channels to move the project toward a working prototype. Our continued partnership with the medical expert ensured the solution remained grounded in practical healthcare requirements.

### 2. Sprint 1
• **Project Initialization and Environment Setup**

Sprint 1 focused on structuring the technical foundation of our project. While development was still at an early stage, the main goal was to set up a clean, scalable environment for future iterations. The backend server was built using FastAPI, and the initial connection with a MongoDB database was successfully tested. A preliminary version of the Dockerfile was also written, aiming to containerize the project and facilitate a consistent development environment across team members.

Key Deliverables:
- Backend server setup using Python and FastAPI
- MongoDB integration and basic connection testing
- Initial Dockerfile for containerization
- Preliminary folder and repository structure
- Basic user creation functionality

This sprint marked the transition from ideation to implementation, ensuring that future development would be built on a well-defined and replicable environment.

### 3. Sprint 2
• **User Management API and Medical Partnership Insights**

With the infrastructure in place, Sprint 2 marked the beginning of true backend development. The primary focus was implementing a complete CRUD (Create, Read, Update, Delete) flow for user data. To inform our data models, we conducted a remote meeting with our healthcare partner, whose insights played a pivotal role in shaping the attributes and behaviors of our system’s users. This ensured alignment between technical development and the actual needs of medical professionals.

Alongside CRUD operations, we began defining the initial logic for dynamic question flows, an essential component of the triage conversation. Furthermore, we successfully integrated the backend with Facebook’s messaging API, allowing our system to send structured messages to users through Messenger.

Key Deliverables:
- Full CRUD implementation for user management
- Definition of user schema based on real-world feedback from medical partner
- Initial implementation of the dynamic question logic
- Integration with Facebook API for message delivery
- Support for multiple message types (text, button interactions, etc.)

This sprint effectively bridged the gap between backend logic and external communication, pushing the system closer to real-time user interaction.

### 4. Sprint 3
• **API Logging and Messaging Infrastructure**

Sprint 3 was dedicated to improving the robustness and observability of our backend system. A key achievement was the full refactoring of the logging architecture, making it easier to track requests, errors, and backend behavior during runtime. This will be essential for future debugging and monitoring.

On the communication side, we developed the logic for sending triage forms via WhatsApp, opening another channel for patient interaction. Additionally, we deployed an NGINX server with a public endpoint capable of receiving messages, marking the first real integration point where external messages can enter and be processed by our system.

Key Deliverables:
- Complete refactoring and enhancement of the API’s logging system
- Implementation of logic for sending forms via WhatsApp
- Deployment of NGINX server with a publicly accessible endpoint
- Real-time message reception functionality

This sprint was crucial in elevating the quality and traceability of our backend system, while simultaneously enabling two-way communication with patients.

### 5. Sprint 4
• **Message Storage Logic**

Sprint 4 focused on enhancing the system’s ability to handle incoming messages from external users via WhatsApp. We developed and implemented the backend logic required to store different types of messages received from users. This allowed the platform to accurately log and persist incoming communication, whether it was text, buttons, or other supported message types.

This functionality is fundamental for maintaining conversation history, enabling analytics, and supporting future enhancements such as conversation-based decision trees or user behavior tracking.

Key Deliverables:
- Implementation of a flexible message storage system
- Support for multiple message formats (text, button responses, etc.)
- Database schema adjustments to accommodate incoming messages
- Initial testing of end-to-end message reception and storage flow

This sprint marked a significant step in making the platform more robust in handling real-world user interactions.

### 6. Sprint 5
• **Dynamic Form Progress Tracking and Response Sending**

In Sprint 5, the team concentrated on enhancing the interaction flow between the backend and users by implementing the logic to track user progress within the triage form. This included creating mechanisms to identify which question the user is currently answering, and dynamically selecting the next appropriate response based on their inputs.

Furthermore, we developed and tested the functionality for sending context-aware replies to users via WhatsApp. This ensured that users would receive the correct follow-up questions or feedback depending on where they were within the form flow.

Key Deliverables:
- Development of progress-tracking logic to monitor user’s current question in the form
- Implementation of dynamic response generation based on user state
- Integration with WhatsApp API for sending real-time replies
- End-to-end testing of form navigation and response accuracy

This sprint represented an essential move towards a responsive and personalized conversation flow, making the triage experience smoother and more intuitive for users.

### 7. Conclusion

Throughout Module 2, our team successfully transitioned from infrastructure setup to implementing real conversational and backend logic. From message storage to real-time form progress tracking and dynamic response generation, each sprint built upon the previous, moving us closer to a fully functional automated triage system.

Our partnership with the medical expert continued to provide vital feedback, ensuring that the technical implementation remained aligned with clinical realities. By the end of this module, we had established a reliable messaging pipeline and laid the groundwork for more complex decision-making logic to be developed in future phases.

Looking ahead, upcoming modules will focus on refining user experience, enhancing system scalability, and integrating more advanced AI-driven decision support functionalities.