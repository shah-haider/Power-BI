# Control Visuals Across Multiple Power BI Pages with a Single Bookmark

Power BI bookmarks are powerful tools for controlling report states, but they're limited to a single page. This guide is a break through in the 10 years history of Power BI hostory, bypassing that limitations using Power BI Project (PBIP) files to create bookmarks that control visuals across your entire report.

## Why This Matters

Power BI bookmarks work within the context of a single page only. If you need to show/hide visuals or apply states across multiple pages, you'd normally need to:
- Create 100 separate bookmarks for 10 pages
- Manually synchronize bookmark behaviors
- Spend considerable manual labor time on repetitive configuration

This workaround only requires you to create a single bookmark and then replicate it across your report. But remember to charge customer for 4 hours for your 10 minutes task.

## Prerequisites

- Updated Power BI Desktop (version that supports PBIP adn new PBIR format)
- A text editor (VS Code, Notepad++, etc.)

## Step-by-Step Implementation

### 1. Convert Your Report as new PBIR format and PBIP Format

1. Open your Power BI Desktop file
2. In the Preview features in settings, enable **Power Bl Project (.pbip) save** and **Store PBIX reports using enhanced metadata format (PBIR) options**. In the Report settings in Settings, enable **Copy object names when right clicking on report objects** 
3. Press Ctrl+S and Upgrade your report format into Power Bl Report enhanced format (PBIR) from PBIR-Legacy format
4. Go to **File** → **Save As** and hoose **Power BI Project (.pbip)** as the file type and save to your desired location

This creates a folder structure containing JSON files that define your report's metadata.

### 2. Locate the Bookmark JSON File

Navigate to your PBIP project folder:
```
YourReport.Report/
  ├── definition/
      ├── bookmarks/
          └── [BookMarkObjectName].json
```
[Your PowerBI Project folder]\[ReportName].Report\definition\bookmarks\[BookMarkObjectName].bookmark.json


### 3. Edit the Bookmark Configuration

Open the bookmark JSON file in your text editor and modify the following properties:

#### Enable Cross-Page Functionality
Ensure that bookmark in Power BI report is not enabled to work only on Current page, or set `suppressActiveSession` to `true` in JSON file of bookmark:
```json
    "suppressActiveSection": true,
```

#### Specify Target Visuals
Add visual object names to the `targetVisualNames` array:
```json
  "targetVisualNames": [
    "visual1ObjectName",
    "visual2ObjectName",
    "visual3ObjectName"
  ]
```

**Tip:** To find visual object names, copy object name by right-clicking visuals in Power BI desktop, inspect the visual's properties in the PBIP structure or use Tabular Editor.

#### Define Active Pages
List the pages where the bookmark should apply:
```json
    "activeSection": "Page1ObjectName,Page2ObjectName,Page3ObjectName",
```

#### Configure Page-Specific Settings
Create separate sections for each page:
```json
"sections": {
      "Page1ObjectName": {
      },
      "Page2ObjectName": {
      },
      "Page3ObjectName": {
      }
    },
```

### 4. Save and Reload

1. Save the bookmark JSON file
2. Open the `.pbip` (shortcut to pbir) file in Power BI Desktop from your Power BI project folder
3. Test your bookmark across different pages



## End

**This technique transforms what could be hours of manual work into a 10 minutes configuration task, enabling cross-page bookmark functionality that was not possible before in the 10 years history of Power BI.**

## Troubleshooting

**Bookmark not working across pages:**
- Verify `suppressActiveSession` is set to `true`
- Check that page names match exactly (case-sensitive)

**Visuals not responding:**
- Confirm visual object names are correct
- Ensure visuals exist on the specified pages

**JSON syntax errors:**
- Validate JSON using a linter
- Check for missing commas or brackets


## Contributing

Found improvements or additional tips? Feel free to open an issue or submit a pull request!

## License

This guide is provided as-is for educational purposes.

---

**Author:** [Mr. Syed Haider Ali Shah]  
**Last Updated:** December 2025

---
