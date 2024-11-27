# SQL-for-filtering
SQL code for filtering the necesary data

WITH
  maintable AS (
  SELECT
    DISTINCT cs.CustomerID,
    con.Firstname,
    con.LastName,
    CONCAT(con.Firstname, ' ',con.LastName) AS full_name,
    CASE
      WHEN con.title IS NULL THEN CONCAT('Dear', ' ',con.Lastname)
    ELSE
    CONCAT(con.title, ' ', con.lastname)
  END
    AS addressing_title,
    con.EmailAddress,
    con.phone,
    cs.accountnumber,
    cs.customertype,
    ad.city,
    sp.name AS state,
    cr.name AS Country,
    COUNT(soh.salesorderid) OVER(PARTITION BY cs.customerID) AS number_orders,
    ROUND(SUM(soh.TotalDue) OVER(PARTITION BY cs.customerID), 2) AS total_amount,
    MAX(soh.OrderDate) OVER(PARTITION BY cs.CustomerID) AS date_last_order,
    MAX(ad.addressID) OVER (PARTITION BY cs.CustomerID) AS latest_adress_id
  FROM
    `tc-da-1.adwentureworks_db.customer` cs
  JOIN
    `adwentureworks_db.individual` ind
  ON
    cs.CustomerID=ind.CustomerID
  JOIN
    `adwentureworks_db.contact` con
  ON
    ind.ContactID=con.ContactId
  JOIN
    `adwentureworks_db.customeraddress` csa
  ON
    cs.CustomerID=csa.CustomerID
  JOIN
    adwentureworks_db.address ad
  ON
    csa.addressId=ad.AddressID
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    ad.StateProvinceID=sp.StateProvinceID
  JOIN
    `adwentureworks_db.countryregion` cr
  ON
    sp.CountryRegionCode=cr.CountryRegionCode
  JOIN
    `adwentureworks_db.salesorderheader` soh
  ON
    con.contactId=soh.ContactID
  WHERE
    CustomerType LIKE 'I'
  ORDER BY
    total_amount DESC),
  addresstable AS (
  SELECT
    customer.customerID,
    MAX(address.AddressLine1) AS AddressLine1,
    MAX(address.AddressLine2) AS AddressLine2
  FROM
    `adwentureworks_db.customer` AS customer
  JOIN
    `adwentureworks_db.customeraddress` AS customeraddress
  ON
    customer.customerID=customeraddress.CustomerID
  JOIN
    `adwentureworks_db.address` AS address
  ON
    customeraddress.addressID=address.AddressID
  GROUP BY
    CustomerID)
SELECT
  *
FROM
  maintable
JOIN
  addresstable
ON
  maintable.customerID=addresstable.customerID






WITH
  maintable AS (
  SELECT
    DISTINCT cs.CustomerID,
    con.Firstname,
    con.LastName,
    CONCAT(con.Firstname, ' ',con.LastName) AS full_name,
    CASE
      WHEN con.title IS NULL THEN CONCAT('Dear', ' ',con.Lastname)
    ELSE
    CONCAT(con.title, ' ', con.lastname)
  END
    AS addressing_title,
    con.EmailAddress,
    con.phone,
    cs.accountnumber,
    cs.customertype,
    ad.city,
    sp.name AS state,
    cr.name AS Country,
    COUNT(soh.salesorderid) OVER(PARTITION BY cs.customerID) AS number_orders,
    ROUND(SUM(soh.TotalDue) OVER(PARTITION BY cs.customerID), 2) AS total_amount,
    MAX(soh.OrderDate) OVER(PARTITION BY cs.CustomerID) AS date_last_order,
    MAX(ad.addressID) OVER (PARTITION BY cs.CustomerID) AS latest_adress_id
  FROM
    `tc-da-1.adwentureworks_db.customer` cs
  JOIN
    `adwentureworks_db.individual` ind
  ON
    cs.CustomerID=ind.CustomerID
  JOIN
    `adwentureworks_db.contact` con
  ON
    ind.ContactID=con.ContactId
  JOIN
    `adwentureworks_db.customeraddress` csa
  ON
    cs.CustomerID=csa.CustomerID
  JOIN
    adwentureworks_db.address ad
  ON
    csa.addressId=ad.AddressID
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    ad.StateProvinceID=sp.StateProvinceID
  JOIN
    `adwentureworks_db.countryregion` cr
  ON
    sp.CountryRegionCode=cr.CountryRegionCode
  JOIN
    `adwentureworks_db.salesorderheader` soh
  ON
    con.contactId=soh.ContactID
  WHERE
    CustomerType LIKE 'I'
  ORDER BY
    total_amount DESC),
  addresstable AS (
  SELECT
    customer.customerID,
    MAX(address.AddressLine1) AS AddressLine1,
    MAX(address.AddressLine2) AS AddressLine2
  FROM
    `adwentureworks_db.customer` AS customer
  JOIN
    `adwentureworks_db.customeraddress` AS customeraddress
  ON
    customer.customerID=customeraddress.CustomerID
  JOIN
    `adwentureworks_db.address` AS address
  ON
    customeraddress.addressID=address.AddressID
  GROUP BY
    CustomerID)
SELECT
  *
FROM
  maintable
JOIN
  addresstable
ON
  maintable.customerID=addresstable.customerID
WHERE
  maintable.date_last_order<'2003-07-31'
ORDER BY
  maintable.total_amount DESC
LIMIT
  200;







WITH
  maintable AS (
  SELECT
    DISTINCT cs.CustomerID,
    con.Firstname,
    con.LastName,
    CONCAT(con.Firstname, ' ',con.LastName) AS full_name,
    CASE
      WHEN con.title IS NULL THEN CONCAT('Dear', ' ',con.Lastname)
    ELSE
    CONCAT(con.title, ' ', con.lastname)
  END
    AS addressing_title,
    con.EmailAddress,
    con.phone,
    cs.accountnumber,
    cs.customertype,
    ad.city,
    sp.name AS state,
    cr.name AS Country,
    COUNT(soh.salesorderid) OVER(PARTITION BY cs.customerID) AS number_orders,
    ROUND(SUM(soh.TotalDue) OVER(PARTITION BY cs.customerID), 2) AS total_amount,
    MAX(soh.OrderDate) OVER(PARTITION BY cs.CustomerID) AS date_last_order,
    MAX(ad.addressID) OVER (PARTITION BY cs.CustomerID) AS latest_adress_id,
    CASE
      WHEN MAX(soh.OrderDate) OVER(PARTITION BY cs.CustomerID)>'2003-07-31' THEN 'active'
    ELSE
    'inactive'
  END
    AS active_or_inactive
  FROM
    `tc-da-1.adwentureworks_db.customer` cs
  JOIN
    `adwentureworks_db.individual` ind
  ON
    cs.CustomerID=ind.CustomerID
  JOIN
    `adwentureworks_db.contact` con
  ON
    ind.ContactID=con.ContactId
  JOIN
    `adwentureworks_db.customeraddress` csa
  ON
    cs.CustomerID=csa.CustomerID
  JOIN
    adwentureworks_db.address ad
  ON
    csa.addressId=ad.AddressID
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    ad.StateProvinceID=sp.StateProvinceID
  JOIN
    `adwentureworks_db.countryregion` cr
  ON
    sp.CountryRegionCode=cr.CountryRegionCode
  JOIN
    `adwentureworks_db.salesorderheader` soh
  ON
    con.contactId=soh.ContactID
  WHERE
    CustomerType LIKE 'I'
  ORDER BY
    total_amount DESC),
  addresstable AS (
  SELECT
    customer.customerID,
    MAX(address.AddressLine1) AS AddressLine1,
    MAX(address.AddressLine2) AS AddressLine2
  FROM
    `adwentureworks_db.customer` AS customer
  JOIN
    `adwentureworks_db.customeraddress` AS customeraddress
  ON
    customer.customerID=customeraddress.CustomerID
  JOIN
    `adwentureworks_db.address` AS address
  ON
    customeraddress.addressID=address.AddressID
  GROUP BY
    CustomerID)
SELECT
  *
FROM
  maintable
JOIN
  addresstable
ON
  maintable.customerID=addresstable.customerID






WITH
  maintable AS (
  SELECT
    DISTINCT cs.CustomerID,
    con.Firstname,
    con.LastName,
    CONCAT(con.Firstname, ' ',con.LastName) AS full_name,
    CASE
      WHEN con.title IS NULL THEN CONCAT('Dear', ' ',con.Lastname)
    ELSE
    CONCAT(con.title, ' ', con.lastname)
  END
    AS addressing_title,
    con.EmailAddress,
    con.phone,
    cs.accountnumber,
    cs.customertype,
    ad.city,
    sp.name AS state,
    cr.name AS Country,
    st.group AS territory_name,
    COUNT(soh.salesorderid) OVER(PARTITION BY cs.customerID) AS number_orders,
    ROUND(SUM(soh.TotalDue) OVER(PARTITION BY cs.customerID), 2) AS total_amount,
    MAX(soh.OrderDate) OVER(PARTITION BY cs.CustomerID) AS date_last_order,
    MAX(ad.addressID) OVER (PARTITION BY cs.CustomerID) AS latest_adress_id,
    CASE
      WHEN MAX(soh.OrderDate) OVER(PARTITION BY cs.CustomerID)>'2003-07-31' THEN 'active'
    ELSE
    'inactive'
  END
    AS active_or_inactive
  FROM
    `tc-da-1.adwentureworks_db.customer` cs
  JOIN
    `adwentureworks_db.individual` ind
  ON
    cs.CustomerID=ind.CustomerID
  JOIN
    `adwentureworks_db.contact` con
  ON
    ind.ContactID=con.ContactId
  JOIN
    `adwentureworks_db.customeraddress` csa
  ON
    cs.CustomerID=csa.CustomerID
  JOIN
    adwentureworks_db.address ad
  ON
    csa.addressId=ad.AddressID
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    ad.StateProvinceID=sp.StateProvinceID
  JOIN
    `adwentureworks_db.countryregion` cr
  ON
    sp.CountryRegionCode=cr.CountryRegionCode
  JOIN
    `adwentureworks_db.salesorderheader` soh
  ON
    con.contactId=soh.ContactID
  JOIN
    `adwentureworks_db.salesterritory` st
  ON
    soh.TerritoryID=st.TerritoryID
  WHERE
    CustomerType LIKE 'I'
  ORDER BY
    total_amount DESC),
  addresstable AS (
  SELECT
    customer.customerID,
    MAX(address.AddressLine1) AS AddressLine1,
    MAX(address.AddressLine2) AS AddressLine2
  FROM
    `adwentureworks_db.customer` AS customer
  JOIN
    `adwentureworks_db.customeraddress` AS customeraddress
  ON
    customer.customerID=customeraddress.CustomerID
  JOIN
    `adwentureworks_db.address` AS address
  ON
    customeraddress.addressID=address.AddressID
  GROUP BY
    CustomerID)
SELECT
  *,
  LEFT(addresstable.addressline1, STRPOS(addresstable.addressline1, ' ') -1) AS address_no,
  TRIM(REGEXP_REPLACE(addresstable.addressline1, r'^[0-9]+ ', '')) AS Address_st
FROM
  maintable
JOIN
  addresstable
ON
  maintable.customerID=addresstable.customerID
WHERE
  maintable.active_or_inactive LIKE 'active'
  AND ((maintable.number_orders>5)
    OR (maintable.total_amount>=2500))
  AND maintable.territory_name LIKE 'North America'
ORDER BY
  maintable.Country,
  maintable.state,
  maintable.date_last_order;







SELECT
  DATE_TRUNC(soh.OrderDate, month) AS order_month,
  st.countryregioncode AS countryregioncode,
  st.name AS region,
  COUNT(soh.SalesOrderID) AS number_orders,
  COUNT (DISTINCT soh.CustomerID) AS number_customers,
  COUNT(DISTINCT soh.SalesPersonID) AS noSalesPerson,
  ROUND(SUM(soh.TotalDue),2) AS total_w_tax
FROM
  `adwentureworks_db.salesorderheader` soh
JOIN
  `adwentureworks_db.salesterritory` sp
ON
  soh.TerritoryID=sp.TerritoryID
JOIN
  `adwentureworks_db.salesterritory` st
ON
  soh.TerritoryID=st.TerritoryID
GROUP BY
  order_month,
  CountryRegionCode,
  region;





WITH
  table1 AS (
  SELECT
    DATE_TRUNC(soh.OrderDate, month) AS order_month,
    st.countryregioncode AS countryregioncode,
    st.name AS region,
    COUNT(soh.SalesOrderID) AS number_orders,
    COUNT (DISTINCT soh.CustomerID) AS number_customers,
    COUNT(DISTINCT soh.SalesPersonID) AS noSalesPerson,
    ROUND(SUM(soh.totaldue),2) AS total_w_tax
  FROM
    `adwentureworks_db.salesorderheader` soh
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    soh.TerritoryID=sp.TerritoryID
  JOIN
    `adwentureworks_db.salesterritory` st
  ON
    soh.TerritoryID=st.TerritoryID
  GROUP BY
    order_month,
    CountryRegionCode,
    region
  ORDER BY
    countryregioncode)
SELECT
  *,
  ROUND(SUM(table1.total_w_tax) OVER(PARTITION BY table1.countryregioncode ORDER BY table1.order_month)) AS cumulative_sum
FROM
  table1;





WITH
  table1 AS (
  SELECT
    DATE_TRUNC(soh.OrderDate, month) AS order_month,
    st.countryregioncode AS countryregioncode,
    st.name AS region,
    COUNT(soh.SalesOrderID) AS number_orders,
    COUNT (DISTINCT soh.CustomerID) AS number_customers,
    COUNT(DISTINCT soh.SalesPersonID) AS noSalesPerson,
    ROUND(SUM(soh.totaldue),2) AS total_w_tax
  FROM
    `adwentureworks_db.salesorderheader` soh
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    soh.TerritoryID=sp.TerritoryID
  JOIN
    `adwentureworks_db.salesterritory` st
  ON
    soh.TerritoryID=st.TerritoryID
  GROUP BY
    order_month,
    CountryRegionCode,
    region
  ORDER BY
    countryregioncode)
SELECT
  *,
  RANK() OVER(PARTITION BY region ORDER BY table1.total_w_tax) AS RANK,
  ROUND(SUM(table1.total_w_tax) OVER(PARTITION BY table1.region ORDER BY table1.order_month)) AS cumulative_sum
FROM
  table1
ORDER BY
  region,
  rank;





WITH
  maintable AS (
  SELECT
    DATE_TRUNC(soh.OrderDate, month) AS order_month,
    st.countryregioncode AS countryregioncode,
    st.name AS region,
    COUNT(soh.SalesOrderID) AS number_orders,
    COUNT (DISTINCT soh.CustomerID) AS number_customers,
    COUNT(DISTINCT soh.SalesPersonID) AS noSalesPerson,
    ROUND(SUM(soh.totaldue),2) AS total_w_tax,
    sp.StateProvinceID AS stateProvinceID,
    str.TaxRate
  FROM
    `adwentureworks_db.salesorderheader` soh
  JOIN
    `adwentureworks_db.stateprovince` sp
  ON
    soh.TerritoryID=sp.TerritoryID
  JOIN
    `adwentureworks_db.salestaxrate` str
  ON
    sp.stateProvinceID=str.StateProvinceID
  JOIN
    `adwentureworks_db.salesterritory` st
  ON
    soh.TerritoryID=st.TerritoryID
  GROUP BY
    order_month,
    CountryRegionCode,
    region,
    StateprovinceID,
    TaxRate
  ORDER BY
    countryregioncode),
  taxtable AS (
  SELECT
    sp.Name AS Region,
    sp.CountryRegionCode,
    str.TaxRate,
    ROUND((COUNT(str.taxrate) OVER (PARTITION BY sp.countryregioncode))/COUNT(*) OVER (PARTITION BY sp.CountryRegionCode),1) AS tax_percentage
  FROM
    `adwentureworks_db.stateprovince` sp
  FULL OUTER JOIN
    `adwentureworks_db.salestaxrate` str
  ON
    sp.StateProvinceID=str.StateProvinceID
  ORDER BY
    CountryRegionCode DESC)
SELECT
  DISTINCT maintable.order_month,
  maintable.countryregioncode,
  maintable.region,
  maintable.number_orders,
  maintable.number_customers,
  maintable.noSalesPerson,
  maintable.total_w_tax,
  maintable.stateProvinceID,
  ROUND(AVG(maintable.taxrate) OVER (PARTITION BY maintable.region)) AS averagetaxrate,
  RANK() OVER(PARTITION BY maintable.region ORDER BY maintable.total_w_tax) AS RANK,
  ROUND(SUM(maintable.total_w_tax) OVER(PARTITION BY maintable.countryregioncode ORDER BY maintable.order_month)) AS cumulative_sum,
  taxtable.tax_percentage
FROM
  maintable
JOIN
  taxtable
ON
  maintable.countryregioncode=taxtable.countryregioncode
ORDER BY
  region,
  rank;
