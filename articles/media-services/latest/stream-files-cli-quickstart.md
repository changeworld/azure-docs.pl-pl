---
title: Przesyłanie strumieniowe plików wideo za pomocą usługi Azure Media Services — interfejs wiersza polecenia | Microsoft Docs
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć nowe konto usługi Azure Media Services, zakodować plik i przesłać go strumieniowo do usługi Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: ''
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: 8de004b0ca55cb46336a072dabb682f342c7d8dd
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446498"
---
# <a name="quickstart-stream-video-files---cli"></a>Szybki start: Przesyłanie strumieniowe plików wideo — interfejs wiersza polecenia

Ten przewodnik Szybki start pokazuje, jak łatwo jest zakodować i rozpocząć strumieniowe przesyłanie wideo do różnych przeglądarek i urządzeń za pomocą usługi Azure Media Services. Zawartość wejściową można określić przy użyciu adresów URL HTTP, adresów URL SAS lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure.
W przykładzie w tym temacie kodowana jest zawartość, która jest udostępniana za pośrednictwem adresu URL protokołu HTTPS. Obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu przez adresy URL HTTPS.

Na końcu tego przewodnika Szybki start będziesz umieć przesyłać strumieniowo filmy wideo.  

![Odtwarzanie wideo](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services

Aby rozpocząć, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, a także zarządzanie nią, musisz utworzyć konto usługi Media Services. Konto usługi Media Services musi być skojarzone z co najmniej jednym kontem magazynu.

Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

W tym przykładzie utworzymy konto ogólnego przeznaczenia w wersji 2 magazynu LRS w warstwie Standardowa.

Jeśli chcesz poeksperymentować z kontami magazynu, użyj parametru `--sku Standard_LRS`. Jednak podczas wybierania jednostki SKU dla środowiska produkcyjnego weź pod uwagę użycie parametru `--sku Standard_RAGRS`, co zapewnia replikację geograficzną na potrzeby zapewnienia ciągłości działania. Aby uzyskać więcej informacji, zobacz temat [Konta magazynu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-service-account"></a>Tworzenie konta usługi Azure Media Services

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Uzyskasz odpowiedź podobną do następującej:

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

## <a name="start-streaming-endpoint"></a>Uruchamianie punktu końcowego przesyłania strumieniowego

Następujące polecenie interfejsu wiersza polecenia uruchamia domyślny **punkt końcowy przesyłania strumieniowego**.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Po jego uruchomieniu otrzymasz odpowiedź podobną do następującej:

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

Jeśli punkt końcowy przesyłania strumieniowego jest już uruchomiony, możesz uzyskać odpowiedź

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Tworzenie transformacji na potrzeby kodowania z adaptacyjną szybkością transmisji bitów

Utwórz **transformację** w celu skonfigurowania typowych zadań związanych z kodowaniem lub analizowaniem wideo. W tym przykładzie chcemy wykonać kodowanie z adaptacyjną szybkością transmisji bitów. Następnie prześlesz **zadanie** w ramach utworzonej transformacji. Zadanie to rzeczywiste żądanie skierowane do usługi Media Services mające na celu zastosowanie transformacji do określonej wejściowej zawartości wideo lub dźwiękowej.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Uzyskasz odpowiedź podobną do następującej:

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

Tworzenie wyjściowego **elementu zawartości** używanego jako dane wyjściowe zadania kodowania.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Uzyskasz odpowiedź podobną do następującej:

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

## <a name="start-job-with-https-input"></a>Uruchamianie zadania przy użyciu danych wejściowych protokołu HTTPS

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Jedną z możliwości jest określenie adresu URL protokołu HTTPS jako danych wejściowych zadania (jak pokazano w tym przykładzie). 

Po uruchomieniu polecenia `az ams job start` można ustawić etykietę dla danych wyjściowych zadania. Etykieta może później służyć do identyfikowania przeznaczenia wyjściowego elementu zawartości. 

- W przypadku przypisywania wartości do etykiety należy dla parametru „--output-assets” ustawić wartość „assetname=etykieta”.
- W przeciwnym przypadku należy dla parametru „--output-assets” ustawić wartość „assetname=”.
  Zwróć uwagę na dodanie znaku „=” do parametru `output-assets`. 

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Uzyskasz odpowiedź podobną do następującej:

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

Po 5 minutach sprawdź stan zadania. Powinno mieć stan „Zakończono”. Jeśli jest inny, sprawdź za kilka minut. Gdy zadanie jest zakończone, przejdź do następnego kroku i utwórz **lokalizator przesyłania strumieniowego**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-streaming-locator-and-get-path"></a>Tworzenie lokalizatora przesyłania strumieniowego i pobieranie ścieżki

Po zakończeniu kodowania następnym krokiem jest udostępnienie klientom w wyjściowym elemencie zawartości wideo, które można odtwarzać. Działanie to można wykonać w dwóch krokach: najpierw należy utworzyć **lokalizator przesyłania strumieniowego**, a następnie utworzyć adresy URL przesyłania strumieniowego, których mogą używać klienci.

### <a name="create-a-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Uzyskasz odpowiedź podobną do następującej:

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

### <a name="get-streaming-locator-paths"></a>Pobieranie ścieżek lokalizatora przesyłania strumieniowego

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Uzyskasz odpowiedź podobną do następującej:

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

Skopiuj ścieżkę Hls. W tym przypadku: `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-url"></a>Tworzenie adresu URL 

### <a name="get-streaming-endpoint-host-name"></a>Pobieranie nazwy hosta punktu końcowego przesyłania strumieniowego

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Skopiuj wartość `hostName`. W tym przypadku: `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-url"></a>Składanie adresu URL

„https://” + &lt;wartość hostName&gt; + &lt;wartość ścieżki Hls&gt;

#### <a name="example"></a>Przykład

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-with-azure-media-player"></a>Odtwarzanie testowe za pomocą usługi Azure Media Player

W tym artykule strumień jest testowany za pomocą odtwarzacza Azure Media Player. 

> [!NOTE]
> Jeśli odtwarzacz jest hostowany w witrynie korzystającej z protokołu HTTPS, zmień adres URL tak, aby zawierał ciąg „https”.

1. Otwórz przeglądarkę i przejdź pod adres [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. W polu **Adres URL:** wklej adres URL utworzony w poprzedniej sekcji. 

  Adres URL możesz wkleić w formacie HLS, Dash, lub Smooth, a usługa Azure Media Player przełączy się na odpowiedni protokół przesyłania strumieniowego w celu automatycznego odtworzenia na urządzeniu.
3. Naciśnij pozycję **Aktualizuj odtwarzacz**.

Usługi Azure Media Player można użyć do testowania, ale nie należy jej używać w środowisku produkcyjnym. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby z grupy zasobów, w tym konto usługi Media Services i magazynu utworzone w ramach tego przewodnika Szybki start, usuń grupę zasobów.

Wykonaj następujące polecenie interfejsu wiersza polecenia:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Zobacz też

Zobacz [kody błędów zadania](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykłady interfejsu wiersza polecenia](cli-samples.md)
