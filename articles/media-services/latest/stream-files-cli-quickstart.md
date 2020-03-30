---
title: Przesyłanie strumieniowe plików wideo za pomocą usługi Azure Media Services i interfejsu wiersza polecenia platformy Azure
description: Wykonaj kroki tego samouczka, aby utworzyć nowe konto usługi Azure Media Services, zakodować plik i przesłać go strumieniowo do programu Azure Media Player.
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
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 91259e10966173cb701b867f5b3ed362112beef3
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382787"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Samouczek: Kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe wideo — narzędzie interfejsu wiersza polecenia platformy Azure

W tym samouczku pokazano, jak łatwo kodować i przesyłać strumieniowo wideo w różnych przeglądarkach i urządzeniach przy użyciu usług Azure Media Services i interfejsu wiersza polecenia platformy Azure. Zawartość wejściową można określić przy użyciu adresów URL protokołu HTTPS lub SAS lub ścieżek do plików w magazynie obiektów blob platformy Azure.

Przykład w tym artykule koduje zawartość, która jest dostępna za pośrednictwem adresu URL HTTPS. Usługi Media Services w wersji 3 nie obsługują obecnie kodowania transferu fragmentaryzowanego za pośrednictwem adresów URL HTTPS.

Pod koniec tego samouczka będziesz mógł przesyłać strumieniowo wideo.  

![Odtwarzanie wideo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Aby można było szyfrować, analizować, analizować, zarządzać i przesyłać strumieniowo zawartość multimediów na platformie Azure, należy utworzyć konto usługi Media Services. To konto musi być skojarzone z co najmniej jednym kontem magazynu.

Konto usługi Media Services i wszystkie skojarzone konta magazynu muszą znajdować się w tej samej subskrypcji platformy Azure. Zaleca się używanie kont magazynu, które znajdują się w tym samym miejscu co konto usługi Media Services, aby ograniczyć opóźnienia i koszty transferu danych wychodzących.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

W tym przykładzie tworzymy ogólne przeznaczenie v2 standardowe konto LRS.

Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Podczas wybierania jednostki SKU dla `--sku Standard_RAGRS`produkcji należy rozważyć użycie programu , który zapewnia replikację geograficzną dla ciągłości biznesowej. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](/cli/azure/storage/account).

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Otrzymasz odpowiedź w ten sposób:

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

Następujące polecenie interfejsu wiersza polecenia platformy Azure uruchamia domyślny **punkt końcowy przesyłania strumieniowego**.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Otrzymasz odpowiedź w ten sposób:

```
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

Jeśli punkt końcowy przesyłania strumieniowego jest już uruchomiony, zostanie wyświetlony ten komunikat:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Tworzenie transformacji w celu adaptacyjnego kodowania szybkości transmisji bitów

Utwórz **transformację** w celu skonfigurowania typowych zadań związanych z kodowaniem lub analizowaniem wideo. W tym przykładzie wykonujemy adaptacyjne kodowanie szybkości transmisji bitów. Następnie przedstawiamy pracę w ramach transformacji, którą stworzyliśmy. Zadanie jest żądaniem usługi Media Services, aby zastosować transformację do danego wejścia zawartości wideo lub audio.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Otrzymasz odpowiedź w ten sposób:

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

Utwórz **zasób** wyjściowy do użycia jako dane wyjściowe zadania kodowania.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Otrzymasz odpowiedź w ten sposób:

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

## <a name="start-a-job-by-using-https-input"></a>Rozpoczynanie zadania przy użyciu danych wejściowych HTTPS

Podczas przesyłania zadań do przetwarzania filmów wideo, musisz poinformować media services, gdzie można znaleźć wejściowy film wideo. Jedną z opcji jest określenie adresu URL HTTPS jako danych wejściowych zadania, jak pokazano w tym przykładzie.

Po uruchomieniu polecenia `az ams job start` można ustawić etykietę dla danych wyjściowych zadania. Następnie można użyć etykiety, aby zidentyfikować, do czego służy zasób wyjściowy.

- Jeśli do etykiety zostanie przypisana wartość, ustaw '--output-assets' na "assetname=label".
- Jeśli nie przypiszesz wartości do etykiety, ustaw '--output-assets' na "assetname=".

  Zauważ, że do . `output-assets`

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Otrzymasz odpowiedź w ten sposób:

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

W ciągu pięciu minut sprawdź stan zadania. Powinno być "Gotowe". To nie jest zakończona, sprawdź ponownie w ciągu kilku minut. Po jego zakończeniu przejdź do następnego kroku i utwórz **lokalizator przesyłania strumieniowego**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Tworzenie lokalizatora przesyłania strumieniowego i uzyskanie ścieżki

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości wideo, które można odtwarzać. Aby to zrobić, najpierw utwórz lokalizator przesyłania strumieniowego. Następnie skompiluj adresy URL przesyłania strumieniowego, z których mogą korzystać klienci.

### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Otrzymasz odpowiedź w ten sposób:

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

### <a name="get-streaming-locator-paths"></a>Uzyskaj ścieżki lokalizatora przesyłania strumieniowego

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Otrzymasz odpowiedź w ten sposób:

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

Skopiuj ścieżkę transmisji strumieniowej na żywo HTTP (HLS). W tym przypadku jest `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`to .

## <a name="build-the-url"></a>Tworzenie adresu URL

### <a name="get-the-streaming-endpoint-host-name"></a>Pobierz nazwę hosta punktu końcowego przesyłania strumieniowego

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Skopiuj wartość `hostName`. W tym przypadku jest `amsaccount-usw22.streaming.media.azure.net`to .

### <a name="assemble-the-url"></a>Zmontuj adres URL

„https://” + &lt;wartość hostName&gt; + &lt;wartość ścieżki Hls&gt;

Oto przykład:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Testowanie odtwarzania przy użyciu programu Azure Media Player

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie HTTPS, upewnij się, że zaczynał on go od "https".

1. Otwórz przeglądarkę internetową [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)i przejdź do pliku .
2. W polu **ADRESU wklej** adres URL wbudowany w poprzednią sekcję. Adres URL można wkleić w formacie HLS, Dash lub Smooth. Program Azure Media Player automatycznie użyje odpowiedniego protokołu przesyłania strumieniowego do odtwarzania na urządzeniu.
3. Wybierz **pozycję Aktualizuj odtwarzacz**.

>[!NOTE]
>Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już żadnych zasobów w grupie zasobów, w tym kont usługi Media Services i magazynu utworzonych dla tego samouczka, usuń grupę zasobów.

Uruchom to polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Media Services](media-services-overview.md)
