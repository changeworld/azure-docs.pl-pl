---
title: Konfigurowanie zasad dostarczania zasobów przy użyciu interfejsu API REST usług Media | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować zasady dostarczania różnych zasobów przy użyciu interfejsu API REST usługi multimediów.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: d6f18363cceaf279d92ada77f52d39b7f1d12f65
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790447"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurowanie zasad dostarczania zasobów
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Jeśli planujesz dostarczanie dynamicznie szyfrowanych zasoby, jeden z kroków w przepływie pracy dostarczania zawartości Media Services konfiguruje zasady dostarczania zasobów. Zasady dostarczania elementu zawartości informuje usługi Media Services sposób dla zawartości dostarczanej: w których przesyłania strumieniowego protokołu powinna zawartości dynamicznie umieszczone (na przykład, MPEG DASH, HLS, Smooth Streaming lub wszystkie), czy ma być dynamicznego szyfrowania zawartości i w jaki sposób (koperty lub szyfrowania common encryption).

W tym temacie omówiono dlaczego i jak utworzyć i skonfigurować zasady dostarczania zasobów.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
>Ponadto aby można było korzystać z dynamicznego tworzenia pakietów i dynamicznego szyfrowania zawartości musi zawierać zestaw o adaptacyjnej szybkości bitowej MP4s lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Różnych zasad można zastosować do tego samego zasobu. Na przykład można zastosować szyfrowanie PlayReady do szyfrowania Smooth Streaming i szyfrowanie AES Envelope MPEG DASH i HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem od tej reguły jest przypadek, w którym nie zdefiniowano żadnej zasady dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli chcesz dostarczyć zasób zaszyfrowanych magazynu, należy skonfigurować zasady dostarczania elementu zawartości. Przed zawartości mogą być przesyłane strumieniowo, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieni zawartości przy użyciu zasady dostarczania określony. Na przykład aby dostarczania zawartości zaszyfrowane za pomocą klucza szyfrowania koperty Advanced Encryption (Standard AES), Ustaw typ zasad **DynamicEnvelopeEncryption**. Aby usunąć szyfrowanie magazynu i zasobów niezabezpieczona strumienia, Ustaw typ zasad **NoDynamicEncryption**. Wykonaj przykłady, które pokazują, jak skonfigurować te typy zasad.

W zależności od konfiguracji zasad dostarczania elementów zawartości będzie możliwe dynamicznie pakietu dynamicznie szyfrowania i przesyłania strumieniowego następujących protokołów: Smooth Streaming, HLS, strumieni MPEG DASH.

Na poniższej liście przedstawiono formaty służącego do strumienia Smooth, HLS, DASH.

Funkcje Smooth Streaming:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

HLS:

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=m3u8-aapl) przesyłania strumieniowego

MPEG DASH

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=mpd-time-csf) przesyłania strumieniowego


Aby uzyskać instrukcje dotyczące sposobu publikowania elementów zawartości i tworzenia adresu URL przesyłania strumieniowego, zobacz artykuł [Build a streaming URL](media-services-deliver-streaming-content.md) (Tworzenie adresu URL przesyłania strumieniowego).

## <a name="considerations"></a>Zagadnienia do rozważenia
* Nie można usunąć AssetDeliveryPolicy skojarzone z zasobów, gdy Lokalizator OnDemand (streaming) istnieje dla tego zasobu. Zalecane jest, aby usunąć zasady z zasobu przed usunięciem zasad.
* Nie można utworzyć Lokalizator przesyłania strumieniowego magazynu trwałego zaszyfrowane, jeśli ustawiono nie zasad dostarczania elementów zawartości.  Jeśli element zawartości nie jest szyfrowany w magazynie, system będzie umożliwiają tworzenie lokalizatora i strumienia zasobów w zwykłym bez zasad dostarczania elementów zawartości.
* Może mieć wiele zasady dostarczania zasobów skojarzonych z pojedynczego zasobu, ale można określić tylko jeden sposób obsługi danego AssetDeliveryProtocol.  Co oznacza, spróbuj połączyć dwie zasady dostarczania, które określają protokół AssetDeliveryProtocol.SmoothStreaming, który spowoduje błąd, ponieważ system nie może określić, które co ma to zastosowanie, gdy klient przesyła żądanie Smooth Streaming.
* Jeśli zasób z istniejących Lokalizator przesyłania strumieniowego, nie można połączyć nowe zasady w zasobie, Rozłącz istniejące zasady z zasobu lub zaktualizowania zasad dostarczania skojarzone z elementu zawartości.  Należy najpierw usuń Lokalizator przesyłania strumieniowego, Dostosuj zasady, a następnie ponownie utwórz Lokalizator przesyłania strumieniowego.  Można użyć tego samego locatorId podczas ponownego tworzenia Lokalizator przesyłania strumieniowego, ale należy upewnić się, że nie będzie powodować problemy dla klientów od zawartości mogą być buforowane źródła lub podrzędne CDN.

>[!NOTE]

>Podczas uzyskiwania dostępu do obiektów w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w Twoich żądań HTTP. Aby uzyskać więcej informacji, zobacz [ustawień dla rozwoju interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsu API usług AMS, zobacz [dostępu Azure Media Services API przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości wyczyść
### <a id="create_asset_delivery_policy"></a>Tworzenie zasad dostarczania elementów zawartości
Następujące żądania HTTP tworzy zasady dostarczania elementu zawartości, określająca nie dotyczyć szyfrowania dynamicznego i dostarczać strumienia w jednym z następujących protokołów: MPEG DASH, HLS i Smooth Streaming protokołów. 

Aby uzyskać informacje na jakie wartości można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typów używanych podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

Żądanie:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a id="link_asset_with_asset_delivery_policy"></a>Łącze zasobów z zasad dostarczania elementów zawartości
Następujące żądania HTTP łączy zasad dostarczania elementów zawartości do określonego zasobu.

Żądanie:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Odpowiedź:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Utwórz klucz zawartości typu EnvelopeEncryption i połącz go do elementu zawartości
Podczas określania zasad dostarczania DynamicEnvelopeEncryption, musisz upewnij się, że klucz zawartości typu EnvelopeEncryption połączyć zawartości. Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Pobieranie adresu URL dostarczania
Pobierz adres URL dostarczania dla metody dostarczania określony klucz zawartości utworzony w poprzednim kroku. Klient używa zwrócony adres URL, aby zażądać klucza AES lub PlayReady licencję w celu odtwarzania zawartości chronionej.

Określ typ adresu URL do pobrania w treści żądania HTTP. W przypadku ochrony zawartości przy użyciu usługi PlayReady, żądanie adresu URL pozyskiwania licencji PlayReady usług nośnika przy użyciu 1 dla keyDeliveryType: {"keyDeliveryType": 1}. W przypadku ochrony zawartości przy użyciu szyfrowania koperty żądanie adresu URL pozyskiwania kluczy, określając 2 dla keyDeliveryType: {"keyDeliveryType": 2}.

Żądanie:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Odpowiedź:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Tworzenie zasad dostarczania elementów zawartości
Tworzy następujące żądania HTTP **AssetDeliveryPolicy** skonfigurowanego na zastosowanie szyfrowania dynamicznego koperty (**DynamicEnvelopeEncryption**) do **HLS** protokołu (w tym przykładzie innych protokołów będzie blokowany przesyłania strumieniowego). 

Aby uzyskać informacje na jakie wartości można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typów używanych podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

Żądanie:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Odpowiedź:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Łącze zasobów z zasad dostarczania elementów zawartości
Zobacz [zasobów łącza z zasad dostarczania elementów zawartości](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Utwórz klucz zawartości typu CommonEncryption i połącz go do elementu zawartości
Podczas określania zasad dostarczania DynamicCommonEncryption, musisz upewnij się, że klucz zawartości typu CommonEncryption połączyć zawartości. Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Pobieranie adresu URL dostarczania
Pobierz adres URL dostarczania dla metody dostarczania PlayReady klucz zawartości utworzony w poprzednim kroku. Klient używa zwróconych adresu URL żądania licencji PlayReady w celu odtwarzania zawartości chronionej. Aby uzyskać więcej informacji, zobacz [uzyskać adres URL dostarczania](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Tworzenie zasad dostarczania elementów zawartości
Tworzy następujące żądania HTTP **AssetDeliveryPolicy** skonfigurowanego do zastosowania dynamicznego szyfrowania common encryption (**DynamicCommonEncryption**) do **Smooth Streaming** protokołu (w tym przykładzie innych protokołów będzie blokowany przesyłania strumieniowego). 

Aby uzyskać informacje na jakie wartości można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typów używanych podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

Żądanie:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Jeśli chcesz chronić zawartość przy użyciu Widevine DRM, zaktualizuj wartości AssetDeliveryConfiguration WidevineLicenseAcquisitionUrl (który ma wartość 7) i podaj adres URL usługi dostarczania licencji. Można użyć następujących partnerów AMS ułatwiające licencje Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Na przykład: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> W przypadku szyfrowania przy użyciu metody Widevine, tylko będzie mogła dostarczać przy użyciu kreska. Upewnij się określić DASH (2) w Protokół dostarczania elementów zawartości.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Łącze zasobów z zasad dostarczania elementów zawartości
Zobacz [zasobów łącza z zasad dostarczania elementów zawartości](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Typy używane podczas definiowania AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Następujące wyliczenia opisano wartości, które można ustawić dla Protokół dostarczania elementów zawartości.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Następujące wyliczenia opisano wartości, które można ustawić dla typu zasady dostarczania elementu zawartości.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Następujące wyliczenia opisano wartości, które służy do konfigurowania metody dostarczania zawartości klucza do klienta.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Następujące wyliczenia opisano wartości, które można ustawić, aby skonfigurować klucze służące do pobrania konfiguracji określonych dla zasad dostarczania elementów zawartości.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

