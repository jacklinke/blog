# Ideas for Improving Git Commits and Application Logs

Between talks at DjangoCon and conversations with [Jeff Triplett @webology](https://twitter.com/webology) and others, I've been thinking a lot about git commits and logging over the past week, so I put together some notes on the topic.

---

## gitmoji

[gitmoji](https://gitmoji.dev/) is "an emoji guide for your commit messages". It provides a set of emoji paired with descriptions, which can help to provide visual cues to more quickly understand the purpose of commits.

The first place I heard of gitmoji was the [openfun git handbook](https://handbook.openfun.fr/git), which has some great tips for using what they describe as "emoji-driven commit message format".

Jeff has used emojis in commits as well, and recommended this excellent style guide: https://github.com/slashsbin/styleguide-git-commit-message

A couple other good reads include:
- https://dev.to/javidjms/git-write-better-commits-with-gitmoji-3193
- https://engineeringfordatascience.com/posts/gitmoji/

The gitmoji website also has [a page with related resources](https://gitmoji.dev/related-tools), including:

- Plugins for Jetbrains IDEs, VSCode, Sublime Text, etc
- Browser extension for gitmoji
- Desktop gitmoji GUIs
- etc

## The Perfect Commit

When it comes to improving your Git Commits, also consider the advice from Simon Willison about the "Perfect Commit". A perfect commit includes the following:

1. The implementation: a single, focused change
2. Tests that demonstrate the implementation works
3. Updated documentation reflecting the change
4. A link to an issue thread providing further context

This is great advice for improving your contributions to software development, and it is something I am working to be more consistent on myself. You can read his post on this topic in detail [at his blog](https://simonwillison.net/2022/Oct/29/the-perfect-commit/).

## Emojis in Logs

I have also begun using emojis in my logs for the same reasons one might use them in git commits.

In my primary django project, I added the following class to my project settings.

```python
@dataclass
class Logmoji:
    """
    Provides Emojis for more eye-catching logs
    """

    curious: str = "🤨"
    paused: str = "💤"
    action_begin: str = "🏁"
    action_end: str = "🔚"

    money_related: str = "💵"
    email_related: str = "📨"
    time_related: str = "📅"
    statistics_related: str = "📈"
    auth_related: str = "🔒"
    maintenance_related: str = "🔧"

    success: str = "🎉"
    denied: str = "⛔"
    potential_issue: str = "👾"
    warning: str = "⚠️"
    error: str = "💀"
    critical: str = "💣"
```

Assuming you already [have logging set up](https://docs.djangoproject.com/en/dev/howto/logging/) in your project, use this in the following ways:

```python
from django.conf.settings import Logmoji

try:
    item = ItemsModel.objects.get(id=1)
except ItemsModel.DoesNotExist as e:
    logger.warning(f"{Logmoji.curious} {e}")
```

```text
🤨 Exception: ItemsModel matching query does not exist.
```

```python
from django.conf.settings import Logmoji

logger.warning(f"{Logmoji.auth_related} User is not logged in. Redirect to login.")
```

```text
🔒 User is not logged in. Redirect to login.
```

You could, of course, directly add the emoji in the text of the log entry, but I like that if I change my mind about the emoji I want for a particular use, I can easily make one change in settings and the update propagates to all future log entries.

## Conclusion

I hope you'll give these all a try and let me know how they work for you.
