#!/usr/bin/env bash
set -euo pipefail

# ──────────────────────────────────────────────────────────────
# Requirements: git, GitHub CLI (gh)
# Install gh: https://cli.github.com/  (macOS: brew install gh)
# ──────────────────────────────────────────────────────────────

need() { command -v "$1" >/dev/null 2>&1 || { echo "Missing dependency: $1"; exit 1; }; }
need git
need gh

echo "→ Checking GitHub authentication…"
if ! gh auth status >/dev/null 2>&1; then
  echo "You are not logged in. Launching gh auth flow…"
  gh auth login -s 'repo,read:org'
fi

USER_LOGIN=$(gh api user --jq .login)
REPO="${USER_LOGIN}.github.io"
SITE_URL="https://${USER_LOGIN}.github.io"

echo "→ Using account: ${USER_LOGIN}"
if gh repo view "$REPO" >/dev/null 2>&1; then
  echo "→ Repo ${REPO} already exists. We'll update it."
else
  echo "→ Creating repo ${REPO}…"
  gh repo create "$REPO" --public --description "Personal website" --disable-wiki --disable-issues
fi

# Work in a clean folder
WORKDIR="${PWD}/${REPO}"
if [ -d "$WORKDIR/.git" ]; then
  echo "→ Local folder already present: $WORKDIR"
else
  echo "→ Cloning ${REPO}…"
  git clone "https://github.com/${USER_LOGIN}/${REPO}.git" "$WORKDIR"
fi
cd "$WORKDIR"

# ──────────────────────────────────────────────────────────────
# Create site files (index.html + style.css)
# Content prefilled from your resume; edit anytime after publishing.
# ──────────────────────────────────────────────────────────────

mkdir -p assets

cat > style.css <<'CSS'
:root{--bg:#0f172a;--panel:#111827;--text:#e5e7eb;--muted:#9ca3af;--accent:#60a5fa;}
*{box-sizing:border-box}body{margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,Inter,sans-serif;background:radial-gradient(1200px 800px at 70% -10%,#1f2937,transparent),var(--bg);color:var(--text);line-height:1.6}
.wrap{max-width:960px;margin:0 auto;padding:56px 20px}
nav{display:flex;gap:18px;flex-wrap:wrap;margin-bottom:24px}
nav a{color:var(--muted);text-decoration:none;padding:8px 12px;border-radius:999px;border:1px solid #1f2937}
nav a:hover{color:var(--text);border-color:#374151}
.header{display:flex;flex-direction:column;gap:6px;margin:24px 0 32px}
h1{margin:0;font-size:40px;letter-spacing:.3px}
.subtitle{color:var(--muted)}
.grid{display:grid;grid-template-columns:1fr;gap:16px}
@media(min-width:860px){.grid{grid-template-columns:1fr 1fr}}
section{background:linear-gradient(180deg,rgba(255,255,255,.03),rgba(255,255,255,.01));border:1px solid #1f2937;border-radius:18px;padding:18px 20px}
h2{margin:0 0 6px 0;font-size:22px}
.item{padding:10px 0;border-top:1px dashed #1f2937}
.item:first-child{border-top:none}
small,time{color:var(--muted)}
.badges{display:flex;flex-wrap:wrap;gap:8px;margin-top:8px}
.badge{border:1px solid #374151;border-radius:10px;padding:4px 8px;font-size:12px;color:var(--muted)}
a{color:var(--accent)}
footer{margin:28px 0;color:var(--muted);font-size:14px;text-align:center}
.btn{display:inline-block;margin-top:8px;padding:8px 12px;border-radius:10px;border:1px solid #1f2937;color:var(--text);text-decoration:none}
.btn:hover{border-color:#374151}
CSS

cat > index.html <<'HTML'
<!doctype html>
<html lang="en">
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Ibrahim Moustafa Abdellatif — Personal Website</title>
<link rel="stylesheet" href="style.css">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<meta name="description" content="Portfolio and projects of Ibrahim Abdellatif — Autonomous Vehicles & Robotics.">
<body>
  <div class="wrap">
    <nav>
      <a href="#about">About</a>
      <a href="#education">Education</a>
      <a href="#publications">Publications</a>
      <a href="#projects">Projects</a>
      <a href="#activities">Activities</a>
      <a href="#skills">Skills</a>
      <a href="#contact">Contact</a>
      <a class="btn" href="https://github.com/" target="_blank" rel="noopener">GitHub</a>
    </nav>

    <header class="header">
      <h1>Ibrahim Moustafa Abdellatif</h1>
      <div class="subtitle">Autonomous Vehicles & Robotics — San Giovanni, Naples, Italy</div>
      <div class="badges">
        <span class="badge">Trajectory Optimization</span>
        <span class="badge">Path Planning</span>
        <span class="badge">Control & Optimization</span>
      </div>
    </header>

    <div class="grid">
      <section id="about">
        <h2>About</h2>
        <div class="item">
          Enthusiastic master's student in Autonomous Vehicles Engineering, aiming to pioneer in the robotics industry. Passionate about systems engineering, trajectory optimization, path planning, and control.
        </div>
      </section>

      <section id="education">
        <h2>Education</h2>
        <div class="item">
          <strong>University of Naples Federico II</strong> — M.Sc. Industrial Engineering (Autonomous Vehicles)
          <br><small>Sep 2024 – Jun 2026 · Naples, Campania, Italy</small>
        </div>
        <div class="item">
          <strong>Nile University</strong> — B.Sc. Mechanical Engineering (Mechatronics, Robotics & Automation)
          <br><small>Feb 2020 – Jun 2024 · Merit Scholarship · GPA 3.49/4.0 · Dean’s List (Fall ’23)</small>
        </div>
      </section>

      <section id="publications">
        <h2>Publications</h2>
        <div class="item">
          Dynamic Modelling and Kinematic Verification for Quadruped Robot — <em>International Journal of Intelligent Robotics and Applications</em>, 2025.
          <br><small>DOI: 10.1007/s41315-025-00472-0</small>
        </div>
        <div class="item">
          Innovations in 3D Printing-Assisted Biopolymers for Biomedical Applications — Wiley (Book Chapter).
        </div>
        <div class="item">
          Optimization of Solar Tree Performance in Egypt — <em>SN Applied Sciences</em>, Nov 2023.
          <br><small>DOI: 10.1007/s42452-023-05575-6</small>
        </div>
        <div class="item">
          Modelling & Control of a Self-Balancing Robot Using Sensor Fusion and LQR — IEEE NILES 2022.
          <br><small>DOI: 10.1109/NILES56402.2022.9942364</small>
        </div>
      </section>

      <section id="projects">
        <h2>Projects</h2>
        <div class="item">
          <strong>Switching between CC/ACC using multiple PID controllers</strong>
          <br><small>Sep 2024 – Jan 2025</small><br>
          Designed a control system to switch between Cruise Control and Adaptive Cruise Control; built 3D driving scenarios in MATLAB Automated Driving Toolbox.
        </div>
        <div class="item">
          <strong>Intelligent motion control of UGV: Quadruped Robot using RL</strong>
          <br><small>Feb 2023 – Feb 2024</small><br>
          Hierarchical control (RL high-level + low-level foot control) on a 12-DOF quadruped (Anubis). Teensy 4 + Raspberry Pi 4B; IMU and LiDAR for state estimation & mapping.
        </div>
        <div class="item">
          <strong>Robotic Arm control in CoppeliaSim</strong>
          <br><small>Feb 2023 – Jun 2023</small><br>
          DH parameterization, inverse kinematics, URDF generation, and control simulation for ABB IRB4600.
        </div>
      </section>

      <section id="activities">
        <h2>Activities</h2>
        <div class="item">
          <strong>IGNIS</strong> — ADCS Member · Feb 2025 – Present
          <br><small>Designing Attitude Determination & Control for a CubeSat (Univ. of Naples & ESA funded).</small>
        </div>
        <div class="item">
          <strong>RPM NU</strong> — Vice President · Sep 2021 – Mar 2023
          <br><small>NASA CanSat team lead; competitions planning; side-projects program lead.</small>
        </div>
      </section>

      <section id="skills">
        <h2>Skills</h2>
        <div class="item">
          <strong>Robotics:</strong> ROS, Gazebo, SLAMTEC ·
          <strong>CAD/CAM:</strong> AutoCAD, SolidWorks, RDWorks, Cura ·
          <strong>Modeling:</strong> Simulink ·
          <strong>PCB:</strong> Proteus, EasyEDA ·
          <strong>Programming:</strong> C/C++, Python, MATLAB ·
          <strong>Research:</strong> LaTeX, Mendeley
        </div>
        <div class="badges">
          <span class="badge">Arabic (native)</span>
          <span class="badge">English (fluent)</span>
          <span class="badge">German (beginner)</span>
          <span class="badge">Italian (beginner)</span>
        </div>
      </section>

      <section id="contact">
        <h2>Contact</h2>
        <div class="item">
          <div><strong>Email:</strong> ab.abdellatif@studenti.unina.it</div>
          <div><strong>Phone:</strong> +39 351 488 7524</div>
          <div><strong>LinkedIn:</strong> <a href="https://www.linkedin.com/in/ibrahim-abdellatif-aa6758150/" target="_blank" rel="noopener">ibrahim-abdellatif-aa6758150</a></div>
        </div>
      </section>
    </div>

    <footer>
      © <span id="y"></span> Ibrahim Moustafa Abdellatif · Built with GitHub Pages.
      <script>document.getElementById('y').textContent=new Date().getFullYear()</script>
    </footer>
  </div>
</body>
</html>
HTML

# Git bootstrap & publish
if [ ! -d .git ]; then
  git init
  git branch -M main
  git remote add origin "https://github.com/${USER_LOGIN}/${REPO}.git"
fi

git add -A
git commit -m "Initial site publish"
git push -u origin main

echo
echo "✅ Done! Your site will be live at: ${SITE_URL} (in a minute or two)."
echo "To customize, edit ${WORKDIR}/index.html and style.css, then:"
echo "  git add -A && git commit -m 'Update site' && git push"
