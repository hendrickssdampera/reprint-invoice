# Product Requirements Document (PRD): Shop & Drive PDF Link Generator

## 1. Project Overview
Build a lightweight, client-side web application that dynamically generates and opens direct PDF URLs for Shop & Drive documents based on branch code (`xxxx`) and document sequence number (`yyyy`).

* **Target URL Pattern:** `https://www.shopanddrive.com/dgt/[xxxx]FP2600100[yyyy].pdf`
* **Architecture:** Single-page static web application (HTML5, Tailwind CSS via CDN, Vanilla JavaScript). No backend or database required.

---

## 2. User Stories
1. **As an operational user**, I want to select a branch from a labeled dropdown (`Kode - Nama Cabang`) so I don't have to memorize branch codes.
2. **As an operational user**, I want to type a short sequence number (e.g., `5` or `42`) and have the system automatically pad it with leading zeros to 4 digits (`0005`, `0042`) so the generated link is always valid.
3. **As an operational user**, I want to see the complete URL preview in real time and either open the PDF in a new tab or copy the link with a single click.

---

## 3. Functional Requirements

### FR-1: Branch Selection (`xxxx`)
* Render a `<select>` dropdown populated from a structured configuration array in JavaScript so future branches can be added easily.
* **Initial Dataset:**
  * Value: `"0285"` | Label: `"0285 - SSD Ampera"`
  * Value: `"0590"` | Label: `"0590 - SD Pondok Labu"`
* Only the 4-digit `kode` value is injected into the `[xxxx]` segment of the URL.

### FR-2: Sequence Number Input & Auto-Padding (`yyyy`)
* Render an `<input type="text" inputmode="numeric" maxlength="4">` field.
* **Sanitization:** Strip any non-numeric characters (`/\D/g`) immediately on input.
* **Zero-Padding Logic:** When generating the URL, pad the sanitized numeric string with leading zeros up to 4 characters using `.padStart(4, "0")`.
  * Input `"1"` $\rightarrow$ Output `"0001"`
  * Input `"89"` $\rightarrow$ Output `"0089"`
  * Input `"1234"` $\rightarrow$ Output `"1234"`

### FR-3: Static Core URL Segment
* Base URL is strictly `https://www.shopanddrive.com/dgt/`
* Middle identifier is strictly static: `FP2600100`
* File extension is strictly `.pdf`

### FR-4: Real-Time URL Preview
* Update the preview box on every `change` event of the dropdown and `input` event of the text field.
* If the number input is empty:
  * Display placeholder preview: `https://www.shopanddrive.com/dgt/[xxxx]FP2600100[yyyy].pdf` (where `[xxxx]` reflects the currently selected branch code).
  * Set `Open PDF` and `Copy Link` buttons to `disabled`.
* If the number input contains at least 1 digit (`0-9`):
  * Display full valid URL.
  * Enable `Open PDF` and `Copy Link` buttons.

### FR-5: Actions & Keyboard Shortcuts
* **Open PDF Button:** Opens the generated URL in a new browser tab (`window.open(url, "_blank", "noopener,noreferrer")`).
* **Enter Key Shortcut:** Pressing `Enter` while focused on the number input triggers the Open PDF action if the input is valid.
* **Copy Link Button:** Copies the generated URL to the system clipboard via `navigator.clipboard.writeText(url)` and displays a temporary visual confirmation (`"Tersalin!"` for 1500ms).
* **Reset Button:** Clears the number input field, updates the preview back to placeholder state, disables action buttons, and sets focus back to the number input field.

---

## 4. Technical Stack & File Structure (for Antigravity Agent)
* **Single-File Output:** `index.html` containing semantic HTML, Tailwind CSS CDN script, and embedded modular JavaScript.
* **Zero External JS Dependencies:** Use standard DOM APIs only.

---

## 5. Acceptance Criteria & Test Cases
| ID | Scenario | Input (`xxxx`, `yyyy`) | Expected Output / Behavior |
| :--- | :--- | :--- | :--- |
| **TC-01** | Initial Load | Default (`0285`), Empty `yyyy` | Preview shows `...0285FP2600100[yyyy].pdf`; Open & Copy buttons disabled. |
| **TC-02** | Single Digit Input | `0285`, `7` | Preview updates to `https://www.shopanddrive.com/dgt/0285FP26001000007.pdf`; buttons enabled. |
| **TC-03** | Branch Switch | Switch to `0590`, keep `7` | Preview updates to `https://www.shopanddrive.com/dgt/0590FP26001000007.pdf`. |
| **TC-04** | Non-Numeric Input | `0590`, `12ab` | Letters `ab` are stripped immediately; preview shows `...0590FP26001000012.pdf`. |
| **TC-05** | Reset Action | Click "Reset Angka" | Input clears, focus returns to input field, buttons become disabled. |