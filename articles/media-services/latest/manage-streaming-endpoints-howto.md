---
title: Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą usługi Azure Media Services w wersji 3
description: W tym artykule pokazano, jak zarządzać punktami końcowymi przesyłania strumieniowego za pomocą usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461048"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Zarządzanie punktami końcowymi przesyłania strumieniowego za pomocą usługi Media Services w wersji 3

Po utworzeniu konta usługi Media Services **domyślny** [punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) jest dodawany do twojego konta w stanie **Zatrzymane.** Aby rozpocząć przesyłanie strumieniowe zawartości i korzystać z [dynamicznego pakowania](dynamic-packaging-overview.md) i [szyfrowania dynamicznego,](content-protection-overview.md)punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo zawartość, musi być w stanie **Uruchomione.**

W tym artykule pokazano, jak wykonać polecenie [start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) w punkcie końcowym przesyłania strumieniowego przy użyciu różnych technologii. 
 
> [!NOTE]
> Naliczane są tylko wtedy, gdy punkt końcowy przesyłania strumieniowego jest w stanie uruchomionym.
    
## <a name="prerequisites"></a>Wymagania wstępne

Który napisze recenzję: 

* [Pojęcia dotyczące usług multimedialnych](concepts-overview.md)
* [Koncepcja punktu końcowego przesyłania strumieniowego](streaming-endpoint-concept.md)
* [Dynamiczne pakowanie](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Używanie interfejsu REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Aby uzyskać więcej informacji, zobacz: 

* Uruchom dokumentację odwołania [do programu StreamingEndpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Uruchamianie punktu końcowego przesyłania strumieniowego jest operacją asynchroniką. 

    Aby uzyskać informacje dotyczące monitorowania długotrwałych operacji, zobacz [Długotrwałe operacje](media-services-apis-overview.md).
* Ta [kolekcja Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) zawiera przykłady wielu operacji REST, w tym sposobu uruchamiania punktu końcowego przesyłania strumieniowego.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 
 
1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Przejdź do konta usługi Azure Media Services.
1. W lewym okienku wybierz pozycję **Punkty końcowe przesyłania strumieniowego**.
1. Wybierz punkt końcowy przesyłania strumieniowego, który chcesz uruchomić, a następnie wybierz pozycję **Start**.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Aby uzyskać więcej informacji, zobacz [az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Korzystanie z zestawów SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Zobacz pełny [przykład kodu Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Zobacz kompletny [przykład kodu .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Następne kroki

* [Specyfikacja OpenAPI usługi Media Services w wersji 3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Przesyłanie strumieniowe operacji punktu końcowego](https://docs.microsoft.com/rest/api/media/streamingendpoints)
