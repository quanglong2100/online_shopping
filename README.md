# online_shopping

**Please refer to diagram for full tables relationship view.**

![Database Diagram](online%20shopping%20diagram.png "Online Shopping Diagram")

\- When customers add items to their carts, they are inserting new rows into CART_ORDER table. (PROCEDURE)\
After a new row is inserted/updated/deleted in CART_ORDER table, CART is automatically triggered to reflect the latest TOTAL_PRICE. (TRIGGER)

\- Customers can use VIEW_CART procedure to view the detail of their carts. (PROCEDURE)

\- ADSVERTISEMENT table can be automatically triggered whenever a customer add an item to his/her cart -> search for a random item of the same TYPE -> recommend to that customer. (TRIGGER)\
More advanced: with the presence of DEAL_ID and a significant enough dataset, a model can be trained to detect items usually purchased together (in the same cart) and suggest those items for higher success rate.

______________________________________________________

\- When a customer purchases a cart, it actually deletes that customer's row in CART. (PROCEDURE)

Before a row in CART is deleted, DEAL is triggered to record everything, automatically register action time, and assign a random shipper. (TRIGGER)\
After a row in DEAL is created, DEAL_ORDER is triggered to pull that customer's data from CART_ORDER, and attach the newly-created DEAL_ID to those rows for recording purpose. (TRIGGER)\
After the data in DEAL_ORDER is created, records in CART_ORDER are cleared, ready for a new cart/new purchases. (TRIGGER)

\- Before a purchase is executed, the system checks for the remaining amount of items in storage. (inside purchase procedure)\
If the amounts suffice, the purchase command is executed and the respective amounts are deducted accordingly. (TRIGGER)\
If not, return Error and stop action.

\- Procedure SHIPPING_SLIP: print detail of an order (a.k.a shipping labels).\
\- Procedure FINISHED_DELIVERY: shippers register finished deliveries in table DEAL.

______________________________________________________

\+ VIEW: View on DEAL where STATUS = 'NOT SHIPPED' to check on lazy shippers.\
\+ VIEW: View on DEAL count where STATUS = 'SHIPPED' to check level of success.\
\+ VIEW: View on CART_ORDER -> count total number of items in cart, grouped by items to actively monitor which products customers are hesitating to buy
to accommodate them with timely sale off programs, further boost website's reputation. (or increase the price because of high demand. Show them some Capitalism.)

/!\ AUDIT LOG: Check 4 tables CUSTOMER, SHIPPER, ITEM, VENDOR because they contain important basic data.\
Separate each table into 3 triggers (insert, delete, update) in order to use Cursor to handle bulk activation (SQL Server fires trigger once per statement and no FOR EACH ROW).
