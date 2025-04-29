---
title: "Journal Articles"
layout: gridlay
sitemap: false
permalink: /publications/
---

<style>
@import url('https://fonts.googleapis.com/css2?family=Barlow:wght@300&display=swap');

/* Scrollbar styling */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
}

::-webkit-scrollbar-thumb {
  background: #ddd;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #ccc;
}

.flex-container {
  list-style-type: none !important;
  padding-left: 0 !important;
  position: relative;
}
.flex-container li {
  list-style-type: none !important;
  padding-right: 155px;
  margin-bottom: 20px;
  position: relative;
  min-height: 140px; /* Ensure minimum height for proper centering */
}
.well-abstract {
  text-align: justify;
  padding: 10px 0;
}
.paper-highlight {
  color: #4a90e2;
  font-style: italic;
  margin-top: 5px;
  margin-bottom: 5px;
  display: flex;
  align-items: baseline;
  gap: 5px;
}
.paper-highlight span {
  flex-shrink: 0;
}
.paper-highlight a {
  color: #4a90e2;
  text-decoration: underline;
}
.paper-title {
  font-size: 1.2em;
  font-family: "Avenir Next", "Avenir Next Light", "Avenir Next", "Avenir", "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-weight: 500;
  color: rgb(137, 54, 54);
}
.paper-authors, .paper-journal {
  font-family: 'Barlow', sans-serif;
  font-weight: 300;
}
.paper-journal {
  margin-bottom: 3px;
  display: inline-block;
}
.paper-thumbnail {
  position: absolute;
  right: 0;
  top: 0;
  margin-left: 15px;
  max-width: 150px;
  border-radius: 4px;
  height: 100%;
  display: flex;
  align-items: center;
}
.paper-thumbnail img {
  width: 100%;
  height: auto;
  display: block;
  object-fit: cover;
  max-height: 140px;
}
.year-header {
  font-family: 'Barlow', sans-serif;
  font-weight: 300;
  color: #999;
  font-size: 1.5em;
  margin-top: 30px;
  margin-bottom: 15px;
  padding-bottom: 5px;
  border-bottom: 2px solid #ddd;
  display: block;
}
.thesis-title {
  font-size: 1.2em;
  font-family: "Avenir Next", "Avenir Next Light", "Avenir Next", "Avenir", "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-weight: 500;
  color: rgb(137, 54, 54);
}
.thesis-details {
  font-family: 'Barlow', sans-serif;
  font-weight: 300;
}
.btn-doi, .btn-arxiv, .btn-pdf {
  padding: 5px 10px;
  border-radius: 3px;
  font-size: 0.9em;
  cursor: pointer;
  margin-right: 8px;
  border: 1px solid #000;
  line-height: 1.2;
  height: 28px;
  min-width: 70px;
  text-align: center;
}

/* Abstract button */
button.btn-doi[data-bs-toggle="collapse"] {
  background-color: #e0e0e0;
  color: #333;
}
button.btn-doi[data-bs-toggle="collapse"]:hover {
  background-color: #d0d0d0;
}

/* DOI button */
a .btn-doi {
  background-color: #4a90e2;
  color: white;
}
a .btn-doi:hover {
  background-color: #357abd;
}

.btn-arxiv {
  background-color: #b31b1b;
  color: white;
}
.btn-arxiv:hover {
  background-color: #8b1515;
}

.btn-pdf {
  background-color: #e74c3c;
  color: white;
}
.btn-pdf:hover {
  background-color: #c0392b;
}
</style>

# Journal Articles

{% assign sorted_publist = site.data.publist | sort: "date" | reverse %}
{% assign total_papers = sorted_publist | size %}
{% assign paper_number = total_papers %}

{% assign grouped_papers = sorted_publist | group_by: "year" | sort: "name" | reverse %}

{% for year_group in grouped_papers %}
  <p class="year-header">{{ year_group.name }}</p>
  {% for publi in year_group.items %}
  <div class="well-sm">
  <ul class="flex-container">
  <li class="flex-item2">
    {% if publi.thumbnail %}
    <div class="paper-thumbnail">
      <img src="{{ publi.thumbnail }}" alt="Paper thumbnail">
    </div>
    {% endif %}
    <strong class="paper-title">{{ paper_number }}. {{ publi.title }}</strong><br/>
    <span class="paper-authors">
    {% assign authors = publi.authors | split: " and " %}
    {% for author in authors %}
      {% assign name_parts = author | split: ", " %}
      {% if name_parts.size == 2 %}
        {{ name_parts[1] }} {{ name_parts[0] }}{% if forloop.last %}{% elsif forloop.rindex0 == 1 %}, and {% else %}, {% endif %}
      {% else %}
        {{ author }}{% if forloop.last %}{% elsif forloop.rindex0 == 1 %}, and {% else %}, {% endif %}
      {% endif %}
    {% endfor %}
    </span><br/>
    <span class="paper-journal"><em>{{ publi.display }}</em>, {{ publi.year }}</span><br/>
    {% if publi.highlight %}<div class="paper-highlight"><span>★</span> {{ publi.highlight | markdownify }}</div>{% endif %}
    {% if publi.abstract %}<button class="btn-doi" data-bs-toggle="collapse" data-bs-target="#{{publi.doi}}">ABSTRACT</button>{% endif %}
    {% if publi.arxiv %}<a href="https://arxiv.org/abs/{{ publi.arxiv }}" target="_blank"><button class="btn-arxiv">ARXIV</button></a>{% endif %}
    {% if publi.pdf %}<a href="{{ publi.pdf }}" target="_blank"><button class="btn-pdf">PDF</button></a>{% endif %}
    {% if publi.doi %}<a href="http://dx.doi.org/{{ publi.doi }}" target="_blank"><button class="btn-doi">DOI</button></a>{% endif %}
    {% if publi.abstract %}
    <div id="{{publi.doi}}" class="collapse">
      <div class="well-abstract">
        <p>{{publi.abstract}}</p>
      </div>
    </div>
    {% endif %}
  </li>
  </ul>
  </div>
  {% assign paper_number = paper_number | minus: 1 %}
  {% endfor %}
{% endfor %}

# Ph.D. Thesis

<div class="well-sm">
<ul class="flex-container">
<li class="flex-item2">
  <strong class="thesis-title">Problems on viscous dynamics of passive and active microfilaments</strong><br/>
  <span class="thesis-details">Brato Chakrabarti<br/>
  University of California, San Diego, 2019</span><br/>
  <a href="/papers/Thesis.pdf" target="_blank"><button class="btn-pdf">PDF</button></a>
</li>
</ul>
</div>

