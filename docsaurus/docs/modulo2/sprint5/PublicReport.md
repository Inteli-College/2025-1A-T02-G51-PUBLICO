[![Inteli Logo](./imgs/inteli_logo.png)](./imgs/inteli_logo.png)

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
