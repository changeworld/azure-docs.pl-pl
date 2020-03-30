---
title: Zarządzanie cyklem życia usługi Azure Storage
description: Dowiedz się, jak utworzyć reguły zasad cyklu życia, aby przejść dane wiekowania z warstw gorąca na chłodna i archiwizowana.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598310"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Zarządzanie cyklem życia magazynu usługi Azure Blob Storage

Zestawy danych mają unikatowe cykle życia. Na początku cyklu życia ludzie często uzyskują dostęp do niektórych danych. Ale potrzeba dostępu drastycznie spada wraz ze starzeje się danych. Niektóre dane pozostają bezczynne w chmurze i rzadko są dostępne po zapisaniu. Niektóre dane wygasa dni lub miesięcy po utworzeniu, podczas gdy inne zestawy danych są aktywnie odczytywane i modyfikowane przez cały okres ich istnienia. Zarządzanie cyklem życia magazynu obiektów blob platformy Azure oferuje bogate zasady oparte na regułach dla kont magazynu GPv2 i obiektów blob. Użyj zasad, aby przejść dane do odpowiednich warstw dostępu lub wygaśnie po zakończeniu cyklu życia danych.

Zasady zarządzania cyklem życia umożliwiają:

- Przejścia obiektów blob do warstwy chłodniejszej pamięci masowej (gorąca do chłodzenia, gorąca do archiwum lub chłodna do archiwum) w celu optymalizacji pod kątem wydajności i kosztów
- Usuwanie obiektów blob po zakończeniu cyklu życia
- Definiowanie reguł, które mają być uruchamiane raz dziennie na poziomie konta magazynu
- Stosowanie reguł do kontenerów lub podzbioru obiektów blob (przy użyciu prefiksów jako filtrów)

Należy wziąć pod uwagę scenariusz, w którym dane uzyskuje częsty dostęp na wczesnych etapach cyklu życia, ale tylko od czasu do czasu po dwóch tygodniach. Po pierwszym miesiącu zestaw danych jest rzadko dostępny. W tym scenariuszu gorąca pamięć masowa jest najlepsza we wczesnych stadiach. Chłodna pamięć masowa jest najbardziej odpowiednia do okazjonalnego dostępu. Magazyn archiwum jest najlepszą opcją warstwy po przekątniu danych przez ponad miesiąc. Dostosowując warstwy magazynu w odniesieniu do wieku danych, można zaprojektować najtańsze opcje pamięci masowej dla swoich potrzeb. Aby osiągnąć to przejście, reguły zasad zarządzania cyklem życia są dostępne do przenoszenia danych wiekowania do chłodniejszych warstw.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Obsługa konta magazynu

Zasady zarządzania cyklem życia są dostępne na kontach ogólnego przeznaczenia w wersji 2 (GPv2), kontach magazynu obiektów Blob i kontach magazynu obiektów Blob bloku premium. W witrynie Azure portal można uaktualnić istniejące konto ogólnego przeznaczenia (GPv1) do konta GPv2. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Cennik

Funkcja zarządzania cyklem życia jest bezpłatna. Klienci są obciążani kosztem regularnych operacji dla [listy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) i ustawiania wywołań interfejsu API [warstwy obiektów blob.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) Operacja usuwania jest bezpłatna. Aby uzyskać więcej informacji na temat cen, zobacz [Ceny obiektów Blob bloku .](https://azure.microsoft.com/pricing/details/storage/blobs/)

## <a name="regional-availability"></a>Dostępność regionalna

Funkcja zarządzania cyklem życia jest dostępna we wszystkich regionach platformy Azure.

## <a name="add-or-remove-a-policy"></a>Dodawanie lub usuwanie zasad

Zasady można dodawać, edytować lub usuwać za pomocą dowolnej z następujących metod:

* [Portal Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Interfejsy API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Zasady można przeczytać lub zapisać w całości. Aktualizacje częściowe nie są obsługiwane. 

> [!NOTE]
> Jeśli włączysz reguły zapory dla konta magazynu, żądania zarządzania cyklem życia mogą zostać zablokowane. Można odblokować te żądania, udostępniając wyjątki dla zaufanych usług firmy Microsoft. Aby uzyskać więcej informacji, zobacz sekcję Wyjątki w obszarze [Konfigurowanie zapór i sieci wirtualnych](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

W tym artykule pokazano, jak zarządzać zasadami przy użyciu portalu i metod programu PowerShell.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Istnieją dwa sposoby dodawania zasad za pośrednictwem witryny Azure portal. 

* [Widok listy portalu Azure](#azure-portal-list-view)
* [Widok kodu portalu Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Widok listy portalu Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. W witrynie Azure portal wyszukaj i wybierz swoje konto magazynu. 

3. W obszarze **Usługa obiektów blob**wybierz pozycję **Zarządzanie cyklem życia,** aby wyświetlić lub zmienić reguły.

4. Wybierz kartę **Widok listy.**

5. Wybierz **pozycję Dodaj regułę,** a następnie wypełnij pola formularza **Zestaw akcji.** W poniższym przykładzie obiekty BLOB są przenoszone do chłodnego magazynu, jeśli nie zostały zmodyfikowane przez 30 dni.

   ![Strona zestawu akcji zarządzania cyklem życia w witrynie Azure portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Wybierz **opcję Filtruj ustawioną,** aby dodać filtr opcjonalny. Następnie wybierz pozycję **Przeglądaj,** aby określić kontener i folder, według którego mają być filtrowane.

   ![Strona zestawu filtrów zarządzania cyklem życia w witrynie Azure portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Wybierz **pozycję Przejrzyj + dodaj,** aby przejrzeć ustawienia zasad.

9. Wybierz **pozycję Dodaj,** aby dodać nową zasadę.

#### <a name="azure-portal-code-view"></a>Widok kodu portalu Azure
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. W witrynie Azure portal wyszukaj i wybierz swoje konto magazynu.

3. W obszarze **Usługa obiektów blob**wybierz pozycję **Zarządzanie cyklem życia,** aby wyświetlić lub zmienić zasady.

4. Poniższy JSON jest przykładem zasad, które mogą być wklejane do karty **Widok kodu.**

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

5. Wybierz **pozycję Zapisz**.

6. Aby uzyskać więcej informacji na temat tego przykładu JSON, zobacz [zasady](#policy) i [reguły](#rules) sekcje.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Poniższy skrypt programu PowerShell może służyć do dodawania zasad do konta magazynu. Zmienna `$rgname` musi zostać zainicjowana z nazwą grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana z nazwą konta magazynu.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

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

# <a name="template"></a>[Szablonu](#tab/template)

Zarządzanie cyklem życia można zdefiniować przy użyciu szablonów usługi Azure Resource Manager. Oto przykładowy szablon do wdrożenia konta magazynu RA-GRS GPv2 z zasadami zarządzania cyklem życia.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

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
| `rules`        | Tablica obiektów reguł | Co najmniej jedna reguła jest wymagana w zasadach. W zasadach można zdefiniować maksymalnie 100 reguł.|

Każda reguła w ramach zasad ma kilka parametrów:

| Nazwa parametru | Typ parametru | Uwagi | Wymagany |
|----------------|----------------|-------|----------|
| `name`         | Ciąg |Nazwa reguły może zawierać maksymalnie 256 znaków alfanumerycznej. W nazwie reguły rozróżniana jest wielkość liter.  Musi być unikatowa w ramach zasad. | True |
| `enabled`      | Wartość logiczna | Opcjonalny wartość logiczna umożliwiająca tymczasową wyłączenie reguły. Wartość domyślna jest true, jeśli nie jest ustawiona. | False | 
| `type`         | Wartość wyliczenia | Bieżący prawidłowy `Lifecycle`typ to . | True |
| `definition`   | Obiekt definiujący regułę cyklu życia | Każda definicja składa się z zestawu filtrów i zestawu akcji. | True |

## <a name="rules"></a>Reguły

Każda definicja reguły zawiera zestaw filtrów i zestaw akcji. [Zestaw filtrów](#rule-filters) ogranicza akcje reguły do określonego zestawu obiektów w obrębie nazwy kontenera lub obiektów. [Zestaw akcji](#rule-actions) stosuje akcje warstwy lub usuwania do filtrowanego zestawu obiektów.

### <a name="sample-rule"></a>Przykładowa reguła

Poniższa przykładowa reguła filtruje konto, aby `container1` uruchomić akcje `foo`na obiektach, które istnieją wewnątrz i rozpocząć od .  

>[!NOTE]
>Zarządzanie cyklem życia obsługuje tylko typ bloku obiektu blob.  

- Obiekt blob poziomu do chłodzenia poziomu 30 dni po ostatniej modyfikacji
- Obiekt blob warstwy do archiwizacji warstwy 90 dni po ostatniej modyfikacji
- Usuwanie obiektu blob 2555 dni (siedem lat) po ostatniej modyfikacji
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

### <a name="rule-filters"></a>Filtry reguł

Filtry ograniczają akcje reguły do podzbioru obiektów blob na koncie magazynu. Jeśli zdefiniowano więcej niż jeden `AND` filtr, logiczne działa na wszystkich filtrach.

Dostępne są następujące filtry:

| Nazwa filtru | Typ filtru | Uwagi | Jest wymagane |
|-------------|-------------|-------|-------------|
| typy obiektów blob   | Tablica wstępnie zdefiniowanych wartości wyliczenia. | Bieżąca wersja `blockBlob`obsługuje . | Tak |
| prefiksMatch | Tablica ciągów dla prefiksów, które mają być zgodne. Każda reguła może definiować maksymalnie 10 prefiksów. Ciąg prefiksu musi zaczynać się od nazwy kontenera. Na przykład, jeśli chcesz dopasować `https://myaccount.blob.core.windows.net/container1/foo/...` wszystkie obiekty blob w obszarze `container1/foo`reguły, prefiksMatch jest . | Jeśli nie zdefiniujesz prefiksmatch, reguła ma zastosowanie do wszystkich obiektów blob w ramach konta magazynu.  | Nie |

### <a name="rule-actions"></a>Akcje reguły

Akcje są stosowane do filtrowanych obiektów blob po spełnieniu warunku uruchomienia.

Zarządzanie cyklem życia obsługuje warstwowe i usuwanie obiektów blob i usuwanie migawek obiektów blob. Zdefiniuj co najmniej jedną akcję dla każdej reguły na migawkach obiektów blob lub obiektów blob.

| Akcja        | Podstawowy obiekt blob                                   | Snapshot      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Obsługa obiektów blob obecnie w warstwie gorąca         | Nieobsługiwane |
| tierToArchive | Obsługa obiektów blob obecnie na gorącym lub chłodnym poziomie | Nieobsługiwane |
| delete        | Obsługiwane                                   | Obsługiwane     |

>[!NOTE]
>Jeśli zdefiniujesz więcej niż jedną akcję na tym samym obiekcie blob, zarządzanie cyklem życia stosuje najtańszą akcję do obiektu blob. Na przykład `delete` działanie jest tańsze `tierToArchive`niż działanie . Działanie `tierToArchive` jest tańsze `tierToCool`niż działanie .

Warunki biegu zależą od wieku. Podstawowe obiekty BLOB używają ostatniego zmodyfikowanego czasu do śledzenia wieku, a migawki obiektów blob używają czasu tworzenia migawki do śledzenia wieku.

| Warunek uruchomienia akcji             | Wartość warunku                          | Opis                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| dniPomodowaniuGreaterThan | Wartość całkowita wskazująca wiek w dniach | Warunek podstawowych akcji obiektu blob     |
| dniPokrujGreaterKreniaTa     | Wartość całkowita wskazująca wiek w dniach | Warunek akcji migawki obiektu blob |

## <a name="examples"></a>Przykłady

Poniższe przykłady pokazują, jak rozwiązać typowe scenariusze z regułami zasad cyklu życia.

### <a name="move-aging-data-to-a-cooler-tier"></a>Przenoszenie danych wiekowania do warstwy chłodniejszej

W tym przykładzie pokazano, jak przejść `container1/foo` `container2/bar`blok obiektów blob poprzedzony lub . Zasady przejścia obiektów blob, które nie zostały zmodyfikowane w ciągu 30 dni do chłodzenia magazynu i obiektów blob nie zmodyfikowane w ciągu 90 dni do warstwy archiwum:

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

### <a name="archive-data-after-ingest"></a>Archiwizuj dane po zjedzenie

Niektóre dane pozostają bezczynne w chmurze i rzadko, jeśli w ogóle, są dostępne po zapisaniu. Następujące zasady cyklu życia są skonfigurowane do archiwizowania danych wkrótce po ich spożyciu. W tym przykładzie przejścia blokować obiekty `archivecontainer` blob na koncie magazynu w kontenerze do warstwy archiwum. Przejście odbywa się poprzez działanie na obiektach blob 0 dni po ostatnim zmodyfikowanym czasie:

> [!NOTE] 
> Zaleca się przekazywanie obiektów blob bezpośrednio warstwy archiwum, aby być bardziej wydajne. Można użyć nagłówka x-ms-acess-tier dla [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) lub [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) z rest w wersji 2018-11-09 i nowszych lub naszych najnowszych bibliotek klienta magazynu obiektów blob. 

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

### <a name="expire-data-based-on-age"></a>Wygasają dane na podstawie wieku

Oczekuje się, że niektóre dane wygasną kilka dni lub miesięcy po utworzeniu. Zasady zarządzania cyklem życia można skonfigurować do wygasania danych przez usunięcie na podstawie wieku danych. W poniższym przykładzie przedstawiono zasadę, która usuwa wszystkie bloki blob starsze niż 365 dni.

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

### <a name="delete-old-snapshots"></a>Usuwanie starych migawek

W przypadku danych, które są regularnie modyfikowane i regularnie dostępne przez cały okres jego istnienia, migawki są często używane do śledzenia starszych wersji danych. Można utworzyć zasadę, która usuwa stare migawki na podstawie wieku migawki. Wiek migawki jest określana przez ocenę czasu tworzenia migawki. Ta reguła zasad usuwa migawki `activedata` bloków obiektów blob w kontenerze, które są 90 dni lub starszych po utworzeniu migawki.

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

## <a name="faq"></a>Najczęściej zadawane pytania

**Stworzyłem nową zasadę, dlaczego akcje nie są uruchamiane natychmiast?**  
Platforma uruchamia zasady cyklu życia raz dziennie. Po skonfigurowaniu zasad może upłynąć do 24 godzin, aby niektóre akcje zostały uruchomione po raz pierwszy.  

**Jeśli zaktualizuję istniejące zasady, jak długo trwa uruchamianie akcji?**  
Zaktualizowane zasady mogą wejść w życie do 24 godzin. Po wejściu w życie zasady może upłynąć do 24 godzin, aby uruchomić akcje. W związku z tym działania zasad może potrwać do 48 godzin, aby zakończyć.   

**Ręcznie nawodniłem zarchiwizowaną obiekt blob, jak zapobiec tymczasowemu przeniesieniu go z powrotem do warstwy Archiwum?**  
Gdy obiekt blob jest przenoszony z jednej warstwy dostępu do innej, jego ostatni czas modyfikacji nie zmienia. Jeśli ręcznie nawodnienia zarchiwizowanego obiektu blob do warstwy gorąca, zostanie on przeniesiony z powrotem do warstwy archiwum przez aparat zarządzania cyklem życia. Wyłącz regułę, która wpływa tymczasowo na ten obiekt blob, aby zapobiec ponownemu archiwizacji. Ponownie włącz regułę, gdy obiekt blob można bezpiecznie przenieść z powrotem do warstwy archiwum. Można również skopiować obiekt blob do innej lokalizacji, jeśli musi pozostać w gorącej lub chłodnej warstwie na stałe.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak odzyskać dane po przypadkowym usunięciu:

- [Soft delete for Azure Storage blobs](../blobs/storage-blob-soft-delete.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)
