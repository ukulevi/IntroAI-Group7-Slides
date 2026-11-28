# IntroAI — Group 7 (CC01) Presentation Slides

Kho lưu trữ source LaTeX Beamer (XeLaTeX) và kịch bản thuyết trình cho môn **Introduction to Artificial Intelligence (CO3061)** — **HCMUT · VNU-HCM**.

Mỗi khi có commit/PR vào `main`, **GitHub Actions** sẽ tự động biên dịch PDF bằng **XeLaTeX** (TeX Live full) và cập nhật vào Release `latest` — các thành viên không cần cài TeX Live hay dùng Overleaf trả phí.

## 1. Tải PDF mới nhất (1-Click)

- **HW1 — PathFinding HCMC (`9:20`):** [IntroAI-HW1-Path-Find-Slide.pdf](https://github.com/ukulevi/IntroAI-Group7-Slides/releases/download/latest/IntroAI-HW1-Path-Find-Slide.pdf)
- **HW2 — Wizard's Chess (`9:20`):** [IntroAI-HW2-WizChess-Slide.pdf](https://github.com/ukulevi/IntroAI-Group7-Slides/releases/download/latest/IntroAI-HW2-WizChess-Slide.pdf)
- Xem lịch sử các lần build: tab **Actions** hoặc **Releases → `latest`**.

## 2. Phân bổ thời lượng thuyết trình (20 phút + 10 phút Q&A)

| Phần | Nội dung | Thời lượng | Source & Kịch bản |
| :--- | :--- | :---: | :--- |
| **Topic 1 (HW1)** | PathFinding HCMC — Traffic-Aware A* & ALT (12 slide chính + 6 Q&A backup) | **9:20** | `hw1/main.tex` · `hw1/script_short_en.md` |
| **Chuyển tiếp** | Đổi người nói / chuyển deck | **0:20** | — |
| **Topic 2 (HW2)** | Wizard's Chess — Alpha-Beta vs MCTS (12 slide chính + 6 Q&A backup) | **9:20** | `hw2/main.tex` · `hw2/script_short_en.md` |
| **Tổng nói chính** | Đệm an toàn 1:00 trong khung 20:00 | **19:00** | — |
| **Q&A chung** | Dùng slide `Q1`–`Q6` (HW1) và `Q1`–`Q6` (HW2) khi được hỏi | **10:00** | Xem mục Q&A trong từng `script_short_en.md` |

## 3. Cách thành viên chỉnh sửa slide (Miễn phí, không cần cài LaTeX)

1. **Sửa nhanh ngay trên trình duyệt:**
   - Mở repo trên GitHub, bấm phím `.` (dấu chấm) để mở trình soạn thảo **github.dev**.
   - Sửa `hw1/main.tex` hoặc `hw2/main.tex`, vào tab **Source Control**, nhập commit message và bấm **Commit & Push**.
   - Sau ~1–2 phút, vào **Releases → `latest`** (hoặc tab **Actions**) để tải PDF mới.
2. **Hoặc sửa trên máy:** Clone repo qua **GitHub Desktop**, chỉnh bằng VS Code, commit & push.
3. **Mời đủ 8 thành viên:** Chủ repo vào **Settings → Collaborators → Add people** (GitHub Free cho phép không giới hạn collaborator trên repo Private, kèm 2,000 phút Actions/tháng).
