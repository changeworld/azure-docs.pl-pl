---
title: Usuwanie nietrwałe dla obiektów blob usługi Azure Storage | Dokumenty firmy Microsoft
description: Usługa Azure Storage oferuje teraz nietrwałe usuwanie obiektów obiektów blob, dzięki czemu można łatwiej odzyskać dane, gdy są błędnie modyfikowane lub usuwane przez użytkownika konta magazynu aplikacji lub innego.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4deae235ed15d02874ab5cb3470c62e934324364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234293"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Soft delete for Azure Storage blobs (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

Usługa Azure Storage oferuje teraz nietrwałe usuwanie obiektów obiektów blob, dzięki czemu można łatwiej odzyskać dane, gdy są błędnie modyfikowane lub usuwane przez użytkownika konta magazynu aplikacji lub innego.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Jak działa usuwanie nietrwałe

Po włączeniu usuwania nietrwałego umożliwia zapisywanie i odzyskiwanie danych po usunięciu migawek obiektów blob lub obiektów blob. Ta ochrona rozciąga się na dane obiektów blob, który jest usuwany w wyniku nadpisywania.

Po usunięciu danych przechodzi do stanu nietrwale usunięte zamiast trwale wymazane. Gdy usuwanie nietrwałe jest włączone i zastępujesz dane, jest generowana nietrwale usunięta migawka w celu zapisania stanu nadpisanych danych. Nietrwale usunięte obiekty są niewidoczne, chyba że zostaną wyraźnie wymienione. Możesz skonfigurować ilość czasu, przez który nietrwale usunięte dane będą możliwe do odzyskania, zanim definitywnie wygasną.

Usuwanie nietrwałe jest zgodne z powrotem, więc nie trzeba wprowadzać żadnych zmian w aplikacjach, aby skorzystać z zabezpieczeń, jakie zapewnia ta funkcja. Jednak [odzyskiwanie danych](#recovery) wprowadza nowy interfejs API **cofania usunięcia obiektów Blob.**

### <a name="configuration-settings"></a>Ustawienia konfiguracji

Podczas tworzenia nowego konta usuwanie nietrwałe jest domyślnie wyłączone. Usuwanie nietrwałe jest również domyślnie wyłączone dla istniejących kont magazynu. Funkcję można włączać i wyłączać w dowolnym momencie w trakcie użytkowania konta magazynu.

Nadal będzie można uzyskać dostęp do nietrwale usuniętych danych i odzyskać je po wyłączeniu funkcji, przy założeniu, że nietrwałe usunięte dane zostały zapisane, gdy funkcja została wcześniej włączona. Po włączeniu usuwania nietrwałego należy również skonfigurować okres przechowywania.

Okres przechowywania wskazuje czas, przez który nietrwale usunięte dane są przechowywane i dostępne do odzyskania. W przypadku migawek obiektów blob i obiektów blob, które są jawnie usuwane, zegar okresu przechowywania rozpoczyna się po usunięciu danych. W przypadku nietrwale usuniętych migawek generowanych przez funkcję usuwania nietrwałego po zastąpieniu danych zegar rozpoczyna się po wygenerowaniu migawki. Obecnie można zachować nietrwale usunięte dane przez okres od 1 do 365 dni.

Okres przechowywania nietrwałego usuwania można zmienić w dowolnym momencie. Zaktualizowany okres przechowywania będzie miał zastosowanie tylko do nowo usuniętych danych. Wcześniej usunięte dane wygasną na podstawie okresu przechowywania, który został skonfigurowany po usunięciu tych danych. Próba usunięcia nietrwale usuniętego obiektu nie wpłynie na jego czas wygaśnięcia.

### <a name="saving-deleted-data"></a>Zapisywanie usuniętych danych

Usuwanie nietrwałe zachowuje dane w wielu przypadkach, gdy migawki obiektów blob są usuwane lub zastępowane.

Gdy obiekt blob jest zastępowany przy użyciu **Put Blob**, **Put Block**, Put **Block List**lub **Kopiuj obiekt blob** migawka stanu obiektu blob przed operacją zapisu jest generowany automatycznie. Ta migawka jest nietrwale usunięta migawka; jest niewidoczny, chyba że nietrwale usunięte obiekty są jawnie wymienione. Zobacz sekcję [Odzyskiwanie,](#recovery) aby dowiedzieć się, jak wyświetlić listę nietrwale usuniętych obiektów.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Nietrwale usunięte dane są szare, a aktywne dane niebieskie. Ostatnio napisane dane pojawiają się pod starszymi danymi. Po zastąpieniu B0 b1, nietrwale usunięte migawki B0 jest generowany. Po zastąpieniu B1 za pomocą B2 jest generowana nietrwale usunięta migawka B1.*

> [!NOTE]  
> Usuwanie nietrwałe zapewnia ochronę przed zastępowaniem operacji kopiowania tylko wtedy, gdy jest włączone dla konta docelowego obiektu blob.

> [!NOTE]  
> Usuwanie nietrwałe nie zapewnia ochrony zastępowania obiektów blob w warstwie archiwum. Jeśli obiekt blob w archiwum zostanie zastąpiony nowym obiektem blob w dowolnej warstwie, nadpisany obiekt blob na stałe wygasł.

Gdy **usuń obiekt blob** jest wywoływana na migawkę, że migawka jest oznaczony jako nietrwale usunięte. Nowa migawka nie jest generowana.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Nietrwale usunięte dane są szare, a aktywne dane niebieskie. Ostatnio napisane dane pojawiają się pod starszymi danymi. Gdy wywoływany jest **obiekt Blob migawki,** B0 staje się migawką, a B1 jest aktywnym stanem obiektu blob. Po usunięciu migawki B0 jest oznaczona jako nietrwale usunięta.*

Gdy **usuń obiekt blob** jest wywoływana na podstawowy obiekt blob (dowolny obiekt blob, który nie jest sam migawka), że obiekt blob jest oznaczony jako nietrwałe usunięte. Zgodnie z poprzednim zachowaniem **wywołanie usuń obiektu blob** w obiekcie blob, który ma aktywne migawki zwraca błąd. Wywołanie **usuń obiektu blob** na obiekcie blob z nietrwale usuniętych migawek nie zwraca błąd. Nadal można usunąć obiekt blob i wszystkie jego migawki w jednej operacji po włączeniu usuwania nietrwałego. W ten sposób oznacza podstawowy obiekt blob i migawki jako nietrwale usunięte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Nietrwale usunięte dane są szare, a aktywne dane niebieskie. Ostatnio napisane dane pojawiają się pod starszymi danymi. W tym miejscu **delete blob** wywołanie jest w celu usunięcia B2 i wszystkie skojarzone migawki. Aktywny obiekt blob, B2 i wszystkie skojarzone migawki są oznaczone jako nietrwałe usunięte.*

> [!NOTE]  
> Po zastąpieniu nietrwale usuniętego obiektu blob automatycznie jest generowana nietrwale usunięta migawka stanu obiektu blob przed operacją zapisu. Nowy obiekt blob dziedziczy warstwę nadpisanego obiektu blob.

Usuwanie nietrwałe nie zapisuje danych w przypadku usunięcia kontenera lub konta, ani gdy metadane obiektów blob i właściwości obiektów blob są zastępowane. Aby chronić konto magazynu przed błędnym usunięciem, można skonfigurować blokadę przy użyciu usługi Azure Resource Manager. Aby dowiedzieć się więcej, zobacz artykuł Usługi Azure Resource Manager [Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom.](../../azure-resource-manager/management/lock-resources.md)

Następująca tabela zawiera szczegóły oczekiwanego zachowania po włączeniu usuwania nietrwałego:

| Operacja INTERFEJSU API REST | Typ zasobu | Opis | Zmiana zachowania |
|--------------------|---------------|-------------|--------------------|
| [Usuwanie](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Usuwa konto magazynu, w tym wszystkie kontenery i obiekty blob, które zawiera.                           | Bez zmian. Kontenery i obiekty BLOB na usuniętym koncie nie można odzyskać. |
| [Usuwanie kontenera](/rest/api/storageservices/delete-container) | Kontener | Usuwa kontener, w tym wszystkie obiekty blob, które zawiera. | Bez zmian. Obiekty BLOB w usuniętym kontenerze nie można odzyskać. |
| [Wstawianie obiektu blob](/rest/api/storageservices/put-blob) | Obiekty blob bloków, dołączania i stron | Tworzy nowy obiekt blob lub zastępuje istniejący obiekt blob w kontenerze | Jeśli jest używany do zastąpienia istniejącego obiektu blob, migawka stanu obiektu blob przed wywołaniem jest generowany automatycznie. Dotyczy to również wcześniej nietrwale usuniętego obiektu blob, jeśli i tylko wtedy, gdy zostanie zastąpiony przez obiekt blob tego samego typu (Blokuj, Dołącz lub Strona). Jeśli zostanie zastąpiony przez obiekt blob innego typu, wszystkie istniejące nietrwałe usunięte dane zostaną trwale wygasły. |
| [Usuwanie obiektu blob](/rest/api/storageservices/delete-blob) | Obiekty blob bloków, dołączania i stron | Oznacza migawkę obiektu blob lub obiektu blob do usunięcia. Obiekt blob lub migawka jest później usuwany podczas wyrzucania elementów bezużytecznych | Jeśli jest używany do usuwania migawki obiektu blob, ta migawka jest oznaczona jako nietrwale usunięte. Jeśli jest używany do usuwania obiektu blob, ten obiekt blob jest oznaczony jako nietrwałe usunięte. |
| [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob) | Obiekty blob bloków, dołączania i stron | Kopiuje źródłowy obiekt blob do docelowego obiektu blob na tym samym koncie magazynu lub na innym koncie magazynu. | Jeśli jest używany do zastąpienia istniejącego obiektu blob, migawka stanu obiektu blob przed wywołaniem jest generowany automatycznie. Dotyczy to również wcześniej nietrwale usuniętego obiektu blob, jeśli i tylko wtedy, gdy zostanie zastąpiony przez obiekt blob tego samego typu (Blokuj, Dołącz lub Strona). Jeśli zostanie zastąpiony przez obiekt blob innego typu, wszystkie istniejące nietrwałe usunięte dane zostaną trwale wygasły. |
| [Umieść blok](/rest/api/storageservices/put-block) | Blokowe obiekty blob | Tworzy nowy blok do zaapelowatego jako część bloku blob. | Jeśli jest używany do zatwierdzania bloku do obiektu blob, który jest aktywny, nie ma żadnych zmian. Jeśli jest używany do zatwierdzania bloku do obiektu blob, który jest nietrwale usunięty, tworzony jest nowy obiekt blob i migawka jest generowany automatycznie do przechwytywania stanu nietrwale usunięte obiektu blob. |
| [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list) | Blokowe obiekty blob | Zatwierdza obiekt blob, określając zestaw identyfikatorów bloków, które składają się na blokowy obiekt blob. | Jeśli jest używany do zastąpienia istniejącego obiektu blob, migawka stanu obiektu blob przed wywołaniem jest generowany automatycznie. Dotyczy to również wcześniej nietrwale usuniętych obiektów blob, jeśli i tylko wtedy, gdy jest blok obiektu blob. Jeśli zostanie zastąpiony przez obiekt blob innego typu, wszystkie istniejące nietrwałe usunięte dane zostaną trwale wygasły. |
| [Umieść stronę](/rest/api/storageservices/put-page) | Stronicowe obiekty blob | Zapisuje zakres stron do obiektu blob strony. | Bez zmian. Dane obiektów blob strony, który jest zastępowany lub czyszczone przy użyciu tej operacji nie jest zapisywany i nie można odzyskać. |
| [Dołącz blok](/rest/api/storageservices/append-block) | Obiekty blob dołączania | Zapisuje blok danych na końcu dołączania obiektu blob | Bez zmian. |
| [Ustawianie właściwości obiektu blob](/rest/api/storageservices/set-blob-properties) | Obiekty blob bloków, dołączania i stron | Ustawia wartości właściwości systemu zdefiniowane dla obiektu blob. | Bez zmian. Właściwości nadpisanych obiektów blob nie można odzyskać. |
| [Ustawianie metadanych obiektów blob](/rest/api/storageservices/set-blob-metadata) | Obiekty blob bloków, dołączania i stron | Ustawia metadane zdefiniowane przez użytkownika dla określonego obiektu blob jako co najmniej jedną parę nazwa-wartość. | Bez zmian. Zastąpione metadane obiektów blob nie można odzyskać. |

Należy zauważyć, że wywołanie "Umieść stronę" w celu nadpisywać lub wyczyść zakresy obiektu blob strony nie będzie automatycznie generować migawki. Dyski maszyny wirtualnej są wspierane przez obiekty blob strony i użyć **put page** do zapisu danych.

### <a name="recovery"></a>Odzyskiwanie

Wywołanie operacji [Cofania usunięcia obiektu blob](/rest/api/storageservices/undelete-blob) na nietrwałym usuniętym podstawowym obiekcie blob przywraca go i wszystkie skojarzone nietrwałe usunięte migawki jako aktywne. Wywołanie `Undelete Blob` operacji na aktywnym podstawowym obiekcie blob przywraca wszystkie skojarzone nietrwałe usunięte migawki jako aktywne. Gdy migawki są przywracane jako aktywne, wyglądają jak migawki generowane przez użytkownika; nie zastępują bazowego obiektu blob.

Aby przywrócić obiekt blob do określonej nietrwale usuniętej migawki, można wywołać `Undelete Blob` na podstawowy obiekt blob. Następnie można skopiować migawkę za pomocą teraz aktywnego obiektu blob. Migawkę można również skopiować do nowego obiektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Nietrwale usunięte dane są szare, a aktywne dane niebieskie. Ostatnio napisane dane pojawiają się pod starszymi danymi. W tym miejscu **Cofnij usunięcie obiektu blob** jest wywoływana w obiekcie blob B, przywracając w ten sposób podstawowy obiekt blob, B1 i wszystkie skojarzone migawki, tutaj tylko B0, jako aktywne. W drugim kroku B0 jest kopiowany przez podstawowy obiekt blob. Ta operacja kopiowania generuje nietrwale usunięte migawki B1.*

Aby wyświetlić nietrwale usunięte obiekty BLOB i migawki obiektów blob, można dołączyć usunięte dane do **listy obiektów blob**. Można wybrać wyświetlanie tylko nietrwale usuniętych podstawowych obiektów blob lub dołączyć również nietrwale usunięte migawki obiektów blob. W przypadku wszystkich nietrwalonych danych można wyświetlić czas, kiedy dane zostały usunięte, a także liczbę dni przed trwałym wygaśnięciem danych.

### <a name="example"></a>Przykład

Poniżej przedstawiono dane wyjściowe konsoli skryptu .NET, który przekazuje, zastępuje, migawki, usuwa i przywraca obiekt blob o nazwie *HelloWorld* po włączeniu usuwania nietrwałego:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Zobacz [Następne kroki](#next-steps) sekcji dla wskaźnika do aplikacji, która wyprodukowała to dane wyjściowe.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie nietrwałe usunięte dane są rozliczane z taką samą szybkością jak aktywne dane. Opłata nie zostanie naliczona za dane, które zostaną trwale usunięte po skonfigurowanym okresie przechowywania. Aby uzyskać głębsze zapoznanie się z migawkami i sposób ich naliczania opłat, zobacz [Opis sposobu naliczania naliczania opłat przez migawki.](storage-blob-snapshots.md)

Nie będą naliczane opłaty za transakcje związane z automatycznym generowaniem migawek. Opłaty będą naliczane za transakcje **undelete blob** po kursie dla operacji zapisu.

Aby uzyskać więcej informacji na temat cen usługi Azure Blob Storage w ogóle, zapoznaj się z [witryny Cennik usługi Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Po początkowym włączeniu usuwania nietrwałego zalecamy użycie małego okresu przechowywania, aby lepiej zrozumieć, jak ta funkcja wpłynie na rachunek.

## <a name="get-started"></a>Wprowadzenie

W poniższych krokach pokazano, jak rozpocząć pracę z usuwaniem nietrwałym.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Włącz usuwanie nietrwałe dla obiektów blob na koncie magazynu przy użyciu witryny Azure portal:

1. W [witrynie Azure portal](https://portal.azure.com/)wybierz swoje konto magazynu. 

2. Przejdź do opcji **Ochrona danych** w obszarze Usługa **obiektów Blob**.

3. Kliknij **pozycję Włączone** w obszarze Usuwanie **nietrwałe obiektów Blob**

4. Wprowadź liczbę dni, dla których chcesz *zachować w* obszarze **Zasady przechowywania**

5. Wybierz przycisk **Zapisz,** aby potwierdzić ustawienia ochrony danych

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Aby wyświetlić nietrwałe usunięte obiekty blob, zaznacz pole wyboru **Pokaż usunięte obiekty blob.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Aby wyświetlić nietrwale usunięte migawki dla danego obiektu blob, zaznacz obiekt blob, a następnie kliknij przycisk **Wyświetl migawki**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Upewnij się, że jest zaznaczone pole wyboru **Pokaż usunięte migawki.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Po kliknięciu na nietrwale usunięty obiekt blob lub migawki, zwróć uwagę na nowe właściwości obiektu blob. Wskazują one, kiedy obiekt został usunięty i ile dni pozostało do migawki obiektu blob lub obiektu blob trwale wygasła. Jeśli nietrwale usunięty obiekt nie jest migawką, będzie również można go cofnąć.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Należy pamiętać, że cofnięcie usunięcia obiektu blob spowoduje również cofnięcie wszystkich skojarzonych migawek. Aby cofnąć usunięcie nieumarszczonych usuniętych migawek dla aktywnego obiektu blob, kliknij obiekt blob i wybierz pozycję **Cofnij usuwanie wszystkich migawek**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po cofnięciu usunięcia migawek obiektu blob, można kliknąć **promuj,** aby skopiować migawkę za pomocą głównego obiektu blob, przywracając w ten sposób obiekt blob do migawki.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektu blob. Poniższy przykład umożliwia usuwanie trębowe dla podzbioru kont w subskrypcji:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Usuwanie nietrwałe zostało włączone za pomocą następującego polecenia:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Aby odzyskać obiekty BLOB, które zostały przypadkowo usunięte, wywołać Undelete na tych obiektów blob. Należy pamiętać, że **wywołanie Undelete Blob**, zarówno na aktywnych i nietrwałych usuniętych obiektów blob, przywróci wszystkie skojarzone nietrwałe usunięte migawki jako aktywne. Poniższy przykład wywołuje Undelete na wszystkich nietrwale usuniętych i aktywnych obiektów blob w kontenerze:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Aby znaleźć bieżące zasady przechowywania usuwania nietrwałego, użyj następującego polecenia:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[Cli](#tab/azure-CLI)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektu blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Aby sprawdzić, czy usuwanie nietrwałe jest włączone, użyj następującego polecenia: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektu blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektu blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Aby odzyskać obiekty BLOB, które zostały przypadkowo usunięte, wywołać Undelete na tych obiektów blob. Należy pamiętać, że **wywołanie Undelete Blob**, zarówno na aktywnych i nietrwałych usuniętych obiektów blob, przywróci wszystkie skojarzone nietrwałe usunięte migawki jako aktywne. Poniższy przykład wywołuje Undelete na wszystkich nietrwale usuniętych i aktywnych obiektów blob w kontenerze:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Aby odzyskać do określonej wersji obiektu blob, najpierw wywołać Undelete na obiekcie blob, a następnie skopiować żądaną migawkę za pomocą obiektu blob. Poniższy przykład odzyskuje blok obiektu blob do ostatnio wygenerowanej migawki:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>Uwagi szczególne

Jeśli istnieje prawdopodobieństwo, że dane zostaną przypadkowo zmodyfikowane lub usunięte przez aplikację lub innego użytkownika konta magazynu, zaleca się włączenie usuwania nietrwałego. Włączenie usuwania nietrwałego dla często nadpisywanych danych może spowodować zwiększenie opłat za pojemność magazynu i zwiększone opóźnienie podczas wyświetlania obiektów blob. Można ograniczyć ten dodatkowy koszt i opóźnienie, przechowując często nadpisywane dane na osobnym koncie magazynu, gdzie usuwanie nietrwałe jest wyłączone. 

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Dla jakich usług magazynu można użyć usuwania nietrwałego?

Obecnie usuwanie nietrwałe jest dostępne tylko dla magazynu obiektów blob (object).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Czy usuwanie nietrwałe jest dostępne dla wszystkich typów kont magazynu?

Tak, usuwanie nietrwałe jest dostępne dla kont magazynu obiektów Blob, a także dla obiektów blob w kontach magazynu ogólnego przeznaczenia (zarówno GPv1, jak i GPv2). Obsługiwane są zarówno standardowe, jak i premium typy kont. Usuwanie nietrwałe jest dostępne dla dysków niezarządzanych, które są obiektami blob stron pod osłonami. Usuwanie nietrwałe nie jest dostępne dla dysków zarządzanych.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Czy usuwanie nietrwałe jest dostępne dla wszystkich warstw magazynu?

Tak, usuwanie nietrwałe jest dostępne dla wszystkich warstw magazynu, w tym hot, cool i archiwum. Jednak usuwanie nietrwałe nie zapewnia ochrony zastępowania obiektów blob w warstwie archiwum.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Czy można użyć interfejsu API warstwy zestaw obiektów blob do warstwy obiektów blob z nietrwale usuniętymi migawkami?

Tak. Nietrwone usunięte migawki pozostaną w oryginalnej warstwie, ale podstawowy obiekt blob zostanie przeniesione do nowej warstwy. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Konta magazynu w wersji Premium mają limit migawki obiektu blob wynoszący 100. Czy nietrwale usunięte migawki są wliczane do tego limitu?

Nie, nietrwale usunięte migawki nie są wliczane do tego limitu.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Czy mogę włączyć usuwanie nietrwałe dla istniejących kont magazynu?

Tak, usuwanie nietrwałe można skonfigurować zarówno dla istniejących, jak i nowych kont magazynu.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Jeśli usunę całe konto lub kontener z włączonym usunięciem nietrwałym, czy wszystkie skojarzone obiekty BLOB zostaną zapisane?

Nie, jeśli usuniesz całe konto lub kontener, wszystkie skojarzone obiekty BLOB zostaną trwale usunięte. Aby uzyskać więcej informacji na temat ochrony konta magazynu przed przypadkowymi usunięciami, zobacz [Blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Czy mogę wyświetlić metryki pojemności usuniętych danych?

Nietrwale usunięte dane są uwzględniane jako część całkowitego pojemność konta magazynu. Aby uzyskać więcej informacji na temat śledzenia i monitorowania pojemności pamięci masowej, zobacz [Analiza pamięci masowej](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Czy jeśli wyłączę usuwanie nietrwałe, nadal będę mieć dostęp do nietrwałych usuniętych danych?

Tak, nadal będziesz mieć dostęp do niewygasanych nietrwalonych danych usuniętych, gdy usuwanie nietrwałe jest wyłączone.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Czy mogę odczytać i skopiować nietrwale usunięte migawki mojego obiektu blob?  

Tak, ale najpierw należy wywołać Undelete na obiekcie blob.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Czy usuwanie nietrwałe jest dostępne dla wszystkich typów obiektów blob?

Tak, usuwanie nietrwałe jest dostępne dla bloków obiektów blob, dołączania obiektów blob i stronicowych obiektów blob.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Czy usuwanie nietrwałe jest dostępne dla dysków maszyn wirtualnych?  

Usuwanie nietrwałe jest dostępne zarówno dla dysków premium, jak i standardowych niezarządzanych, które są obiektami blob stron pod osłonami. Usuwanie nietrwałe pomoże tylko odzyskać dane usunięte przez **usuń obiekt blob**, **Put Blob**, **Put Block List**, Put **Block** i **Copy Blob** operacji. Dane zastąpione przez wywołanie **strony Put Page** nie można odzyskać.

Maszyna wirtualna platformy Azure zapisuje na dysku niezarządzanym przy użyciu wywołań **put page**, więc przy użyciu usuwania nietrwałego, aby cofnąć zapisy na dysku niezarządzanym z maszyny Wirtualnej platformy Azure nie jest obsługiwany scenariusz.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Czy muszę zmienić istniejące aplikacje, aby używać usuwania nietrwałego?

Istnieje możliwość skorzystania z usuwania nietrwałego niezależnie od używanej wersji interfejsu API. Jednak aby wyświetlić listę i odzyskać nietrwale usunięte obiekty blob i migawki obiektów blob, należy użyć wersji 2017-07-29 [interfejsu API REST usług magazynu](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) lub większej. Firma Microsoft zaleca zawsze przy użyciu najnowszej wersji interfejsu API usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

* [Przykładowy kod platformy .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Interfejs API REST usługi Blob Service](/rest/api/storageservices/blob-service-rest-api)
* [Replikacja usługi Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Projektowanie aplikacji o wysokiej dostępności przy użyciu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Odzyskiwanie po awarii i przeczesywek konta magazynu (wersja zapoznawcza) w usłudze Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
