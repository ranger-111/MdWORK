## E2E Test Automation - Ansible

Jira - E2E Test Automation: [https://jira.tools.sap/browse/1ECSPBM-16883](https://jira.tools.sap/browse/1ECSPBM-16883) Sharepoint E2E Test Automation: [LINK](https://sap.sharepoint.com/:f:/r/teams/ECSToolingandfuturecollaboration/Shared%20Documents/General/28_TestAutomation?d=wee03245b7e8e4b86ab49a1d4f8e8eefe&csf=1&web=1&e=D7b8AX) Transcripts/Documents: [LINK](https://sap.sharepoint.com/:f:/r/teams/ECSToolingandfuturecollaboration/Shared%20Documents/General/28_TestAutomation/Transcripts/TranscripsDocumentsSebastian?d=wb82c34d702004f4d9c3aa9002fd7b076&csf=1&web=1&e=vPqhmV)

### Project Team TnA:

- Diana Simina
- Adrian Radu Ilea
- Martin Kramme-Ruhkamp
- Johannes Link
- Maik Smuda

### Other Teams:

- Stanimir Eisner & Tsanko Aleksandrov (CLE LO)
- Chhabra, Vidhi, Subramanian, Gayathri (Productizaion)
- Mario Dietner (RedHat - External Consultant)

### Scope:

- Implement standardized quality controls and automated code validation for following defined use case.
- Integrate a robust automated testing framework into CI/CD pipelines.
- Identify the requirements for an automation for immutable, on‑demand development and test environments
- Analyze how an automation for immutable, on‑demand development and test environments can be realized
- Fit Gap Analysis
- Create target architecture + use cases for successor projects

### ToDos

#### CAL POC

- Provide via TIC created VMs, which adhere to our ECS standard to CLE LO (T)
- CLE LO will continue to perform necessary steps to create the DB and SAP systems on this VM (T)
- Identify within this process the required integration points, gaps to be resolved for a fully automated approach (T)
- Analyse together with Productization, if the systems fulfills are the needed requirements (T)
- Identify how this new approach can be integrated into the CI/CD pipeline project of Iulia and Ovi (T)

### Ansible block

- Analyze how the improved testing approch created together with RH consultant can be integrated into the CI/CD pipeline project of Iulia and Ovi (T)
- Integrate the "Ansible testing block" into CI/CD pipeline (T)
- Where and how need the test results of the triggered tests be stored? (e.g. jira items for successful/failed tests) (T)
- Define which kind of test systems are required per future test case in collab with productization colleagues (T)
- Research which standard dashboards/reporting tools are used in SAP to display automated testing results & how to integrate/implement them in ECS (T)

### Overall Project

- Define a set of test cases spanning across the tools in ECS (e.g. Ansible, LaMa, SPC) for most critical/error-prone activities (T)