---
layout: page
title: About me
permalink: /about/
---
<div>
<p>My name is Borja Garcia de Vinuesa Ordovás.</p>
</div>
<p> <span style="font-size: 24px;">1985</span> Born.</p>
<div style="padding-left: 10px;">
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">12</strong></span> had my first drumkit (thanks mom).</p>
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">15</strong></span> spent one year in Boston, Massachussets. Got obsessed with music. Metal and Hardcore.      </p>
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">18</strong></span> graduated from highschool. Started school (Telecom Engineering).      </p>
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">20</strong></span> I droped-off from school and tried to become an actor. Got some secundary roles. I continued with my music too.      </p>
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">23</strong>,</span> while acting, I worked in the film industry. Work was sometimes too boring. I needed brain food. I decided to go back to school and finish.      </p>
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">27</strong></span> graduated from school while working in a telecom company as a VoIP engineer.      </p>
<p>at <span style="font-size: 18px"><strong style="color: #4f4f4f;">29</strong></span> decided to follow my entrepreneurial side. 3-month bootcamp with Rails at <a href="https://www.ironhack.com/">Ironhack</a> (second cohort at Ironhack). Gemssy Technologies is born (<a href="https://heepsy.com">heepsy.com</a> / <a href="https://www.moonio.io">moonio.io</a>)</p>
<p>at <span style="font-size: 18px"><strong>34</strong></span> left the company. Took some time off.</p>
</div>

{% assign current_date = 'now' | date: "%Y-%m-%d" | split: "-" %}
{% assign birth_date = "1985-01-10" | split: "-" %}

{% assign current_year = current_date[0] | plus: 0 %}
{% assign current_month = current_date[1] | plus: 0 %}
{% assign current_day = current_date[2] | plus: 0 %}
{% assign birth_year = birth_date[0] | plus: 0 %}
{% assign birth_month = birth_date[1] | plus: 0 %}
{% assign birth_day = birth_date[2] | plus: 0 %}

{% assign age_years = current_year | minus: birth_year %}
{% assign age_months = current_month | minus: birth_month %}

{% if age_months < 0 %}
  {% assign age_years = age_years | minus: 1 %}
  {% assign age_months = 12 | plus: age_months %}
{% endif %}


<p style="margin-bottom: 0px;"> <span style="font-size: 24px;">20{{ site.time | date: '%y' }}</span> I'm <strong>{{ age_years }} </strong> now.</p>
<p style="padding-left: 30px;">Working as consultant/part-time senior engineer since then while doing projects on the side. Now <a href="https://srefs.co">srefs.co</a>.