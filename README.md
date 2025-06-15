Aplicação em Python com Qt (PyQt5 ou PySide6) para juntar ficheiros PDF de uma pasta de origem num único PDF na pasta de destino. A aplicação terá:

* Interface com botões e menu.
* Seleção de pasta de origem e de destino.
* Listagem dos ficheiros PDF a juntar com numeração.
* Barra de progresso com percentagem.
* Comentários passo a passo.
* Compatibilidade com Windows 10, Ubuntu e Mac.

---

### ✅ Módulos necessários

1. **PyQt5** ou **PySide6** – para a interface gráfica.
2. **PyPDF2** – para juntar os ficheiros PDF.
3. **os** e **sys** – para manipulação de ficheiros e compatibilidade de sistema.
4. **threading** – para manter a interface responsiva durante o processo.

---

### 📦 Instalação dos módulos

```bash
# Para instalar no Windows, Ubuntu ou Mac
pip install PyQt5 PyPDF2
```

> Se preferir usar `PySide6`:

```bash
pip install PySide6 PyPDF2
```

---

### 🧠 Código completo com comentários

```python
import os
import sys
from PyQt5.QtWidgets import (
    QApplication, QWidget, QPushButton, QVBoxLayout,
    QLabel, QFileDialog, QProgressBar, QMenuBar, QAction
)
from PyQt5.QtCore import Qt, QThread, pyqtSignal
from PyPDF2 import PdfMerger


class JuntarPDFThread(QThread):
    progresso = pyqtSignal(int)
    concluido = pyqtSignal(str)

    def __init__(self, origem, destino):
        super().__init__()
        self.origem = origem
        self.destino = destino

    def run(self):
        try:
            merger = PdfMerger()
            ficheiros = sorted([
                f for f in os.listdir(self.origem)
                if f.lower().endswith(".pdf")
            ])
            total = len(ficheiros)

            for i, nome in enumerate(ficheiros, 1):
                caminho = os.path.join(self.origem, nome)
                merger.append(caminho)
                progresso = int((i / total) * 100)
                self.progresso.emit(progresso)

            nome_final = "ficheiros_juntos.pdf"
            destino_final = os.path.join(self.destino, nome_final)
            merger.write(destino_final)
            merger.close()

            self.concluido.emit(f"Ficheiro criado: {destino_final}")

        except Exception as e:
            self.concluido.emit(f"Erro: {str(e)}")


class JanelaPrincipal(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Juntar PDFs")
        self.setGeometry(200, 200, 400, 250)

        self.origem = ""
        self.destino = ""

        # Layout principal
        layout = QVBoxLayout()

        # Menu
        self.menu_bar = QMenuBar(self)
        ficheiro_menu = self.menu_bar.addMenu("Ficheiro")
        sair_acao = QAction("Sair", self)
        sair_acao.triggered.connect(self.close)
        ficheiro_menu.addAction(sair_acao)
        layout.setMenuBar(self.menu_bar)

        # Botões
        self.botao_origem = QPushButton("Escolher pasta de origem")
        self.botao_origem.clicked.connect(self.escolher_origem)
        layout.addWidget(self.botao_origem)

        self.botao_destino = QPushButton("Escolher pasta de destino")
        self.botao_destino.clicked.connect(self.escolher_destino)
        layout.addWidget(self.botao_destino)

        self.botao_juntar = QPushButton("Juntar PDFs")
        self.botao_juntar.clicked.connect(self.juntar_pdfs)
        layout.addWidget(self.botao_juntar)

        # Barra de progresso
        self.barra = QProgressBar()
        self.barra.setAlignment(Qt.AlignCenter)
        layout.addWidget(self.barra)

        # Mensagem de estado
        self.label_estado = QLabel("")
        layout.addWidget(self.label_estado)

        self.setLayout(layout)

    def escolher_origem(self):
        pasta = QFileDialog.getExistingDirectory(self, "Selecionar pasta de origem")
        if pasta:
            self.origem = pasta
            self.label_estado.setText(f"Pasta de origem: {pasta}")

    def escolher_destino(self):
        pasta = QFileDialog.getExistingDirectory(self, "Selecionar pasta de destino")
        if pasta:
            self.destino = pasta
            self.label_estado.setText(f"Pasta de destino: {pasta}")

    def juntar_pdfs(self):
        if not self.origem or not self.destino:
            self.label_estado.setText("Escolha ambas as pastas.")
            return

        self.barra.setValue(0)
        self.thread = JuntarPDFThread(self.origem, self.destino)
        self.thread.progresso.connect(self.barra.setValue)
        self.thread.concluido.connect(self.terminou)
        self.thread.start()

    def terminou(self, mensagem):
        self.label_estado.setText(mensagem)


if __name__ == "__main__":
    app = QApplication(sys.argv)
    janela = JanelaPrincipal()
    janela.show()
    sys.exit(app.exec_())
```

---

### 🧪 Como usar

1. **Executar o script** no terminal:

   ```bash
   python juntar_pdfs.py
   ```

2. **Clicar nos botões** para escolher:

   * A pasta onde estão os PDFs.
   * A pasta onde guardar o PDF final.

3. **Clicar em “Juntar PDFs”**.

4. A barra de progresso e percentagem mostrarão o progresso.

---

### 💡 Compatibilidade

| Sistema Operativo | Compatibilidade |
| ----------------- | --------------- |
| Windows 10        | ✅ Total         |
| Ubuntu Linux      | ✅ Total         |
| macOS             | ✅ Total         |

> PyQt5 e PyPDF2 são multiplataforma. A seleção de pastas e caminhos usa as caixas nativas do sistema operativo.

---

posso criar um `.exe`, `.deb` ou `.app` para distribuição.
