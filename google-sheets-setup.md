# Google Sheets Waitlist Setup

Follow these steps to connect the waitlist form to Google Sheets.

## Step 1: Create a Google Sheet

1. Go to [Google Sheets](https://sheets.google.com) and create a new spreadsheet
2. Name it "SmartRouter Waitlist"
3. Add headers in Row 1:
   - A1: `Email`
   - B1: `Timestamp`
   - C1: `Source`

## Step 2: Create the Apps Script

1. In your Google Sheet, go to **Extensions > Apps Script**
2. Delete any existing code and paste this:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const data = JSON.parse(e.postData.contents);
    
    sheet.appendRow([
      data.email,
      data.timestamp || new Date().toISOString(),
      data.source || 'website'
    ]);
    
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService
    .createTextOutput('SmartRouter Waitlist API is running')
    .setMimeType(ContentService.MimeType.TEXT);
}
```

3. Save the project (Ctrl/Cmd + S)

## Step 3: Deploy as Web App

1. Click **Deploy > New deployment**
2. Click the gear icon next to "Select type" and choose **Web app**
3. Configure:
   - Description: "SmartRouter Waitlist"
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Click **Deploy**
5. Authorize the app when prompted
6. Copy the **Web app URL** (looks like `https://script.google.com/macros/s/ABC123.../exec`)

## Step 4: Update the Website

1. Open `index.html`
2. Find this line:
   ```javascript
   const GOOGLE_SHEETS_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
   ```
3. Replace `YOUR_SCRIPT_ID` with your actual script ID from the URL you copied

## Testing

1. Open the website in a browser
2. Enter a test email and submit
3. Check your Google Sheet - a new row should appear!

## Troubleshooting

- **No data appearing**: Make sure the Web App is deployed with "Anyone" access
- **CORS errors**: The form uses `mode: 'no-cors'` which should work, but the response won't be readable
- **Script errors**: Check the Apps Script execution log (View > Executions)

## Local Backup

The website also stores submissions in `localStorage` as a backup. You can view them in browser DevTools:

```javascript
JSON.parse(localStorage.getItem('smartrouter_waitlist'))
```

