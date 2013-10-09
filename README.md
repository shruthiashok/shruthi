Assignment 3.sql


QUERY 1
a)Find the model number, speed, and hard-disk size for all PC’s whose price is under $1000.00.
SELECT MODEL, SPEED, HD FROM PC WHERE PRICE<1000 ORDER BY MODEL;

b) Do the same as (a), but rename the speed column ‘gigahertz’ and the hd column ‘gigabytes’.
Alter table "PC" rename column "SPEED" to "GIGAHERTZ" ;
Alter table "PC" rename column "HD" to "GIGABYTES" ;
SELECT MODEL, GIGAHERTZ, GIGABYTES FROM PC WHERE PRICE<1000 ORDER BY MODEL;

c) Find the manufacturers of printers.
SELECT MAKER FROM PRODUCT WHRE TYPE="PRINTER";

d) Find the model number, memory size, and screen size for laptops costing more than $1000.00.
SELECT MODEL, RAM, SCREEN FROM LAPTOP WHERE PRICE>1000 ORDER BY MODEL;
e) Find all the tuples in the Printer relation for color printers.
SELECT * FROM PRINTER WHERE COLOR=TRUE ORDER BY MODEL;

f) Find the model number and hard-disk size for those PC’s that have a speed of 3.2 and a price less than $2000.00
SELECT MODEL, HD FROM (SELECT * FROM PC WHERE PRICE<2000) WHERE SPEED=3.2  ORDER BY MODEL;

QUERY 2
a) Give the manufacturer and speed of laptops with a hard disk of at least thirty gigabytes.
SELECT MAKER, SPEED FROM PRODUCT NATURAL JOIN LAPTOP WHERE HD>=30;

b) Find the model number and price of all products (of any type) made by manufacturer B.

SELECT PC.MODEL, PC.PRICE
FROM PRODUCT P, PC
WHERE P.MAKER='B' AND P.MODEL = PC.MODEL
UNION
SELECT L.MODEL, L.PRICE
FROM PRODUCT P, LAPTOP L
WHERE P.MAKER='B' AND P.MODEL = L.MODEL
UNION
SELECT PR.MODEL, PR.PRICE
FROM PRODUCT P, PRINTER PR
WHERE P.MAKER='B' AND P.MODEL = PR.MODEL

c) Find those manufacturers that sell Laptops, but not PC’s.
SELECT MAKER 
FROM PRODUCT 
WHERE TYPE=LAPTOP AND MAKER NOT IN (SELECT MAKER FROM PRODUCT WHERE TYPE=PC)

d) Find those hard-disk sizes that occur in two or more PC’s.
SELECT HD, COUNT(*)
FROM PC
GROUP BY HD 
HAVING COUNT(*) > 1 ORDER BY HD;

e) Find those pairs of PC models that have both the same speed and RAM
SELECT p1.model, p2.model
FROM PC p1, PC p2
WHERE p1.speed=p2.speed AND p1.ram=p2.ram p1.model>p2.model

f) Find those manufacturers of at least two different computers (PC’s or laptops) with speeds of atleast 3.0.
SELECT DISTINCT P.maker
FROM Product P, Product P1,
WHERE P.maker = P1.maker
and P.model in (select PC.model from PC where PC.speed>3.0 UNION select LP.model from Laptop LP where Lp.speed>3.0)
and P1.model in (select PC.model from PC where PC.speed>3.0 UNION select LP.model from Laptop LP where Lp.speed>3.0)
and P.model <> P1.model

QUERY 3
a) Find the makers of PC’s with a speed of at least 3.0.
SELECT MAKER FROM PRODUCT WHERE MODEL IN (SELECT MODEL FROM PC WHERE SPEED>=3.0)
b) Find the printers with the highest price.
SELECT MODEL FROM PRINTER WHERE PRICE =(SELECT MAX(PRICE) FROM PRINTER) ;
c) Find the laptops whose speed is slower than that of any PC.
SELECT MODEL FROM LAPTOP WHERE SPEED <= ALL (SELECT SPEED FROM PC);

d) Find the model number of the item (PC, laptop, or printer) with the highest price.
SELECT MODEL FROM 
(SELECT MODEL, PRICE FROM PC) UNION (SELECT MODEL, PRICE FROM LAPTOP) UNION (SELECT MODEL, PRICE FROM PC) MODEL_PRICE
WHERE PRICE >= ALL (SELECT PRICE FROM MODEL_PRICE)

e) Find the maker of the color printer with the lowest price.
Select distinct maker from Product, Printer
Where color=true and Printer.model=Product.model
And price <= ALL (Select price from Printer where color=true)

f) Find the maker(s) of the PC(s) with the fastest processor among all those PC’s that have the smallest amount of RAM.
Select distinct maker from Product, PC
Where Product.model=PC.model
And Ram <= ALL (Select ram from PC)
And Speed >= ALL (Select speed from PC where ram=(Select min(ram) from PC))

QUERY 4
a) Find the average speed of PC’s.
SELECT AVG(SPEED) FROM PC;

b) Find the average speed of laptops costing over $1000.00.
SELECT AVG(SPEED) FROM LAPTOP WHERE PRICE>1000;

c) Find the average price of PC’s make by manufacturer ‘A’.
SELECT AVG(PRICE) FROM PC NATURAL JOIN PRODUCT WHERE MAKER="A";
OR 
SELECT AVG(PRICE) FROM PC WHERE MODEL IN (SELECT MODEL FROM PRODUCT WHERE MAKER="A");

d) Find the average price of PC’s and laptops made by manufacturer ‘B’.
SELECT AVG(MODEL_PRICE) FROM 
(SELECT PRICE FROM PC WHERE MODEL= (SELECT MODEL FROM PRODUCT WHERE MAKER="B" ) ) UNION (SELECT PRICE FROM LAPTOP  WHERE MODEL= (SELECT MODEL FROM PRODUCT WHERE MAKER="B" ) ) MODEL_PRICE;
e) Find, for each different speed, the average price of a PC.
SELECT SPEED, AVG(PRICE)
FROM
(
    SELECT SPEED AS SPEED, PRICE AS PRICE  FROM PC
    UNION ALL
    SELECT SPEED, PRICE FROM PC
)
GROUP BY SPEED ORDER BY SPEED;

f) Find for each manufacturer, the average screen size of its laptops.
SELECT maker, AVG(screen)
FROM Product, Laptop
WHERE Product.model = Laptop.model
GROUP BY maker;

g) Find the manufacturers that make at least three different models of PC.
SELECT maker
FROM Product, PC WHERE Product.model = PC.model AND COUNT(MAKER)>=3 AND TYPE="PC" GROUP BY maker;
h) Find for each manufacturer who sells PC’s the maximum price of a PC.
SELECT maker
FROM Product, PC
WHERE Product.model = PC.model AND MAX(PRICE)
GROUP BY maker;

i) Find, for each speed of PC above 2.0, the average price.
SELECT AVG(PRICE)
FROM PC
WHERE speed>2.0 ORDER BY AVG(PRICE);

j) Find the average hard disk size of a PC for all those manufactures that make printers.
SELECT AVG(HD)
FROM PC, PRODUCT
WHERE TYPE="PRINTER";

QUERY 5

a) Using two INSERT statements, store in the database the fact that PC model 1100 is made by manufacturer C, has a speed of 3.2, RAM 1024, hard disk 180, and sells for $2400.00.
INSERT INTO PC(model, speed, ram, hd, price) VALUES(1100,3.2,1024,180,2400);
INSERT INTO PRODUCT(maker, model, type) VALUES('C',1100,PC);

c) Delete all PC’s with less than 100 gigabytes of hard disk.
DELETE FROM PC
WHERE HD<100;

d) Delete all laptops made by a manufacturer that doesn’t make printers.
DELETE FROM LAPTOP
WHERE IN (SLECT MAKER FROM PRODUCT WHERE TYPE != "PRINTER");

e) Manufacturer A buys manufacturer B. Change all products made by B so they are now made by A.
UPDATE PRODUCT
SET MAKER='A'
WHERE MAKER='B'; 


f) For each PC, double the amount of RAM and add 60 gigabytes to the amount of hard disk.
UPDATE PC
SET RAM=2*RAM, HD=HD+60; 

g) For each laptop made by manufacturer B, add one inch to the screen size and subtract $100.00 from the price.
UPDATE LAPTOP
SET SCREEN='SCREEN+100'
WHERE MODEL= (SELECT MODEL FROM PRODUCT WHERE MAKER='B');
