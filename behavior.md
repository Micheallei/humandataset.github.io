---
layout: page
title: "Behavior"
permalink: /behavior/
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
  // 多任务筛选
  [
    {prefix:'behavior', task:'social qa'},
    {prefix:'writing', task:'writing imitation'},
    {prefix:'comment', task:'personalized comments'},
    {prefix:'item', task:'item selection'}
  ].forEach(({prefix})=>{
    ['userid','source'].forEach(f=>{
      const el = document.getElementById('filter-'+prefix+'-'+f);
      if(el) el.addEventListener('change', ()=>filterCases(prefix));
    });
  });
  function filterCases(prefix) {
    let cond = {};
    ['userid','source'].forEach(f=>{
      cond[f] = document.getElementById('filter-'+prefix+'-'+f).value;
    });
    document.querySelectorAll('.case-card[data-prefix="'+prefix+'"]').forEach(function(card) {
      let ok = true;
      ['userid','source'].forEach(f=>{
        if(cond[f] !== '' && card.dataset[f] !== cond[f]) ok = false;
      });
      card.style.display = ok ? '' : 'none';
    });
  }
  // 卡片展开/收起
  document.querySelectorAll('.case-card').forEach(function(card) {
    card.addEventListener('click', function(e) {
      card.classList.toggle('expanded');
    });
  });
});
</script>

<!-- ========== Social QA Task ========== -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#e58551;text-align:center;">Task: Social QA</h2>
    {% assign maincolor = "#e58551" %}
    <div class="filter-bar">
      <label for="filter-behavior-userid">UserID:</label>
      <select name="userid" id="filter-behavior-userid">
        <option value="">All</option>
        {% assign cases = site.data.behavior_cases | where: "task_name", "social qa" %}
        {% assign userids = cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}
          <option value="{{ id }}">{{ id }}</option>
        {% endfor %}
      </select>
      <label for="filter-behavior-source">Source:</label>
      <select name="source" id="filter-behavior-source">
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
             data-source="{{ case.source }}">
          <div class="case-summary">
            <div><b>UserID:</b> {{ case.userid }}</div>
            <div><b>Source:</b> {{ case.source }}</div>
          </div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% for pair in case.task_specific %}
              <div style="margin:0.3em 0;">
                <b style="color:{{ maincolor }}; font-weight:600;">{{ pair[0] | replace: '_', ' ' | capitalize }}:</b>
                <span style="{% if pair[0]=='question' %}font-weight:600; color:#111;{% else %}font-size:0.97em; color:#31425c;{% endif %}">{{ pair[1] | newline_to_br }}</span>
              </div>
            {% endfor %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>

<!-- Writing Imitation Task -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#339af0;text-align:center;">Task: Writing Imitation</h2>
    {% assign maincolor = "#339af0" %}
    <div class="filter-bar">
      <label for="filter-writing-userid">UserID:</label>
      <select name="userid" id="filter-writing-userid">
        <option value="">All</option>
        {% assign cases = site.data.behavior_cases | where: "task_name", "writing imitation" %}
        {% assign userids = cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}<option value="{{ id }}">{{ id }}</option>{% endfor %}
      </select>
      <label for="filter-writing-source">Source:</label>
      <select name="source" id="filter-writing-source">
        <option value="">All</option>
        {% assign sources = cases | map: 'source' | uniq | sort %}
        {% for s in sources %}<option value="{{ s }}">{{ s }}</option>{% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in cases %}
        <div class="case-card" data-prefix="writing" data-userid="{{ case.userid }}" data-source="{{ case.source }}">
          <div class="case-summary"><div><b>UserID:</b> {{ case.userid }}</div><div><b>Source:</b> {{ case.source }}</div></div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% for pair in case.task_specific %}
              <div style="margin:0.3em 0;">
                <b style="color:{{ maincolor }};font-weight:600;">{{ pair[0] | replace: '_', ' ' | capitalize }}:</b>
                <span style="{% if pair[0]=='question' %}font-weight:600;color:#111;{% else %}font-size:0.97em;color:#31425c;{% endif %}">{{ pair[1] | newline_to_br }}</span>
              </div>
            {% endfor %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>

<!-- Personalized Comments Task -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#16a085;text-align:center;">Task: Personalized Comments</h2>
    {% assign maincolor = "#16a085" %}
    <div class="filter-bar">
      <label for="filter-comment-userid">UserID:</label>
      <select name="userid" id="filter-comment-userid">
        <option value="">All</option>
        {% assign cases = site.data.behavior_cases | where: "task_name", "personalized comments" %}
        {% assign userids = cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}<option value="{{ id }}">{{ id }}</option>{% endfor %}
      </select>
      <label for="filter-comment-source">Source:</label>
      <select name="source" id="filter-comment-source">
        <option value="">All</option>
        {% assign sources = cases | map: 'source' | uniq | sort %}
        {% for s in sources %}<option value="{{ s }}">{{ s }}</option>{% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in cases %}
        <div class="case-card" data-prefix="comment" data-userid="{{ case.userid }}" data-source="{{ case.source }}">
          <div class="case-summary"><div><b>UserID:</b> {{ case.userid }}</div><div><b>Source:</b> {{ case.source }}</div></div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% for pair in case.task_specific %}
              <div style="margin:0.3em 0;">
                <b style="color:{{ maincolor }};font-weight:600;">{{ pair[0] | replace: '_', ' ' | capitalize }}:</b>
                <span style="{% if pair[0]=='question' %}font-weight:600;color:#111;{% else %}font-size:0.97em;color:#31425c;{% endif %}">{{ pair[1] | newline_to_br }}</span>
              </div>
            {% endfor %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>

<!-- Item Selection Task -->
<div class="task-block">
  <div class="task-panel">
    <h2 style="color:#f368e0;text-align:center;">Task: Item Selection</h2>
    {% assign maincolor = "#f368e0" %}
    <div class="filter-bar">
      <label for="filter-item-userid">UserID:</label>
      <select name="userid" id="filter-item-userid">
        <option value="">All</option>
        {% assign cases = site.data.behavior_cases | where: "task_name", "item selection" %}
        {% assign userids = cases | map: 'userid' | uniq | sort %}
        {% for id in userids %}<option value="{{ id }}">{{ id }}</option>{% endfor %}
      </select>
      <label for="filter-item-source">Source:</label>
      <select name="source" id="filter-item-source">
        <option value="">All</option>
        {% assign sources = cases | map: 'source' | uniq | sort %}
        {% for s in sources %}<option value="{{ s }}">{{ s }}</option>{% endfor %}
      </select>
    </div>
    <div class="case-list-scroll">
      {% for case in cases %}
        <div class="case-card" data-prefix="item" data-userid="{{ case.userid }}" data-source="{{ case.source }}">
          <div class="case-summary"><div><b>UserID:</b> {{ case.userid }}</div><div><b>Source:</b> {{ case.source }}</div></div>
          <span class="case-expand-tip">expand/collapse</span>
          <div class="case-details">
            {% for pair in case.task_specific %}
              <div style="margin:0.3em 0;">
                <b style="color:{{ maincolor }};font-weight:600;">{{ pair[0] | replace: '_', ' ' | capitalize }}:</b>
                <span style="{% if pair[0]=='question' %}font-weight:600;color:#111;{% else %}font-size:0.97em;color:#31425c;{% endif %}">{{ pair[1] | newline_to_br }}</span>
              </div>
            {% endfor %}
          </div>
        </div>
      {% endfor %}
    </div>
  </div>
</div>