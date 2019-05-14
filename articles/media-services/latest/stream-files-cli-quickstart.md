---
title: Stream plików wideo za pomocą wiersza polecenia platformy Azure i usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Wykonaj kroki tego samouczka, aby utworzyć nowe konto usługi Azure Media Services, Koduj plik i prześlij go strumieniowo do usługi Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: cce424b11cc4cd587c6e7c50bc8bdf988004a43a
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550197"
---
# <a name="tutorial-stream-video-files---cli"></a>Samouczek: Przesyłanie strumieniowe plików wideo — interfejs wiersza polecenia

W tym samouczku pokazano, jak łatwo kodowanie i przesyłanie strumieniowe filmów wideo na różnych przeglądarkach i urządzeniach, przy użyciu usługi Azure Media Services i wiersza polecenia platformy Azure. Zawartość danych wejściowych można określić przy użyciu protokołu HTTPS lub adresy URL sygnatury dostępu Współdzielonego lub ścieżki do plików w usłudze Azure Blob storage.

W przykładzie w tym artykule koduje zawartości, który należy udostępnić za pośrednictwem adresu URL HTTPS. Usługa Media Services v3 nie obsługuje obecnie transferu pakietowego kodowania za pośrednictwem adresy URL HTTPS.

Do końca tego samouczka będziesz mieć możliwość przesyłania strumieniowego wideo.  

![Odtwarzanie wideo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Zanim można szyfrowanie, kodowanie, analizowanie, zarządzanie i przesyłanie strumieniowe zawartości na platformie Azure, musisz utworzyć konto usługi Media Services. To konto musi być skojarzony z co najmniej jedno konto magazynu.

Konto usługi Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zaleca się użycie kont magazynu, które znajdują się w tym samym miejscu, co konto usługi Media Services, aby ograniczyć koszty wyjścia z opóźnieniami i danych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

W tym przykładzie firma Microsoft konto ogólnego przeznaczenia v2 Standard LRS.

Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Gdy jesteś pobrania jednostki SKU dla środowiska produkcyjnego, rozważ użycie `--sku Standard_RAGRS`, zapewniającą replikacji geograficznej dla ciągłości działania. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Pojawi się odpowiedź podobna:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Uruchamianie punktu końcowego przesyłania strumieniowego

Następujące polecenie z wiersza polecenia platformy Azure rozpoczyna się domyślnie **punkt końcowy przesyłania strumieniowego**.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Pojawi się odpowiedź podobna:

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Jeśli punkt końcowy przesyłania strumieniowego jest już uruchomiona, zostanie wyświetlony ten komunikat:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Utwórz przekształcenia do kodowania z adaptacyjną szybkością transmisji bitów

Utwórz **transformację** w celu skonfigurowania typowych zadań związanych z kodowaniem lub analizowaniem wideo. W tym przykładzie robimy kodowania z adaptacyjną szybkością transmisji bitów. Możemy przesłać zadanie w ramach transformacji, który utworzyliśmy. Zadanie to żądanie do usługi Media Services na przekształcenie, które dotyczą danego filmu wideo lub audio zawartości danych wejściowych.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Pojawi się odpowiedź podobna:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Tworzenie zasobu wyjściowego

Tworzenie danych wyjściowych **zasobów** o używaniu danych wyjściowych zadania kodowania.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Pojawi się odpowiedź podobna:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Uruchom zadanie przy użyciu protokołu HTTPS w danych wejściowych

Po przesłaniu zadania do przetwarzania plików wideo należy stwierdzić, gdzie można znaleźć wejściowego filmu wideo usługi Media Services. Jedną z opcji jest określenie adresu URL HTTPS jako dane wejściowe zadania, jak pokazano w poniższym przykładzie.

Po uruchomieniu polecenia `az ams job start` można ustawić etykietę dla danych wyjściowych zadania. Etykieta może następnie umożliwia zidentyfikowanie, co to jest elementu zawartości wyjściowej.

- Jeśli przypisujesz wartość do etykiety "--dane wyjściowe — zasoby do" assetname = label ".
- Jeśli wartość nie przypisuj do etykiety, ustaw "--dane wyjściowe — zasoby do" assetname = ".

  Należy zauważyć, że firma Microsoft dodać "=" do `output-assets`.

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Pojawi się odpowiedź podobna:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Sprawdzanie stanu

W ciągu pięciu minut Sprawdź stan zadania. Powinien być "zakończeniem." Nie zostało zakończone, sprawdź ponownie za kilka minut. Po zakończeniu, przejdź do następnego kroku i Utwórz **lokalizatora przesyłania strumieniowego**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Utwórz Lokalizator przesyłania strumieniowego i uzyskać ścieżki

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości wideo, które można odtwarzać. Aby to zrobić, najpierw należy utworzyć Lokalizator przesyłania strumieniowego. Następnie kompilacji adresów URL, których klienci mogą używać przesyłania strumieniowego.

### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Pojawi się odpowiedź podobna:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Rozpoczynanie przesyłania strumieniowego lokalizatora ścieżki

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Pojawi się odpowiedź podobna:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Skopiuj na żywo przesyłania strumieniowego (HLS) ścieżki HTTP. W tym przypadku ma `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>Tworzenie adresu URL 

### <a name="get-the-streaming-endpoint-host-name"></a>Pobierz nazwę hosta punktu końcowego przesyłania strumieniowego

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```
Skopiuj wartość `hostName`. W tym przypadku ma `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>Złóż adresu URL

„https://” + &lt;wartość hostName&gt; + &lt;wartość ścieżki Hls&gt;

Oto przykład:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Podczas odtwarzania testu za pomocą usługi Azure Media Player

> [!NOTE]
> Jeśli gracz znajduje się w witrynie HTTPS, upewnij się, można uruchomić do adresu URL "https".

1. Otwórz przeglądarkę internetową i przejdź do [ https://aka.ms/azuremediaplayer/ ](https://aka.ms/azuremediaplayer/).
2. W **adresu URL** pole, wklej adres URL, który utworzoną w poprzedniej sekcji. Możesz wkleić adres URL w formacie Smooth, Dash lub HLS. Usługa Azure Media Player automatycznie zastosuje odpowiednie protokołu przesyłania strumieniowego do odtwarzania na urządzeniu.
3. Wybierz **aktualizacja Player**.

>[!NOTE]
>Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów w grupie zasobów, w tym usługi Media Services i kont magazynu, utworzone na potrzeby tego samouczka, Usuń grupę zasobów.

Uruchom następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

[Przegląd usługi Media Services](media-services-overview.md)

