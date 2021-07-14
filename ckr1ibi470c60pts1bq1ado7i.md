## Cheatsheet for Django Models

Basic cheatsheet for django models
----------------------------------

Sometimes it's useful to have a condensed version of highly detailed information.

This resource is intended mainly for folks who are regularly building out models, are comfortable with the concepts of django models, and want a consolidated listing of the majority of things available for models. I use this quite often, and it saves me a lot of time since I generally don't need to look up the full documentation. I can see at a glance what a particular field, Meta class option, QuerySet, etc should look like.

I've previously posted prior versions of this elsewhere, so you may have seen something similar, but this is the latest and most up-to-date version.

```python
import uuid
from django.db import models
# Use the import below instead, if using GeoDjango fields
# from django.contrib.gis.db import models
from django.utils.translation import ugettext_lazy as _

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

# https://docs.djangoproject.com/en/3.1/topics/db/managers/#from-queryset
class MyModelQuerySet(models.QuerySet):
    """Used when we need to return a queryset that has been filtered in some way"""
    def manager_and_queryset_method(self):
        return

class MyModelManager(models.Manager):
    """Used when we need a table-level method that does NOT return a queryset"""
    def manager_only_method(self):
        return

CombinedMyModelManager = MyModelManager.from_queryset(MyModelQuerySet)

class MyModel(models.Model):
    """Comment"""

    class CompanyType(models.TextChoices):
        PUBLIC_LIMITED_COMPANY = 'PLC', _('Public Limited Company')
        PRIVATE_COMPANY_LIMITED = 'LTD', _('Private Company limited by shares')
        LIMITED_LIABILITY_PARTNERSHIP = 'LLP', _('Limited Liability Partnership')
        __empty__ = _('(Unknown)')

    # https://docs.djangoproject.com/en/3.1/ref/models/fields/
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

    # Relationships
    # https://docs.djangoproject.com/en/3.1/ref/models/fields/#module-django.db.models.fields.related
    oto = models.OneToOneField(AA, on_delete=models.CASCADE, null=True, blank=True)
    fk = models.ForeignKey(BB, on_delete=models.CASCADE, related_name="xxs", null=True, blank=True)
    mtm = models.ManyToManyField(CC, blank=True)

    # Auto-date fields
    # https://docs.djangoproject.com/en/3.1/ref/models/fields/#datefield
    created = models.DateTimeField(_("DateTime Created"), auto_now_add=True, help_text=_("When this item was created"))
    modified = models.DateTimeField(_("DateTime Modified"), auto_now=True, help_text=_("When this item last updated"))

    # Postgres-specific range fields
    # https://docs.djangoproject.com/en/3.1/ref/contrib/postgres/fields/
    integer_range_field = IntegerRangeField(_("IntegerRange"))
    big_integer_range_field = BigIntegerRangeField(_("BigIntegerRange"))
    decimal_range_field = DecimalRangeField(_("DecimalRange"))
    date_time_range_field = DateTimeRangeField(_("DateTimeRange"))
    date_range_field = DateRangeField(_("DateRange"))

    # GeoDjango fields
    # https://docs.djangoproject.com/en/3.1/ref/contrib/gis/model-api/
    point = models.PointField(srid=4326)
    multi_point = models.MultiPointField()
    polygon = models.PolygonField()
    multi_polygon = models.MultiPolygonField()
    linestring = models.LineStringField()
    multi_linestring = models.MultiLineStringField()
    geometry_collection = models.GeometryCollectionField()
    raster = models.RasterField()

    objects = CombinedMyModelManager()

    class Meta:
        # https://docs.djangoproject.com/en/3.1/ref/models/options/
        verbose_name = _("My Model")
        verbose_name_plural = _("My Models")
        # abstract = True  # https://docs.djangoproject.com/en/3.1/topics/db/models/#abstract-base-classes
        # proxy = True  # https://docs.djangoproject.com/en/3.1/topics/db/models/#proxy-models

        db_table = 'my_model'

        # Specifies the default field(s) to use in your model Manager's latest() and earliest() methods.
        get_latest_by = ['-datetime_field']

        # The default ordering for the object, for use when obtaining lists of objects:
        ordering = ['-datetime_field']

        unique_together = [
            ['char_field', 'slug_field']
        ]
        index_together = [
            ["created", "char_field"],
        ]

        # https://docs.djangoproject.com/en/3.1/ref/models/indexes/
        indexes = [
            models.Index(fields=['char_field', 'text_field']),
            models.Index(fields=['url_field'], name='url_field_idx'),
        ]

        # https://docs.djangoproject.com/en/3.1/ref/models/constraints/
        constraints = [
            models.CheckConstraint(check=models.Q(integer_field__gte=18), name='integer_gte_18'),
            models.UniqueConstraint(fields=['created', 'slug_field'], name='unique_slug_created'),
            models.UniqueConstraint(
                fields=['fk'],
                condition=Q(char_field_choice=CompanyType.LIMITED_LIABILITY_PARTNERSHIP),
                name='unique_llc_for_fk'
            ),
        ]

    def __str__(self):
        # https://docs.djangoproject.com/en/3.1/ref/models/instances/#str
        return f'{self.char_field} {self.char_field}'

    def save(self, *args, **kwargs):
        # https://docs.djangoproject.com/en/3.1/topics/db/models/#overriding-model-methods
        if not self.pk: # this will ensure that the object is new
            self.slug_field = create_rand_value(
                app_label=self._meta.app_label,
                model_name=self.__class__.__name__,
                prefix="",
                suffix_len=10
            )
        super().save(*args, **kwargs)

    def get_absolute_url(self):
        # https://docs.djangoproject.com/en/3.1/ref/models/instances/#get-absolute-url
        from django.urls import reverse
        return reverse('people.views.details', args=[str(self.id)])

    @property
    # https://docs.djangoproject.com/en/3.1/glossary/#term-property
    def char_slug(self):
        return self.char_field.lower().replace(' ','-')

    def process_invoices(self):
        do_something()
```

I hope you find this helpful. I have been working with django for a few years now, and regularly contribute to OSS projects & packages, but I'm still pretty new to blogging.