# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This project is to help KBR employees input their timesheet in the MSD (Microsoft Dynamix) timesheet system. This is done by providing a simple html based interface that will export a .csv file that can be imported into the MSD system. The project should focus on user accessibility and speed, allowing the user to easily and clearly record timesheets and export a .csv file

## HTML

The project should use the guidance provided by Simon Willson in the article [Useful patterns for building HTML tools](https://simonwillison.net/2025/Dec/10/html-tools/#host-them-somewhere-else). In particular, this includes keeping it as a single html file, avoiding react (or anything with a build step), loading any dependencies from a CDN, and keeping the file small.

## Features

- The user should be greeted with a table in which they can input their worked hours for the week. The user should be able move back and forward through different weeks (starting on Monday).
- The user should be able to assign hours to each day, with the smallest increment in 0.5 hours.
- Each row should columns 'project', 'project task', 'cbs', 'role', 'type', then the columns from monday to sunday where hours can be input.
- Cost Type and Work Location are global settings (same for all rows) and can be configured in the header area.
- Some of the row columns, e.g. 'role', 'type', should be able to be restricted to specific values.
- For a particular week, the user should be able to export a .csv file with the hours. An example of the output .csv format is provided in docs/TimesheetUpload2025-12-11.csv. Note that hours for each day need to be provided on a per row basis, so there will be a bit of data duplication.
- The page should use localStorage, so the user's data is saved. The user should also be able to export and import this data as a backup.
- Template system to save and reuse common row configurations
- Full keyboard navigation with arrow keys and shortcuts
- Copy/paste support for efficient data entry
- Validation rules to ensure data quality before export

## Implementation

The application is implemented as a single HTML file (`index.html`) with embedded CSS and JavaScript. All data is stored in the browser's localStorage.

### Main File

- `index.html` - Complete application (HTML + CSS + JavaScript)

### Reference Files

- `docs/TimesheetUpload2025-12-11.csv` - Example CSV export format for Microsoft Dynamics

## Configuration

Configuration values are defined in the `CONFIG` object at the top of the JavaScript section in `index.html`.

### Dropdown Options

To add or modify dropdown values for fields like Role, Type, and CBS:

```javascript
const CONFIG = {
    dropdownOptions: {
        role: ["Team Member", "Senior Engineer", "Project Manager"],
        type: ["Work", "Leave", "Training"],
        cbs: [
            "A030700-Design Documents & Drawings",
            "A020200-Project Management"
        ]
    },
    // ...
};
```

### Global Settings (Cost Type & Work Location)

Cost Type and Work Location are global settings that apply to all rows. They are configured via modals accessible from the header area:

```javascript
const CONFIG = {
    costTypes: {
        structural: { name: "Structural", code: "L0700-Structural" },
        civil: { name: "Civil", code: "L0700-Civil" }
    },
    workLocations: {
        brisbane: { name: "Brisbane", code: "03600100000" },
        sydney: { name: "Sydney", code: "SYDNEY_CODE" }
    },
    // ...
};
```

### CSV Fixed Values

These values are included in every CSV export but not shown in the UI:

```javascript
csvDefaults: {
    timeSource: "Project Service",
    entryStatus: "Draft"
}
```

### Validation Rules

To add custom validation rules, add entries to the `validationRules` array:

```javascript
validationRules: [
    {
        id: "uniqueRuleId",
        level: "error",  // or "warning"
        message: "Description of the validation error",
        test: (row) => {
            // Return true if validation passes, false if it fails
            if (row.project?.toUpperCase().includes("RESOURCE POOL")) {
                return row.description?.trim().length > 0;
            }
            return true;
        }
    }
]
```

**Available fields in row object:**
- `row.project`, `row.projectTask`, `row.cbs`
- `row.role`, `row.type`, `row.description`
- `row.hours.mon`, `row.hours.tue`, `row.hours.wed`, `row.hours.thu`, `row.hours.fri`, `row.hours.sat`, `row.hours.sun`

Note: Cost Type and Work Location are global settings (not per-row), accessible via `app.getCostType()` and `app.getWorkLocationCode()`.

## Keyboard Shortcuts

- **Ctrl+Enter**: Add new row
- **Ctrl+Delete**: Delete current row (when focused on a cell in that row)
- **Ctrl+D**: Duplicate current row (copies all fields except hours, also available via Duplicate button)
- **Arrow Keys**: Navigate between cells (like Excel)
  - For hours inputs: Arrow keys always navigate to adjacent cells
  - For text inputs: Left/Right only navigate when cursor is at start/end of value
- **Tab**: Move to next field
- **Shift+Tab**: Move to previous field
- **Mouse Wheel**: When a hours input is focused and mouse is hovering over it, scroll up/down to increase/decrease value by 0.5 hours (capped at 0-12 hours)

## Duplicating Rows

Each row has a "Duplicate" button next to the Delete button that creates a copy of the row with all field values preserved but hours reset to 0. This is useful for quickly creating similar timesheet entries.

- Click the "Duplicate" button on any row, OR
- Use **Ctrl+D** keyboard shortcut when focused on a cell in that row

## Column Sorting

Click any column header to sort the timesheet table by that column.

**Sorting Behavior:**
- **First click**: Sort ascending (A→Z for text, 0→9 for numbers)
  - Indicator changes to **▲** (blue up arrow)
- **Second click**: Sort descending (Z→A for text, 9→0 for numbers)
  - Indicator changes to **▼** (blue down arrow)
- Click the **Clear Sort ✕** button (appears in toolbar when sorting is active) to restore the original insertion order
  - All indicators return to **▾** (gray down triangle)

**Sortable Columns:**
All data columns can be sorted:
- **Text fields**: Project, Project Task, Description
- **Dropdown fields**: Cost Type, CBS, Role, Type, Work Location
- **Calculated fields**:
  - Category (sorts by abbreviation: BD, O, P, T)
  - Total (sorts by row sum)
- **Hour columns**: Monday through Sunday

**Notes:**
- Empty values always appear at the end of sorted lists
- Text sorting is case-insensitive
- Category and Total are calculated fields - changes to Project or hours do not automatically re-sort
- Click the column header again to update the sort order after making changes
- Adding or deleting rows during an active sort maintains the sort order
- Each week remembers its own sort preference
- Sort state is automatically saved to localStorage

## Copy/Paste

The application supports copying and pasting data:
- Copy cells from Excel/Google Sheets and paste into the timesheet table
- Paste multi-cell selections (tab-separated values)
- Paste across multiple rows - new rows are automatically created as needed
- Copy/paste works within the same week or between different weeks

## Copying Rows from Other Weeks

To copy rows from a previous or future week:

1. Click "Copy from Other Week" button in toolbar
2. Select the week from the dropdown
3. Review the list of rows from that week (shows project, description, and total hours)
4. Check the boxes next to rows you want to copy
5. Optionally check "Include hours" to also copy the hour values (default: hours reset to 0)
6. Click "Copy Selected Rows"
7. Rows are added to the end of the current week's table

**Features:**
- Select multiple rows at once using checkboxes
- Use "Select All" button to quickly select all rows from the week
- Copied rows get new unique IDs
- By default, hours are reset to 0 (similar to duplicate)
- Enable "Include hours" checkbox to copy exact hours from the source week
- All weeks with data are available in descending order (newest first)
- Current week is marked in the dropdown

## Expected Weekly Hours

You can set your expected weekly hours in the header:

1. Click the ⚙️ (settings/cog) icon next to "Expected hours per week: 38 hours"
2. Enter your expected hours in the modal dialog (e.g., 38, 40, 37.5)
3. Click "Save" to save or "Cancel" to dismiss
4. Setting is saved globally and persists across sessions

This setting allows you to track your target hours for the week. The default is 38 hours.

## Quick Week Navigation

Navigate between weeks using these options:

**Previous/Next Week Buttons**
- Click "← Previous Week" or "Next Week →" to move one week at a time

**Calendar Picker (Click Week Display)**
- Click on "Week of Dec 22 - Dec 28, 2025" text
- Select any date from the calendar
- Automatically jumps to the week containing that date
- **Note:** The app always displays complete weeks (Monday-Sunday). When you select a date, it navigates to the Monday of that week.

**Go to Current Week**
- Click "📍 This Week" button to quickly return to the current week
- Useful after navigating to past or future weeks

## Category Classification and Weekly Summary

The timesheet automatically classifies each row into categories based on the Project field:

**Categories:**
- **Business Development (BD)**: Projects ending with "BP"
- **Overheads (O)**: Projects containing "RESOURCE POOL"
- **Time Off (T)**: Projects containing "TIME OFF"
- **Productive (P)**: All other projects

**Category Column:**
- Displays abbreviated category (BD, O, T, P) in table
- Hover over abbreviation to see full category name
- Automatically updated when Project field changes
- Non-editable (calculated field)

**Weekly Summary:**
- Displayed below the timesheet table
- Shows total hours for each category
- Shows percentage of expected weekly hours
- Example: "15.0 hrs" and "39.5%" if expected hours is 38
- Responsive layout: 4 columns (desktop), 2 columns (tablet), 1 column (mobile)

**Classification Rules:**
- Categories are determined by pattern matching on Project field
- Case-insensitive matching for "RESOURCE POOL" and "TIME OFF"
- Exact suffix match for "BP" (Business Development)
- Empty or unknown projects default to Productive

## Data Storage

Data is automatically saved to browser localStorage. The data structure:

```javascript
{
  version: 1,
  preferences: {
    lastWeekStart: "2024-12-23",  // ISO date, Monday
    timeIncrement: 0.5,
    expectedWeeklyHours: 38  // Target hours per week
  },
  dropdownOptions: { ... },
  templates: [ ... ],
  weeks: {
    "2024-12-23": {
      weekStart: "2024-12-23",
      rows: [ ... ]
    }
  }
}
```

**Backup**: Use "Export Backup (JSON)" to save all data to a file. Use "Import Backup (JSON)" to restore.

## CSV Export Format

- **Duration**: Stored in minutes (hours × 60)
- **Date**: DD/MM/YYYY format
- **Fixed columns**: Time Source, Entry Status are always "Project Service" and "Draft"
- **Empty columns**: First 3 columns are always empty as required by MSD
- **Description**: Duplicated to both "Description" and "External Comments" columns
- **Row expansion**: Each timesheet row generates one CSV row per day that has hours > 0

Example: If a row has hours on Monday (2h) and Wednesday (4h), it creates 2 CSV rows with different dates and durations (120 and 240 minutes).

## Troubleshooting

### localStorage Full

If you see "localStorage may be full" error:
1. Export your data using "Export Backup (JSON)"
2. Clear old weeks you no longer need
3. Browser localStorage limit is typically 5-10 MB

### Data Not Saving

- Check browser console for errors (F12)
- Verify localStorage is enabled in browser settings
- Try exporting data as backup
- Private/Incognito mode may have restricted localStorage

### CSV Import Fails in MSD

- Verify all required fields are filled (especially for rows with hours)
- Check validation panel for errors
- Ensure date format is DD/MM/YYYY
- Confirm CSV has all 16 columns

### Browser Compatibility

Tested and supported:
- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)

Requires: Modern browser with ES6 support and localStorage

## Development Notes

### Adding New Fields

To add a new field to the timesheet:

1. Add to data model in `createEmptyRow()` function
2. Add column header in HTML `<thead>`
3. Add input/select in `renderTable()` function
4. Handle in `handleInputChange()` if needed
5. Include in CSV export in `exportCSV()` function

### Modifying Week Start Day

Currently weeks start on Monday. To change:
- Modify `getCurrentWeekStart()` function
- Adjust the `diff` calculation for different start day

### Performance

- Debounced auto-save (500ms delay) prevents excessive localStorage writes
- Table rendering is optimized for reasonable number of rows (<100 per week)
- For very large datasets, consider archiving old weeks


