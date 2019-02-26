---
group: mbi
title: Magento BI Import API Getting Started
functional_areas:
  - Getting started
  - System
  - Setup
---

Before you begin, you should checkout the <a href="{{ page.baseurl }}/mbi/libraries.html">libraries</a> section to install your chosen client library. Here are some quick examples that can get you started with the Import API.


## Create a Users Table {#create-users-table}

Your most important table in Magento BI is your `Users` table. In your app, you probably have a `user` object with some data like `id`, `email`, and `acquisition_source`.

Let's walk through how you would push this data to the Import API.

First, let's lay out a template for pushing the data. We'll make sure the client is `authenticated <api-authentication>` and prepare to push the data to the Import API.

**Javascript**
```js

    var rjmetrics = require("rjmetrics");
    client = new rjmetrics.Client(your-client-id, "your-api-key");

    // make sure the client is authenticated before we do anything
    client.authenticate().then( function(data) {
      // this is where we'll push the data
    }).fail(function(err) {
      console.error("Failed to authenticate!");
    });
```

**PHP**
```php

    require 'vendor/autoload.php';
    $client = new RJMetrics\Client($your-client-id, "your-api-key");

    // make sure the client is authenticated before we do anything
    if($client->authenticate()) {
      // this is where we'll push the data
    }
```

**Clojure**
```clojure

    (ns examples.acquisition-source
      (:require [rjmetrics.core :as rjmetrics]))

    (defn run
      []
      (let [config {:client-id your-client-id :api-key "your-api-key"}]
        (when (rjmetrics/authenticated? config)
          ;; this is where we'll push data
          )))
```

**Ruby**
```ruby

    require "rjmetrics_client"
    client = RJMetricsClient.new(your-client-id, "your-api-key")

    # make sure the client is authenticated before we do anything
    if client.authenticated?
      # this is where we'll push data
    end
```

**Python**
 ```python

    import rjmetrics.client
    client = rjmetrics.client.Client(your_client_id, "your_api_key")

    # make sure the client is authenticated before we do anything
    if client.authenticate()
      # this is where we'll push data
```

Next, we want to actually push the data. Let's create a new function to do the dirty work of syncing the new data. Check out the NEW LINK TO API REF WILL GO HERE: `upsert documentation <api-upsert>` to learn about the specifics on data that can be pushed to the API.

**Javascript**
```js

    function syncUser(client, user) {
      return client.pushData(
        // table named "users"
        "users",
        {
          // user_id is the unique key here, since each user should only
          // have one record in this table
          "keys": ["id"],
          "id": user.id,
          "email": user.email,
          "acquisition_source": user.acquisition_source
        });
    }
```

**PHP**
```php

    function syncUser($client, $user) {
      $dataToPush = new stdClass();
      $dataToPush->id = $user->id;
      $dataToPush->email = $user->email;
      $dataToPush->acquisition_source = $user->acquisitionSource;
      // user_id is the unique key here, since each user should only
      // have one record in this table
      $dataToPush->keys = array("id");

      // table named "users"
      return $client->pushData("users", $dataToPush);
    }
```

**Clojure**
```clojure

    (defn- sync-user
      [config user]
      (let [result (rjmetrics/push-data config
                                        ;; table named "users"
                                        "users"
                                        ;; user_id is the unique key here, since each user
                                        ;; should only have one record in the table
                                        (assoc user :keys ["id"]))]
        (if (= (-> result first :status) 201)
            (print "Synced user with id" (:id user) "\n")
            (print "Failed to sync user with id" (:id user) "\n"))))
```

**Ruby**
```ruby

    def sync_user(client, user)
      # `id` is the unique key here, since each user should only
      # have one record in this table
      user[:keys] = [:id]
      # table named "users"
      return client.pushData("users", user)
    end
```

**Python**
```python

    def sync_user(client, user):
      # `id` is the unique key here, since each user should only
      # have one record in this table
      user["keys"] = ["id"]
      # table named "users"
      return client.push_data("users", [user]) # NOTE: the python library only pushes lists
```

Putting it all together, we incorporate the `syncUser` function into our original script.

**Javascript**
```js

    var rjmetrics = require("rjmetrics");
    var client = new rjmetrics.Client(your-client-id, "your-api-key");

    function syncUser(client, user) {
      return client.pushData(
        // table named "users"
        "users",
        {
          // user_id is the unique key here, since each user should only
          // have one record in this table
          "keys": ["id"],
          "id": user.id,
          "email": user.email,
          "acquisition_source": user.acquisition_source
        });
    }

    // let's define some fake users
    var users = [
      {id: 1, email: "joe@schmo.com", acquisition_source: "PPC"},
      {id: 2, email: "mike@smith.com", acquisition_source: "PPC"},
      {id: 3, email: "lorem@ipsum.com", acquisition_source: "Referral"},
      {id: 4, email: "george@vandelay.com", acquisition_source: "Organic"},
      {id: 5, email: "larry@google.com", acquisition_source: "Organic"},
    ];

    // make sure the client is authenticated before we do anything
    client.authenticate().then( function(data) {

      // iterate through users and push data
      users.forEach( function(user) {
        syncUser(client, user).then( function(data) {
          console.log("Synced user with id " + user.id);
        }, function(error) {
          console.error("Failed to sync user with id " + user.id);
        })
      });

    }).fail(function(err) {
      console.error("Failed to authenticate!");
    });
```

**PHP**
```php

    require 'vendor/autoload.php';
    $client = new RJMetrics\Client($your-client-id, "your-api-key");

    function syncUser($client, $user) {
      $dataToPush = new stdClass();
      $dataToPush->id = $user->id;
      $dataToPush->email = $user->email;
      $dataToPush->acquisition_source = $user->acquisitionSource;
      // user_id is the unique key here, since each user should only
      // have one record in this table
      $dataToPush->keys = array("id");

      // table named "users"
      return $client->pushData("users", $dataToPush);
    }

    // let's define some fake users
    function fakeUserGenerator($id, $email, $acquisitionSource) {
      $toReturn = new stdClass();

      $toReturn->id = $id;
      $toReturn->email = $email;
      $toReturn->acquisitionSource = $acquisitionSource;

      return $toReturn;
    }

    $users = array(
      fakeUserGenerator(1, "joe@schmo.com", "PPC"),
      fakeUserGenerator(2, "mike@smith.com", "PPC"),
      fakeUserGenerator(3, "lorem@ipsum.com", "Referral"),
      fakeUserGenerator(4, "george@vandelay.com", "Organic"),
      fakeUserGenerator(5, "larry@google.com", "Organic"),
    );

    // make sure the client is authenticated before we do anything
    if($client->authenticate()) {
      // iterate through users and push data
      foreach($users as $user) {
        $responses = syncUser($client, $user);

        // api calls always return an array of responses
        foreach($responses as $response) {
          if($response->code == 201)
            print("Synced user with id {$user->id}\n");
          else
            print("Failed to sync user with id {$user->id}\n");
        }
      }
    }
```

**Clojure**
```clojure

    (ns examples.acquisition-source
      (:require [rjmetrics.core :as rjmetrics]))

    (defn- sync-user
      [config user]
      (let [result (rjmetrics/push-data config
                                        ;; table named "users"
                                        "users"
                                        ;; user_id is the unique key here, since each user
                                        ;; should only have one record in the table
                                        (assoc user :keys ["id"]))]
        (if (= (-> result first :status) 201)
            (print "Synced user with id" (:id user) "\n")
            (print "Failed to sync user with id" (:id user) "\n"))))

    (defn run
      []
      (let [config {:client-id your-client-id :api-key "your-api-key"}
            ;; let's define some fake users
            users [{:id 1, :email "joe@schmo.com", :acquisition_source "PPC"}
                   {:id 2, :email "mike@smith.com", :acquisition_source "PPC"}
                   {:id 3, :email "lorem@ipsum.com", :acquisition_source "Referral"}
                   {:id 4, :email "george@vandelay.com", :acquisition_source "Organic"}
                   {:id 5, :email "larry@google.com", :acquisition_source "Organic"}]]
        ;; make sure the client is authenticated before we do anything
        (when (rjmetrics/authenticated? config)
          ;; iterate through users and push data
          (dorun (map (partial sync-user config) users)))))
```

**Ruby**
```ruby

    require "rjmetrics_client"
    client = RJMetricsClient.new(your-api-key, "your-client-id")

    # let's define some fake users
    fake_users = [
      {:id => 1, :email => "joe@schmo.com", :acquisition_source => "PPC"},
      {:id => 2, :email => "mike@smith.com", :acquisition_source => "PPC"},
      {:id => 3, :email => "lorem@ipsum.com", :acquisition_source => "Referral"},
      {:id => 4, :email => "george@vandelay.com", :acquisition_source => "Organic"},
      {:id => 5, :email => "larry@google.com", :acquisition_source => "Organic"}
    ]

    def sync_user(client, user)
      # `id` is the unique key here, since each user should only
      # have one record in this table
      user[:keys] = [:id]
      # table named "users"
      return client.pushData("users", user)
    end

    # make sure the client is authenticated before we do anything
    if client.authenticated?
      fake_users.each do |user|
        # iterate through users and push data
        sync_user(client, user).each do |response|
          if response["code"]
            puts "Synced user with id #{user[:id]}"
          else
            puts "Failed to sync user with id #{user[:id]}"
          end
        end
      end
    end
```

**Python**
```python

    import rjmetrics.client

    CLIENT_ID = 0000
    API_KEY = 'your_api_key'

    client = rjmetrics.client.Client(CLIENT_ID, API_KEY)

    # let's define some fake users
    fake_users = [
      {"id": 1, "email": "joe@schmo.com", "acquisition_source": "PPC"},
      {"id": 2, "email": "mike@smith.com", "acquisition_source": "PPC"},
      {"id": 3, "email": "lorem@ipsum.com", "acquisition_source": "Referral"},
      {"id": 4, "email": "george@vandelay.com", "acquisition_source": "Organic"},
      {"id": 5, "email": "larry@google.com", "acquisition_source": "Organic"}
    ]


    def sync_user(client, user):
      # `id` is the unique key here, since each user should only
      # have one record in this table
      user["keys"] = ["id"]
      # table named "users"
      return client.push_data("users", [user])[0]


    # make sure the client is authenticated before we do anything
    if client.authenticate():
      for user in fake_users:
        # iterate through users and push data
        response = sync_user(client, user)
        if response.ok:
            print "Synced user with id ", user["id"]
        else:
            print "Failed to sync user with id ", user["id"]
```

This example is included with the <a href="{{ page.baseurl }}/mbi/libraries.html">client libraries</a> or can be [downloaded from github](http://www.github.com/rjmetrics).

**Javascript**
```text

    npm install
    node users-table.js
```

**PHP**
```text

    composer install
    php users-table.php
```

**Clojure**
```clojure

    lein repl

    > (ns examples.users-table)
    > (require :reload 'examples.users-table)
    > (run)
```

**Ruby**
```text

    bundle install
    ruby users-table.rb
```

**Python**
```text

    pip install
    python examples/users_table.py
```
## Create an Orders Table {#create-an-orders-table}

Now, let's create an orders table with the following fields: `id`, `user_id`, `value` and `sku`.

We'll need a new function to push the order object:

**Javascript**
```js

    function syncOrder(client, order) {
      return client.pushData(
        "orders",
        {
          "keys": ["id"],
          "id": order.id,
          "user_id": order.user_id,
          "value": order.value,
          "sku": order.sku
        });
    }
```

**PHP**
```php

    function syncOrder($client, $order) {
      $dataToPush = new stdClass();
      $dataToPush->id = $order->id;
      $dataToPush->user_id = $order->user_id;
      $dataToPush->value = $order->value;
      $dataToPush->sku = $order->sku;
      $dataToPush->keys = array("id");

      return $client->pushData("orders", $dataToPush);
    }
```

**Clojure**
```clojure

    (defn- sync-order
      [config order]
      (let [result (rjmetrics/push-data config
                                        "orders"
                                        (assoc order :keys ["id"]))]
        (if (= (-> result first :status) 201)
            (print "Synced order with id" (:id order) "\n")
            (print "Failed to sync order with id" (:id order) "\n"))))
```

**Ruby**
```ruby

    def sync_order(client, order)
      order[:keys] = [:id]
      return client.pushData("orders", order)
    end
```

**Python**
```python

    def sync_order(client, order):
      order["keys"] = ["id"]
      return client.push_data("orders", [order])[0]
```

Now, we can plug this into the same template from the `users` table:

**Javascript**
```js

    var rjmetrics = require("rjmetrics");
    var client = new rjmetrics.Client(your-client-id, "your-api-key");

    function syncOrder(client, order) {
      return client.pushData(
        "orders",
        {
          "keys": ["id"],
          "id": order.id,
          "user_id": order.user_id,
          "value": order.value,
          "sku": order.sku
        });
    }

    var orders = [
      {"id": 1, "user_id": 1, "value": 58.40,  "sku": "milky-white-suede-shoes"},
      {"id": 2, "user_id": 1, "value": 23.99,  "sku": "red-button-down-fleece"},
      {"id": 3, "user_id": 2, "value": 5.00,   "sku": "bottle-o-bubbles"},
      {"id": 4, "user_id": 3, "value": 120.01, "sku": "zebra-striped-game-boy"},
      {"id": 5, "user_id": 5, "value": 9.90  , "sku": "kitten-mittons"}
    ];

    client.authenticate().then( function(data) {

      orders.forEach( function(order) {
        syncOrder(client, order).then( function(data) {
          console.log("Synced order with id " + order.id);
        }, function(error) {
          console.error("Failed to sync order with id " + order.id);
        })
      });

    }).fail(function(err) {
      console.error("Failed to authenticate!");
    });
```

**PHP**
```php

    require 'vendor/autoload.php';
    $client = new RJMetrics\Client($your-client-id, "your-api-key");

    function syncOrder($client, $order) {
      $dataToPush = new stdClass();
      $dataToPush->id = $order->id;
      $dataToPush->user_id = $order->user_id;
      $dataToPush->value = $order->value;
      $dataToPush->sku = $order->sku;
      $dataToPush->keys = array("id");

      return $client->pushData("orders", $dataToPush);
    }

    function fakeOrderGenerator($id, $userId, $value, $sku) {
      $toReturn = new stdClass();

      $toReturn->id = $id;
      $toReturn->user_id = $userId;
      $toReturn->value = $value;
      $toReturn->sku = $sku;

      return $toReturn;
    }

    $orders = array(
      fakeOrderGenerator(1, 1, 58.40, "milky-white-suede-shoes"),
      fakeOrderGenerator(2, 1, 23.99, "red-buttons-down-fleece"),
      fakeOrderGenerator(3, 2, 5.00, "bottle-o-bubbles"),
      fakeOrderGenerator(4, 3, 120.01, "zebra-striped-game-boy"),
      fakeOrderGenerator(5, 5, 9.90, "kitten-mittons")
    );

    if($client->authenticate()) {
      foreach($orders as $order) {
        $responses = syncOrder($client, $order);

        foreach($responses as $response) {
          if($response->code == 201)
            print("Synced order with id {$order->id}\n");
          else
            print("Failed to sync order with id {$order->id}\n");
        }
      }
    }
```

**Clojure**
```clojure

    (ns examples.orders-table
      (:require [rjmetrics.core :as rjmetrics]))

    (defn- sync-order
      [config order]
      (let [result (rjmetrics/push-data config
                                        "orders"
                                        (assoc order :keys ["id"]))]
        (if (= (-> result first :status) 201)
            (print "Synced order with id" (:id order) "\n")
            (print "Failed to sync orfer with id" (:id order) "\n"))))

    (defn run
      []
      (let [config {:client-id your-client-id :api-key "your-api-key"}
            orders [{:id 1, :user_id 1 :value 58.40  :sku "milky-white-suede-shoes"}
                    {:id 2, :user_id 1 :value 23.99  :sku "red-button-down-fleece"}
                    {:id 3, :user_id 2 :value 5.00   :sku "bottle-o-bubbles"}
                    {:id 4, :user_id 3 :value 120.01 :sku "zebra-striped-game-boy"}
                    {:id 5, :user_id 5 :value 9.90   :sku "kitten-mittons"}]]
        (when (rjmetrics/authenticated? config)
          (dorun (map (partial sync-order config) users)))))
```

**Ruby**
```ruby

    require "rjmetrics_client"
    client = RJMetricsClient.new(your-client-id, "your-api-key")

    fake_orders = [
      {:id => 1, :user_id => 1, :value => 58.40,  :sku => "milky-white-suede-shoes"},
      {:id => 2, :user_id => 1, :value => 23.99,  :sku => "red-button-down-fleece"},
      {:id => 3, :user_id => 2, :value => 5.00,   :sku => "bottle-o-bubbles"},
      {:id => 4, :user_id => 3, :value => 120.01, :sku => "zebra-striped-game-boy"},
      {:id => 5, :user_id => 5, :value => 9.90,   :sku => "kitten-mittons"}
    ]

    def sync_order(client, order)
      order[:keys] = [:id]
      return client.pushData("orders", order)
    end

    if client.authenticated?
      fake_orders.each do |order|
        sync_order(client, order).each do |response|
          if response["code"]
            puts "Synced order with id #{order[:id]}"
          else
            puts "Failed to sync order with id #{order[:id]}"
          end
        end
      end
    end
```

**Python**
```python

    import rjmetrics.client

    CLIENT_ID = 0000
    API_KEY = 'your_api_key'

    client = rjmetrics.client.Client(CLIENT_ID, API_KEY)

    fake_orders = [
      {"id": 1, "user_id": 1, "value": 58.40,  "sku": "milky-white-suede-shoes"},
      {"id": 2, "user_id": 1, "value": 23.99,  "sku": "red-button-down-fleece"},
      {"id": 3, "user_id": 2, "value": 5.00,   "sku": "bottle-o-bubbles"},
      {"id": 4, "user_id": 3, "value": 120.01, "sku": "zebra-striped-game-boy"},
      {"id": 5, "user_id": 5, "value": 9.90,   "sku": "kitten-mittons"}
    ]


    def sync_order(client, order):
      order["keys"] = ["id"]
      return client.push_data("orders", [order])[0]


    # make sure the client is authenticated before we do anything
    if client.authenticate():
      for order in fake_orders:
        # iterate through users and push data
        response = sync_order(client, order)
        if response.ok:
            print "Synced order with id ", order["id"]
        else:
            print "Failed to sync order with id ", order["id"]
```            