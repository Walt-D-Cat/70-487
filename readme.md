# 70-487 Exam Prep Notes

- [70-487 Exam Prep Notes](#70-487-exam-prep-notes)
  - [Journal](#journal)
    - [2018-09-14](#2018-09-14)
  - [General Notes](#general-notes)
    - [Resources](#resources)
  - [Note Organized by Topic](#note-organized-by-topic)
    - [Accessing Data (20-25%)](#accessing-data-20-25)
      - [Choose data access technologies](#choose-data-access-technologies)
        - [ADO.NET](#adonet)
        - [Entity Framework](#entity-framework)
        - [WCF Data Services](#wcf-data-services)
        - [Cosmos DB](#cosmos-db)
      - [Implement caching](#implement-caching)
      - [Implement transactions](#implement-transactions)
      - [Implement data storage in Microsoft Azure](#implement-data-storage-in-microsoft-azure)
      - [Create and implement a WCF Data Services service](#create-and-implement-a-wcf-data-services-service)
    - [Querying and Manipulating Data by Using the Entity Framework (20-25%)](#querying-and-manipulating-data-by-using-the-entity-framework-20-25)
      - [Query and manipulate data by using the Entity Framework](#query-and-manipulate-data-by-using-the-entity-framework)
      - [Query and manipulate data by using Data Provider for Entity Framework](#query-and-manipulate-data-by-using-data-provider-for-entity-framework)
      - [Query data by using LINQ to Entities](#query-data-by-using-linq-to-entities)
      - [Query and manipulate data by using ADO.NET](#query-and-manipulate-data-by-using-adonet)
      - [Create an Entity Framework data model](#create-an-entity-framework-data-model)
      - [Implement Entity Framework with third party databases](#implement-entity-framework-with-third-party-databases)
    - [Creating and Consuming Web API-based services (20-25%)](#creating-and-consuming-web-api-based-services-20-25)
      - [Design a Web API](#design-a-web-api)
      - [Implement a Web API](#implement-a-web-api)
      - [Secure a Web API](#secure-a-web-api)
      - [Host and manage Web API](#host-and-manage-web-api)
      - [Consume Web API web services](#consume-web-api-web-services)
    - [Designing and Implementing Web Services (15-20%)](#designing-and-implementing-web-services-15-20)
      - [Consume a WCF service](#consume-a-wcf-service)
      - [Implement serverless Azure Web Services](#implement-serverless-azure-web-services)
      - [Implement traffic management in Azure](#implement-traffic-management-in-azure)
      - [Implement Azure API Management](#implement-azure-api-management)
      - [Monitor web services](#monitor-web-services)
    - [Deploying Web Applications and Services (15-20%)](#deploying-web-applications-and-services-15-20)
      - [Design a deployment strategy](#design-a-deployment-strategy)
      - [Choose a deployment strategy for Azure](#choose-a-deployment-strategy-for-azure)
      - [Configure a web application for deployment](#configure-a-web-application-for-deployment)
      - [Manage packages by using NuGet](#manage-packages-by-using-nuget)
      - [Share assemblies between multiple applications and servers](#share-assemblies-between-multiple-applications-and-servers)

## Journal

### 2018-09-14

Restarted the learning plan.  I've run through the _Azure_ primer on [_PluralSight_](https://www.pluralsight.com/courses/dd24bd67-7526-48d4-a313-a0a5ff82ad2d), yes extremely general.  Does a reasonable job of high level overview.

## General Notes

### Resources

Resource | Type | Current | Notes
:--- | :--- | --- | :--
[Exam Site](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=2ahUKEwiD3sCy47jdAhXLq1kKHZ9eAogQFjABegQIChAB&url=https%3A%2F%2Fwww.microsoft.com%2Fen-ca%2Flearning%2Fexam-70-487.aspx&usg=AOvVaw2x3zfd_9UTaArUbsgUs9jh) | Official Site | 2018-01-15 | Using this for general guidance only.  Ensuring the other resources are hitting the requirements.
[FailedTuring](http://failedturing.blogspot.com/2018/01/microsoft-exam-70-487-study-guide.html) | Blog Site | 2018-01-16 | This is looking like an excellent reference.
[Safari Video](https://www.safaribooksonline.com/videos/70-487-developing/100000006A0653) | 15hr video course | 2017-10
[PluralSight](https://app.pluralsight.com/paths/skills/microsoft-azure-for-developers) | 32hr Video course | 2015-09 through 2017-12
[AlertCoding](https://alertcoding.wordpress.com/2013/01/09/microsoft-exam-70-487-study-guide/) | Blog Site | 2013-01-09
[MS Press Book](https://www.safaribooksonline.com/library/view/exam-ref-70-487/9780735677241/) | Book (1/5 rating) | 2013-11

## Note Organized by Topic

### Accessing Data (20-25%)

#### Choose data access technologies

> Choose a data access technology, including ADO.NET, Entity Framework, WCF Data Services, and Azure Cosmos DB, based on application requirements

This section is about choosing a data access technology, so knowing the features of each technology is key.  Questions will likely be a list of requirements and a _pick tech stack_ multiple choice.  The how to do something is in the mplementatoin sections down below.

The lot of this can be found directly on the [_Microsoft_ site](https://docs.microsoft.com/en-us/dotnet/framework/data).

##### ADO.NET

[Microsoft](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/ado-net-overview) on ADO.NET.

This is the traditional data-set and data-reader technology the we have always loved with C#.

```csharp
// this is a reader connection
var connectionString = {my connection string}
var query = "SELECT * FROM users WHERE userId = @userId;";

using (var connection = new SqlConnection(connectionString)){
  // the next two lines are effectively synonymous
  var command = new SqlCommand(query,connection))
  // var command = connection.CreateCommand();
  command.Parameters.AddWithValue("@user",userId);
  connection.Open();
  var reader = command.ExecuteReader();
  while (reader.Read()){
    var value = reader[0];
  }
  reader.Close();
}
```

Other common commands to call:

- `command.ExecuteNonQuery(~);` 
  - this would be used to `UPDATE`, `INSERT` or `DELETE` records, it could also be used to execute non `DML` sql statements, for example: `CREATE`, `ALTER`, etc..
- `command.ExecuteScalar(~)'`
  - this would return a single value from an aggregating query, for example: `SELECT MAX(sale) FROM annualSales;` would be expected to return one value.  This could also be used to return the affected rows count from an `INSERT` or `UPDATE` statement though this is non-standard.

**ADO.NET** is also used to build `DataSets` and `TableSets` using the `DataAdaptor`:

```csharp
// this is an adaptor connection
var connectionString = {my connection string}
var query = "SELECT * FROM users";

using (var connection = new SqlConnection(connectionString)){
  var adaptor = new SqlAdaptor(query,connection))
  var users = new DataSet();
  adaptor.Fill(users,"Users");
}
```

There is also LINQ to DataSet, in reality this looks are cumbersome as you would expect it to be.  Fill a dataset, then write a LINQ statement against the dataset that is cast `AsEnumerable()` and each of the field values need to be extracted with `record.Field<{type}>("field name")`.  

##### Entity Framework

[Covered under the EF section below](#querying-and-manipulating-data-by-using-the-entity-framework-20-25)

##### WCF Data Services

##### Cosmos DB

#### Implement caching

> Cache static data, apply cache policies, including policy expirations; use CacheDependency to refresh cache data; query notifications; implement caching using Redis

#### Implement transactions

> Manage transactions by using the API from System.Transactions namespace; implement distributed transactions including distributed transaction on SQL Azure; specify a transaction isolation level

#### Implement data storage in Microsoft Azure

> Access data storage in Windows Azure; choose a data storage mechanism in Microsoft Azure. including blobs, tables, queues, Azure SQL, and Cosmos DB; distribute data by using the Content Delivery Network (CDN) and Azure File Sync; handle exceptions by using retries; use Elastic client library with Azure SQL

#### Create and implement a WCF Data Services service

> Address resources; implement filtering; create a query expression; access payload formats, including JSON; use data service interceptors and service operators; version a data service; implement data services providers; host the dataservice; use actions to implement server-side behavior

    Preparation resources

        Transaction isolation levels
        WCF Data Services
        XML documents and data

### Querying and Manipulating Data by Using the Entity Framework (20-25%)

Entity Framework (EF) is a Object Relational Mapper (ORM), it is separate from LINQ which is a query mechanism,  however it is common to see both technologies wrapped together.  EF provides a means to map the database into a data context and then provides a _Repository_ like collection of `DbSet<T>` that LINQ can query against.  Additionally, it implements `Add` and `Get` methods which work against the in-memory collection.

> I don;t know as much about this as I thought I did, **more work required**. Important to remember that _EF Core_ does not implement lazy object graph mapping and to load relationships between `DbSet` collections it needs to done in one of three ways:
> - _eager loading_, by using `Include(~)` in the actual query
> - _explicit loading_, by 
> [more](https://docs.microsoft.com/en-us/ef/core/querying/related-data)

[Microsoft recommends](https://docs.microsoft.com/en-us/ef/ef6/index#should-i-use-ef6-or-ef-core) using EF Core on all new projects.

#### Query and manipulate data by using the Entity Framework

> Query, update, and delete data by using DbContext; build a query that uses deferred execution; implement lazy loading and eager loading; create and run compiled queries; query data by using Entity SQL; create global query filters

#### Query and manipulate data by using Data Provider for Entity Framework

> Query and manipulate data by using Connection, DataReader, and Command objects from the System.Data.EntityClient namespace; perform synchronous and asynchronous operations; manage transactions (API)

#### Query data by using LINQ to Entities

> Query data by using LINQ operators, including project, skip, aggregate, filter, and join; log queries; implement query boundaries

#### Query and manipulate data by using ADO.NET

> Query and manipulate data by using Connection, DataReader, and Command objects; perform synchronous and asynchronous operations; manage transactions

#### Create an Entity Framework data model

> Structure a data model using Table per type, table per class, and table per hierarchy; select and implement an approach to manage a data model, including code first, model first, and database first; implement POCO objects; describe a data model by using conceptual schema definitions, storage schema definitions, and mapping languages, including CSDL, SSDL, and MSL

#### Implement Entity Framework with third party databases

> Implement Entity Framework using MySQL and SQLite databases; design a strategy to manage differences between database capabilities; leverage database specific technologies, including ON DUPLICATE KEY using Entity Framework

    Preparation resources

        Entity Framework
        Loading related entities
        IQueryable<T> Interface

### Creating and Consuming Web API-based services (20-25%)

#### Design a Web API

> Define HTTP resources with HTTP actions; plan appropriate URI space, and map URI space using routing; choose appropriate HTTP method to meet requirements; choose appropriate Web API formats for responses to meet requirements; plan when to make HTTP actions asynchronous

#### Implement a Web API

> Accept data in JSON format; use content negotiation to deliver different data formats to clients; define actions and parameters to handle data binding; implement dependency injection to create more flexible applications; implement action filters and exception filters to manage controller execution; implement asynchronous and synchronous actions; implement streaming actions; implement middleware

#### Secure a Web API

> Implement Identity for authentication; implement authorization using roles, claims, and custom authorization; implement Data Protection APIs; enable cross-domain requests (CORS); prevent cross-site request forgery (XSRF); implement and extend authorization filters to control access to applications

#### Host and manage Web API

> Host Web API in IIS; self-host a Web API in your own process; host Web API in Kestrel; host services in a Windows Azure Web App; host services in Docker containers; configure the host server for streaming; work with a hosting environment

#### Consume Web API web services

> Consume Web API services by using HttpClient; send and receive requests in different formats; handle retry logic; implement content negotiation; use Swagger to construct Uris and payloads; use AutoRest to build clients

    Preparation resources

        Getting started with ASP.NET Web API 2 (C#)
        Implementing Basic CRUD functionality with the Entity Framework in ASP.NET MVC application
        Json class

### Designing and Implementing Web Services (15-20%)

#### Consume a WCF service

> Generate proxies by using SvcUtil; generate proxies by creating a service reference; create and implement channel factories; configure WCF services by using configuration settings; create and configure bindings for WCF services; relay bindings to Azure using service bus endpoints; integrate with the Azure service bus relay

#### Implement serverless Azure Web Services

> Host web services using App Services, including Logic Apps and API Apps; design and implement Azure Function based services; design and implement Azure Web Jobs; design and implement Service Fabric based web services; implement schedule-based processing in a serverless environment

#### Implement traffic management in Azure

> Implement Azure Load Balancer, including scaling; implement Azure Application Gateway; implement Azure Traffic Manager; design for multiple regions; leverage Azure CDN for caching web services; implement Log Analytics

#### Implement Azure API Management

> Secure Web Services using certificates, Azure Active Directory, and OAuth; define and implement policies, including secrets, caching, external services, monitoring and throttling; define API interface using the Azure Portal and Swagger; manage running services using logging, disaster recovery, and multiple regions

#### Monitor web services

> Collect logs and metrics using Azure Event Hubs; process logs and metrics using Azure Event Hubs, Stream Analytics, and Machine Learning; use Azure App Insights to monitor and troubleshoot web services

    Preparation resources

        ASP.NET Web Deployment using Visual Studio: Introduction
        How to: Create a Web Deployment Package in Visual Studio
        Installing NuGet

### Deploying Web Applications and Services (15-20%)

#### Design a deployment strategy

> Deploy using Web Deploy; deploy using Web Publishing in Visual Studio; deploy a web application by using XCopy; automate a deployment from VSTS or TFS Build Server; deploy a web application to a Docker container; design a continuous deployment pipeline; deploy using cloud sync

#### Choose a deployment strategy for Azure

> Determine appropriate service; perform an in-place upgrade and deployment slot swap; create staging environments; configure an upgrade domain; create and configure input and internal endpoints; specify operating system configuration; implement ARM templates to customize deployment; deploy to Service Fabric; deploy to Azure Stack

#### Configure a web application for deployment

> Switch from production or release mode to debug mode; transform configuration files ; configure Azure configuration settings; configure Azure Key Vault for application secrets; configure deployment credentials for Azure App Service

#### Manage packages by using NuGet

> Create and configure a NuGet package; install and update an existing NuGet package; resolve versioning conflict issues; connect to a local repository cache for NuGet, set up your own package repository; manage NuGet dependencies

#### Share assemblies between multiple applications and servers

> Prepare the environment for use of assemblies across multiple servers; sign assemblies by using a strong name; deploy assemblies to the global assembly cache; implement assembly versioning; create an assembly manifest; configure assembly binding redirects; target netstandard for cross platform libraries

    Preparation resources

        ASP.NET Web Deployment using Visual Studio: Introduction
        How to: Create a Web Deployment Package in Visual Studio
        Installing NuGet