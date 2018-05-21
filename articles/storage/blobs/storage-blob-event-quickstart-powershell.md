---
title: Trasy do punktu końcowego sieci web niestandardowego - Powershell zdarzenia magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 9ea51f6ea55c62fdd01efb155d26fade3941ce41
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Zdarzenia magazynu obiektów Blob trasy do punktu końcowego niestandardowe sieci web przy użyciu programu PowerShell

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule subskrybować zdarzenia magazynu obiektów Blob, wyzwalacz zdarzenia przy użyciu programu Azure PowerShell i wyświetlenia wyników. 

Zazwyczaj wysyła się zdarzenia do punktu końcowego, który na nie reaguje, takiego jak element webhook lub funkcja platformy Azure. Aby uprościć przykładzie przedstawionym w tym artykule, zdarzenia są wysyłane na adres URL, który tylko zbiera wiadomości. Ten adres URL jest tworzony w narzędziu innej firmy przy użyciu narzędzia [Hookbin](https://hookbin.com/).

> [!NOTE]
> **Hookbin** nie jest przeznaczony do użycia z wysokiej przepływności. Zastosowanie tego narzędzia ma charakter czysto poglądowy. W przypadku wypchnięcia więcej niż jednego zdarzenia w tym samym czasie w narzędziu mogą nie być widoczne wszystkie zdarzenia. Ponadto należy pamiętać, że **Hookbin** pobiera [szczególnego traktowania](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-grid#create-a-requestbin-endpoint) Azure zdarzeń siatki. Aby ułatwić testowanie, siatki zdarzeń wysyła zdarzenia bez konieczności poprawne odpowiedzi na żądania weryfikacji subskrypcji (się stanie, który [w przeciwnym razie](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication#validation-details)).

Po wykonaniu czynności opisanych w tym artykule dane powinny zostać wysłane do punktu końcowego.

## <a name="setup"></a>Konfiguracja

Ten artykuł wymaga używania najnowszej wersji programu Azure PowerShell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount`, a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> Dostępność dla magazynu zdarzenia jest powiązany z siatki zdarzeń [dostępności](../../event-grid/overview.md) i będą dostępne w różnych regionach, tak jak w przypadku zdarzeń siatki.

W tym przykładzie użyto **westus2** i przechowuje zaznaczenie w zmiennej do wykorzystania w całej.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów na pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie **gridResourceGroup** w lokalizacji **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby używać zdarzenia magazynu obiektów Blob, należy albo [kontem magazynu obiektów Blob](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) lub [konta magazynu ogólnego przeznaczenia v2](../common/storage-account-options.md#general-purpose-v2). **Ogólnego przeznaczenia v2 (GPv2)** są konta magazynu, które obsługują wszystkie funkcje dla wszystkich usług magazynu, w tym obiekty BLOB, plików, kolejek i tabel. A **kontem magazynu obiektów Blob** to specjalne konto magazynu do przechowywania danych bez struktury jako obiekty BLOB (obiekty) w usłudze Azure Storage. Konta magazynu obiektów blob są podobne do kont magazynu ogólnego przeznaczenia i udostępniać wszystkie trwałości, dostępności, skalowalności i wydajności zalety że używane obecnie, łącznie z 100% spójnością interfejsu API dla blokowych obiektów blob i uzupełnialnych obiektów blob. W przypadku aplikacji wymagających tylko magazynu obiektów blokowych lub uzupełnialnych obiektów blob zalecamy używanie kont usługi Blob Storage.  

Utwórz konto magazynu obiektów Blob przy użyciu replikacji LRS [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), następnie pobrać kontekst konta magazynu, który definiuje konto magazynu do użycia. Wykonując działania względem konta magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia. W tym przykładzie tworzy konto magazynu o nazwie **gridstorage** z magazyn lokalnie nadmiarowy (LRS). 

> [!NOTE]
> Nazwy konta magazynu są w przestrzeni nazwy globalnych, więc należy dołączyć niektórych losowo wybranych znaków na nazwę podaną w tym skrypcie.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zamiast pisać kod w celu zareagowania na zdarzenie, utwórzmy punkt końcowy, który będzie zbierać komunikaty, aby można było je wyświetlić. Hookbin to narzędzie innego producenta, które umożliwia utworzenie punktu końcowego i wyświetlanie wysyłanych do niego żądań. Przejdź do narzędzia [Hookbin](https://hookbin.com/) i kliknij pozycję **Create New Endpoint** (Utwórz nowy punkt końcowy). Skopiuj adres URL bin i Zastąp `<bin URL>` w poniższym skrypcie.

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>Subskrypcja konta magazynu

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić. Poniższy przykład subskrybuje konta magazynu utworzone i przekazuje adres URL z Hookbin jako punkt końcowy powiadomienia o zdarzeniach. 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Wyzwalanie zdarzenia z usługi Blob Storage

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Najpierw utwórz kontener i obiektu. Następnie możemy przekazać obiekt do kontenera.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Przejdź do adresu URL punktu końcowego utworzonego wcześniej. Ewentualnie kliknij przycisk Refresh (Odśwież) w otwartej przeglądarce. Zobaczysz właśnie wysłane zdarzenie. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zamierzasz kontynuować pracę z tym kontem magazynu i tą subskrypcją zdarzeń, nie usuwaj zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

Wiesz już, jak tworzyć tematy i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co mogą Ci ułatwić zdarzenia usługi Blob Storage i usługa Event Grid:

- [Reagowanie na zdarzenia usługi Blob Storage](storage-blob-event-overview.md)
- [Event Grid — informacje](../../event-grid/overview.md)
