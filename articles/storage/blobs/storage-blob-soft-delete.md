---
title: Usuwanie nietrwałe dla obiektów blob usługi Azure Storage | Dokumentacja firmy Microsoft
description: Usługa Azure Storage oferuje teraz usuwania nietrwałego dla obiektów blob, tak, aby łatwiej można odzyskać dane, gdy jest błędnie zmodyfikowany lub usunięty przez aplikację lub innego użytkownika do konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 8c23e429966cf9a1e93ac46ea3ecd11744761872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148620"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Nietrwałe usuwanie obiektów blob usługi Azure Storage
Usługa Azure Storage oferuje teraz usuwania nietrwałego dla obiektów blob, tak, aby łatwiej można odzyskać dane, gdy jest błędnie zmodyfikowany lub usunięty przez aplikację lub innego użytkownika do konta magazynu.

## <a name="how-does-it-work"></a>Jak to działa?
Po jej włączeniu usuwania nietrwałego umożliwia zapisywanie i odzyskiwanie danych, usunięcie obiektów blob lub migawki obiektów blob. Ta ochrona dotyczy też do danych obiektów blob, które są usuwane w wyniku zastąpienia.

Po usunięciu danych przechodzi nietrwałego stanu usunięcia, a nie jest trwale usunięte. Podczas usuwania nietrwałego jest włączona, i zastąpić dane, nietrwałego Usunięto migawkę jest generowana do zapisywania stanu dane zastąpione. Nietrwale usuniętych obiektów są niewidoczne, chyba że wyraźnie wymienione. Możesz skonfigurować ilość czasu, przez który nietrwale usunięte dane są możliwe do odzyskania, zanim trwale wygasł.

Usuwanie nietrwałego jest zgodna z poprzednimi wersjami; nie trzeba wprowadzać zmian w aplikacjach, aby móc korzystać z ochrony, które zapewnia tę funkcję. Jednak [odzyskiwanie danych](#recovery) wprowadzono nowy **cofanie usunięcia obiektu Blob** interfejsu API.

### <a name="configuration-settings"></a>Ustawienia konfiguracji
Podczas tworzenia nowego konta usuwania nietrwałego jest domyślnie wyłączona. Usuwanie nietrwałego jest również domyślnie wyłączona dla istniejących kont magazynu. Funkcja włączać i wyłączać można przełączać się na każdym etapie cyklu życia na koncie magazynu.

Nadal będzie możliwe uzyskanie dostępu i odzyskanie nietrwale usunięte dane, gdy ta funkcja jest wyłączona, przy założeniu, że nietrwale usunięte dane została zapisana, gdy ta funkcja była wcześniej włączona. Po włączeniu usuwania nietrwałego musisz również skonfigurować okres przechowywania.

Okres przechowywania wskazuje ilość czasu, który nietrwale usunięte dane są przechowywane i dostępne do odzyskania. Dla obiektów blob i migawek obiektów blob, które są jawnie usuwane okres przechowywania zegara rozpoczyna się, gdy dane zostaną usunięte. Nietrwale usunięte migawki generowane za pomocą funkcji usuwania nietrwałego, gdy dane są zastępowane zegara rozpoczyna się po wygenerowaniu migawki. Obecnie można zachować nietrwale usunięte dane od 1 do 365 dni.

Możesz zmienić okres przechowywania usuwania nietrwałego w dowolnym momencie. Okres przechowywania zaktualizowanych będzie dotyczyć tylko nowo usuniętych danych. Wcześniej usuniętych danych wygaśnie oparte na okres przechowywania, który został skonfigurowany, usunięcie tych danych. Podjęto próbę usunięcia obiektu usuniętego nietrwale nie wpływa na czas jego wygaśnięcia.

### <a name="saving-deleted-data"></a>Zapisywanie usuniętych danych
Usuwanie nietrwałe zachowuje dane w wielu przypadkach, gdy obiekty BLOB lub migawki obiektów blob są usunięty lub zastąpiony.

Gdy obiekt blob jest zastępowany przy użyciu **umieszczanie obiektu Blob**, **umieszczania bloku**, **umieścić zablokowanych** lub **obiektu Blob kopiowania** migawkę stanu obiektu blob w programach starszych niż program zapis operacji jest generowany automatycznie. Ta migawka jest elastyczne Usunięto migawkę; może to być niewidoczne, chyba że jawnie należą nietrwale usuniętych obiektów. Zobacz [odzyskiwania](#recovery) sekcji, aby dowiedzieć się, jak wyświetlić listę nietrwale usuniętych obiektów.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Nietrwale usunięte dane jest szary, gdy aktywne dane znajdują się niebieskie. Więcej ostatnio zapisanych danych pojawia się poniżej starszych danych. Gdy B0 zostały zastąpione B1, generowany jest nietrwale usunięte migawki B0. Gdy B1 zostały zastąpione B2, generowany jest nietrwale usunięte migawki B1.*

> [!NOTE]  
> Usuwanie nietrwałe, tylko daje zastąpić ochrony dla operacji kopiowania, gdy jest włączona dla konta docelowego obiektu blob.

> [!NOTE]  
> Usuwanie nietrwałe nie umożliwia zastąpienie ochrony dla obiektów blob w warstwie archiwum. Jeśli obiekt blob w warstwie archiwum, jest zastępowany nowy obiekt blob w dowolnej warstwie, zastąpione blob trwale wygasł.

Podczas **usunięcia obiektu Blob** nosi nazwę migawkę, tej migawki jest oznaczony jako nietrwale usunięte. Nowa migawka nie jest generowany.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Nietrwale usunięte dane jest szary, gdy aktywne dane znajdują się niebieskie. Więcej ostatnio zapisanych danych pojawia się poniżej starszych danych. Gdy **wykonanie migawki obiektu Blob** jest wywoływana, staje się B0 migawki i B1 jest w stanie aktywnym obiektu blob. Po usunięciu migawki B0 jest oznaczony jako nietrwale usunięte.*

Podczas **usunięcia obiektu Blob** nosi nazwę podstawowego obiektu blob (dowolnego obiektu blob to znaczy sam nie migawki) tego obiektu blob jest oznaczony jako nietrwale usunięte. Zgodne z poprzednie zachowanie podczas wywoływania **usunięcia obiektu Blob** dla obiektu blob, który ma migawki aktywnego zwraca błąd. Wywoływanie **usunięcia obiektu Blob** na obiekt blob z nietrwale usunięte migawki nie zwróci błąd. Możesz nadal usunąć obiektu blob i jego migawek w jednej operacji podczas usuwania nietrwałego jest włączona. Ten sposób oznacza podstawowego obiektu blob i migawki jako nietrwale usunięte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Nietrwale usunięte dane jest szary, gdy aktywne dane znajdują się niebieskie. Więcej ostatnio zapisanych danych pojawia się poniżej starszych danych. W tym miejscu **usunięcia obiektu Blob** zostanie nawiązane połączenie, aby usunąć B2 i wszystkie skojarzone migawki. Aktywnych obiektów blob, B2 i wszystkie skojarzone migawki są oznaczone jako nietrwale usunięte.*

> [!NOTE]  
> W przypadku miękkiej usunięto obiekt blob zostanie zastąpiony, nietrwałe Usunięto migawkę obiektu blob stanu sprzed operacji zapisu jest generowany automatycznie. Nowy obiekt blob dziedziczy warstwę obiektu blob zastąpione.

Usuwania nietrwałego nie zapisać dane w przypadku kontenerów lub usuwa konta ani gdy metadane obiektu blob i właściwości obiektu blob zostaną zastąpione. Aby chronić konto magazynu, przed usunięciem błędne, można skonfigurować blokadę przy użyciu usługi Azure Resource Manager. Zobacz artykuł z usługi Azure Resource Manager [blokowanie zasobów, aby uniemożliwić nieoczekiwane zmiany](../../azure-resource-manager/resource-group-lock-resources.md) Aby dowiedzieć się więcej.

W poniższej tabeli przedstawiono oczekiwane zachowanie podczas usuwania nietrwałego jest włączona:

| Operacja interfejsu API REST | Typ zasobu | Opis | Zmiana zachowania |
|--------------------|---------------|-------------|--------------------|
| [Usuwanie](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Usuwa konto magazynu, w tym wszystkich kontenerów i obiektów blob, które zawiera.                           | Bez zmian. Kontenery i obiekty BLOB w ramach usuniętego konta nie są możliwe do odzyskania. |
| [Usuwanie kontenera](/rest/api/storageservices/delete-container) | Kontener | Usuwa kontener, w tym wszystkie obiekty BLOB, które zawiera. | Bez zmian. Obiekty BLOB w kontenerze usuniętych nie są możliwe do odzyskania. |
| [Put Blob](/rest/api/storageservices/put-blob) | Dołącz bloku, i stronicowe obiekty BLOB | Tworzy nowy obiekt blob lub zastępuje istniejący obiekt blob w kontenerze | Jeśli używane, aby zastąpić istniejący obiekt blob, migawkę obiektu blob stanu przed wywołaniem jest generowany automatycznie. Dotyczy to również słabe wcześniej usunięto obiekt blob. tylko wtedy, gdy jest zastępowany przez obiekt blob z tego samego typu (blokowy, Dołącz lub stronicowy). Zastępuje obiekt blob innego typu, wszystkie istniejące nietrwale usunięte dane zostaną trwale wygasł. |
| [Usuwanie obiektu Blob](/rest/api/storageservices/delete-blob) | Dołącz bloku, i stronicowe obiekty BLOB | Oznacza obiekt blob lub migawki obiektu blob do usunięcia. Obiekt blob lub migawki, później zostanie usunięty podczas wyrzucania elementów bezużytecznych | Jeśli używany do usunięciu migawki obiektu blob, tej migawki jest oznaczony jako nietrwale usunięte. Jeśli używany, aby usunąć obiekt blob, tego obiektu blob jest oznaczony jako nietrwale usunięte. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Dołącz bloku, i stronicowe obiekty BLOB | Kopiuje obiekt blob źródła do docelowego obiektu blob w tym samym koncie magazynu lub innego konta magazynu. | Jeśli używane, aby zastąpić istniejący obiekt blob, migawkę obiektu blob stanu przed wywołaniem jest generowany automatycznie. Dotyczy to również słabe wcześniej usunięto obiekt blob. tylko wtedy, gdy jest zastępowany przez obiekt blob z tego samego typu (blokowy, Dołącz lub stronicowy). Zastępuje obiekt blob innego typu, wszystkie istniejące nietrwale usunięte dane zostaną trwale wygasł. |
| [Umieść bloku](/rest/api/storageservices/put-block) | Obiekty BLOB typu Block | Tworzy nowy blok do można zatwierdzić blokowych obiektów blob. | Jeśli użyte do zatwierdzania bloku do obiektu blob, który jest aktywny nie nastąpiła żadna zmiana. Jeśli używane, aby zatwierdzić bloku do obiektu blob, który jest nietrwale usunięte, zostanie utworzony nowy obiekt blob i automatycznie wygenerowana migawka do przechwytywania stanu nietrwale usuniętych obiektów blob. |
| [Umieść zablokowanych](/rest/api/storageservices/put-block-list) | Obiekty BLOB typu Block | Zatwierdza obiektu blob, określając zbiór bloku identyfikatorów, wchodzące w skład blokowych obiektów blob. | Jeśli używane, aby zastąpić istniejący obiekt blob, migawkę obiektu blob stanu przed wywołaniem jest generowany automatycznie. Dotyczy to również słabe wcześniej usunięto obiekt blob. tylko wtedy, gdy jest blokowy obiekt Blob. Zastępuje obiekt blob innego typu, wszystkie istniejące nietrwale usunięte dane zostaną trwale wygasł. |
| [Umieść strony](/rest/api/storageservices/put-page) | Page Blobs | Zapisuje zakres stron do stronicowych obiektów Blob. | Bez zmian. Danych stronicowy obiekt Blob zostanie zastąpiony lub wyczyszczone, ta operacja nie jest zapisywana i nie jest możliwe do odzyskania. |
| [Dołącz bloku](/rest/api/storageservices/append-block) | Uzupełnialne obiekty BLOB | Zapisuje bloku danych na końcu obiektu Blob dołączania | Bez zmian. |
| [Ustaw właściwości obiektu Blob](/rest/api/storageservices/set-blob-properties) | Dołącz bloku, i stronicowe obiekty BLOB | Ustawia wartości właściwości systemu zdefiniowane dla obiektu blob. | Bez zmian. Właściwości zastąpione obiektu blob nie są możliwe do odzyskania. |
| [Ustaw metadane obiektu Blob](/rest/api/storageservices/set-blob-metadata) | Dołącz bloku, i stronicowe obiekty BLOB | Ustawia metadanych zdefiniowanych przez użytkownika dla określonego obiektu blob jako jedną lub więcej par nazwa wartość. | Bez zmian. Metadane zastąpione obiektu blob nie jest możliwe do odzyskania. |

Należy zauważyć, że wywołanie "Put Page", aby zastąpić, lub usuń zaznaczenie zakresów stronicowych obiektów Blob nie automatycznie wygeneruje migawki. Dyski maszyny wirtualnej są objęte stronicowe obiekty BLOB i użyj **umieścić strony** do zapisywania danych.

### <a name="recovery"></a>Odzyskiwanie
Aby ułatwić odzyskanie danych usuniętych, wprowadziliśmy nowy interfejs API "Cofanie usunięcia obiektu Blob". Wywołanie interfejsu API usuniętych w nietrwale usunięte podstawowego obiektu blob przywraca je, a wszystkie skojarzone nietrwale usunięte migawki jako aktywny. Wywołanie interfejsu API usuniętych active podstawowego obiektu blob przywraca wszystkie skojarzone nietrwale usunięte migawki jako aktywny. Gdy migawki są przywracane jako aktywny, wyglądają jak migawek generowanych przez użytkowników; Nie zastępuj podstawowego obiektu blob.

Można przywrócić obiektu blob określoną migawkę usunięte nietrwale może wywołać **cofanie usunięcia obiektu Blob** podstawowego obiektu blob. Następnie można skopiować migawki obiektu blob teraz aktywny. Migawki można także skopiować do nowego obiektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Nietrwale usunięte dane jest szary, gdy aktywne dane znajdują się niebieskie. Więcej ostatnio zapisanych danych pojawia się poniżej starszych danych. W tym miejscu **cofanie usunięcia obiektu Blob** jest wywoływana dla obiektu blob B, a tym samym przywracania podstawowego obiektu blob, B1 i wszystkie skojarzone migawki, w tym miejscu po prostu B0 jako aktywny. W drugim kroku B0 jest kopiowana za pośrednictwem podstawowych obiektów blob. Ta operacja kopiowania generuje nietrwale usunięte migawki B1.*

Aby wyświetlić nietrwale usunięte obiekty BLOB i migawek obiektów blob, użytkownik może uwzględnić usunięte dane w **wyświetlanie listy obiektów blob**. Można wybrać, aby wyświetlić tylko nietrwale usunięte podstawowy obiekty BLOB lub w celu dołączenia także migawek nietrwale usuniętych obiektów blob. Wszystkie nietrwale usunięte dane można wyświetlić czas usunięcia danych oraz liczbę dni, zanim dane zostaną trwale wygasł.

### <a name="example"></a>Przykład
Oto dane wyjściowe konsoli skryptu .NET, która przekazuje, zastępuje, migawki, usuwa i przywracanie, usuwanie obiektu blob o nazwie "nazwę HelloWorld", gdy nietrwałego jest włączona:

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

Zobacz [następne kroki](#next-steps) sekcji dla wskaźnika do aplikacji, które generowane dane wyjściowe.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Wszystkie nietrwale usunięte dane jest naliczana przy użyciu stawki stosowanej jako aktywnych danych. Nie będzie naliczana dla danych, które zostaną trwale usunięte po upływie okresu przechowywania skonfigurowane. Bardziej zgłębić temat do migawki i jak są naliczane opłaty, zobacz [zrozumienie sposobu migawek naliczania opłat za](storage-blob-snapshots.md).

Użytkownik nie jest naliczana za transakcje związane z automatyczne generowanie migawek. Opłata zostanie naliczona za **cofanie usunięcia obiektu Blob** transakcje zgodnie ze stawką "Operacje zapisu".

Aby uzyskać szczegółowe informacje na temat cen usługi Azure Blob Storage ogólnie rzecz biorąc, zapoznaj się [stronie cennika usługi Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Włączenie początkowo usuwania nietrwałego, zaleca się przy użyciu okresu przechowywania małe, aby lepiej zrozumieć, jak funkcja wpłynie na rachunku.

## <a name="quickstart"></a>Szybki start
### <a name="azure-portal"></a>Azure Portal
Aby włączyć usuwanie nietrwałe, przejdź do **usuwania nietrwałego** opcji w obszarze **usługę Blob Service**. Następnie kliknij przycisk **włączone** i wprowadź liczbę dni, aby zachować nietrwale usunięte dane.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Zaznacz, aby wyświetlić nietrwale usunięte obiekty BLOB **Pokaż usunięte obiekty BLOB** pola wyboru.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Aby wyświetlić nietrwale usunięte migawki dla danego obiektu blob, wybierz obiekt blob, a następnie kliknij przycisk **Wyświetl migawki**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Upewnij się, że **Pokaż usunięte migawki** pole wyboru jest zaznaczone.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Po kliknięciu nietrwale usuniętych obiektów blob lub migawki, zwróć uwagę, nowe właściwości obiektu blob. Wskazują, kiedy obiekt został usunięty i ile dni pozostało do obiektu blob lub migawki obiektu blob trwale wygasł. Jeśli nietrwałego usuniętego obiektu nie jest migawką, również masz możliwość cofnięcia usunięcia go.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Należy pamiętać, że cofanie usunięcia obiektu blob będzie również Cofnij usunięcie wszystkich skojarzonych migawek. Można cofnąć usunięcia nietrwale usunięte migawki dla aktywnego obiektu blob, kliknij obiekt blob, a następnie wybierz pozycję **Cofnij usunięcie wszystkich migawek**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po można cofnąć usunięcia migawek obiektu blob, możesz kliknąć **Podwyższ poziom** można skopiować migawki obiektu blob główny, w tym samym przywracania migawki obiektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektu blob. Poniższy przykład umożliwia usuwania nietrwałego dla podzbioru kont w ramach subskrypcji:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Możesz sprawdzić, czy tego usuwania nietrwałego została włączona przy użyciu następującego polecenia:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Aby odzyskać przypadkowo usunięte obiekty BLOB, należy wywołać Cofnij usunięcie tych obiektów blob. Należy pamiętać, że wywołanie **cofanie usunięcia obiektu Blob**, zarówno dla aktywnych i nietrwałe usuniętych obiektów blob, spowoduje przywrócenie wszystkich powiązanych z nim nietrwale usunięte migawki jako aktywny. Poniższy przykład wywołuje Cofnij usunięcie wszystkich nietrwale usunięte i aktywnych obiektów blob w kontenerze:
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

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure 
Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Aby sprawdzić, nietrwałe usuwanie jest włączona, użyj następującego polecenia: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Biblioteka klientów języka Python
Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Biblioteki klienta platformy .NET
Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Aby odzyskać przypadkowo usunięte obiekty BLOB, należy wywołać Cofnij usunięcie tych obiektów blob. Należy pamiętać, że wywołanie **cofanie usunięcia obiektu Blob**, zarówno dla aktywnych i nietrwałe usuniętych obiektów blob, spowoduje przywrócenie wszystkich powiązanych z nim nietrwale usunięte migawki jako aktywny. Poniższy przykład wywołuje Cofnij usunięcie wszystkich nietrwale usunięte i aktywnych obiektów blob w kontenerze:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Aby odzyskać do wersji konkretny obiekt blob, najpierw wywołać cofanie usunięcia obiektu blob, a następnie skopiuj żądanej migawki obiektu blob. Poniższy przykład odzyskuje blokowych obiektów blob do jego najnowszego wygenerowanego migawki:

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

## <a name="should-i-use-soft-delete"></a>Należy używać usuwania nietrwałego?
Jeśli istnieje ryzyko, że danych przypadkowo zmienione lub usunięte przez aplikację lub innemu użytkownikowi konta magazynu, zaleca się włączenie usuwania nietrwałego. Usuwanie nietrwałego jest częścią strategii ochrony danych i może pomóc w uniknięciu utraty danych przypadkowego.

## <a name="faq"></a>Często zadawane pytania
**Czy istnieją wszystkie uwagi dotyczące Korzystanie z miękkiego usuwania?**  
Włączanie usuwania nietrwałego dla często zastępowane danych może spowodować naliczenie opłaty za pojemność zasobów pamięci masowej i większe opóźnienia, podczas wyświetlania listy obiektów blob. Można temu zaradzić, przechowując dane często zastąpione w oddzielne konto magazynu przy użyciu usuwania nietrwałego wyłączone. 

**Dla jakich typów pamięci masowej można używać usuwania nietrwałego?**  
Obecnie usuwania nietrwałego jest dostępna tylko dla magazynu obiektów blob (obiekt).

**Usuwanie nietrwałego jest dostępna dla wszystkich typów kont magazynu?**  
Tak, usuwania nietrwałego jest dostępna dla kont usługi blob storage oraz jak obiekty BLOB w ogólnego przeznaczenia w przypadku kont magazynu (GPv1 i GPv2). Dotyczy to kont w warstwach standardowa i premium. Usuwanie nietrwałe nie jest dostępna dla dysków zarządzanych.

**Usuwanie nietrwałego jest dostępna dla wszystkich warstw magazynowania?**  
Tak, usuwania nietrwałego jest dostępna dla wszystkich warstw magazynowania, włącznie z warstw gorąca, chłodna i archiwum. Jednak usuwania nietrwałego nie umożliwia zastąpienie ochrony dla obiektów blob w warstwie archiwum.

**Warstwy obiektów blob za pomocą nietrwale usunięte migawki można używać interfejsu API Ustawianie warstwy obiektu Blob?**  
Tak. Nietrwale usunięte migawki pozostaną w warstwie oryginalnej, ale podstawowy obiekt blob zostanie przeniesione do nowej warstwy. 

**Kont usługi Premium storage mają limit migawek obiektów blob 100 na. Czy nietrwale usunięte migawki wliczają się do tego limitu?**  
Nie, wstępnie usunięty migawek nie są wliczane do tego limitu.

**Można włączyć usuwania nietrwałego dla istniejących kont magazynu?**  
Tak, usuwania nietrwałego jest konfigurowany dla nowych i istniejących kont magazynu.

**Jeśli usunę całe konto i kontener za pomocą usuwania nietrwałego, włączone, wszystkie skojarzone obiekty BLOB są zapisywane?**  
Nie, usunięcie całego konta lub kontenera, wszystkie skojarzone obiekty BLOB zostaną trwale usunięte. Aby dowiedzieć się, jak chronić konto magazynu z przypadkowemu usuwaniu, zobacz artykuł usługi Azure Resource Manager [blokowanie zasobów, aby uniemożliwić nieoczekiwane zmiany](../../azure-resource-manager/resource-group-lock-resources.md).

**Może wyświetlać metryki pojemności dla usuniętych danych?**  
Nietrwale usunięte dane jest dołączony jako część Twojego całkowita pojemność konta magazynu. Aby uzyskać więcej informacji na temat śledzenia i monitorowania pojemności magazynu, zobacz [Storage Analytics](../common/storage-analytics.md) artykułu.

**Wyłączenia usuwania nietrwałego I nadal będą mogli korzystać z nietrwale usunięte dane?**  
Tak, nadal będzie możliwe uzyskanie dostępu i odzyskanie niewygasłe nietrwale usunięte dane podczas usuwania nietrwałego została wyłączona.

**Czy mogę odczytywać i skopiowania nietrwale usunięte migawki obiektu blob Moje?**  
Tak, ale należy najpierw wywołać Cofnij usunięcie w obiekcie blob.

**Usuwanie nietrwałego jest dostępna, dla wszystkich typów magazynu blob?**  
Tak, usuwania nietrwałego jest dostępna dla blokowych obiektów blob i Uzupełnialnych obiektów blob, stronicowe obiekty BLOB.

**Usuwanie nietrwałego jest dostępna dla dysków maszyny wirtualnej?**  
Usuwanie nietrwałego jest dostępna dla dysków niezarządzanych w warstwie premium i standardowa. Usuwania nietrwałego tylko pomoże Ci odzyskać dane usunięte przez **usunięcia obiektu Blob**, **umieszczanie obiektu Blob**, **umieścić zablokowanych**, **umieszczania bloku** i **Kopiowanie obiektu Blob**. Dane zastąpione przez wywołanie **umieścić strony** nie jest możliwe do odzyskania.

**Czy muszę zmienić swoje istniejące aplikacje, aby używać usuwania nietrwałego?**  
Użytkownik może korzystać z zalet usuwania nietrwałego, niezależnie od wersji interfejsu API, którego używasz. Jednak aby wyświetlić listę i odzyskanie nietrwale usunięte obiekty BLOB i migawek obiektów blob, należy użyć wersji 2017-07-29 [interfejsu API REST usług Storage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) lub nowszej. Ogólnie rzecz biorąc zawsze zalecamy używanie najnowszej wersji niezależnie od tego, czy używasz tej funkcji.

## <a name="next-steps"></a>Kolejne kroki
* [Przykładowy kod .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Interfejs API REST usługi blob](/rest/api/storageservices/blob-service-rest-api)
* [Replikacja usługi Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Projektowanie wysoko dostępnych aplikacji przy użyciu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Awaryjnego odzyskiwania i przechowywania konta pracy awaryjnej (wersja zapoznawcza) w usłudze Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
