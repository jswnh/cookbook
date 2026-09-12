## 1. Table Architecture

HTML tables organize tabular data into rows and columns. Semantic table markup ensures proper rendering and screen reader accessibility.

### Table Elements Reference

| Element | Semantic Role | Description |
| :--- | :--- | :--- |
| **`<table>`** | Table Root | Container for all tabular data and structures. |
| **`<caption>`** | Table Title | Provides an accessible title and summary for the table. Must be the first child of `<table>`. |
| **`<colgroup>`** | Column Group | Groups one or more columns within a table for formatting purposes. |
| **`<col>`** | Column Specification | Defines attributes (such as `span`) for a column within a `<colgroup>`. |
| **`<thead>`** | Table Head | Encapsulates the header rows defining column titles. |
| **`<tbody>`** | Table Body | Encapsulates the main data rows of the table. Multiple `<tbody>` elements can exist. |
| **`<tfoot>`** | Table Foot | Encapsulates the summary or footer rows (e.g., totals, averages). |
| **`<tr>`** | Table Row | Defines a single row of cells. |
| **`<th>`** | Header Cell | Defines a header cell (rendered bold and centered by default). |
| **`<td>`** | Data Cell | Defines a standard data cell containing values. |

---

## 2. Table Cell Attributes

| Attribute | Elements | Values | Purpose |
| :--- | :--- | :--- | :--- |
| **`scope`** | `<th>` | `col`, `row`, `colgroup`, `rowgroup` | Explicitly associates header cells with corresponding columns or rows for assistive technology. |
| **`colspan`** | `<th>`, `<td>` | Positive integer ($\ge 1$) | Specifies the number of columns the cell spans horizontally. |
| **`rowspan`** | `<th>`, `<td>` | Positive integer ($\ge 1$) | Specifies the number of rows the cell spans vertically. |
| **`headers`** | `<td>` | Space-separated list of `<th>` IDs | Associates complex data cells with their respective header IDs. |

---

## 3. Accessible Table Example with Colspan & Rowspan

```html
<div class="table-container">
  <table>
    <caption>Quarterly Regional Sales & Performance Breakdown</caption>
    
    <!-- Column grouping for styling -->
    <colgroup>
      <col class="col-region">
      <col class="col-rep">
      <col class="col-q1">
      <col class="col-q2">
      <col class="col-total">
    </colgroup>

    <thead>
      <tr>
        <th scope="col">Region</th>
        <th scope="col">Lead Representative</th>
        <th scope="col">Q1 Revenue</th>
        <th scope="col">Q2 Revenue</th>
        <th scope="col">Subtotal</th>
      </tr>
    </thead>

    <tbody>
      <!-- Row 1 & 2 share same Region cell using rowspan -->
      <tr>
        <th scope="rowgroup" rowspan="2">North America</th>
        <td>Sarah Connor</td>
        <td>$124,000</td>
        <td>$145,000</td>
        <td>$269,000</td>
      </tr>
      <tr>
        <td>John Reese</td>
        <td>$98,000</td>
        <td>$112,000</td>
        <td>$210,000</td>
      </tr>

      <!-- Row 3: Single region row -->
      <tr>
        <th scope="row">Europe</th>
        <td>Elena Rostova</td>
        <td>$150,000</td>
        <td>$168,000</td>
        <td>$318,000</td>
      </tr>
    </tbody>

    <tfoot>
      <tr>
        <th scope="row" colspan="2">Total Revenue</th>
        <td>$372,000</td>
        <td>$425,000</td>
        <td>$797,000</td>
      </tr>
    </tfoot>
  </table>
</div>
```

> [!TIP]
> Never use `<table>` elements for general page layout. Use CSS Grid or Flexbox instead. Reserve HTML tables strictly for tabular datasets.
