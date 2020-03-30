---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468723"
---
### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
**Usługa połączona usługi Azure Storage** umożliwia łączenie konta magazynu platformy Azure z fabryką danych platformy Azure przy użyciu **klucza konta**, który zapewnia fabryce danych globalny dostęp do usługi Azure Storage. Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej usługi Azure Storage.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **AzureStorage** |Tak |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z magazynem platformy Azure dla właściwości connectionString. |Tak |

Aby uzyskać informacje dotyczące pobierania kluczy dostępu do konta magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../articles/storage/common/storage-account-keys-manage.md).

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

### <a name="azure-storage-sas-linked-service"></a>Usługa połączona usługi Azure Storage Sas
Sygnatura dostępu współdzielonego (SAS, shared access signature) zapewnia delegowany dostęp do zasobów w ramach konta magazynu. Umożliwia przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień, bez konieczności udostępniania kluczy dostępu do konta. Sygnatura dostępu Współdzielonego jest identyfikatorem URI, który obejmuje w swoich parametrach zapytania wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu za pomocą sygnatury dostępu Współdzielonego, klient musi przekazać tylko w sygnaturze dostępu Współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat sygnatury dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../articles/storage/common/storage-sas-overview.md)

> [!IMPORTANT]
> Usługa Azure Data Factory obsługuje teraz tylko **usługę Sygnatury dostępu Współdzielonego,** ale nie sygnatury dostępu Współdzielonego konta. Należy zauważyć, że adres URL sygnatury dostępu Współdzielonego z witryny Azure portal lub Eksploratora usług magazynowych jest sygnaturą dostępu Współdzielonego konta, która nie jest obsługiwana.

> [!TIP]
> Poniżej poleceń programu PowerShell można wykonać, aby wygenerować sygnatury dostępu Współdzielonego usługi dla konta magazynu (zastąpić posiadaczy miejsc i udzielić wymaganych uprawnień):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Usługa połączona usługi Azure Storage SAS umożliwia łączenie konta usługi Azure Storage z fabryką danych platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS). Zapewnia fabryce danych ograniczony/czasowy dostęp do wszystkich/określonych zasobów (blob/container) w magazynie. Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej usługi Azure Storage SAS. 

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **AzureStorageSas** |Tak |
| sasuri ( sasuri ) |Określ identyfikator URI podpisu dostępu współdzielonego w zasobach usługi Azure Storage, takich jak obiekt blob, kontener lub tabela.  |Tak |

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

Podczas tworzenia identyfikatora **URI**sygnatury dostępu Współdzielonego, biorąc pod uwagę następujące kwestie:  

* Ustaw odpowiednie **uprawnienia** do odczytu/zapisu dla obiektów na podstawie sposobu, w jaki usługa połączona (odczyt, zapis, odczyt/zapis) jest używana w fabryce danych.
* Ustaw czas **wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów usługi Azure Storage nie wygaśnie w aktywnym okresie potoku.
* Uri powinny być tworzone na poziomie odpowiedniego kontenera/obiektu blob lub tabeli na podstawie potrzeby. Identyfikator Uri sygnatury dostępu współdzielonego platformy Azure umożliwia usłudze data factory dostęp do tego określonego obiektu blob. Identyfikator Uri sygnatury dostępu Współdzielonego do kontenera obiektów blob platformy Azure umożliwia usługi data factory do iteracji za pośrednictwem obiektów blob w tym kontenerze. Jeśli chcesz później zapewnić dostęp więcej/mniej obiektów lub zaktualizować identyfikator URI sygnatury dostępu współdzielonego, należy pamiętać o zaktualizowaniu połączonej usługi za pomocą nowego identyfikatora URI.   

