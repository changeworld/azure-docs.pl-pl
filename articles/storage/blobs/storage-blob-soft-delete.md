---
title: Soft usuwania obiektów blob magazynu Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Azure Storage oferuje teraz usuwania nietrwałego (wersja zapoznawcza) dla obiektów blob, aby łatwiej można odzyskać dane, gdy jest błędnie zmodyfikowany lub usunięty przez aplikację lub innego użytkownika z konta magazynu.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: mihauss
ms.openlocfilehash: 0e728f9f9754d76d893b12309bb52201d772efbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057863"
---
# <a name="soft-delete-for-azure-storage-blobs-preview"></a>Usuwania nietrwałego dla obiektów blob magazynu Azure (wersja zapoznawcza)

## <a name="overview"></a>Przegląd

Azure Storage oferuje teraz usuwania nietrwałego (wersja zapoznawcza) dla obiektów blob, aby łatwiej można odzyskać dane, gdy jest błędnie zmodyfikowany lub usunięty przez aplikację lub innego użytkownika z konta magazynu.

## <a name="how-does-it-work"></a>Jak to działa?

Po włączeniu, usuwania nietrwałego umożliwia zapisywanie i odzyskiwanie danych po usunięciu obiektów blob lub migawki obiektu blob. Ta ochrona obejmuje dane obiektów blob jest usunięte w wyniku nadpisanie.

Gdy dane są usuwane, przejdzie stanie zamiast są trwale usuwane. Podczas usuwania nietrwałego jest włączona i zastąpienia danych, elastyczne migawki usuniętych jest generowany można zapisać stanu dane zastąpione. Elastyczne usuniętych obiektów są niewidoczne, chyba że jawnie wymienione.
Możesz skonfigurować czas, który nietrwałego usunięte dane są możliwe do odzyskania, zanim trwale wygasł.

Usuwania nietrwałego jest wstecznie zgodna; nie trzeba wprowadzać żadnych zmian w aplikacji, aby móc korzystać z ochrony, który zapewnia tę funkcję. Jednak [odzyskiwania danych](#recovery) wprowadzono nowy **Blob cofanie usuwania** interfejsu API.

> [!NOTE]
> W publicznej wersji zapoznawczej wywoływania ustawić warstwy obiektu Blob dla obiektu blob z migawki jest niedozwolone.
Usuwania nietrwałego generuje migawek, aby chronić dane, gdy jest on zastąpiony. Aktywnie pracujemy nad rozwiązanie umożliwiające obsługę poziomów obiektów blob z migawki.

### <a name="configuration-settings"></a>Ustawienia konfiguracji

Podczas tworzenia nowego konta usuwania nietrwałego jest domyślnie wyłączone. Usuwania nietrwałego jest również domyślnie wyłączona w przypadku istniejących kont magazynu. Funkcja włączać i wyłączać można przełączyć w czasie życia konta magazynu.

Nadal będzie mogła uzyskać dostęp i odzyskiwanie danych usuniętych miękkie, gdy funkcja ta jest wyłączona, przy założeniu, że nietrwałego usunięte dane został zapisany, gdy wcześniej została włączona funkcja. Po włączeniu usuwania nietrwałego, należy skonfigurować okres przechowywania.

Okres przechowywania wskazuje ilość czasu, przez który nietrwałego usunięte dane są przechowywane i dostępne do odzyskania. Dla obiektów blob i migawki obiektu blob, które są jawnie usunięte zegara okresu przechowywania rozpoczyna się, gdy dane są usuwane. Elastyczne migawki usunięto generowane przez funkcję usuwania nietrwałego, gdy dane są zastępowane zegar rozpoczyna się po wygenerowaniu migawki. Obecnie można zachować nietrwałego usunięte dane dla zakresu od 1 do 365 dni.

Można zmienić w dowolnym momencie okres przechowywania usuwania nietrwałego. Okres przechowywania zaktualizowane będzie dotyczyć tylko usunięte nowo dane. Wcześniej usunięte dane wygaśnie oparte na okres przechowywania skonfigurowanego podczas tych danych została usunięta.

### <a name="saving-deleted-data"></a>Zapisywanie danych usuniętych

Usuwania nietrwałego zachowuje dane w wielu przypadkach, gdy obiekty BLOB lub migawki obiektu blob są usuwane lub zastąpione.

Gdy obiektu blob jest zastępowany przy użyciu **umieszczanie obiektu Blob**, **Put bloku**, **Put listy zablokowanych** lub **kopiowania obiektu Blob** migawkę stanu obiektu blob w programach starszych niż Operacja jest generowana automatycznie zapisu. Ta migawka jest nietrwałego migawki usunięte; jest niewidoczny, chyba że wymienionych nietrwałego usuniętych obiektów. Zobacz [odzyskiwania](#recovery) sekcji, aby dowiedzieć się, jak lista nietrwałego usuniętych obiektów.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Elastyczne usunięte dane jest szary, zablokowaniu niebieski aktywnych danych. Więcej ostatnio zapisywane dane są wyświetlane poniżej starszych danych. Gdy B0 jest zastępowany B1, słabe Usunięto migawkę B0 jest generowany. Gdy B1 jest zastępowany B2, nietrwałego Usunięto migawkę B1 jest generowany.*

> [!NOTE]
> Tylko zapewnia usuwania nietrwałego zastąpić ochronę operacje kopiowania, gdy jest włączona dla konta docelowego obiektu blob.

> [!NOTE]
> Nie dają usuwania nietrwałego zastąpić ochronę obiekty BLOB w warstwie archiwum. Jeśli obiekt blob w archiwum jest zastępowany nowych obiektów blob w dowolnej wersji, zastąpione obiektu blob trwale wygasł.

Podczas **usunąć obiektu Blob** jest wywoływana w migawce tej migawki jest oznaczony jako nietrwałego usunięte. Nie jest generowany nową migawkę.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Elastyczne usunięte dane jest szary, zablokowaniu niebieski aktywnych danych. Więcej ostatnio zapisywane dane są wyświetlane poniżej starszych danych. Gdy **migawki obiektu Blob** jest wywoływana, B0 staje się migawkę i B1 jest w stanie aktywnym obiektu blob. Po usunięciu migawki B0 jest oznaczony jako nietrwałego usunięte.\*

Gdy **usunąć obiektu Blob** zostanie wywołany dla obiekt blob podstawowej (żadnego obiektu blob to znaczy nie migawkę), tego obiektu blob jest oznaczony jako słabe usunięte. Zgodny z poprzednim zachowanie wywoływania **usunąć obiektu Blob** dla obiektu blob, dysponującej migawkami active zwraca błąd. Wywoływanie **usunąć obiektu Blob** dla obiektu blob z migawkami usunięto miękkiej nie zwraca błąd. Możesz nadal usunąć obiektu blob i wszystkie jego migawek w jednej operacji po włączeniu usuwania nietrwałego. W ten sposób oznacza podstawowego obiektu blob i usunąć migawki jako nietrwałego.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Elastyczne usunięte dane jest szary, zablokowaniu niebieski aktywnych danych. Więcej ostatnio zapisywane dane są wyświetlane poniżej starszych danych. W tym miejscu **usunąć obiektu Blob** wywołanie do usuwania B2 i wszystkie skojarzone migawki. Aktywne obiektów blob, B2 i wszystkie skojarzone migawki są oznaczone jako nietrwałego usunięte.\*

> [!NOTE]
> Słabe usunięto obiekt blob jest zastąpiony, jest generowany automatycznie nietrwałego migawka usuniętego obiektu blob stanu przed operacji zapisu. Nowy obiekt blob dziedziczy warstwy zastąpione obiektu blob.

Usuwania nietrwałego nie zapisywanie danych w przypadku kontenera lub usuwa konta, ani Jeśli metadane obiektu blob i właściwości obiektu blob zostaną zastąpione. Aby chronić konto magazynu, przed usunięciem błędne, można skonfigurować blokadę przy użyciu usługi Azure Resource Manager. Zobacz artykuł usługi Azure Resource Manager [blokady zasobów, aby zapobiec nieoczekiwane](../../azure-resource-manager/resource-group-lock-resources.md) Aby dowiedzieć się więcej.

W poniższej tabeli przedstawiono oczekiwane zachowanie po włączeniu usuwania nietrwałego:

| Operacja interfejsu API REST                                                                                                | Typ zasobu                 | Opis                                                                                                 | Zmiana zachowania                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Usuwanie](/rest/api/storagerp/StorageAccounts/Delete)              | Konto                       | Usuwa konto magazynu, w tym wszystkie kontenery i obiekty BLOB, które zawiera.                           | Brak zmian. Kontenery i obiekty BLOB w usuniętego konta nie są możliwe do odzyskania.                                                                                                                                                                                                                                                                                          |
| [Usunąć kontenera](/rest/api/storageservices/fileservices/delete-container)       | Kontener                     | Usuwa kontener, w tym wszystkie obiekty BLOB, które zawiera.                                                | Brak zmian. Obiekty BLOB w kontenerze usuniętych nie są możliwe do odzyskania.                                                                                                                                                                                                                                                                                                       |
| [Put Blob](/rest/api/storageservices/fileservices/put-blob)                       | Dołącz bloku i stronicowe obiekty BLOB | Tworzy nowy obiekt blob lub Zamienia istniejący obiekt blob w kontenerze                                          | Jeśli używany do zastępowania istniejącego obiektu blob, automatycznie wygenerowana Migawka stanu obiektu blob przed wywołaniem. Dotyczy to również nietrwałego wcześniej usuniętych obiektów blob tylko wtedy, gdy zostanie zastąpiony przez blob tego samego typu (bloku, Dołącz lub strony). Zastępuje obiektu blob innego typu, wszystkie istniejące dane usunięto nietrwałego zostanie trwale wygasł. |
| [Usuwanie obiektów Blob](/rest/api/storageservices/fileservices/delete-blob)                 | Dołącz bloku i stronicowe obiekty BLOB | Oznacza obiekt blob lub migawki obiektu blob do usunięcia. Obiekt blob lub migawki zostaną później usunięte podczas wyrzucanie elementów bezużytecznych | Jeśli używany do usunąć Usuń migawki obiektu blob, tej migawki jest oznaczone jako nietrwałego. Jeśli używana, można usunąć obiektu blob, tego obiektu blob jest oznaczony jako nietrwałego usunięte.                                                                                                                                                                                                                           |
| [Copy Blob](/rest/api/storageservices/fileservices/copy-blob)                     | Dołącz bloku i stronicowe obiekty BLOB | Kopiuje źródłowego obiektu blob do docelowego obiektu blob w tym samym koncie magazynu lub na innym koncie magazynu.       | Jeśli używany do zastępowania istniejącego obiektu blob, automatycznie wygenerowana Migawka stanu obiektu blob przed wywołaniem. Dotyczy to również nietrwałego wcześniej usuniętych obiektów blob tylko wtedy, gdy zostanie zastąpiony przez blob tego samego typu (bloku, Dołącz lub strony). Zastępuje obiektu blob innego typu, wszystkie istniejące dane usunięto nietrwałego zostanie trwale wygasł. |
| [Umieść bloku](/rest/api/storageservices/put-block)                                  | Obiekty BLOB typu Block                   | Tworzy nowy blok można przesłać blokowych obiektów blob.                                                | Jeśli używane można przekazać do obiektu blob, który jest aktywny w bloku nie została zmieniona. Jeśli używana, można przekazać bloku blob, w którym jest delikatny usunięty, jest tworzony nowy obiekt blob i automatycznie wygenerowana migawka do przechwytywania stanu nietrwałego usuniętego obiektu blob.                                                                                                                      |
| [Umieść zablokowanych](/rest/api/storageservices/fileservices/put-block-list)           | Obiekty BLOB typu Block                   | Zatwierdza obiektu blob, określając zbiór bloku identyfikatorów wchodzące w skład blokowych obiektów blob.                             | Jeśli używany do zastępowania istniejącego obiektu blob, automatycznie wygenerowana Migawka stanu obiektu blob przed wywołaniem. Dotyczy to również nietrwałego wcześniej usuniętych obiektów blob tylko wtedy, gdy jest blokowych obiektów Blob. Zastępuje obiektu blob innego typu, wszystkie istniejące dane usunięto nietrwałego zostanie trwale wygasł.                                                |
| [Umieść stronę](/rest/api/storageservices/fileservices/put-page)                       | Page Blobs                    | Zapisuje zakres stron stronicowych obiektów Blob.                                                                     | Brak zmian. Danych stronicowych obiektów Blob jest zastępowany lub wyczyszczone, ta operacja nie jest zapisywana i nie jest możliwe do odzyskania.                                                                                                                                                                                                                                                   |
| [Dołącz bloku](/rest/api/storageservices/fileservices/append-block)               | Uzupełnialnych obiektów blob                  | Zapisuje bloku danych w celu dołączenia obiektu blob                                                         | Brak zmian.                                                                                                                                                                                                                                                                                                                                                           |
| [Ustaw właściwości obiektów Blob](/rest/api/storageservices/fileservices/set-blob-properties) | Dołącz bloku i stronicowe obiekty BLOB | Ustawia wartości właściwości systemu zdefiniowane dla obiektu blob.                                                       | Brak zmian. Właściwości zastąpione obiektu blob nie są możliwe do odzyskania.                                                                                                                                                                                                                                                                                                          |
| [Ustaw metadane obiektu Blob](/rest/api/storageservices/fileservices/set-blob-metadata)     | Dołącz bloku i stronicowe obiekty BLOB | Ustawia metadane zdefiniowane przez użytkownika dla określonego obiektu blob jako pary nazwa wartość co najmniej jeden.                          | Brak zmian. Metadane obiektu blob zastąpione nie jest możliwe do odzyskania.                                                                                                                                                                                                                                                                                                             |

Należy zauważyć, że wywołanie "Put Page", aby zastąpić, lub wyczyść zakresy stronicowych obiektów Blob nie będą automatycznie generowane migawki. Dyski maszyny wirtualnej bazują na stronicowych obiektów blob, użyj **umieścić strony** zapisu danych.

### <a name="recovery"></a>Odzyskiwanie

Aby ułatwić odzyskać usunięte dane mają wprowadzono nowy interfejs API "Cofanie usuwania obiektów Blob". Wywoływanie interfejsu API cofanie usuwania nietrwałego usuniętego obiektu blob podstawowej przywracania go i wszystkie skojarzone nietrwałego usunięto migawki jako aktywne. Wywołanie interfejsu API cofanie usuwania na active podstawowego obiektu blob przywraca wszystkie skojarzone nietrwałego usunięto migawki jako aktywne. Gdy migawki są przywracane jako aktywny, wyglądają migawek generowanych przez użytkownika; Nie zastępuj podstawowego obiektu blob.

Można przywrócić obiektu blob do określonych nietrwałego migawki usunięto można wywołać **Blob cofanie usuwania** na podstawowy obiektu blob. Następnie można skopiować migawki za pośrednictwem obiektu blob teraz aktywny. Migawki można również skopiować do nowego obiektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Elastyczne usunięte dane jest szary, zablokowaniu niebieski aktywnych danych. Więcej ostatnio zapisywane dane są wyświetlane poniżej starszych danych. W tym miejscu **Blob cofanie usuwania** jest wywoływana dla obiektu blob B, a tym samym przywracanie podstawowego obiektu blob, B1 i wszystkie skojarzone migawki, w tym miejscu po prostu B0 jako aktywne. W drugim kroku B0 jest kopiowana za pośrednictwem podstawowego obiektu blob. Ta operacja kopiowania generuje nietrwałego Usunięto migawkę B1.\*

Aby wyświetlić nietrwałego usuniętych obiektów blob i migawki obiektu blob, możesz dołączyć usunięte dane w **listę obiektów blob**. Można wybrać, aby wyświetlić tylko nietrwałego usunięto podstawowej obiektów blob lub obejmują również migawki nietrwałego usuniętego obiektu blob. Dla wszystkich danych usuniętych miękkie możesz wyświetlić czas usunięcia danych oraz liczbę dni, zanim dane zostaną trwale wygasł.

### <a name="example"></a>Przykład

Poniżej przedstawiono dane wyjściowe konsoli skrypt .NET, który zostanie przesłany, zastępuje, migawek, usuwa i przywraca usunąć obiektu blob o nazwie "HelloWorld", gdy nietrwałego jest włączona:

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

Zobacz [następne kroki](#Next steps) sekcji dla wskaźnika do aplikacji, której te dane wyjściowe.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie usunięte dane nietrwałego jest on rozliczany na tym samym poziomie jak aktywnych danych. Nie zostanie obciążona dla danych, które są trwale usuwane po upływie okresu przechowywania skonfigurowane. Aby uzyskać bardziej zgłębić temat do migawki i sposobu ich naliczania opłat, zobacz [zrozumienie sposobu migawki naliczania opłat](storage-blob-snapshots.md).

Nie będą naliczane za transakcje związane z automatyczne generowanie migawek. Opłaty będą naliczane dla **Blob cofanie usuwania** transakcji szybkością "Operacje zapisu".

Aby uzyskać więcej informacji dotyczących cen magazynu obiektów Blob Azure ogólnie, zapoznaj się [stronie cennika usługi magazynu obiektów Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Włączenie początkowo usuwania nietrwałego, firma Microsoft zaleca używanie okres przechowywania małych, aby lepiej zrozumieć wpływ funkcji na rachunku.

## <a name="quickstart"></a>Szybki start

### <a name="azure-portal"></a>Azure Portal
Aby włączyć usuwania nietrwałego, przejdź do **usuwania nietrwałego** opcję w obszarze **usługa Blob**. Następnie kliknij przycisk **włączone** i wprowadź liczbę dni, aby zachować nietrwałego usunięte dane.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Aby wyświetlić nietrwałego usuniętych obiektów blob, wybierz **Pokaż usunięte obiekty BLOB** wyboru.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Aby wyświetlić nietrwałego usunięto migawek dla danego obiektu blob, wybierz obiekt blob, następnie kliknij przycisk **wyświetlić migawki**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Upewnij się, że **Pokaż usunięte migawki** pole wyboru jest zaznaczone.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Po kliknięciu nietrwałego usuniętych obiektów blob lub migawki, zauważyć nowe właściwości obiektu blob. Wskazują, gdy obiekt został usunięty, a liczby dni pozostałych do obiektów blob lub migawki obiektu blob trwale wygasł. Jeśli nietrwałego usuniętego obiektu nie jest migawką, będą także mieć opcję go przywrócić.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Należy pamiętać, że cofanie usunięcia obiektu blob będzie również cofanie usuwania wszystkie skojarzone migawki. Aby cofanie usuwania nietrwałego usunięto migawek dla obiekt blob active, kliknij na obiekt blob i wybierz **cofanie usuwania wszystkich migawek**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po undelete migawki obiektu blob, możesz kliknąć **Podnieś poziom** kopiować migawki obiektu blob głównego, a tym samym Przywracanie do migawki obiektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
Aby włączyć usuwania nietrwałego, należy zaktualizować klienta obiektów blob usługi właściwości. Poniższy przykład umożliwia włączenie usuwania nietrwałego dla podzbioru kont w ramach subskrypcji:

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Aby odzyskać obiektów blob, które zostały przypadkowo usunięte, należy wywołać Cofnij usunięcie dla tych obiektów blob. Należy pamiętać, że wywołania **Blob cofanie usuwania**, zarówno na aktywne i elastyczne usuniętych obiektów blob, spowoduje przywrócenie nietrwałego wszystkie skojarzone migawki usuniętych jako aktywne. Poniższy przykład wywołuje Cofnij usunięcie dla wszystkich nietrwałego usunięte i aktywne obiektów blob w kontenerze:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="python-client-library"></a>Biblioteka klienta języka Python

Aby włączyć usuwania nietrwałego, zaktualizuj właściwości usługi klienta obiektów blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Biblioteka klienta .net

Aby włączyć usuwania nietrwałego, zaktualizuj właściwości usługi klienta obiektów blob:

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Aby odzyskać obiektów blob, które zostały przypadkowo usunięte, należy wywołać Cofnij usunięcie dla tych obiektów blob.
Należy pamiętać, że wywołania **Blob cofanie usuwania**, zarówno na aktywne i elastyczne usuniętych obiektów blob, spowoduje przywrócenie nietrwałego wszystkie skojarzone migawki usuniętych jako aktywne. Poniższy przykład wywołuje Cofnij usunięcie dla wszystkich nietrwałego usunięte i aktywne obiektów blob w kontenerze:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Odzyskiwanie do wersji konkretnego obiektu blob, najpierw wywołać Cofnij usunięcie obiektu blob, a następnie skopiować żądanej migawki obiektu blob. Poniższy przykład odzyskuje blokowych obiektów blob do jego najnowszego wygenerowanego migawki:

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

## <a name="should-i-use-soft-delete"></a>Należy użyć usuwania nietrwałego?

Jeśli istnieje ryzyko, że dane jest przypadkowo zmodyfikowany lub usunięty przez aplikację lub innego użytkownika z konta magazynu, zaleca się włączenie usuwania nietrwałego.
Usuwania nietrwałego jest częścią strategii ochrony danych i ułatwia zapobieganie przypadkowej utracie.

## <a name="faq"></a>Często zadawane pytania

**Dla jakich typów pamięci masowej można używać usuwania nietrwałego?**

Obecnie usuwania nietrwałego jest dostępna tylko dla magazynu obiektów blob (obiekt).

**Usuwania nietrwałego jest dostępna dla wszystkich typów kont magazynu?**

Tak, usuwania nietrwałego jest dostępny dla konta magazynu obiektów blob oraz jak w przypadku obiektów blob na kontach magazynu ogólnego przeznaczenia. Dotyczy to zarówno warstwy standardowa i premium kont. Usuwania nietrwałego nie jest dostępne dla zarządzanych dysków.

**Jest dostępna dla wszystkich warstw magazynowania usuwania nietrwałego?**

Tak, usuwania nietrwałego jest dostępna dla wszystkich warstw magazynowania, łącznie z gorącego i chłodnego i archiwum. Jednak nie dają usuwania nietrwałego zastąpić ochronę obiekty BLOB w warstwie archiwum.

**Konta Premium magazynu ma limitu migawki obiektu blob 100. Czy nietrwałego migawki usunięto wliczane ten limit?**

Nie, wstępnie usunięty migawki nie są wliczane do tego limitu.

**Można włączyć usuwania nietrwałego dla istniejących kont magazynu?**

Tak, można skonfigurować zarówno istniejących i nowych kont magazynu jest usuwania nietrwałego.

**Jeśli z usuwania nietrwałego włączona usunąć całą konta lub kontenera, wszystkie skojarzone obiekty BLOB są zapisywane?**

Nie, usunięcie całego konta lub kontenera, wszystkie skojarzone obiekty BLOB zostaną trwale usunięte. Aby dowiedzieć się, jak chronić konto magazynu z przypadkowemu usuwaniu, zobacz artykuł usługi Azure Resource Manager [blokady zasobów, aby zapobiec nieoczekiwane](../../azure-resource-manager/resource-group-lock-resources.md).

**Czy można wyświetlić metryki pojemności usunięte dane?**

Elastyczne usunięte dane jest uwzględniana jako część pojemności konta całkowita ilość miejsca. Aby uzyskać więcej informacji na śledzenie i monitorowanie pojemności, zobacz [analityka magazynu](../common/storage-analytics.md) artykułu.

**Jeśli wyłączyć usuwania nietrwałego I nadal będzie możliwość dostępu nietrwałego usunięte dane?**

Tak, nadal będzie mogła uzyskać dostęp i odzyskać niewygasłe nietrwałego usunięte dane po wyłączeniu usuwania nietrwałego.

**Można I do odczytu i skopiowania nietrwałego migawki usunięto Moje obiektu blob**

Tak, ale należy najpierw wywołać Cofnij usunięcie w obiekcie blob.

**Jest dostępna usuwania nietrwałego, dla wszystkich typów obiektu blob?**

Tak, usuwania nietrwałego jest dostępna dla blokowych obiektów blob, Dołącz obiektów blob i stronicowe obiekty BLOB.

**Usuwania nietrwałego jest dostępna dla dysków maszyny wirtualnej?**

Usuwania nietrwałego jest dostępna zarówno premium i standardowa niezarządzane dysków. Usuwania nietrwałego tylko pomoże Ci odzyskania danych usunięte przez **usunąć obiektu Blob**, **umieszczanie obiektu Blob**, **Put zablokowanych**, **Put bloku** i **Kopiowanie obiektu Blob**. Dane zastąpione przez wywołanie do **umieścić strony** nie jest możliwe do odzyskania.

**Czy muszę zmienić swoje istniejące aplikacje, aby użyć usuwania nietrwałego?**

Jest to możliwe, należy korzystać z usuwania nietrwałego, niezależnie od używanej wersji interfejsu API. Jednak do listy i odzyskiwania nietrwałego usuniętych obiektów blob i migawki obiektu blob, konieczne będzie używana wersja 2017-07-29 [interfejsu API REST usług Storage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) lub nowszej. Ogólnie rzecz biorąc zawsze zalecamy używanie najnowszej wersji niezależnie od tego, czy używasz tej funkcji.

## <a name="next-steps"></a>Kolejne kroki

* [.NET przykładowy kod](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [Interfejs API REST usługi blob](/rest/api/storageservices/fileservices/blob-service-rest-api)

* [Replikacja usługi Azure Storage](../common/storage-redundancy.md)

* [Projektowanie wysokiej dostępności aplikacji przy użyciu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

* [Co robić w przypadku wystąpienia awarii usługi Azure Storage](../common/storage-disaster-recovery-guidance.md)
