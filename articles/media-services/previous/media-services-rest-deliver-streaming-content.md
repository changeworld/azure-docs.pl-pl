---
title: Publikowanie zawartości usługi Azure Media Services przy użyciu usługi REST
description: Dowiedz się, jak utworzyć Lokalizator, który jest używany do tworzenia adresu URL przesyłania strumieniowego. Kod używa interfejsu API REST.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 974f0af461ecdc7de820191950b010035d02a601
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598309"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publikowanie zawartości usługi Azure Media Services przy użyciu usługi REST 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Można przesyłanie strumieniowe z adaptacyjną szybkością transmisji bitów w formacie MP4 ustawione przez utworzenie lokalizatora OnDemand przesyłania strumieniowego i tworzenia adresu URL przesyłania strumieniowego. [Kodowanie elementu zawartości](media-services-rest-encode-asset.md) artykule pokazano, jak do zakodowania w adaptacyjną szybkością transmisji bitów zestawu plików MP4. Jeśli zawartość jest zaszyfrowany, konfigurowanie zasad dostarczania elementów zawartości (zgodnie z opisem w [to](media-services-rest-configure-asset-delivery-policy.md) artykułu) przed utworzeniem lokalizatora. 

Lokalizatora OnDemand przesyłania strumieniowego służy również do tworzenia adresów URL, które wskazują na pliki MP4, które można pobrać progresywnie.  

W tym artykule pokazano, jak utworzyć Lokalizator OnDemand przesyłania strumieniowego w celu publikowania elementów zawartości i tworzyć Smooth, MPEG DASH i HLS adresów URL przesyłania strumieniowego. Pokazuje także gorąca tworzyć adresy URL pobierania progresywnego.

[Następujące](#types) sekcji pokazano typy wyliczenia, których wartości są używane w wywołania REST.   

> [!NOTE]
> Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Po pomyślnym nawiązaniu połączenia z https://media.windows.net, otrzymasz 301 przekierowanie, określając inny identyfikator URI usługi multimediów. Użytkownik musi zapewnić kolejnych wywołań nowy identyfikator URI.

## <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora OnDemand przesyłania strumieniowego
Aby utworzyć Lokalizator OnDemand przesyłania strumieniowego i uzyskiwanie adresów URL, należy wykonać następujące czynności:

1. Jeśli zawartość jest zaszyfrowany, należy zdefiniować zasadę dostępu.
2. Tworzenie lokalizatora OnDemand przesyłania strumieniowego.
3. Jeśli użytkownik chce przesyłać strumieniowo, Pobierz przesyłania strumieniowego pliku manifestu (ISM) w elemencie zawartości. 
   
   Jeśli planujesz progresywnie uzyskać nazwy plików MP4 w elemencie zawartości. 
4. Tworzenie adresów URL do pliku manifestu lub plików MP4. 
5. Nie można utworzyć Lokalizator przesyłania strumieniowego przy użyciu AccessPolicy, która obejmuje zapisu lub usunąć uprawnienia.

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze używasz tych samych dni / dostęp do uprawnień, na przykład zasad lokalizatorów, które powinny pozostać w miejscu przez długi czas (nieprzekazywane zasady). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

Żądanie:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora OnDemand przesyłania strumieniowego
Tworzenie lokalizatora dla określonego zasobu i zasad zasobów.

Żądanie:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Tworzenie adresów URL przesyłania strumieniowego
Użyj **ścieżki** zwracana wartość po utworzeniu lokalizatora do tworzenia, Smooth, HLS i MPEG DASH w adresach URL. 

Zestaw Smooth Streaming: **Ścieżka** + nazwa pliku manifestu + "/ manifest"

przykład:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Ścieżka** + nazwa pliku manifestu + "/ manifest(format=m3u8-aapl)"

przykład:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Ścieżka** + nazwa pliku manifestu + "/ manifest(format=mpd-time-csf)"

przykład:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Tworzyć adresy URL pobierania progresywnego
Użyj **ścieżki** zwracana wartość po utworzeniu lokalizatorów tworzenia adresu URL pobierania progresywnego.   

Adres URL: **Ścieżka** + nazwa mp4 pliku elementu zawartości

przykład:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Typach wyliczeniowych
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz także
[Omówienie interfejsu API REST usługi Media Services operacji](media-services-rest-how-to-use.md)

[Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md)

