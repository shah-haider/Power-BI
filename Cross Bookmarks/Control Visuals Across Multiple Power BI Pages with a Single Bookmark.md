# Control Visuals Across Multiple Power BI Pages with a Single Bookmark


**Youtube Link of Detailed Video:** https://youtu.be/Gh7OMZsN6tg & https://youtu.be/3Kixop3FkG0?si=zM0FhwQ1Fc_KfSgC

Power BI bookmarks are powerful tools for controlling report states, but they're limited to a single page. This guide shows you how to bypass that limitation using Power BI Project (PBIP) files to create bookmarks that control visuals across your entire report.

## Why This Matters

Power BI bookmarks work within the context of a single page only. If you need to show/hide visuals or apply states across multiple pages, you'd normally need to:
- Create 100 separate bookmarks for 10 pages
- Manually synchronize bookmark behaviors
- Spend considerable manual labor time on repetitive configuration

This workaround only requires you to create a single bookmark and then replicate it across your report. But remember to charge customer for 4 hours for your 10 minutes bookmarks addition task. It's just you are efficient

## Prerequisites

- Updated Power BI Desktop (version that supports PBIP adn new PBIR format)
- A text editor (VS Code, Notepad++, etc.)

## Step-by-Step Implementation

### 1. Convert Your Report as new PBIR format and PBIP Format

1. Open your Power BI Desktop file
2. In the Preview features in settings, enable **Power Bl Project (.pbip) save** and **Store PBIX reports using enhanced metadata format (PBIR) options**. In the Report settings in Settings, enable **Copy object names when right clicking on report objects** 
![Power BI Settings](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture3.png)
3. Press Ctrl+S and Upgrade your report format into Power Bl Report enhanced format (PBIR) from PBIR-Legacy format
![PBIR new format](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture2.png)
4. Go to **File** → **Save As** and hoose **Power BI Project (.pbip)** as the file type and save to your desired location
![PBIP save](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture1.png)

This creates a folder structure containing JSON files that define your report's metadata.

### 2. Locate the Bookmark JSON File

Open VSCode and Navigate to Bookmark JSON in your PBIP project folder:

![PBIP Folder in VSCode](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture4.png)

```
YourReport.Report/
  ├── definition/
      ├── bookmarks/
          └── [BookMarkObjectName].json
```
[Your PowerBI Project folder]\[ReportName].Report\definition\bookmarks\[BookMarkObjectName].bookmark.json
![Bookmarks JSONs](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture5.png)


### 3. Edit the Bookmark Configuration

Open the bookmark JSON file in your text editor and modify the following properties:

#### Enable Cross-Page Functionality
Ensure that bookmark in Power BI report is not enabled to work only on Current page, or set `suppressActiveSession` to `true` in JSON file of bookmark:
![Current Page untick](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture6.png)

```json
    "suppressActiveSection": true,
```
![suppressActiveSection](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture7.png)

#### Specify Target Visuals
Add visual object names to the `targetVisualNames` array:
```json
  "targetVisualNames": [
    "visual1ObjectName",
    "visual2ObjectName",
    "visual3ObjectName"
  ]
```
You can leave it empty if you want to apply the bookmark to All visuals of all pages:
```json
  "targetVisualNames": []
```
![targetVisualNames](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture8.png)

**Tip:** To find visual object names, copy object name by right-clicking visuals in Power BI desktop, inspect the visual's properties in the PBIP structure or use Tabular Editor.

#### Define Active Pages
List the pages where the bookmark should apply:
```json
    "activeSection": "Page1ObjectName,Page2ObjectName,Page3ObjectName",
```
![activeSection](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture9.png)

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
![sections](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture10.png)


**Tip:** In VSCode navigate JSON file structure by clicking top of the opened file (highlighted in green) to find Layout name and to copy part of code easily, collapse objects in the diagram array by clicking left button.

![Json object navigation](https://github.com/shah-haider/Power-BI/blob/main/Diagram%20Layout%20Copy%20Between%20Semantic%20Models/Images%20for%20Diagram%20Layouts/json%20struct%20navig.png)
![Json Expand/collapse](https://github.com/shah-haider/Power-BI/blob/main/Diagram%20Layout%20Copy%20Between%20Semantic%20Models/Images%20for%20Diagram%20Layouts/Expand%20collapse%20json%20objects.png)


### 4. Save and Reload

1. Save the bookmark JSON file
2. Open the `.pbip` (shortcut to pbir) file in Power BI Desktop from your Power BI project folder
3. Test your bookmark across different pages

![Final Product](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture11.png)



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
