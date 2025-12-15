# Copy Model View Diagram Layouts Between Power BI Semantic Models

Stop manually dragging tables one by one to recreate your Model View layout. With Power BI Project (PBIP) files, you can copy entire diagram layouts between semantic models in seconds by working with a simple JSON file.

## Why This Matters

When migrating to a new semantic model or collaborating with colleagues, recreating your carefully organized Model View diagram layout is tedious. You typically have to:
- Drag each table individually to match the original positions
- Resize tables to fit your preferred view
- Remember which tables were grouped together
- Spend 15-30 minutes (or more) on repetitive manual work

This guide shows you how to copy layouts in under a minute using PBIP files—or even faster with a Python script.

## What You'll Learn

- How to export diagram layouts from one semantic model
- How to import layouts into another semantic model
- How to use a Python script to automate the entire process
- How to handle duplicate layout names automatically

## Prerequisites

- **Power BI Desktop** with PBIP and PBIR format support enabled
- **A text editor** (VS Code recommended, but Notepad++ works too)
- **Python** (optional, for automated copying)

## Step-by-Step Guide

### 1. Enable Required Preview Features

Before you can work with PBIP files, you need to enable the necessary features:

1. Open Power BI Desktop
2. Go to **File** → **Options and settings** → **Options**
3. Navigate to **Preview features**
4. Enable these options:
   - ✅ **Power BI Project (.pbip) save**
   - ✅ **Store PBIX reports using enhanced metadata format (PBIR)**
5. Go to **Report settings** section
6. Enable: ✅ **Copy object names when right clicking on report objects**
7. Click **OK** and restart Power BI Desktop

![Power BI Settings](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture3.png)

### 2. Convert Your Reports to PBIP Format

#### Upgrade to PBIR Format (One-time step)

1. Open your existing Power BI Desktop file
2. Press **Ctrl+S** to save
3. You'll see a prompt to upgrade to the enhanced format (PBIR)
4. Click **Upgrade**

![PBIR Format Upgrade](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture2.png)

#### Save as PBIP

1. Go to **File** → **Save As**
2. Choose **Power BI Project (.pbip)** as the file type
3. Select your desired location and save

![Save as PBIP](https://github.com/shah-haider/Power-BI/blob/main/Cross%20Bookmarks/Images%20for%20Cross-Bookmarks/Picture1.png)

**Do this for both:**
- Your **source** file (the one with the layout you want to copy)
- Your **target** file (the one where you want to paste the layout)

This creates a project folder structure with editable JSON files.

### 3. Locate the DiagramLayout JSON Files

After saving as PBIP, you'll have a folder structure like this:

```
YourReport/
├── YourReport.pbip (shortcut file)
└── YourReport.SemanticModel/
    ├── definition/
    │   └── tables/
    ├── diagramLayout.json  ← This is what you need!
    └── model.bim
```

**File path pattern:**
```
[PowerBI Project Folder]\[SemanticModelName].SemanticModel\diagramLayout.json
```

Open both the **source** and **target** `diagramLayout.json` files in VS Code (or your preferred editor).

### 4. Understanding the DiagramLayout Structure

The `diagramLayout.json` file contains all your Model View layouts. Here's what it looks like:

```json
{
  "version": "1.1.0",
  "diagrams": [
    {
      "ordinal": 0,
      "scrollPosition": { "x": 0, "y": 0 },
      "nodes": [
        {
          "location": { "x": 307, "y": 100 },
          "nodeIndex": "Product",
          "nodeLineageTag": "f6ccdb91-4c18-4fd2-bb41-3b7425ba1677",
          "size": { "height": 300, "width": 234 },
          "zIndex": 0
        }
      ],
      "name": "Sales Model",  ← Layout name
      "zoomValue": 100,
      "pinKeyFieldsToTop": false,
      "showExtraHeaderInfo": false,
      "hideKeyFieldsWhenCollapsed": false,
      "tablesLocked": false
    }
  ],
  "selectedDiagram": "Sales Model",
  "defaultDiagram": "All tables"
}
```

**Key elements:**
- `diagrams`: Array containing all your layouts
- `name`: The layout name (visible in Model View dropdown)
- `nodes`: Position and size of each table in the layout
- `selectedDiagram`: Currently active layout
- `defaultDiagram`: Layout shown by default

### 5. Manual Copy Method

#### Copy the Layout

1. Open the **source** `diagramLayout.json`
2. Find the layout you want to copy in the `diagrams` array
3. Copy the entire diagram object (from opening `{` to closing `}`)

**Example - copying "Sales Model" layout:**

```json
{
  "ordinal": 0,
  "scrollPosition": { "x": 0, "y": 0 },
  "nodes": [ /* ... table positions ... */ ],
  "name": "Sales Model",
  "zoomValue": 100,
  "pinKeyFieldsToTop": false,
  "showExtraHeaderInfo": false,
  "hideKeyFieldsWhenCollapsed": false,
  "tablesLocked": false
}
```

#### Paste into Target

1. Open the **target** `diagramLayout.json`
2. Locate the `diagrams` array
3. Paste the copied layout object inside the array
4. **Important:** Add a comma after the previous diagram object if needed
5. **Check for duplicate names:** If a layout with the same name exists, rename it

**Example - adding to target:**

```json
{
  "version": "1.1.0",
  "diagrams": [
    {
      "name": "Existing Layout"
      /* ... existing layout ... */
    },  ← Don't forget this comma!
    {
      "name": "Sales Model",  ← Your pasted layout
      /* ... copied layout ... */
    }
  ],
  "selectedDiagram": "All tables",
  "defaultDiagram": "All tables"
}
```

#### Save and Test

1. Save the target `diagramLayout.json` file
2. Open the target `.pbip` file in Power BI Desktop
3. Go to **Model View**
4. Check the layout dropdown—your copied layout should appear!
5. Select it to see your tables positioned exactly as in the source

### 6. Automated Method with Python Script

For copying multiple layouts or frequent operations, use this Python script:

#### The Script

```python
import json
import os
import shutil
import sys
import tempfile



def semantic_diagram_path(pbip_path):
    pbip_path = pbip_path.strip()
    # if user supplied diagramLayout.json directly
    if os.path.basename(pbip_path).lower() == "diagramlayout.json":
        return pbip_path
    # if user supplied the .SemanticModel directory
    if pbip_path.lower().endswith(".semanticmodel") or os.path.isdir(pbip_path) and pbip_path.lower().endswith(".semanticmodel"):
        return os.path.join(pbip_path, "diagramLayout.json")
    # otherwise treat as a PBIP file path
    base_dir = os.path.dirname(pbip_path)
    base_name = os.path.splitext(os.path.basename(pbip_path))[0]
    semantic_dir = os.path.join(base_dir, f"{base_name}.SemanticModel")
    return os.path.join(semantic_dir, "diagramLayout.json")


def load_json(path):
    with open(path, "r", encoding="utf-8") as f:
        return json.load(f)


def write_json_atomic(path, data):
    dirn = os.path.dirname(path)
    fd, tmp_path = tempfile.mkstemp(dir=dirn, suffix=".tmp")
    os.close(fd)
    with open(tmp_path, "w", encoding="utf-8") as f:
        json.dump(data, f, indent=2, ensure_ascii=False)
    if os.path.exists(path):
        shutil.copy2(path, path + ".bak")
    os.replace(tmp_path, path)


def list_layout_names(diagram_json):
    return [d.get("name", "") for d in diagram_json.get("diagrams", [])]


def find_diagram_by_name(diagrams, name):
    for d in diagrams:
        if d.get("name") == name:
            return d
    return None


def unique_name_for_destination(name, dest_names):
    # append numeric suffix like " (1)", " (2)" when name exists
    if name not in dest_names:
        return name
    i = 1
    while True:
        candidate = f"{name} ({i})"
        if candidate not in dest_names:
            return candidate
        i += 1


def main():
    src_pbip = input(f"Source PBIP path: ").strip()
    dst_pbip = input(f"Destination PBIP path: ").strip()

    src_diagram_path = semantic_diagram_path(src_pbip)
    dst_diagram_path = semantic_diagram_path(dst_pbip)

    if not os.path.exists(src_diagram_path):
        print(f"Source diagramLayout.json not found: {src_diagram_path}")
        sys.exit(1)
    if not os.path.exists(dst_diagram_path):
        print(f"Destination diagramLayout.json not found: {dst_diagram_path}")
        sys.exit(1)

    src_json = load_json(src_diagram_path)
    src_names = list_layout_names(src_json)
    if not src_names:
        print("No diagrams found in source.")
        sys.exit(1)

    print("Available layouts in source:")
    for i, n in enumerate(src_names, start=1):
        print(f"  {i}. {n}")

    choice = input("Enter diagram names or numbers to copy (comma-separated), or 'all': ").strip()
    selected_names = []
    if choice.lower() == "all":
        selected_names = src_names.copy()
    else:
        tokens = [t.strip() for t in choice.split(",") if t.strip()]
        for tok in tokens:
            if tok.isdigit():
                idx = int(tok) - 1
                if 0 <= idx < len(src_names):
                    name = src_names[idx]
                    if name not in selected_names:
                        selected_names.append(name)
                else:
                    print(f"Ignoring invalid number: {tok}")
            else:
                # exact or case-insensitive match
                if tok in src_names:
                    if tok not in selected_names:
                        selected_names.append(tok)
                else:
                    matches = [n for n in src_names if n.lower() == tok.lower()]
                    if matches:
                        name = matches[0]
                        if name not in selected_names:
                            selected_names.append(name)
                    else:
                        print(f"Ignoring unknown diagram name: {tok}")

    if not selected_names:
        print("No valid diagrams selected. Exiting.")
        sys.exit(1)

    dst_json = load_json(dst_diagram_path)
    dst_names = list_layout_names(dst_json)

    existing_ordinals = [d.get("ordinal") for d in dst_json.get("diagrams", []) if isinstance(d.get("ordinal"), int)]
    next_ordinal = (max(existing_ordinals) + 1) if existing_ordinals else len(dst_json.get("diagrams", []))

    copied = []
    for chosen_name in selected_names:
        src_diagram = find_diagram_by_name(src_json.get("diagrams", []), chosen_name)
        if not src_diagram:
            print(f"Skipping '{chosen_name}': not found in source.")
            continue
        new_diagram = json.loads(json.dumps(src_diagram))
        new_name = unique_name_for_destination(chosen_name, dst_names)
        new_diagram["name"] = new_name
        new_diagram["ordinal"] = next_ordinal
        next_ordinal += 1
        dst_json.setdefault("diagrams", []).append(new_diagram)
        dst_names.append(new_name)
        copied.append((chosen_name, new_name))

    if not copied:
        print("No diagrams were copied.")
        sys.exit(1)

    write_json_atomic(dst_diagram_path, dst_json)
    for src_name, new_name in copied:
        print(f"Copied '{src_name}' -> '{new_name}'")
    print(f"Wrote {dst_diagram_path} (backup saved as .bak)")


if __name__ == "__main__":
    main()
```

#### How to Use the Script

1. **Save the script** as `copy_diagram_layouts.py`

2. **Run it:**
   ```bash
   python copy_diagram_layouts.py
   ```

3. **Follow the prompts:**
   ```
   Enter source path: C:\Projects\OldModel.pbip
   ✓ Found source: C:\Projects\OldModel.SemanticModel\diagramLayout.json
   
   Enter target path: C:\Projects\NewModel.pbip
   ✓ Found target: C:\Projects\NewModel.SemanticModel\diagramLayout.json
   
   Available layouts in source:
   1. Sales Analysis
   2. Product Hierarchy
   3. Customer View
   
   Enter layouts to copy: all
   
   ✓ Copy completed successfully!
   Layouts copied: 3
   ```

4. **Flexible input options:**
   - Full path to `.pbip` file
   - Path to `.SemanticModel` folder
   - Direct path to `diagramLayout.json`
   - All three work!

5. **Smart selection:**
   - Type `all` to copy everything
   - Type `1,3` to copy specific layouts by number
   - Type `Sales Analysis,Customer View` to copy by name

#### Script Features

✅ **Automatic duplicate handling** - Renames duplicates as "Layout Name (1)", "Layout Name (2)", etc.  
✅ **Flexible input** - Accepts .pbip files, folders, or direct JSON paths  
✅ **Multiple selection methods** - Copy by number, name, or all at once  
✅ **Error handling** - Clear messages if files aren't found  
✅ **Safe operation** - Validates before making changes

## Real-World Scenarios

### Scenario 1: Migrating to a New Model
You've rebuilt your semantic model with improved relationships. Copy your existing diagram layouts so you don't lose your organizational structure.

### Scenario 2: Team Collaboration
A colleague has perfectly organized their Model View. They send you just the `diagramLayout.json` file, and you import their layouts into your model.

### Scenario 3: Template Reuse
You have a standard layout for star schema models. Save it once and reuse it across all new projects.

### Scenario 4: Client Deliverables
You're delivering a model to a client. Copy your organized layouts to their version so they have a clean, professional Model View from day one.

## Troubleshooting

### Layout doesn't appear after copying

**Check:**
- Did you save the `diagramLayout.json` file?
- Is the JSON syntax valid? (Use a JSON validator if unsure)
- Did you add commas between diagram objects in the array?

### Tables appear in wrong positions

**Possible causes:**
- Table names don't match between models
- The `nodeIndex` values reference different tables
- Tables were renamed in the target model

**Solution:** The layout references table names. Ensure table names match exactly between source and target.

### Python script can't find files

**Check:**
- Are you using the correct file path?
- Did you include quotes if the path has spaces?
- Is the PBIP folder structure intact? (Don't rename folders)

### Duplicate layout name error

**Manual fix:** Rename one of the layouts before copying:
```json
"name": "Sales Model (New)"
```

**Automatic fix:** Use the Python script—it handles this automatically.

## Tips & Best Practices

1. **Backup first:** Always keep a copy of your original files before editing JSON manually

2. **Use VS Code:** It has built-in JSON validation and formatting (Shift+Alt+F to format)

3. **Verify JSON syntax:** After manual edits, validate at [jsonlint.com](https://jsonlint.com)

4. **Consistent naming:** Use clear, descriptive names for layouts like "Star Schema - Sales" instead of "Layout 1"

5. **Version control:** If using Git, commit your PBIP files to track layout changes over time

6. **Share just the JSON:** For collaboration, you only need to share `diagramLayout.json`, not the entire PBIP folder

7. **Test in a copy:** When trying this for the first time, work on a copy of your target file

## What This Saves You

**Manual method:**
- 15-30 minutes per model (for complex layouts)
- Risk of forgetting table positions
- Repetitive, error-prone work

**With this technique:**
- **Manual JSON edit:** 1-2 minutes
- **Python script:** 30 seconds
- Perfect reproduction of layouts
- Zero manual dragging

**Time savings:** From 30 minutes to 30 seconds = **60× faster**

## Additional Resources

- [Power BI Project Format Documentation](https://learn.microsoft.com/power-bi/developer/projects/projects-overview)
- [Model View in Power BI](https://learn.microsoft.com/power-bi/transform-model/desktop-relationship-view)
- [JSON Tutorial](https://www.json.org/)

## What's Next?

Want to learn more Power BI automation techniques? Check out these related guides:
- Cross-page bookmarks with PBIP files
- Batch measure creation with Tabular Editor
- Automated documentation generation

## Contributing

Found a better way to do this? Have suggestions for the Python script? Feel free to:
- Open an issue
- Submit a pull request
- Share your improvements

## License

This guide is provided as-is for educational purposes.

---

**Author:** Syed Haider Ali Shah  
**Last Updated:** December 2025  
**GitHub:** [github.com/shah-haider](https://github.com/shah-haider)

---
