---
title: "Schedule"
layout: content

permalink: /schedule/
---

# Course Schedule

*(This schedule will be adjusted as the semester progresses.  Future topics are a best-estimate on when we expect to cover them.)*


<table class="table table-bordered stat107-schedule">
  <thead>
    <tr>
      <th style="min-width: 80px">Date</th>
      <th>Event</th>
    </tr>
  </thead>
  <tbody>
  {% for lecture in site.data.lecture.lectures %}
    {% assign remainder = forloop.index | modulo: 2 %}
    {% assign week = forloop.index | plus: 2 | divided_by: 2  %}
    {% if remainder == 1 %}
    </tbody>
    <thead>
      <tr>
        <td class="pb-2 pt-4" colspan="2"><h3 class="mb-0">Week {{ week }} </h3></td>
      </tr>
    </thead>
    <tbody>
    {% endif %}
    {% if lecture.note %}
      <tr>
        <td colspan="2" style="background-color: lightyellow;"><i>{{ lecture.note }}</i></td>
      </tr>
    {% endif %}
    <tr>
      <td>{{lecture.date | date: "%b. %e" }}</td>
      <td>
        {% if lecture.holiday %}
        <b>No Lecture</b> &mdash;
        {% endif %}
        {{lecture.title}}
        {% if lecture.description %}
        <div class="small"><i>{{lecture.description}}</i></div>
        {% endif %}
        <ul>
          {% for link in lecture.links %}
          <li>
              <a href="{{ link.first[1] | absolute_url }}">{{ link.first[0] }}</a>
          </li>
          {% endfor %}
        </ul>


        {% if lecture.readings %}
        Additional Readings:
        <ul>
          {% for link in lecture.readings %}
          {% if link.first[1] != "#" %}
          <li>
              <a href="{{ link.first[1] | absolute_url }}">{{ link.first[0] }}</a>
          </li>
          {% else %}
          <li>
              <i>{{ link.first[0] }}</i>
          </li>
          {% endif %}
          {% endfor %}
        </ul>
        {% endif %}        

        {% if lecture.assignments %}
        Assignments Released:
        <ul>
          {% for link in lecture.assignments %}
          {% if link.first[1] != "#" %}
          <li>
              <a href="{{ link.first[1] | absolute_url }}">{{ link.first[0] }}</a>
          </li>
          {% else %}
          <li>
              <i>{{ link.first[0] }}</i>
          </li>
          {% endif %}
          {% endfor %}
        </ul>
        {% endif %}

      </td>
    </tr>

  {% endfor %}
  </tbody>
</table>
