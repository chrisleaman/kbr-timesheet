# KBR Timesheet

A simple, single-file HTML application for KBR employees to input weekly timesheets and export CSV files for Microsoft Dynamics (MSD) import.

## Quick Start

1. Open `timesheet.html` in your web browser
2. Enter your hours for the current week
3. Click "Export CSV" to download a file for MSD import

That's it! Your data is automatically saved in your browser.

## Features

- **Week-based timesheet** - Navigate between weeks starting from Monday
- **0.5 hour increments** - Enter hours in half-hour intervals
- **Auto-save** - Data automatically saved to browser localStorage
- **CSV Export** - Generate CSV files matching MSD format
- **Backup/Restore** - Export and import your data as JSON
- **Row Templates** - Save and reuse common project configurations
- **Keyboard Navigation** - Arrow keys and shortcuts for fast data entry
- **Copy/Paste** - Copy data from Excel/Google Sheets
- **Validation** - Ensures data quality before export
- **Print Support** - Clean printing for record keeping

## Usage

### Basic Data Entry

1. Fill in project details in each row
2. Enter hours for each day (Monday-Sunday)
3. Add more rows as needed using the "+ Add Row" button
4. Daily and weekly totals are calculated automatically

### Exporting Timesheet

1. Ensure all validation errors are resolved (check the validation panel)
2. Click "Export CSV"
3. Upload the downloaded CSV file to Microsoft Dynamics

### Keyboard Shortcuts

- **Ctrl+Enter** - Add new row
- **Ctrl+Delete** - Delete current row
- **Ctrl+D** - Duplicate current row (without hours)
- **Arrow Keys** - Navigate between cells
- **Tab/Shift+Tab** - Move between fields

### Templates

1. Fill in a row with project details
2. Click "Templates" button
3. Click "+ Save Current Row as Template"
4. Select the row and give it a name
5. Later, use "Templates" to quickly insert saved rows

### Backup Your Data

**Important**: Your data is stored only in your browser. Regular backups are recommended.

1. Click "Export Backup (JSON)"
2. Save the file to a safe location
3. To restore, click "Import Backup (JSON)" and select your backup file

## Configuration

See [CLAUDE.md](CLAUDE.md) for detailed configuration instructions, including:
- Modifying dropdown values
- Adding custom validation rules
- Understanding the data structure
- Troubleshooting tips

## Technical Details

- **Single file** - No installation or build process required
- **No server required** - Runs entirely in the browser
- **No external dependencies** - All code is self-contained
- **Browser storage** - Uses localStorage (5-10MB limit)
- **Modern browsers** - Requires Chrome, Firefox, Safari, or Edge (latest versions)

## File Structure

```
kbr-timesheet/
├── timesheet.html              # Main application file
├── docs/
│   └── TimesheetUpload2025-12-11.csv  # Example CSV format
├── CLAUDE.md                   # Technical documentation
└── README.md                   # This file
```

## CSV Export Format

The exported CSV file matches the Microsoft Dynamics timesheet upload format:

- Each day with hours creates a separate row
- Hours are converted to minutes
- Dates use DD/MM/YYYY format
- Required MSD columns are included automatically

Example: If you enter 2 hours on Monday and 4 hours on Wednesday for a project, the CSV will contain 2 rows - one for Monday (120 minutes) and one for Wednesday (240 minutes).

## Browser Compatibility

Tested on:
- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)

Requires a modern browser with:
- ES6 JavaScript support
- localStorage support
- Modern CSS support

## Data Privacy

- All data is stored locally in your browser
- No data is sent to any server
- No analytics or tracking
- Use "Export Backup" regularly to keep copies of your data

## Troubleshooting

### Data not saving?
- Check that you're not in Private/Incognito mode
- Verify localStorage is enabled in browser settings
- Check browser console (F12) for errors

### localStorage full?
- Export your data as backup
- Delete old weeks you no longer need
- Browser limit is typically 5-10 MB

### CSV import fails in MSD?
- Check the validation panel for errors
- Ensure all required fields are filled
- Verify the date format is correct

For more help, see [CLAUDE.md](CLAUDE.md#troubleshooting)

## License

This project is private and intended for KBR internal use.

## Support

For technical assistance or questions about configuration, see the detailed documentation in [CLAUDE.md](CLAUDE.md).
