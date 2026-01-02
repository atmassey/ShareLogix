# Bardiani IO-Link Valve – Maintenance Handoff

## Purpose

This document is intended for maintenance, controls, and troubleshooting personnel. It provides the information required to identify, verify, operate, and troubleshoot a **Bardiani IO-Link valve** connected through an **IFM IO-Link master** using a **PLC Add-On Instruction (AOI)**.

---

## System Overview

* **Device:** Bardiani IO-Link Valve
* **Network:** IO-Link
* **IO-Link Master:** IFM IO-Link Master
* **PLC Integration:** AOI-based (Rockwell Automation / Studio 5000)

Each valve is represented in the PLC by a **dedicated AOI instance**.

---

## AOI Identification

### AOI Instance Name

* **`Bardiani_IO_Link`**
* Represents **one physical valve**
* Multiple valves require multiple AOI instances

**Example instance name:**

```text
Bardiani_Test_Valve
```

---

## AOI Required Configuration Parameters

### PLC_Input

* **Description:** IFM master input process data
* **Direction:** Device → PLC
* **Used for:** Valve position, status, diagnostics

**Typical tag example:**

```text
IFM_IOLink_Master:I1.Data
```

---

### PLC_Output

* **Description:** IFM master output process data
* **Direction:** PLC → Device
* **Used for:** Valve commands

**Typical tag example:**

```text
IFM_IOLink_Master:O1.Data
```

---

### Port_Process_Data_Size

* **Value:** `4`
* **Units:** Bytes

> ⚠️ **Must always be set to 4.**
> Incorrect values will cause invalid data or AOI faults.

---

### Port_Number

* **Description:** Physical IO-Link port number on IFM master
* **Typical range:** 1–8 (depends on master model)

**Example:**

```text
Port_Number := 4
```

---

## Device Identification (Read-Only)

### Vendor_ID

* Identifies the manufacturer
* Used to verify correct device

### Device_ID

* Identifies the specific Bardiani valve model
* Used for device validation

> If either value is incorrect, the AOI will indicate a **configuration or connection error**.

---

## Process Data – Input (PDin)

### PDin Word 0 – Valve Position

| Bits | Description                       |
| ---: | --------------------------------- |
| 15–0 | Valve Position (Primary feedback) |

---

### PDin Word 2 – Valve Position (Redundant)

| Bits | Description                         |
| ---: | ----------------------------------- |
| 15–0 | Valve Position (Secondary feedback) |

---

### PDin Word 4 – Status Information

|  Bit | Signal        |
| ---: | ------------- |
|    0 | S1            |
|    1 | S2            |
|    2 | S3            |
|    3 | S4            |
|  4–7 | Valve Mode    |
| 8–15 | Device Status |

---

## Process Data – Output (PDout)

### PDout Word 0 – Control Commands

| Bit | Function             |
| --: | -------------------- |
|   0 | MS – Main Switch     |
|   1 | LL – Lower Limit     |
|   2 | UL – Upper Limit     |
|   3 | Localize Device      |
| 4–7 | Not Used (must be 0) |

---

## Valve Command Descriptions

### MS (Main Switch)

Enables or disables valve operation.

### LL (Lower Limit)

Commands the valve to its lower mechanical limit.

### UL (Upper Limit)

Commands the valve to its upper mechanical limit.

### Localize_Device

Activates the valve identification feature (used to locate the device in the field).

### Valve_Mode

Indicates or selects the current operating mode of the valve.

---

## Diagnostics and Status Indicators

The AOI provides decoded diagnostic bits for maintenance and troubleshooting:

* `Device_OK`
* `Device_Not_Connected`
* `Invalid_Data`
* `Wrong_VID_DID`
* `Diagnostic_Avail`
* `AOI_Settings_Error`
* `Maintenance_required`
* `Out_Of_Specification`
* `Functional_check`
* `Failure`
* `Switch1` – `Switch4`

These tags should be monitored by:

* PLC logic
* HMI displays
* Alarm systems

---

## Normal Operation Indicators

A properly operating valve will show:

* `Device_OK = TRUE`
* No active fault or failure bits
* Valid position feedback

---

## Common Fault Conditions and Checks

### Device Not Connected

* Check IO-Link cable
* Verify correct port number
* Confirm IO-Link mode on IFM master

### Invalid Data

* Verify `Port_Process_Data_Size = 4`
* Confirm PLC input/output tags

### Wrong Vendor / Device ID

* Confirm correct valve model is installed
* Check wiring and device replacement history

### Maintenance Required

* Valve may still operate
* Schedule inspection or service

---

## Commissioning Checklist

* AOI imported and instantiated
* PLC_Input mapped correctly
* PLC_Output mapped correctly
* Port_Process_Data_Size set to 4
* Port_Number matches physical connection
* Vendor_ID and Device_ID valid
* Device_OK active

---

## Maintenance Notes

* Do not manually bit-map IO-Link data
* All decoding and scaling handled by AOI
* Replace valves with identical model to avoid ID mismatch
* Document port number and AOI instance name when replacing hardware

---

## Revision Information

* **Document Type:** Maintenance Handoff
* **Device:** Bardiani IO-Link Valve
* **Integration:** IFM IO-Link Master with AOI

---

If you want, I can also:

* Split this into **README.md + reference sections**
* Convert it into **GitHub-flavored Markdown**
* Generate a **Mermaid diagram** for the data flow
* Normalize tag names for **Studio 5000 documentation**

