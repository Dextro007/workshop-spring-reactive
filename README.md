<!--- STARTEXCLUDE --->
# Reactive Implementation of 🐈 Spring PetClinic 🐕


[![Gitpod ready-to-code](https://img.shields.io/badge/Gitpod-ready--to--code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/datastaxdevs/workshop-streaming-game)
[![License Apache2](https://img.shields.io/hexpm/l/plug.svg)](http://www.apache.org/licenses/LICENSE-2.0)
[![Discord](https://img.shields.io/discord/685554030159593522)](https://discord.com/widget?id=685554030159593522&theme=dark)


*50 minutes, Intermediate, [Start Building](https://github.com/DataStax-Examples/spring-petclinic-reactive#prerequisites)*

![banner](doc/img/banner.png?raw=true)

This sample is a fully reactive version of the Spring PetClinic application using Spring WebFlux.
<!--- ENDEXCLUDE --->


## :clipboard: Table of content

<img src="https://github.com/datastaxdevs/workshop-spring-reactive/blob/master/doc/img/ui-top.png?raw=true" align="right" width="400px"/>


1. [Objectives](#1-objectives)
2. [Frequently asked questions](#1-objectives)
3. [Materials for the Session](#2-frequently-asked-questions)
4. [Create your Database](#3-materials-for-the-session)
5. [Create your Schema](#3-materials-for-the-session)
6. [Create your Token](#3-materials-for-the-session)

5. [Hands-on - Work with Drivers](#1-objectives)
6. [Hands-on - Work with Spring](#1-objectives)
7. [Hands-on - User Interface](#1-objectives)
8. [Homeworks](#1-objectives)


## 1. Objectives

* *Learn how Apache Cassandra **data modelling** is different from relational*
* *Understand how **Java Applications** connect to Apache Cassandra™*
* *Learn about **Spring and Spring Boot** Microservices*
* *Understand what are the benefit a **Reactive Programming*** 
* ***Get a working full stack application Spring Boot-Data-Reactive***

## 2. Frequently asked questions
<p/>
<details>
<summary><b> 1️⃣ Can I run the workshop on my computer?</b></summary>
<p>There is nothing preventing you from running the workshop on your own machine, If you do so, you will need the following
<ol>
<li><b>git</b> installed on your local system
<li><b>JDK 8+</b> installed on your local system
<li><b>Maven 3.6+</b> installed on your local system
<li><a href="https://www.whitesourcesoftware.com/free-developer-tools/blog/update-node-js/" >Node 15 and npm 7 or later</a>
</ol>
</p>
In this readme, we try to provide instructions for local development as well - but keep in mind that the main focus is development on Gitpod, hence **We can't guarantee live support** about local development in order to keep on track with the schedule. However, we will do our best to give you the info you need to succeed.
</details>
<p/>
<details>
<summary><b> 2️⃣ What other prerequisites are there?</b></summary>
<ul>
<li>You will need a GitHub account
<li>You will also need an Astra account: don't worry, we'll work through that in the following
</ul>
</p>
</details>
<p/>
<details>
<summary><b> 3️⃣ Do I need to pay for anything for this workshop?</b></summary>
<b>No.</b> All tools and services we provide here are FREE.
</details>
<p/>
<details>
<summary><b> 4️⃣ Will I get a certificate if I attend this workshop?</b></summary>
Attending the session is not enough. You need to complete the homeworks detailed below and you will get a nice badge.
</details>
<p/>

## 3. Materials for the Session

It doesn't matter if you join our workshop live or you prefer to work at your own pace,
we have you covered. In this repository, you'll find everything you need for this workshop:

- [Slide deck](doc/slides.pdf)
- [Datastax Developers Discord chat](https://bit.ly/cassandra-workshop)
- [Questions and Answers](https://community.datastax.com/)


## 4. Create Astra DB Instance

**`ASTRA DB`** is the simplest way to run Cassandra with zero operations at all - just push the button and get your cluster. No credit card required, $25.00 USD credit every month, roughly 5M writes, 30M reads, 40GB storage monthly - sufficient to run small production workloads.

#### ✅ Step 4a. 

If you do have an account yet register and sign In to Astra DB this is FREE and NO CREDIT CARD asked** [https://astra.datastax.com](https://astra.dev/11-22): You can use your `Github`, `Google` accounts or register with an `email`.

_Make sure to chose a password with minimum 8 characters, containing upper and lowercase letters, at least one number and special character_

#### ✅ Step 4b. Create a "FREE" plan

Follow this [guide](https://docs.datastax.com/en/astra/docs/creating-your-astra-database.html), to set up a pay as you go database with a free $25 monthly credit. You will find below recommended values to enter:

- **For the database name** - `workshops`

- **For the keyspace name** - `spring_petclinic`

_You can technically use whatever you want and update the code to reflect the keyspace. This is really to get you on a happy path for the first run._

- **For provider and region**: Choose a provider (GCP, Azure or AWS) and then the related region is where your database will reside physically (choose one close to you or your users).

- **Create the database**. Review all the fields to make sure they are as shown, and click the `Create Database` button.

You will see your new database `pending` in the Dashboard.

![my-pic](doc/img//db-pending.png?raw=true)

The status will change to `Active` when the database is ready, this will only take 2-3 minutes. You will also receive an email when it is ready.

**👁️ Walkthrough**

*The Walkthrough mentionned the wrong keyspace, make sure to use `spring_petclinic`*

![image](doc/img/astra-create-db.gif?raw=true)


## 5. Create your schema with CQL Console

#### ✅ 5a. Open CqlConsole and select your Keyspace

As seen in the slides on the contrary of relational you start with the request and data model BEFORE CODING.

![image](doc/img/db-cqlconsole-1.png?raw=true)

 Let's create the tables. In the console use the command:

```sql
use spring_petclinic;
```

![image](doc/img/db-cqlconsole-2.png?raw=true)

#### ✅ 5b. Create tables

This is the data model we are looking for with 6 tables.

![Pet Clinic Welcome Screen](doc/img/data-model.png?raw=true)

Execute the following in the cql console

```sql
DROP INDEX IF EXISTS petclinic_idx_vetname;
DROP INDEX IF EXISTS petclinic_idx_ownername;
DROP TABLE IF EXISTS petclinic_vet;
DROP TABLE IF EXISTS petclinic_vet_by_specialty;
DROP TABLE IF EXISTS petclinic_reference_lists;
DROP TABLE IF EXISTS petclinic_owner;
DROP TABLE IF EXISTS petclinic_pet_by_owner;
DROP TABLE IF EXISTS petclinic_visit_by_pet;

CREATE TABLE IF NOT EXISTS petclinic_vet (
  id          uuid,
  first_name  text,
  last_name   text,
  specialties set<text>,
  PRIMARY KEY ((id))
);


CREATE TABLE IF NOT EXISTS petclinic_vet_by_specialty (
 specialty   text,
 vet_id      uuid,
 first_name  text,
 last_name   text,
 PRIMARY KEY ((specialty), vet_id)
);


CREATE TABLE IF NOT EXISTS petclinic_owner (
  id         uuid,
  first_name text,
  last_name  text,
  address    text,
  city       text,
  telephone  text,
  PRIMARY KEY ((id))
);

CREATE TABLE IF NOT EXISTS petclinic_pet_by_owner (
  owner_id   uuid,
  pet_id     uuid,
  pet_type   text,
  name       text,
  birth_date date,
  PRIMARY KEY ((owner_id), pet_id)
);

CREATE TABLE IF NOT EXISTS petclinic_visit_by_pet (
   pet_id      uuid,
   visit_id    uuid,
   visit_date  date,
   description text,
   PRIMARY KEY ((pet_id), visit_id)
);

CREATE TABLE IF NOT EXISTS petclinic_reference_lists (
  list_name text,
  values set<text>,
  PRIMARY KEY ((list_name))
);

/** We could search veterinarians by their names. */
CREATE INDEX IF NOT EXISTS petclinic_idx_ownername ON petclinic_owner(last_name);
/** We could search vet by their names. */
CREATE INDEX IF NOT EXISTS petclinic_idx_vetname ON petclinic_vet(last_name);
```

#### ✅ 5c. Check our 6 tables

```sql
describe tables;
```

#### ✅ 5d. Insert Reference Data

```sql
INSERT INTO petclinic_reference_lists(list_name, values) 
VALUES ('pet_type ', {'bird', 'cat', 'dog', 'lizard','hamster','snake'});

INSERT INTO petclinic_reference_lists(list_name, values) 
VALUES ('vet_specialty', {'radiology', 'dentistry', 'surgery'});
```

## 6. Create Astra Token

To connect to the database from Java code we need some credentials, this is what we are doing here.

#### ✅ Step 4a: Generate Token

Following the [Manage Application Tokens docs](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html) create a token with `Database Admnistrator` roles.

- Go the `Organization Settings`

- Go to `Token Management`

- Pick the role `Database Admnistrator` on the select box

- Click Generate token

**👁️ Walkthrough**

![image](doc/img/astra-create-token.gif?raw=true)

This is what the token page looks like. You can now download the values as a CSV. We will need those values but you can also keep this window open for use later.

![image](images/tutorials/astra-token.png?raw=true)

Notice the clipboard icon at the end of each value.

- `clientId:` We will use it as a _username_ to contact Cassandra

- `clientSecret:` We will use it as a _password_ to contact Cassandra

- `appToken:` We will use it as a api token Key to interact with APIs.

To know more about roles of each token you can have a look to [this video.](https://www.youtube.com/watch?v=nRqu44W-bMU)

**Note: Make sure you don't close the window accidentally or otherwise - if you close this window before you copy the values, the application token is lost forever. They won't be available later for security reasons.**

We are now set with the database and credentials. Let's start coding with Spring !

[🏠 Back to Table of Contents](#table-of-content)


### 3. Start in Gitpod

**✅ Open Gitpod (with creds copied to clipboard)**

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/datastaxdevs/workshop-spring-reactive)

When you first launch gitpod, it builds the image.
![image](doc/img/building-workspace.png?raw=true)

Git pod pulls the image.
![image](doc/img/pulling-image.png?raw=true)

**✅ Paste credentials in Gitpod terminal**

Once Gitpod loads the workspace, you'll be asked to paste your service account credentials in the Gitpod terminal at the bottom of the screen. The [setup.sh](setup.sh) script at the root of the repository is what asks this question.

![image](doc/img/script-copy-creds.png?raw=true)

**✅ Open Swagger UI in browser**

When gitpod finishes building the app, a new tab will open in your browser showing the following.

![image](doc/img/exec-start.png?raw=true)

**🎉 Celebrate!**

You've successfully built the Spring Petclinic Reactive backend application!

![image](doc/img/exec-api-page.png?raw=true)

**✅ Start the Web UI** :

You may have noticed another terminal named `spring-petclinic-angular`. This is where the UI should start.

![image](doc/img/start-ui.png?raw=true)

After answering the question about analytics usage, you should be able to access the UI on a new tab.

![Pet Clinic Welcome Screen](doc/img/ui-top.png?raw=true)

**NOTE** If you want to run everything locally, reference the [LOCAL_README.md](doc/LOCAL_README.md)

## Understand the architecture

### Internal Architecture our of component

Let's have a look inside the main component `spring-petclinic-reactive` to see which libraries and frameworks have been used.

![Pet Clinic Welcome Screen](doc/img/internal-architecture.png?raw=true)

- `Spring-boot`: Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run". We take an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. Most Spring Boot applications need minimal Spring configuration.

- `Spring-Security`: Spring Security is a powerful and highly customizable authentication and access-control framework. It is the de-facto standard for securing Spring-based applications. Spring Security is a framework that focuses on providing both authentication and authorization to Java applications. Like all Spring projects, the real power of Spring Security is found in how easily it can be extended to meet custom requirements.

- `Spring-WebFlux`: Spring sub framework allowing to create Reactive Rest Endpoint.

- `Spring-Actuator`: Expose Endpoints to expose metrics to third party system: health, infos, jmx,prometheus,...

- `Spring-Test`: Enabled unit testing and mocking with Spring configuration and beans.

- `Spring-Cloud`: Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems (e.g. configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus, one-time tokens, global locks, leadership election, distributed sessions, cluster state). Coordination of distributed systems leads to boiler plate patterns, and using Spring Cloud developers can quickly stand up services and applications that implement those patterns. They will work well in any distributed environment, including the developer’s own laptop, bare metal data centres, and managed platforms such as Cloud Foundry.

- `SpringFox` *(Swagger)*: Annotation based rest documentation generation and test client generation (swagger-ui).

![Pet Clinic Welcome Screen](doc/img/swagger.png?raw=true)


### Logical Architecture

![Pet Clinic Welcome Screen](doc/img/logical-architecture.png?raw=true)

Here, you can find a description of the logical architecture components:

- `spring-parclinic-angular`: This is the existing project that provides a user interface implementation using Angular. It has been used as well for other backend projects like the
**spring-petclinic-rest**

- `prometheus`: Our component exposes some metrics through the actuator endpoint. A registry will push this information into the Prometheus database (docker-based).

![Pet Clinic Welcome Screen](doc/img/prometheus.png?raw=true)

- `Grafana`: Allows to create dashboards based on data stored in prometheus.

![Pet Clinic Welcome Screen](doc/img/grafana.png?raw=true)

- `zipkin`: Our component includes the `spring-cloud-sleuth` dependency allowing Brave to push metrics usage of the API to the distributed tracing component Zipkin. To enable this tracing
set the property `zipkin.enabled` to true in `application.yaml`.
To start zipkin use `docker-compose up -d`

```
  zipkin:
    enabled: true
    baseUrl: http://localhost:9411
    sender:
      type: web
```

![Pet Clinic Welcome Screen](doc/img/zipkin.png?raw=true)

- `Apache Cassandra`: A NoSQL database

- `DataStax Astra` : Apache Cassandra available in the Cloud for free as a managed service (DBaas)


### Data Model diagram

The underlying data model implemented in Apache Cassandra is different from the one you would have defined with a relational database.

![Pet Clinic Welcome Screen](doc/img/data-model.png?raw=true)

To enable scalability, Apache Cassandra does not support joins or integrity constraints. Therefore we used some denormalization.
We also created some `secondary indices` to queries columns that are not the PARTITION KEY. These secondary indices work well in this case because the cardinality is low (e.g, few pets for an owner).

The application generates the objects related to the data model (e.g., tables, indices, udts) at startup.

## Homework

<img src="images/streaming-workshop.png?raw=true" width="200" align="right" />

Don't forget to complete your assignment and get your verified skill badge! Finish and submit your homework!

1. Complete the practice steps as described below until you have your own app running in Gitpod.
2. Now roll up your sleeves and modify the code in two ways: (1) we want the API to send a greeting to each new player in the chat box, and (2) we want the player names in the game area to match the icon color. _Please read the detailed guidance found [below](#6-homework-instructions)_.
3. Take a SCREENSHOT of the running app modified this way. _Note: you will have to restart the API and reload the client to see all changes!_
4. Submit your homework [here](https://dtsx.io/streaming-game-homework).

That's it, you are done! Expect an email in a few days!

