---
layout: page
title: "Scenario"
permalink: /scenario/
---

{% include password.html %}
<style>
h1.page-title, .page-title, h1 {
  text-align: center;
}
</style>

<style>
.case-card {
  border: 1px solid #e2e8f0;
  border-radius: 1em;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
  padding: 1em 1.5em;
  margin: 1.5em 0;
  background: #fafbfc;
  cursor: pointer;
  transition: box-shadow .2s;
  position: relative;
}
.case-card:hover {
  box-shadow: 0 6px 24px rgba(0,0,0,0.13);
  background: #f5f7fa;
}
.case-summary {
  display: flex;
  gap: 1.2em;
  font-weight: 500;
}
.case-details {
  margin-top: 1em;
  display: none;
  font-size: 0.98em;
  color: #34495e;
}
.case-card.expanded .case-details {
  display: block;
}
.case-expand-tip {
  color: #888;
  font-size: 0.92em;
  position: absolute;
  top: 1em;
  right: 1.5em;
  user-select: none;
}
.filter-bar {
  margin: 1.5em 0 2.5em 0;
  display: flex;
  gap: 1.6em;
  align-items: center;
  flex-wrap: wrap;
  justify-content: center;
}

.filter-bar label {
  font-weight: 600;
  color: #57606a;
  margin-right: 0.2em;
  font-size: 1.07em;
}

.filter-bar select {
  padding: 0.35em 1.3em 0.35em 0.7em;
  border-radius: 0.7em;
  border: 1px solid #ccd6df;
  background: #f6f8fa url("data:image/svg+xml;utf8,<svg width='14' height='14' xmlns='http://www.w3.org/2000/svg'><polygon points='7,10 3,5 11,5' fill='%2398a1b3'/></svg>") no-repeat right 0.9em center/1em 1em;
  box-shadow: 0 2px 8px rgba(35,45,58,0.06);
  font-size: 1em;
  color: #25282c;
  transition: border 0.2s, box-shadow 0.2s;
  min-width: 115px;
  outline: none;
  appearance: none;
  -webkit-appearance: none;
  cursor: pointer;
}

.filter-bar select:focus {
  border: 1.5px solid #4f8cff;
  box-shadow: 0 2px 16px rgba(85,140,255,0.10);
  background-color: #f0f6ff;
}

.task-panel {
  border: 2.5px solid #e0e7ef;
  border-radius: 1.2em;
  box-shadow: 0 4px 22px #e5eefa;
  background: #fcfcfe;
  padding: 2.1em 1.7em 1.7em 1.7em;
  margin: 2em auto 3.2em auto;
  max-width: 900px;
  min-width: 330px;
  position: relative;
  /* 可根据需求加宽度 */
}
.task-panel h2 {
  font-size: 1.25em;
  font-weight: 700;
  letter-spacing: 0.04em;
  margin-bottom: 1.6em;
}
.task-panel .filter-bar {
  margin-bottom: 1.3em;
  margin-top: 0;
}
.task-panel .case-list-scroll {
  max-height: 340px;
  overflow-y: auto;
  padding-right: 0.5em;
  border-radius: 0.5em;
  /* 为了显式分区，可以加背景色 */
  background: #f7fafd;
}
.task-panel .case-card {
  margin: 1.3em 0;
}
</style>


<script>
document.addEventListener('DOMContentLoaded', function() {
  // 卡片展开/收起
  document.querySelectorAll('.case-card').forEach(function(card) {
    card.addEventListener('click', function(e) {
      card.classList.toggle('expanded');
    });
  });
  // 筛选功能（针对每个任务单独的 select）
  const tasks = ['long-scenario','long-story'];
  tasks.forEach(task=>{
    ['userid','source'].forEach(f=>{
      const el = document.getElementById('filter-'+task+'-'+f);
      if(el) el.addEventListener('change', ()=>filterCases(task));
    });
  });
  function filterCases(task) {
    let cond = {};
    ['userid','source'].forEach(f=>{
      cond[f] = document.getElementById('filter-'+task+'-'+f).value;
    });
    document.querySelectorAll('.case-card[data-task="'+task+'"]').forEach(function(card) {
      let ok = true;
      ['userid','source'].forEach(f=>{
        if(cond[f] !== '' && card.dataset[f] !== cond[f]) ok = false;
      });
      card.style.display = ok ? '' : 'none';
    });
  }
});
</script>

<!-- ========== Long Scenario Task ========== -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#51a885;text-align:center;">Task: Long Scenario</h2>
    <div class="filter-bar">
      <label for="filter-long-scenario-userid">UserID:</label>
      <select name="userid" id="filter-long-scenario-userid">
        <option value="">All</option>
        {% assign cases = site.data.scenario_cases | where: "task_name", "long scenario" %}
        {% assign userids = cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-long-scenario-source">Source:</label>
      <select name="source" id="filter-long-scenario-source">
        <option value="">All</option>
        {% assign sources = cases | map: 'source' | uniq | sort %}
        {% for s in sources %}
          <option value="{{ s }}">{{ s }}</option>
        {% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in cases %}
        <div class="case-card"
             data-userid="{{ case.userid }}"
             data-source="{{ case.source }}"
             data-task="long-scenario">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% if case.task_specific.characters %}
              <div style="margin-bottom:1em;">
                <b style="color:#51a885; font-weight:600;">Characters:</b>
                <ul style="padding-left:0;">
                  {% for character in case.task_specific.characters %}
                    <li style="list-style:none;padding:0 0 0.6em 0;">
                      {% if character.name %}
                        <span style="font-weight:600; color:#111;">{{ character.name }}</span>
                        {% if character.role %}<span style="color:#31425c;"> ({{ character.role }})</span>{% endif %}
                      {% else %}
                        <span style="color:#31425c;">{{ character }}</span>
                      {% endif %}
                    </li>
                  {% endfor %}
                </ul>
              </div>
            {% endif %}
            {% if case.task_specific.summary %}
              <div style="margin-bottom:.7em;">
                <b style="color:#51a885; font-weight:600;">Summary:</b>
                <span>{{ case.task_specific.summary }}</span>
              </div>
            {% endif %}
            {% if case.task_specific.scenario %}
              <div style="margin-bottom:.7em;">
                <b style="color:#51a885; font-weight:600;">Scenario:</b>
                <span>{{ case.task_specific.scenario | markdownify | replace: '<p>', '' | replace: '</p>', '' }}</span>
              </div>
            {% endif %}
            {% if case.task_specific['raw text'] %}
              <div style="margin-bottom:.7em;">
                <b style="color:#51a885; font-weight:600;">Raw Text:</b>
                <span>{{ case.task_specific['raw text'] }}</span>
              </div>
            {% endif %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>

<!-- ========== Long Story Task ========== -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#e58551;text-align:center;">Task: Long Story</h2>
    <div class="filter-bar">
      <label for="filter-long-story-userid">UserID:</label>
      <select name="userid" id="filter-long-story-userid">
        <option value="">All</option>
        {% assign cases = site.data.scenario_cases | where: "task_name", "long story" %}
        {% assign userids = cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-long-story-source">Source:</label>
      <select name="source" id="filter-long-story-source">
        <option value="">All</option>
        {% assign sources = cases | map: 'source' | uniq | sort %}
        {% for s in sources %}
          <option value="{{ s }}">{{ s }}</option>
        {% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in cases %}
        <div class="case-card"
             data-userid="{{ case.userid }}"
             data-source="{{ case.source }}"
             data-task="long-story">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% if case.task_specific.characters %}
              <div style="margin-bottom:1em;">
                <b style="color:#e58551; font-weight:600;">Characters:</b>
                <span style="color:#31425c;">{{ case.task_specific.characters }}</span>
              </div>
            {% endif %}
            {% if case.task_specific.background %}
              <div style="margin-bottom:.7em;">
                <b style="color:#e58551; font-weight:600;">Background:</b>
                <span>{{ case.task_specific.background }}</span>
              </div>
            {% endif %}
            {% if case.task_specific.plots %}
              <div style="margin-bottom:.7em;">
                <b style="color:#e58551; font-weight:600;">Plots:</b>
                <span>{{ case.task_specific.plots | markdownify | replace: '<p>', '' | replace: '</p>', '' }}</span>
              </div>
            {% endif %}
            {% if case.task_specific['raw text'] %}
              <div style="margin-bottom:.7em;">
                <b style="color:#e58551; font-weight:600;">Raw Text:</b>
                <span>{{ case.task_specific['raw text'] }}</span>
              </div>
            {% endif %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>