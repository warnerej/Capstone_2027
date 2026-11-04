**Assignment 9 – Requirements and Technical Specifications**

**Team Members:** Nate Heath, Elliot Warner, William Thomas

**Project Title:** Data-Day

**Goal Statement:** To build and open source a 64 x 32 pixel desktop display and the developer platform for creating applications for the device.

**Requirements**

1.  **Version and Change Log**

| **Version** | **Date** | **Change**                     | **Author** |
|-------------|----------|--------------------------------|------------|
| 0.1         | 10/30/26 | Initial requirements doc draft | Nate Heath |
|             |          |                                |            |
|             |          |                                |            |
|             |          |                                |            |

2.  **Purpose and Scope**

**2.1 – Purpose**

The purpose of this document is to define the functional and non-functional requirements for the open-sourced LED display application system. It defines what the system must provide to its users and establishes measurable conditions that can be used to verify whether the system meets its goals.

The document serves as the list of requirements for the project. Its requirement IDs will be referenced in the Technical Specifications and traceability matrix.

**2.2 – Project Scope**

The open-sourced LED display application system is a small, customizable display device built around an Orange Pi Zero 3 and a 32x64 LED display. The system aims to provide a platform where developers can create applications that display info or graphics on the LED matrix without needing to implement the low-level hardware operations themselves, like pixel management, low-level drawing, or driver handling.

The system will provide:

- A physical LED display.

- A software library/SDK that provides developers with simplified functions for drawing to the display/managing the Orange Pi device.

- Two physical dial buttons for navigating through applications and interacting with specific applications, respectively.

- Local application management functionality for running, selecting, and stopping applications.

- A way to transfer application files to the device via a network.

**2.3 – Out of Scope**

The following features are NOT required:

- A public online marketplace or application store.

- Cloud-based execution or remote rendering.

- A mobile application for managing the device.

- Support for display hardware.

- An account and permissions system.

3.  **Stakeholders and User Goals**

**3.1 – Stakeholders**

| **Stakeholder**  | **Description**                                                         | **Goals**                                                                                                                             |
|------------------|-------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| App Developer    | A person who creates applications for the system                        | Create applications using a simplified library/SDK, avoid managing low-level display hardware, and install applications on the device |
| Device User      | A person who interacts with the completed device                        | View displayed information, navigate available applications, and interact with the physical inputs                                    |
| Project Dev Team | The students responsible for designing and implementing the system (us) | Produce a functional, working, maintainable, documented system                                                                        |

**3.2 – User Goals**

The system Is designed to support these primary user goals:

- Create applications without hardware expertise. Developers should be able to use the provided SDK to display content without implementing LED display communication or driver/refresh behavior.

- Install applications conveniently. Developers should be able to transfer applications to the device over a local network.

- Navigate applications physically. Device users should be able to use the main dial button to browse and select installed applications.

- Display application content. Users should see application-generated text, graphics, etc on the LED matrix.

- Recover from application problems. The device should remain usable when an application encounters an error or stops unexpectedly.

4.  **Glossary**

| **Term**                        | **Definition**                                                                                                             |
|---------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Application                     | A software program installed on the device that produces content for the LED display.                                      |
| Application developer/developer | Someone who creates applications using system’s SDK/library.                                                               |
| Application file                | The file that contains the code written by an application developer to run on the Orange Pi and display on the LED matrix. |
| Device user                     | A person who interacts with the device through its physical controls and display.                                          |
| Dial button                     | The physical input device used to navigate between applications, confirm selections, and interact within applications.     |
| Display                         | The 32x64 LED matrix used to present application content.                                                                  |
| Frame                           | A complete set of pixel values to be shown on the display at once.                                                         |
| Orange Pi                       | The Orange Pi Zero 3 single-board computer that runs the system’s software and applications.                               |
| SDK (Software Development Kit)  | The software library and supporting tools that developers use to create applications for the platform.                     |

5.  **Functional Requirements**

| **ID** | **Requirement Statement**                                                                                                                       | **Priority** | **Source Story**                               | **Verification Method**                                                                                            |
|--------|-------------------------------------------------------------------------------------------------------------------------------------------------|--------------|------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| REQ-01 | The system shall display application-generated content on the LED matrix, with each pixel reachable by its horizontal and vertical coordinates. | Must         | US02 Display application content               | Run an application that draws pixel patterns and verify the expected vs actual pixel positions.                    |
| REQ-02 | The system shall allow a device user to navigate the installed application list using clockwise and counterclockwise dial rotation.             | Must         | US01 Navigate between applications             | Rotate the dial button in both directions and verify the selected application changes accordingly.                 |
| REQ-03 | The system shall allow a device user to select and launch the currently highlighted application by pressing the dial button.                    | Must         | US01 Navigate between applications             | Select an installed application using instructions above and verify it launches.                                   |
| REQ-04 | The system shall provide an SDK with functions for clearing the display, setting pixels, drawing supported text/shapes, and presenting a frame. | Must         | US03 Develop applications                      | Build and run a sample application using each supported drawing operation.                                         |
| REQ-05 | The system shall allow a developer to upload an application file to the device over a local network.                                            | Must         | US03 Install applications remotely via network | Upload a valid application file and verify that the device receives it.                                            |
| REQ-06 | The system shall maintain a list of installed applications and provide an identifier to the application-selection view.                         | Should       | US01 Navigate between applications             | Install multiple applications and verify that each one appears in the list.                                        |
| REQ-07 | The system shall allow a developer to remove an installed application without removing the core software or other applications.                 | Should       | US03 Manage applications                       | Remove one application and verify the remaining applications are still listed and can be ran.                      |
| REQ-08 | The system shall return to the application-selection view after an application is stopped or terminates unexpectedly.                           | Could        | US03 Troubleshoot application failure          | Force an application to terminate and verify that the selection interface is present and you can interact with it. |

6.  **Non-Functional Requirements**

| **ID** | **Requirement Statement**                                                                                                                | **Priority** | **Source Story**                      | **Verification Method**                                                                   |
|--------|------------------------------------------------------------------------------------------------------------------------------------------|--------------|---------------------------------------|-------------------------------------------------------------------------------------------|
| NFR-01 | The system should update the displayed frame within 0.5 seconds after receiving a completed frame.                                       | Should       | US02 Display application content      | Log the time between submitting a frame and observing the updated display.                |
| NFR-02 | The system should reject all application files with invalid structure, or files that are flagged/rejected by the malicious code checker. | Must         | US03 Install applications safely      | Submit valid and deliberately invalid files and verify that invalid files are rejected.   |
| NFR-03 | The system should allow a user to select and launch an already-installed application within 20 seconds.                                  | Should       | US01 Navigate between applications    | Observe users completing the selection and run app task and record time and success rate. |
| NFR-04 | The system shall recover to the application-selection view within 10 seconds after a running application terminates unexpectedly.        | Should       | US03 Recover from application failure | Force an application to terminate unexpectedly and record recovery time and success rate. |

7.  **MoSCoW Summary**

**Must Have**

- REQ-01: Display application-generated context on the LED display.

- REQ-02: Navigate between applications using dial button input.

- REQ-03: Run the selected application using dial button input.

- REQ-04: Provide the core SDK functions.

- REQ-05: Upload application files over a local network.

- NFR-02: Reject malformed or unsafe applications.

**Should Have**

- REQ-06: Maintain and display an installed application list.

- REQ-07: Remove an installed application without modifying the availability of other applications.

- NFR-01: Meet the proposed 0.5s display update target.

- NFR-03: Meet the proposed usability target for selecting and running an app.

- NFR-04: Recover from unexpected application crashing.

**Could Have**

- REQ-08: Return to application selection view after application stops.

**Won’t Have (for this release)**

- A public online marketplace or application store.

- Cloud-based execution or remote rendering.

- A mobile application for managing the device.

- Support for display hardware.

- An account and permissions system.

8.  **Inherited Constraints**

| **ID** | **Constraint**                                                                                                           | **Impact**                                                                                                   |
|--------|--------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| CON-01 | The project must remain within the team’s hardware/development budget.                                                   | The team must prioritize affordable options for hardware and software tools. The product must remain simple. |
| CON-02 | The system must use the Orange Pi Zero 3 as its primary computer.                                                        | The software architecture and selected tools must be compatible with an Orange Pi Zero 3.                    |
| CON-03 | The initial system must support the 32x64 LED display.                                                                   | The initial display interface must accommodate the panel’s fixed resolution and color specs.                 |
| CON-04 | The project must use an open-source approach for the library/SDK.                                                        | The team must select compatible licenses and document third-party dependencies.                              |
| CON-05 | Uploaded applications must not be allowed to corrupt the platform through malicious code.                                | The application installation process must validate file contents.                                            |
| CON-06 | The device must operate as a locally-managed physical device rather than depending on cloud-based application rendering. | Application execution and display rendering must take place on the Orange Pi.                                |

9.  **Assumptions and Open Questions**

**9.1 – Assumptions**

| **ID** | **Assumption**                                                                                     | **Reason**                                                                                                                     |
|--------|----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ASM-01 | The Orange Pi Zero 3 and selected 32x64 LED display will be available for development and testing. | These components form the core hardware platform.                                                                              |
| ASM-02 | Applications will be developed using the programming language and SDK selected by the team.        | The platform is intended to simplify development through an SDK.                                                               |
| ASM-03 | Application uploads will occur over a local network connection.                                    | The device is intended to support convenient installation without requiring direct physical access to the Orange Pi’s storage. |
| ASM-04 | The release will support one currently-running application at a time.                              | This keeps applications management and display ownership manageable.                                                           |
| ASM-05 | Initial applications will be developed by trusted project users or developers.                     | The project is not intended to provide a fully-isolated execution environment.                                                 |

**9.2 – Open Questions**

| **ID** | **Open Question**                                                                 | **Owner**      | **Target Resolution Date** |
|--------|-----------------------------------------------------------------------------------|----------------|----------------------------|
| OQ-01  | What is the maximum permitted application file size?                              | Orange Pi lead | 11/6/27                    |
| OQ-02  | What exact display update rate and color depth can the panel and drivers support? | Display lead   | 11/20/27                   |
| OQ-03  | What should happen when the device starts and no applications are installed?      | Entire team    | 11/13/27                   |
| OQ-04  | Will applications be restarted automatically after a crash?                       | Orange Pi lead | 11/20/27                   |
