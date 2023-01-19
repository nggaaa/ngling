# NGL BUT NOT REALY

Berikut caranya membuat ngl sendiri

1.  Buka [Google Spreadsheet](https://docs.google.com/spreadsheets/) di laptop
2.  Buat tabel seperti di bawah
    | timestamp|pesan|
    |---|---|
3.  Lalu ke `Extension`. Klik opsi `Apps Script`.
4.  Kamu diarahkan ke tab baru ke script.google.com
5.  Copy kode di bawah. lalu pastekan ke Apps Script

```js
var sheetName = "Sheet1";
var scriptProp = PropertiesService.getScriptProperties();
function intialSetup() {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  scriptProp.setProperty("key", activeSpreadsheet.getId());
}
function doPost(e) {
  var lock = LockService.getScriptLock();
  lock.tryLock(10000);
  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty("key"));
    var sheet = doc.getSheetByName(sheetName);
    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
    var nextRow = sheet.getLastRow() + 1;
    var newRow = headers.map(function (header) {
      return header === "timestamp" ? new Date() : e.parameter[header];
    });
    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow]);
    return ContentService.createTextOutput(
      JSON.stringify({ result: "success", row: nextRow })
    ).setMimeType(ContentService.MimeType.JSON);
  } catch (e) {
    return ContentService.createTextOutput(
      JSON.stringify({ result: "error", error: e })
    ).setMimeType(ContentService.MimeType.JSON);
  } finally {
    lock.releaseLock();
  }
}
```

6. Lalu klik tombol save
7. Klik Sidebar, masuk menu Triggers
8. Tambahkan trigger dengan klik tombol `+`
9. Maka muncul form. Isi seperti di bawah ini.

   ![FORM TRIGGER NGGANGL]("./formtriggernggangl.png")

10. Klik tombol `Save`. Trigger akan dibuat.
11. Setelah itu, klok tombol `Deploy` di navbar. Akan muncul form. Pilih App Type `Web App`. Lalu klik `Deploy di form`.
12. Copy untuk Web URL nya dengan klik tombol `Copy`
