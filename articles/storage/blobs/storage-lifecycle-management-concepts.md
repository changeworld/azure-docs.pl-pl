---
title: Zarządzanie cyklem życia usługi Azure Storage
description: Informacje dotyczące tworzenia cyklu życia reguły zasad danych przestarzałej przejścia z gorąca na chłodna lub archiwalna.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 4dff63a20f9ae3372e37cbd413dd3ec6187ea2cc
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310266"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Zarządzanie usługą Azure Blob storage w cyklu życia (wersja zapoznawcza)

Zestawy danych ma unikatowy cykle życia. Na wczesnym etapie cyklu życia ludzie często dostęp do niektórych danych. Ale potrzebę dostępu spada znacząco rzadziej. Niektóre dane pozostaje w stanie bezczynności w chmurze i są rzadko używane po umieszczeniu. Niektóre dane wygasa dni lub miesięcy po utworzeniu, podczas gdy inne zestawy danych są aktywnie odczytu i modyfikacji przez cały okres ich istnienia. Cyklem życiowym usługi Azure storage Blob (wersja zapoznawcza) oferuje zaawansowane, oparte na regułach zasad dla kont usługi GPv2 i Blob storage. Użyj zasad przeniesienie danych do warstw odpowiedni dostęp, lub wygaśnie z końcem cykl życia danych.

Zasady zarządzania cyklem życia zapewnia następujące możliwości:

- Obiekty BLOB do chłodniejszej warstwy magazynowania (gorąca do chłodna, gorąca do archiwum lub chłodna, archiwum) przejście zoptymalizowane pod kątem wydajności i kosztów
- Usuwanie obiektów blob na końcu ich cykle życia
- Definiowanie reguł do uruchamiania raz dziennie na poziomie konta magazynu
- Zastosuj reguły do kontenerów lub podzbiór obiektów blob (przy użyciu prefiksy jako filtry)

Rozważmy scenariusz, w którym zestaw danych pobiera częstego dostępu wczesnych etapach cyklu życia, ale następnie tylko od czasu do czasu po dwóch tygodniach. Po pierwszym miesiącu zestawu danych są rzadko używane. W tym scenariuszu magazynu gorącego najlepiej na wczesnych etapach. Magazyn Cool jest najbardziej odpowiednie dla okazjonalne dostępu i archive storage jest najlepszym rozwiązaniem warstwy po rzadziej ponad miesiąc. Dostosowując warstw magazynowania w odniesieniu do wiek danych, można zaprojektować najniższy opcje magazynu do własnych potrzeb. Aby osiągnąć ten proces przejścia, reguły zasad zarządzania cyklem życia dostępnych do przenoszenia danych przestarzałej do chłodniejszej warstwy.

## <a name="storage-account-support"></a>Obsługa konta magazynu

Zasady zarządzania cyklem życia jest dostępny z obu ogólnego przeznaczenia w wersji 2 (GPv2) konta i kont usługi Blob storage. W witrynie Azure portal możesz uaktualnić istniejące konto ogólnego przeznaczenia (GPv1) do wersji GPv2 przy użyciu prostego procesu jednym kliknięciem. Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview](../common/storage-account-overview.md) (Omówienie konta usługi Azure Storage).  

## <a name="pricing"></a>Cennik 

Funkcja zarządzania cyklem życia jest bezpłatne w wersji zapoznawczej. Klienci są obciążani koszt normalnej [wyświetlanie listy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) i [Ustawianie warstwy obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) wywołań interfejsu API. Aby uzyskać więcej informacji o cenach, zobacz [ceny blokowych obiektów Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Rejestrowanie na potrzeby wersji zapoznawczej 
Aby zarejestrować się w publicznej wersji zapoznawczej, musisz przesłać żądanie, aby zarejestrować tę funkcję, aby Twoja subskrypcja. Żądania odbywa się zwykle w ciągu dwóch tygodni. Po zatwierdzeniu funkcji dołączania wszystkie istniejące i nowe konta GPv2 lub Blob konta magazynu w następujących regionach: Zachodnie stany USA 2, zachodnie środkowe stany USA, wschodnie stany USA 2 i Europa Zachodnia. Wersja zapoznawcza obsługuje tylko blokowe obiekty blob. Podobnie jak w przypadku większości wersji Preview, nie należy używać tej funkcji dla obciążeń produkcyjnych aż do napotkania GA.

Aby przesłać żądanie, uruchom następujące polecenia programu PowerShell lub interfejsu wiersza polecenia.

### <a name="powershell"></a>PowerShell

Aby przesłać żądanie:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Możesz sprawdzić stan zatwierdzenia rejestracji za pomocą następującego polecenia:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Zatwierdzenia i odpowiednie rejestracji, pojawi się *zarejestrowanej* stanu, gdy prześlesz poprzednich żądań.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przesłać żądanie: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Możesz sprawdzić stan zatwierdzenia rejestracji za pomocą następującego polecenia:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Zatwierdzenia i odpowiednie rejestracji, pojawi się *zarejestrowanej* stanu, gdy prześlesz poprzednich żądań.


## <a name="add-or-remove-a-policy"></a>Dodawanie lub usuwanie zasad 

Możesz dodawać, edytować lub usunąć zasadę przy użyciu witryny Azure portal, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), [interfejsów API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate), lub narzędzia klienta w następujących językach: [platformy .NET ](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **wszystkie zasoby** i następnie wybierz swoje konto magazynu.

3. Wybierz **zarządzania cyklem życia (wersja zapoznawcza)** zgrupowane w obszarze usługi obiektów Blob, aby wyświetlić lub zmienić zasady.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
Włączenie reguły zapory dla konta magazynu, mogą być zablokowane żądania zarządzania cyklem życia. Zapewniając wyjątków, mogą odblokować te żądania. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w [skonfigurować zapory i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Zasady

Zasady zarządzania cyklem życia to zbiór reguł w dokumencie JSON:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


Zasady wymaga dwóch parametrów:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| version        | Wyrażonej w postaci ciągu `x.x` | Numer wersji (wersja zapoznawcza) to 0,5. |
| rules          | Tablica obiektów reguły | Co najmniej jedna reguła jest wymagana w każdej z zasad. W trakcie okresu zapoznawczego można określić maksymalnie 4 reguły, zgodnie z zasadami. |

Każda reguła w ramach zasad wymaga trzech parametrów:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| Name (Nazwa)           | Ciąg | Nazwa reguły może zawierać dowolną kombinację znaków alfanumerycznych. Nazwa reguły jest rozróżniana wielkość liter. Musi być unikatowa w ramach zasad. |
| type           | Wartość wyliczenia | Nieprawidłowa wartość (wersja zapoznawcza) jest `Lifecycle`. |
| definicja     | Obiekt, który definiuje reguły cyklu życia | Każda definicja składa się z zestawem filtru i zestawem akcji. |

## <a name="rules"></a>Reguły

Każda definicja reguły obejmuje zestaw filtrów i zestawu zadań. [Filtrowania zestawu](#rule-filters) ogranicza akcji reguł do zestawu obiektów w kontenerze lub nazw obiektów. [Zestaw akcji](#rule-actions) stosuje warstwy lub usuwanie akcji na zestawie filtrowanych obiektów.

### <a name="sample-rule"></a>Przykładowa reguła
Następująca reguła Przykładowe filtry konta, aby uruchomić tylko na akcje `container1/foo`. Uruchom następujące akcje dla wszystkich obiektów, które istnieją w `container1` **i** rozpoczyna się od `foo`: 

- Blob z warstwy chłodna warstwa 30 dni od ostatniej modyfikacji
- Warstwa obiektu blob, do archiwizacji warstwy 90 dni od ostatniej modyfikacji
- Usuwanie obiektu blob 2,555 dni (siedem lat) po ostatniej modyfikacji
- Usuwanie migawek obiektów blob 90 dni po utworzeniu migawki

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
      "type": "Lifecycle", 
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>Reguły filtrów

Filtry ograniczają akcji reguł do podzbioru obiektów blob na koncie magazynu. Jeśli nie zdefiniowano więcej niż jeden filtr, wartość logiczna `AND` działa na wszystkich filtrów.

W trakcie okresu zapoznawczego dostępne są następujące filtry prawidłowy:

| Nazwa filtru | Typ filtru | Uwagi | Jest wymagany |
|-------------|-------------|-------|-------------|
| blobTypes   | Tablica wartości wyliczenia wstępnie zdefiniowane. | Wersja zapoznawcza obsługuje tylko `blockBlob`. | Yes |
| prefixMatch | Tablica ciągów dla prefiksów dopasować się. Ciąg prefiksu musi rozpoczynać się od nazwy kontenera. Na przykład, jeśli chcesz dopasować wszystkich obiektów blob w obszarze "https://myaccount.blob.core.windows.net/container1/foo/..." dla reguły jest prefixMatch `container1/foo`. | Jeśli nie zdefiniowano prefixMatch, reguły mają zastosowanie do wszystkich obiektów blob w ramach konta. | Nie |

### <a name="rule-actions"></a>Akcje reguły

Akcje są stosowane do filtrowanych obiektów blob, po spełnieniu warunku wykonywania.

W wersji zapoznawczej zarządzania cyklem życia obsługuje warstw i usuwanie obiektów blob i usuwanie migawek obiektów blob. Dla obiektów blob lub migawki obiektów blob, należy zdefiniować co najmniej jedną akcję dla każdej reguły.

| Akcja        | Podstawowy obiekt Blob                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Obsługuje obiekty BLOB, obecnie w warstwie gorąca         | Nieobsługiwane |
| tierToArchive | Obsługuje obiekty BLOB, obecnie w warstwie gorącej lub chłodnej | Nieobsługiwane |
| delete        | Obsługiwane                                   | Obsługiwane     |

>[!NOTE] 
Jeśli więcej niż jedna akcja jest zdefiniowana w tym samym obiekcie blob, Zarządzanie cyklem życia dotyczy najniższy akcję obiektu blob. Na przykład akcja `delete` jest tańsze niż akcja `tierToArchive`. Akcja `tierToArchive` jest tańsze niż akcja `tierToCool`.

W wersji zapoznawczej warunki wykonanie akcji są oparte na okres ważności. Podstawowy obiektów blob umożliwia śledzenie wiek godzinę ostatniej modyfikacji oraz obiektów blob migawki użycia czas utworzenia migawki śledzić okres ważności.

| Warunek wykonania akcji | Wartość warunku | Opis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Wartość całkowitą, wskazując wiek w dniach | Nieprawidłowy warunek akcji podstawowego obiektu blob |
| daysAfterCreationGreaterThan     | Wartość całkowitą, wskazując wiek w dniach | Nieprawidłowy warunek akcji migawki obiektu blob | 

## <a name="examples"></a>Przykłady
Poniższe przykłady pokazują, jak rozwiązywać typowe scenariusze za pomocą reguły zasad cyklu życia.

### <a name="move-aging-data-to-a-cooler-tier"></a>Przenoszenie danych przestarzałej do chłodniejszej warstwy

W tym przykładzie pokazano, jak przejście blokowych obiektów blob z prefiksem `container1/foo` lub `container2/bar`. Zasady przejścia obiektów blob, które nie zostały zmodyfikowane w ciągu 30 dni do magazynu chłodnego i obiektów blob, nie zostały zmodyfikowane w ciągu 90 dni do warstwy archiwum:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "container1/foo", "container2/bar" ]
          },
          "actions": {
            "baseBlob": {
              "tierToCool": { "daysAfterModificationGreaterThan": 30 },
              "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
            }
          }
        }      
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Pozyskiwanie danych archiwum 

Niektóre dane pozostaje w stanie bezczynności w chmurze i uzyskuje się rzadko, jeśli, uzyskuje się dostęp po przechowywane. Archiwizowanie danych, natychmiast po ich przetwarzania. Następujące zasady cyklu życia jest skonfigurowany pod kątem archiwizowania danych na odbierania. W tym przykładzie przejścia blokowe obiekty BLOB na koncie magazynu w kontenerze `archivecontainer` natychmiast w warstwie archiwum. Natychmiastowe przejście odbywa się według działających obiektów blob 0 dni po Godzina ostatniej modyfikacji:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "archivecontainer" ]
          },
          "actions": {
            "baseBlob": { 
                "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
            }
          }
        }      
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Wygasanie danych w oparciu o wieku

Niektóre dane powinien wygasają dni lub miesięcy po utworzeniu, aby zredukować koszty i spełniać wymagania dla instytucji rządowych. Można skonfigurować zasady zarządzania cyklem życia, wygaśnięcie danych przez usunięcie oparte na wiek danych. Poniższy przykład pokazuje zasadę, która usuwa wszystkie obiekty BLOB typu block (z żadnego prefiksu, określone) starsze niż 365 dni.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ]
          },
          "actions": {
            "baseBlob": {
              "delete": { "daysAfterModificationGreaterThan": 365 }
            }
          }
        }      
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Usuń stare migawki

W przypadku danych, który został zmodyfikowany i uzyskać dostęp regularnie w okresie swojego istnienia migawki są często używane do śledzenia starszych danych. Można utworzyć zasadę, która usuwa stare migawki, oparte na wiek migawki. Wiek migawki jest określany przez dokonanie oceny oprogramowania czas utworzenia migawki. Tej reguły zasad, które usuwa block migawek obiektów blob w kontenerze `activedata` 90 dni, które są lub lat po utworzeniu migawki.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
          "filters": {
            "blobTypes": [ "blockBlob" ],
            "prefixMatch": [ "activedata" ]
          },
          "actions": {            
            "snapshot": {
              "delete": { "daysAfterCreationGreaterThan": 90 }
            }
          }
        }      
    }
  ]
}
```
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>Często zadawane pytania — I utworzyć nowe zasady, dlaczego akcje nie są uruchamiane natychmiast? 

Platforma działa zasady cyklu życia raz dziennie. Po ustawieniu nowej zasady może potrwać do 24 godzin w przypadku niektórych działań (np. warstw i usuwanie) do uruchomienia.  

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak odzyskiwać dane po przypadkowym usunięciem:

- [Nietrwałe usuwanie obiektów blob usługi Azure Storage ](../blobs/storage-blob-soft-delete.md)
