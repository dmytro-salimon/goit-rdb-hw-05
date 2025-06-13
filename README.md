#1
SELECT od.*, (SELECT o.customer_id FROM orders o WHERE o.id = od.order_id) as customer_id_1
from order_details od;

#2
SELECT od.*
from order_details od
WHERE od.order_id in (SELECT o.id FROM orders o WHERE o.shipper_id=3); 

#3
SELECT od_inner.order_id, AVG(od_inner.quantity)
FROM(SELECT * FROM order_details od WHERE od.quantity > 10) AS od_inner
GROUP BY od_inner.order_id;

#4
WITH temp AS (SELECT * FROM order_details od WHERE od.quantity > 10)
SELECT od_inner.order_id, AVG(od_inner.quantity)
FROM temp od_inner
GROUP BY od_inner.order_id;

#5-FN
DROP FUNCTION IF EXISTS divide_numbers;

delimiter //

CREATE FUNCTION divide_numbers (
num1 FLOAT, 
num2 FLOAT
) RETURNS FLOAT

NO SQL
DETERMINISTIC
BEGIN
  DECLARE result FLOAT;
  IF num2 = 0 THEN
    RETURN NULL;
  ELSE
    SET result=num1/ num2;
  END IF;
  
  RETURN result;  
END
//

delimiter ;

#5
SELECT id, quantity, divide_numbers(quantity, id) FROM order_details;
