# AeU-SPEED AI Training Landing Page

A single-page, self-contained customer-facing landing page for **AeU-SPEED** (School of Professional and Executive Education Development, Asia e University) promoting HRD Corp claimable corporate AI training courses in Malaysia.

## Files
- `index.html` — Self-contained landing page (inline CSS, inline JS, animated inline SVG hero artwork, Google Fonts CDN).

---

## Deploying to GitHub Pages

1. **Create or open your GitHub repository** (e.g., `aeu-speed-ai-training`) and push `index.html` and `README.md` to the `main` branch.
2. On GitHub, go to **Settings → Pages**.
3. Under **Build and deployment → Source**, select **Deploy from a branch**.
4. Choose **Branch: `main`** and **Folder: `/ (root)`**, then click **Save**.
5. Wait 1–2 minutes for GitHub Actions to build, then visit the published URL shown at the top of the **Pages** settings page (e.g. `https://<username>.github.io/aeu-speed-ai-training/`).

---

## Connecting the Lead Form to Google Sheets (Apps Script)

By default, `FORM_ENDPOINT` in `index.html` is set to `"PASTE_APPS_SCRIPT_WEB_APP_URL_HERE"`. Until replaced, submitting the form generates a downloadable CSV and a pre-filled `mailto:speed@aeu.edu.my` draft.

To capture leads directly into a Google Sheet:

1. **Create a Google Sheet** (e.g., *AeU-SPEED AI Training Leads*).
2. Open **Extensions → Apps Script**, delete any starter code, and paste the `doPost(e)` handler (also included in the HTML comment at the bottom of `index.html`):

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const data = JSON.parse(e.postData.contents);

    if (sheet.getLastRow() === 0) {
      sheet.appendRow([
        "Timestamp",
        "Full Name",
        "Company",
        "Work Email",
        "Phone / WhatsApp",
        "Course of Interest",
        "Participants",
        "Preferred Month",
        "Message"
      ]);
    }

    sheet.appendRow([
      data.submittedAt || new Date().toISOString(),
      data.fullName || "",
      data.company || "",
      data.workEmail || "",
      data.phone || "",
      data.course || "",
      data.participants || "",
      data.preferredMonth || "",
      data.message || ""
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: "ok" }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: "error", message: err.message }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

3. Click **Deploy → New deployment → Select type: Web app**.
4. Set **Execute as** to **Me** and **Who has access** to **Anyone**, then click **Deploy** and authorise permissions when prompted.
5. Copy the generated **Web app URL** and paste it into `const FORM_ENDPOINT = "..."` near the top of the `<script>` tag in `index.html`, then commit and push the update.
