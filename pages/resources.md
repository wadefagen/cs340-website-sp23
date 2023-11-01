---
layout: default
title: Resources
permalink: /resources/

layout: content
---


# Course Resources

## "Cheat Sheets" for Topics in DISCOVERY

- [Pandas Cheat Sheet](https://github.com/pandas-dev/pandas/blob/master/doc/cheatsheet/Pandas_Cheat_Sheet.pdf)
- [Illinois' Python Syntax Cheat Sheet]({{ site.baseurl }}/static/illinois-python-cheatsheet.pdf)
- [Statistics Formula Sheet](https://docs.google.com/document/d/12eYPeOzGnPrFQBJfyZuiZJ4k0OfW6zAohjXc4cQN64c/edit?usp=sharing)


## Datasets

### Illinois-specific Datasets

- ["Hello Dataset" (Spring 2020)](https://docs.google.com/spreadsheets/d/1uS6OoVsaCNEDwi-ozOXJe7Vn08M-41k7W1cdAlwLIiY/edit?usp=sharing) -- Requires @illinois.edu login
- [Course Catalog Dataset](https://docs.google.com/spreadsheets/d/1ZnpZpipyui_zIIBblmalRYiyEG1glUKinqJtQxVL04o/edit#gid=0)
- [UIUC GPA Dataset](https://raw.githubusercontent.com/wadefagen/datasets/master/gpa/uiuc-gpa-dataset.csv)
- [Perception of Probability Words Dataset](https://docs.google.com/spreadsheets/d/1Toez3i69iBNoeG0lHg1FVzoVmCTT2BPe-uKgBO__FOA/edit?usp=sharing)
- [Illini Football Dataset](https://raw.githubusercontent.com/wadefagen/datasets/master/illini-football/illini-football-scores.csv)


### Classic Data Science Datasets

- [UC-Berkeley Graduate Admission Dataset](https://docs.google.com/spreadsheets/d/1k3AMY3JtmIf60DwmCfLORKsvYRKu6XRIJ000wbWFnjQ/edit?usp=sharing)
- [Diamonds Dataset](https://docs.google.com/spreadsheets/d/1VSYPeI6SCF6JhzqaIcN5PvjH60xI7PrsOROCvfbz2rY/edit?usp=sharing)
- [Calories in Beer Dataset](https://drive.google.com/file/d/1NQX-KqMi6npDydKf_Yojhldm24KPUP5-/view?usp=sharing)
- [Iris Dataset](https://en.wikipedia.org/wiki/Iris_flower_data_set)


<!--
## Real-World Datasets Used in DISCOVERY

### Illinois-specific Datasets

- [Fall 2019 "Hello Dataset"](https://docs.google.com/spreadsheets/d/1LB4E89GoVi5yezEfHhopbI5BMxodSG7VvoznkaAkMAQ/edit#gid=0)
- [Perception of Probability Words Dataset](https://docs.google.com/spreadsheets/d/18zQc-cYb376QA478lgWnMbQj39M9wVqHtjbvLP0PTSc/edit?usp=sharing)
- [Course Catalog Dataset](https://docs.google.com/spreadsheets/d/1ZnpZpipyui_zIIBblmalRYiyEG1glUKinqJtQxVL04o/edit#gid=0)
- [UIUC GPA Dataset](https://raw.githubusercontent.com/wadefagen/datasets/master/gpa/uiuc-gpa-dataset.csv)
- [Illinois Graybook Dataset](https://github-dev.cs.illinois.edu/stat107-fa19/_release/blob/lab_plots/lab_plots/graybook.csv)
- [Illini Football Dataset](https://raw.githubusercontent.com/wadefagen/datasets/master/illini-football/illini-football-scores.csv)

### Classic Data Science Datasets

- [Spotify Song Attributes](https://www.kaggle.com/geomack/spotifyclassification)
- [Calories in Beer Dataset](https://drive.google.com/file/d/1NQX-KqMi6npDydKf_Yojhldm24KPUP5-/view?usp=sharing)
- [UC-Berkeley Graduate Admission Dataset](https://docs.google.com/spreadsheets/d/1k3AMY3JtmIf60DwmCfLORKsvYRKu6XRIJ000wbWFnjQ/edit?usp=sharing)
- [Iris Dataset](https://en.wikipedia.org/wiki/Iris_flower_data_set)
-->

## Resource Guides

{% if site.resources.size > 0 %}
<ul>
    {% for resource in site.resources %}
    <li>
        <a href="{{ site.baseurl }}{{ resource.url }}">{{ resource.title }}</a>
    </li>
    {% endfor %}
</ul>
{% else %}
<p>No resources have been released yet. Check back later!</p>
{% endif %}

