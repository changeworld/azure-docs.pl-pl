---
title: Jak uzyskać wystąpienie procesor multimediów, przy użyciu usługi REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć składnik procesor multimediów do kodowania, przekonwertowania formatu, szyfrowanie i odszyfrowywanie zawartości multimediów dla usług Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d342cff6d322195ee88a74215f814be7d702aa5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761980"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak uzyskać wystąpienie procesor multimediów
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Omówienie
Procesory multimediów to składnik, który obsługuje określone wideo lub audio przetwarzania zadania, takie jak kodowania, konwersji formatów, zawartości multimedialnej szyfrowania lub odszyfrowywania. Wszystkie zadania przesłane do usługi Media Services wymaga procesor multimediów do kodowania, szyfrowania i konwersji zawartości wideo lub audio. 

## <a name="azure-media-processors"></a>Procesory multimediów Azure 

Poniższy temat zawiera listę procesory multimediów:

* [Kodowanie procesorów multimediów](scenarios-and-availability.md#encoding-media-processors)
* [Procesory multimediów usługi analizy](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Pobierz procesor multimediów

Poniższe wywołanie REST pokazuje, jak uzyskać wystąpienie procesora multimediów za pomocą nazwy (w tym przypadku **Media Encoder Standard**). 

Żądanie:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

Odpowiedź:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Teraz gdy wiesz, jak uzyskać wystąpienie procesora media, przejdź do [jak kodowanie elementu zawartości](media-services-rest-get-started.md) artykułu, który pokazuje, jak za pomocą usługi Media Encoder Standard kodowanie elementu zawartości.

