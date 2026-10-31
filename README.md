# Gantt Manager

A Java Swing desktop application for loading, browsing and exporting **Gantt project plans**. It reads a project's tasks from a TSV file, organizes them into a two-level hierarchy (top-level tasks and their subtasks), automatically computes the cost and duration of composite tasks, and exports the full plan as a **text, Markdown or HTML report**.

Developed for the **Software Development** course at the Department of Computer Science & Engineering, University of Ioannina (2023).

![Screenshot](https://github.com/PanagiotisT2307/GanttManager-Project/assets/49351300/3fb252d0-f512-4ddd-88a4-768603c74133)

## Features
- **Load a project** from a tab-separated (`.tsv`) file through a file dialog.
- **Task hierarchy:** top-level tasks with subtasks, displayed in a table view.
- **Automatic aggregation:** a top-level task that has subtasks gets its **cost** (sum of the subtasks' costs) and its **start/end time** (earliest start, latest end of its subtasks) computed automatically.
- **Sorted view:** tasks are ordered by start time (ties broken by ID), with each top-level task followed by its own subtasks.
- **Filters:**
  - Top-level tasks only
  - Task by ID
  - Tasks whose description starts with a given prefix
- **Reports:** export the whole plan as `.txt`, `.md` (Markdown table) or `.html` (table with top-level tasks in bold).

## Input format
Each line of the TSV file is a task, with tab-separated columns:

| Column | Meaning |
|---|---|
| `TaskId` | Unique numeric ID |
| `TaskText` | Task description |
| `MamaId` | ID of the parent task, or `0` for a top-level task |
| `Start` | Start time |
| `End` | End time |
| `Cost` | Cost of the task |

Top-level tasks that have subtasks only need the first three columns; their start, end and cost are computed from their subtasks. Example (`src/main/resources/input/Eggs.tsv`):

```
100	Prepare Fry	0
101	Turn on burner (low)	100	1	1	10
102	Break eggs and pour into fry	100	2	4	10
...
200	Prepare the bread	0
201	Heat bread in toaster	200	10	12	10
```

After loading, `Prepare Fry` is shown with start `1`, end `12` and cost `60`, computed from its six subtasks.

Sample input files are in `src/main/resources/input/` (`Eggs.tsv`, `EggsScrambled.tsv` with the rows in random order, and `Shop.tsv`), and example reports are in `src/main/resources/output/`.

## Design
The application is split into a GUI front end and a back end that communicate only through the `IMainController` interface. The interface, the table model (`dom2app`) and parts of the GUI skeleton were provided by the course; the back end was implemented by us.

- **Domain model:** an abstract `Task` class with two subclasses, `SimpleTask` and `ComplexTask`. A `ComplexTask` holds its subtasks and computes its own cost and time span from them.
- **Loading:** `LoadFromText` parses the TSV file, builds the task hierarchy and sorts it.
- **Filters:** a generic `Display<T>` base class with one subclass per filter (`DisplayTopLevel`, `DisplayByID`, `DisplayByPrefix`).
- **Reports:** an abstract `Save` class with one subclass per output format (`SaveText`, `SaveMarkdown`, `SaveHTML`). Adding a new format means adding one new subclass.
- **Factory:** `MainControllerFactory` creates the back-end controller, so the GUI never depends on the concrete implementation.

## Project structure
```
GanttManager_Project/src/main/java/
├── app/
│   ├── gui/
│   │   ├── AppStarter.java              # Main class (launches the GUI)
│   │   ├── JFrameLevel00RootFrame.java  # Main window and menus
│   │   └── jtableview/JTableViewer.java # Table view of tasks
│   ├── naive/NaiveClient.java           # Console demo without the GUI
│   └── AppController.java               # Connects the GUI to the back end
├── backend/
│   ├── IMainController.java             # Front end / back end contract
│   ├── MainController.java              # Back-end implementation
│   ├── MainControllerFactory.java
│   ├── LoadFromText.java                # TSV parsing and sorting
│   ├── Task.java, SimpleTask.java, ComplexTask.java
│   ├── ReportType.java                  # TEXT, MD, HTML
│   ├── display/                         # Filters
│   └── savefile/                        # Report writers
└── dom2app/SimpleTableModel.java        # Table data passed to the GUI
```

## Requirements
- Java 8 or later.
- Apache Commons Collections 4.4 (included in `src/main/resources/lib/`).

## Build & Run

### With Eclipse
1. **File → Import → Existing Projects into Workspace** (or create a Java project from the `GanttManager_Project` folder).
2. Add `src/main/resources/lib/commons-collections4-4.4.jar` to the build path.
3. Run `src/main/java/app/gui/AppStarter.java`.

### From the command line
From the `GanttManager_Project` folder:

```bash
# Compile
javac -d out -cp src/main/resources/lib/commons-collections4-4.4.jar \
  $(find src/main/java -name "*.java")

# Run the GUI
java -cp "out:src/main/resources/lib/commons-collections4-4.4.jar" app.gui.AppStarter

# Or run the console demo
java -cp "out:src/main/resources/lib/commons-collections4-4.4.jar" app.naive.NaiveClient
```

On Windows, use `;` instead of `:` as the classpath separator.

## How to use
1. **File → Load TSV** and choose a `.tsv` file (for example from `src/main/resources/input/`).
2. Use the **Filters** menu to show only top-level tasks, a task by ID, or tasks by description prefix.
3. Use the **Report** menu to save the plan as `.txt`, `.md` or `.html`.

## Authors

**Panagiotis Trypos**
- [Website / Portfolio](https://ptrypos.me)
- [GitHub](https://github.com/ptrypos)
- [LinkedIn](https://www.linkedin.com/in/%CF%80%CE%B1%CE%BD%CE%B1%CE%B3%CE%B9%CF%8E%CF%84%CE%B7%CF%82-%CF%84%CF%81%CF%8D%CF%80%CE%BF%CF%82-983abb242)


**Athanasios Tonios**
- [GitHub](https://github.com/athanasios-tonios)
