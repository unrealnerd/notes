# Common Notes

## Nuget

- to package the library `dotnet pack .`
- pack with nuspec number `dotnet pack featureprovider.core.csproj -p:NuspecFile=.nuspec`
- push the package to local repo `dotnet nuget push .\bin\Debug\FeatureProvider.1.0.0.nupkg -s C:\localnugetpackages\`
- clean the nuget cache `dotnet nuget locals --clear all`
- adding he package to project `dotnet add package featureprovider -s c:\localnugetpackages` or if already added `dotnet restore`
- pushing the package to nuget.org `dotnet nuget push bin/Debug/FeatureProvider.1.0.0.nupkg -k <api key> -s https://api.nuget.org/v3/index.json`

## dotnet core

- setting proxy for .net core app `netsh winhttp set proxy 127.0.0.1:8888` to reset `netsh winhttp reset proxy`
- constructor injections for a class which has constructor paramters

```cs
  services.AddScoped<IDataContext<Product>>(_ =>
                new DataContext<Product>("products", _.GetRequiredService<IOptions<InventoryOptions>>())
            );
``` 
- `IOptionsSnaphot` Options snapshots are designed for use with transient and scoped dependencies and `IOptionsMonitor` with singleton service

### propagate the header from client to another api called from the server

```cs
//Startup.cs
services.AddHeaderPropagation(options => options.Headers.Add("Cookie"));
services.AddHttpClient("Service", options =>
{
    options.BaseAddress = new Uri(@"https://www.asfasddfsdafas.com");
}
).AddHeaderPropagation();
```

Using header propagation to pass on `cookie` did not work since it was passing on comma seperate cookie instead of semicolon. ended adding cookie header manually as below

```cs
var cookieString = _httpContextAccessor.HttpContext.Request.Cookies.Aggregate("", (key, kv) => key + kv.Key + "=" + kv.Value + "; ");
request.Headers.Add("Cookie", cookieString);
```

- to pick configuration from environment variable set `"GitlabService__GitlabBaseUrl":"https://gitlab.com/api/v4",` is equilent to `"Gitlabservice:GitlabBaseUrl"`. Notice the doudle underscore
- `.ConfigureAwait(False)` this tells the Task that it can resume itself on any thread that is available instead of waiting for the thread that originally created it. This will speed up responses and avoid many deadlocks.[...](https://www.skylinetechnologies.com/Blog/Skyline-Blog/December_2018/async-await-configureawait) ** dotnet core does have this method **
- consuming rest api
```cs
private readonly IHttpClientFactory _clientFactory;
  
public async Task<IList<NotificationViewModel>> GetAllNotifications()
{
    var url = @"https://url";

    var client = _clientFactory.CreateClient("client");
    var streamTask = client.GetStreamAsync(notificationUrl);
    var notifications = await JsonSerializer.DeserializeAsync<List<NotificationViewModel>>(await streamTask);

    return notifications;
}
```

## Python

- creating virtual env `python -m venv tracker` so that we can have custom versions on this project

## Essbase

- https://www.mindstreamanalytics.com/articles/essbase-cube.html

## Extras

- copy files from linux machine using ssh `scp username@servernameorip:/u01/app/tomcat/work/Catalina/localhost/filename /c/folder`

## Maven

- for including external jars in the project use the following command to install package in your repository
`mvn install:install-file --debug -Dfile=src/main/resources/lib/ess_japi.jar -DgroupId='com.essbase'`
- when including jar from local folder in pom.xml
```xml
    <dependency>
			<groupId>com.oracle</groupId>
			<artifactId>ojdbc6</artifactId>
			<scope>system</scope>
			<systemPath>${project.basedir}/src/main/resources/lib/ojdbc6.jar</systemPath>
			<version>6</version>
		</dependency>

```

## Programming

- [Desing patterns](https://howtodoinjava.com/)
- http2 allows server to send more than client asked for. eg: server can send all the images css required without initiating req for each resource.

## Performance

- [framework benchmarks](https://www.techempower.com/benchmarks/#section=test&runid=8ca46892-e46c-4088-9443-05722ad6f7fb&hw=ph&test=plaintext)

## Scripts

- find who is using the port using following commands
  
    ```shell
    netstat -aon | findstr '8888'
    tasklist | findstr '4036'
    Stop-Process -Id 3952
    ```

## AWS- Cron

- running cron with elastic bean causes job to run multiple time duplication
  - wrote a trigger from postgres on row add using `pg_notify` then using nodejs api and `pg` package subscribed to the event like [this](https://medium.com/@ederng/psql-event-triggers-in-node-js-ec27a0ba9baa)

## OAUTH2

- [howtodoitinjava](https://howtodoinjava.com/spring5/security5/oauth2-auth-server/)
- Authorization server: someone who provides access token
- scope: resources to which I have access to if i have a token
- grant: how the token will provided to me. In case of authorization code, I need to present/grant auth code to auth server to get token
- Refresh token: using which we can ask for a new token once the current expires.
- Access Token: holds scope, time its valid, refresh token
- Resource server: something which I like to access providing the access token
- [spring.io](https://projects.spring.io/spring-security-oauth/docs/oauth2.html)

## KAFKA

- kafka cluster: set of multiple servers/broker
- a topic can be across cluster as different partitions
- replication factor: means number of copies. if factor is 2 then data will be replicated in 2 brokers
- topic: basically something like a table
- topics are split as partitions
- we can set no.of partitions required when we create a topic
- offset: indicator for the sequence of messages, every partition has independent offsets
- producers acks: acks 0 means no acknowledgement sent, 1 means acknowledge after broker receives,2 means broker received and replicated
- using docker to set up kafka in local [setup](https://kafka-tutorials.confluent.io/kafka-console-consumer-producer-basics/kafka.html)
- yml for local setup 
```yml
version: '3'

services:
  zookeeper:
    image: confluentinc/cp-zookeeper:5.4.0
    hostname: zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  broker:
    image: confluentinc/cp-server:5.4.0
    hostname: broker
    container_name: broker
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://broker:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_METRIC_REPORTERS: io.confluent.metrics.reporter.ConfluentMetricsReporter
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_CONFLUENT_LICENSE_TOPIC_REPLICATION_FACTOR: 1
      CONFLUENT_METRICS_REPORTER_BOOTSTRAP_SERVERS: broker:29092
      CONFLUENT_METRICS_REPORTER_ZOOKEEPER_CONNECT: zookeeper:2181
      CONFLUENT_METRICS_REPORTER_TOPIC_REPLICAS: 1
      CONFLUENT_METRICS_ENABLE: 'true'
      CONFLUENT_SUPPORT_CUSTOMER_ID: 'anonymous'

  kafka-tools:
    image: confluentinc/cp-kafka:5.4.0
    hostname: kafka
    container_name: kafka
    command: ["tail", "-f", "/dev/null"]
    network_mode: "host"
```
- `docker-compose up -d`
- `docker exec -it broker bash`
- creating a topic `kafka-topics --create --topic article --bootstrap-server broker:9092`
- starting a consumer `kafka-console-consumer --topic article --bootstrap-server broker:9092 `
- producer to send message to the consumers `kafka-console-producer --topic article --broker-list broker:9092 --property "parse.key=true" --property "key.separator=:"`
- deleting a topic ` kafka-topics --delete --topic article --bootstrap-server localhost:9092`

## ANGULAR

- [When to to use whar for sharing data between components](https://angularfirebase.com/lessons/sharing-data-between-angular-components-four-methods/)
- Routing
  - [Angular Guide](https://angular.io/guide/router)
  - base href defined in index html head which will be the root for the application
  - routing state is a tree with routes defined in routes array forroot is the root node and rest for forchild
  - RouterState provides way to traverse the route tree
  - activatedorute service will have the details on the active route to use in the component
  - gaurds & resolvers are used to diffrent tasks in routing eg: canactivate
  - candeactivate used to ask for save or discrad changes before leaving
  - when using IIS for angular app install [URL rewrite module](https://www.iis.net/downloads/microsoft/url-rewrite) and add the web config to redirect to index.html [Angular docs](https://angular.io/guide/deployment#server-configuration).
  [ref](https://medium.com/angular-in-depth/deploy-an-angular-application-to-iis-60a0897742e7)
  - when deploying angular to sub folder in IIS make sure base tag is set to the subfolder
  `ng build --base-href "/kukke/" --prod`

### angular service worker

- `ng build --prod`
- run the http-server from dist/appname folder `http-server -p 4200`
- open the index.html specifically to run the app [](http:\\localhost:4200\index.html)
- run the notify express app
- check for allow/block notification on chrome
- [push notification](https://blog.angular-university.io/angular-push-notifications/)
- [push notificationa article](https://medium.com/@arjenbrandenburgh/angulars-pwa-swpush-and-swupdate-15a7e5c154ac)
- for enabling notification click to pen url follow this
  - create a new custom service worker js file in src folder  
  ```javascript
  importScripts('./ngsw-worker.js');

  (function () {
      'use strict';

      self.addEventListener('notificationclick', (event) => {
          console.log("notification clicked!");
          if (clients.openWindow && event.notification.data.url) {
              event.waitUntil(clients.openWindow(event.notification.data.url));
          }
      });}
  ());
  ```
  - add this file as asset in angular.json

### PEGA

- case type is a model of repeating business transaction. eg: submission->processing-->delivery
- case is a specific transaction. eg: transaction id. order#123


### Entity Framework EFCore

- Remove migrations `dotnet ef migrations remove -v`
- adding migrations `dotnet ef migrations add InitialCreate`
- updating the DB using the new migration `dotnet ef database update`
- when storing json in a column
  ```csharp
  protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<T>().ToTable(Configuration["TableName"]);
            modelBuilder.Entity<T>().Property(x => x.Id).ValueGeneratedOnAdd();

            modelBuilder.Entity<DepotKeyVal>().Property(x => x.Value).
                                                HasConversion(
                                                    y => JsonConvert.SerializeObject(y),
                                                    y => JsonConvert.DeserializeObject<Dictionary<string, object>>(y)
                                                );
        }
  ```
- to map different table and column name to model
  ```csharp
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
      modelBuilder.Entity<LowQuoteDetail>(x =>
          {
              x.ToTable("rdl_dell_low_qte_dtl_vw");


              x.Property(y => y.Number).HasColumnName("qte_num");
              x.Property(y => y.QuoteID).HasColumnName("qte_id");
              x.Property(y => y.Version).HasColumnName("qte_vrsn");
          }
      );

  }
  ```
- handle default date (zero date time) in mysql when date is '0000-00-00 00:00:00' in db. add that in connection string `server=servername;database=db;user=user;password=pwd;Convert Zero Datetime=True;` 

### GraphQL

- variables can be used as a param to a query
- aliases used to provide field values under alias field name like `As` in sql
`user_name: UName`
- include directive includes a field if the condition matches and skip skips if the condition is true
- Authorizing when using graphql.net
  ```cs
  //Startup.cs
  services.AddAuthentication(x =>
        {
            x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(x =>
        {
            x.Audience = Configuration["Jwt:Audience"];
            x.Authority = Configuration["Jwt:Authority"];
            x.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuerSigningKey = true,
                ValidIssuer = "https://token.com/oauth/token",
                ValidateIssuer = true,
                ValidateAudience = true,
                ValidateLifetime = false,
            };
        });
  #region "If using `UseGraphQL` which excludes our contollers"
    services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();

    services
        .AddTransient<IValidationRule, AuthorizationValidationRule>()
        .AddAuthorization(options =>
        {
            options.AddPolicy("Readonly", p => p.RequireClaim("scope","app1.readonly"));
        });
    #endregion
  ```
  - In query constructor 
    ```cs
    //QUery.cs
    public Query(Repository repo)
        {
            this.AuthorizeWith("Readonly");
    ```
  - in playground ui add the header with token
    ```js
      {
        "Authorization":"Bearer <token>"
      }
    ``` 


```js
query someQuery($inResult: Boolean) {
  dield1 @skip(if: $inResult)
  field2 @include(if: $inResult)
}
```

- fragments define reponse templates
- solves underfecthing/overfetching and reduce round trips

### Oracle

- using a cert to connect thorugh ssl connection
  - `orapki wallet create -wallet -auto_login -pwd welcome1` -- creates wallet with auto-login option at `C:\app\oracle\product\12.1.0\client_1\network\admin\wallet`
  - set wallet location & ssl client auth to false at `C:\app\oracle\product\12.1.0\client_1\network\admin\sqlnet.ora` 
    ```
    SSL_CLIENT_AUTHENTICATION = FALSE

    WALLET_LOCATION =
    (SOURCE =
      (METHOD = FILE)
      (METHOD_DATA =
        (DIRECTORY = C:\app\oracle\product\12.1.0\client_1\network\admin\wallet)
      )
    )

    ```
  - add the alias in `C:\app\oracle\product\12.1.0\client_1\network\admin\tnsnames.ora`
    ```
    DB_TLS =
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCPS)
        (HOST=hostip)
        (PORT=1523)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME = dbname)
      )
      (SECURITY=
        (SSL_SERVER_CERT_DN="CN=server host,OU=IEO,O=Google")
      )
    )
    ```
  - escape the `program files` space char in JDK and JAVA_HOME path using `Progra~1`  
  - to find service name use the below query. service name is required for the TNS.
  ```sql
    select name from V$SERVICES;
    select name from V$ACTIVE_SERVICES;
  ```


### Random

- running Jekyl locally `bundle exec jekyll serve`
- [neovim installation](https://jdhao.github.io/2018/11/15/neovim_configuration_windows/)

### Linux

- `lsof -i:8081` to check who is using the port
- `kill <PID>` to kill a process.
- `kill -9 <PID>` to force kill a process

### Java Keytool

- open java configure and make sure the jre is set to the right version
- use InstallCert.java for installation of cert from host []()
- to import certs from one keystore to another `keytool -importkeystore -srckeystore "C:\Program Files\Java\jdk1.8.0_221\jre\lib\security\file.jks" -destkeystore "C:\Program Files\Java\jdk1.8.0_221\jre\lib\security\cacerts" -srcstorepass changeit`
- to delete a certificate `./keytool -delete -alias server -keystore ../jre/lib/security/file.jks`
- to list all certs `./keytool -list -v -keystore ../jre/lib/security/file.jks`

### installting a localhost certificate using git openssl

- goto `C:\Program Files\Git\usr\bin`
- generate key `.\openssl.exe genrsa -des3 -out server.key 1024`
- create csr `.\openssl.exe req -new -key server.key -out server.csr`
- create cert file `.\openssl.exe x509 -req -days 365 -in server.csr -signkey server.key -out server.crt`
- install as trusted cert

### Fiddler

- Failed to negotiate HTTPS connection with server.fiddler.network.https
  this error means fiddler is not enabled to handle version of TLS
  tools->options-->https-->protocols link set `<client>;ssl3;tls1.0;tls1.1;tls1.2`
- setting proxy for .net core app `netsh winhttp set proxy 127.0.0.1:8888` to reset `netsh winhttp reset proxy`


### Windows

- finding the port in use `netstat -an| find /i "80"`

### Java

- to update tomcat version in springboot add this property in pom.xml
  ```xml
  <properties>
    <java.version>1.8</java.version>
    <tomcat.version>9.0.38</tomcat.version>
  </properties>
  ```
- to pass vaules to thread use constructor arguments
- Default swagger docs url for an api `https://<hostname>/swagger-ui.html`

###  Spring


- what is [spring framework](https://www.marcobehler.com/guides/spring-framework)
- @Component is kind of marker that will be considered in DI container using @ComponentScan

### integrating fiddler proxy on dev environment in dotnet core


```cs
services.AddHttpClient("NotificationService", options =>
    {
        options.BaseAddress = new Uri(@"https://www.asdfsadfswaddfasdf.com");
    }
).AddHeaderPropagation()
#if ENABLE_FIDDLER_DEBUG_PROXY
.ConfigurePrimaryHttpMessageHandler(() =>
{
    return new HttpClientHandler
    {
        Proxy = new WebProxy("http://localhost.dell.com:8888"),
        UseProxy = true
    };
#endif
```

If using vscode add args to build task
`"/p:DefineConstants=\"ENABLE_FIDDLER_DEBUG_PROXY\"",`

### Installing dev certificate in dotnet core

```sh
dotnet dev-certs https --clean
dotnet dev-certs https
dotnet dev-certs https --trust
```

### MAC

- ** setting env vairable **
  ```
  vi /etc/zshrc
  //append the export commands to set env variables
  export PATH="<new value>:$PATH"
  ```

### Privacy Pass Architecture

- Consists 2 protocols `issuance` and `redemption`
- Redemption protocol - server/origin challenge/prompts for the token from the client. Client may respond with the previously obtained token based on the token attributes (validity, same origin, IP address , etc.)

```
      Origin          Client        Attester          Issuer
  /--------------------------------------------------------------------
  |                 /-----------------------------------------\
  |   Challenge ----> Attest --->                             |
  |                 | TokenRequest --------------->           |
  |   Redemption    |                              (validate) | Issuance
  |      Flow       |                              (evaluate) |   Flow
  |                 |     <-------------------  TokenResponse |
  |   <--- Response |                                         |
  |                 \-----------------------------------------/
  \--------------------------------------------------------------------
```

- References
  - [IETF doc](https://www.ietf.org/archive/id/draft-ietf-privacypass-architecture-03.html)


### AWS

- instance type ondemand,savings plan( for compute based on previous usage, after committed usage exceeds on-demand costs take charge for the exceeded),spot (jobs which can be interuppted. since aws can witdraw these instances anytime)
- ec2 autoscaling: dynamic- based on demand, predective - based on predicted demand
- internet gateway & private gateway to enter a VPC
- SSM Agent-  is Amazon software that runs on ec2 instances to help manage resouces
- Install docker in EC2 
  ````sh
  sudo amazon-linux-extras install docker
  sudo usermod -a -G docker ec2-user
  ```
  ref:
  - https://medium.com/appgambit/part-1-running-docker-on-aws-ec2-cbcf0ec7c3f8
- to enable logging in ECS container, add this to task definition
  ```
  "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
            "awslogs-group": "/ecs/servicename",
            "awslogs-region": "us-west-1",
            "awslogs-stream-prefix": "ecs"
        }
    }
  ```
  - ECS Summary
    - VPC is created with couple of subnet
    - Network load balancer points to a target group
    - Cluster is created inside of which we create service
    - The service will hold the details like the vpc, loadbalancer, subet
    - this service will run task 
    - task dfinitaiton contains the container info 
    - logging is also enabled here using awslog driver


#### READ REPLICA
- replication process uses WAL which logs transactions on master and then replay those on the read replica
- relpica lag can be caused coz of
  - slow queries being run on read replica will pause the replication
  - 
- VACCUM in postgres basically cleans data that update and delete queries maintain versions of so that the read queries are served with the data that it asked at the time it initiated.
- On Aurora since they share the same storage as the master/source DB relica lag is almost in single digit ms.
- each ACU is approx 2gb and corresponding cpu and networking

- references
  - [gitlab issue](https://gitlab.com/gitlab-com/gl-infra/reliability/-/issues/7735)
  - [AutoVaccum pgsl docs](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html)
  - [AWS read replica best practices](https://aws.amazon.com/blogs/database/best-practices-for-amazon-rds-postgresql-replication/)

### Nodejs

- code blocks irrespective of async await unless for fetch, fiel reads etc. [reference](https://stackoverflow.com/questions/46004290/will-async-await-block-a-thread-node-js)
- *Node.js is fast when the work associated with each client at any given time is "small".* <br/> *Remember, the Event Loop should orchestrate client requests, not fulfill them itself.*<br/> [Dont block the event loop](https://nodejs.org/en/docs/guides/dont-block-the-event-loop/)
- settimeout is something that lives outside of v8. Its a web api provided by browser.
- webapi results or callback is sent to task queue once finshed running
- eventloop if stack empty pick a task from task queue or render queue. render queue is given higher priority
- setTimeOut not guaranteed time of exceution but minimum
- reference - [What the heck is eventloop](https://www.youtube.com/watch?v=8aGhZQkoFbQ&t=940s)
- complete guide for worker thread and event looping blocking explained [here](https://www.digitalocean.com/community/tutorials/how-to-use-multithreading-in-node-js)react

### React

- creating ref for dynamically created component
  ```
  //create refs array
  const refs = useRef([]);

  //add this ref property to element
  <input type="text" ref={(ref) => refs.current.push(ref)}>

  //referencing this ref
  ref1 = refs.current[0];
  ```

  ### javascript

- Example for common wrapper for unique function and binding this 
  ```js
  commonWrap(this.uniqueFn.bind(this), params);

  async commonWrap(fn, ...params) {
    for (let i = 0; i < 5; i++) {
      fn(true, new Date(new Date().setDate(new Date().getDate() + i), ), ...params);
    }
  }
  ```