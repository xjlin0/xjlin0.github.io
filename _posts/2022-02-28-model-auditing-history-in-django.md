---
layout: post
title: "Model Auditing and History in Django"
description: "Tracking model changes history in Django"
category: tech
tags: [python, django]
---
{% include JB/setup %}
### Need to track changes in the past? <img src="/assets/imgs/edna_mode.webp"  alt="Various Edna Mode Styles" width="40%"/>

There's a feature request to my meeting management Django App "Attendees": show all changes history of data, in case something happens. That sounds like a reasonable request since there's always human error. Sometimes we just need to trace back to find some old data.

There are [many Django packages capable doing model tracking](https://djangopackages.org/grids/g/model-audit/). Here I am listing two interesting packages: [Django-pghistory](https://github.com/Opus10/django-pghistory) 1.2.1 and [Django-reversion](https://github.com/etianen/django-reversion) 5.0.0.  They are using different approaches for tracking the model data changes, so you can find who changed what at when in the past.  Both of their documentation explain well on how to start. Again, there's nothing perfect, please find one that suits you the best.

<hr/>

| Item                    | [Django-pghistory](https://github.com/Opus10/django-pghistory) |[Django-reversion](https://github.com/etianen/django-reversion)|
| ----------------------- | --------------- | -------------- |
| database supported      | only Postgres   | any database Django supported |
| history stored location | Each model has its own db table | All models history in one single db table |
| history stored models   | Each model has its own history model | All models history share one history model |
| History generates by  | Postgres triggers automatically| [Python decorators](https://django-reversion.readthedocs.io/en/stable/views.html#decorators) added by developers  |
| tracking coverage  | everything captured by database| Python code is required to track changes|
| grouping multiple models changes | [by AggregateEvent](https://django-pghistory.readthedocs.io/en/latest/extras.html#aggregating-events-with-aggregateevent) | [by revision block](https://django-reversion.readthedocs.io/en/stable/api.html#creating-revisions)|
| Rollback/Revert         | N/A           | supported out of box |
|Django admin UI support | [needs coding](https://django-pghistory.readthedocs.io/en/latest/extras.html#showing-event-history-in-the-django-admin) | supported out of box |
|Tracking Third-Party Model Changes| [set in AppConfig](https://django-pghistory.readthedocs.io/en/latest/tutorial.html#tracking-third-party-model-changes) | [re-register by VersionAdmin](https://django-reversion.readthedocs.io/en/stable/admin.html?#integration-with-3rd-party-apps)  |
|Relation tracking by|Context Collection with through models| [`register(follow=...)`](https://django-reversion.readthedocs.io/en/stable/api.html#registration-api) |
{:style="width: 100%;"}
<hr/>

Django-pghistory tracks model changes by Postgres database triggers, no other databases supported. Once configured a model's history model "Event", all changes will be recorded to its history table automatically at database level, without additional codes. So tracking is working out of box after proper configuration. Even changes in Django or PSQL shell will be recored automatically without python code, and the history generation is surely quicker. This is extremely useful if other non-python services also writing to your Django databases.  Each model has its own history table, and recorded changes are plain database rows, so querying particular history is straightforward.  You can even bundle changes of multiple models to the same context by Event Aggregate. However there's no active development after May 2021, and the installation/configuration may take some time.

Django-reversion is undoubtedly the most popular and active history tracking package with bigger user community. After registering models in Admin, changes of all registered Django models will be captured in json format at a single database table.  History listing and rollback in Admin UI works like a breeze.  You can bundle changes of several models by writing revision block codes. However please be prepared to manually add tracking codes to all your Django code changing model data, or tracking won't work if missed.  For example, if there are 4 views changes the model of interest, you will need to add tracking code to all of 4 views.  Adding new views/codes without tracking may escape the history recording.  Also, querying certain changes in json history from all models may not be very straightforward.

Of course, these are just some of my observations, please let me know if any info are incorrect or changed anytime.