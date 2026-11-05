**Assignment 9 – Technical Specification Document**

**Team Members:** Nate Heath, Elliot Warner, William Thomas

**Project Title:** Data-Day

**Goal Statement:** To build and open source a 64 x 32 pixel desktop display and the developer platform for creating applications for the device.

**Version and Change Log**

| **Version** | **Date** | **Change** | **Author** |
| --- | --- | --- | --- |
| 0.1 | 11/4/26 | Initial draft | William Thomas, Elliot Warner |

**Architecture Summary**

Data-Day is built around a local standalone embedded deployment model that is hosted on an Orange Pi Zero 3. As shown on the D0 diagram, the system integrates hardware input (dial button), a local web-server interface and a direct memory mapped display controller.

**Major System Components**

- Orange PI Zero 3: The central single-board computer serves as the brain of the whole device. It hosts the operating system, local upload server, and the application execution runtime.

- Upload Server: A lightweight API backend web server that is hosted on the Orange Pi that receives, validates, and stores user submitted application files over the local network. GitHub will host the SDK and templates while the Orange Pi runs its own upload server.

- Dial Button: Physical rotary dial buttons wired directly to the Orange Pis hardware inputs to handle user input and navigation.

- Display Module: The user-facing RGB LED matrix, 32x64 HUB75 LED Panel, responsible for displaying real-time digital outputs.

- Local Machine and GitHub: External developer workstation used to write code and pull the SDK packages from GitHub.

**Deployment Model and Data Flow**

- Application Upload Flow (I5 -> I6): A developer on a local machine would send a Python script to the upload server. The file will be sanitized and stored locally on the Orange Pi.

- Input and Navigation Flow (I1 -> I2): Physical rotations or button presses on the Dial Button will send signals via GPIO bus polling to the Orange PI. The OS kernel will update the active application slot.

- Rendering and Display Flow (I3 -> I4): Active user applications execute on the Orange Pi. The display driver streams the pixel array to the HUB75 board interface and will refresh the display matrix.

**Interfaces and Data Formats**

| **Connection** | **Input** | **Output** | **Data Format** | **Protocol** | **Error** | **Responsibility** |
| --- | --- | --- | --- | --- | --- | --- |
| I1 | Rotation / Press | Usage IDs | Digital High/Low | GPIO SPO bus polling | Error Handling on display | William Thomas |
| I2 | OS hardware events | Method calls | Internal objects | OS kernel interrupts | Warning logged and handled by Device Application | William Thomas |
| I3 | API request | JSON | JSON | HTTPS / REST | Display offline screen handled by Device Application | Elliot Warner |
| I4 | Display instructions | Light activation | Pixel array stream | HUB75 Board Interface | Displays every pixel as red | Elliot Warner |
| I5 | Python file payload | Status message (JSON) | Python script | HTTP POST | Returns 400 and displayed on UI | Nate Heath |
| I6 | Sanitized Python script | Execution status | Python script | Local file system | Log to console and handled by Device Application | Nate Heath |

**Network API Endpoints**

**Endpoint Name:** `POST /api/v1/programs/upload`

**Component:** Upload Server

**Description:** Accepts a Python file from the local machine, sends it to get sanitized, and then stores it on the Orange Pi device (if it is considered safe).

**Inputs:**

- `file` (multipart/form-data): The Python file to upload
  - Required: Yes
  - Valid Range: File size must be greater than 0 bytes and strictly less than 5 MB; the file extension must be exactly `.py`

**Outputs:**

A JSON object representing the execution status and metadata of the uploaded file.

- `status` (int): Indicates the upload result
- `programID` (int): The primary key for the program
- `fileName` (string): The name of the Python file
- `slotNumber` (int): The index slot assigned for this program to deploy to
- `sanitizationPassed` (boolean): `true` if the scanner found no malicious patterns, `false` otherwise
- `uploadTime` (DateTime): The date and time the file was saved

**Error Responses:**

- `400 Bad Request`: Returned if the file is missing, the file uploaded is not a Python file, or if `sanitizationPassed` is false.
- `500 Internal Server Error`: Returned if the Orange Pi runs into a hardware/OS failure and can’t save the file.

**Example Request:**

```http
POST /api/v1/programs/upload HTTP/1.1
Host: orangepi.local:8000
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="weather_tracking.py"
Content-Type: text/x-python

import time
# User's Python app code here...
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

**Example Response:**

```json
{
  "status": "success",
  "programID": 104,
  "fileName": "weather_tracking.py",
  "slotNumber": 2,
  "sanitizationPassed": true,
  "uploadTime": "2026-10-08T19:35:00Z"
}
```

**Technology Stack**

| Layer | Choice | Why |
| --- | --- | --- |
| Device Frontend | HUB75 | The display is simple to work with and provides an outlet for creative displays along with being cheap. |
| Device Backend | OrangePI | It’s cheap and accessible. |
| Driver/App Frontend | React | It is a flexible and easy to use frontend framework. |
| Driver/app Backend | FastApi | It uses python and is easy to work with along with fitting the scope of this project. |
| Database | Supabase | It’s free plan is very generous and uses postgres sql which we all know well. |
| Application hosting | Github | It’s free and very trusted and heavily used. |

**Known Constraints**

**Economic**

Economic constraints are important because the physical product must be affordable for the general public (a budget of $150 is deemed as reasonable). The product requires obtaining an Orange Pi Zero 3 and a 64x32 LED display. The team must prioritize these components and avoid unnecessary hardware so that the device remains affordable to build and reproduce. This limits the complexity of the physical device and encourages us to perform application processing directly on the Orange Pi device rather than deferring computing to more expensive hardware.

**Professional**

Professional constraints are important because the project is being developed by a three-person team, so the software must be maintainable and understandable by developers beyond the original team. The open-source application library should hide boilerplate code behind a simple API so that users can focus on creating applications themselves, rather than how to communicate with the display board and Orange Pi. This creates a conflict between Professional and Economic constraints because creating a well-documented, reusable library requires additional development time. We therefore need to limit the initial library to a manageable set of core features while leaving the architecture extensible for future contributors.

**Legal**

Legal constraints factor into this project because the application will be open-source and may eventually include code or dependencies created by other developers. The team must therefore select compatible open-source licenses for our own code and follow the license requirements of any third-party libraries used to control the Orange Pi or LED display.

**Traceability Matrix**

| Req ID | Description | Design Component | Status | Test Case |
| --- | --- | --- | --- | --- |
| REQ-1 | Allow User to Download Software onto Device | Orange Pi / React Frontend | Incomplete |  |
| REQ-2 | Allow User to Download Application for Device | Orange Pi / React Frontend / Api | Incomplete |  |
| REQ-3 | Display software on Screen | Orange Pi / Hub75 Board | Incomplete |  |
| REQ-4 | Hosting Application and Software Server | Vercel or other Provider | Incomplete |  |
| REQ-5 | Orange Pi to Hub75 library | Orange Pi / Hub75 | Incomplete |  |
| REQ-6 | Handle Initialization Errors | Orange Pi / Logging and status script | Incomplete |  |
| REQ-7 | Implement Dial navigation | Dial / Orange Pi /  Developer SDK | Incomplete |  |
| REQ-8 | Handle Application Errors | Orange Pi / Error code | Incomplete |  |
| REQ-9 | FastAPI Backend for API | Render or other provider | Incomplete |  |
| REQ-10 | App management / download | Github / Orange Pi / Frontend | Incomplete |  |
| REQ-11 | App verification and rating | Frontend / Github | Incomplete |  |

**Review**
