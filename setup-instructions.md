# Expo Lead Capture — Setup Guide

## 1. Google Sheets Backend (5 min)

### Step 1: Create the Spreadsheet
1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet called **"Comeketo Expo Leads"**
3. In Row 1, add these headers:
   ```
   A: Full Name | B: Email | C: Phone | D: Event Type | E: Guest Count | F: Event Date | G: Venue | H: How Heard | I: Notes | J: Source | K: Captured At
   ```

### Step 2: Create the Apps Script
1. In the spreadsheet, go to **Extensions → Apps Script**
2. Delete the default code and paste this:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);
    
    sheet.appendRow([
      data.fullName,
      data.email,
      data.phone,
      data.eventType,
      data.guestCount,
      data.eventDate || '',
      data.venue || '',
      data.howHeard || '',
      data.notes || '',
      data.source || 'expo',
      data.capturedAt || new Date().toISOString()
    ]);
    
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'ok' }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

3. Click **Deploy → New deployment**
4. Type: **Web app**
5. Execute as: **Me**
6. Who has access: **Anyone**
7. Click **Deploy** → Copy the URL

### Step 3: Connect to the Landing Page
1. Open `index.html`
2. Find: `const GOOGLE_SHEETS_URL = '';`
3. Paste your URL inside the quotes:
   ```js
   const GOOGLE_SHEETS_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
   ```

## 2. Deploy to GitHub Pages

The page will live at: `rawdre.github.io/comeketo-expo`

## 3. QR Code

Once live, generate a QR code pointing to the URL. 
Use any QR generator or I can make one.

## 4. Uploading to Close CRM

When you have leads in the sheet:
1. Download as CSV from Google Sheets
2. In Close CRM → Import → Upload CSV
3. Map columns: Full Name → Contact Name, Email → Email, Phone → Phone, etc.
4. Set lead owner → André or assign as needed

## Offline Fallback

If the internet goes down at the expo, the form still works!
- Leads are stored in the browser's localStorage
- Type `exportLeads()` in the browser console to download them as CSV
- Or scan a new QR code from a different phone

## What the Guest Sees

1. Scan QR code at booth
2. Quick form: Name, Email, Phone, Event Type, Guest Count (all dropdowns = fast)
3. Optional: Date, Venue, How Heard, Notes
4. Submit → confetti + "Show this screen for your prize!"
5. Rodrigo/team sees the lead appear in the spreadsheet in real-time
