Nate Heath, Elliot Warner, William Thomas

CS5001

9/28/26

# User Stories and Use Cases

## Stakeholder Map
- _Primary Stakeholder_ - UC Student
    - A current student at the University of Cincinnati who uses the display to check useful, frequently changing information, such as UC shuttle arrival times or weather for their commute to class.
- _Secondary Stakeholder_ - Device Administrator/Support
    - A person responsible for configuring applications and resolving issues with data retrieval or display operation.
- _Hidden Stakeholder_ - Accessibility
    - A person who may be unable to distinguish information if it relies solely on color or has low contrast, tiny text, or quick animations.

## User Stories
- _US01 - Primary Stakeholder_: As a student walking to class most days, I want to view the weather forecast for the full day each morning, and if the weather is poor for walking to class, view estimated arrival times for the UC shuttle that stops near my house, so that I can prepare for my commute to campus each morning. 
- _US02 - Primary Stakeholder_: As a student managing a busy schedule, I want to view my upcoming calendar events without opening my phone or computer so I can stay on top of upcoming commitments without getting distracted.
- _US03 - Secondary Stakeholder_: As a device maintainer, I want to be able to easily update configurations and applications independently and remotely, so I can maintain applications without needing to modify the hardware in-person.
- _US04 - Hidden Stakeholder_: As someone with a visual impairment, I want information to be displayed at a reasonable size and include use of symbols and color to clearly communicate information so that I can interpret displayed information easily.

## INVEST Check

## Use Cases
### _UC01 - Planning Full Commute with Shuttle Times_:
  - Primary Actor: UC Student
  - Secondary Actors: UC Shuttle Tracking API, Weather Data API
  - Preconditions: The device is powered on, connected to the internet, and configured with the student’s zip code/preferred shuttle stop
 
  #### Main Flow:
  1) **Actor**: The student views the display device in the morning.
  2) **System**: The system fetches and displays the current day's weather forecast (temperature, precipitation chance, etc.).
  3) **Actor**: The student notices a high chance of rain and decides to check the shuttle times.
  4) **System**: The system fetches live tracking data from the Shuttle Tracking API for their desired stop.
  5) **System**: The system displays the _estimated_ arrival times for the next 3 arriving shuttles.

  #### Alternate Flow:
  - **At Step 3:** The student sees clear weather with a 0% chance of rain, decides to walk to class, and does not interact further with the display.
  - **System:** The system does not issue a request to the UC Shuttle Tracking API.

  #### Exception Flow:
  - **At Step 4:** The system sends a GET request to the UC Shuttle Tracking API, but the API times out or returns a server error.
  - **System Action:** 
    1) The system displays an error banner reading "Live tracking unavailable."
    2) The system falls back to displaying the static, scheduled arrival times stored locally for that stop (based on history).

## Acceptance Criteria
- _AC-01.1_:\
  **Given** the display is connected to the internet and configured for the students stop,\
  **When** the student opens the Shuttle view,\
  **Then** the system displays the estimated arrival times for the next 3 scheduled shuttles and refreshes every 60 seconds.

- _AC-01.2_:\
  **Given** the UC Shuttle Tracking API fails to respond in 5 seconds,\
  **When** the system is requesting live data,\
  **Then** the system will display a visual "Live tracking is unavailable" message 
