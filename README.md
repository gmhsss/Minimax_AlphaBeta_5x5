# TDE 2 — Minimax vs Alpha-Beta (5×5 Tic-Tac-Toe, 4-in-a-row)

Two competitive agents for a modified Tic-Tac-Toe on a **5×5** board where the goal is **4 in a row**:

- **Agent A:** depth-limited **Minimax** with heuristic at cutoff  
- **Agent B:** **Minimax + Alpha-Beta pruning** (same evaluation, fewer explored nodes)

This project includes simulation code, CSV outputs, example charts, and a short PDF report.

---


> If some optional files are missing, generate them following the steps below.

---

## Requirements

- **Python 3.10+**
- To run simulations and produce CSV: no extra packages required.
- To generate charts and PDF locally, install:

```bash
pip install pandas matplotlib Pillow
---
▶️ Run Simulations

simulate.py exposes functions; call them directly via python -c.

1) Single Series (Creates results.csv)
Windows (PowerShell)
python -c "from simulate import run_series; print(run_series(n_games=6, depth_minimax=3, depth_alphabeta=3, out_csv='results.csv'))"

Linux/macOS
python -c "from simulate import run_series; print(run_series(n_games=6, depth_minimax=3, depth_alphabeta=3, out_csv='results.csv'))"


CSV columns:

game, agent_X, agent_O, result, minimax_nodes, alphabeta_nodes, minimax_time_ms, alphabeta_time_ms

2) Two Series (Depth 2 and 3), Like the Sample Report
Windows (PowerShell)
python -c "from simulate import run_series; run_series(6,2,2,'results_d2.csv')"
python -c "from simulate import run_series; run_series(6,3,3,'results_d3.csv')"

Linux/macOS
python -c "from simulate import run_series; run_series(6,2,2,'results_d2.csv')"
python -c "from simulate import run_series; run_series(6,3,3,'results_d3.csv')"

📈 Generate Charts & PDF (Optional)

Install the required packages:

pip install pandas matplotlib pillow


Create a file named make_report.py, paste the following snippet, then run:

python make_report.py

make_report.py
import pandas as pd, matplotlib.pyplot as plt
from PIL import Image, ImageDraw, ImageFont
import os

BASE = os.path.dirname(__file__)
def p(*x): return os.path.join(BASE, *x)

# Load CSVs produced by run_series (depth 2 and 3)
d2 = pd.read_csv(p("results_d2.csv"))
d3 = pd.read_csv(p("results_d3.csv"))
df_sum = pd.DataFrame([
    {"depth":2, "minimax_nodes_total":int(d2.minimax_nodes.sum()), "alphabeta_nodes_total":int(d2.alphabeta_nodes.sum()),
     "minimax_time_ms_total":float(d2.minimax_time_ms.sum()), "alphabeta_time_ms_total":float(d2.alphabeta_time_ms.sum())},
    {"depth":3, "minimax_nodes_total":int(d3.minimax_nodes.sum()), "alphabeta_nodes_total":int(d3.alphabeta_nodes.sum()),
     "minimax_time_ms_total":float(d3.minimax_time_ms.sum()), "alphabeta_time_ms_total":float(d3.alphabeta_time_ms.sum())},
])

# Chart: nodes by depth
plt.figure()
df_sum.set_index("depth")[["minimax_nodes_total","alphabeta_nodes_total"]].plot(kind="bar", title="Totals per Depth – Nodes")
plt.ylabel("nodes"); plt.tight_layout(); plt.savefig(p("totals_nodes.png")); plt.close()

# Chart: time by depth
plt.figure()
df_sum.set_index("depth")[["minimax_time_ms_total","alphabeta_time_ms_total"]].plot(kind="bar", title="Totals per Depth – Time (ms)")
plt.ylabel("ms"); plt.tight_layout(); plt.savefig(p("totals_time_ms.png")); plt.close()

# One-page PDF with charts
PAGE_W, PAGE_H = 1240, 1754
MARGIN = 80
img = Image.new("RGB", (PAGE_W, PAGE_H), "white")
draw = ImageDraw.Draw(img)
try:
    font = ImageFont.truetype("DejaVuSans.ttf", 28)
    font2 = ImageFont.truetype("DejaVuSans.ttf", 24)
except:
    font = ImageFont.load_default(); font2 = ImageFont.load_default()

def paste_center(path, y, max_h=500):
    im = Image.open(path).convert("RGB")
    scale = min(1.0, (PAGE_W-2*MARGIN)/im.width, max_h/im.height)
    im = im.resize((int(im.width*scale), int(im.height*scale)))
    x = (PAGE_W-im.width)//2
    img.paste(im, (x, y))
    return y + im.height + 20

draw.text((MARGIN, MARGIN), "TDE2 – Minimax vs Alpha-Beta (5×5, 4-in-a-row)", font=font, fill="black")

⚙️ Parameters (run_series)

n_games: number of games (e.g., 6)

depth_minimax: depth for the Minimax agent

depth_alphabeta: depth for the Alpha-Beta agent

out_csv: output CSV filename

Example:

python -c "from simulate import run_series; run_series(10,4,4,'results_d4.csv')"

