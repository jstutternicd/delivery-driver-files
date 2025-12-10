[`legs.csv`](legs.csv) describes an undirected graph between numbered nodes
(stops), with the distance between.

[`routes.csv`](routes.csv) gives a number of delivery routes from stop to stop
(`route_id`).

[`stops.csv`](stops.csv) provides detail for each stop: for a given `stop_id`,
how many floors up the delivery is (0 is ground level), and if the delivery
driver must take the stairs (`False` means that there is a lift).  All routes
begin and end at stop 0, which is the depot.

[`events.csv`](events.csv) gives the event log within trips.  A given `trip_id`
will be on a set `route_id` (from [`routes.csv`](routes.csv)).  It is assigned
to one of three drivers (given by `driver_id`).  For each event, a timestamp is
given, in seconds, from when they signed onto that route.  The event type is
given: “arrive” is when the driver stops their van at the given stop, and
“depart” is when they have completed the delivery entirely and star moving
again.  `n_items` is the number of items that they delivered at each stop, and
is only ever given on the depart events, so “depart” on stop with ID 42 with 2
items means that the driver successfully delivered 2 items to stop 42.  This is
because when they arrive, they do not yet know if they are able to deliver the
items.

## Problem

We want to be able to predict the time that a delivery driver will finish a
route, given the events currently seen.  At each event, we know how far through
the route they are (given the stops they have already made), how much is left to
go, and what they might expect at the remaining stops (how many floors and if
there is a lift), but we cannot be sure how many items they will be delivering.
For example, in [`events.csv`](events.csv) (rows 36098–36100), the driver spent
exactly 2 minutes and 30 seconds delivering to their final stop (ID 1021) on
route 228 (arrived at 28943 and departed again at 29093), delivering 1 item.
Then they spent 14 minutes and 38 seconds returning to the depot (stop with ID
0) and dropped off the remaining packages (presumably undelivered).  It should
be possible to estimate the driver’s ETA from each stop using the distance, but
each driver may have a different driving style.
