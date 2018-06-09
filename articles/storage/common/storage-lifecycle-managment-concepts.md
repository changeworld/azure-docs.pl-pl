---
title: Zarządzanie cyklem życia usługi Azure Storage
description: Jak utworzyć cyklu życia reguły zasad danych againg przejścia z gorącej do warstwy chłodnej i archiwum.
services: storage
author: yzheng-msft
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.openlocfilehash: bd36cfd0cd03592396a2aa9a977124880f47ec90
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248473"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Zarządzanie cyklem życia z magazynu obiektów Blob platformy Azure (wersja zapoznawcza)

Zbiory danych mają unikatowe cyklami życia. Niektóre jest uzyskiwany dostęp do danych często wcześnie w cyklu życia, ale potrzebę dostępu znacząco porzuca rzadziej. Niektóre dane pozostają w stanie bezczynności w chmurze i rzadko dostępne raz przechowywane. Niektóre dane wygaśnie dni lub miesięcy po utworzeniu, podczas gdy inne zestawy danych aktywnie odczytać i zmodyfikować przez cały okres ich istnienia. Zarządzanie cyklem życia Azure Blob Storage (wersja zapoznawcza) oferuje rozbudowane, na podstawie reguł zasad, które można użyć do przejścia dane w warstwie dostępu do najlepszych i wygasić dane na końcu cykl życia.

Zasady zarządzania cyklem życia pomoże Ci:

- Przejście obiekty BLOB do lodówki warstwy magazynu (gorąca Cool gorąca archiwum, lub Cool archiwum) w celu zoptymalizowania wydajności i kosztów
- Usuwanie obiektów blob na końcu ich cyklów
- Definiowanie reguł na poziomie konta magazynu wykonywane raz dziennie (obsługuje ona zarówno GPv2, jak i obiektów Blob kont magazynu)
- Zastosuj reguły do kontenerów lub podzbiór obiektów blob (przy użyciu prefiksy jako filtrów)

Należy wziąć pod uwagę zestawu danych, która jest uzyskiwany dostęp do często podczas wczesnym etapie cyklu życia, wymagane jest tylko od czasu do czasu po dwóch tygodniach i rzadko jest dostępna po miesiącu i nowszych. W tym scenariuszu magazynu gorącego najlepiej wczesnych etapach magazynu chłodnego jest najbardziej odpowiednia w potrzebują dostępu, a Magazyn archiwum jest najlepszą opcją warstwy po rzadziej w miesiącu. Dostosowując warstw magazynowania w odniesieniu do wiek danych, można zaprojektować najniższych opcje magazynu do własnych potrzeb. Aby osiągnąć ten proces przejścia, zasady zarządzania cyklem życia są dostępne do przenoszenia danych przedawnienia do warstw lodówki.

## <a name="storage-account-support"></a>Obsługa konta magazynu

Zasady zarządzania cyklem życia jest dostępny z obu ogólnego przeznaczenia v2 (GPv2) konto oraz konto magazynu obiektów Blob. Istniejące konto ogólnego przeznaczenia (GPv1) można przekonwertować na konto GPv2 za pośrednictwem prosty proces jednym kliknięciem w portalu Azure. Aby dowiedzieć się więcej, zobacz [Opcje kont usługi Azure Storage](../common/storage-account-options.md).  

## <a name="pricing"></a>Cennik 

Funkcja zarządzania cyklem życia jest bezpłatne w wersji zapoznawczej. Klienci są naliczane koszt regularnie [listę obiektów blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) i [ustawić warstwy obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) wywołań interfejsu API. Zobacz [cennik blokowych obiektów Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) Aby dowiedzieć się więcej o cenach.

## <a name="register-for-preview"></a>Zarejestruj w wersji zapoznawczej 
Aby zarejestrować się w publicznej wersji zapoznawczej, należy przesłać żądanie do zarejestrowania tej funkcji do subskrypcji. Po zatwierdzeniu żądania (w ciągu kilku dni), wszelkie istniejące i nowe GPv2 lub magazynu obiektów Blob konto w zachodnie stany USA 2 i zachodnie środkowe stany będzie ma włączenie tej funkcji. Podczas udostępniania wersji zapoznawczej obsługiwane jest tylko blokowych obiektów blob. Podobnie jak w przypadku większości wersji zapoznawczych, ta funkcja nie powinna być używana w przypadku obciążeń produkcyjnych aż osiągnie po

Aby przesłać żądanie, uruchom następujące polecenia programu PowerShell lub interfejsu wiersza polecenia.

### <a name="powershell"></a>PowerShell

Aby przesłać żądanie:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Można sprawdzić stan zatwierdzenia rejestracji przy użyciu następującego polecenia:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Jeśli funkcja jest zatwierdzona i prawidłowo zarejestrowane, powinien zostać wyświetlony stan "Zarejestrowanej".

### <a name="cli-20"></a>Interfejs wiersza polecenia 2.0

Aby przesłać żądanie: 
```cli
az feature register –-namespace Microsoft.Storage –-name DLM
```
Można sprawdzić stan zatwierdzenia rejestracji przy użyciu następującego polecenia:
```cli
-az feature show –-namespace Microsoft.Storage –-name DLM
```
Jeśli funkcja jest zatwierdzona i prawidłowo zarejestrowane, powinien zostać wyświetlony stan "Zarejestrowanej". 


## <a name="add-or-remove-policies"></a>Dodaj lub Usuń zasady 

Można dodać, edytować lub usunąć zasadę przy użyciu portalu Azure, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), interfejsów API REST lub narzędzia klienta w następujących językach: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [ Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby]( https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

3. W bloku ustawienia kliknij **zarządzania cyklem życia** zgrupowane w ramach usługi obiektów Blob, aby wyświetlić i/lub zmiany zasad.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Włączenie reguły zapory dla konta magazynu, może zostać zablokowany żądań zarządzania cyklem życia. Można go odblokować zapewniając wyjątków. Aby uzyskać więcej informacji, zobacz sekcję wyjątków na [skonfigurować zapory i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Zasady

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


W ramach zasad wymagane są dwa parametry:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| wersja        | Wyrażonej w postaci ciągu `x.x` | Numer wersji preview to 0,5 |
| rules          | Tablica obiektów reguły | W każdej zasady jest wymagana co najmniej jedną regułę. Podczas udostępniania wersji zapoznawczej można określić maksymalnie 4 reguł dla poszczególnych zasad. |

Parametry wymagane w regule są:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| Name (Nazwa)           | Ciąg | Nazwa reguły może zawierać dowolną kombinację znaki alfanumeryczne. Nazwa reguły jest rozróżniana wielkość liter. Musi być unikatowa w ramach zasad. |
| type           | Wartość wyliczenia | Jest prawidłową wartością dla wersji zapoznawczej `Lifecycle` |
| definicja     | Obiekt, który definiuje reguły cykl życia | Każda definicja składa się z zestaw filtrów i akcji. |

## <a name="rules"></a>Reguły

Każda definicja reguły obejmuje zestaw filtrów i akcji. Poniższa reguła próbki modyfikuje warstwy podstawowej blokowych obiektów blob z prefiksem `foo`. W zasadach te zasady są zdefiniowane jako:

- Obiekty blob warstwy do cool magazynu 30 dni od daty ostatniej modyfikacji
- Warstwy obiektu blob do magazynu archiwum 90 dni od daty ostatniej modyfikacji
- Usuń obiekt blob 2,555 dni (7 lat) po ostatniej modyfikacji
- Usuń migawki obiektu blob 90 dni, po utworzeniu migawki

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
          "prefixMatch": [ "foo" ]
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

Filtry ograniczyć akcje reguły do podzbioru obiektów blob w ramach konta magazynu. Jeśli zdefiniowano wiele filtrów, logicznych `AND` odbywa się na wszystkie filtry.

Podczas udostępniania wersji zapoznawczej dostępne są następujące filtry prawidłowy:

| Nazwa filtru | Typ filtru | Uwagi | Jest wymagana |
|-------------|-------------|-------|-------------|
| blobTypes   | Tablica wartości wyliczenia wstępnie zdefiniowane. | W wersji zapoznawczej, tylko `blockBlob` jest obsługiwana. | Yes |
| prefixMatch | Tablica ciągów dla prefiksów być zgodne. | Jeśli nie jest zdefiniowany, ta reguła ma zastosowanie do wszystkich obiektów blob w ramach konta. | Nie |

### <a name="rule-actions"></a>Akcje reguły

Akcje są stosowane do filtrowanych obiektów blob, po spełnieniu warunku wykonywania.

W wersji zapoznawczej Zarządzanie cyklem życia obsługuje warstwy i usuwania obiektów blob i usuwanie migawki obiektu blob. Każda reguła musi mieć co najmniej jedną akcję zdefiniowane dla obiektów blob lub migawki obiektu blob.

| Akcja        | Podstawowy obiektów Blob                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Obsługuje obiekty BLOB dla warstwy dostępu         | Nieobsługiwane |
| tierToArchive | Obsługuje obiekty BLOB obecnie na aktywny lub chłodnych warstwy | Nieobsługiwane |
| usuwanie        | Obsługiwane                                   | Obsługiwane     |

>[!NOTE] 
Jeśli więcej niż jedna akcja jest zdefiniowany w tym samym obiekcie blob, Zarządzanie cyklem życia dotyczy najniższych akcję obiektu blob. (np. Akcja `delete` jest tańsze niż akcja `tierToArchive`. Akcja `tierToArchive` jest tańsze niż akcja `tierToCool`.)

W wersji zapoznawczej warunki wykonywania akcji są oparte na wieku. Używa podstawowej blob Data ostatniej modyfikacji śledzenia wieku i czas utworzenia migawki używa migawek do śledzenia wieku obiektu blob.

| Warunek wykonywania akcji | Wartość warunku | Opis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Wartość całkowitą, wskazującą wiek w dniach | Nieprawidłowy warunek akcji podstawowego obiektu blob |
| daysAfterCreationGreaterThan     | Wartość całkowitą, wskazującą wiek w dniach | Nieprawidłowy warunek akcji migawki obiektu blob | 

## <a name="examples"></a>Przykłady
Poniższe przykłady pokazują, jak w celu rozwiązania typowych scenariuszy przy użyciu reguł zasad cyklu życia.

### <a name="move-aging-data-to-a-cooler-tier"></a>Przenoszenie danych przedawnienia do lodówki warstwy

W poniższym przykładzie pokazano sposób na przejście blokowych obiektów blob jest poprzedzony prefiksem `foo` lub `bar`. Zasady przejścia obiektów blob, które nie zostały zmodyfikowane w ciągu 30 dni do magazynu chłodnego i obiektów blob nie zostały zmodyfikowane w ciągu 90 dni do warstwy archiwum:

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
            "prefixMatch": [ "foo", "bar" ]
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

### <a name="archive-data-at-ingest"></a>Archiwizowanie danych w pozyskiwanie 

Niektóre dane pozostają nieużywane w chmurze i dostęp do nich uzyskuje się rzadko (lub w ogóle) po umieszczeniu ich w magazynie. Te dane są najlepsze do zarchiwizowania natychmiast, gdy jest on pozyskanych. Skonfigurowano następujące zasady cyklu pomocy archiwum danych pozyskiwania. W tym przykładzie przejścia blokowych obiektów blob na koncie magazynu z prefiksem `archive` bezpośrednio pod warstwą archiwum. Natychmiastowe przejście odbywa się przy działające na obiekty BLOB 0 dni od ostatniej modyfikacji:

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
            "prefixMatch": [ "archive" ]
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

### <a name="expire-data-based-on-age"></a>Wygasić dane według ważności

Niektóre dane są oczekiwane wygaśnie dni lub miesięcy po utworzeniu do zredukowania kosztów lub zachować zgodność z przepisami dla instytucji rządowych. Zasady zarządzania cyklem życia można skonfigurować wygaśnie Data przez usunięcie oparte na wiek danych. W poniższym przykładzie przedstawiono zasady, która usuwa wszystkie blokowe (z nie prefiksu określonego) starsze niż 365 dni.

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

Dla danych modyfikacji i dostępu do regularnie przez cały cykl jej życia migawki są często używane do śledzenia starsze wersje danych. Można utworzyć zasadę, która usuwa stare migawki oparte na wieku migawki. Wiek migawki zależy od obliczenia czas utworzenia migawki. Tę regułę zasad usuwa zablokować migawki obiektu blob z prefiksem `activeData` , które są 90 dni lub starsze po utworzeniu migawki.

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
            "prefixMatch": [ "activeData" ]
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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak i odzyskiwania po przypadkowym usunięciu danych:

- [Soft usuwania obiektów blob magazynu Azure ](../blobs/storage-blob-soft-delete.md)
