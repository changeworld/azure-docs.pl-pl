---
title: Tworzenie filtrów za pomocą usługi Azure Media Services — interfejs API REST | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, więc klienta można ich używać do określonych sekcji strumienia strumienia. Usługa Media Services tworzy manifestów dynamicznych w celu uzyskania tego selektywne przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 5b023a152cf93ec6ff688674e991ad55db215965
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767816"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Tworzenie filtrów za pomocą usługi Azure Media interfejs API REST usług 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Począwszy od wersji 2.17, Media Services umożliwia definiowanie filtrów dla zasobów. Te filtry są reguły po stronie serwera, które umożliwiają klientom chce wykonywać następujące czynności: odtwarzanie tylko na część wideo (zamiast odtwarzanie całego), lub określ tylko podzbiór odwzorowaniami audio i wideo, obsługujące przez klienta urządzenia (zamiast z wszystkie wersje, które są skojarzone z elementem zawartości). Filtrowanie zasobów są archiwizowane za pośrednictwem **manifestów dynamicznych**s, które są tworzone na żądanie klienta do przesyłania strumieniowego wideo oparte na określonej filtry.

Aby uzyskać szczegółowe informacje dotyczące filtrów i manifestów dynamicznych, zobacz [dynamiczne manifesty Przegląd](media-services-dynamic-manifest-overview.md).

W tym artykule pokazano, jak używać interfejsów API REST do tworzenia, aktualizacji i usuwania filtrów. 

## <a name="types-used-to-create-filters"></a>Typy używane do tworzenia filtrów
Podczas tworzenia filtrów, używane są następujące typy:  

* [Filtr](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect i FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Tworzenie filtrów
### <a name="create-global-filters"></a>Tworzenie filtrów globalnych
Aby utworzyć filtr globalny, użyj następujących żądań HTTP:  

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądania

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

Treść żądania 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>Odpowiedź HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Tworzenie lokalnej AssetFilters
Aby utworzyć AssetFilter lokalnych, wykonaj następujące żądania HTTP:  

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądania

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

Treść żądania 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>Odpowiedź HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Lista filtrów
### <a name="get-all-global-filters-in-the-ams-account"></a>Pobierz wszystkie globalne **filtru**s w ramach konta usługi AMS
Aby wyświetlić listę filtrów, użyj następujących protokołu HTTP żądania: 

#### <a name="http-request"></a>Żądanie HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Pobierz **AssetFilter**s skojarzone z elementem zawartości
#### <a name="http-request"></a>Żądanie HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Pobierz **AssetFilter** na podstawie jego identyfikatora
#### <a name="http-request"></a>Żądanie HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Zaktualizuj filtry
Użyj poprawki, PUT lub scalania, aby zaktualizować filtr przy użyciu nowej wartości właściwości.  Aby uzyskać więcej informacji na temat tych operacji, zobacz [umieszczanie poprawek i SCALIĆ](https://msdn.microsoft.com/library/dd541276.aspx).

Jeśli zaktualizujesz filtru, może potrwać do dwóch minut, zanim punkt końcowy przesyłania strumieniowego odświeżyć zasady. Zawartość zostało obsłużone za pomocą tego filtru (i w pamięci podręcznej serwerów proxy i Azure CDN pamięci podręcznych), aktualizacja tego filtru może spowodować błędy odtwarzacza. Wyczyść pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy wziąć pod uwagę przy użyciu innego filtru.  

### <a name="update-global-filters"></a>Zaktualizuj filtry globalne
Aby zaktualizować filtrów globalnych, użyj następujących żądań HTTP: 

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądania: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Treść żądania: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>Zaktualizuj lokalne AssetFilters
Aby zaktualizować lokalny filtr, użyj następujących żądań HTTP: 

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądania: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Treść żądania: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>Usuń filtry
### <a name="delete-global-filters"></a>Usuń filtry globalne
Aby usunąć filtr globalny, użyj następujących żądań HTTP:

#### <a name="http-request"></a>Żądanie HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Usuń lokalne AssetFilters
Aby usunąć AssetFilter lokalnym, użyj następujących żądań HTTP:

#### <a name="http-request"></a>Żądanie HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Tworzenie adresów URL, które używają filtrów przesyłania strumieniowego
Aby uzyskać informacje na temat publikowania i dostarczać zasobów, zobacz [dostarczanie zawartości klientom Przegląd](media-services-deliver-content-overview.md).

Następujące przykłady przedstawiają sposób dodawania filtrów do przesyłania strumieniowego adresami URL.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live przesyłania strumieniowego V4 (HLS)**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live przesyłania strumieniowego (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md)

