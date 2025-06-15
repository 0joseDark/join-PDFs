Here is the **translated version of the code and explanation in English**, keeping the same structure and comments:

---

### ✅ Required Modules

1. **PyQt5** or **PySide6** – for the graphical interface.
2. **PyPDF2** – to merge PDF files.
3. **os** and **sys** – for file operations and system compatibility.
4. **threading** – to keep the interface responsive during the merge.

---

### 📦 Module Installation

```bash
# For Windows, Ubuntu or macOS
pip install PyQt5 PyPDF2
```

> If you prefer to use `PySide6`:

```bash
pip install PySide6 PyPDF2
```

---

### 🧠 Full Code with Step-by-Step Comments

```python
import os
import sys
from PyQt5.QtWidgets import (
    QApplication, QWidget, QPushButton, QVBoxLayout,
    QLabel, QFileDialog, QProgressBar, QMenuBar, QAction
)
from PyQt5.QtCore import Qt, QThread, pyqtSignal
from PyPDF2 import PdfMerger


class MergePDFThread(QThread):
    progress = pyqtSignal(int)
    finished = pyqtSignal(str)

    def __init__(self, source, destination):
        super().__init__()
        self.source = source
        self.destination = destination

    def run(self):
        try:
            merger = PdfMerger()
            files = sorted([
                f for f in os.listdir(self.source)
                if f.lower().endswith(".pdf")
            ])
            total = len(files)

            for i, name in enumerate(files, 1):
                path = os.path.join(self.source, name)
                merger.append(path)
                percentage = int((i / total) * 100)
                self.progress.emit(percentage)

            final_name = "merged_files.pdf"
            final_path = os.path.join(self.destination, final_name)
            merger.write(final_path)
            merger.close()

            self.finished.emit(f"File created: {final_path}")

        except Exception as e:
            self.finished.emit(f"Error: {str(e)}")


class MainWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Merge PDFs")
        self.setGeometry(200, 200, 400, 250)

        self.source = ""
        self.destination = ""

        # Main layout
        layout = QVBoxLayout()

        # Menu
        self.menu_bar = QMenuBar(self)
        file_menu = self.menu_bar.addMenu("File")
        exit_action = QAction("Exit", self)
        exit_action.triggered.connect(self.close)
        file_menu.addAction(exit_action)
        layout.setMenuBar(self.menu_bar)

        # Buttons
        self.button_source = QPushButton("Choose source folder")
        self.button_source.clicked.connect(self.choose_source)
        layout.addWidget(self.button_source)

        self.button_destination = QPushButton("Choose destination folder")
        self.button_destination.clicked.connect(self.choose_destination)
        layout.addWidget(self.button_destination)

        self.button_merge = QPushButton("Merge PDFs")
        self.button_merge.clicked.connect(self.merge_pdfs)
        layout.addWidget(self.button_merge)

        # Progress bar
        self.progress_bar = QProgressBar()
        self.progress_bar.setAlignment(Qt.AlignCenter)
        layout.addWidget(self.progress_bar)

        # Status label
        self.status_label = QLabel("")
        layout.addWidget(self.status_label)

        self.setLayout(layout)

    def choose_source(self):
        folder = QFileDialog.getExistingDirectory(self, "Select source folder")
        if folder:
            self.source = folder
            self.status_label.setText(f"Source folder: {folder}")

    def choose_destination(self):
        folder = QFileDialog.getExistingDirectory(self, "Select destination folder")
        if folder:
            self.destination = folder
            self.status_label.setText(f"Destination folder: {folder}")

    def merge_pdfs(self):
        if not self.source or not self.destination:
            self.status_label.setText("Please choose both folders.")
            return

        self.progress_bar.setValue(0)
        self.thread = MergePDFThread(self.source, self.destination)
        self.thread.progress.connect(self.progress_bar.setValue)
        self.thread.finished.connect(self.done)
        self.thread.start()

    def done(self, message):
        self.status_label.setText(message)


if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = MainWindow()
    window.show()
    sys.exit(app.exec_())
```

---

### 🧪 How to Use

1. **Run the script** in the terminal:

   ```bash
   python merge_pdfs.py
   ```

2. **Click the buttons** to:

   * Select the folder with the source PDFs.
   * Select the destination folder for the merged PDF.

3. **Click “Merge PDFs”**.

4. The progress bar will show the merging progress.

---

### 💡 Compatibility

| Operating System | Status            |
| ---------------- | ----------------- |
| Windows 10       | ✅ Fully supported |
| Ubuntu Linux     | ✅ Fully supported |
| macOS            | ✅ Fully supported |

> PyQt5 and PyPDF2 are cross-platform. The folder dialogs are native to the operating system.
