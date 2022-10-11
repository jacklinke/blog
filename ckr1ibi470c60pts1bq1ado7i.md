# Cheatsheet for Django Models

Basic Cheatsheet for Django Models
----------------------------------

*Updated 10 Oct 2022*

Sometimes it is useful to have a condensed version of highly detailed information.

This resource is intended mainly for folks who are regularly building out models, are comfortable with the concepts of django models, and want a consolidated listing of the majority of things available for models. I use this quite often, and it saves me a lot of time since I generally don't need to look up the full documentation. I can see at a glance what a particular field, Meta class option, QuerySet, etc should look like.

I've previously posted prior versions of this elsewhere, so you may have seen something similar, but this is the latest and most up-to-date version.

```python
import uuid

from django.db import models
# Use the import below instead, if using GeoDjango fields
# from django.contrib.gis.db import models

from django.utils.translation import gettext_lazy as _

from django.contrib.postgres.fields import (
    ArrayField,
    CICharField,
    CIEmailField,
    CITextField,
    HStoreField,
    IntegerRangeField,
    BigIntegerRangeField,
    DecimalRangeField,
    DateTimeRangeField,
    DateRangeField,
)
from django.contrib.postgres import ExclusionConstraint


def file_directory_path(instance, filename):
    # file will be uploaded to MEDIA_ROOT/my_files/{id}/{year}/{month}/{filename}
    return f"my_files/{instance.id}/{timezone.now().date().strftime('%Y/%m')}/{filename}"

def create_rand_value(app_label, model_name, prefix=None, suffix_len=10):
    """
    Just an example function, used to demonstrate overriding save()
    It generates an easily readable, unique string
    """

    code = ""

    if prefix is not None:
        code = code.join(prefix).join("-")

    while True:
        code = code.join(random.SystemRandom().choice("23456789BCDFGHJKMNPQRSTVWXYZ") for _ in range(suffix_len))
        Model = apps.get_model(app_label=app_label, model_name=model_name)

        try:
            present = Model.objects.first()
        except Model.DoesNotExist:
            present = None

        if present is not None:
            if not Model.objects.filter(code=code).exists():
                return code

# https://docs.djangoproject.com/en/dev/topics/db/managers/#from-queryset
class MyModelQuerySet(models.QuerySet):
    """Used to return a QuerySet that has been filtered"""
    
    def expired_items(self):
        return self.filter(expired__isnull=True)

class MyModelManager(models.Manager):
    """Used for table-level methods that do NOT return QuerySet"""
    def delete_expired_items(self):
        self.expired_items().delete()

    def get_queryset(self):
        # Limit every query used with this QuerySet / Manager to critical Items
        return super().get_queryset().filter(critical=True)

class MyModel(models.Model):
    """Comment about this model"""

    class CompanyType(models.TextChoices):
        PUBLIC_LIMITED_COMPANY = 'PLC', _('Public Limited Company')
        PRIVATE_COMPANY_LIMITED = 'LTD', _('Private Company limited by shares')
        LIMITED_LIABILITY_PARTNERSHIP = 'LLP', _('Limited Liability Partnership')
        __empty__ = _('(Unknown)')

    # Basic Model Fields
    # https://docs.djangoproject.com/en/dev/ref/models/fields/
    boolean_field = models.BooleanField(_("Boolean"), default=False)
    char_field = models.CharField(_("Character"), max_length=100, blank=True)
    char_field_choice = models.CharField(_("Character Choices"), choices=CompanyType.choices, default=CompanyType.__empty__, max_length=100, blank=True)
    date_field = models.DateField(_("Date"), null=True, blank=True)
    datetime_field = models.DateTimeField(_("DateTime"), null=True, blank=True)
    decimal_field = models.DecimalField(_("Decimal"), max_digits=5, decimal_places=2, null=True)
    duration_field = models.DurationField(_("Duration"), )
    email_field = models.EmailField(_("Email"), max_length=254, blank=True)
    file_field = models.FileField(_("File"), upload_to=file_directory_path, max_length=100)
    float_field = models.FloatField(_("Float"), null=True)
    generic_ip_address_field = models.GenericIPAddressField(_("Generic IP"), protocol='both', unpack_ipv4=False, null=True) # 'both', 'IPv4' or 'IPv6'
    image_field = models.ImageField(_("Image"), upload_to=file_directory_path, height_field=None, width_field=None, max_length=100)
    integer_field = models.IntegerField(_("Number"), null=True)
    json_field = models.JSONField(_("JSONField"), default=dict)
    pos_int_field = models.PositiveIntegerField(_("Positive Integer"), )
    pos_small_int_field = models.PositiveSmallIntegerField(_("Positive Small Integer"), )
    slug_field = models.SlugFiels(_("Slug"), max_length=50, blank=True)
    small_int_field = models.SmallIntegerField(_("Small Integer"), )
    text_field = models.TextField(_("Text"), blank=True)
    time_field = models.TimeField(_("Time"), null=True, blank=True)
    url_field = models.URLField(_("URL"), max_length=200,)
    uuid_field = models.UUIDField(_("UUID"), default=uuid.uuid4)

    # Relationship Fields
    # https://docs.djangoproject.com/en/dev/ref/models/fields/#module-django.db.models.fields.related
    oto = models.OneToOneField(AA, on_delete=models.CASCADE, null=True, blank=True)
    fk = models.ForeignKey(BB, on_delete=models.CASCADE, related_name="xxs", null=True, blank=True)
    mtm = models.ManyToManyField(CC, blank=True)

    # Automatic date fields
    # https://docs.djangoproject.com/en/dev/ref/models/fields/#datefield
    created = models.DateTimeField(_("DateTime Created"), auto_now_add=True, help_text=_("When this item was created"))
    modified = models.DateTimeField(_("DateTime Modified"), auto_now=True, help_text=_("When this item last updated"))

    # Postgres-specific Range Fields
    # https://docs.djangoproject.com/en/dev/ref/contrib/postgres/fields/
    integer_range_field = IntegerRangeField(_("IntegerRange"))
    big_integer_range_field = BigIntegerRangeField(_("BigIntegerRange"))
    decimal_range_field = DecimalRangeField(_("DecimalRange"))
    date_time_range_field = DateTimeRangeField(_("DateTimeRange"))
    date_range_field = DateRangeField(_("DateRange"))

    # GeoDjango Fields (for GIS projects)
    # https://docs.djangoproject.com/en/dev/ref/contrib/gis/model-api/
    point = models.PointField(srid=4326)
    multi_point = models.MultiPointField()
    polygon = models.PolygonField()
    multi_polygon = models.MultiPolygonField()
    linestring = models.LineStringField()
    multi_linestring = models.MultiLineStringField()
    geometry_collection = models.GeometryCollectionField()
    raster = models.RasterField()

    # Example Fields to demonstrate Manager, QuerySet, and Model methods below
    critical = models.BooleanField(_("Critical"), default=False)
    expired = models.DateTimeField(_("Expired"), null=True, blank=True)

    # Uses the `from_queryset` method to combine Manager & QuerySet
    # https://docs.djangoproject.com/en/4.1/topics/db/managers/#from-queryset
    CombinedMyModelManager = MyModelManager.from_queryset(MyModelQuerySet)
    objects = CombinedMyModelManager()

    # Optional additional manager with no query filter limitations
    unscoped = models.Manager()

    class Meta:
        # https://docs.djangoproject.com/en/dev/ref/models/options/
        verbose_name = _("My Model")
        verbose_name_plural = _("My Models")

        # abstract = True  # https://docs.djangoproject.com/en/dev/topics/db/models/#abstract-base-classes
        # proxy = True  # https://docs.djangoproject.com/en/dev/topics/db/models/#proxy-models

        db_table = 'my_model'  # https://docs.djangoproject.com/en/4.1/ref/models/options/#db-table

        # Specifies the default field(s) to use in your model Manager's latest() and earliest() methods
        get_latest_by = ['-datetime_field']

        # The default ordering for the object, for use when obtaining lists of objects
        ordering = ['-datetime_field']

        # These are Deprecated. Recommended to use Meta  UniqueConstraint and Indexes
        unique_together = [
            ['char_field', 'slug_field']
        ]
        index_together = [
            ["created", "char_field"],
        ]

        # https://docs.djangoproject.com/en/dev/ref/models/indexes/
        indexes = [
            models.Index(fields=['char_field', 'text_field']),
            models.Index(fields=['url_field'], name='url_field_idx'),
        ]

        constraints = [
            # https://docs.djangoproject.com/en/dev/ref/models/constraints/
            # Check that new/updated instances have an `integer_field` with value of 18 or greater
            models.CheckConstraint(check=models.Q(integer_field__gte=18), name='integer_gte_18'),
            # Prevent more than once instance sharing the same `created` and `slug_field` values
            models.UniqueConstraint(fields=['created', 'slug_field'], name='unique_slug_created'),
            # Prevent more than once instance where the `fk` field is the same and the Company Type
            #    is `LIMITED_LIABILITY_PARTNERSHIP` 
            models.UniqueConstraint(
                fields=['fk'],
                condition=Q(char_field_choice=CompanyType.LIMITED_LIABILITY_PARTNERSHIP),
                name='unique_llc_for_fk'
            ),
            # https://docs.djangoproject.com/en/4.1/ref/contrib/postgres/constraints/
            # Exclude new instances of this model for the same `fk` field where there are overlapping
            #    `integer_range_field` range values when the `expired` field is `False`
            ExclusionConstraint(
                name='exclude_overlapping_reservations',
                expressions=[
                    ('integer_range_field', RangeOperators.OVERLAPS),
                    ('fk', RangeOperators.EQUAL),
                ],
                condition=Q(expired__isnull=True),
            ),
        ]

    def __str__(self):
        # https://docs.djangoproject.com/en/dev/ref/models/instances/#str
        return f'{self.char_field} {self.char_field}'

    def save(self, *args, **kwargs):
        # https://docs.djangoproject.com/en/dev/topics/db/models/#overriding-model-methods
        # Here we create a random value and assign to the `slug_field` before actually saving
        if not self.pk: # this will ensure that the object is new
            self.slug_field = create_rand_value(
                app_label=self._meta.app_label,
                model_name=self.__class__.__name__,
                prefix="",
                suffix_len=10
            )
        super().save(*args, **kwargs)

    def get_absolute_url(self):
        # https://docs.djangoproject.com/en/dev/ref/models/instances/#get-absolute-url
        from django.urls import reverse
        return reverse('people.views.details', args=[str(self.id)])

    @property
    def char_slug(self):
        # https://docs.djangoproject.com/en/dev/glossary/#term-property
        return self.char_field.lower().replace(' ','-')

    def expire_item(self):
        # https://docs.djangoproject.com/en/dev/topics/db/models/#model-methods
        # Set the value of `expired` to `timezone.now()` for the current instance
        self.expired = timezone.now()
        self.save()
```

Example usage of the QuerySet, Manager, and Model methods:

```python
# Returns all critical Items
Item.objects.all()

# Returns critical Items that are expired
Item.objects.expired_items()

# Expire a single Item
item = Item.objects.first()
item.expire_item()

# Deletes any critical Items that are expired
Item.objects.delete_expired_items()
```

Have any additional tips/recommendations? Share them. I love to refine this over time.