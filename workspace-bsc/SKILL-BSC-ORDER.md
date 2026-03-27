# SKILL-BSC-ORDER.md — Blossom School Catering: Place Order

Use this skill whenever anyone asks you to place a meal order on the school catering site.

## Trigger Phrases
- "place order", "book lunch", "order food", "add order"
- Any message containing a student username, date(s), session, and dish list

## NEVER Use the Browser for Orders
The site's Add buttons do not respond to browser automation. Always use the API via curl.

---

## SINGLE ORDER FLOW

### Step 0 — Parse the Message
Extract:
- STUDENT_USERNAME — verbatim (e.g. syrowatka_natasha)
- SENDER_PHONE — sender WhatsApp number in digits/E.164 form from message metadata (e.g. +628176917122)
- DATE — convert to YYYY-MM-DD (e.g. "2nd April" → "2026-04-02")
- SESSION — uppercase (lunch → LUNCH, snack → SNACK, breakfast → BREAKFAST)
- DISHES — array of dish names, split by comma or "+"

### Step 1 — Login (once per session)
curl -s -X POST http://34.158.47.112/schoolcatering/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"teameditor123"}'

Extract accessToken. If empty or missing → reply "Login failed — server error" and stop.

### Step 2 — Place Order
curl -s -X POST http://34.158.47.112/schoolcatering/api/v1/order/quick \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN_HERE" \
  -d '{"childUsername":"STUDENT_USERNAME","senderPhone":"SENDER_PHONE","date":"YYYY-MM-DD","session":"SESSION","dishes":["Dish1","Dish2"]}'

If you receive HTTP 401 on Step 2: re-login ONCE (Step 1 again) then retry this step.

### Step 3 — Reply
See REPLY FORMAT section below.

---

## BULK / MULTI-DATE ORDER FLOW

When the message contains orders for multiple dates (e.g. "order for the month of April"):

### Step 0 — Parse ALL dates and dishes
Extract SENDER_PHONE from the sender WhatsApp metadata once for the whole batch.
Build a list: [ {date, dishes}, {date, dishes}, ... ]
Skip any dates that fall on Saturday or Sunday — note them as "weekend, skipped".

### Step 1 — Login ONCE
curl -s -X POST http://34.158.47.112/schoolcatering/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"teameditor123"}'

Extract and store TOKEN. Use this single token for ALL orders in the batch.
If login fails → stop and report error.

### Step 2 — Loop through each date
For each entry in the list:
  curl -s -X POST http://34.158.47.112/schoolcatering/api/v1/order/quick \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer TOKEN" \
    -d '{"childUsername":"STUDENT_USERNAME","senderPhone":"SENDER_PHONE","date":"YYYY-MM-DD","session":"SESSION","dishes":[...]}'

  - If HTTP 401 → re-login once (update TOKEN) then retry this date.
  - Record result (ok/error) for each date.
  - Do NOT stop on individual date failures — continue to next date.

### Step 3 — Reply with full summary
Use the BULK REPLY FORMAT below.

---

## REPLY FORMAT — Single Order

SUCCESS (ok = true):

Brian ♾️ {SenderName},

Yes, I have successfully placed the order:

Order placed : ✅
Order ID     : #{ref}
Date         : {serviceDate}
Session      : {session title-cased}
Items        : {items joined by ", "}
Total        : {totalPriceFormatted}

Enjoy your meal, {studentName}! 🍽️

ERROR:
Brian ♾️ {SenderName},
Order failed ✗ — {concise reason}

---

## REPLY FORMAT — Bulk Order

Brian ♾️ {SenderName},

Here is the order summary for {studentFirstName}:

• {date e.g. Apr 2} — ✅ {dishes} — {totalPriceFormatted}
• {date} — ✅ {dishes} — {totalPriceFormatted}
• {date} — ⏭️ Weekend, skipped
• {date} — ✗ {short error reason}

Total orders placed: {count}
Total amount: {sum of all successful order prices}

Enjoy your meals, {studentFirstName}! 🍽️

---

## Sender Authorization Rule
- Always send `senderPhone` in the `/order/quick` JSON body.
- Whitelisted admin numbers may order for any student.
- All other numbers must match the student phone number or a linked parent phone number stored in Schoolcatering.
- If `senderPhone` is missing for an admin-driven order, the API will reject it.

## ERROR CODES
- "Dishes not found for session..." → dish name(s) not on the {session} menu. Check dish names.
- "ORDER_ALREADY_EXISTS_FOR_DATE" → order already exists for that date/session.
- "ORDER_WEEKEND_SERVICE_BLOCKED" → weekend date, no service.
- "ORDER_BLACKOUT_BLOCKED" → blackout date, no service.
- "SESSION_CUTOFF_PASSED" → ordering cutoff passed for this date.
- "Student with username ... not found" → student username not registered.
- "No linked parent for billing" → student has no linked parent, contact admin.
- anything else → show raw message

## Credentials & Endpoints
See TOOLS.md — BSC section.
Login: admin / teameditor123 (ADMIN role — can order for any student)

## Quick Test Page
http://34.158.47.112/schoolcatering/tools/quick-order
