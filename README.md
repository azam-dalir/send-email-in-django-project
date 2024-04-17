add environment variables for debug and secret key
1-add this code in setting:

from environs import Env
# for environment variables
env = Env()
env.read_env()


ECRET_KEY = env("DJANGO_SECRET_KEY")
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = env.bool("DJANGO_DEBUG")
ALLOWED_HOSTS = ['127.0.0.1', 'localhost', '.herokuapp.com']



2-add this code in docker-compose.yml:
depends_on:
  - db
environment:
  - "DJANGO_SECRET_KEY=django-insecure-b764$$#(&o4o((cvy!z^nsseq5@erq#e^0%5r5a(%c24*f^*sde"
  - "DJANGO_DEBUG=True"



3-install environs in venv and requirements.txt
4-add this code in templates/account/email/email_confirmation_message.txt:

</head>
<body>

{% if user.is_authenticated %}
    <span>Hello {{ user.email }}</span> | <a href="{% url 'account_logout' %}">logout</a>
{% else %}
    <a href="{% url 'account_login' %}">login</a> | <a href="{% url 'account_signup' %}">sign up</a>
{% endif %}

{% block content %}

{% endblock %}


5-add this code in templates/account/email/email_confirmation_subject.txt

{% load i18n %}
{% autoescape off %}
{% blocktrans %}Please confirm email{% endblocktrans %}
{% endautoescape %}


6-add this code in templates/account/email_confirm.html:
{% extends '_base.html' %}

{% load i18n %}
{% load account %}

{% block page_title %}{% trans "Confirm E-mail Address" %}{% endblock %}

{% block content %}
<h1>{% trans "Confirm E-mail Address" %}</h1>

{% if confirmation %}

{% user_display confirmation.email_address.user as user_display %}

<p>{% blocktrans with confirmation.email_address.email as email %}Please confirm that <a href="mailto:{{ email }}">{{ email }}</a> is an e-mail address for user {{ user_display }}.{% endblocktrans %}</p>

<form method="post" action="{% url 'account_confirm_email' confirmation.key %}">
{% csrf_token %}
    <button type="submit">{% trans 'Confirm' %}</button>
</form>

{% else %}

{% url 'account_email' as email_url %}

<p>{% blocktrans %}This e-mail confirmation link expired or is invalid. Please <a href="{{ email_url }}">issue a new e-mail confirmation request</a>.{% endblocktrans %}</p>

{% endif %}

{% endblock %}

7-check templates/_base.html:

</head>
<body>

{% if user.is_authenticated %}
    <span>Hello {{ user.email }}</span> | <a href="{% url 'account_logout' %}">logout</a>
{% else %}
    <a href="{% url 'account_login' %}">login</a> | <a href="{% url 'account_signup' %}">sign up</a>
{% endif %}

{% block content %}

{% endblock %}




