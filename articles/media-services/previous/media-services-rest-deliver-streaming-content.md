---
title: Publikowanie zawartości usługi Azure Media Services za pomocą usługi REST
description: Dowiedz się, jak utworzyć Lokalizator, który jest używany do tworzenia adresu URL przesyłania strumieniowego. Kod używa interfejsu API REST.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 8385dedd494c0cef968cb869ded3e92ce213da5e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790370"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publikowanie zawartości usługi Azure Media Services za pomocą usługi REST
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Można strumienia o adaptacyjnej szybkości bitowej MP4 ustawione przez utworzenie lokalizatora OnDemand przesyłania strumieniowego i tworzenia adresu URL przesyłania strumieniowego. [Kodowanie zasób](media-services-rest-encode-asset.md) artykule przedstawiono sposób kodowania w adaptacyjnej szybkości bitowej MP4 zestawu. Jeśli zawartość jest zaszyfrowany, należy skonfigurować zasady dostarczania elementu zawartości (zgodnie z opisem w [to](media-services-rest-configure-asset-delivery-policy.md) artykułu) przed utworzeniem lokalizatora. 

Lokalizator OnDemand przesyłania strumieniowego umożliwia również tworzenie adresów URL wskazujących na pliki MP4, które można pobrać progresywnie.  

W tym artykule pokazano, jak utworzyć Lokalizator OnDemand przesyłania strumieniowego w celu publikowania zawartości i tworzenie Smooth, MPEG DASH i HLS adresów URL przesyłania strumieniowego. Zawiera także gorących do tworzenia adresów URL pobierania progresywnego.

[Następujące](#types) sekcji przedstawiono typów wyliczenia, których wartości są używane w wywołaniach REST.   

> [!NOTE]
> Podczas uzyskiwania dostępu do obiektów w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsu API usług AMS, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Po pomyślnym połączeniu się https://media.windows.net, otrzymasz 301 przekierowanie, określając inny identyfikator URI usługi multimediów. Upewnij się kolejne wywołania nowy identyfikator URI.

## <a name="create-an-ondemand-streaming-locator"></a>Utwórz Lokalizator OnDemand przesyłania strumieniowego
Aby utworzyć Lokalizator OnDemand przesyłania strumieniowego i uzyskiwanie adresów URL, należy wykonać następujące czynności:

1. Jeśli zawartość jest zaszyfrowany, należy zdefiniować zasadę dostępu.
2. Utwórz Lokalizator OnDemand przesyłania strumieniowego.
3. Jeśli planujesz strumieniowo, Pobierz przesyłania strumieniowego pliku manifestu (.ism) w elemencie zawartości. 
   
   Jeśli planujesz ma być pobierana progresywnie, Pobierz nazwy plików MP4 w elemencie zawartości. 
4. Tworzenie adresów URL do pliku manifestu lub plików MP4. 
5. Nie można utworzyć Lokalizator przesyłania strumieniowego przy użyciu AccessPolicy, który zawiera zapisu lub usunąć uprawnienia.

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze korzystają z tej samej dni / dostęp uprawnień, na przykład zasady dla lokalizatorów, które powinny pozostać w miejscu przez długi czas (— przekazywanie zasady). Więcej informacji znajduje się w [tym](media-services-dotnet-manage-entities.md#limit-access-policies) artykule.

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

### <a name="create-an-ondemand-streaming-locator"></a>Utwórz Lokalizator OnDemand przesyłania strumieniowego
Utwórz Lokalizator określonym zasobie i zasad zasobów.

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

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"http://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"http://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Tworzenie adresów URL przesyłania strumieniowego
Użyj **ścieżki** zwracana wartość po utworzeniu lokalizatora tworzenie Smooth, HLS i MPEG DASH z adresów URL. 

Smooth Streaming: **ścieżki** + nazwa pliku manifestu + "/ manifest"

Przykład:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **ścieżki** + nazwa pliku manifestu + "/ manifest(format=m3u8-aapl)"

Przykład:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


KRESKA: **ścieżki** + nazwa pliku manifestu + "/ manifest(format=mpd-time-csf)"

Przykład:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Tworzenie adresy URL pobierania progresywnego
Użyj **ścieżki** zwracana wartość po utworzeniu lokalizatora tworzenia adresu URL pobierania progresywnego.   

Adres URL: **ścieżki** + nazwa mp4 pliku zasobów

Przykład:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Typy wyliczeniowe
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
[Przegląd interfejsu API REST operacji usługi Media Services](media-services-rest-how-to-use.md)

[Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md)

