# online_shopping

- When customers add items to their carts, they are actually inserting new rows into CART_ORDER table. (PROCEDURE)\
After a new row is inserted/updated/deleted in CART_ORDER table, CART is automatically triggered to reflect the latest TOTAL_PRICE. (TRIGGER)\
TOTAL_PRICE is calculated by multiplying items' prices by their respective amounts, then sum all items a person has.\

- A view table can be created by joining CART_ORDER and ITEM to show what items a customer currently has in his/her cart, and their respective amounts. (PROCEDURE)\

- When a customer purchases a cart, it actually deletes that customer's row in CART. (PROCEDURE)\
Before a row in CART is deleted, DEAL is triggered to record everything, automatically register action time, and assign a random shipper. (TRIGGER)\
After a row in DEAL is created, DEAL_ORDER is triggered to pull that customer's data from CART_ORDER, and attach the newly-created DEAL_ID to those rows for record purpose. (TRIGGER)\
After the data in DEAL_ORDER is created, records in CART_ORDER are cleared, ready for a new cart/new purchases. (TRIGGER)\

- Before a purchase is executed, the system checks for the remaining amount of items in storage.\
If the amounts suffice, the purchase command is executed and the respective amounts are deducted accordingly. (TRIGGER)\
If not, return Error and stop action.\

- Transaction history (DEAL) and CART tables are separated for faster query time purpose.\

- A shipping slip can be created by joining SHIPPER, DEAL, CUSTOMER, DEAL_ORDER, ITEM, and VENDOR to print vendors' addresses, items and amounts, customer's address, and customer's phone for shipping purpose. (PROCEDURE)\
As known as the small piece of paper containing info glued on the box for shipping purpose.\

- ADSVERTISEMENT table can be automatically triggered whenever a customer add an item to his/her cart -> search for a random item of the same TYPE -> recommend to that customer. (TRIGGER)\
More advanced: with the presence of DEAL_ID and a significant enough dataset, a model can be trained to detect items usually purchased together (in the same cart) and suggest those items for higher success rate.\

+ VIEW: View on DEAL where STATUS = 'NOT SHIPPED' to check on lazy shippers.\
+ VIEW: View on DEAL count where STATUS = 'SHIPPED' to check level of success.\
+ VIEW: View on CART_ORDER -> count total number of items in cart, grouped by items to actively monitor which products customers are hesitating to buy
to accommodate them with timely sale off programs, further boost website's reputation. (or increase the price because of high demand. Show them some Capitalism.)\
+ PROCEDURE: Shippers register finished deliveries.\

/!\ AUDIT LOG: Check 4 tables CUSTOMER, SHIPPER, ITEM, VENDOR because they contain important basic data.\
Separate each table into 3 triggers (insert, delete, update) in order to use Cursor to handle bulk activation (SQL Server fires trigger once per statement and no FOR EACH ROW).\
