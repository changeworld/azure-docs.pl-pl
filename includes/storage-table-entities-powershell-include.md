<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Zarządzanie jednostkami tabeli

Teraz, gdy masz tabelę Przyjrzyjmy się jak zarządzać jednostek lub wierszy w tabeli. 

Jednostka może mieć maksymalnie 255 właściwości, w tym 3 Właściwości systemowe: **PartitionKey**, **RowKey**, i **sygnatury czasowej**. Jest odpowiedzialny za wstawiania i aktualizowania wartości **PartitionKey** i **RowKey**. Serwer zarządza wartością **sygnatury czasowej**, która nie może być modyfikowany. Razem **PartitionKey** i **RowKey** jednoznacznie zidentyfikować każdej jednostki w tabeli.

* **PartitionKey**: Określa jednostki przechowywanego w partycji.
* **RowKey**: unikatowo identyfikuje jednostek w partycji.

Można zdefiniować maksymalnie 252 właściwości niestandardowych dla jednostki. 

### <a name="add-table-entities"></a>Dodaj jednostki tabeli

Dodaj jednostki do tabeli za pomocą **StorageTableRow Dodaj**. Te przykłady użycia kluczy partycji z wartości "Partycja1" i "Partycja2" i klucze wierszy równa skróty stanu. Właściwości w każdej jednostki są nazwy użytkownika i identyfikator użytkownika. 

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

### <a name="query-the-table-entities"></a>Zapytanie jednostek tabeli

Istnieje kilka różnych sposobów, aby zbadać jednostek w tabeli.

#### <a name="retrieve-all-entities"></a>Pobieranie wszystkich jednostek

Aby uzyskać dostęp do wszystkich obiektów, użyj **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

To polecenie zwraca wyniki podobne do poniższej tabeli:

| Nazwa użytkownika | nazwa użytkownika | partycja | rowkey |
|----|---------|---------------|----|
| 1 | Krzysztof | Partycja1 | Urząd certyfikacji |
| 3 | Christine | Partycja1 | WA |
| 2 | Joasia | Partycja2 | NM |
| 4 | Steven | Partycja2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Pobieranie jednostek dla określonej partycji

Aby uzyskać dostęp do wszystkich jednostek w określonej partycji, użyj **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Wyniki wyglądać podobnie do poniższej tabeli:

| Nazwa użytkownika | nazwa użytkownika | partycja | rowkey |
|----|---------|---------------|----|
| 1 | Krzysztof | Partycja1 | Urząd certyfikacji |
| 3 | Christine | Partycja1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Pobieranie jednostek dla określonej wartości w określonej kolumnie

Aby uzyskać dostęp do jednostki, której wartość w określonej kolumnie równa określonej wartości, należy użyć **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

To zapytanie pobiera jeden rekord.

|Pole|wartość|
|----|----|
| Nazwa użytkownika | 1 |
| nazwa użytkownika | Krzysztof |
| PartitionKey | Partycja1 |
| RowKey      | Urząd certyfikacji |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Pobieranie jednostek przy użyciu niestandardowego filtru 

Aby pobrać jednostek przy użyciu niestandardowego filtru, należy użyć **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

To zapytanie pobiera jeden rekord.

|Pole|wartość|
|----|----|
| Nazwa użytkownika | 1 |
| nazwa użytkownika | Krzysztof |
| PartitionKey | Partycja1 |
| RowKey      | Urząd certyfikacji |

### <a name="updating-entities"></a>Aktualizowanie jednostek 

Istnieją trzy kroki dla aktualizowanie jednostek. Najpierw należy pobrać jednostki można zmienić. Po drugie wprowadzić zmiany. Trzecie, Zatwierdź zmiany przy użyciu **AzureStorageTableRow aktualizacji**.

Aktualizowanie jednostki z nazwą użytkownika = Joasia, aby nazwa użytkownika = "Jessie2". W tym przykładzie przedstawiono również inny sposób tworzenia niestandardowego filtru przy użyciu typów .NET. 

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
| Nazwa użytkownika | 2 |
| nazwa użytkownika | Jessie2 |
| PartitionKey | Partycja2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Usuwanie jednostek tabeli

Można usunąć jednego lub wszystkich jednostek w tabeli.

#### <a name="deleting-one-entity"></a>Usuwanie jedną jednostkę

Aby usunąć pojedynczą jednostkę, odwołać się do tej jednostki i przekazać go do **AzureStorageTableRow Usuń**.

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

Aby usunąć wszystkich jednostek w tabeli, pobrać je i przekazać wyników w potoku do polecenia cmdlet remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
