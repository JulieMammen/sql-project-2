# sql-project-2-nashville housing data
***SELECTING TOP 1000 ROWS
SELECT TOP (1000)
UniqueID,ParcelID,LandUse,PropertyAddress,SaleDate,SalePrice,LegalReference,
SoldAsVacant,OwnerName,OwnerAddress,Acreage,
TaxDistrict,LandValue,BuildingValue,TotalValue,
YearBuilt,Bedrooms,FullBath, HalfBath

FROM dbo.nashvillehousing
DAta exploration
SELECT*
FROM dbo.nashvillehousing
***Saledate

SELECT SaleDate,CONVERT(Date,SaleDate)
FROM dbo.nashvillehousing

SELECT SaleDateConverted ,CONVERT(Date,SaleDate)
FROM dbo.nashvillehousing
ALTER TABLE nashvillehousing
Add SaleDateConverted 
Update nashvillehousing
SET SaleDateConverted=CONVERT(Date,SaleDate)


***Populating Property address

SELECT *
---WHERE PropertyAddress is null
FROM dbo.nashvillehousing
Order by ParcelID;

SELECT a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress,ISNULL(a.PropertyAddress,b.PropertyAddress)
FROM dbo.nashvillehousing A
Join dbo.nashvillehousing b
on a.ParcelID=b.ParcelID
AND a.[UniqueID ]<>b.[UniqueID ]
WHERE a.PropertyAddress is null
Update a
SET PropertyAddress=ISNULL(a.PropertyAddress,b.PropertyAddress)
FROM dbo.nashvillehousing A
Join dbo.nashvillehousing b
on a.ParcelID=b.ParcelID
AND a.[UniqueID ]<>b.[UniqueID ]
WHERE a.PropertyAddress is null
---Breaking out address into individual column as address,city &state
SELECT PropertyAddress
---WHERE PropertyAddress is null
FROM dbo.nashvillehousing
---Order by ParcelID;
SELECT
SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress) -1)As Address
FROM dbo.nashvillehousing
----1took off comma at the end of the address
SELECT
SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress) -1) as Address,
SUBSTRING(PropertyAddress,CHARINDEX(',',PropertyAddress) +1,LEN(PropertyAddress)) as Address
FROM dbo.nashvillehousing
--adding new columns to the main table
ALTER TABLE Nashvillehousing
Add PropertySplitaddress Nvarchar(255)
Update nashvillehousing 
SET PropertySplitAddress=SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress) -1)
Alter Table Nashvillehousing
Add PropertySplitCity Nvarchar(255)
Update nashvillehousingO noO 
SET PropertySplitCity=SUBSTRING(PropertyAddress,CHARINDEX(',',PropertyAddress) +1,LEN(PropertyAddress))
SELECT *
FROM dbo.nashvillehousing
--Easier O no...O no...Get wel
Select
PARSENAME(REPLACE(OwnerAddress,',','.'),3),
PARSENAME(REPLACE(OwnerAddress,',','.'),2),
PARSENAME(REPLACE(OwnerAddress,',','.'),1)
FROM dbo.nashvillehousing
ALTER TABLE Nashvillehousing
Add OwnerPropertySplitaddress Nvarchar(255)
Update nashvillehousing 
SET OwnerPropertySplitAddress=PARSENAME(REPLACE(OwnerAddress,',','.'),3)
Alter Table Nashvillehousing
Add OwnerPropertySplitCity Nvarchar(255)
Update nashvillehousing
SET OwnerPropertySplitCity=PARSENAME(REPLACE(OwnerAddress,',','.'),2)
ALTER TABLE Nashvillehousing
Add OwnerPropertySplitState Nvarchar(255)
Update nashvillehousing
SET OwnerPropertySplitState=PARSENAME(REPLACE(OwnerAddress,',','.'),1)
SELECT *
FROM dbo.nashvillehousing
---Change Y & N to Yes &NO in sold as vacant field
SELECT Distinct(SoldAsVacant),COUNT(SoldAsVacant)
From dbo.nashvillehousing
Group by SoldAsVacant
Select SoldAsVacant,
Case when SoldAsVacant='Y'THEN 'YES'
 when SoldAsVacant='N'Then 'NO'
Else SoldAsVacant
END
From dbo.nashvillehousing

UPDATE nashvillehousing
SET SoldAsVacant=Case when SoldAsVacant='Y'THEN 'YES'
 when SoldAsVacant='N'Then 'NO'
Else SoldAsVacant
END
---Remove duplicates
WITH RowNUMCTE AS(
SELECT *
,ROW_NUMBER() OVER(PARTITION BY ParcelId,
PropertyAddress,SalePrice,
SaleDate,
LegalReference
Order by 
UniqueID)ROW_NUM
FROM dbo.nashvillehousing
SELECT *,
FROM Row_numCTE
WHERE row_num>1
Order by PropertyAddress
---DELETING UNUSED COLUMNS
SELECT *
FROM dbo.nashvillehousing
ALTER TABLE dbo.nashvillehousing
DROP COLUMN OWNERAddress,TaxDistrict,PropertyAddress
