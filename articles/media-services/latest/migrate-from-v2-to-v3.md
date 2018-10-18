---
title: Migrowanie z usługi Azure Media Services v2 do v3 | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services v3 i przedstawiono różnice między dwoma wersjami.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: a17bad5beb651aaa085c626296c200a00c30647e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376366"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrowanie z usługi Media Services v2 do v3

W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services (AMS) w wersji 3 i przedstawiono różnice między dwoma wersjami.

## <a name="why-should-you-move-to-v3"></a>Dlaczego należy można przenieść do wersji 3?

### <a name="api-is-more-approachable"></a>Interfejs API jest bardziej przystępne.

*  v3 opiera się na ujednoliconego powierzchni interfejsu API, który uwidacznia funkcje operacji oraz zarządzania oparta na usłudze Resource Manager. Szablony usługi Azure Resource Manager może służyć do tworzenia i wdrażania przekształceń, punkty końcowe przesyłania strumieniowego, LiveEvents i nie tylko.
* Otwórz interfejs API (zwane również struktury Swagger) dokument specyfikacji.
* Zestawy SDK dostępne dla platformy .net,.Net Core, Node.js, Python, Java, Ruby.
* Integracja z interfejsu wiersza polecenia platformy Azure.

### <a name="new-features"></a>Nowe funkcje

* Kodowanie obsługuje teraz protokół HTTPS pozyskiwania (wejście opartego na adresach Url).
* Przekształcenia stanowią nowość w wersji 3. Przekształcenie umożliwia udostępnianie konfiguracji, tworzenia szablonów usługi Azure Resource Manager i izolować ustawienia kodowania, dla konkretnego klienta lub jednego dzierżawcy. 
* Element zawartości może mieć wiele StreamingLocators z różnymi ustawieniami funkcji dynamicznego tworzenia pakietów i szyfrowania dynamicznego.
* Usługa Content protection obsługuje funkcje wielu kluczy. 
* Element LiveEvent (wersja zapoznawcza) obsługuje funkcję dynamicznego tworzenia pakietów i szyfrowania dynamicznego. Dzięki temu ochrony zawartości w wersji zapoznawczej, a także DASH i HLS pakowania.
* LiveOuput jest prostsza w użyciu niż starsza jednostki programu. 
* Dodano obsługę funkcji RBAC w jednostkach.

## <a name="changes-from-v2"></a>Zmiany w wersji 2

* W Media Services v3 szyfrowania magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

    Dla zasoby utworzone w wersji 3, Media Services obsługuje [usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) szyfrowanie magazynu po stronie serwera.
    
* Zestawy SDK usług Media całkowicie niezależna od zestawu SDK magazynu, co daje większą kontrolę nad zestawu SDK usługi Storage używane i eliminuje problemy z wersjonowaniem. 
* W wersji 3 są wszystkie kodowania szybkości transmisji bitów w bitach na sekundę. Stanowi to odmianę v2 REST, które ustawienia wstępne usługi Media Encoder Standard. Na przykład szybkości transmisji bitów w wersji 2 będzie można określić jako 128, ale w wersji 3 byłoby 128000. 
* IngestManifests AssetFiles, AccessPolicies, nie istnieją w wersji 3.
* Kluczy zawartości nie są już jednostki, właściwość StreamingLocator.
* Obsługa siatki zdarzeń zastępuje NotificationEndpoints.
* Niektóre jednostki zostały zmienione.

  * JobOutput zastępuje zadanie teraz po prostu część zadania.
  * Element LiveEvent zastępuje kanału.
  * LiveOutput zastępuje Program.
  * StreamingLocator zastępuje lokalizatora.

## <a name="code-changes"></a>Zmiany kodu

### <a name="create-an-asset-and-upload-a-file"></a>Utworzenie elementu zawartości i przekaż plik 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Przesyłanie zadania

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Publikowanie elementu zawartości przy użyciu szyfrowania AES 

#### <a name="v2"></a>v2

1. Utwórz ContentKeyAuthorizationPolicyOption
2. Utwórz ContentKeyAuthorizationPolicy
3. Utwórz AssetDeliveryPolicy
4. Tworzenie elementu zawartości i przekazywać zawartość lub Prześlij zadanie i korzystają z danych wyjściowych elementu zawartości
5. Kojarzenie AssetDeliveryPolicy z elementem zawartości
6. Utwórz ContentKey
7. Dołącz ContentKey do zasobu
8. Utwórz AccessPolicy
9. Tworzenie lokalizatora

#### <a name="v3"></a>v3

1. Tworzenie zasad kluczy zawartości
2. Utwórz zasób
3. Przekazywanie zawartości lub Użyj zasobów jako JobOutput
4. Utwórz StreamingLocator

## <a name="next-steps"></a>Kolejne kroki

Aby sprawdzić, jak łatwo rozpocząć kodowanie i strumieniowe przesyłanie plików wideo, zobacz [Strumieniowe przesyłanie plików](stream-files-dotnet-quickstart.md). 

