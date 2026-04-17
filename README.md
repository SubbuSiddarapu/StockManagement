[readme.txt](https://github.com/user-attachments/files/26770018/readme.txt)

Deployment ID
AKfycbwf3b2oK5544HELNm4r8-7nDAmQ1xXtrw8UblmLXOhAnoAwoMC5kso8CLDykN5ZWA68uw

Web app
URL
https://script.google.com/macros/s/AKfycbwf3b2oK5544HELNm4r8-7nDAmQ1xXtrw8UblmLXOhAnoAwoMC5kso8CLDykN5ZWA68uw/exec

https://script.google.com/macros/s/AKfycbzDm3odQUEMqQniQ1Tx__PtRpjsuelZY8BB5VoHihff0HYBo5Gyc6LBN44EvbdcfmXE/exec

file:///G:/My%20Drive/001MYINFO/Naakistam/Restaurant/StockManagement/stock_management.html

v1
const SHEET_NAME = "stock";

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const ss    = SpreadsheetApp.getActiveSpreadsheet();
    const sheet = ss.getSheetByName(SHEET_NAME) || ss.getActiveSheet();

    if (data.action === "append") {
      data.rows.forEach(row => sheet.appendRow(row));
      return ContentService
        .createTextOutput(JSON.stringify({ status:"ok", added:data.rows.length }))
        .setMimeType(ContentService.MimeType.JSON);
    }

    return ContentService
      .createTextOutput(JSON.stringify({ status:"error", message:"Unknown action" }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch(err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status:"error", message:err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService
    .createTextOutput(JSON.stringify({ status:"ok", message:"Stock API ready" }))
    .setMimeType(ContentService.MimeType.JSON);
}

v2
const SHEET_NAME = "stock";
const LOW_STOCK_THRESHOLD = 5;

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);

    if (!sheet) return json({ status:"error", message:"Sheet not found" });

    // INSERT
    if (data.action === "append") {
      const rows = data.rows;

      if (rows.length) {
        sheet.getRange(sheet.getLastRow()+1,1,rows.length,rows[0].length).setValues(rows);
      }

      return json({ status:"ok" });
    }

    // GET DATA
    if (data.action === "getData") {
      return json({ status:"ok", data: sheet.getDataRange().getValues() });
    }

    // DASHBOARD
    if (data.action === "getDashboard") {
      const dataRows = sheet.getDataRange().getValues().slice(1);
      const balances = {};

      dataRows.forEach(r => {
        const item = r[1];
        const inQty = parseFloat(r[3]) || 0;
        const outQty = parseFloat(r[4]) || 0;

        if (!balances[item]) balances[item] = 0;
        balances[item] += inQty - outQty;
      });

      return json({ status:"ok", balances });
    }

    return json({ status:"error", message:"Unknown action" });

  } catch(err) {
    return json({ status:"error", message:err.toString() });
  }
}

function doGet() {
  return json({ status:"ok", message:"API ready" });
}

function json(obj) {
  return ContentService.createTextOutput(JSON.stringify(obj))
    .setMimeType(ContentService.MimeType.JSON);
}
