# AI Appointment Setter
A voice AI appointment management system built in n8n that integrates
with Vapi to handle full appointment lifecycles, checking availability,
booking, updating, cancelling, and logging call results all through
a voice call without human involvement.

---

## The Problem

Service businesses lose bookings when no one is available to answer
calls and check calendars in real time. This system gives every
business a 24/7 voice agent that manages appointments end to end
on a live phone call.

---

## What It Does

- Caller speaks to a Vapi voice agent on the phone
- Vapi calls n8n tools in real time depending on what the caller wants
- n8n handles the full appointment logic:
  - Checks available calendar slots
  - Books new appointments on Google Calendar
  - Updates existing appointments
  - Cancels appointments and updates Airtable records
  - Logs every call result to Airtable automatically

---

## Architecture

**Vapi Voice Agent (caller on phone) triggers one of 5 n8n workflows:**

---

**1. GET SLOTS**
WebSlots Trigger
↓
Code Node — extract arguments
↓
Check Availability (Google Calendar)
↓
Is Time Available?
↓
Get All Calendar Events → Extract Start & End → Sort → Format
↓
Available Times & Ranges
↓
Respond to Webhook (back to Vapi)

---

**2. BOOK SLOTS**
BookSlots Trigger
↓
Code Node → Input Args → Has All Info?
↓
Escape JSON → Convert Time to CST (America/Chicago)
↓
Create Event (Google Calendar)
↓
Booking Payload → Success Response → Respond to Vapi
↓
Confirmed Booking? → Save to Airtable → Log Book Details
↓
Error Path → Build Error Response → Respond With Error

---

**3. UPDATE SLOTS**
UpdateSlots Trigger
↓
Code Node → Input Args → Check Required Info
↓
Find Original Appointment
↓
Update Event (Google Calendar) → Update Airtable Record
↓
Respond to Vapi (update results)
↓
Error Path → Build Error Response → Respond With Error

---

**4. CANCEL SLOTS**
CancelSlots Trigger
↓
Code Node → Input Args → Check Required Info
↓
Find Appointment Record (Airtable)
↓
Delete Appointment (Google Calendar)
↓
Update Record to Cancelled (Airtable)
↓
Respond to Vapi (cancellation confirmed)
↓
Error Path → Build Error Response → Respond With Error

---

**5. CALL RESULTS LOG**
call_results Trigger
↓
Extract All Input Arguments
↓
Create or Update Airtable Record
---


## Stack

| Layer | Tool |
|---|---|
| Automation | n8n (self-hosted) |
| Voice AI | Vapi |
| Calendar | Google Calendar |
| Records | Airtable |
| Logic | Code Nodes · Conditional Nodes |

---

## Setup Instructions

1. Import `workflow.json` into your n8n instance
2. Add credentials:
   - Vapi API Key
   - Google Calendar OAuth
   - Airtable API Key + Base ID
3. Set your timezone in the Convert Time node (currently CST)
4. Connect your Vapi assistant to the n8n webhook URLs
5. Test by placing a call through your Vapi assistant

---

## Files

| File | Description |
|---|---|
| `workflow.json` | Importable n8n workflow — all credentials removed |
| `assets/screenshot.png` | Workflow canvas screenshot |

---

Built by Sulyman Habeebullah —
AI Automation Specialist
