---
title: Jak uzyskać wystąpienie procesora multimediów przy użyciu REST | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć składnik procesora multimediów do kodowania, konwertowania formatu, szyfrowania lub odszyfrowywania zawartości multimedialnej dla usługi Azure Media Services.
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
ms.openlocfilehash: 3d7b3922c9bb7eb79cd6436ba1b265714678fcc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774903"
---
# <a name="how-to-get-a-media-processor-instance"></a>Jak uzyskać wystąpienie procesora multimediów
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Reszta](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Omówienie
Procesory multimediów to składnik obsługujący określone zadanie przetwarzania wideo lub audio, takie jak kodowanie, konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. Wszystkie zadania przesłane do usługi Media Services wymagają procesora multimediów do kodowania, szyfrowania lub konwertowania zawartości wideo lub audio. 

## <a name="azure-media-processors"></a>Procesory multimediów platformy Azure 

Poniższy temat zawiera listy procesorów multimediów:

* [Kodowanie procesorów multimediów](scenarios-and-availability.md#encoding-media-processors)
* [Procesory multimediów usługi analizy](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 


## <a name="get-a-media-processor"></a>Pobierz procesor multimediów

Poniższe wywołanie REST pokazuje, jak uzyskać wystąpienie procesora multimediów według nazwy (w tym przypadku **Media Encoder Standard).** 

Żądanie:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
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
Teraz, gdy wiesz, jak uzyskać wystąpienie procesora multimediów, przejdź do artykułu [Jak zakodować zasoby,](media-services-rest-get-started.md) który pokazuje, jak używać media Encoder Standard do kodowania zasobu.

