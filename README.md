<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Revision & Syllabus Tracker</title>
  <style>
    :root {
      --bg: #0f172a;
      --card-bg: #1e293b;
      --border: #334155;
      --text: #f8fafc;
      --text-muted: #94a3b8;
      --primary: #3b82f6;
      --primary-hover: #2563eb;
      --success: #10b981;
      --warning: #f59e0b;
      --danger: #ef4444;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
    body { background-color: var(--bg); color: var(--text); padding: 1.5rem 1rem; min-height: 100vh; }
    .container { max-width: 900px; margin: 0 auto; }

    header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1.5rem; }
    h1 { font-size: 1.5rem; font-weight: 700; }
    .btn { background: var(--primary); color: white; border: none; padding: 0.5rem 1rem; border-radius: 6px; cursor: pointer; font-size: 0.875rem; font-weight: 500; transition: background 0.2s; }
    .btn:hover { background: var(--primary-hover); }
    .btn-secondary { background: #334155; }
    .btn-secondary:hover { background: #475569; }

    /* KPI Cards */
    .kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 1rem; margin-bottom: 1.5rem; }
    .kpi-card { background: var(--card-bg); border: 1px solid var(--border); padding: 1rem; border-radius: 8px; }
    .kpi-card span { font-size: 0.75rem; color: var(--text-muted); text-transform: uppercase; letter-spacing: 0.05em; }
    .kpi-card h2 { font-size: 1.5rem; margin-top: 0.25rem; }

    /* Progress bar */
    .progress-wrapper { background: var(--card-bg); border: 1px solid var(--border); padding: 1rem; border-radius: 8px; margin-bottom: 1.5rem; }
    .progress-label { display: flex; justify-content: space-between; font-size: 0.875rem; margin-bottom: 0.5rem; }
    .progress-bar-bg { width: 100%; height: 10px; background: #334155; border-radius: 999px; overflow: hidden; }
    .progress-bar-fill { height: 100%; background: linear-gradient(90deg, #3b82f6, #10b981); width: 0%; transition: width 0.3s ease; }

    /* Input Form */
    .form-card { background: var(--card-bg); border: 1px solid var(--border); padding: 1rem; border-radius: 8px; margin-bottom: 1.5rem; display: flex; gap: 0.5rem; flex-wrap: wrap; }
    input, select { background: #0f172a; border: 1px solid var(--border); color: var(--text); padding: 0.5rem 0.75rem; border-radius: 6px; font-size: 0.875rem; }
    input:focus, select:focus { outline: 2px solid var(--primary); }
    .input-subject { width: 140px; }
    .input-chapter { flex: 1; min-width: 180px; }

    /* Table */
    .table-container { background: var(--card-bg); border: 1px solid var(--border); border-radius: 8px; overflow-x: auto; }
    table { width: 100%; border-collapse: collapse; text-align: left; font-size: 0.875rem; min-width: 650px; }
    th, td { padding: 0.75rem 1rem; border-bottom: 1px solid var(--border); }
    th { color: var(--text-muted); font-weight: 600; font-size: 0.75rem; text-transform: uppercase; }
    tr:last-child td { border-bottom: none; }
    tr.completed { opacity: 0.6; }

    .checkbox-group { display: flex; gap: 0.75rem; align-items: center; }
    .checkbox-item { display: flex; align-items: center; gap: 0.25rem; font-size: 0.75rem; color: var(--text-muted); }
    input[type="checkbox"] { width: 16px; height: 16px; accent-color: var(--success); cursor: pointer; }

    .badge { padding: 0.2rem 0.5rem; border-radius: 4px; font-size: 0.75rem; font-weight: 500; cursor: pointer; border: none; }
    .badge-weak { background: rgba(239, 68, 68, 0.2); color: #f87171; }
    .badge-medium { background: rgba(245, 158, 11, 0.2); color: #fbbf24; }
    .badge-strong { background: rgba(16, 185, 129, 0.2); color: #34d399; }

    .btn-delete { background: none; border: none; color: var(--danger); cursor: pointer; font-size: 1rem; padding: 0.25rem; }
    .btn-delete:hover { opacity: 0.8; }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div>
        <h1>🎯 Revision Tracker</h1>
        <p style="color: var(--text-muted); font-size: 0.875rem;">Track concept mastery, problem practice, and mock tests</p>
      </div>
      <div style="display: flex; gap: 0.5rem;">
        <button class="btn btn-secondary" onclick="exportData()">Export Data</button>
        <button class="btn btn-secondary" onclick="resetToSample()">Load Demo</button>
      </div>
    </header>

    <div class="kpi-grid">
      <div class="kpi-card">
        <span>Total Topics</span>
        <h2 id="kpi-total">0</h2>
      </div>
      <div class="kpi-card">
        <span>Completed Revisions</span>
        <h2 id="kpi-revs">0</h2>
      </div>
      <div class="kpi-card">
        <span>Mastered Topics</span>
        <h2 id="kpi-mastered">0</h2>
      </div>
    </div>

    <div class="progress-wrapper">
      <div class="progress-label">
        <span>Overall Syllabus Completion</span>
        <span id="progress-percent" style="font-weight: 700; color: var(--success);">0%</span>
      </div>
      <div class="progress-bar-bg">
        <div class="progress-bar-fill" id="progress-bar"></div>
      </div>
    </div>

    <form class="form-card" onsubmit="addTopic(event)">
      <input type="text" id="new-subject" class="input-subject" placeholder="Subject (e.g. Accounts)" required />
      <input type="text" id="new-chapter" class="input-chapter" placeholder="Chapter name or topic" required />
      <select id="new-priority">
        <option value="High">High Weightage</option>
        <option value="Medium" selected>Medium Weightage</option>
        <option value="Low">Low Weightage</option>
      </select>
      <button type="submit" class="btn">+ Add Chapter</button>
    </form>

    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>Subject</th>
            <th>Chapter</th>
            <th>Weightage</th>
            <th>Revisions (R1 / R2 / R3)</th>
            <th>Confidence</th>
            <th>Status</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody id="tracker-body"></tbody>
      </table>
    </div>
  </div>

  <script>
    const sampleTopics = [
      { id: 1, subject: "Accounts", chapter: "Bank Reconciliation Statement", priority: "High", r1: true, r2: true, r3: false, confidence: "Medium" },
      { id: 2, subject: "Accounts", chapter: "Depreciation & Provisions", priority: "High", r1: true, r2: false, r3: false, confidence: "Weak" },
      { id: 3, subject: "Economics", chapter: "Elasticity of Demand", priority: "Medium", r1: true, r2: true, r3: true, confidence: "Strong" },
      { id: 4, subject: "Business", chapter: "Forms of Business Organization", priority: "Medium", r1: false, r2: false, r3: false, confidence: "Weak" }
    ];

    let topics = JSON.parse(localStorage.getItem("student_revision_data")) || sampleTopics;

    function saveAndRender() {
      localStorage.setItem("student_revision_data", JSON.stringify(topics));
      renderTable();
      renderMetrics();
    }

    function renderTable() {
      const tbody = document.getElementById("tracker-body");
      tbody.innerHTML = "";

      topics.forEach((t) => {
        const revCount = (t.r1 ? 1 : 0) + (t.r2 ? 1 : 0) + (t.r3 ? 1 : 0);
        const status = revCount === 3 ? "Mastered" : revCount > 0 ? "In Progress" : "Not Started";
        const isDone = revCount === 3;

        const tr = document.createElement("tr");
        if (isDone) tr.classList.add("completed");

        tr.innerHTML = `
          <td style="font-weight: 600;">${t.subject}</td>
          <td>${t.chapter}</td>
          <td><span style="font-size: 0.75rem; color: var(--text-muted);">${t.priority}</span></td>
          <td>
            <div class="checkbox-group">
              <label class="checkbox-item"><input type="checkbox" ${t.r1 ? "checked" : ""} onchange="toggleRev(${t.id}, 'r1')" /> R1</label>
              <label class="checkbox-item"><input type="checkbox" ${t.r2 ? "checked" : ""} onchange="toggleRev(${t.id}, 'r2')" /> R2</label>
              <label class="checkbox-item"><input type="checkbox" ${t.r3 ? "checked" : ""} onchange="toggleRev(${t.id}, 'r3')" /> R3</label>
            </div>
          </td>
          <td>
            <button class="badge ${getConfidenceClass(t.confidence)}" onclick="cycleConfidence(${t.id})">
              ${t.confidence}
            </button>
          </td>
          <td>
            <span style="font-size: 0.75rem; font-weight: 600; color: ${status === "Mastered" ? "var(--success)" : status === "In Progress" ? "var(--warning)" : "var(--text-muted)"}">
              ${status}
            </span>
          </td>
          <td>
            <button class="btn-delete" title="Delete" onclick="deleteTopic(${t.id})">✕</button>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function getConfidenceClass(conf) {
      if (conf === "Strong") return "badge-strong";
      if (conf === "Medium") return "badge-medium";
      return "badge-weak";
    }

    function toggleRev(id, field) {
      topics = topics.map(t => t.id === id ? { ...t, [field]: !t[field] } : t);
      saveAndRender();
    }

    function cycleConfidence(id) {
      const order = ["Weak", "Medium", "Strong"];
      topics = topics.map(t => {
        if (t.id === id) {
          const next = order[(order.indexOf(t.confidence) + 1) % 3];
          return { ...t, confidence: next };
        }
        return t;
      });
      saveAndRender();
    }

    function addTopic(e) {
      e.preventDefault();
      const sub = document.getElementById("new-subject").value.trim();
      const chap = document.getElementById("new-chapter").value.trim();
      const prio = document.getElementById("new-priority").value;

      if (!sub || !chap) return;

      topics.push({
        id: Date.now(),
        subject: sub,
        chapter: chap,
        priority: prio,
        r1: false,
        r2: false,
        r3: false,
        confidence: "Weak"
      });

      document.getElementById("new-chapter").value = "";
      saveAndRender();
    }

    function deleteTopic(id) {
      topics = topics.filter(t => t.id !== id);
      saveAndRender();
    }

    function renderMetrics() {
      const total = topics.length;
      let revCount = 0;
      let mastered = 0;

      topics.forEach(t => {
        const count = (t.r1 ? 1 : 0) + (t.r2 ? 1 : 0) + (t.r3 ? 1 : 0);
        revCount += count;
        if (count === 3) mastered++;
      });

      const maxRevs = total * 3;
      const pct = maxRevs > 0 ? Math.round((revCount / maxRevs) * 100) : 0;

      document.getElementById("kpi-total").innerText = total;
      document.getElementById("kpi-revs").innerText = `${revCount}/${maxRevs}`;
      document.getElementById("kpi-mastered").innerText = mastered;
      document.getElementById("progress-percent").innerText = `${pct}%`;
      document.getElementById("progress-bar").style.width = `${pct}%`;
    }

    function exportData() {
      const blob = new Blob([JSON.stringify(topics, null, 2)], { type: "application/json" });
      const a = document.createElement("a");
      a.href = URL.createObjectURL(blob);
      a.download = "revision_backup.json";
      a.click();
    }

    function resetToSample() {
      if (confirm("Reset to sample topics?")) {
        topics = sampleTopics;
        saveAndRender();
      }
    }

    // Initialize on load
    saveAndRender();
  </script>
</body>
</html>
