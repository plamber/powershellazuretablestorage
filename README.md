# Azure Table Storage CRUD operations with PowerShell
This PowerShell module encapsulates basic CRUD operations against [Azure Table Storage](https://azure.microsoft.com/en-us/services/storage/tables/). The module uses the REST API to perform these activities.
This module has been used in several projects to store and manipulate data in automation scripts against Azure Tables.

# Usage
- Get the AZTableModule.psm1 and store it to your desired location
- Get your Azure Storage name and key. You will need it in the script

# Examples

**initializing the module**
$storage = "addYourStorageNameHere"
$key = "addYourStorageSecretKeyHere"

Import-Module AZTableModule.psm1 -ArgumentList $storage, $key

**creating a new table**
New-AzTable "sampletable"

**add a new entry to your table**
- Dates must be older or equal than "1901-01-01"
- Replaces the entry if the unique key and partitionkey matches

$birthDate = (Get-date -date "1983-01-02")
$patrick = @{
    PartitionKey = 'yourPartitionName'
    RowKey = 'yourUniqueRowKeyPatrick'
    "birthDate@odata.type"="Edm.DateTime"
    birthDate = $birthDate.toString("yyyy-MM-ddT00:00:00.000Z")
    name = "Patrick"
    lastname = "Lamber"
}
Add-AzTableEntry -table "sampletable" -partitionKey $patrick.PartitionKey -rowKey $patrick.RowKey -entity $patrick

**create a new entry or merge it with an existing one**
$birthDate = (Get-date -date "1986-10-19")
$rene = @{
    PartitionKey = 'yourPartitionName'
    RowKey = 'yourUniqueRowKeyRene'
    "birthDate@odata.type"="Edm.DateTime"
    birthDate = $birthDate.toString("yyyy-MM-ddT00:00:00.000Z")
    name = "Rene'"
    lastname = "Lamber"
}
Merge-AzTableEntry -table "sampletable" -partitionKey $rene.PartitionKey -rowKey $rene.RowKey -entity $rene

**return a single entry**
$patrickFromTheCloud = Get-AzTableEntry -table "sampletable" -partitionKey $patrick.PartitionKey -rowKey $patrick.RowKey

**update an individual field of an existing entry**
$patrickFromTheCloud = Get-AzTableEntry -table "sampletable" -partitionKey $patrick.PartitionKey -rowKey $patrick.RowKey
$patrickFromTheCloud.name = "Patrick has been updated"
Merge-AzTableEntry -table "sampletable" -partitionKey $patrickFromTheCloud.PartitionKey -rowKey $patrickFromTheCloud.RowKey -entity $patrickFromTheCloud

**get all entries**
$entries = Get-AzTableEntries -table "sampletable"

**select individual fields from the table**
$entriesWithSomeProperties = Get-AzTableEntries -table "sampletable" -select "RowKey,PartitionKey,name"

**filter entries**
$filteredEntries = Get-AzTableEntries -table "sampletable" -filter "name eq 'Patrick'"

**delete an entry**
Remove-AzTableEntry -table "sampletable" -partitionKey $rene.PartitionKey -rowKey $rene.RowKey

**delete a table**
Remove-AzTable -table "sampletable"


