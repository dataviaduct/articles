_In this introductory article, we would like to highlight some background of Data Lakes creation in an abstract Enterprise company. Of course, every case is unique, but we can distinguish certain patterns and anti-patterns which being used over-and-over again by different businesses._

# Problem statement

Pretty much every person who works with data in the enterprise world knows that it can grow at a tremendous pace. Every corporate subsystem, every appliance or application generates bits of information - business data itself, data about the data (aka metadata), logs, metrics, etc. All these get stored in one way or another. For a long period, it was either independent service storage where data got eventually deleted according to the retention policies, or some central corporate storage, where it left forever consuming space and money, and eventually died from a business value perspective. 

<img align="right" src="./images/org_structure.png"/>

Writing about business it's worth to mention, that usually it's not a monolith, but rather a distributed branched structure. It's especially true if we're talking about big enterprises, where each department can have its own processes, development and analytics teams, relatively independent goals, and of course, own entity we're particularly interested in - **the data**. 

From time to time, datasets from different departments need to be merged together to comprise some global data-product. This brings the following obvious questions:

* Where to search for the missing piece of data for the resulting data-product? There can be hundreds of departments and thousands of data-sources.
* How to request permissions to get access to that data? I.e., who is the owner and what is the process to follow? 
* How to get (aka download) the actual dataset? 

Gathering all this information takes time. And usually, the larger the enterprise is, the more time it takes to answer them. 

<img align="left" src="./images/data_science.png"/>

The good part here is that number of such global reports, comprised of many data sources of different departments were static or at least not quickly increasing in numbers. 

That was the case for some time, until the Data Science area opened sort of Pandora's Box, showing that even data that were considered garbage before, now can produce some interesting and valuable results when combined together. Now analytics and data scientists suddenly want to have access to all tiny pieces of information distributed across who-knows-where.

Now when we have a situation on the plate, we will describe hypothetical course of events, which unfortunately take places more times when desired and often result in time and money waste.

# It was working 15 years ago. It should work now as well.

What interface IT can provide in such circumstances? Most probably nobody was preparing nothing for such a use-case and business wants this to be ready 'yesterday'. The answer lies on the surface - the use of well-known time-proven technologies. So, let's take all data, and ingest it in some way of RDBMS. Multiple things usually go wrong here. 

How performant the relational database is extremely dependent on the effective schema design. We would need to have highly-experienced developers with DBA-background who should be able to take semi-structured or unstructured data-sets and transform them into an efficient relational structure. It's hard to underestimate the importance of this step as if we will find some flaws in the future and need to change the schema, it certainly be quite a painful process.

But even if the schema was designed perfectly, we still might face the same issue. Our data-sources are independent services mostly, part of them we might not even control. They have their own lifecycles, which might bring changes in the data structures at totally random points for us.

The next flaw of relational databases, which comes from the good old times, is the fact that computing and storage are tightly coupled. It's either quite hard or impossible to scale these two resources independently. Most of the time we have either under- or over-provisioning, which leads, in its turn, to a bad query performance or a money waste respectively. 

Scaling is another thing to consider, because RDBMS tends to scale vertically, which has its physical limits. Proper horizontal scaling again requires a lot of expertise and tendency to the future telling.

Considering all above, as a result of this approach business usually gets expensive quite-complicated hard-to-scale and not-so-fast huge DWH cluster.

# One step forward and two steps back?

As time goes by, data still grows as well as the number of data users and complexity of their queries, making RDMBS-based solution less and less useful. One of the options to go from that point is some modern distributed NoSQL database, which, according to the Internet and its vendor, fits all imaginable cases and scales horizontally almost infinitely. Right? Well, not exactly. As an output of such an approach, we'll have most probably over-expensive extremely complicated, and not-so-fast cluster, which, in addition to all that, goes down from time-to-time because of its infrastructure complexity, lack of expertise, and still-growing query load. 

**Disclaimer**: "_I suppose it is tempting, if the only tool you have is a hammer, to treat everything as if it were a nail_" (c). In other words, every tool is good for the case it was designed to. Both RBDMS and NoSQL databases are work extremely good for 'their' cases.

# All roads lead to ~~Rome~~ Data Lake

Finally, company may come to the idea of building a Data Lake. The Data Lake. A '_silver bullet_' which should solve all problems and issues. And as nowadays we have clouds, it should not take too much time, right? Again, not exactly, unfortunately. 

Data Lake tend to be complex solutions, especially when you're trying to build one from the ground without having proper experience and expertise. We personally saw many projects starting Data Lake initiative, that never made it to the production. There are multiple reasons for that.

<img align="right" src="./images/choises.jpg"/>

Let's start with the environment. Obviously if we want to launch something in a reasonable time nowadays, we have to use a cloud provider. And here goes the first question: which one? We have big three players, and dozens of smaller ones, which offers pretty much similar, at least on the paper, functionality and pricing.

If we managed to choose the cloud somehow, we have to choose how we're going to deploy the solution. Should we rely on plain **virtual machines** and deploy all services there or use **cloud managed services**? 

## VM-based setup

Very tempting to start such a project using virtual machines. The common fallacy here is the following - it's a familiar approach, which means easy, quick and flexible implementation. In addition to that, we can be cloud-agnostic as VM is still a VM regardless of the provider. 

Let's try to put away the veil of prejudice, at operate with facts. Here is the list of big pieces, **apart from the business logic**, we would have to implement on our own, which are pretty much hidden from us in cloud-managed services:

* Automation of every service deployment and their core configuration;
* Updates management, resolving versioning conflicts (version lock);
* Security patch management;
* At least basic monitoring;
* High availability;
* Some backup mechanisms;
* Support and incident resolution;

Given the above, the only way when it makes sense to go with a DIY approach is when you are obliged to deploy the solution on-prem. In all other cases, clouds are the way to go. 

## Cloud managed services

If we choose later option, so called serverless approach, what exactly we're going to use? AWS for example doubled their services in 2019 and a lot of them have similar or even identical functionality. Just not to be unfounded, let's take AWS orchestration tools for the ETL jobs as an example: 

* AWS Step Functions
* AWS Glue Workflow
* MWAA - Amazon Managed Workflows for Apache Airflow
* And you can actually build one yourself using other managed services like Lambda

One of paths companies may choose is to create a multiple PoC-teams to iterate over possible approaches, choose the most appropriate ones and maybe even deliver some MVP based on them. How long it will take and if this approach be successful depends on many factors: team expertise and experience, proper team and cross-team coordination, budget and realistic time goals. In other words, the road is long and rough, and the results are vague.

Every reasonable person would ask here a perfectly valid question about framework. If someone is writhing a web service, it’s a rare case they write everything from point zero, as there are tons or different web frameworks. If someone is trying to deploy Kubernetes cluster, they also won’t go with reinventing the wheel as there are dozens of different tools what will do all routine for you. 

With Data Lakes, as you may already know, the situation is not so promising. There are almost no open-source frameworks that can create and configure at least core infrastructure layers for the Data Lake, which are:

* Storage
* Metadata Catalog
* ETL jobs
* ETL orchestration
* Exploratory environment

We should also not forget that having these as independent entities won’t give us much. All layers should be bind together with common interfaces and appropriate security permissions.  

<img align="center" src="./images/datalake_as_code.png"/>

In this series of articles, we would like to show how we can build a **typical** Data Lake for a **typical** enterprise with **typical** requirements in a really **fast** and **fully automated** manner. Next chapter will be dedicated to the more detailed description of layers we mentioned above, the ‘_glue_’ which ties them all together, and the Terraform-based automation framework which allows us to easily deploy our solution to the AWS environment. 

Thank you for your attention and stay tuned!

---

**Authors**: Artem Penner, Daniil Sabadash.

**Published Online**: January 2021

**Licence**: Creative Commons Attribution-NonCommercial License, [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/).

---

Used illustrations (not part of the article):

<ul>
  <li><a href="https://www.freepik.com/vectors/abstract">Abstract vector created by iuriimotov - www.freepik.com</a></li>
  <li><a href="https://www.freepik.com/vectors/background">Background vector created by rawpixel.com - www.freepik.com</a></li>
  <li><a href="https://www.freepik.com/vectors/road">Road vector created by macrovector - www.freepik.com</a></li>
</ul>
