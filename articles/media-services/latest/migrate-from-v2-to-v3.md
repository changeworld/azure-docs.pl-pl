---
title: Migrowanie z usługi Azure Media Services w wersji 2 do 3 | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services w wersji 3 oraz przedstawiono różnice między dwoma wersjami.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/22/2018
ms.author: juliako
ms.openlocfilehash: 4e644db12a74d6ef132a0c8d64ef517a0c2253cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655630"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Migrowanie z usługi Media Services w wersji 2 do 3

> [!NOTE]
> Najnowsza wersja usługi Azure Media Services jest w wersji zapoznawczej i może być nazywana wersją 3.

W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services (AMS) w wersji 3 oraz przedstawiono różnice między dwoma wersjami.

## <a name="why-should-you-move-to-v3"></a>Dlaczego należy przenieść v3?

### <a name="api-is-more-approachable"></a>Interfejs API jest bardziej przystępne

*  w wersji 3 jest oparta na ujednoliconego powierzchni interfejsu API, który udostępnia funkcję zarówno zarządzanie i operacje, które są wbudowane w Menedżera zasobów Azure. Szablony usługi Azure Resource Manager może służyć do tworzenia i wdrażania transformacje, punkty końcowe przesyłania strumieniowego, LiveEvents i.
* Otwórz interfejsu API (Swagger alias) dokument specyfikacja.
* Zestawy SDK jest dostępna dla platformy .net, .net Core, Node.js, Python, Java, Ruby.
* Integracja interfejsu wiersza polecenia platformy Azure.

### <a name="new-features"></a>Nowe funkcje

* Kodowanie obsługuje teraz HTTPS pozyskiwania (adres Url na podstawie danych wejściowych).
* Transformacje stanowią nowość w wersji 3. Transformacja służy do udziału konfiguracje, tworzyć szablony Menedżera zasobów Azure i izolowanie ustawienia kodowania dla określonego klienta lub jednego dzierżawcy. 
* Zasób może mieć wielu StreamingLocators z innymi ustawieniami dynamicznego tworzenia pakietów i dynamicznego szyfrowania.
* Ochrona zawartości obsługuje wiele kluczowych funkcji. 
* Podgląd LiveEvent obsługuje funkcję dynamicznego tworzenia pakietów i dynamicznego szyfrowania. Dzięki temu ochrony zawartości na opakowaniu podgląd oraz KRESKI i HLS.
* LiveOuput użycie jest łatwiejsze niż starszy jednostki programu. 
* Dodano obsługę RBAC na jednostkach.

## <a name="changes-from-v2"></a>Zmiany w wersji 2

* Zestawy SDK usługi Media całkowicie niezależna od zestawu SDK magazynu, który zapewnia większą kontrolę nad zestawu SDK usługi Magazyn używany i pozwala uniknąć problemów z kontroli wersji. 
* W wersji 3 są wszystkie kodowania szybkości transmisji bitów w bitów na sekundę. To jest inny niż v2 REST, które ustawienia standardu Media Encoder Standard. Na przykład szybkości transmisji bitów w wersji 2 zostałaby określona jako 128, ale w wersji 3 byłoby 128000. 
* IngestManifests AssetFiles, AccessPolicies, nie istnieją w wersji 3.
* ContentKeys nie są już jednostki, właściwość StreamingLocator.
* Zdarzenia techniczną siatka zastępuje NotificationEndpoints.
* Zmieniono niektóre jednostki

  * JobOutput zastępuje zadanie teraz tylko część zadania.
  * LiveEvent zastępuje kanału.
  * LiveOutput zastępuje Program.
  * StreamingLocator zastępuje lokalizatora.

## <a name="code-changes"></a>Zmiany kodu

### <a name="create-an-asset-and-upload-a-file"></a>Utworzenie elementu zawartości i przekazywanie pliku 

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

### <a name="submit-a-job"></a>Prześlij zadanie

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

### <a name="publish-an-asset-with-aes-encryption"></a>Publikowanie elementu zawartości z szyfrowaniem AES 

#### <a name="v2"></a>v2

1. Utwórz ContentKeyAuthorizationPolicyOption
2. Utwórz ContentKeyAuthorizationPolicy
3. Utwórz AssetDeliveryPolicy
4. Tworzenie zasobów i przekazać zawartość lub przesyłania zadania i używanie zawartości wyjściowej
5. Skojarz AssetDeliveryPolicy z zasobów
6. Utwórz ContentKey
7. Dołącz ContentKey do zasobów
8. Utwórz AccessPolicy
9. Utwórz Lokalizator

#### <a name="v3"></a>v3

1. Tworzenie zasad klucza zawartości
2. Tworzenie zasobu
3. Przekaż zawartość lub Użyj zasobów jako JobOutput
4. Utwórz Lokalizator

## <a name="next-steps"></a>Kolejne kroki

Aby sprawdzić, jak łatwo rozpocząć kodowanie i strumieniowe przesyłanie plików wideo, zobacz [Strumieniowe przesyłanie plików](stream-files-dotnet-quickstart.md). 

