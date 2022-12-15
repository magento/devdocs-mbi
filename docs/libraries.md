---
group: mbi
title: Magento BI Libraries
functional_areas:
  - Libraries
  - System
  - Setup
redirect_to: https://developer.adobe.com/commerce/services/reporting/libraries/
status: migrated
---

To make integrating with the Import API as easy as possible, we've created some client libraries for you to use. Have your own library? Want to contribute to ours? [Reach out to us!](https://support.magento.com/hc/en-us)

## Clojure {#clojure}

You can install the RJMetrics Clojure client library by adding the following dependency to your `project.clj`.

```clojure
[rjmetrics "0.2.0"]
```

Now, you can use the library like this:

```clojure
(ns example
    (:require [rjmetrics.core :as rjmetrics]))

(def config-map {:client-id 1
                  :api-key "your-api-key"})

(when (rjmetrics/authenticated? config-map)
  ;; do stuff with client
  )
```

For more information, check out the [source code on Github](https://github.com/RJMetrics/RJMetrics-clj).

## Javascript {#javascript}

The RJMetrics Javascript client library is available via npm:

```bash
npm install rjmetrics
```

You can also install it via npm by adding the following line to the dependencies field in your `package.json` file:

```javascript
"rjmetrics": "0.2.0"
```

Now, you can use the library like this:

```javascript
var rjmetrics = require("rjmetrics");
client = rjmetrics.Client(client_id, api_key);

# do stuff with client
```

For more information, check out the [source code on Github](https://github.com/RJMetrics/RJMetrics-js).

## PHP {#php}

The RJMetrics PHP client library can be installed using [Composer](https://getcomposer.org).

Add the following to your project's `composer.json`:

```javascript
  {
    "require": {
    ...
    "rjmetrics/rjmetrics-client": "0.2.0"
  }
}
```

Now, you can use the library in your script, like this:

```php
<?php

require 'vendor/autoload.php';
$client = new RJMetrics\Client($myClientId, $myApiKey);

/* do stuff with $client */

?>
```

For more information, you can see the [documentation for Httpful](https://phphttpclient.com/) or [view the source on Github](https://github.com/RJMetrics/RJMetrics-php).

## Python {#python}

You can install the RJMetrics Python client library with `pip` or `easy_install`.

```bash
pip install rjmetrics
```

You can also install it by adding the following line to a pip requirements file:

```python
rjmetrics
```

Now, you can use the library like this:

```python
from rjmetrics.client import Client
client = Client(client_id, api_key)

# do stuff with client
```

For more information, check out the [source code on Github](https://github.com/RJMetrics/RJMetrics-py).

## Ruby {#ruby}

The RJMetrics Ruby client library is available as a gem:

```bash
gem install rjmetrics-client
```

You can also install it via bundler by adding the following line in your `Gemfile`:

```ruby
gem 'rjmetrics-client'
```

Now, you can use the library like this:

```ruby
require "rjmetrics_client"
client = RJMetricsClient.new(client_id, api_key)

# do stuff with client
```

For more information, check out the [source code on Github](https://github.com/RJMetrics/RJMetrics-ruby).
