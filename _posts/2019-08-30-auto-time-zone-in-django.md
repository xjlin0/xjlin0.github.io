---
layout: post
title: "Auto time zone in Django"
description: "Automatically setup timezones for users on different time zones"
category: tech
tags: [Python, Django, JavaScript]
---
{% include JB/setup %}
### Time has been changed. <img src="/assets/imgs/incredibles_watch_skin.png"  alt="watch skin of incredibles" width="20%"/>

The servers running typical web apps are usually in UTC time zone.  Servers has no idea of user time zones. When users around the world visiting the website, the time attributes of objects from server can be converted to user local time zones in their browsers.  This is a scalable approach, not only such setting can serves users in different time zones, allows spinning up more servers without extra settings, but also fits the design principle for separating concerns.

In Rails there are multiple ways to do such time conversion, [mostly manually in View layer](https://medium.com/@theodorelaurent/display-time-in-user-local-time-in-rails-212155fa6447).  How do we setup the time conversion in Django? For display, there is a great Django template filter ```|date:'c'``` can always show ISO 8601 time format for conversion later, but we also want to get user time input correctly converted back to UTC at server.

There is [a great app](https://github.com/adamcharnock/django-tz-detect) supposed to do that but unfortunately does not work in my environment. Here is my implementation to achieve it by cookie automatically and system-wisely, without disturbing users.

<img src="/assets/imgs/Auto_time_zone_in_Django_by_cookie.png"  alt="watch skin of incredibles" width="80%"/>

{% comment %}
title Auto time zone in Django by cookie

participant browser\n(time zone X)
participant Template
participant middleware &\ncontext_processors
participant View&Model\n(UTC time)

browser\n(time zone X)->middleware &\ncontext_processors:first visit
middleware &\ncontext_processors->View&Model\n(UTC time):no time info in cookie:\nactivate default time zone\n& set time zone variable
View&Model\n(UTC time)->View&Model\n(UTC time): is user logged in?\n no
View&Model\n(UTC time)-->Template: render login page
Template-->browser\n(time zone X): return login page with JS\nfor detecting timezone 

browser\n(time zone X)->middleware &\ncontext_processors: JS detects browser timezone\nand sets timezone in cookie\n'America/Los_Angeles'
middleware &\ncontext_processors->View&Model\n(UTC time): Got user time zone in cookie:\nactivate user time zone\n& set time zone variable: 'PDT'
View&Model\n(UTC time)-->Template: object responded with converted time
Template-->browser\n(time zone X): page rendered with time zone variable\ndata time in 'PDT'
{% endcomment %}

In the JavaScript, upon page load, detecting client time zone and write encoded time zone name (such as 'America/Los_Angeles') is possible by calling native ```Intl.DateTimeFormat``` when every page loads:

{% highlight javascript %}
const timeZoneName = Intl.DateTimeFormat().resolvedOptions().timeZone;
document.cookie = 'timezone=' + encodeURIComponent(timeZoneName) + '; path=/';
{% endhighlight %}

So the cookie now have encoded user time zone data, we just need read cookie, decode and activate the user time zone by marvelous ```MiddlewareMixin```.  For the case of initial visit, we can refer to default setting in settings.py

```python
# app/middleware.py
import pytz
from urllib import parse
from django.utils import timezone
from django.utils.deprecation import MiddlewareMixin
from django.conf import settings


class TimezoneMiddleware(MiddlewareMixin):

    @staticmethod
    def process_request(request):
        tzname = request.COOKIES.get('timezone') or settings.CLIENT_DEFAULT_TIME_ZONE
        timezone.activate(pytz.timezone(parse.unquote(tzname)))
```

To make user see what time zone is shown, we also declare a time zone variable to store time zone abbreviation, such as ```PDT```.

```python
# app/context_processors.py
from pytz import timezone
from datetime import datetime
from django.conf import settings
from urllib import parse


def common_variables(request):
    tzname = request.COOKIES.get('timezone') or settings.CLIENT_DEFAULT_TIME_ZONE
    return {
       "timezone_name": datetime.now(timezone(parse.unquote(tzname))).tzname()
    }

```

And then add our new magic TimezoneMiddleware and new varialbe in the Django settings.py:
```python
CLIENT_DEFAULT_TIME_ZONE = "pick a time zone, i.e. America/Los_Angeles"

MIDDLEWARE = [
    ## other Middlewares
    "app.middleware.TimezoneMiddleware",
    ]

TEMPLATES = [
    {
        ### other settings
        "context_processors": [
            ### other context_processors
            "app.context_processors.common_variables",
        ]
    
    }
]    
```

It is fine to show user what time zone is being used, even [the abbreviations are not unique](https://en.wikipedia.org/wiki/List_of_time_zone_abbreviations), for example, AST stands for Arabia Standard Time (UTC+3) or Atlantic Standard Time (UTC-4).

```html
{% raw %}
<p>
    You last visited at {{ current_user.last_visited_at }} {{timezone_name}}
</p>
When do you want to go?
<input type="datetime-local" name="departure-time">
{% endraw %}
```

And voilà! the time will be shown in user time zone, even it was originally stored on the server in UTC time zone.  Moreover, when user inputs their time, the saved record at the server will be automatically converted back to UTC. Isn't that great?

The caveat of the implementation can be spotted when user system time changes due to daylight saving time or user traveling. If user still have the session, he/she will see the page remain in the old time timezone.  It may not be a common issue with shorter SESSION_COOKIE_AGE. Since there's no browser event for system time change, the workaround is to reload page or periodically update cookie by JavaScript.