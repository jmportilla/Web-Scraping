# Miniquiz:

In this quiz, we'll be analyzing data from from a ridesharing app called "Hitch".

**Background:** schema, datatypes, and schema relationships:

**Assume a PostgreSQL database, server timezone is UTC.**

Table Name: **trips**

| Column Name: | Datatype: |
|----|---------|
| id | integer |
| client_id integer | (Foreign keyed to users.usersid) |
| driver_id integer | (Foreign keyed to users.usersid) |
| city_id | integer |
| client_rating | integer |
| driver_rating | integer |
| request_device | Enum(‘android’, ‘iphone’, ‘sms’, ‘mobile_web’) |
| status | Enum(‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’) |
| predicted_eta | integer |
| actual_eta | integer |
| request_at | timestamp with timezone |

Table Name: **users**


| Column Name: | Datatype: |
|---------|---------|
| usersid | integer |
| email | character varying |
| firstname | character varying |
| lastname | character varying |
| banned | Boolean |
| role | Enum(‘client’, ‘driver’, ‘partner’) |
| creationtime | timestamp with time zone |

**Exercise:** Its common at Hitch to want to know various business metrics about recent trips. Given the above subset of Hitch's schema, write executable SQL queries to answer the following questions:

1. Between `12/1/2013 10:00:00 PST` and `12/8/2013 17:00:00 PST`, how many completed trips were requested on `iphones` in City #5? _(Hint: look at the `trips.status` column)_ How about on `android` phones?

2. In City #8, how many unique, currently unbanned clients completed a trip in October 2013? Of these, how many trips did each client take?

3. In City #8, how many unique, currently unbanned clients completed a trip between 9/10/2013 and 9/20/2013 with drivers who started between 9/1/2013 and 9/10/2013 and are currently banned?

**Extra Credit:** Add to your statement in 2) to exclude Hitch admins. Hitch admins have an email
address from `@hitch.com` (example: ‘jsmith@hitch.com’).

## SQL Schema

```sql
CREATE TYPE trips_request_device_enum AS ENUM (
    'android',
    'iphone',
    'sms',
    'mobile_web'
);


CREATE TYPE trips_status_enum AS ENUM(
    'completed',
    'cancelled_by_driver',
    'cancelled_by_client'
);

CREATE TYPE users_role_enum AS ENUM(
    'client',
    'driver',
    'partner'
);

CREATE TABLE trips (
    id integer NOT NULL,
    client_id integer NOT NULL,
    driver_id integer,
    client_rating integer,
    driver_rating integer,
    request_device trips_request_device_enum,
    status trips_status_enum,
    predicted_eta integer,
    actual_eta integer,
    city_id integer DEFAULT 1 NOT NULL,
    request_at timestamp with time zone
);

CREATE TABLE users (
    usersid integer NOT NULL,
    email character varying(100),
    firstname character varying(45),
    lastname character varying(45),
    banned boolean DEFAULT false,
    role users_role_enum,
    creationtime timestamp with time zone
);
```
