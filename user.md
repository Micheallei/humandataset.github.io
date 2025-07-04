---
layout: page
title: "User"
permalink: /user/
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
  document.querySelectorAll('.task-block').forEach(function(block) {
    // 卡片展开/收起
    block.querySelectorAll('.case-card').forEach(function(card) {
      card.addEventListener('click', function(e) {
        card.classList.toggle('expanded');
      });
    });
    // 筛选
    let selects = block.querySelectorAll('.filter-bar select');
    selects.forEach(sel => sel.addEventListener('change', filterCases));
    function filterCases() {
      let cond = {};
      selects.forEach(sel=>{
        cond[sel.name] = sel.value;
      });
      block.querySelectorAll('.case-card').forEach(function(card) {
        let ok = true;
        for (let key in cond) {
          if (cond[key] !== '' && card.dataset[key] !== cond[key]) ok = false;
        }
        card.style.display = ok ? '' : 'none';
      });
    }
  });
});
</script>


<!-- ========== Persona Task ========== -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#51a885;text-align:center;">Task: Persona</h2>
    <div class="filter-bar">
      <label for="filter-persona-userid">UserID:</label>
      <select name="userid" id="filter-persona-userid">
        <option value="">All</option>
        {% assign persona_cases = site.data.user_cases | where: "task_name", "persona" %}
        {% assign userids = persona_cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-persona-source">Source:</label>
      <select name="source" id="filter-persona-source">
        <option value="">All</option>
        {% assign sources = persona_cases | map: 'source' | uniq | sort %}
        {% for s in sources %}
          <option value="{{ s }}">{{ s }}</option>
        {% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in persona_cases %}
        <div class="case-card"
             data-userid="{{ case.userid }}"
             data-source="{{ case.source }}">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% if case.task_specific.persona %}
              <div style="margin:0.3em 0;">
                <b style="color:#51a885; font-weight:600;">Persona:</b>
                <span style="font-weight:400; color:#31425c;">{{ case.task_specific.persona }}</span>
              </div>
            {% endif %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>


<!-- ========== Profile Task ========== -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#4973db;text-align:center;">Task: Profile</h2>
    <div class="filter-bar">
      <label for="filter-profile-userid">UserID:</label>
      <select name="userid" id="filter-profile-userid">
        <option value="">All</option>
        {% assign profile_cases = site.data.user_cases | where: "task_name", "profile" %}
        {% assign userids = profile_cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-profile-source">Source:</label>
      <select name="source" id="filter-profile-source">
        <option value="">All</option>
        {% assign sources = profile_cases | map: 'source' | uniq | sort %}
        {% for s in sources %}
          <option value="{{ s }}">{{ s }}</option>
        {% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in profile_cases %}
        <div class="case-card"
             data-userid="{{ case.userid }}"
             data-source="{{ case.source }}">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% if case.task_specific.profile %}
              <div style="margin:0.3em 0;">
                <b style="color:#4973db; font-weight:600;">Profile:</b>
                <span style="font-weight:400; color:#31425c;">{{ case.task_specific.profile }}</span>
              </div>
            {% endif %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>


<!-- ========== Life Stories Task ========== -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#e58551;text-align:center;">Task: Life Stories</h2>
    <div class="filter-bar">
      <label for="filter-life-stories-userid">UserID:</label>
      <select name="userid" id="filter-life-stories-userid">
        <option value="">All</option>
        {% assign life_stories_cases = site.data.user_cases | where: "task_name", "life stories" %}
        {% assign userids = life_stories_cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-life-stories-source">Source:</label>
      <select name="source" id="filter-life-stories-source">
        <option value="">All</option>
        {% assign sources = life_stories_cases | map: 'source' | uniq | sort %}
        {% for s in sources %}
          <option value="{{ s }}">{{ s }}</option>
        {% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in life_stories_cases %}
        <div class="case-card"
             data-userid="{{ case.userid }}"
             data-source="{{ case.source }}">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% if case.task_specific.diary_entries %}
              <div style="margin-bottom:1.2em;">
                <b style="color:#e58551; font-weight:600;">Life Stories:</b>
                <ul style="padding-left:0;">
                  {% for entry in case.task_specific.diary_entries %}
                    <li style="margin-bottom:1.1em;list-style:none;padding:0;">
                      <div style="border:1px solid #eee; border-radius:0.6em; background:#f8fafc; box-shadow:0 1px 4px #eef2f7; padding:0.7em 1em;">
                        {% if entry.summary %}
                          <div style="font-weight:600; color:#111;">{{ entry.summary }}</div>
                        {% endif %}
                        {% if entry.content %}
                          <div style="font-size:0.97em; color:#31425c;">{{ entry.content }}</div>
                        {% endif %}
                      </div>
                    </li>
                  {% endfor %}
                </ul>
              </div>
            {% endif %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>

<!-- ========== Life Journeys Task ==========
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#a07ee5;text-align:center;">Task: Life Journeys</h2>
    <div class="filter-bar">
      <label for="filter-life-journeys-userid">UserID:</label>
      <select name="userid" id="filter-life-journeys-userid">
        <option value="">All</option>
        {% assign life_journeys_cases = site.data.user_cases | where: "task_name", "life journeys" %}
        {% assign userids = life_journeys_cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-life-journeys-source">Source:</label>
      <select name="source" id="filter-life-journeys-source">
        <option value="">All</option>
        {% assign sources = life_journeys_cases | map: 'source' | uniq | sort %}
        {% for s in sources %}
          <option value="{{ s }}">{{ s }}</option>
        {% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in life_journeys_cases %}
        <div class="case-card"
             data-userid="{{ case.userid }}"
             data-source="{{ case.source }}">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% if case.task_specific.diary_entries %}
              <div style="margin-bottom:1.2em;">
                <b style="color:#a07ee5; font-weight:600;">Life Journeys:</b>
                <ul style="padding-left:0;">
                  {% for entry in case.task_specific.diary_entries %}
                    <li style="margin-bottom:1.1em;list-style:none;padding:0;">
                      <div style="border:1px solid #eee; border-radius:0.6em; background:#f8fafc; box-shadow:0 1px 4px #eef2f7; padding:0.7em 1em;">
                        {% if entry.summary %}
                          <div style="font-weight:600; color:#111;">{{ entry.summary }}</div>
                        {% endif %}
                        {% if entry.content %}
                          <div style="font-size:0.97em; color:#31425c;">{{ entry.content }}</div>
                        {% endif %}
                      </div>
                    </li>
                  {% endfor %}
                </ul>
              </div>
            {% endif %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div> -->
