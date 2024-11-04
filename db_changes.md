# DB changes

## User

Additional changes to the User model

1. can_be_lp (Boolean, default - false)
2. contactless (Boolean, default - false)

## LP

The rider entry is created when the rider logs in to the rider app for the first time.

1. id
2. user (foreign key to User)
3. is_active (Boolean, default - false)
4. lat
5. long
6. lat_long_updated_at

V2- aggregate_info ?? (can be calculated from query sets) like cumulative PODs collected and submitted

## Lat-Long

1. delivery_slot (foreign key to delivery-slot)
2. rider (foreign key to rider)
3. lat
4. long
5. timestamp

Have 10 days worth of data. Set up cron to delete

## Route

Route can either be created while uploading dispatch sheet or created from add route button on the new route planning screen

1. id
2. delivery_slot (foreign key to DeliverySlot)
3. rider (foreign key to Rider) NULLABLE
4. ref string
5. V2 - POD collected ?? (calculated due to the flexibility of reassignments)
6. V2 - POD submitted ?? (calculated)

unique constraint on (ref, delivery_slot)

Since dispatch sheet can be uploaded without ref, we provide a default ref - ‘’ for the route on uploading the dispatch sheet

## Delivery

Delivery can be created when the order status moves to `placed` (or) `preparing`. Check for any existing delivery with the same address-delivery_slot.
YES : add the delivery_id to the Order. NO : create a new Delivery

### Status Codes:

1. CREATED
2. STARTED
3. COMPLETED

### Schema:

1. id
2. status
3. user (foreign key to User)
4. address (foreign key to Address)
5. delivery_slot (foreign key to DeliverySlot)
6. rider (foreign key to Rider) NULLABLE
7. otp (V2 - contactless delivery - delivery level (OR) user level)
8. otp_sent (boolean - default false)
9. otp_last_sent_at
10. num_of_otp_attempts
11. image NULLABLE
12. route (foreign key to Route) NULLABLE
13. completed_at_lat NULLABLE
14. completed_at_long NULLABLE
15. created_at Auto
16. started_at DISPATCHED_AT on Order model NULLABLE
17. completed_at DELIVERED_AT on Order model NULLABLE
18. completed_by (foreign key to User) NULLABLE
19. logs
20. (V2 - using just lat-long to create addresses?)

unique constraint on (address, delivery_slot)

## Order

Additions to the existing Order model

1. delivery (foreign key to Delivery) NULLABLE

Implement at Order Cancellations flow -
Null the delivery FK
Delete the Delivery if no order_id is attached to it.

## POD

Additions to the existing POD model

1. delivery (foreign key to Delivery) NULLABLE

Implement at Order Cancellations flow -
Null the delivery FK
