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

















### Step 1 — Freeze the formulation

Already done:

#### Model B

Unit: Product × Week
Horizon: 1 week ahead
Target: all requested quantity
Chronological evaluation
No future information in features
### Step 2 — Establish the real baseline

This comes before ML.

We should implement proper rolling one-week-ahead evaluation for:

Naive-1: last week's demand
4-week moving average

The forecast must advance through time rather than using one fixed prediction for an entire validation period.

### Step 3 — Freeze validation

We choose the chronological training/validation/test periods before looking at test performance.

Validation is where we decide:

preprocessing
model
hyperparameters
feature choices

The test set stays untouched until everything is frozen.

### Step 4 — Train the candidate ML models

Then we test the agreed regression methods, such as:

Linear Regression
Lasso
Ridge
KNN Regressor
Decision Tree
Random Forest

We don't assume any of them is superior.

### Step 5 — Compare ML against the baselines

This is the critical decision point.

For example:

Result	Interpretation
ML clearly beats 4-week MA	Evidence of incremental predictive value
ML slightly beats it	Potential value, but we examine whether improvement is practically meaningful
ML ≈ baseline	Simpler forecasting method may be sufficient
ML loses	We report that honestly; Model B remains a valid forecasting investigation but ML did not add value
### Step 6 — Final untouched test

Only after selecting the approach using validation do we evaluate once on the latest future period.

That gives us the strongest evidence we can obtain from this dataset.

#### One important correction to our thinking

We should not ask “Will Model B be useful?” as though that can be known from EDA alone.

There are actually two separate questions:

1. Is the business problem sensible?
Yes.

2. Does this dataset contain enough predictable information for ML to outperform reasonable forecasting methods?
Unknown until we run the controlled experiment.

That distinction is exactly what will keep this project technically credible.

So from here, I recommend no more feature engineering yet. The next thing should be the proper rolling baseline evaluation, because that establishes the hurdle every ML model must beat.