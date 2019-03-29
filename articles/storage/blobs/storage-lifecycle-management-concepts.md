---
title: Zarządzanie cyklem życia usługi Azure Storage
description: Informacje dotyczące tworzenia cyklu życia reguły zasad danych przestarzałej przejścia z gorąca na chłodna lub archiwalna.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: df38fd30c1bfba4993e9992783a130262a703370
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579516"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Zarządzanie usługą Azure Blob storage w cyklu życia

Zestawy danych ma unikatowy cykle życia. Na wczesnym etapie cyklu życia ludzie często dostęp do niektórych danych. Ale potrzebę dostępu spada znacząco rzadziej. Niektóre dane pozostaje w stanie bezczynności w chmurze i są rzadko używane po umieszczeniu. Niektóre dane wygasa dni lub miesięcy po utworzeniu, podczas gdy inne zestawy danych są aktywnie odczytu i modyfikacji przez cały okres ich istnienia. Zarządzanie cyklem życia magazynu w usłudze Azure Blob oferuje zaawansowane, oparte na regułach zasad dla kont usługi GPv2 i Blob storage. Użyj zasad przeniesienie danych do warstw odpowiedni dostęp, lub wygaśnie z końcem cykl życia danych.

Zasady zarządzania cyklem życia zapewnia następujące możliwości:

- Obiekty BLOB do chłodniejszej warstwy magazynowania (gorąca do chłodna, gorąca do archiwum lub chłodna, archiwum) przejście zoptymalizowane pod kątem wydajności i kosztów
- Usuwanie obiektów blob na końcu ich cykle życia
- Definiowanie reguł do uruchamiania raz dziennie na poziomie konta magazynu
- Zastosuj reguły do kontenerów lub podzbiór obiektów blob (przy użyciu prefiksy jako filtry)

Rozważmy scenariusz, w którym zestaw danych pobiera częstego dostępu wczesnych etapach cyklu życia, ale następnie tylko od czasu do czasu po dwóch tygodniach. Po pierwszym miesiącu zestawu danych są rzadko używane. W tym scenariuszu magazynu gorącego najlepiej na wczesnych etapach. Magazyn Cool jest najbardziej odpowiednie dla okazjonalne dostępu i archive storage jest najlepszym rozwiązaniem warstwy po rzadziej ponad miesiąc. Dostosowując warstw magazynowania w odniesieniu do wiek danych, można zaprojektować najniższy opcje magazynu do własnych potrzeb. Aby osiągnąć ten proces przejścia, reguły zasad zarządzania cyklem życia dostępnych do przenoszenia danych przestarzałej do chłodniejszej warstwy.

## <a name="storage-account-support"></a>Obsługa konta magazynu

Zasady zarządzania cyklem życia jest dostępny z obu ogólnego przeznaczenia w wersji 2 (GPv2) konta i kont usługi Blob storage. W witrynie Azure portal możesz uaktualnić istniejące konto ogólnego przeznaczenia (GPv1) do wersji GPv2 przy użyciu prostego procesu jednym kliknięciem. Aby uzyskać więcej informacji dotyczących kont magazynu, zobacz temat [Azure Storage account overview](../common/storage-account-overview.md) (Omówienie konta usługi Azure Storage).  

## <a name="pricing"></a>Cennik 

Funkcja zarządzania cyklem życia jest bezpłatne. Klienci są obciążani koszt normalnej [wyświetlanie listy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) i [Ustawianie warstwy obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) wywołań interfejsu API. Operacja usuwania jest bezpłatne. Aby uzyskać więcej informacji o cenach, zobacz [ceny blokowych obiektów Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Dostępność regionalna 
Funkcja zarządzania cyklem życia jest dostępna we wszystkich publicznych regionach platformy Azure. 


## <a name="add-or-remove-a-policy"></a>Dodawanie lub usuwanie zasad 

Dodawanie, edytowanie lub usuwanie zasady za pomocą witryny Azure portal [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases), wiersza polecenia platformy Azure, interfejsów API REST lub narzędzia klienta. W tym artykule przedstawiono sposób zarządzania zasady za pomocą portalu i metod programu PowerShell.  

> [!NOTE]
> Włączenie reguły zapory dla konta magazynu, mogą być zablokowane żądania zarządzania cyklem życia. Zapewniając wyjątków, mogą odblokować te żądania. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w [skonfigurować zapory i sieci wirtualne](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **wszystkie zasoby** i następnie wybierz swoje konto magazynu.

3. W obszarze **usługę Blob Service**, wybierz opcję **zarządzania cyklem życia** do wyświetlania lub zmieniania swoich zasad.

### <a name="powershell"></a>PowerShell

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery 

#Create a new action object

$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd 

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy 
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1

```


## <a name="policy"></a>Zasady

Zasady zarządzania cyklem życia to zbiór reguł w dokumencie JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
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


Zasady to zbiór reguł:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| rules          | Tablica obiektów reguły | Co najmniej jedna reguła jest wymagana w zasadach. W zasadach, można zdefiniować reguły do 100.|

Każda reguła w ramach zasad ma kilka parametrów:

| Nazwa parametru | Typ parametru | Uwagi | Wymagane |
|----------------|----------------|-------|----------|
| name           | String |Nazwa reguły może zawierać maksymalnie 256 znaków alfanumerycznych. Nazwa reguły jest rozróżniana wielkość liter.  Musi być unikatowa w ramach zasad. | True |
| enabled | Wartość logiczna | Opcjonalna wartość logiczna, aby umożliwić reguły do zastosowania tymczasowego wyłączone. Wartość domyślna to true, jeśli nie jest ustawiona. | False | 
| type           | Wartość wyliczenia | Bieżącym typem prawidłowe jest `Lifecycle`. | True |
| definicja     | Obiekt, który definiuje reguły cyklu życia | Każda definicja składa się z zestawem filtru i zestawem akcji. | True |

## <a name="rules"></a>Reguły

Każda definicja reguły obejmuje zestaw filtrów i zestawu zadań. [Filtrowania zestawu](#rule-filters) ogranicza akcji reguł do zestawu obiektów w kontenerze lub nazw obiektów. [Zestaw akcji](#rule-actions) stosuje warstwy lub usuwanie akcji na zestawie filtrowanych obiektów.

### <a name="sample-rule"></a>Przykładowa reguła
Następująca reguła Przykładowe filtry konto do uruchamiania działania na obiektach, które istnieją w `container1` **i** rozpoczynać `foo`.  

- Blob z warstwy chłodna warstwa 30 dni od ostatniej modyfikacji
- Warstwa obiektu blob, do archiwizacji warstwy 90 dni od ostatniej modyfikacji
- Usuwanie obiektu blob 2,555 dni (siedem lat) po ostatniej modyfikacji
- Usuwanie migawek obiektów blob 90 dni po utworzeniu migawki

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
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

Prawidłowymi filtrami obejmują:

| Nazwa filtru | Typ filtru | Uwagi | Wymagany |
|-------------|-------------|-------|-------------|
| blobTypes   | Tablica wartości wyliczenia wstępnie zdefiniowane. | Bieżąca wersja obsługuje `blockBlob`. | Yes |
| prefixMatch | Tablica ciągów dla prefiksów dopasować się. Każda reguła można zdefiniować maksymalnie 10 prefiksy. Ciąg prefiksu musi rozpoczynać się od nazwy kontenera. Na przykład, jeśli chcesz dopasować wszystkich obiektów blob w obszarze "https://myaccount.blob.core.windows.net/container1/foo/..." dla reguły jest prefixMatch `container1/foo`. | Jeśli nie zdefiniowano prefixMatch, reguła ma zastosowanie do wszystkich obiektów blob na koncie magazynu.  | Nie |

### <a name="rule-actions"></a>Akcje reguły

Akcje są stosowane do filtrowanych obiektów blob, po spełnieniu warunku wykonywania.

Zarządzanie cyklem życia obsługuje warstw i usuwanie obiektów blob i usuwanie migawek obiektów blob. Dla obiektów blob lub migawki obiektów blob, należy zdefiniować co najmniej jedną akcję dla każdej reguły.

| Akcja        | Podstawowy obiekt Blob                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Obsługuje obiekty BLOB, obecnie w warstwie gorąca         | Nieobsługiwane |
| tierToArchive | Obsługuje obiekty BLOB, obecnie w warstwie gorącej lub chłodnej | Nieobsługiwane |
| delete        | Obsługiwane                                   | Obsługiwane     |

>[!NOTE] 
>Jeśli więcej niż jedna akcja jest zdefiniowana w tym samym obiekcie blob, Zarządzanie cyklem życia dotyczy najniższy akcję obiektu blob. Na przykład akcja `delete` jest tańsze niż akcja `tierToArchive`. Akcja `tierToArchive` jest tańsze niż akcja `tierToCool`.

Warunki uruchomienia zależą od wieku. Podstawowy obiektów blob umożliwia śledzenie wiek godzinę ostatniej modyfikacji oraz obiektów blob migawki użycia czas utworzenia migawki śledzić okres ważności.

| Uruchom warunku akcji | Wartość warunku | Opis |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Wartość całkowitą, wskazując wiek w dniach | Nieprawidłowy warunek akcji podstawowego obiektu blob |
| daysAfterCreationGreaterThan     | Wartość całkowitą, wskazując wiek w dniach | Nieprawidłowy warunek akcji migawki obiektu blob | 

## <a name="examples"></a>Przykłady
Poniższe przykłady pokazują, jak rozwiązywać typowe scenariusze za pomocą reguły zasad cyklu życia.

### <a name="move-aging-data-to-a-cooler-tier"></a>Przenoszenie danych przestarzałej do chłodniejszej warstwy

W tym przykładzie pokazano, jak przejście blokowych obiektów blob z prefiksem `container1/foo` lub `container2/bar`. Zasady przejścia obiektów blob, które nie zostały zmodyfikowane w ciągu 30 dni do magazynu chłodnego i obiektów blob, nie zostały zmodyfikowane w ciągu 90 dni do warstwy archiwum:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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

Niektóre dane pozostaje w stanie bezczynności w chmurze i uzyskuje się rzadko, jeśli, uzyskuje się dostęp po przechowywane. Następujące zasady cyklu życia jest skonfigurowany pod kątem archiwizowania danych po ich przetwarzania. W tym przykładzie przejścia blokowe obiekty BLOB na koncie magazynu w kontenerze `archivecontainer` w warstwie archiwum. Przejście odbywa się według działających obiektów blob 0 dni po Godzina ostatniej modyfikacji:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
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
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",      
    "definition": {
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

Platforma działa zasady cyklu życia raz dziennie. Po skonfigurowaniu zasad, może upłynąć do 24 godzin w przypadku niektórych działań (np. warstw i usuwanie) do uruchomienia po raz pierwszy.  

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak odzyskiwać dane po przypadkowym usunięciem:

- [Nietrwałe usuwanie obiektów blob usługi Azure Storage](../blobs/storage-blob-soft-delete.md)
