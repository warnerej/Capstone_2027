**Team Members:** Nate Heath, Elliot Warner, William Thomas 

**Project Title:** Data-Day 

**Goal Statement:** To build and open source a 64 x 32 pixel desktop display and the developer platform for creating applications for the device. 

**Components:** This document will detail the Dial Button, Orange Pi, and Display components of the D0 high level system. The Device Application and Upload Server components were completed in the D1 detailed design. 

# **Peer Review Notes** 

|**ID **|**Rubric**<br>**Criterion **|**Issue & Reviewing**<br>**Team **|**Action **|**Owner  **|**Priority **|**Due**<br>**Date **|
|---|---|---|---|---|---|---|
|**PR-**<br>**01 **|**Risk**|**The security**<br>**algorithm removes**<br>**suspicious lines**<br>**from uploaded**<br>**Python programs.**<br>**Could this break**<br>**otherwise valid**<br>**programs? — Team:**<br>**Nate Poppe and**<br>**Braden Hayes**|**Consider**<br>**rejecting**<br>**flagged**<br>**uploads with**<br>**an**<br>**explanation**<br>**rather than**<br>**modifying**<br>**source**<br>**code.**|<br>**William**<br>**Thomas**|<br>**Worth**<br>**Considering  **|<sup>**Week 9 **</sup>|
|**PR-**<br>**02 **|**Completeness **|<br>**When users switch**<br>**applications using**<br>**the dial, does the**<br>**previous**<br>**application**<br>**continue running or**<br>**terminate? How is**<br>**its state handled? —**<br>**Team: Nate Poppe**<br>**and Braden Hayes**|<br>**Define**<br>**application**<br>**lifecycle**<br>**behavior,**<br>**including**<br>**execution,**<br>**state**<br>**preservation**<br>**, and**<br>**hardware**<br>**resource**<br>**managemen**|**Elliot**<br>**Warner**|<sup>**Must-fix**</sup>|**Week 9 **|



||||**t.**||||
|---|---|---|---|---|---|---|
|**PR-**<br>**03**|**Completeness **|**Component Orange**<br>**Pi 3**<br>**Since you guys are**<br>**choosing to make**<br>**this open source, is**<br>**there any plan on**<br>**creating some type**<br>**of library for**<br>**application to**<br>**interaction with the**<br>**IO’s and Display**<br>**Team: Cameron**<br>**Pocisk, Jake Skaff**|**Define what**<br>**capabilities**<br>**can come**<br>**out of the**<br>**box with the**<br>**device and**<br>**basic**<br>**implementat**<br>**ion with**<br>**clear**<br>**documentati**<br>**on**|**William**<br>**Thomas**|**Important**|**Week 10**|
|**PR-**<br>**04**|**Completeness**<br>**/Risk**|**API:**<br>**/api/v1/program/upl**<br>**oad HTTP/1.1**<br>**Is there a protocol**<br>**needed for large**<br>**python files?**<br>**Team: Cameron**<br>**Pocisk, Jake Skaff**|**Clarify how**<br>**large files**<br>**will be**<br>**handled,**<br>**and**<br>**managed**<br>**with AST**|**Nate**<br>**Heath**|**Must-fix**|**Week 10**|



**Completeness** drew the most feedback across the reviews, appearing in 3 out of the 4 items, mainly focusing on missing documentation/system specifications. 

## **Top two issues raised:** 

- **1) ColorStylus:** There was an issue with their storage silently being overwritten once they reached a maximum capacity, we suggested for them to add an indicator to their light display to notify the user that they are almost at max capacity. 

- **2) Chess Blunder-Risk Analyzer:** There is an unhandled scenario when the user may be importing their game data. If the software crashes mid-request, their upload will permanently be stuck in a “running” state. The main issue is that they only allow one request to be in the “running” state, so a timeout mechanism is necessary to circumvent this. 

