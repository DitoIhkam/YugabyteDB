# LAB : CREATE A DISTRIBUTED SQL DATABASES

## Create a database with tables
In this step, you'll create the ```ybu_demo``` sample database, create tables, and insert data into the various tables. 

To copy and execute the SQL code snippets, simply select the  `Copy` button. Here are the steps to follow:

* In **YugabyteDB Managed Shell**, at the `yugabyte=>` prompt, create the ```ybu_demo database```.
```
DROP DATABASE IF EXISTS ybu_demo;
CREATE DATABASE ybu_demo;
```

* Connect to the database. 
```
\c ybu_demo;
```

* With the following Data Definition Language (DDL) statements, create four tables: tbl_products, tbl_users, tbl_orders, and tbl_reviews.

```
CREATE TABLE tbl_products (
  id         bigserial PRIMARY KEY,
  created_at timestamp,
  category   text,
  ean        text,
  price      float,
  quantity   int default(5000),
  rating     float,
  title      text,
  vendor     text
);

CREATE TABLE tbl_users (
  id         bigserial PRIMARY KEY,
  created_at timestamp,
  name       text,
  email      text,
  address    text,
  city       text,
  state      text,
  zip        text,
  birth_date text,
  latitude   float,
  longitude  float,
  password   text,
  source     text
);

CREATE TABLE tbl_orders (
  id         bigserial PRIMARY KEY,
  created_at timestamp,
  user_id    bigint,
  product_id bigint,
  discount   float,
  quantity   int,
  subtotal   float,
  tax        float,
  total      float
);

CREATE TABLE tbl_reviews (
  id         bigserial PRIMARY KEY,
  created_at timestamp,
  reviewer   text,
  product_id bigint,
  rating     int,
  body       text
);
```

* List your database table objects such as tables, sequences, and indexes.
```
\d
\di
```

## Insert data
In this exercise, you'll insert data into the four tables that you just created. To copy and execute the SQL code snippets, simply select the  Copy  button. Here are the steps to follow:

With the following Data Manipulation Language (DML) statements, insert data into the tbl_products table.

```
INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (1,'Gizmo','2017-07-19T19:44:56.582Z',1018947080336,29.463261130679875,4.6,'Rustic Paper Wallet','Swaniawski, Casper and Hilll');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (2,'Doohickey','2019-04-11T08:49:35.932Z',7663515285824,70.07989613071763,0.0,'Small Marble Shoes','Balistreri-Ankunding');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (3,'Doohickey','2018-09-08T22:03:20.239Z',4966277046676,35.38874881539054,4.0,'Synergistic Granite Chair','Murray, Watsica and Wunsch');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (4,'Doohickey','2018-03-06T02:53:09.937Z',4134502155718,73.9917800854834,3.0,'Enormous Aluminum Shirt','Regan Bradtke and Sons');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (5,'Gadget','2016-10-03T01:47:39.147Z',5499736705597,82.745097680356,4.0,'Enormous Marble Wallet','Price, Schultz and Daniel');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (6,'Doohickey','2017-03-29T05:43:40.150Z',2293343551454,64.9574710229587,3.8,'Small Marble Hat','Nolan-Wolff');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (7,'Doohickey','2017-06-03T03:07:28.061Z',0157967025871,98.8193384368194,4.3,'Aerodynamic Linen Coat','Little-Pagac');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (8,'Doohickey','2018-04-30T15:03:53.193Z',1078766578568,65.8921569329305,4.1,'Enormous Steel Watch','Senger-Stamm');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (9,'Widget','2019-02-07T08:26:25.647Z',7217466997444,58.3131209826137,4.2,'Practical Bronze Computer','Keely Stehr Group');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (10,'Gizmo','2017-01-09T09:51:20.352Z',1807963902339,31.7862188085793,4.3,'Mediocre Wooden Table','Larson, Pfeffer and Klocko');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (11,'Gadget','2018-05-28T08:02:54.482Z',3642408008706,88.3045327661709,0.0,'Ergonomic Silk Coat','Upton, Kovacek and Halvorson');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (12,'Gizmo','2017-11-12T14:51:16.221Z',9482467478850,77.3428505412217,4.4,'Sleek Paper Toucan','Mueller-Dare');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (13,'Gizmo','2016-05-24T23:09:46.392Z',0399569209871,75.086169270371,0.0,'Synergistic Steel Chair','Mr. Tanya Stracke and Sons');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (14,'Widget','2017-12-31T14:41:56.870Z',8833419218504,25.0987635271891,4.0,'Awesome Concrete Shoes','McClure-Lockman');

INSERT INTO tbl_products (id, category, created_at, ean, price, rating, title, vendor) VALUES (15,'Widget','2016-09-08T14:42:57.264Z',5881647583898,25.0987635271891,4.0,'Aerodynamic Paper Computer','Friesen-Anderson');
```

* Insert data into the tbl_users table.
```
INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2017-10-07T01:34:35.462Z','Twitter','Hudson Borer','Wood River','1986-12-12',40.71314890000001,68883,'ccca881f-3e4b-4e5c-8336-354103604af6',1,-98.5259864,'9611-9809 West Rosedale Road','NE','borer-hudson@yahoo.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-04-09T12:10:05.167Z','Affiliate','Domenica Williamson','Searsboro','1967-06-10',41.5813224,50242,'eafc45bf-cf8e-4c96-ab35-ce44d0021597',2,-92.6991321,'101 4th Street','IA','williamson-domenica@yahoo.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2017-06-27T06:06:20.625Z','Facebook','Lina Heaney','Sandstone','1961-12-18',46.11973039999999,55072,'36f67891-34e5-4439-a8a4-2d9246775ff8',3,-92.8416108,'29494 Anderson Drive','MN','lina.heaney@yahoo.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2019-02-21T13:59:15.348Z','Google','Arnold Adams','Rye','1992-08-12',37.9202933,81069,'537a727b-7525-44a3-99c8-8fdc488fbf02',4,-104.9726909,'2-7900 Cuerno Verde Road','CO','adams.arnold@gmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2017-09-05T03:36:44.811Z','Twitter','Dominique Leffler','Beaver Dams','1974-04-20',42.348954,14812,'6a802b6c-4da8-4881-9ca6-4f69085c7c14',5,-77.056681,'761 Fish Hill Road','NY','leffler.dominique@hotmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2016-09-22T10:08:29.599Z','Google','Rene Muller','Morse','1983-03-27',30.1514772,70559,'760495fb-9c38-4351-a6ee-4743d10d345e',6,-92.4861786,'1243 West Whitney Street','LA','rene.muller@gmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-05-24T06:18:20.069Z','Facebook','Roselyn Bosco','Leakesville','1996-01-19',31.2341055,39451,'43adf4af-055b-4a39-bc06-489fb0ffcf40',7,-88.5856948,'630 Coaker Road','MS','bosco.roselyn@hotmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2017-08-18T02:55:11.873Z','Facebook','Aracely Jenkins','Pittsburg','1973-06-05',37.43472089999999,66762,'6bb01b7f-6426-47d3-bfca-95dcc42b8b27',8,-94.6426865,'1167 East 570th Avenue','KS','aracely.jenkins@gmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-10-13T23:52:56.176Z','Twitter','Anais Ward','Ida Grove','1999-10-16',42.29790209999999,51445,'a3de5208-2f2f-4c81-9fe7-8d254bd5095d',9,-95.4673587,'5816-5894 280th Street','IA','ward.anais@gmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-08-16T12:16:30.307Z','Google','Tressa White','Upper Sandusky','1968-01-13',40.8006673,43351,'81052233-b32e-43cb-9505-700dbd8d3fca',10,-83.2838391,'13081-13217 Main Street','OH','white.tressa@yahoo.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-03-19T07:17:22.759Z','Facebook','Lolita Schaefer','Pilot Mound','1982-08-20',42.1394217,50223,'ceddd766-924b-4856-9dcb-a15b4c1b154c',11,-93.982366,'495 Juniper Road','IA','schaefer-lolita@hotmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-06-27T17:25:24.344Z','Facebook','Ciara Larson','Florence','1982-12-16',44.9564152,57235,'eb925d11-ea2f-41fb-a490-72b7c2b24dc0',12,-97.2287266,'16701-16743 449th Avenue','SD','ciara-larson@hotmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2017-09-21T16:21:06.408Z','Facebook','Mustafa Thiel','Santa Ysabel','1963-07-20',33.08172,92070,'bb85c7fa-4314-4e41-a060-7d0159f16931',13,-116.661853,'2993 Hoskings Ranch Road','CA','mustafa.thiel@hotmail.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-06-17T23:29:39.271Z','Facebook','Lavonne Senger','Chico','1963-09-22',39.6485802,95928,'8beebcc9-a376-447a-812f-7544fdc52ec7',14,-121.9343322,'3964 Chico River Road','CA','senger.lavonne@yahoo.com');

INSERT INTO tbl_users (created_at,source,name,city,birth_date,latitude,zip,password,id,longitude,address,state,email) VALUES ('2018-12-20T17:50:32.296Z','Twitter','Bertrand Romaguera','El Paso','2000-02-14',35.1080351,72045,'2734ae7a-aa25-4907-9d2e-a6992750db60',15,-92.0101859,'258 Opal Road','AR','romaguera.bertrand@gmail.com');
```

* Insert data into the tbl_orders table.
```

```



<details>
  <summary>Click to view code</summary>

  ```python
  # Contoh kode panjang di bawah ini
  print("Hello World!")
  for i in range(1000):
      print(i)
