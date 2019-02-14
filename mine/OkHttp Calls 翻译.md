# OkHttp

翻译文 [连接](https://github.com/square/okhttp/wiki/Calls)

#Calls

The HTTP client’s job is to accept your request and produce its response. This is simple in theory but it gets tricky in practice.

## Request

Each HTTP request contains a URL, a method (like GET or POST), and a list of headers. Requests may also contain a body: a data stream of a specific content type.

## Responses

The response answers the request with a code (like 200 for success or 404 for not found), headers, and its own optional body.

## Rewriting Requests

When you provide OkHttp with an HTTP request, you’re describing the request at a high-level: “fetch me this URL with these headers.” For correctness and efficiency, OkHttp rewrites your request before transmitting(发送) it.

OkHttp may add headers that are absent(缺席的) from the original request, including Content-Length, Transfer-Encoding, User-Agent, Host, Connection, and Content-Type. It will add an Accept-Encoding header for transparent(透明的) response compression unless the header is already present. If you’ve got cookies, OkHttp will add a Cookie header with them.

Some requests will have a cached response. When this cached response isn’t fresh, OkHttp can do a conditional GET to download an updated response if it’s newer than what’s cached. This requires headers like If-Modified-Since and If-None-Match to be added.

## Rewriting Response

If transparent compression was used, OkHttp will drop the corresponding response headers Content-Encoding and Content-Length because they don’t apply to the decompressed response body.

If a conditional GET was successful, responses from the network and cache are merged as directed by the spec.

## Follow-up(跟进) Requests

When your requested URL has moved, the webserver will return a response code like 302 to indicate the document’s new URL. OkHttp will follow the redirect to retrieve a final response.

If the response issues an authorization challenge, OkHttp will ask the Authenticator (if one is configured) to satisfy the challenge. If the authenticator supplies a credential(证书), the request is retried with that credential included.

## Retrying Requests

Sometimes connections fail: either a pooled connection was stale and disconnected, or the webserver itself couldn’t be reached. OkHttp will retry the request with a different route if one is available.

## Calls

With rewrites, redirects, follow-ups and retries, your simple request may yield many requests and responses. OkHttp uses Call to model the task of satisfying your request through however many intermediate requests and responses are necessary. Typically this isn’t many! But it’s comforting to know that your code will continue to work if your URLs are redirected or if you failover(故障转移) to an alternate(备用) IP address.

Calls are executed in one of two ways:

* Synchronous: your thread blocks until the response is readable.
* Asynchronous: you enqueue the request on any thread, and get called back on another thread when the response is readable.

Calls can be canceled from any thread. This will fail the call if it hasn’t yet completed! Code that is writing the request body or reading the response body will suffer an IOException when its call is canceled.

## Dispatch

For synchronous calls, you bring your own thread and are responsible for managing how many simultaneous requests you make. Too many simultaneous connections wastes resources; too few harms latency.

For asynchronous calls, Dispatcher implements policy for maximum simultaneous requests. You can set maximums per-webserver (default is 5), and overall (default is 64).