---
layout: default
title: Course Assignments
permalink: /assignments/

layout: content
---

# Assignments

## Homework

{% if site.homeworks.size > 0 %}
<ul>
    {% assign homeworks = site.homeworks | sort: 'due' | reverse %}
    {% for homework in homeworks %}
    <li>
        <a href="{{ site.baseurl }}{{ homework.url }}">
            <b>Homework {{ homework.number }}</b>: {{ homework.title }}
        </a>
        (Due: {{ homework.due | date: '%B %e, %Y at %l:%M%P' }})
    </li>
    {% endfor %}
</ul>
{% else %}
<p>No homeworks have been released yet. Check back later!</p>
{% endif %}

## MPs

{% if site.mp.size > 0 %}
<ul>
    {% assign mps = site.mp | sort: 'due' | reverse %}
    {% for mp in mps %}
    <li>
        {% if mp.ec == true %}
        <a href="{{ site.baseurl }}{{ mp.url }}">
            {% if mp.number %}
            <b>MP {{ mp.number }} - JKLMNOP</b>
            {% else %}
            <b>{{ mp.title }}</b>
            {% endif %}
        </a>
        <i>(Just Keep Letting Me Nerd Out Please!)</i>
        {% else %}
        <a href="{{ site.baseurl }}{{ mp.url }}">
            <b>MP {{ mp.number }}</b>: {{ mp.title }}
        </a>
        (Due: {{ mp.due | date: '%B %e, %Y at %l:%M%P' }})
        {% endif %}
    </li>
    {% endfor %}
</ul>
{% else %}
<p>No projects have been released yet. Check back later!</p>
{% endif %}


## Final Project

{% if site.project.size > 0 %}
<ul>
    {% assign items = site.project | sort: 'due' | reverse %}
    {% for d in items %}
    <li>
        <a href="{{ site.baseurl }}{{ d.url }}">
            {{ d.title }}
        </a>
        (Due: {{ d.due | date: '%B %e, %Y at %l:%M%P' }})
    </li>
    {% endfor %}
</ul>
{% else %}
<p>No projects have been released yet. Check back later!</p>
{% endif %}
