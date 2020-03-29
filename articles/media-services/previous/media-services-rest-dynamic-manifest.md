---
title: Tworzenie filtrów za pomocą interfejsu API REST usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł ich używać do przesyłania strumieniowego określonych sekcji strumienia. Usługa Media Services tworzy dynamiczne manifesty w celu osiągnięcia tego selektywnego przesyłania strumieniowego.
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
ms.author: juliako
ms.reviewr: cenkdin
ms.openlocfilehash: b778ad8c59cf51f92584cd3590f7d99244f37b2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774952"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Tworzenie filtrów za pomocą interfejsu API REST usługi Azure Media Services 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Reszta](media-services-rest-dynamic-manifest.md)
> 
> 

Począwszy od wersji 2.17, program Media Services umożliwia definiowanie filtrów dla zasobów. Filtry te są regułami po stronie serwera, które umożliwiają klientom wybranie takich czynności, jak: odtwarzanie tylko sekcji wideo (zamiast odtwarzania całego filmu) lub określanie tylko podzbioru wersji audio i wideo, które może obsługiwać urządzenie klienta (zamiast wszystkich wersji skojarzonych z zasobem). To filtrowanie zasobów jest archiwizowane za pomocą **manifestów dynamicznych,** które są tworzone na żądanie klienta w celu przesyłania strumieniowego wideo na podstawie określonych filtrów.

Aby uzyskać bardziej szczegółowe informacje dotyczące filtrów i manifestu dynamicznego, zobacz [Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md).

W tym artykule pokazano, jak używać interfejsów API REST do tworzenia, aktualizowania i usuwania filtrów. 

## <a name="types-used-to-create-filters"></a>Typy używane do tworzenia filtrów
Podczas tworzenia filtrów używane są następujące typy:  

* [Filtr](https://docs.microsoft.com/rest/api/media/operations/filter)
* [Filtr zasobów](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange (Zestaw czasu prezentacji)](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect i FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Podczas uzyskiwania dostępu do encji w programie Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie programu Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje dotyczące łączenia się z interfejsem API usługi AMS, zobacz [Dostęp do interfejsu API usługi Azure Media Services za pomocą uwierzytelniania usługi Azure AD.](media-services-use-aad-auth-to-access-ams-api.md) 

## <a name="create-filters"></a>Tworzenie filtrów
### <a name="create-global-filters"></a>Tworzenie filtrów globalnych
Aby utworzyć filtr globalny, użyj następujących żądań HTTP:  

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądań

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
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

### <a name="create-local-assetfilters"></a>Tworzenie lokalnych filtrów zasobów
Aby utworzyć lokalny filtr zasobów, użyj następujących żądań HTTP:  

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądań

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
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

## <a name="list-filters"></a>Filtry listy
### <a name="get-all-global-filters-in-the-ams-account"></a>Pobierz wszystkie **filtry**globalne na konto AMS
Aby wyświetlić filtry listy, użyj następujących żądań HTTP: 

#### <a name="http-request"></a>Żądanie HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Pobierz **filtr zasobów**skojarzony z zasobem
#### <a name="http-request"></a>Żądanie HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Pobierz **filtr assetfilter** na podstawie jego identyfikatora
#### <a name="http-request"></a>Żądanie HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Aktualizuj filtry
Użyj patcha, put lub merge, aby zaktualizować filtr o nowe wartości właściwości.  Aby uzyskać więcej informacji na temat tych operacji, zobacz [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Jeśli zaktualizujesz filtr, odświeżenie reguł może potrwać do dwóch minut. Jeśli zawartość została wyświetlona przy użyciu tego filtru (i buforowana w plikach proxy i pamięci podręcznych sieci CDN), zaktualizowanie tego filtru może spowodować błędy odtwarzacza. Wyczyść pamięć podręczną po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru.  

### <a name="update-global-filters"></a>Aktualizowanie filtrów globalnych
Aby zaktualizować filtr globalny, użyj następujących żądań HTTP: 

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądań: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
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

### <a name="update-local-assetfilters"></a>Aktualizowanie lokalnych filtrów zasobów
Aby zaktualizować filtr lokalny, użyj następujących żądań HTTP: 

#### <a name="http-request"></a>Żądanie HTTP
Nagłówki żądań: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
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
### <a name="delete-global-filters"></a>Usuwanie filtrów globalnych
Aby usunąć filtr globalny, użyj następujących żądań HTTP:

#### <a name="http-request"></a>Żądanie HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.19 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Usuwanie lokalnych filtrów zasobów
Aby usunąć lokalny filtr zasobów, użyj następujących żądań HTTP:

#### <a name="http-request"></a>Żądanie HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Tworzenie adresów URL przesyłania strumieniowego, które używają filtrów
Aby uzyskać informacje na temat publikowania i dostarczania zasobów, zobacz [Omówienie dostarczania zawartości klientom.](media-services-deliver-content-overview.md)

Poniższe przykłady pokazują, jak dodać filtry do adresów URL przesyłania strumieniowego.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Transmisja na żywo (HLS) v4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Transmisja na żywo (HLS) v3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie manifestów dynamicznych](media-services-dynamic-manifest-overview.md)

