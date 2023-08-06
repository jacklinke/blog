---
title: "Alternatives to BooleanField in Django"
datePublished: Sun Aug 06 2023 04:45:56 GMT+0000 (Coordinated Universal Time)
cuid: clkyynq4i00070ajuauvt65pc
slug: alternatives-to-booleanfield-in-django
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1691297129589/a94bd848-d41c-407f-b7ae-34f3ef85717f.png
tags: django, database, state-management, models

---

I hope to expand on some thoughts I have been mulling over regarding the (over)use of BooleanField in Django projects.

I rarely use [BooleanField](https://docs.djangoproject.com/en/4.2/ref/models/fields/#booleanfield) in a Django project. In **nearly every case** where I might be tempted to use BooleanField, a better alternative exists.

Two of the biggest benefits of BooleanField are its small size in the database and the simplicity of working with the field. But the size of one type of field compared to another is rarely a dealbreaker or a significant contributor to query time or other concerns in building an app for production. And while simplicity is nice, the use of BooleanField in the wrong place can lead to frustration.

As an example of the potential for things to go awry, consider a common situation django beginners experience.

---

## A desire to track the state of something

Starting with a very basic example model:

```python
class Comment(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    body = models.CharField(max_length=200)
    created_on = models.DateTimeField(auto_add=True)
```

One requirement for our `Comment` model is that we must track if the model is approved by the administrator. So a BooleanField is added.

```python
class Comment(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    body = models.CharField(max_length=200)
    created_on = models.DateTimeField(auto_add=True)

    is_active = models.BooleanField(default=False)
```

Everything looks good. We can now approve user comments by setting `is_active` to `True` in any given `Comment` instance.

But another requirement is identified: We need to be able to archive comments as well. So, let's add another BooleanField!

```python
class Comment(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    body = models.CharField(max_length=200)
    created_on = models.DateTimeField(auto_add=True)

    is_active = models.BooleanField(default=False)
    is_archived = models.BooleanField(default=False)
```

Now when we archive a file, we have to set both `is_active` as `False` and `is_archived` as `True`. If we unarchive something, we have to set both `is_active` as `True` and `is_archived` as `False`.

Each additional BooleanField we add increased the number of steps we need to take (and increases the potential risk we may get the combination of `Trues` and `Falses` wrong). *What does it even mean if both* `is_active` *and* `is_archived` *are set to* `True`*?* It is an undefined state 😧

---

## Alternatives

There are two main situations where we might want an alternative to BooleanField

* When we are tracking [state/status](https://en.wikipedia.org/wiki/State_(computer_science)), we can use something that ensures only one valid state is selected at any given time
    
* When we want to track whether a single event or action has taken place, we can use DateTimeField
    

Let's discuss these in more detail...

---

## Tracking state

Just as in the example above, it is common to need to track the state of a model instance.

Some examples include:

* Is an `Order` instance in *cart*, *paid*, *shipped*, *completed*, or *canceled* status?
    
* Is a `Document` in *draft*, *submitted*, *published*, or *deleted* status?
    
* Is an `Appointment` in *requested*, *approved*, *confirmed*, *completed*, or *canceled* status?
    

In each of these cases, only one status makes sense at any given time.

Alternatives to BooleanField in these cases include:

* Using either [CharField](https://docs.djangoproject.com/en/4.2/ref/models/fields/#charfield) or [IntegerField](https://docs.djangoproject.com/en/4.2/ref/models/fields/#integerfield) along with [choices](https://docs.djangoproject.com/en/4.2/ref/models/fields/#choices) to allow selection of each status. I am particularly fond of using [TextChoices or IntegerChoices](https://docs.djangoproject.com/en/4.2/ref/models/fields/#enumeration-types) enumeration types, and wrote [a small gist cheatsheet](https://gist.github.com/OmenApps/3eef60ba4204f3d1842d9d7477efcce1) when these became available in django 3.0. This is a simple and effective approach when the choices for the status field will not change often, and the user does not need to add new statuses.
    
* Adding a ForeignKey to another model which contains the available state choices can make it possible for end-users to add new states, but adds increased complexity and another database table.
    
* Using [django-fsm](https://github.com/viewflow/django-fsm), [django-pgtrigger](https://django-pgtrigger.readthedocs.io/en/latest/cookbook.html#validating-field-transitions), or other third-party apps to ensure state is correctly maintained. These tools help ensure that status transitions in the correct order from one state to another. This comes with added dependencies, which may be undesired, but both of the packages I mention here are fantastic!
    

I tend to use the first or last option, depending on the complexity of the business logic. If I am not concerned about the order in which a model instance transitions from one state to another, I tend to use CharField like this:

```python
class Comment(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    body = models.CharField(max_length=200)
    created_on = models.DateTimeField(auto_add=True)

    class StatusChoices(models.TextChoices):
        NEW = "NW" _("New")
        ACTIVE = "AC", _("Active")
        ARCHIVED = "AR", _("Archived")

    status = models.CharField(
        max_length=2,
        choices=StatusChoices.choices,
        default=StatusChoices.NEW,
    )
```

## Tracking a single event or action

When we want to track an event or action, such as whether a `Notification` has been read, we often first reach for a BooleanField.

But what if you could add context about the action without any significant effort? You can! Just swap BooleanField for [DateTimeField](https://docs.djangoproject.com/en/4.2/ref/models/fields/#datetimefield).

Instead of...

```python
class Notifications(models.Model):
    # Some fields...

    viewed = models.BooleanField(default=False)
```

Use...

```python
class Notifications(models.Model):
    # Some fields...

    viewed = models.DateTimeField(null=True, black=True)
```

When the action or event takes place, set the field to [`timezone.now()`](https://docs.djangoproject.com/en/4.2/ref/utils/#django.utils.timezone.now).

This tiny change means that we can now

* Show the user whether the Notification was read
    
* List all read Notifications
    
* Show the user when the Notification was read
    
* List all Notifications read within the last week
    
* Create a timeline showing the rate of Notifications read during each month of the year
    
* Calculate the average gap in time between the creation of a Notification and when that Notification was read
    
* etc.
    

With BooleanField, we could only perform the first two of those queries. Being able to query *when* events took place can easily add value to your end users, and can make it easier for you to audit activities and usage.

---

## When to use BooleanField?

BoleanField does have its use-cases. Here is where I would still use them:

* To determine whether a model instance **is** *something* or **can be used for** *something*, and it does not make sense to track when this state came to be. For instance, if we want to track whether an `Asset` is a spare part, we might use `is_spares = models.BooleanField(default=False)` Another example is whether the `Asset` can be checked out: `can_be_checked_out = models.BooleanField(default=True)`. This use-case is *squishy*. It can be hard to tell when we might be better off tracking when these fields change or when simply keeping it to true or false is more appropriate.
    
* When we are using the field as a setting that does not have state or a particular occurrence time. Usually this is when setting a value for a model instance that is not expected to change (or at least not often). I use these for configuring tenants in one of my projects. For instance, `allow_tenant_to_place_orders = models.BooleanField(default=True)`. Here I do not care about when the decision to allow or disallow this tenant from placing orders took place, and there is no other state this decision might transition to. The tenant either **can** or **cannot** place orders, and that's it.
    

---

## Conclusion

Hopefully some of you find these notes helpful. BooleanFields have their uses, but they are often over-used. The alternatives mentioned above can help to prevent spaghetti code as the number of potential statuses increase or help to add value when you may need to track when actions in your project took place.

Have thoughts or questions about these ideas? I would love to hear them 🙂