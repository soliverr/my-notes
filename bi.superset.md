---
id: au23651c53hwtdjy4yom1b0
title: Apache Superset
desc: ''
updated: 1680105106369
created: 1617891785340
---

* [Apache Superset](https://superset.apache.org/) is a modern data exploration and visualization platform.
* [Preset Cloud](https://preset.io/product/) - Fully-managed, open-source BI for the modern data stack. Preset Cloud is a fully-hosted, hassle-free cloud service for Apache Superset™. Get started for free today!

# Install

## From scratch

```
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev
```

_numpy не поддерживает Python > 3.9_

**Here we name the virtual env 'superset'**

```sh
pyenv install 3.8.12
pyenv virtualenv 3.8.12 superset
pyenv activate superset

pip install apache-superset
```

```sh
# Для версии 2.1.1 ошибка в использовании модуля
pip install 'markupsafe==2.0.1'
```

## Коннекторы к базам

* https://superset.apache.org/docs/databases/installing-database-drivers

**Trino:**

```sh
pip install sqlalchemy-trino
```

_trino://{username}:{password}@{hostname}:{port}/{catalog}_

```sh
superset db upgrade

# Create an admin user in your metadata database (use `admin` as username to be able to load the examples)
export FLASK_APP=superset
superset fab create-admin

# Load some data to play with
superset load_examples

# Create default roles and permissions
superset init
```

## Start development Web server

To start a development web server on port 8088, use -p to bind to another port

```sh
superset run -p 8088 --with-threads --reload --debugger
```

# Embed Apache Superset dashboard

* [Superset Embedded SDK](https://www.npmjs.com/package/@superset-ui/embedded-sdk)
* [How to embed an Apache Superset dashboard in a webpage? - Stack Overflow](https://stackoverflow.com/questions/54219101/how-to-embed-an-apache-superset-dashboard-in-a-webpage)
* [Embedding Superset dashboards in your React application](https://medium.com/@khushbu.adav/embedding-superset-dashboards-in-your-react-application-7f282e3dbd88)

## Dashboard UUID

To ensure that the dashboard is accessible from our react app, we need to first enable the “embedded” support feature in the super set config.

Это делается через Web UI в Superset.

## Interacting with Embedded Dashboard Superset APIs
### API to get JWT access token

Для начала нужен **JWT access token**:

```
/api/v1/security/login
```

```json
{
    "message": {
        "provider": [
            "Must be one of: db, ldap."
        ]
    }
}
```
en

### Authentication/Authorization with Guest Tokens

Embedded resources use a special auth token called a Guest Token to grant Superset access to your users, without requiring your users to log in to Superset directly. Your backend must create a Guest Token by requesting Superset's POST `/security/guest_token` endpoint, and pass that guest token to your frontend.

```
/api/v1/security/guest_token/
```

### Security

* https://apache-superset.readthedocs.io/en/0.35.1/security.html

**Public**

It’s possible to allow logged out users to access some Superset features.

By setting `PUBLIC_ROLE_LIKE_GAMMA = True` in your `superset_config.py`, you grant public role the same set of permissions as for the **GAMMA** role. This is useful if one wants to enable anonymous users to view dashboards. _Explicit grant on specific datasets **is still required**, meaning that you need to edit the Public role and add the Public data sources to the role manually._



# Using 

* [Table chart: Conditional formatting assigns color to N/A values](https://github.com/apache/superset/issues/21341)