# Advisor Review & Recording Plan

**Project:** Data-Day -- Open-Source LED Application Platform

**Team:** Nate Heath, Elliot Warner, William Thomas

**Advisor:** Prof. Jillian Aurisano

**Deck:** `DataDay_Presentation.pptx` (12 slides)

---

## 1. Meeting Record

| Field | Entry |
|---|---|
| Date | 11/17/26 |
| Duration | ~30 minutes |
| Attendees | Nate Heath, Elliot Warner, William Thomas, Prof. Jillian Aurisano |
| Format | In-person, Live deck walkthrough & critique |
| Deck Reviewed | `DataDay_Presentation.pptx` (Draft v1, 12 slides) |

---

## 2. Feedback Log

| Slides Affected | Advisor Feedback | Team Response | Change Made | Owner | Status |
|---|---|---|---|---|---|
| Slide 7 (Constraints) | Note open-source licenses and ensure all third-party dependencies are valid; follow up to confirm compliance. | **Accepted** | Added license compliance to Project Constraints and Anticipated Questions; initiated a dependency audit to verify MIT/Apache/GPL compatibility. | Elliot | In Progress |
| Slide 9 (End of Term Goals) | Make sure the core framework is fully working right after winter break so the team can focus on building demo apps. | **Accepted** | Updated End of Term Goals and timeline milestones to prioritize hardware/SDK freeze before winter break, reserving Spring for app development. | Will | Complete |
| Slide 6 (How It Works) | Go more in-depth with the dial button hardware/UX on the slide, or ensure it is clearly explained verbally. | **Modified** | Kept the slide diagram clean to preserve visual flow, but expanded Elliot's speaker notes and Q&A prep to detail the dual-dial system vs. app-level input mapping. | Elliot | Complete |
| Slide 10 (Division of Work) | Project is well conceived with a strong, balanced division of labor across the team. | **Accepted** | Maintained current role split (Hardware/System, SDK/Server, Frontend/Apps) and kept Slide 10 concise (30 s). | All | Complete |
| Slides 7 & 9 (Constraints / Goals) | Allow extra buffer time in the schedule for scope creep or unexpected hardware/integration problems. | **Accepted** | Built explicit contingency buffer weeks into the Spring development roadmap and flagged hardware cohesion as our primary risk constraint. | Nate | Complete |

---

## 3. Summary of Changes

After meeting with Prof. Aurisano, we restructured our presentation flow away from the rigid course template to better tell the story of Data-Day as an open-source developer platform. We updated our technical roadmap and Slide 9 milestones to ensure the core Orange Pi framework and SDK are stabilized immediately after winter break, leaving dedicated buffer time for unexpected integration issues and demo app creation. Additionally, we added explicit open-source license verification to our constraints and expanded our verbal talking points on Slide 6 to clearly explain how the physical dial buttons handle both system navigation and in-app controls.

---

## 4. Open Items

| Action Item | Owner | Target Close Date |
|---|---|---|
| Audit all third-party library and SDK licenses and confirm validity with Prof. Aurisano. | Elliot | 11/25/26 |
| Re-time Slides 4 and 11 in final rehearsal to trim variance (+13 s and +27 s) closer to the 9:00 pre-Q&A mark. | Nate | 11/22/26 |
| Record final backup presentation video and upload deck + video links to the Week 13 team spreadsheet. | Will | 11/24/26 |

---

## 5. Recording & Expo Contingency Plan

### Recording Plan

| Field | Plan Details |
|---|---|
| Presenters | Nate Heath, Elliot Warner, William Thomas |
| Recording Software | ClipChamp |
| Target Length | 9:00 – 10:00 total (8:30 – 9:00 core presentation + ~1:00 Q&A/wrap-up). |
| Sharable Link Location | YouTube |
| Week 13 Submission Owner | Nate Heath (posts final `.pptx` deck link and video URL to the official Week 13 team spreadsheet). |

### Expo Demo & Fallback Plan

* **Live Expo Demo Shows:** 
  * 3 Physical Data-Day hardware units running on the Orange Pi Zero 3s connected to the LED matrix displays.
  * Interactive navigation demo using the dual dial buttons (switching between system menu and active applications).
  * Real-time networked data fetching (live sports scores or weather updates) alongside local app uploads using the upload server.
* **Fallback Plan (If Live Demo Fails):** 
  * Switch the booth laptop to a pre-recorded, demo video showing the physical LED matrix running live API feeds, dial-button switching, and local app deployment.
  * Keep the physical hardware on the table as a static display so people can still inspect the Orange Pi Zero 3 setup, enclosure, and dial controls while walking through the SDK architecture.