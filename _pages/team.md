---
title: "Team"
layout: gridlay
sitemap: false
permalink: /team/
---


<h2 style="color:rgb(137, 54, 54); font-family: 'Avenir'">Principal Investigator</h2>

{% for member in site.data.pi %}



<div class="jumbotron" style="max-width: 1000px; margin: 5 auto; padding: 0px 10px;">
<div class="row">
<div class="col-sm-2">
  <img src="{{ site.url }}{{ site.baseurl }}/images/teampic/{{ member.photo }}" width="120%" style="max-width:300px; margin-left:0px"/>
</div>
<div class="col-sm-9 col-xs-15">
<h3 style="font-family: 'Avenir', sans-serif; margin-left: 0.6em;">{{ member.name }}</h3>
<p style="
      font-family:  'Avenir Light', sans-serif;
      font-size:    1.05em;
      line-height:  1.5;        /* tighter lines */
      margin-bottom: -1em;       /* space after the paragraph */
      margin-left:    1em;       /* space after the paragraph */
      margin-right:  -2em;       /* space after the paragraph */
      text-align: justify;       /* justify text */
    ">
    {{ member.info | newline_to_br}}
  </p>


<div style="margin-top: 25px; margin-left: 1em;">
  {% if member.scholar %} <a href="{{ member.scholar }}"      target="_blank" style="margin-right: 15px;"><i class="ai ai-google-scholar ai-lg"></i></a> {% endif %}
  {% if member.cv %}      <a href="{{ member.cv }}"           target="_blank" style="margin-right: 15px;"><i class="ai ai-cv ai-lg"></i></a> {% endif %}
  <!-- {% if member.email %}   <span style="margin-right: 15px;" title="Email"><i class="far fa-envelope fa-lg"></i> {{ member.email }}</span> {% endif %} -->
</div>

<ul style="overflow: hidden">
</ul>
</div>
</div>
</div>

{% endfor %}

<h2 style="color:rgb(137, 54, 54); font-family: 'Avenir'">Current Members</h2>

<div class="jumbotron" style="margin: 0 auto; padding: 0px 10px;">
{% assign number_printed = 0 %}
{% for member in site.data.phd_students %}

{% assign even_odd = number_printed | modulo: 2 %}

{% if even_odd == 0 %}

<div class="row">
{% endif %}

<div class="col-sm-2">
  <img src="{{ site.url }}{{ site.baseurl }}/images/teampic/{{ member.photo }}" width="105%" style="max-width:300px; margin-left:-2px"/>
</div>
<div class="col-sm-4 col-xs-12">
<h3 style="font-family: 'Avenir', sans-serif; margin-left: -1em; font-size: 1.2em;">{{ member.name }}</h3>
<p style="
      font-family:  'Avenir Light', sans-serif;
      font-size:    1.05em;
      line-height:  1.2;        /* slightly increased line height */
      margin-top:   -0.2em;       
      margin-left:  -1em;
    ">
    {{ member.info | newline_to_br}}
  </p>
  <p style="
      font-family:  'Courier New', sans-serif;
      font-size:     0.9em;
      line-height:  1.2;        /* slightly increased line height */
      margin-top:   -0.8em;       
      margin-left:  -1em;
      margin-bottom:-0.1em;
    ">
    {% if member.email %}  <span style="margin-right: -5px; margin-left: 0em;" title="Email"><i class="far fa-envelope fa-ls" style="vertical-align: middle;"></i> <span style="font-size: 0.95em; vertical-align: middle;">{{ member.email }}</span></span> {% endif %}
  </p>
  <p style="
      font-family:  'Avenir Light', sans-serif;
      font-size:     0.9em;
      line-height:  1.2;        /* slightly increased line height */
      margin-top:   0.5em;       
      margin-left:  -1em;
      margin-bottom:-0.1em;
    ">
    {{ member.advisor | newline_to_br}}
  </p>



<!-- {% if member.email %}<a href="mailto:{{ member.email }}" target="_blank"><i class="fa fa-envelope-square fa-2x"></i></a> {% endif %}
{% if member.email %}  <span style="margin-right: -5px; margin-left: -1em; margin-top: -1em;" title="Email"><i class="far fa-envelope fa-xs" style="vertical-align: middle;"></i> <span style="font-size: 0.7em; vertical-align: middle;">{{ member.email }}</span></span> {% endif %} -->

</div>
<!-- </div> -->

{% assign number_printed = number_printed | plus: 1 %}

{% if even_odd == 1 %}

</div>
{% endif %}

{% endfor %}

{% assign even_odd = number_printed | modulo: 2 %}
{% if even_odd == 1 %}

</div>
{% endif %}
</div>

<h2 style="color:rgb(137, 54, 54); font-family: 'Avenir'">Visiting/rotation students</h2>

<div class="jumbotron" style="margin: 3 auto; padding: 1px 10px;">
<ol style="list-style-type: decimal; padding-left: 20px;">
{% for member in site.data.visiting %}
  <li style="font-family: 'Avenir Light', sans-serif; font-size: 1.1em; margin: 0.5em 0;">{{ member.name }}</li>
{% endfor %}
</ol>
</div>