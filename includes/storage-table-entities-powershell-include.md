---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182906"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Zarządzanie jednostkami tabeli

Teraz, gdy masz tabelę, Przyjrzyjmy się jak zarządzać jednostki lub wiersze w tabeli. 

Jednostki może mieć maksymalnie 255 właściwości, w tym trzy właściwości systemowe: **PartitionKey**, **RowKey**, i **sygnatura czasowa**. Jesteś odpowiedzialny za wstawiania i aktualizowania wartości **PartitionKey** i **RowKey**. Serwer zarządza wartością **sygnatura czasowa**, który nie może być modyfikowany. Razem **PartitionKey** i **RowKey** jednoznacznie zidentyfikować każda jednostka w obrębie tabeli.

* **PartitionKey**: Określa jednostki przechowywane w partycji.
* **RowKey**: Jednoznacznie identyfikuje jednostek w partycji.

Można zdefiniować maksymalnie 252 właściwości niestandardowych dla jednostki. 

### <a name="add-table-entities"></a>Dodawanie jednostki do tabeli

Dodawanie jednostki do tabeli przy użyciu **AzTableRow Dodaj**. Te przykłady użycia kluczy partycji przy użyciu wartości `partition1` i `partition2`, i kluczy wierszy równa zawierający skróty nazw stanów. Właściwości w każdej jednostki są `username` i `userid`. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Zapytanie jednostki tabeli

Wykonywanie zapytań jednostek w tabeli przy użyciu **Get AzTableRow** polecenia.

> [!NOTE]
> Polecenia cmdlet **Get AzureStorageTableRowAll**, **Get AzureStorageTableRowByPartitionKey**, **Get AzureStorageTableRowByColumnName**, i  **Get-AzureStorageTableRowByCustomFilter** są przestarzałe i zostanie usunięta w przyszłej wersji aktualizacji.

#### <a name="retrieve-all-entities"></a>Pobieranie wszystkich jednostek

```powershell
Get-AzTableRow -table $cloudTable | ft
```

To polecenie daje wyniki podobne do poniższej tabeli:

| userid | username | partycji | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partycja1 | CA |
| 3 | Christine | Partycja1 | WA |
| 2 | Jessie | Partycja2 | NM |
| 4 | Steven | Partycja2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Pobieranie jednostek dla określonej partycji

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Wyniki wyglądać podobnie do poniższej tabeli:

| userid | username | partycji | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partycja1 | CA |
| 3 | Christine | Partycja1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Pobieranie jednostek dla określonej wartości w określonej kolumnie

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

To zapytanie pobiera jeden rekord.

|Pole|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | Partycja1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Pobieranie jednostki przy użyciu niestandardowego filtru 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

To zapytanie pobiera jeden rekord.

|Pole|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | Partycja1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizowanie jednostek 

Istnieją trzy kroki w celu zaktualizowania jednostki. Najpierw należy pobrać jednostkę którą chcesz zmienić. Po drugie wprowadzić zmiany. Po trzecie, Zatwierdź zmiany, przy użyciu **AzTableRow aktualizacji**.

Aktualizowanie jednostki przy użyciu nazwy użytkownika = "Jessie", aby nazwa użytkownika = "Jessie2". Ten przykład pokazuje także innym sposobem tworzenia niestandardowego filtru przy użyciu typów .NET.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Wyniki wskazują rekord Jessie2.

|Pole|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | Partycja2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Usuwanie jednostek tablic

Można usunąć jednego lub wszystkich jednostek w tabeli.

#### <a name="deleting-one-entity"></a>Usuwanie jednej jednostki

Aby usunąć pojedynczą jednostkę, Pobierz odwołanie do tej jednostki i przekazać go do **AzTableRow Usuń**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Usuń wszystkie jednostki w tabeli

Aby usunąć wszystkie jednostki w tabeli, je pobrać i przekazać wyniki do polecenia cmdlet remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
