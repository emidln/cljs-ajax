##cljs-ajax

simple Ajax client for ClojureScript

### Leiningen

```clojure
[cljs-ajax "0.1.6"]
```

### Usage


The client provides an easy way to send Ajax queries to the server using `ajax-request`, `GET`, and `POST` functions.

#### ajax-request

The `ajax-request` is the base request function that accepts the following parameters:

* `:uri` - the URI for the request
* `:method` - a string representing the HTTP request type, eg: "PUT", "DELETE", etc.
* `:format` - a keyword indicating the response format, can be either `:json`, `:edn` or `:raw`\*, defaults to `:edn`
* `:handler` - success handler, a function that accepts the response as a single argument
* `:error-handler` - error handler, a function that accepts a map representing the error with keys `:status` and `:status-text`
* `:params` - a map of params to be sent to the server
* `:keywordize-keys` - true/false specifies whether keys in maps will be keywordized when using `:json` format

\* when `:raw` format is selected the server response will not be parsed and is returned as is.

#### GET/POST helpers

The `GET` and `POST` helpers accept a URI followed by a map of options:

* `:handler` - the handler function for successful operation should accept a single parameter which is the deserialized response
* `:error-handler` - the handler function for errors, should accept a map with keys `:status` and `:status-text`
* `:format` - the format for the response `:edn` or `:json` defaults to `:json` if specified by the Content-Type header of the response or `:edn` otherwise
* `:params` - a map of parameters that will be sent with the request
* `:keywordize-keys` - true/false specifies whether keys in maps will be keywordized when using `:json` format


```clojure
(ns foo
  (:require [ajax.core :refer [GET POST]]))

(defn handler [response]
  (.log js/console (str response)))

(defn error-handler [{:keys [status status-text]}]
  (.log js/console (str "something bad happened: " status " " status-text)))

(GET "/hello")

(GET "/hello" {:params {:foo "foo"}})

(GET "/hello" {:handler handler
               :error-handler error-handler})

(POST "/hello")

(POST "/send-message"
        {:params {:message "Hello World"
                  :user    "Bob"}
         :handler handler
         :error-handler error-handler})

(POST "/send-message"
        {:params {:message "Hello World"
                  :user    "Bob"}
         :handler handler
         :format :json
         :keywordize-keys true})
```

### Error Handler

The error handler function has a map with the following keys passed to it:

* `:status` - the HTTP status code
* `:status-text` - the HTTP status message, or feedback from a parse failure
* `:response` - the EDN/JSON response if it's valid
* `:original-text` The response as raw text (if parsing failed)
* `:is-parse-error` Is true if this is feedback from a parse failure
* `:parse-error` If the server returned an error, and that then failed to parse, the map contains the error, and this contains the parse failure

### Contributing

All pull requests are welcome, but we do ask that any changes come with tests that demonstrate the original problem.  For this, you'll need to get the test environment working.  First, you need to install phantom.js somewhere on your path.  We recommend you download directly from the website [http://phantomjs.org/download.html].  (Do _not_ give into the temptation of using apt-get on Ubuntu; it installs v1.4 and won't work.)

After that `lein cljsbuild test` should run the tests.
