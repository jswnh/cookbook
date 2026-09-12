## 1. Form Element (`<form>`)

The `<form>` element defines an interactive section containing controls for collecting and submitting user data to a server or script.

### Form Attributes Reference

| Attribute | Accepted Values | Description |
| :--- | :--- | :--- |
| **`action`** | URL or endpoint path | The destination URL where submitted form data is sent. |
| **`method`** | `get`, `post`, `dialog` | HTTP method used for submission (`dialog` closes a parent `<dialog>`). |
| **`enctype`** | `application/x-www-form-urlencoded` (default), `multipart/form-data`, `text/plain` | Encoding format. Use `multipart/form-data` when uploading files. |
| **`novalidate`** | Boolean attribute | Bypasses browser native constraint validation upon submission. |
| **`target`** | `_self`, `_blank`, `_parent`, `_top` | Where to display the submission response. |
| **`autocomplete`** | `on`, `off` | Enables or disables browser auto-fill suggestions for the entire form. |

---

## 2. The `<input>` Types Reference

The `<input>` tag is a void (self-closing) element whose behavior varies significantly depending on its `type` attribute.

| Input Type | Description | Key Attributes | Mobile Keyboard / Control |
| :--- | :--- | :--- | :--- |
| **`text`** | Single-line plain text. | `maxlength`, `minlength`, `placeholder` | Default keyboard |
| **`password`** | Masked text for passwords. | `autocomplete="new-password \| current-password"` | Secure entry |
| **`email`** | Validated email address. | `multiple` | Optimized email layout (`@`, `.com`) |
| **`tel`** | Telephone number. | `pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"` | Numeric dialpad |
| **`url`** | Fully qualified URL (starts with `http://` or `https://`). | `placeholder="https://..."` | URL keyboard (`/`, `.com`) |
| **`search`** | Search query field (often includes clear 'x' icon). | `placeholder` | Search action button |
| **`number`** | Numeric values with increment/decrement steppers. | `min`, `max`, `step` | Number keypad |
| **`range`** | Numeric slider control. | `min`, `max`, `step`, `value` | Slider track |
| **`date`** | Date picker (year, month, day). | `min`, `max` (`YYYY-MM-DD`) | Date picker widget |
| **`time`** | Time picker (hours and minutes, optionally seconds). | `min`, `max`, `step` (`HH:MM`) | Clock / scroll wheel |
| **`datetime-local`** | Date and local time without timezone. | `min`, `max` (`YYYY-MM-DDTHH:MM`) | Combined picker |
| **`month`** | Year and month picker (`YYYY-MM`). | `min`, `max` | Month selector |
| **`week`** | Year and week number (`YYYY-Www`). | `min`, `max` | Week selector |
| **`color`** | Color swatch picker (returns `#RRGGBB` hex). | `value="#000000"` | OS Color picker dialog |
| **`checkbox`** | Toggle on/off box (multiple allowed). | `checked`, `value` | Checkbox toggle |
| **`radio`** | Mutually exclusive radio group (same `name`). | `checked`, `value`, `name` | Radio circle |
| **`file`** | File attachment picker. | `accept`, `multiple`, `capture` | File browser / camera |
| **`hidden`** | Hidden from UI, included in payload. | `value` | None |
| **`submit`** | Button to submit form. | `value` | Push button |
| **`reset`** | Button to reset form fields to defaults. | `value` | Push button |
| **`button`** | Generic push button with no default action. | `value` | Push button |

---

## 3. Other Form Controls

### Labels, Fieldsets & Legends

```html
<fieldset>
  <legend>Shipping Preferences</legend>

  <!-- Explicit label association via 'for' and 'id' -->
  <div>
    <input type="radio" id="ship-standard" name="shipping" value="standard" checked>
    <label for="ship-standard">Standard Shipping (3-5 days)</label>
  </div>

  <!-- Implicit label association via wrapping -->
  <div>
    <label>
      <input type="radio" name="shipping" value="express">
      Express Shipping (Next day)
    </label>
  </div>
</fieldset>
```

### `<select>`, `<optgroup>`, `<option>`

```html
<label for="country-select">Country:</label>
<select id="country-select" name="country" required>
  <option value="" disabled selected>-- Select a Country --</option>
  <optgroup label="North America">
    <option value="US">United States</option>
    <option value="CA">Canada</option>
  </optgroup>
  <optgroup label="Europe">
    <option value="GB">United Kingdom</option>
    <option value="DE">Germany</option>
  </optgroup>
</select>
```

### `<textarea>`, `<datalist>`, `<output>`, `<progress>`, `<meter>`

```html
<!-- Multi-line text area -->
<label for="comments">Feedback:</label>
<textarea id="comments" name="comments" rows="4" cols="40" maxlength="500" placeholder="Type comments here..."></textarea>

<!-- Autocomplete datalist for input recommendations -->
<label for="browser-choice">Preferred Browser:</label>
<input list="browsers" id="browser-choice" name="browser">
<datalist id="browsers">
  <option value="Google Chrome">
  <option value="Mozilla Firefox">
  <option value="Apple Safari">
  <option value="Microsoft Edge">
</datalist>

<!-- Calculated mathematical output -->
<form oninput="total.value = parseInt(qty.value) * parseInt(price.value)">
  <input type="number" id="qty" value="2" min="1"> &times; 
  <input type="number" id="price" value="25" min="1"> =
  <output name="total" for="qty price">50</output>
</form>

<!-- Progress indicator -->
<label for="file-progress">Upload Progress:</label>
<progress id="file-progress" max="100" value="70">70%</progress>

<!-- Scalar meter (gauge with optimum range) -->
<label for="disk-usage">Disk Storage:</label>
<meter id="disk-usage" min="0" max="100" low="25" high="75" optimum="10" value="45">45 GB</meter>
```

---

## 4. Modern Validation & Virtual Keyboard Attributes

### Native Constraints Validation

| Attribute | Description | Example |
| :--- | :--- | :--- |
| **`required`** | Field must have a valid value before submission. | `<input required>` |
| **`pattern`** | Regular expression that field value must match. | `pattern="[0-9]{5}"` (US Zip) |
| **`min` / `max`** | Minimum and maximum values for number/date inputs. | `min="1" max="100"` |
| **`minlength` / `maxlength`** | Minimum and maximum character length for string inputs. | `minlength="8"` |
| **`step`** | Valid step granularity for number/date controls. | `step="0.01"` (currency) |

### Virtual Keyboard & Mobile UX Attributes

| Attribute | Values | Description |
| :--- | :--- | :--- |
| **`inputmode`** | `numeric`, `decimal`, `tel`, `email`, `url`, `search`, `text`, `none` | Displays the ideal mobile keyboard layout without changing input type validation. |
| **`enterkeyhint`** | `enter`, `done`, `go`, `next`, `previous`, `search`, `send` | Customizes the label/icon of the virtual keyboard's return/action key. |
| **`autocomplete`** | `username`, `current-password`, `new-password`, `one-time-code`, `cc-number`, `postal-code` | Tells password managers and autofill services exactly which data to supply. |

#### High-Conversion Mobile Form Snippet

```html
<!-- One-Time SMS Verification Code Input -->
<label for="otp">Enter 6-Digit Code:</label>
<input 
  type="text" 
  id="otp" 
  name="otp" 
  inputmode="numeric" 
  pattern="[0-9]{6}" 
  autocomplete="one-time-code" 
  enterkeyhint="done" 
  required
>
```
