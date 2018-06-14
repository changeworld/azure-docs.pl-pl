### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
**Połączonej usługi magazynu Azure** umożliwia łączenie z kontem magazynu platformy Azure do fabryki danych Azure za pomocą **klucz konta**, co umożliwia fabryka danych z globalnego dostępu do magazynu Azure. Poniższa tabela zawiera opis elementów JSON specyficzne dla połączoną usługą magazynu Azure.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną: **AzureStorage** |Yes |
| Parametry połączenia |Podaj informacje wymagane do połączenia z magazynem platformy Azure dla właściwości connectionString. |Yes |

Zawiera następujący artykuł w celu widoku/kopiowania klucza konta usługi Azure Storage: [wyświetlanie, kopiowanie i regenerate magazynu, klucze dostępu](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

**Przykład:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

### <a name="azure-storage-sas-linked-service"></a>Sas magazynu Azure połączona usługa
Sygnatury dostępu współdzielonego (SAS) umożliwiają dostęp delegowany do zasobów na koncie magazynu. Umożliwia przyznanie klienta ograniczone uprawnienia do obiektów na koncie magazynu w określonym przedziale czasu i z określonym zestawem uprawnień, bez konieczności udostępniania kluczy dostępu konta. Sygnatury dostępu Współdzielonego to identyfikator URI, który obejmuje w jego parametrów zapytania, wszystkie informacje niezbędne do uwierzytelniony dostęp do zasobów magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu Współdzielonego, klient musi tylko Przekaż sygnatury dostępu Współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać szczegółowe informacje na temat sygnatury dostępu Współdzielonego, zobacz [sygnatury dostępu współdzielonego: opis modelu sygnatur dostępu Współdzielonego](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure obsługuje teraz tylko w fabryce danych **sygnatury dostępu Współdzielonego usługi** , ale nie SAS konta. Zobacz [typy z sygnatury dostępu współdzielonego](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) szczegóły dotyczące tych dwóch typów oraz sposobu tworzenia. Zanotuj adres URL SAS generable z portalu Azure lub Eksploratora usługi Storage jest SAS konta, który nie jest obsługiwany.

> [!TIP]
> Możesz wykonać poniżej polecenia programu PowerShell, aby wygenerować sygnaturę dostępu Współdzielonego usługi dla konta magazynu (Zastąp posiadaczy miejsce i przyznaj uprawnienie wymagane):`$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Usługa połączone SAS magazynu Azure umożliwia łączenie konto magazynu Azure do fabryki danych Azure za pomocą udostępnionego podpis dostępu (SAS). Fabryka danych zapewnia ograniczony/czas-powiązane z dostęp do określonego/all zasobów (kontener/obiektów blob) w magazynie. Poniższa tabela zawiera opis specyficzne dla usługi Azure magazynu SAS połączone elementy JSON. 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną: **element AzureStorageSas** |Yes |
| sasUri |Określ udostępniony URI sygnatury dostępu do zasobów usługi Azure Storage, takich jak obiektów blob, kontenera lub tabeli.  |Yes |

**Przykład:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"   
        }  
    }  
}  
```

Podczas tworzenia **identyfikatora URI połączenia SAS**, biorąc pod uwagę następujące:  

* Ustaw odpowiednie odczytu/zapisu **uprawnienia** na obiektach w oparciu używania połączonej usługi (Odczyt, zapis, Odczyt/zapis) w fabryce danych.
* Ustaw **czas wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów usługi Azure Storage nie wygasa w aktywnym okresie potoku.
* Identyfikator URI utworzony na poziomie oparte na potrzeby prawo kontenera/obiektów blob lub tabeli. Identyfikator Uri sygnatury dostępu Współdzielonego do obiektów blob platformy Azure umożliwia usłudze fabryka danych dostęp do tego konkretnego obiektu blob. Identyfikator Uri sygnatury dostępu Współdzielonego do kontenera obiektów blob platformy Azure umożliwia usłudze fabryka danych iterację obiektów blob w tym kontenerze. Jeśli trzeba udostępnić więcej/mniej obiektów później lub zaktualizować identyfikatora URI połączenia SAS, pamiętaj, aby zaktualizować połączonej usługi o nowy identyfikator URI.   

