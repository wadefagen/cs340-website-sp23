---
layout: bare
title: "Index"
custom_css:
 - index
---

<div class="stat107-bg" id="particles">
  <div class="container">
    <div class="stat107-homepage-block">
      <h1>CS 340: Introduction to Computer Systems</h1>
      <p class="lead"><b>Spring 2023</b>, University of Illinois</p>
      {% include nav.html %}
    </div>
  </div>
</div>



{% assign assignments = site.homeworks | concat: site.prelecture | concat: site.lecture | concat: site.mp | concat: site.project %}

{% assign now = 'now' | date: '%s' %}
{% assign upcoming = assignments | where_exp: "item", "item.due > site.time"  %}
{% assign upcoming = upcoming | sort: 'due'  %}


{% assign assignments = site.data.lecture.lectures | concat: site.mp | concat: site.project %}
{% assign assignments = site.lecture | concat: site.mp %}

{% assign lectures = site.data.lecture.lectures | where_exp: "lecture", "lecture.visible == true" %}

{% assign entries = assignments | concat: lectures | concat: site.news  %}
{% assign entries = entries | sort: 'date' | reverse %}



<main role="main" class="container">
<!--
<div class="card mb-4 box-shadow mx-5" style="box-shadow:rgba(255, 247, 0, 0.4) 0px 0px 30px 0px;">
  <div class="card-body">
    <div class="row">
      <div class="col-md-8">
        <h3>Final Project: Illinois Infinite Maze</h3>
        <p class="mb-0">
          The final project in CS 240 this semester was the creation of the <b>Illinois Infinite Maze</b>!  After four weeks of design, implementation, integration, and deployment, 91 students simultaneously managed 91 separate VMs to launch a 218 individual processes that each acted as an independent microservice that could generate a maze segment on request.
        </p>
        <ul style="margin: 0px">
          <li><a href="https://waf.cs.illinois.edu/pages/cs340/infinite-maze/">View a detailed writeup of the infinite maze project</a>.</li>
          <li><a href="https://waf.cs.illinois.edu/pages/cs340/infinite-maze/maze.html?all=true">Explore the final infinite maze yourself</a>!</li>
        </ul>
      </div>
      <div class="col-md-4">      
        <a href="https://waf.cs.illinois.edu/pages/cs340/infinite-maze/">
          <img src="/cs240/sp2022/static/maze.png" class="img-fluid" style="border: solid 1px #999;">
        </a>
      </div>
    </div>
  </div>
</div>
<hr>
-->

<div class="row">
  <div class="col-md-4">
    <div class="card mb-4 box-shadow">
      <div class="card-body">
        <h3>Upcoming Deadlines</h3>
        {% for entry in upcoming %}
          <hr>
          <a href="{{ site.baseurl }}{{ entry.url }}">
          {% if entry.collection == "homeworks" %}
          <b>Homework {{entry.number}}: {{entry.title}}</b>
          {% elsif entry.collection == "mp" and entry.ec == true %}
          <b>Extra Credit: {{entry.title}}</b>
          {% elsif entry.collection == "mp" %}
          <b>MP {{entry.number}}: {{entry.title}}</b>
          {% elsif entry.collection == "labs" %}
          <b>Lab: {{entry.title}}</b>
          {% elsif entry.collection == "project" %}
          <b>{{entry.title}}</b>
          {% elsif entry.collection == "ec" %}
          <b>Extra Credit: {{entry.title}}</b>
          {% elsif entry.collection == "prelecture" %}
          <b>Pre-Lecture: {{entry.title}}</b>
          {% endif %}
          </a>
          <ul>
            <li>Due: {{ entry.due | date: "%B %e, %Y" }}</li>
          </ul>
        {% endfor %}
        <hr>
        <b>Welcome to CS 340!</b>
      </div>
    </div>
  </div>

  {% assign now = 'now' | date: '%s' %}
  {% for entry in entries %}
  {% comment %}
  {% assign entrydate = entry.date | date: '%s' %}
  {% if entrydate > now  %}
    {% continue %}
  {% endif %}
  {% endcomment %}

  {% assign collection = entry.collection | default: "Lecture" %}
  {% if collection == "Lecture" and entry.holiday == true %}
    {% continue %}
  {% endif %}

  {% if collection == "mp" and entry.ec == true %}
    {% continue %}
  {% endif %}

  <div class="col-md-4">
    <div class="card mb-4 box-shadow">
      {% if entry.image %}
      <img class="card-img-top" src="{{ site.baseurl }}/{{ entry.image }}" alt="{{ entry.title }}">
      {% else %}
        {% if entry.collection == "labs" %}
        <img class="card-img-top" src="{{ site.baseurl }}/static/stock-photos/lab-default.jpg" alt="{{ entry.title }}">
        {% else %}
        <img class="card-img-top" src="{{ site.baseurl }}/static/stock-photos/lecture1.jpg" alt="{{ entry.title }}">
        {% endif %}
      {% endif %}
      <div class="card-body">
        {% assign collection = entry.collection | default: "default-lecture" %}
        {% if collection == "news" %}
        <h3>{{entry.title}}</h3>
        {% elsif collection == "homeworks" %}
        <h3>Homework {{entry.number}}: {{entry.title}}</h3>
        {% elsif collection == "mp" %}
        <h3>MP {{entry.number}}: {{entry.title}}</h3>
        {% elsif collection == "labs" %}
        <h3><code>{{entry.repo}}</code>: {{entry.title}}</h3>
        {% elsif collection == "lecture" %}
        <h3>Week {{entry.number}} Available: {{entry.title}}</h3>
        {% elsif collection == "default-lecture" %}
        <h3>Lecture #{{entry.number}}: {{entry.title}}</h3>
        {% else %}
        <h3>{{ entry.collection | default: "Lecture" | capitalize }}: {{entry.title}}</h3>
        {% endif %}
        <p class="card-text">
          {% if entry.description %}
          <i>{{ entry.description }}</i>
          {% endif %}
          {% if entry.collection == "news" %}
          {{ entry }}
          {% endif %}
          {% if entry.links %}
          <ul>
            {% for link in entry.links %}
            <li>
                {% if link.first[1] != "#" %}
                <a href="{{ link.first[1] | absolute_url }}">{{ link.first[0] }}</a>
                {% else %}
                <i>{{ link.first[0] }}</i>
                {% endif %}
            </li>
            {% endfor %}
          </ul>
          {% endif %}
          {% if entry.readings %}
          <h6>Additional Readings</h6>
          <ul>
            {% for link in entry.readings %}
            <li>
                {% if link.first[1] != "#" %}
                <a href="{{ link.first[1] | absolute_url }}">{{ link.first[0] }}</a>
                {% else %}
                <i>{{ link.first[0] }}</i>
                {% endif %}
            </li>
            {% endfor %}
          </ul>
          {% endif %}
          {% if entry.assignments %}
          <h6>Assignments:</h6>
          <ul>
            {% for link in entry.assignments %}
            <li>
                {% if link.first[1] != "#" %}
                <a href="{{ link.first[1] | absolute_url }}">{{ link.first[0] }}</a>
                {% else %}
                <i>{{ link.first[0] }}</i>
                {% endif %}
            </li>
            {% endfor %}
          </ul>
          {% endif %}
        </p>
        <div class="d-flex justify-content-between align-items-center">
          <small class="text-muted">{{ entry.date | date: "%B %e, %Y" }}</small>
          <div class="btn-group">
            {% if entry.collection == "labs" %}
            <a href="{{ site.baseurl }}{{ entry.url }}">
            <button type="button" class="btn btn-sm btn-outline-secondary">Go to Lab {{entry.short | default: entry.title}}</button>
            </a>
            {% endif %}
            {% if entry.collection == "lecture" %}
            <a href="{{ site.baseurl }}{{ entry.url }}">
            <button type="button" class="btn btn-sm btn-outline-secondary">Go to Week {{entry.number}}</button>
            </a>
            {% endif %}
            {% if entry.collection == "projects" %}
            <a href="{{ site.baseurl }}{{ entry.url }}">
            <button type="button" class="btn btn-sm btn-outline-secondary">View Project</button>
            </a>
            {% endif %}
            {% if entry.collection == "mp" %}
            <a href="{{ site.baseurl }}{{ entry.url }}">
            <button type="button" class="btn btn-sm btn-outline-secondary">Go to MP {{entry.number}}</button>
            </a>
            {% endif %}            
            {% if entry.collection == "homeworks" %}
            <a href="{{ site.baseurl }}{{ entry.url }}">
            <button type="button" class="btn btn-sm btn-outline-secondary">Go to Homework {{entry.number}}</button>
            </a>
            {% endif %}
          </div>
        </div>
      </div>
    </div>
  </div>
{% endfor %}
</div>
</main>

