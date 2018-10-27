---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: e8c5bf8e3c4cd63b7eec278c480527e95455140d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165865"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Zarządzanie jednostkami tabeli

Teraz, gdy masz tabelę, Przyjrzyjmy się jak zarządzać jednostki lub wiersze w tabeli. 

Jednostka może mieć maksymalnie 255 właściwości, w tym 3 Właściwości systemowe: **PartitionKey**, **RowKey**, i **sygnatura czasowa**. Odpowiedzialność za wstawiania i aktualizowania wartości **PartitionKey** i **RowKey**. Serwer zarządza wartością **sygnatura czasowa**, który nie może być modyfikowany. Razem **PartitionKey** i **RowKey** jednoznacznie zidentyfikować każda jednostka w obrębie tabeli.

* **PartitionKey**: Określa jednostki przechowywane w partycji.
* **RowKey**: jednoznacznie identyfikują jednostkę w ramach partycji.

Można zdefiniować maksymalnie 252 właściwości niestandardowych dla jednostki. 

### <a name="add-table-entities"></a>Dodawanie jednostki do tabeli

Dodawanie jednostki do tabeli przy użyciu **StorageTableRow Dodaj**. Te przykłady użycia kluczy partycji o wartości "Partycja1" i "Partycja2" oraz kluczy wierszy równa zawierający skróty nazw stanów. Właściwości w każdej jednostce są nazwy użytkownika i identyfikator użytkownika. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Zapytanie jednostki tabeli

Istnieje kilka różnych sposobów, aby wysłać zapytanie jednostki w tabeli.

#### <a name="retrieve-all-entities"></a>Pobieranie wszystkich jednostek

Aby pobrać wszystkie jednostki, użyj **Get AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

To polecenie daje wyniki podobne do poniższej tabeli:

| Identyfikator użytkownika | nazwa użytkownika | partycja | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partycja1 | Urząd certyfikacji |
| 3 | Christine | Partycja1 | WA |
| 2 | Jessie | Partycja2 | NM |
| 4 | Steven | Partycja2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Pobieranie jednostek dla określonej partycji

Aby pobrać wszystkie jednostki w określonej partycji, użyj **Get AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Wyniki wyglądać podobnie do poniższej tabeli:

| Identyfikator użytkownika | nazwa użytkownika | partycja | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partycja1 | Urząd certyfikacji |
| 3 | Christine | Partycja1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Pobieranie jednostek dla określonej wartości w określonej kolumnie

Aby pobrać jednostki, w którym wartość w określonej kolumnie jest równa określonej wartości, użyj **Get AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

To zapytanie pobiera jeden rekord.

|Pole|wartość|
|----|----|
| Identyfikator użytkownika | 1 |
| nazwa użytkownika | Chris |
| PartitionKey | Partycja1 |
| RowKey      | Urząd certyfikacji |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Pobieranie jednostki przy użyciu niestandardowego filtru 

Aby pobrać jednostki przy użyciu niestandardowego filtru, użyj **Get AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

To zapytanie pobiera jeden rekord.

|Pole|wartość|
|----|----|
| Identyfikator użytkownika | 1 |
| nazwa użytkownika | Chris |
| PartitionKey | Partycja1 |
| RowKey      | Urząd certyfikacji |

### <a name="updating-entities"></a>Aktualizowanie jednostek 

Istnieją trzy kroki w celu zaktualizowania jednostki. Najpierw należy pobrać jednostkę którą chcesz zmienić. Po drugie wprowadzić zmiany. Po trzecie, Zatwierdź zmiany, przy użyciu **AzureStorageTableRow aktualizacji**.

Aktualizowanie jednostki przy użyciu nazwy użytkownika = "Jessie", aby nazwa użytkownika = "Jessie2". Ten przykład pokazuje także innym sposobem tworzenia niestandardowego filtru przy użyciu typów .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

Wyniki wskazują rekord Jessie2.

|Pole|wartość|
|----|----|
| Identyfikator użytkownika | 2 |
| nazwa użytkownika | Jessie2 |
| PartitionKey | Partycja2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Usuwanie jednostek tablic

Można usunąć jednego lub wszystkich jednostek w tabeli.

#### <a name="deleting-one-entity"></a>Usuwanie jednej jednostki

Aby usunąć pojedynczą jednostkę, Pobierz odwołanie do tej jednostki i przekazać go do **AzureStorageTableRow Usuń**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Usuń wszystkie jednostki w tabeli 

Aby usunąć wszystkie jednostki w tabeli, je pobrać i przekazać wyniki do polecenia cmdlet remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
