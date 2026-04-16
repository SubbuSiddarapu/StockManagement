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
