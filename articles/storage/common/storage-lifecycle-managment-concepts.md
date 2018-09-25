---
title: Zarządzanie cyklem życia usługi Azure Storage
description: Informacje dotyczące tworzenia cyklu życia reguły zasad danych againg przejścia z gorącej do warstwy chłodna i archiwum.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 25e6fba6ac8aa34c0c30fd61f5fe297b94720439
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983671"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Zarządzanie cyklem życia z magazynu obiektów Blob platformy Azure (wersja zapoznawcza)

Zestawy danych ma unikatowy cykle życia. Niektóre dane są używane często wcześnie w cyklu życia, ale potrzebę dostępu znacząco umieszcza się rzadziej. Niektóre dane pozostają w stanie bezczynności w chmurze i są rzadko używane po umieszczeniu. Niektóre dane wygasają dni lub miesięcy po utworzeniu, podczas gdy inne zestawy danych są aktywnie odczytu i modyfikacji przez cały okres ich istnienia. Zarządzanie cyklem życia w usłudze Azure Blob Storage (wersja zapoznawcza) oferuje zaawansowane, oparte na regułach zasad, który umożliwia przeniesienie danych do najlepszych warstwy dostępu i wygasanie danych na końcu jej cyklu projektowania.

Zasady zarządzania cyklem życia pomoże Ci:

- Obiekty BLOB do chłodniejszej warstwy magazynowania (gorąca do chłodna, gorąca do archiwum lub chłodna do archiwalna) przejście zoptymalizowane pod kątem wydajności i kosztów
- Usuwanie obiektów blob na końcu ich cykle życia
- Zdefiniuj reguły wykonywane raz dziennie na poziomie konta magazynu (obsługuje on kont magazynu GPv2 i Blob)
- Zastosuj reguły do kontenerów lub podzbiór obiektów blob (przy użyciu prefiksy jako filtry)

Należy wziąć pod uwagę zestaw danych, który jest dostępny w punktach wczesnym etapie cyklu życia, wymagane jest tylko od czasu do czasu po dwóch tygodniach i są rzadko używane, po upływie miesiąca i nie tylko. W tym scenariuszu podczas wczesnych etapach, najlepiej jest magazynu gorącego magazynu chłodnego jest najbardziej odpowiednie dla okazjonalne dostępu, a archive storage jest najlepszym rozwiązaniem warstwy po rzadziej ponad miesiąc. Dostosowując warstw magazynowania w odniesieniu do wiek danych, można zaprojektować najniższy opcje magazynu do własnych potrzeb. Aby osiągnąć ten proces przejścia, zasady zarządzania cyklem życia dostępnych do przenoszenia danych przestarzałej do chłodniejszej warstwy.

## <a name="storage-account-support"></a>Obsługa konta magazynu

Zasady zarządzania cyklem życia jest dostępny z obu ogólnego przeznaczenia w wersji 2 (GPv2) konto oraz konto magazynu obiektów Blob. Możesz przekonwertować istniejące konto ogólnego przeznaczenia (GPv1) do wersji GPv2 przy użyciu prostego procesu jednym kliknięciem w witrynie Azure portal. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Przegląd konta usługi Azure storage](../common/storage-account-overview.md) Aby dowiedzieć się więcej.  

## <a name="pricing"></a>Cennik 

Funkcja zarządzania cyklem życia jest bezpłatne w wersji zapoznawczej. Klienci są obciążani koszt normalnej [wyświetlanie listy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) i [Ustawianie warstwy obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) wywołań interfejsu API. Zobacz [ceny blokowych obiektów Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) Aby dowiedzieć się więcej o cenach.

## <a name="register-for-preview"></a>Rejestrowanie na potrzeby wersji zapoznawczej 
Aby zarejestrować się w publicznej wersji zapoznawczej, należy przesłać żądanie, aby zarejestrować tę funkcję, aby Twoja subskrypcja. Po zatwierdzeniu żądania (w ciągu kilku dni), wszelkie istniejące i nowe konta GPv2 lub Blob Storage konta w regionie zachodnie stany USA 2, zachodnio-środkowe stany USA i Europa Zachodnia mają włączoną funkcję. W trakcie okresu zapoznawczego jest obsługiwana tylko blokowych obiektów blob. Podobnie jak w przypadku większości wersji zapoznawczych, ta funkcja nie należy używać w przypadku obciążeń produkcyjnych aż do napotkania GA.

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
Jeśli funkcja jest zatwierdzona poprawnie zarejestrowane, powinny pojawić się w stanie "Zarejestrowane".

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przesłać żądanie: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Możesz sprawdzić stan zatwierdzenia rejestracji za pomocą następującego polecenia:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Jeśli funkcja jest zatwierdzona poprawnie zarejestrowane, powinny pojawić się w stanie "Zarejestrowane". 


## <a name="add-or-remove-policies"></a>Dodawanie lub usuwanie zasad 

Możesz dodawać, edytować lub usunąć zasadę przy użyciu witryny Azure portal, [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), [interfejsów API REST](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/createorupdatemanagementpolicies), lub narzędzia klienta w następujących językach: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [językaPython](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

3. W bloku ustawienia kliknij **zarządzania cyklem życia** zgrupowane w obszarze usługi obiektów Blob, aby wyświetlić lub zmienić zasady.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Włączenie reguły zapory dla konta magazynu, mogą być zablokowane żądania zarządzania cyklem życia. Może je odblokować, zapewniając wyjątków. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w [skonfigurować zapory i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

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
| wersja        | Wyrażonej w postaci ciągu `x.x` | Numer wersji (wersja zapoznawcza) wynosi 0,5 |
| rules          | Tablica obiektów reguły | Co najmniej jedna reguła jest wymagana w każdej z zasad. W trakcie okresu zapoznawczego można określić maksymalnie 4 reguły, zgodnie z zasadami. |

Parametry wymagane w ramach reguły są następujące:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| Name (Nazwa)           | Ciąg | Nazwa reguły może zawierać dowolną kombinację znaki alfanumeryczne. Nazwa reguły jest rozróżniana wielkość liter. Musi być unikatowa w ramach zasad. |
| type           | Wartość wyliczenia | Nieprawidłowa wartość (wersja zapoznawcza) `Lifecycle` |
| definicja     | Obiekt, który definiuje reguły cyklu życia | Każda definicja składa się z zestawem filtru i zestawem akcji. |

## <a name="rules"></a>Reguły

Każda definicja reguły obejmuje zestaw filtrów i zestawu zadań. Następująca reguła przykład modyfikuje warstwy podstawowa blokowych obiektów blob z prefiksem `container1/foo`. W zasadach te reguły są zdefiniowane jako:

- Warstwa obiektu blob, do chłodnego magazynu 30 dni od ostatniej modyfikacji
- Warstwa obiektu blob do usługi Archive storage 90 dni od ostatniej modyfikacji
- Usuwanie obiektu blob 2,555 dni (7 lat) po ostatniej modyfikacji
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

Filtry ograniczają akcji reguł do podzbioru obiektów blob na koncie magazynu. Jeśli zdefiniowano wielu filtrów, wartość logiczna `AND` odbywa się na wszystkie filtry.

W trakcie okresu zapoznawczego dostępne są następujące filtry prawidłowy:

| Nazwa filtru | Typ filtru | Uwagi | Jest wymagany |
|-------------|-------------|-------|-------------|
| blobTypes   | Tablica wartości wyliczenia wstępnie zdefiniowane. | W przypadku wersji zapoznawczej tylko `blockBlob` jest obsługiwana. | Yes |
| prefixMatch | Tablica ciągów dla prefiksów dopasować się. Ciąg prefiksu musi rozpoczynać się od nazwy kontenera. Na przykład, jeśli wszystkie obiekty BLOB w obszarze "https://myaccount.blob.core.windows.net/mycontainer/mydir/..." powinny być dopasowane reguły, prefiks jest "mycontainer/mydir". | Jeśli nie jest zdefiniowana, to reguła ma zastosowanie do wszystkich obiektów blob w ramach konta. | Nie |

### <a name="rule-actions"></a>Akcje reguły

Akcje są stosowane do filtrowanych obiektów blob, po spełnieniu warunku wykonywania.

W wersji zapoznawczej zarządzania cyklem życia obsługuje warstw i usuwanie obiektów blob i usuwanie migawek obiektów blob. Każda reguła musi mieć co najmniej jedną akcję zdefiniowany dla obiektów blob lub migawki obiektów blob.

| Akcja        | Podstawowy obiekt Blob                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Obsługuje obiekty BLOB, obecnie w warstwie gorąca         | Nieobsługiwane |
| tierToArchive | Obsługuje obiekty BLOB obecnie na gorąca lub chłodna warstwa | Nieobsługiwane |
| delete        | Obsługiwane                                   | Obsługiwane     |

>[!NOTE] 
Jeśli więcej niż jedna akcja jest zdefiniowany dla tego samego obiektu blob, Zarządzanie cyklem życia dotyczy najniższy akcję obiektu blob. (np. Akcja `delete` jest tańsze niż akcja `tierToArchive`. Akcja `tierToArchive` jest tańsze niż akcja `tierToCool`.)

W wersji zapoznawczej warunki wykonanie akcji są oparte na okres ważności. Zastosowań podstawowego obiektu blob Data ostatniej modyfikacji śledzenie wiek i obiektów blob czas utworzenia migawki używa migawek śledzić okres ważności.

| Warunek wykonania akcji | Wartość warunku | Opis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Wartość całkowitą, wskazując wiek w dniach | Nieprawidłowy warunek akcji podstawowego obiektu blob |
| daysAfterCreationGreaterThan     | Wartość całkowitą, wskazując wiek w dniach | Nieprawidłowy warunek akcji migawki obiektu blob | 

## <a name="examples"></a>Przykłady
Poniższe przykłady pokazują, jak rozwiązywać typowe scenariusze za pomocą reguły zasad cyklu życia.

### <a name="move-aging-data-to-a-cooler-tier"></a>Przenoszenie danych przestarzałej do chłodniejszej warstwy

Poniższy przykład pokazuje, jak przejście blokowych obiektów blob z prefiksem `container1/foo` lub `container2/bar`. Zasady przejścia obiektów blob, które nie zostały zmodyfikowane w ciągu 30 dni do magazynu chłodnego i obiektów blob, nie zostały zmodyfikowane w ciągu 90 dni do warstwy archiwum:

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

Niektóre dane pozostają nieużywane w chmurze i dostęp do nich uzyskuje się rzadko (lub w ogóle) po umieszczeniu ich w magazynie. Te dane są najlepsze do archiwizacji natychmiast, po ich przetwarzania. Następujące zasady cyklu życia jest skonfigurowany pod kątem archiwizowania danych na odbierania. W tym przykładzie przejścia blokowe obiekty BLOB na koncie magazynu w kontenerze `archivecontainer` natychmiast w warstwie archiwum. Natychmiastowe przejście odbywa się według działających obiektów blob 0 dni po Godzina ostatniej modyfikacji:

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

Niektóre dane powinien wygasają dni lub miesięcy po utworzeniu w celu zredukowania kosztów lub zachowania zgodności z przepisami dla instytucji rządowych. Zasady zarządzania cyklem życia można ustawić wygaśnięcie danych, przez usunięcie oparte na wiek danych. Poniższy przykład pokazuje zasadę, która usuwa wszystkie obiekty BLOB typu block (z żadnego prefiksu, określone) starsze niż 365 dni.

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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak odzyskiwać dane po przypadkowym usunięciem:

- [Nietrwałe usuwanie obiektów blob usługi Azure Storage ](../blobs/storage-blob-soft-delete.md)
