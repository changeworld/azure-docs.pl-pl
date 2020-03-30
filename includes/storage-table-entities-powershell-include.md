---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182906"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Zarządzanie jednostkami tabeli

Teraz, gdy masz tabelę, przyjrzyjmy się, jak zarządzać encjami lub wierszami w tabeli. 

Jednostki mogą mieć maksymalnie 255 właściwości, w tym trzy właściwości systemu: **PartitionKey**, **RowKey**i **Timestamp**. Jesteś odpowiedzialny za wstawianie i aktualizowanie wartości **PartitionKey** i **RowKey**. Serwer zarządza wartością **sygnatury czasowej,** której nie można zmodyfikować. Razem **PartitionKey** i **RowKey** jednoznacznie zidentyfikować każdą jednostkę w tabeli.

* **PartitionKey**: Określa partycję, w której jest przechowywana jednostka.
* **RowKey**: Jednoznacznie identyfikuje jednostkę w ramach partycji.

Można zdefiniować maksymalnie 252 właściwości niestandardowych dla jednostki. 

### <a name="add-table-entities"></a>Dodawanie encji tabeli

Dodawanie encji do tabeli przy użyciu **funkcji Add-AzTableRow**. W tych przykładach użyto kluczy partycji z wartościami `partition1` i `partition2`klawiszami wierszy równymi skrótom stanu. Właściwości w każdej jednostce są `username` i `userid`. 

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

### <a name="query-the-table-entities"></a>Kwerenda jednostek tabeli

Można wysyłać kwerendy do jednostek w tabeli za pomocą polecenia **Get-AzTableRow.**

> [!NOTE]
> Polecenia cmdlet **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**i **Get-AzureStorageTableRowByCustomFilter** są przestarzałe i zostaną usunięte w przyszłej aktualizacji wersji.

#### <a name="retrieve-all-entities"></a>Pobieranie wszystkich encji

```powershell
Get-AzTableRow -table $cloudTable | ft
```

To polecenie daje wyniki podobne do poniższej tabeli:

| userid | nazwa użytkownika | partition | klucz wiersza |
|----|---------|---------------|----|
| 1 | Chris | partycja1 | CA |
| 3 | Christine | partycja1 | WA |
| 2 | Jessie | partycja2 | NM |
| 4 | Steven | partycja2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Pobieranie jednostek dla określonej partycji

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Wyniki wyglądają podobnie do poniższej tabeli:

| userid | nazwa użytkownika | partition | klucz wiersza |
|----|---------|---------------|----|
| 1 | Chris | partycja1 | CA |
| 3 | Christine | partycja1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Pobieranie encji dla określonej wartości w określonej kolumnie

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Ta kwerenda pobiera jeden rekord.

|pole|value|
|----|----|
| userid | 1 |
| nazwa użytkownika | Chris |
| PartitionKey | partycja1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Pobieranie jednostek przy użyciu filtru niestandardowego 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Ta kwerenda pobiera jeden rekord.

|pole|value|
|----|----|
| userid | 1 |
| nazwa użytkownika | Chris |
| PartitionKey | partycja1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizowanie encji 

Istnieją trzy kroki aktualizowania jednostek. Najpierw pobierz jednostkę, aby zmienić. Po drugie, dokonaj zmiany. Po trzecie, zatwierdź zmianę za pomocą **Update-AzTableRow**.

Zaktualizuj encję o nazwę użytkownika = 'Jessie', aby mieć nazwę użytkownika = 'Jessie2'. W tym przykładzie pokazano również inny sposób tworzenia filtru niestandardowego przy użyciu typów .NET.

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

Wyniki pokazują rekord Jessie2.

|pole|value|
|----|----|
| userid | 2 |
| nazwa użytkownika | Okręg wyborczy Jessie2 |
| PartitionKey | partycja2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Usuwanie encji tabeli

Można usunąć jedną encję lub wszystkie encje w tabeli.

#### <a name="deleting-one-entity"></a>Usuwanie jednej encji

Aby usunąć pojedynczą jednostkę, uzyskaj odwołanie do tej encji i przesuń ją do **pliku Remove-AzTableRow**.

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

#### <a name="delete-all-entities-in-the-table"></a>Usuwanie wszystkich encji w tabeli

Aby usunąć wszystkie jednostki w tabeli, należy je pobrać i włożyć wyniki do polecenia cmdlet usuwania. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
