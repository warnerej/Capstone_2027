**Assignment 10 – Test and Validation Plan**

**Team Members:** Nate Heath, Elliot Warner, William Thomas

**Project Title:** Data-Day

**Goal Statement:** To build and open source a 64 x 32 pixel desktop display and the developer platform for creating applications for the device.

**QA / Test Lead:** Nate Heath (owner of T15, full-system integration). Each component owner wrote the cases for their own component.

**Version and Change Log**

| **Version** | **Date** | **Change** | **Author** |
| --- | --- | --- | --- |
| 0.1 | 11/4/26 | Initial test plan draft. Test cases renumbered TC-01 to TC-42 against the requirement IDs in Requirements_Document.md; Week 9 traceability matrix updated | Data-Day team |

**1. Scope**

**1.1 – In Scope**

| **Req ID** | **Requirement** | **Priority** | **Component(s) being tested** | **Owner** | **Status** | **Test Cases** |
| --- | --- | --- | --- | --- | --- | --- |
| REQ-01 | Display app content; every pixel addressable by (x, y) | Must | Display, SDK framebuffer | Nate | Not Run | TC-01 – TC-04 |
| REQ-02 | Navigate app list with CW / CCW dial rotation | Must | Dial Button, selection view | Elliot | Not Run | TC-05 – TC-08 |
| REQ-03 | Launch highlighted app with dial press | Must | Dial Button, Orange Pi app | Elliot | Not Run | TC-09 – TC-12 |
| REQ-04 | SDK: clear, set pixel, text/shapes, present frame | Must | SDK, Display | Nate | Not Run | TC-13 – TC-16 |
| REQ-05 | Upload app file over the local network | Must | Upload server, upload page | William | Not Run | TC-17 – TC-20 |
| NFR-02 | Reject invalid or flagged application files | Must | Upload Server, malware scanner | William | Not Run | TC-21 – TC-24 |
| REQ-06 | Maintain installed-app list for the selection view | Should | Orange Pi app registry | William | Not Run | TC-25 – TC-27 |
| REQ-07 | Remove one app without affecting core or other apps | Should | Orange Pi app, Upload Server | William | Not Run | TC-28 – TC-30 |
| NFR-01 | Frame shown within 0.5 s of a completed frame | Should | Display driver, SDK | Nate | Not Run | TC-34 – TC-36 |
| NFR-03 | User selects and launches an installed app within 20 s | Should | Dial Button, selection view | Elliot | Not Run | TC-37 – TC-39 |
| NFR-04 | Recover to selection view within 10 s after a crash | Should | Orange Pi app | William | Not Run | TC-40 – TC-42 |
| REQ-08 | Return to selection view after an app stops or crashes | Could | Orange Pi app | William | Not Run | TC-31 – TC-33 |

**1.2 – Out of Scope**

| **Item** | **Type** | **Reason for exclusion** |
| --- | --- | --- |
| Public online marketplace / app store | Won’t | Listed as Won’t Have in MoSCoW Summary. Not anticipated to be large-scale enough to focus on now. |
| Cloud-based execution / remote rendering | Won’t | Listed as Won’t Have in MoSCoW Summary. CON-06 requires all execution and rendering to take place on Orange Pi |
| Mobile app to manage device | Won’t | Listed as Won’t Have in MoSCoW Summary. Too time-consuming considering team’s lack of mobile app development experience. |
| Support for display hardware | Won’t | Listed as Won’t Have in MoSCoW Summary. Out of scope with current responsibility as team members and product owners. |
| Account and permissions system; Supabase database | Won't | Won't Have. The device is single-owner on a local network (ASM-05). |

**2. Verification and Validation Strategy**

**Verification (team, against the spec)**

The team verifies REQ-01, REQ-04, REQ-05, REQ-06, REQ-07, REQ-08, NFR-01, NFR-02, and NFR-04 against the Requirements Document, the D1 API contract (POST /api/v1/programs/upload, file size > 0 bytes and < 5 MB, .py extension only), and the D2 pseudocode and edge-case table. This is done through code review on every pull request, automated unit and integration tests run with pytest on the developer laptops, and scripted system tests on the bench device.

**Validation (users and advisor, against stakeholder needs)**

REQ-02, REQ-03, and NFR-03 are validated with real device users in a timed usability session (TC-37 to TC-39). The overall goal of building an app without hardware expertise (REQ-04, US03) is validated by a developer outside the team, ideally from a peer-review team, who builds a sample app using only the SDK documentation. Our project mentor, Professor Aurisano, validates the end-to-end path at the milestone 10, 12, and 15 demos and signs off on acceptance.

**Test Pyramid**

| **Layer** | **Approx. count** | **Coverage focus** |
| --- | --- | --- |
| Unit | ~45 | Framebuffer set/clear/text/shape operations, coordinate and color validation, dial debouncer and selection-index wraparound, malware scanner rules, upload validation (size, extension, syntax) |
| Integration | ~15 | API endpoint → scanner → file system → app registry; dial driver → selection controller; SDK → HUB75 driver → panel; app manager launch / stop / crash detection |
| System | ~10 | End-to-end flows on the bench device: upload → list → select → launch → display; crash recovery; latency experiment |
| Acceptance | ~3 | Timed usability session (NFR-03), advisor full-path walkthrough (milestone 12), final product sign-off (milestone 15) |

**Hardware experiment parameters.** NFR-01 and NFR-04 are timing experiments on physical hardware, so they are planned as controlled trials:

| **Experiment** | **Independent variable** | **Dependent variable** | **Measurement** | **Tolerance** | **Trials** |
| --- | --- | --- | --- | --- | --- |
| Frame latency (NFR-01, TC-34/35) | Frame content: one pixel changed, full-frame color flip, text redraw; upload load present or absent | Time from present() call to visible panel change | present() drives a spare GPIO pin high, which lights an indicator LED next to the panel. A phone records at 60 fps and the tester counts video frames from the indicator LED lighting to the panel changing. | About 15 ms, or one 60fps frame | 30 per condition (90 total, plus 30 under load) |
| Crash recovery (NFR-04, TC-40–42) | Crash type: raised exception, sys.exit(3), kill -9 | Time from crash to selection view visible | Device log timestamp APP_CRASH → SELECTION_VIEW_SHOWN, cross-checked against a stopwatch | 1s (stopwatch) | 10 per type |
| Dial accuracy (REQ-02, TC-05–08) | Rotation rate: 1 tick/s, 10 tick/s | Missed or duplicated selection events | Ticks counted by the tester against a metronome, compared with ROTATE_CW / ROTATE_CCW log lines | 0 duplicates; ≤ 2% missed at 10/s | 3 runs per rate |

**3. Success Criteria**

| **ID** | **Category** | **Metric** | **Target threshold** | **How it is measured** | **Req ID** |
| --- | --- | --- | --- | --- | --- |
| M-01 | Accuracy | Pixel addressing correctness | 2048 / 2048 coordinates map to the correct framebuffer cell; 4 corners lit correctly on the panel in 5 / 5 boots | Framebuffer readback unit test; photo of the corner pattern | REQ-01 |
| M-02 | Reliability | Out-of-bounds and malformed draws rejected | 100% of 200 random out-of-bounds draws and 100% of malformed frames rejected, with the framebuffer checksum unchanged | Unit and integration tests | REQ-01, REQ-04 |
| M-03 | Accuracy | Dial navigation accuracy | 100 / 100 ticks in each direction move the selection by exactly one; wraparound correct | Event log compared with tick count | REQ-02 |
| M-04 | Reliability | Rapid-rotation event loss | 0 duplicated events; ≤ 1 missed in 50 ticks at 10 ticks/s | Same as M-03, 3 runs | REQ-02 |
| M-05 | Reliability | Launch success rate | 50 / 50 dial presses launch the highlighted app, and only one app is running at a time | Event log plus ps on the device | REQ-03 |
| M-06 | Accuracy | SDK function correctness | 100% of public SDK functions have passing unit tests with 0-pixel difference from golden fixtures; ≥ 80% line coverage on the SDK package | pytest --cov report | REQ-04 |
| M-07 | Reliability | Upload integrity | 20 / 20 valid files return HTTP 200 with every D1 response field | curl script with checksum compare | REQ-05 |
| M-08 | Accuracy | Invalid / unsafe file rejection | 30 / 30 invalid-corpus files return HTTP 400 with a reason; 0 / 20 valid files falsely rejected; 0 bytes of user code modified | Integration test over fixtures | NFR-02 |
| M-09 | Accuracy | App list correctness | The list matches installed files exactly after 10 install/remove operations and after a reboot | List endpoint vs. directory listing | REQ-06 |
| M-10 | Reliability | Removal isolation | Remaining apps all launch; SHA-256 of core install directory unchanged | Checksum before and after | REQ-07 |
| M-11 | Performance | Frame display latency | 100% of 120 trials ≤ 500 ms | 60 fps video experiment | NFR-01 |
| M-12 | Usability | Select-and-launch task time | ≥ 4 of 5 first-time participants finish within 20 s, unaided, in each of TC-37 to TC-39 | Stopwatch, moderated session | NFR-03 |
| M-13 | Reliability | Crash recovery time | 30 / 30 forced crashes reach the selection view in ≤ 10 s | Log timestamps | NFR-04 |
| M-14 | Reliability | Return after app stop | 10 / 10 clean exits and 10 / 10 crashes return to a navigable selection view | Event log plus one dial rotation | REQ-08 |

**4. Test Cases**

**Shared setup (referenced by the steps below)**

"Bench device" means the staging environment, freshly booted with a fixture set installed: counter_app.py (slot 0), clock_demo.py (slot 1), pattern_corners.py (slot 2). All fixtures live in `tests/fixtures/`. "Device log" means `/var/log/dataday/device.log`, read over SSH with tail -f. Coordinates use x = 0–63 (left to right) and y = 0–31 (top to bottom). Unit cases run on a developer laptop with pytest and a mocked GPIO and driver.

**4.1 – REQ-01: Display application content (Owner: Nate Heath)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-01 | [Unit] Every pixel is reachable by its coordinates. Traces to REQ-01. | 1. Run pytest tests/unit/test_framebuffer.py::test_all_coordinates. 2. The test calls set_pixel(x, y, color) with a unique color per cell for all 64 × 32 cells. 3. It reads every cell back with get_pixel(x, y). | 2048 / 2048 cells return the color written to them; no other cell changed. | Not Run |
| TC-02 | [System] Corner pattern renders correctly on the physical panel. Traces to REQ-01. | 1. On the bench device, rotate to pattern_corners.py and press the dial. 2. Photograph the panel from 30 cm. 3. Power-cycle and repeat 5 times. | In 5 / 5 boots, (0,0) is red, (63,0) green, (0,31) blue, (63,31) white, and no other pixel is lit. | Not Run |
| TC-03 | [Unit, edge EC09] Out-of-bounds draws are rejected. Traces to REQ-01. | 1. Record the framebuffer checksum. 2. Call set_pixel with (-1,0), (64,0), (0,32), (0,-1), and (64,32), then 200 random out-of-range coordinates. 3. Record the checksum again. | Each call raises ValueError; the checksum is unchanged; no exception escapes the test harness. | Not Run |
| TC-04 | [Integration, failure EC11/EC12] A malformed frame is rejected and the last valid frame is kept. | 1. Launch pattern_corners.py. 2. From a test app, call present() with a 63 × 32 buffer, then with a pixel channel value of 256. 3. Photograph the panel. 4. Switch off the panel's 5 V supply for 5 s, then switch it back on. | Both frames are rejected with FRAME_INVALID in the device log; the panel still shows the corner pattern; after power returns, the panel resumes the last valid frame within 10 s and the device app is still running. | Not Run |

**4.2 – REQ-02: Navigate with dial rotation (Owner: Elliot Warner)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-05 | [Integration] Clockwise rotation moves the selection forward. Traces to REQ-02. | 1. On the bench device, confirm the selection view highlights slot 0. 2. Rotate the main dial 1 tick clockwise. 3. Rotate 99 more ticks clockwise at about 1 per second. | After step 2, clock_demo.py is highlighted and the log shows one ROTATE_CW. After step 3, the log shows exactly 100 ROTATE_CW events and slot 1 is highlighted (100 mod 3). | Not Run |
| TC-06 | [Integration, edge EC01] Counterclockwise rotation wraps from the first slot to the last. Traces to REQ-02. | 1. Highlight slot 0. 2. Rotate 1 tick counterclockwise. 3. Rotate 99 more ticks counterclockwise at about 1 per second. | After step 2, slot 2 (pattern_corners.py) is highlighted. After step 3, there are exactly 100 ROTATE_CCW events and slot 2 is highlighted. | Not Run |
| TC-07 | [Unit, edge] Selection-index boundaries. Traces to REQ-02. | 1. Run pytest tests/unit/test_selection.py, which drives the controller with mocked events for list sizes 3, 1, and 0. | Size 3: index 2 + CW → 0, index 0 + CCW → 2. Size 1: the index stays 0 in both directions. Size 0: the controller shows "No Applications Available" and raises no exception. | Not Run |
| TC-08 | [Integration, edge EC02/EC03] Rapid rotation and contact bounce. Traces to REQ-02. | 1. Highlight slot 0 and clear the log. 2. Rotate 50 ticks clockwise in 5 s, timed with a 10 Hz metronome. 3. Count the ROTATE_CW lines. 4. Repeat 3 times. 5. Run the unit test that injects 1 ms bounce pulses on the encoder line. | Each run logs 49 or 50 events and never more than 50 (0 duplicates, ≤ 1 missed). The injected bounce pulses produce 0 events. | Not Run |

**4.3 – REQ-03: Launch with dial press (Owner: Elliot Warner)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-09 | [System] Pressing the dial launches the highlighted app. Traces to REQ-03. | 1. On the bench device, highlight slot 1. 2. Press the dial once. 3. Repeat 50 times, cycling through slots 0–2, returning to the selection view by rotating between launches. | clock_demo.py output appears and the log shows APP_LAUNCH slot=1. Across 50 presses, 50 / 50 launch the highlighted app. | Not Run |
| TC-10 | [System, edge EC06] Switching apps stops the previous one first. Traces to REQ-03. | 1. Launch counter_app.py. 2. Rotate to slot 2 and press. 3. Over SSH, run `ps aux \| grep dataday_app`. | The log shows on_save called for counter_app.py, then APP_STOP slot=0, then APP_LAUNCH slot=2. Exactly one user-app process is running (ASM-04). | Not Run |
| TC-11 | [Integration, failure EC07] An app with an invalid entry point is refused. Traces to REQ-03. | 1. Over SSH, copy fixtures/invalid/no_entry.py (no main()) directly into the apps directory, bypassing upload, and restart the service. 2. Highlight it and press the dial. | No launch; the panel shows "Cannot launch app" for 3 s and then the selection view; the log shows APP_INVALID; the device app keeps running. | Not Run |
| TC-12 | [Unit, edge] A double press or long hold launches only once. Traces to REQ-03. | 1. Run pytest tests/unit/test_press.py, which injects two presses 100 ms apart, then one press held for 3 s. | Each sequence produces exactly one APP_LAUNCH event. | Not Run |

**4.4 – REQ-04: SDK drawing functions (Owner: Nate Heath)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-13 | [Unit] clear() sets every pixel. Traces to REQ-04. | 1. Fill the framebuffer with random values. 2. Call clear((0, 0, 255)). 3. Call clear() with no argument. 4. Read back all cells after each call. | After step 2, 2048 / 2048 cells are (0,0,255). After step 3, all cells are (0,0,0). | Not Run |
| TC-14 | [Unit] Text and shapes match golden references. Traces to REQ-04. | 1. Run pytest tests/unit/test_draw.py, which calls draw_text("HELLO", 0, 0), draw_line(0,0,63,31), and draw_rect(10,5,20,10). 2. Compare each frame to the .json golden fixtures. | A 0-pixel difference for every function. | Not Run |
| TC-15 | [System] A sample app uses every SDK function on the device. Traces to REQ-04. | 1. Upload fixtures/valid/sdk_tour.py, which calls each public SDK function once, 2 s apart. 2. Launch it and follow the printed checklist on the panel. 3. Run grep -E "import .*driver" sdk_tour.py. | Every checklist frame matches its reference photo; no exception in the device log; the grep returns nothing (the app never touches the driver directly). | Not Run |
| TC-16 | [Unit, edge] Text clipping and invalid colors. Traces to REQ-04. | 1. Call draw_text("WWWWWWWWWWWW", 60, 0). 2. Call set_pixel(0, 0, (256, 0, 0)) and set_pixel(0, 0, (-1, 0, 0)). | Step 1: pixels past x = 63 are clipped; nothing wraps to the next row; no exception. Step 2: both calls raise ValueError and the pixel is unchanged. | Not Run |

**4.5 – REQ-05: Upload over local network (Owner: William Thomas)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-17 | [Integration] A valid file uploads intact through the API. Traces to REQ-05. | 1. From the test laptop, run curl -F "file=@fixtures/valid/counter_app.py" http://orangepi.local:8000/api/v1/programs/upload. 2. Over SSH, run sha256sum on the stored file and compare it with the local file. | HTTP 200. The JSON contains programID, fileName, slotNumber, sanitizationPassed: true, and uploadTime. The checksums match. | Not Run |
| TC-18 | [System] Upload through the React page. Traces to REQ-05. | 1. In Chrome on the test laptop, open http://orangepi.local:8000. 2. Choose clock_demo.py and click Upload. 3. Look at the device selection view. | The page shows "Upload successful – slot N". The app appears in the selection view within 10 s without a reboot. | Not Run |
| TC-19 | [Integration, boundary EC05] File-size limits. Traces to REQ-05. | 1. Upload size_0.py (0 bytes), size_max.py (5,242,879 bytes), and size_over.py (5,242,880 bytes) with the curl command from TC-17. | size_max.py returns 200. size_0.py and size_over.py return 400 with the reason "file size out of range". The apps directory gains exactly one file. | Not Run |
| TC-20 | [System, failure EC06/EC08] Upload during a running app; network drop mid-upload. | 1. Launch counter_app.py. 2. Upload size_max.py and watch the counter. 3. Start a second upload of size_max_b.py and turn off the laptop's Wi-Fi at about 50% progress. 4. Over SSH, list the apps directory. | The counter never pauses for more than 1 s during step 2. In step 4, there is no partial or temp file from size_max_b.py and no new list entry; counter_app.py is still running. | Not Run |

**4.6 – NFR-02: Reject invalid or unsafe files (Owner: William Thomas)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-21 | [Unit] The scanner flags denylisted code, including aliases. Traces to NFR-02. | 1. Run pytest tests/unit/test_scanner.py over fixtures/unsafe/ (12 files: import os, import subprocess as sp, from os import system, socket, shutil.rmtree, eval, exec, __import__, open("/etc/..."), and others). | 12 / 12 files return sanitizationPassed = false and name the offending line. | Not Run |
| TC-22 | [Unit, failure] No false positives and no modification of user code (PR-01). Traces to NFR-02. | 1. Run the scanner over the 20 files in fixtures/valid/. 2. Compare each output file byte-for-byte with its input. | 0 / 20 flagged; 20 / 20 files byte-identical (the scanner rejects files and never edits them). | Not Run |
| TC-23 | [Integration] The invalid corpus is rejected at the API. Traces to NFR-02. | 1. Record the apps directory file count. 2. Run scripts/upload_corpus.sh fixtures/invalid/, which uploads 30 files: .txt, .py.exe, a binary renamed .py, syntax errors, missing main(), and the unsafe files from TC-21. 3. Record the file count again. | 30 / 30 return HTTP 400 with a non-empty reason; the file count and the app list are unchanged. | Not Run |
| TC-24 | [Integration, edge, known limitation] Indirect imports and code hidden in strings. Traces to NFR-02. | 1. Upload indirect_import.py (importlib.import_module("o"+"s")). 2. Upload string_exec.py (exec("imp"+"ort os")). 3. Upload getattr_builtins.py (getattr(__builtins__, "ev"+"al")). | All 3 return HTTP 400, because importlib, exec, and getattr(__builtins__, …) are denylisted names. Any file that is accepted is logged as a High-severity defect. | Not Run |

**4.7 – REQ-06: Installed-application list (Owner: William Thomas)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-25 | [Integration] The list matches installed apps. Traces to REQ-06. | 1. Start from F-EMPTY. 2. Upload the 3 F-BASE files. 3. Call GET /api/v1/programs. 4. Rotate through the selection view. | 3 entries, each with a unique programID and slotNumber. The selection view shows the same 3 names in slot order. | Not Run |
| TC-26 | [System] The list persists across a reboot. Traces to REQ-06. | 1. Save the output of GET /api/v1/programs. 2. Run sudo reboot. 3. After boot, call the endpoint again and compare. | The two responses are identical (same IDs, names, and slots). | Not Run |
| TC-27 | [System, edge OQ-03] Empty device on first boot. Traces to REQ-06. | 1. Boot with F-EMPTY. 2. Rotate the dial 3 times in each direction and press once. 3. Upload counter_app.py. | Step 1: "No Applications Available" within 60 s of power-on. Step 2: no crash and no APP_LAUNCH. Step 3: the app appears in the selection view within 10 s without a reboot. | Not Run |

**4.8 – REQ-07: Remove an application (Owner: William Thomas)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-28 | [Integration] Remove one app. Traces to REQ-07. | 1. On the bench device, call DELETE /api/v1/programs/{id} for clock_demo.py. 2. Call GET /api/v1/programs. 3. Launch each remaining app with the dial. | HTTP 200; the file is gone from the apps directory; the list shows 2 apps; both launch. | Not Run |
| TC-29 | [System] Core software is untouched by a removal. Traces to REQ-07. | 1. Run find /opt/dataday -type f -exec sha256sum {} + > before.txt. 2. Remove pattern_corners.py. 3. Repeat step 1 into after.txt and run diff before.txt after.txt. 4. Run systemctl status dataday. | diff prints nothing; the service shows active (running). | Not Run |
| TC-30 | [Integration, edge] Remove a running app; remove a nonexistent ID. Traces to REQ-07. | 1. Launch counter_app.py, then delete it through the API. 2. Call DELETE /api/v1/programs/9999. | Step 1: the app stops, the selection view appears, and the app is removed. Step 2: HTTP 404; no files or list entries change. | Not Run |

**4.9 – REQ-08: Return to selection view (Owner: William Thomas)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-31 | [System] A clean exit returns to the selection view. Traces to REQ-08. | 1. Upload and launch fixtures/crash/exit_clean.py, which draws for 5 s and returns. 2. Repeat 10 times. | 10 / 10 runs show the selection view with the same app highlighted; the log shows APP_STOP reason=exit. | Not Run |
| TC-32 | [System, failure] A crash returns to a navigable selection view. Traces to REQ-08. | 1. Launch crash_raise.py, which raises RuntimeError after 3 s. 2. When the selection view appears, rotate 1 detent clockwise. 3. Repeat 10 times. | 10 / 10 runs show the selection view; the log contains APP_CRASH with a traceback; the rotation changes the highlight each time. | Not Run |
| TC-33 | [System, edge OQ-04] An app that crashes immediately does not loop. Traces to REQ-08. | 1. Launch crash_on_start.py 5 times in a row. 2. Watch the log for 60 s after the last launch. | 5 APP_CRASH lines and no automatic relaunch; the selection view stays responsive. Revisit if OQ-04 adopts auto-restart. | Not Run |

**4.10 – NFR-01: Frame display latency (Owner: Nate Heath)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-34 | [System, experiment] Latency at idle. Traces to NFR-01. | 1. Mount the phone on a tripod, recording at 60 fps, with the panel and the indicator LED in frame. 2. Run latency_probe.py --mode {pixel,flip,text} --trials 30. 3. For each trial, count the video frames from the LED lighting to the panel changing, and multiply by 4.17 ms. | All 90 trials ≤ 500 ms. Record the median and the maximum. | Not Run |
| TC-35 | [System, edge EC06] Latency while an upload is in progress. Traces to NFR-01. | 1. Start a loop that uploads size_max.py repeatedly from the laptop. 2. Run latency_probe.py --mode flip --trials 30 with the same capture as TC-34. | All 30 trials ≤ 500 ms. | Not Run |
| TC-36 | [System, edge EC10] No torn frames during concurrent drawing. Traces to NFR-01. | 1. Run tear_probe.py, which alternates full-red and full-blue frames as fast as the SDK allows, for 60 s. 2. Step through the 60 fps video frame by frame. | 0 video frames show red and blue on the panel at the same time. | Not Run |

**4.11 – NFR-03: Usability of select-and-launch (Owner: Elliot Warner)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-37 | [Acceptance] A first-time user launches a named app. Traces to NFR-03. | 1. Install fixture set F-FIVE (5 apps). The device shows the selection view at slot 0. 2. Give each of 5 participants who have never used the device the card "Start the app called Clock Demo" (slot 1). 3. Start the stopwatch when they finish reading the card; stop it at the app's first frame. Give no hints. | ≥ 4 / 5 participants finish within 20 s. | Not Run |
| TC-38 | [Acceptance, edge] Worst-case position. Traces to NFR-03. | 1. Same setup and participants as TC-37, but the card names the app in slot 4. | ≥ 4 / 5 finish within 20 s. Record whether each participant wrapped counterclockwise or went forward. | Not Run |
| TC-39 | [Acceptance, edge] Start while another app is running. Traces to NFR-03. | 1. Launch counter_app.py first. 2. Give the card naming slot 3, then run as in TC-37. | ≥ 4 / 5 finish within 20 s, and none of them needs to power-cycle the device. | Not Run |

**4.12 – NFR-04: Crash recovery time (Owner: William Thomas)**

| **ID** | **Description** | **Steps** | **Expected Result** | **Status** |
| --- | --- | --- | --- | --- |
| TC-40 | [System, experiment] Recovery from a raised exception. Traces to NFR-04. | 1. Launch crash_raise.py 10 times. 2. For each run, subtract the APP_CRASH log timestamp from the SELECTION_VIEW_SHOWN timestamp. | 10 / 10 ≤ 10 s. | Not Run |
| TC-41 | [System, failure] Recovery from a nonzero exit and a killed process. Traces to NFR-04. | 1. Launch crash_exit.py (sys.exit(3)) 10 times. 2. Launch counter_app.py 10 times and run sudo kill -9 <pid> over SSH each time. 3. Time each run as in TC-40. | 20 / 20 ≤ 10 s; the device app service is never restarted by systemd (it recovers on its own). | Not Run |
| TC-42 | [System, edge EC06] Crash during an upload. Traces to NFR-04. | 1. Launch crash_raise.py. 2. Start an upload of size_max.py so that it is in progress when the crash happens. | Recovery ≤ 10 s, and the upload still returns HTTP 200 with a correct checksum. | Not Run |

**5. Test Environment**

| **Item** | **Development (unit and integration; every commit)** | **Staging – bench device (system tests; milestone demos 7–13)** | **Pilot (acceptance TC-37–39; milestone 15 sign-off)** |
| --- | --- | --- | --- |
| Hardware | Team laptops (Windows 11, macOS 14+, Ubuntu 24.04). Tools: pytest --cov HTML report | Orange Pi Zero 3 (RAM size recorded on the board label), 64x32 HUB75 RGB panel, 5 V supply sized per T11, two rotary encoders with push switch, indicator LED on a spare GPIO, 32 GB microSD card. Tools: phone camera recording at 60 fps on a tripod, stopwatch, SSH session to the device log | The finished device in its housing, on a desk in a tester's home. Tools: stopwatch, session observer sheet |
| OS / runtime / browser | Python 3.11 in a venv; pytest 8, pytest-cov, FastAPI TestClient, httpx; mocked GPIO and display driver; Node 20 LTS for the React page; Chrome (current stable) and Firefox (current stable); versions written into each test log | Armbian (Debian 12 Bookworm base); Python 3.11; FastAPI and Uvicorn served at orangepi.local:8000; dataday systemd service. Browser: Chrome on the test laptop for TC-18 | Same image as staging, flashed from the release tag. Browser: Chrome on the participant's or tester's laptop |
| Version control | All Python versions pinned in requirements.txt, React versions in package-lock.json, and the OS image filename and SHA-256 in docs/ENVIRONMENT.md (to be recorded in T6 and T11) | Same pins, plus the image checksum verified before each system-test run | Same as staging |
| Test data | tests/fixtures/: golden framebuffer .json files, F-BASE, F-EMPTY, F-FIVE, valid/ (20 files, 0.5 KB to 5,242,879 B), invalid/ (30 files), unsafe/ (12 files), crash/ (exit_clean, crash_raise, crash_exit, crash_on_start), probes (latency_probe, tear_probe, sdk_tour) | The same fixtures, uploaded with scripts/seed_device.sh <set>, which wipes the apps directory and installs the named set | F-FIVE only |
| Network | No network needed (mocked) | A dedicated 2.4 GHz test router with no internet route, used only by the device and the test laptop. Degraded condition: laptop Wi-Fi switched off mid-upload (TC-20) | The tester's home Wi-Fi |

**Test hooks the code must provide** (added to tasks T3, T8, T10, and T13): get_pixel() readback in the SDK; the log events ROTATE_CW, ROTATE_CCW, APP_LAUNCH, APP_STOP, APP_CRASH, APP_INVALID, FRAME_INVALID, and SELECTION_VIEW_SHOWN, each with a millisecond timestamp; GPIO pulse on present() when started with --latency-probe; and the endpoints GET /api/v1/programs and DELETE /api/v1/programs/{id}, which extend the D1 contract.

**6. Pass and Fail Criteria**

**Per test case**

- **Pass:** every condition in the Expected Result column is met in the stated number of trials, with numbers measured as described in §3.

- **Fail:** any condition is not met, including a single trial over a threshold (for example, one of 120 latency trials at 510 ms fails TC-34 or TC-35). A failure is logged as a GitHub issue that names the TC ID, the measured value, and a severity.

- **Blocked:** a precondition is unavailable (for example, the panel has not arrived or a dependent case failed). The blocker is logged. A Blocked case does not count as a pass.

- **Not Run:** the case has not been executed in this cycle.

- **Retest rule:** after a fix, the failed case and every other case in its group (§4.x) are re-run on the same environment. The result is only recorded as Pass once the whole group passes.

**Defect severity**

| **Severity** | **Definition (example)** |
| --- | --- |
| Critical | Device unusable, data lost, or an unsafe file accepted (e.g., an unsafe file passes upload (TC-21/23/24); the device app crashes and does not recover) |
| High | A Must requirement or its metric fails (e.g., pixel mapping wrong (TC-01); a valid upload fails (TC-17)) |
| Medium | A Should or Could requirement or its metric fails, and a workaround exists (e.g., recovery takes 12 s (TC-40)) |
| Low | Cosmetic issue; no requirement affected (e.g., error text misaligned on the panel) |

**Plan as a whole.** The test cycle passes when all of the following are true:

- Every Must test case (TC-01 to TC-24) is Pass, and metrics M-01 to M-08 meet their thresholds.

- At least 90% of Should and Could cases (TC-25 to TC-42) are Pass. Any failing Should metric (M-09 to M-14) has an issue logged with a fix date or a waiver that Professor Aurisano has approved in writing.

- Zero open Critical or High defects; at most 3 open Medium defects, each with an owner.

- No case is still Blocked or Not Run at the milestone 15 sign-off.

The plan fails if any of these conditions is not met by 4/15/27. In that case, the team cuts scope using the R3 mitigation (dropping a feature from the demo path) rather than lowering a threshold.

**7. Test Schedule**

Unit and integration tests are written alongside the code during each sprint and run on every pull request. System and acceptance tests are scheduled runs held before each milestone review. Tests run in risk order: the upload/scanner path (NFR-02, REQ-05) and the display path (REQ-01, risks R1 and R2) are tested first, then navigation, then the Should and Could items.

| **Milestone (Week 8)** | **Date** | **Test activity** | **Layer** | **Owner** | **Evidence** |
| --- | --- | --- | --- | --- | --- |
| 4 – Requirements list; Week 10 test plan | 10/30 – 11/6/26 | Requirement IDs frozen as the test baseline; test plan and corrected traceability matrix submitted | — | Nate (QA lead) | Requirements_Document.md v0.1; Test_Plan.md / PDF |
| 5 – Presentation | 11/18/26 | Present the test strategy and definition of done; advisor agrees on thresholds | — | All | Slides; advisor notes |
| 6 – Design notebook | 12/2/26 | Fixtures and test hooks specified; pytest harness and mocked GPIO/driver committed | Unit | Nate, Elliot | Harness in repo; CI run log |
| Sprint T1–T4, T11–T14 | 12/1 – 12/20/26 | TC-01, 03, 05–08, 12–14, 16 written with the code | Unit, Integration | Elliot (dial), Nate (display) | pytest and coverage reports attached to PRs |
| 7 – Working prototype | 1/7/27 | TC-02, TC-04; first NFR-01 latency run (TC-34) | System | Nate | Panel photos; latency spreadsheet; advisor demo |
| 8 / 9 – SDK defined / complete | 2/2 – 2/14/27 | TC-13–16 complete; SDK coverage ≥ 80% (M-06) | Unit | Nate | Coverage report |
| Sprint T8–T10 | 2/8 – 3/5/27 | TC-17, 19, 21–25, 28, 30 written with the code | Unit, Integration | William | pytest reports; corpus script output |
| 10 – Sample program runs | 3/7/27 | TC-09–11, TC-15, TC-31–33 | System | Elliot, William | Test log; video |
| 11 – Valid upload | 3/15/27 | TC-18, 20, 23, 24, 26, 27, 29; NFR-02 sign-off | System | William | Test log; advisor demo |
| 12 – Full path works | 3/22/27 | Full regression of TC-01–36, 40–42; T15 integration | System, Acceptance | Nate | Regression report; advisor walkthrough notes |
| 13 – Demo programs | 3/29/27 | Demo apps pass the TC-21/22 scanner and TC-15 checks | System | All | Test log |
| 14 – Documentation | 4/5/27 | Project-level definition-of-done review (§8) | — | All | Signed checklist |
| Pilot | 4/5 – 4/12/27 | TC-37–39 usability session; pilot run on the housed device | Acceptance | Elliot | Observer sheets; timing table |
| 15 – Complete product | 4/15/27 | Final regression and acceptance sign-off | Acceptance | Nate; Prof. Aurisano | Signed acceptance form; final test report |

**8. Definition of Done**

**Feature level.** A feature is done when:

- Code is merged to main through a pull request reviewed and approved by at least one teammate who is not the author.

- Unit tests for the feature are written and passing; SDK code meets the ≥ 80% line coverage target.

- Integration tests with the dependent components are passing on the bench device.

- Every TC traced to the feature's requirement ID is Pass, and its §3 metric threshold is met.

- At least one edge or failure case in the group is Pass (not only the happy path).

- Docstrings, README setup steps, and the SDK reference are updated; any new log event or endpoint is documented.

- No open Critical or High defects against the feature.

- The traceability matrix (§1.1) status column is updated.

**Project level.** The project is done when:

- All plan-level pass criteria in §6 are met.

- A full regression run on the staging image tagged for release is Pass, and the image SHA-256 is recorded.

- Usability acceptance (TC-37 to TC-39) is Pass, and an outside developer has built a sample app using only the SDK documentation.

- All documents (requirements, technical specification, test plan, SDK reference, user setup guide) are current, and their version logs are updated.

- Open defects are at most 3 Medium, each with an owner and a target date; no Critical or High defects are open.

- Open-source license files and third-party notices are present (CON-04).

- Professor Aurisano has signed the acceptance form at milestone 15.