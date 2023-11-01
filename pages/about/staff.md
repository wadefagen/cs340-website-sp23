---
title: Staff
permalink: /staff/
---

<style>
.staff-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
    grid-gap: 20px;
    align-items: stretch;
}
.card-body {
    padding: 0.75rem;
}
</style>

<div class="container-fluid">

    {% include staff_cards.html role='all' %}
    
    <!--
    <h3>Instructors</h3>
    {% include staff_cards.html role='prof' %}

    <h3>Graduate Teaching Assistants</h3>
    {% include staff_cards.html role='ta' %}

    <h3>Undergraduate Course Assistants</h3>
    {% include staff_cards.html role='ca' %}
    -->
</div>
