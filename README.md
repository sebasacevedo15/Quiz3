# Quiz3
Quiz3-Gestión de datos SQL 


a. Obtener la categoría, subcategoría, nombre y número de cada producto de la base de datos.

Para este punto se necesitaron las tablas:
production.product ---- De esta tabla se obtuvo Product number y Product Name
production.productsubcategory --- De esta tabla se obtuvo Nombre de categoría y se obtiene la conexión con la tabla Category
production.productcategory --- De esta tabla se obtiene nombre de categoria

select DISTINCT C.name as Category,
	B.name as Subcategory, 
	A.name as product,
	A.productnumber as numero_producto
from production.product A
LEFT JOIN production.productsubcategory B
ON A.productsubcategoryid = B.productsubcategoryid
left join production.productcategory C
ON B.productcategoryid = C.productcategoryid
;

Se encuentra que la mayoría de registros no tienen detalle de categoría o subcategoría

b. Obtener la serie de tiempo mensual del total de ventas por región solo para las regiones de Estados Unidos.

PAra este punto se observa que la región de estados unidos con más ventas en southwest con al rededor de 30Millones de registros. 
Los registros de las regiones que se obtienen con los filtros
"Northeast"
"Southwest"
"Northwest"
"Central"
"Southeast"

SELECT C.mes
 ,SUM(C.sales) as sales
 ,C.region
 ,C.countryregioncode
 FROM(
 SELECT --SUBSTRING(CAST(A.duedate AS VARCHAR),1,4) AS ANIO
 CASE WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='01' THEN 'ene'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='02' THEN 'feb'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='03' THEN 'mar'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='04' THEN 'abr'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='05' THEN 'may'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='06' THEN 'jun'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='07' THEN 'jul'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='08' THEN 'ago'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='09' THEN 'sep'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='10' THEN 'oct'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='11' THEN 'nov'
	 WHEN SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)='12' THEN 'dic'
	 END AS MES
 ,SUM(A.totaldue)  as sales
 ,B.NAME as region
 ,B.COUNTRYREGIONCODE
	FROM sales.salesorderheader A
 left join sales.salesterritory B
	ON A.territoryid=B.territoryid
	Where B.countryregioncode = 'US'
GROUP BY 
--SUBSTRING(CAST(A.duedate AS VARCHAR),1,4) 
 SUBSTRING(CAST(A.orderdate AS VARCHAR),6,2)
 ,B.NAME
 ,B.COUNTRYREGIONCODE
	 )C
	 GROUP BY 
	 C.mes
	 ,C.region
 ,C.countryregioncode
ORDER by C.mes
;


C. La compañía requiere saber el top 3 de  productos con mayor valor siempre y cuando haya existencia en stock para ventas. Es importante para el negocio conocer la categoría del producto.

Se obtiene que los productos con más ventas son:
"Short-Sleeve Classic Jersey, S"
"Full-Finger Gloves, M"
"Racing Socks, M"
con las unidades respectivas son:

4
3
7

select DISTINCT A.name as product,
	C.name as Category,
	A.productnumber as num_producto,
	D.standardcost as valor,
	E.quantity as UnidadesDisponibles
from production.product A
LEFT JOIN production.productsubcategory B
ON A.productsubcategoryid = B.productsubcategoryid
left join production.productcategory C
ON B.productcategoryid = C.productcategoryid
left join production.productcosthistory D
on D.productid = A.productid
left join sales.shoppingcartitem E
on E.productid = D.productid
where D.standardcost IS NOT NULL and E.quantity IS NOT NULL
ORDER BY D.standardcost DESC, E.quantity DESC limit 3



