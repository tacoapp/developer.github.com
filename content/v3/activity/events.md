---
title: Events
---

# Events

This is a read-only API to the {{ site.data.variables.product.product_name }} events. These events power the
various activity streams on the site.

{:toc}

Events are optimized for polling with the "ETag" header.  If no new events have
been triggered, you will see a "304 Not Modified" response, and your current
rate limit will be untouched.  There is also an "X-Poll-Interval" header that
specifies how often (in seconds) you are allowed to poll.  In times of high
server load, the time may increase.  Please obey the header.

``` command-line
$ curl -I https://{{ site.data.variables.product.api_url_pre }}/users/tater/events
> HTTP/1.1 200 OK
> X-Poll-Interval: 60
> ETag: "a18c3bded88eb5dbb5c849a489412bf3"

# The quotes around the ETag value are important
$ curl -I https://{{ site.data.variables.product.api_url_pre }}/users/tater/events \
$    -H 'If-None-Match: "a18c3bded88eb5dbb5c849a489412bf3"'
> HTTP/1.1 304 Not Modified
> X-Poll-Interval: 60
```

Events support [pagination](/v3/#pagination),
however the `per_page` option is unsupported. The fixed page size is 30 items.
Fetching up to ten pages is supported, for a total of 300 events.

Only events created within the past 90 days will be included in timelines. Events
older than 90 days will not be included (even if the total number of events
in the timeline is less than 300).

All Events have the same response format:

<%= headers 200, :pagination => default_pagination_rels %>
<%= json(:event) { |h| [h] } %>

## List public events

    GET /events

## List repository events

    GET /repos/:owner/:repo/events

## List issue events for a repository

Repository issue events have a different format than other events,
as documented in the [Issue Events API](https://developer.github.com/v3/issues/events/).

    GET /repos/:owner/:repo/issues/events

## List public events for a network of repositories

    GET /networks/:owner/:repo/events

## List public events for an organization

    GET /orgs/:org/events

## List events that a user has received

These are events that you've received by watching repos and following
users.  If you are authenticated as the given user, you will see private
events.  Otherwise, you'll only see public events.

    GET /users/:username/received_events

## List public events that a user has received

    GET /users/:username/received_events/public

## List events performed by a user

If you are authenticated as the given user, you will see your private
events.  Otherwise, you'll only see public events.

    GET /users/:username/events

## List public events performed by a user

    GET /users/:username/events/public

## List events for an organization

This is the user's organization dashboard.  You must be authenticated as
the user to view this.

    GET /users/:username/events/orgs/:org
