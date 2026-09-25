### Column	Available when order is placed?	Potential    feature?	Reason
order_id	       Yes	No	Identifier, not predictive information
customer_id	       Yes	Maybe	Could support historical customer features later
order_date	       Yes	Yes	Can derive temporal features
product	           Yes	Yes	Product selected at order time
category	       Yes	Yes	Category selected at order time
quantity	       Yes	Yes	Quantity requested at order time
unit_price	       Yes	Yes	Price known at order time
discount_percent   Yes	Yes	Discount known at order time
city	           Yes	Yes	Assuming shipping location is known
payment_method     Yes	Yes	Assuming selected at checkout
order_status	  No	Target	This is what we're trying to predict
total_amount	   ?	?	Must investigate how it is generated





### 1. What does one row in this dataset represent?

Each row represents **one unique customer order**. The dataset contains 25,000 rows, 25,000 unique `order_id` values, and no duplicated order IDs. Therefore, the prediction unit is **one order**.

### 2. What real-world situation should the cancellation-risk prediction help with, and when would it be made?

The model will predict the **probability that a newly placed order will eventually be cancelled**. The prediction will be made **immediately after order placement**, using only information available at that point. The purpose is to help the e-commerce operations or customer-support team identify orders with elevated cancellation risk and determine whether appropriate intervention or review is warranted before normal fulfillment proceeds.

### 3. How should refunded orders be treated for that prediction? What information or business rule would help decide?

`Refunded` orders will be **excluded from the initial binary cancellation target**. The dataset contains 692 refunded orders but does not establish whether a refund represents a cancellation, a post-delivery return, a payment reversal, or another process. Therefore, refunds will not be silently classified as cancellations.

The initial target population will consist of **Delivered and Cancelled orders only**:

* `Cancelled = 1`
* `Delivered = 0`
* `Refunded = excluded`

A documented business rule or source-data definition linking refunds to cancellations would be required before changing this treatment.

### 4. Who would use the prediction, and what might they do when the risk is high?

The prediction would be used by the **e-commerce operations or customer-support team**. A high predicted cancellation probability would flag an order for appropriate review or an established customer/order verification workflow. The model would provide a **risk probability**, not automatically cancel an order. The operational action and risk threshold would be determined separately based on business rules and the relative costs of incorrect decisions.
