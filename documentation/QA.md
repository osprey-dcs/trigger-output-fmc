# Trigger Output FMC Quality Assurance Procedure
## Purpose
The purpose of this document is to have a consistent unified way each Board is tested before deployment. This procedure will be updated periodically to improve QA of the boards.

## Equipment needed
* 2 x MMCX to SMA Cables
* 2 x SMA to BNC adapters
* 1 x SMA to SMA Cable
* Multimeter (Fluke 115 or better)
* Triple output Benchtop Power Supply
* 2 x FMC Power breakout module [link](https://www.iamelectronic.com/shop/produkt/fpga-mezzanine-card-fmc-power-module/)
* Marble Testing Chassis
* PC to run IOC and Phoebus

***
***
<form onsubmit="return false">
## Procedure

### QA Metadata
* **Board Serial Number:** <input type="text" placeholder="e.g., SN-1042">
* **PCB Revision:** <input type="text" placeholder="e.g., Rev B">

### Visual Inspection
Check visual for any board defects. If the board has come back from rework look at the reworked areas carefully under magnification for any shorts etc.

Areas to check with extra scrutiny are around the clock jitter cleaner components.

- [<input type="checkbox">] **Visual Inspection Complete**
  *Notes / Defects:* <input type="text" style="width: 80%;">

***

### Power Testing
Next the Board will be powered up in various ways to protect other testing hardware.

### Looking for Shorts to GND
Using the multimeter check that all voltage test points
have a resistance to board ground greater than 1kΩ.

| Silkscreened Label | Nominal Voltage | Measured Resistance (Ω) | Status |
| ---------- | --------- | ------------- | ------------ |
| VDD3P3V    | +3.3V     | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| VADJ       | +2.5V     | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| 12P0V      | +12V      | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| TP5        | +1.8V     | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| TP6        | +3.3V     | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| TP7        | +3.3V     | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| TP8        | +5.45V    | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |
| TP9        | +1.8V     | <input type="text">   | [<input type="checkbox">] Pass (>1kΩ) |

  - [<input type="checkbox">] **Resistance Check Complete**

***

### 12V/3.3V/2.5V Power Testing
One at a time inject 12V, 3.3V & 2.5V using the voltage test points found on the board. Do this with a 1.5A current limit.

Note variance from nominal current if found.

| Rail | Nominal Expected | Measured Current | Notes / Variance |
| ---- | ---------------- | ---------------- | ---------------- |
| 12V  | ~130mA           | <input type="text"> | <input type="text"> |
| 3.3V | ~70mA            | <input type="text"> | <input type="text"> |
| 2.5V | ~4mA             | <input type="text"> | <input type="text"> |

- [<input type="checkbox">] **Individual Power Test Complete**

***

### FMC breakout Power Testing
Using a 3 output power supply and two FMC Power breakout modules: Power up all input power rails (12V/3.3V/2.5V) simultaneously.

Note variance from nominal current if found.

| Rail | Nominal Expected | Measured Current | Notes / Variance |
| ---- | ---------------- | ---------------- | ---------------- |
| 12V  | ~130mA           | <input type="text"> | <input type="text"> |
| 3.3V | ~70mA            | <input type="text"> | <input type="text"> |
| 2.5V | ~4mA             | <input type="text"> | <input type="text"> |

- [<input type="checkbox">] **FMC Breakout Power Test Complete**

***

### Power up with Marble
Attach a now power tested quartz to a **powered off** marble testing chassis.

- [<input type="checkbox">] Power up the testing chassis.
- [<input type="checkbox">] Start the IOC.
- [<input type="checkbox">] Ensure proper readback of diagnostic signals (voltage, etc.).
<br>
- [<input type="checkbox">] **Power Up with Marble Complete**

***

### EEPROM Programmed

Checkout the branch/revision appropriate to the PCB model to be programmed,
and edit `createEEPROMs.sh` with the appropriate serial numbers.

* **Git Revision / Commit Used:** <input type="text">
- [<input type="checkbox">] **EEPROM Programmed Complete**

***

### Signal Check - Clock Routing

* [<input type="checkbox">] Ensure the chassis under test has an EVR input at a nominal 125MHz provided by another chassis
* [<input type="checkbox">] Route received 125MHz through to the first jitter cleaner (U18) using FMC_LA_1 pin pair
    * [<input type="checkbox">] Ensure the System can now lock to FMC_GBTCLK0
    * [<input type="checkbox">] Using the FPGA console run 'fmon' to see the clock also on FMC_LA_0 signal pair
* [<input type="checkbox">] Route received 125MHz through to the first jitter cleaner (U18) using FMC_LA_18
    * [<input type="checkbox">] Ensure the System can now lock to FMC_GBTCLK1
    * [<input type="checkbox">] Using the FPGA console run 'fmon' to see the clock also on FMC_LA_17 signal pair
* [<input type="checkbox">] Ensure that each scenario locks

- [<input type="checkbox">] **Clock Routing Signal Check Complete**

***

### Signal Check - Digital Outputs
- [<input type="checkbox">] For each Digital Output set up and send an event stream turning all outputs on and off at 1Hz.
- [<input type="checkbox">] Force 0/1
- [<input type="checkbox">] Look at each of the 16 outputs to ensure compliance.

**Channels Checked:**

|  |  |
| :--- | :--- |
| Ch 0 [<input type="checkbox">] Pass | Ch 8 [<input type="checkbox">] Pass |
| Ch 1 [<input type="checkbox">] Pass | Ch 9 [<input type="checkbox">] Pass |
| Ch 2 [<input type="checkbox">] Pass | Ch 10 [<input type="checkbox">] Pass |
| Ch 3 [<input type="checkbox">] Pass | Ch 11 [<input type="checkbox">] Pass |
| Ch 4 [<input type="checkbox">] Pass | Ch 12 [<input type="checkbox">] Pass |
| Ch 5 [<input type="checkbox">] Pass | Ch 13 [<input type="checkbox">] Pass |
| Ch 6 [<input type="checkbox">] Pass | Ch 14 [<input type="checkbox">] Pass |
| Ch 7 [<input type="checkbox">] Pass | Ch 15 [<input type="checkbox">] Pass |

- [<input type="checkbox">] **Digital Outputs Signal Check Complete**

***

## Start/Completion Validation

* **Tested Performed By:** <input type="text" placeholder="Inspector Name">
* **Date Initiated:** <input type="date">
* **Date Completed:** <input type="date">

### Final Result
Indicate a Pass/Fail if this performed with no deviations or waivers

* [<input type="radio" name="result" value="PASS">] **PASS** / [<input type="radio" name="result" value="FAIL">] **FAIL**

* **Deviations / Waivers / Comments:**

    <textarea rows="3" cols="60" placeholder="Notes..."></textarea>

* Record the git commit of **this QA procedure** in the Testing Log:

    <input type="text" placeholder="Git Commit...">

<div class="no-print" style="display: flex; flex-wrap: wrap; gap: 12px 10px; align-items: center; margin-top: 15px;">
<hr>
<button type="button" onclick="copyForSpreadsheet()" style="padding: 10px 20px; font-weight: bold; cursor: pointer; background-color: #0366d6; color: white; border: none; border-radius: 4px;">
  📋 Copy Row for Spreadsheet
</button>
<button type="button" onclick="window.print()" style="padding: 10px 20px; font-weight: bold; cursor: pointer; background-color: #0366d6; color: white; border: none; border-radius: 4px;">
  📄 Print / Save as PDF
</button>
<button type="reset" style="padding: 10px 20px; font-weight: bold; cursor: pointer; background-color: #0366d6; color: white; border: none; border-radius: 4px;">
  🗑️ Clear All Fields
</button>
<span id="copy-status" style="margin-left: 10px; color: #28a745; font-weight: bold;"></span>
</div>
</form>

<script>
function copyForSpreadsheet() {
  const data = [];

  function cleanCell(val) {
    if (val === null || val === undefined) return '';
    return String(val).replace(/[\r\n\t]+/g, ' ').trim();
  }

  // 1. Gather all form controls in DOM order
  const controls = document.querySelectorAll('input, textarea');

  controls.forEach(control => {
    // Skip radio buttons that aren't checked
    if (control.type === 'radio' && !control.checked) return;

    // Capture checkbox state
    if (control.type === 'checkbox') {
      data.push(control.checked ? 'PASS' : 'UNCHECKED');
      return;
    }

    // Capture text, date, textarea, and selected radio value
    data.push(cleanCell(control.value));
  });

  const tsvRow = data.join('\t');

  // 2. Robust Copy Mechanism
  if (navigator.clipboard && window.isSecureContext) {
    navigator.clipboard.writeText(tsvRow).then(showSuccess).catch(fallbackCopy);
  } else {
    fallbackCopy();
  }

  // Fallback clipboard copy mechanism for browser environments where the modern Async Clipboard API (navigator.clipboard.writeText) is unavailable or blocked—such as local file:// protocols, older browsers, or unsecure contexts (http://)
  function fallbackCopy() {
    const tempTextArea = document.createElement('textarea');
    tempTextArea.value = tsvRow;
    tempTextArea.style.position = 'fixed';
    tempTextArea.style.left = '-9999px';
    document.body.appendChild(tempTextArea);
    tempTextArea.select();
    try {
      document.execCommand('copy');
      showSuccess();
    } catch (err) {
      alert('Failed to copy: ' + err);
    }
    document.body.removeChild(tempTextArea);
  }

  function showSuccess() {
    const status = document.getElementById('copy-status');
    status.textContent = 'Copied to clipboard!';
    setTimeout(() => { status.textContent = ''; }, 3000);
  }
}
</script>

<style>
@media print {
  /* Hide procedural text, equipment, and instruction steps */
  .no-print, nav, header, footer {
    display: none !important;
  }

  /* Compact spacing for the printed summary page */
  body {
    font-size: 11pt;
    line-height: 1.2;
    color: #000;
  }

  /* Make inputs look like clean text instead of form fields */
  input[type="text"], input[type="date"], textarea {
    border: none !important;
    border-bottom: 1px solid #000 !important;
    background: transparent !important;
  }

  /* Ensure tables print cleanly without breaking across pages */
  table {
    page-break-inside: avoid;
    width: 100%;
    border-collapse: collapse;
  }
}
</style>
