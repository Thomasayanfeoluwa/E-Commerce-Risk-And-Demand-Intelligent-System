Column	Available when order is placed?	Potential feature?	Reason
order_id	Yes	No	Identifier, not predictive information
customer_id	Yes	Maybe	Could support historical customer features later
order_date	Yes	Yes	Can derive temporal features
product	Yes	Yes	Product selected at order time
category	Yes	Yes	Category selected at order time
quantity	Yes	Yes	Quantity requested at order time
unit_price	Yes	Yes	Price known at order time
discount_percent	Yes	Yes	Discount known at order time
city	Yes	Yes	Assuming shipping location is known
payment_method	Yes	Yes	Assuming selected at checkout
order_status	No	Target	This is what we're trying to predict
total_amount	?	?	Must investigate how it is generated