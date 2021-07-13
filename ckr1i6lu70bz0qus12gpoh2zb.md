## Ajax-Enabled Checkbox and Select with Django and HTMX

*Republished from the original blog at the same domain*

What we're going to build
-------------------------

We want to allow users to set preferences on their profile page. We will look at two elements used for basic settings -- Checkboxes and Selects.

Traditionally there are two approaches to settings like these -- Django forms or traditional HTML forms -- both usually requiring a full page reload or custom javascript to post the values via Ajax.

Well... we want the benefits of Ajax form submissions *without* the pain of writing a bunch of javascript.


![Final Result - User preferences](https://cdn.hashnode.com/res/hashnode/image/upload/v1626146785789/g2ekCjhTU.png "Final Result - User preferences")

Background
----------

[HTMX](https://htmx.org/) is a new player in town. The successor to [Intercooler JS](https://intercoolerjs.org/), HTMX promises to allow you to "access [AJAX](https://htmx.org/docs#ajax), [CSS Transitions](https://htmx.org/docs#css_transitions), [WebSockets](https://htmx.org/docs#websockets) and [Server Sent Events](https://htmx.org/docs#sse) directly in HTML, using [attributes](https://htmx.org/reference#attributes), so you can build [modern user interfaces](https://htmx.org/examples) with the [simplicity](https://en.wikipedia.org/wiki/HATEOAS) and [power](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) of hypertext".[](https://intercoolerjs.org/)

We're going to utilize HTMX to implement some user settings using checkboxes for boolean values and select boxes for choices. This is something that might be used on a user's personal settings page to allow them to change values easily. In this example, users can select whether they want to receive messages (maybe from the system administrator or from other users?) via email and/or SMS, and also how often notifications about their orders should be sent. We are only implementing the preference selections here, not the messaging/notifications/order tooling that would back these up. This example also does not concern itself with security.

This article assumes you already know the basics of django. If not, check out [these great resources](https://www.reddit.com/r/djangolearning/wiki/index) to get started.

It also assumes your project has a [custom User model](https://docs.djangoproject.com/en/3.1/topics/auth/customizing/#a-full-example) -- something that's highly recommended for *every* django project.

User Model
----------

We start with a basic user model, adding the following new fields:

users/views.py

```python
 class User(AbstractBaseUser):

    # ... Ignoring existing fields ...

    receive_email_messages = models.BooleanField(
        _("Receive Email Messages"),
        default=False,
    )

    receive_sms_messages = models.BooleanField(
        _("Receive SMS Messages"),
        default=False,
    )

    class NotificationChoices(models.TextChoices):
        NONE = "NONE", _("None")
        IMMEDIATE = "IMMEDIATE", _("Immediately")
        DAILY = "DAILY", _("Daily Archive")
        WEEKLY = "WEEKLY", _("Weekly Archive")

    order_notification_freq = models.CharField(
        max_length=9,
        choices=NotificationChoices.choices,
        default=NotificationChoices.NONE,
        help_text=_(
            "How often do you want to receive notifications about your orders?"
        ),
    )
```

Basic Template
--------------

The following basic template gives us Bootstrap, HTMX, and _Hyperscript. We will fill in the contents of the inner div as we go along. We also add a basic view and url to display the preferences. You won't see much yet if you navigate to this url.

preferences.html

```html
<!doctype html>

<html lang="en">
    <head>
        <title>User Preferences</title>

        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">

    </head>

    <body>
        <script src="https://unpkg.com/htmx.org@1.3.3"></script>
        <script src="https://unpkg.com/hyperscript.org@0.0.9"></script>
        <h3>User Preferences</h3>
        <div class="row m-5">
            <!-- Content will be added here -->
        </div>

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>
    </body>
</html>
```

users/urls.py

```python
import logging
from django.urls import path

from users import views

app_name = "users"
urlpatterns = [
    path("preferences/", views.preferences, name="preferences"),
]
```

users/views.py

```python
from django.contrib.auth import get_user_model
from django.contrib.auth.decorators import login_required
from django.http import HttpResponse
from django.shortcuts import render
from django.views.generic import TemplateView

User = get_user_model()

@login_required
def preferences(request):
    return render(request, "users/preferences.html")
```

Toggle Preferences
------------------

Let's start with the messaging preferences. We will have two checkboxes that allow us to set or un-set the *User.receive_email_messages* and *User.receive_sms_messages* model fields. Additionally, we'll add an alert div to notify the user whether the process was successful or not, and we will make it disappear after 2 seconds using [_hyperscript](https://hyperscript.org/).

There are several ways to approach this. We could have a separate view and url for every checkbox we want to toggle. I have opted instead to have one view and url that handles all toggles. Depending on the value of the *preference* hidden form field, the view determines which preference was submitted.

Insert the following into the inner <div> of our preferences.html template.

```html
<div class="custom-control custom-switch col-lg-3 mt-3 mb-1">
    <form method="POST">
        <input type="checkbox" class="form-check-input" {% if request.user.receive_email_messages == True %}checked{% endif %} name="set_value" id="set_email_value" hx-post="{% url "users:toggle_preference" %}" hx-trigger="click" hx-target="#email_response">
        <label class="form-check-label" for="set_email_value">Receive Email Messages</label>
        <input type="hidden" name="preference" value="email_msg">
        {% csrf_token %}
    </form>
    <div id="email_response"></div>
</div>
<div class="custom-control custom-switch col-lg-3 mt-3 mb-2">
    <form method="POST">
        <input type="checkbox" class="form-check-input" {% if request.user.receive_sms_messages == True %}checked{% endif %} name="set_value" id="set_sms_value" hx-post="{% url "users:toggle_preference" %}" hx-trigger="click" hx-target="#sms_response">
        <label class="form-check-label" for="set_sms_value">Receive SMS Messages</label>
        <input type="hidden" name="preference" value="sms_msg">
        {% csrf_token %}
    </form>
    <div id="sms_response"></div>
</div>
```

This is pretty standard stuff, except for the HTMX attributes in the <input> tags. Specifically:

> hx-post="{% url "users:toggle_preference" %}" hx-trigger="click" hx-target="#email_response"

 This translates to on-click post the form contents to *users:toggle_preference* url and put anything returned from that URL to the html tag with an id of *email_response*.

Let's add the view and URL that will allow use of this chunk of the template.

users/views.py (continued)

```python
@login_required
def toggle_preference(request):
    if request.method == "POST":
        user = request.user
        successful_toggle = False

        # Set/un-set email messaging preference
        if (request.POST.get("preference", None) == "email_msg"):
            if request.POST.get("set_value", None) is not None:
                user.receive_email_messages = True
                user.save()
                successful_toggle = True
            else:
                user.receive_email_messages = False
                user.save()
                successful_toggle = True

        # Set/un-set sms messaging preference
        if (request.POST.get("preference", None) == "sms_msg"):
            if request.POST.get("set_value", None) is not None:
                user.receive_sms_messages = True
                user.save()
                successful_toggle = True
            else:
                user.receive_sms_messages = False
                user.save()
                successful_toggle = True

        if successful_toggle:
            return HttpResponse(
                (
                    '<div _="on load wait 2s then remove me" class="alert alert-success alert-dismissible fade show" role="alert">'
                    "<strong>Success! Your preferences were updated.</strong>"
                    "</div>"
                ),
                status=200,
                content_type="text/html",
            )

    # If we did not successfully toggle one of the preferences, notify the user of the failure
    return HttpResponse(
        (
            '<div _="on load wait 2s then remove me" class="alert alert-warning alert-dismissible fade show" role="alert">'
            "<strong>Warning! Preferences were not updated. Notify the webmaster.</strong>"
            "</div>"
        ),
        status=200,
        content_type="text/html",
    )
```

In the view we are checking for the value of the *preference* hidden form field, and depending on whether it is *sms_msg* or *email_msg*, we set or unset the corresponding model field before returning a message the the user. Finally, if we did not toggle any of the preferences, we alert the user to the failure.

users/urls.py

```python
import logging
from django.urls import path

from users import views

app_name = "users"
urlpatterns = [
    path("preferences/", views.preferences, name="preferences"),
    path("preferences/toggle_preference/", views.toggle_preference, name="toggle_preference"),
]
```

At this point you should be able to go to the user preferences page and see the two toggle preferences.


![preferences-with-swap-toggle-success.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1626146830487/GVnyURyHn.png  "preferences with swap toggle success")

Select Preferences
------------------

This time, instead of toggles/checkboxes, we want the user to select from multiple choices for the frequency of order notifications. If you look back at out User model you will see that we have a NotificationChoices class with 4 options.

Append the following immediately after the content we added for the toggle preferences:

preferences.html

```html
<div class="col-lg-6 mb-1">
    <form method="POST">
        <label class="form-label" for="order_notify">Order Notification Frequency</label>
        <select class="form-control" name="order_notify" id="order_notify" hx-post="{% url 'users:select_order_notification_freq' %}" hx-trigger="change" hx-target="#notification_response">
            <option value="NONE" {% if user.order_notification_freq == "NONE" %}selected{% endif %}>None</option>
            <option value="IMMEDIATE" {% if user.order_notification_freq == "IMMEDIATE" %}selected{% endif %}>Immediate</option>
            <option value="DAILY" {% if user.order_notification_freq == "DAILY" %}selected{% endif %}>Daily</option>
            <option value="WEEKLY" {% if user.order_notification_freq == "WEEKLY" %}selected{% endif %}>Weekly</option>
        </select>
        {% csrf_token %}
    </form>
    <div id="notification_response"></div>
</div>
```

Like before, it's pretty standard. I manually added the options for the <select> here, but you could also easily pull the values from the *User.NotificationChoices* class and add this as context in the view to make things more dynamic and easier to change later on.

Here our HTMX attributes are saying...

*On-change*, post the form contents to *users:select_order_notification_freq* and replace the HTML element of *id=notification_response* with whatever is returned from the view.

Now to add the view and url.

users/views.py

```python
@login_required
def select_order_notification_freq(request):
    if request.method == "POST":
        user = request.user
        order_notify = request.POST.get("order_notify", None)

        if order_notify is not None:
            if order_notify in User.NotificationChoices.values:
                user.order_notification_freq = order_notify
                user.save()
            else:
                return HttpResponse(
                    (
                        '<div _="on load wait 2s then remove me" class="alert alert-warning" role="alert">'
                        "<strong>Warning! Preferences were not updated. Notify the webmaster.</strong>"
                        "</div>"
                    ),
                    status=200,
                    content_type="text/html",
                )
        else:
            user.order_notification_freq = User.NotificationChoices.NONE
            user.save()

    return HttpResponse(
        (
            '<div _="on load wait 2s then remove me" class="alert alert-success" role="alert">'
            "<strong>Success! Preferences were updated.</strong>"
            "</div>"
        ),
        status=200,
        content_type="text/html",
    )
```

users/urls.py

```python
import logging
from django.urls import path

from users import views

app_name = "users"
urlpatterns = [
    path("preferences/", views.preferences, name="preferences"),
    path("preferences/toggle_preference/", views.toggle_preference, name="toggle_preference"),
    path("preferences/select_order_notification_freq/", views.select_order_notification_freq, name="select_order_notification_freq"),
]
```

Conclusion
----------

At this point, both type of preferences (toggle and select) should work. Each time a change is made, an alert should pop up letting you know the status of that change. If you make a change and refresh the page, the updated value should remain.

The full code for the files mentioned here can be found at <https://gist.github.com/OmenApps/b76638287286a475a36404e919658ce1>