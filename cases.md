---
layout: page
title: "Cases"
permalink: /cases/
---
{% include password.html %}

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

</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  // 卡片展开/收起
  document.querySelectorAll('.case-card').forEach(function(card) {
    card.addEventListener('click', function(e) {
      card.classList.toggle('expanded');
    });
  });
  // 筛选功能
  const filterFields = ['source','tag','task_name'];
  filterFields.forEach(f=>{
    document.getElementById('filter-'+f).addEventListener('change',filterCases);
  });
  function filterCases() {
    let cond = {};
    filterFields.forEach(f=>{
      cond[f] = document.getElementById('filter-'+f).value;
    });
    document.querySelectorAll('.case-card').forEach(function(card) {
      let ok = true;
      filterFields.forEach(f=>{
        if(cond[f] !== '' && card.dataset[f] !== cond[f]) ok = false;
      });
      card.style.display = ok ? '' : 'none';
    });
  }
});
</script>

<div class="filter-bar">
  <label for="filter-tag">Tag:</label>
  <select id="filter-tag">
    <option value="">All</option>
    <option value="Users">Users</option>
    <option value="Scenarios">Scenarios</option>
    <option value="Behaviors">Behaviors</option>
  </select>
  <label for="filter-source">Source:</label>
  <select id="filter-source">
    <option value="">All</option>
    <option value="Reddit">Reddit</option>
    <option value="Amazon">Amazon</option>
    <option value="Twitter">Twitter</option>
    <option value="Blogauthorship">Blogauthorship</option>
  </select>
  <label for="filter-task_name">Task:</label>
  <select id="filter-task_name">
    <option value="">All</option>
    <option value="Persona">Persona</option>
    <option value="Profile">Profile</option>
    <option value="Life Stories">Life Stories</option>
    <option value="Life Journeys">Life Journeys</option>
    <option value="Long Scenario">Long Scenario</option>
    <option value="Long Story">Long Story</option>
    <option value="Long Story Focus on Behavior">Long Story Focus on Behavior</option>
    <option value="Social QA single">Social QA single</option>
    <option value="Social QA">Social QA</option>
    <option value="Item Selection">Item Selection</option>
    <option value="Writing Imitation">Writing Imitation</option>
    <option value="Personalized Comment">Personalized Comment</option>
  </select>
</div>


{% assign list_fields = "diary_entries,characters" | split: "," %}
{% for case in site.data.cases %}
  <div class="case-card"
       data-tag="{{ case.tag }}"
       data-source="{{ case.source }}"
       data-task_name="{{ case.task_name }}">
    <div class="case-summary" style="font-family:'Segoe UI', 'Roboto', 'Helvetica Neue', Arial, 'PingFang SC', 'Microsoft YaHei', sans-serif;">
      <div><b>UserID:</b> {{ case.userid }}</div>
      <div><b>Tag:</b> {{ case.tag }}</div>
      <div><b>Source:</b> {{ case.source }}</div>
      <div><b>Task:</b> {{ case.task_name }}</div>
    </div>
    <span class="case-expand-tip">点击展开/收起</span>
    <div class="case-details" style="font-family:'Segoe UI', 'Roboto', 'Helvetica Neue', Arial, 'PingFang SC', 'Microsoft YaHei', sans-serif; font-size:1.08em; color:#31425c; line-height:1.8;">
      {% if case.task_specific %}
        {% for list_field in list_fields %}
          {% if case.task_specific[list_field] %}
            <div style="margin-bottom:1.2em;">
              <b style="color:#4973db; font-weight:600;">{{ list_field | capitalize }}:</b>
              <ul style="padding-left:0;">
                {% for entry in case.task_specific[list_field] %}
                  <li style="margin-bottom:1.1em;list-style:none;padding:0;">
                    <div style="border:1px solid #eee; border-radius:0.6em; background:#f8fafc; box-shadow:0 1px 4px #eef2f7; padding:0.7em 1em;">
                      {% if entry.summary %}
                        <div style="font-weight:600; color:#111;">{{ entry.summary }}</div>
                      {% endif %}
                      {% if entry.name %}
                        <div style="font-weight:600; color:#111;">{{ entry.name }}</div>
                      {% endif %}
                      {% if entry.content %}
                        <div style="font-size:0.97em; color:#31425c;">{{ entry.content }}</div>
                      {% endif %}
                      {% if entry.role %}
                        <div style="color:#357;">Role: {{ entry.role }}</div>
                      {% endif %}
                    </div>
                  </li>
                {% endfor %}
              </ul>
            </div>
          {% endif %}
        {% endfor %}
        <!-- 展示其他普通字段 -->
        {% for field in case.task_specific %}
          {% unless list_fields contains field[0] %}
            <div style="margin:0.3em 0;">
              <b style="color:#4973db; font-weight:600;">{{ field[0] | capitalize }}:</b>
              <span style="font-weight:400; color:#31425c;">{{ field[1] }}</span>
            </div>
          {% endunless %}
        {% endfor %}
      {% endif %}
    </div>
  </div>
{% endfor %}

