---
title: Tworzenie filtrów za pomocą usługi Azure Media Services interfejsu API REST | Dokumentacja firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów dzięki klienta można użyć ich do określonych sekcji strumienia strumienia. Usługa Media Services tworzy dynamiczne manifestów do osiągnięcia tej selektywnego przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako;cenkdin
ms.openlocfilehash: 396abe0801d76af3820d302d40d2fc076754741b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790279"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Tworzenia filtrów z usługi Azure Media Services interfejsu API REST
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Począwszy od wersji 2.17, Media Services można zdefiniować filtry dla zasobów. Te filtry są reguły po stronie serwera, które umożliwiają klientom wybierz można wykonywać następujące czynności: odtwarzanie tylko części klipu wideo (zamiast odtwarzanie całego), lub określ tylko podzestaw wersji audio i wideo, które urządzenia klienta może obsłużyć (zamiast elementu wszystkie wersje, które są skojarzone z elementu zawartości). Filtrowania elementów zawartości zostaną zarchiwizowane za pośrednictwem **dynamiczne manifestu**, które są tworzone na żądanie klienta do strumienia wideo oparte na określonej filtry.

Aby uzyskać szczegółowe informacje dotyczące filtrów i dynamicznych manifestu, zobacz [dynamicznie manifesty omówienie](media-services-dynamic-manifest-overview.md).

W tym artykule przedstawiono sposób tworzenia, aktualizacji i usuwania filtrów przy użyciu interfejsów API REST. 

## <a name="types-used-to-create-filters"></a>Typy używane do tworzenia filtrów
Następujące typy są używane podczas tworzenia filtrów:  

* [Filtr](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect i FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

>[!NOTE]

>Podczas uzyskiwania dostępu do obiektów w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsu API usług AMS, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Tworzenie filtrów
### <a name="create-global-filters"></a>Utwórz filtry globalne
Aby utworzyć filtr globalny, użyj następujących żądań HTTP:  

#### <a name="http-request"></a>Żądania HTTP
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

### <a name="create-local-assetfilters"></a>Utwórz AssetFilters lokalnego
Aby utworzyć lokalnego AssetFilter, użyj następujących żądań HTTP:  

#### <a name="http-request"></a>Żądania HTTP
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

#### <a name="http-request"></a>Żądania HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Pobierz **AssetFilter**s skojarzony z zasobem
#### <a name="http-request"></a>Żądania HTTP
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
#### <a name="http-request"></a>Żądania HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Filtry aktualizacji
Użyj poprawki, PUT lub scalania, aby zaktualizować filtr z nowej wartości właściwości.  Aby uzyskać więcej informacji o tych operacjach, zobacz [poprawki, PUT, MERGE](http://msdn.microsoft.com/library/dd541276.aspx).

Po zaktualizowaniu filtru może potrwać maksymalnie dwie minuty dla punktu końcowego przesyłania strumieniowego odświeżyć zasady. Jeśli zawartość zostało obsłużone przy użyciu tego filtru (i w pamięci podręcznej serwerów proxy i sieci CDN pamięci podręcznych), aktualizacja tego filtru może powodować błędy player. Wyczyścić pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwe, należy rozważyć użycie inny filtr.  

### <a name="update-global-filters"></a>Filtry globalne aktualizacji
Aby zaktualizować filtr globalny, należy zastosować następujące żądania HTTP: 

#### <a name="http-request"></a>Żądania HTTP
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

### <a name="update-local-assetfilters"></a>Aktualizacja lokalnych AssetFilters
Aby zaktualizować lokalny filtr, należy zastosować następujące żądania HTTP: 

#### <a name="http-request"></a>Żądania HTTP
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


## <a name="delete-filters"></a>Usuwanie filtrów
### <a name="delete-global-filters"></a>Usuń filtry globalne
Aby usunąć filtr globalny, użyj następujących żądań HTTP:

#### <a name="http-request"></a>Żądania HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Usuń lokalne AssetFilters
Aby usunąć lokalnego AssetFilter, użyj następujących żądań HTTP:

#### <a name="http-request"></a>Żądania HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Tworzenie adresów URL, które należy używać filtrów przesyłania strumieniowego
Aby uzyskać informacje na temat sposobu publikowania i dostarczanie zasobów, zobacz [dostarczania zawartości do klientów omówienie](media-services-deliver-content-overview.md).

Poniższe przykłady przedstawiają sposób dodawania filtrów do przesyłania strumieniowego adresami URL.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live przesyłania strumieniowego V4 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live przesyłania strumieniowego V3 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie dynamicznej manifestów](media-services-dynamic-manifest-overview.md)

