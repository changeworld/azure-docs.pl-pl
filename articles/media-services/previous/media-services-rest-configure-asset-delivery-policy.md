---
title: Konfigurowanie zasad dostarczania elementów zawartości przy użyciu interfejsu API REST usługi Media Services | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować zasady dostarczania różnych zasobów przy użyciu interfejsu API REST usługi Media Services.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60817396"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurowanie zasad dostarczania elementów zawartości
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Jeśli planujesz dostarczanie dynamicznie szyfrowanych zasoby jedną z czynności w przepływie pracy dostarczania zawartości usługi Media Services służy do konfigurowania zasad dostarczania zasobów. Zasady dostarczania elementu zawartości informuje Media Services, jak chcesz uzyskać element zawartości został dostarczony: do protokołu przesyłania strumieniowego powinny element zawartości można dynamicznie spakować (na przykład, między innymi MPEG DASH, HLS, Smooth Streaming lub wszystkie), czy chcesz szyfrować dynamicznie element zawartości i w jaki sposób (koperty lub szyfrowania common encryption).

W tym temacie omówiono, dlaczego i jak tworzenie i konfigurowanie zasad dostarczania elementów zawartości.

> [!NOTE]
> Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
> Ponadto aby można było korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego element zawartości musi zawierać zestaw każdego pliku MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Różne zasady można zastosować do tego samego zasobu. Na przykład można zastosować szyfrowanie PlayReady Smooth Streaming i szyfrowanie AES Envelope szyfrowanie MPEG DASH i HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem od tej reguły jest przypadek, w którym nie zdefiniowano żadnej zasady dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli użytkownik chce dostarczać zaszyfrowane trwałego magazynu, należy skonfigurować zasady dostarczania elementu zawartości. Zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu zasad dostarczania określony. Na przykład do dostarczania elementów zawartości szyfrowane przy użyciu klucza szyfrowania koperty Advanced Encryption Standard (AES), Ustaw typ zasad na **DynamicEnvelopeEncryption**. Aby usunąć szyfrowania magazynu i przesyłanie strumieniowe zawartości niezaszyfrowane, Ustaw typ zasad **NoDynamicEncryption**. Postępuj zgodnie z przykładami pokazującymi sposób konfigurowania następujących typów zasad.

W zależności od konfiguracji zasad dostarczania elementów zawartości będzie mieć możliwość dynamicznego pakowania, dynamicznego szyfrowania i strumienia z następujących protokołów przesyłania strumieniowego: Zestaw Smooth Streaming, HLS, MPEG DASH strumieni.

Na poniższej liście przedstawiono formaty służącego do strumienia Smooth, HLS, DASH.

Zestaw Smooth Streaming:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

HLS:

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Aby uzyskać instrukcje dotyczące sposobu publikowania elementów zawartości i tworzenia adresu URL przesyłania strumieniowego, zobacz artykuł [Build a streaming URL](media-services-deliver-streaming-content.md) (Tworzenie adresu URL przesyłania strumieniowego).

## <a name="considerations"></a>Zagadnienia do rozważenia
* Nie można usunąć AssetDeliveryPolicy skojarzone z elementem zawartości, gdy Lokalizator OnDemand (streaming) nie istnieje dla tego zasobu. Zalecane jest, aby usunąć zasady z zasobu przed usunięciem zasad.
* Nie można utworzyć Lokalizator przesyłania strumieniowego magazynu trwałego zaszyfrowane, jeśli skonfigurowano nie zasad dostarczania elementów zawartości.  Jeśli zasób nie jest szyfrowany w magazynie, system będzie można utworzyć i przesyłanie strumieniowe zasób w zabezpieczeniu bez zasad dostarczania elementu zawartości.
* Może mieć wiele zasad dostarczania elementów zawartości skojarzone z pojedynczego zasobu, ale można określić tylko jeden sposób obsługi danego AssetDeliveryProtocol.  Co oznacza, Jeśli spróbujesz połączyć dwie zasady dostarczania, które określają protokół AssetDeliveryProtocol.SmoothStreaming, który spowoduje wystąpienie błędu, ponieważ system nie może określić, którego, w którym ma się są stosowane, gdy klient wysyła żądanie Smooth Streaming.
* Jeśli element zawartości za pomocą Lokalizator przesyłania strumieniowego, nie można połączyć nowych zasad do elementu zawartości, Rozłącz istniejące zasady z zasobu lub zaktualizowania zasad dostarczania skojarzone z elementem zawartości.  Musisz najpierw usuń Lokalizator przesyłania strumieniowego, Dostosuj zasady, a następnie ponownie utwórz Lokalizator przesyłania strumieniowego.  Podczas ponownego tworzenia Lokalizator przesyłania strumieniowego, ale należy upewnić się, że, nie będzie powodować problemy dla klientów od zawartości mogą być buforowane przez źródło lub podrzędnego usługi CDN, możesz użyć tego samego locatorId.

> [!NOTE]
> 
> Podczas uzyskiwania dostępu do jednostek w usłudze Media Services, należy ustawić określonych pól nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [Instalatora w celu tworzenia interfejsu API REST usługi Media](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje o tym, jak połączyć się z interfejsem API usługi AMS, zobacz [dostęp do interfejsu API usługi multimediów Azure przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości wyczyść
### <a id="create_asset_delivery_policy"></a>Tworzenie zasad dostarczania elementów zawartości
Następujące żądanie HTTP tworzy zasady dostarczania elementu zawartości, która określa nie chcesz stosować szyfrowania dynamicznego i że dostarczymy strumienia w dowolnym z następujących protokołów:  Protokoły MPEG DASH, HLS i Smooth Streaming. 

Instrukcje dotyczące wartości, jakie można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

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

### <a id="link_asset_with_asset_delivery_policy"></a>Link zasobu ze wszystkimi zasad dostarczania elementów zawartości
Następujące żądanie HTTP łączy zasad dostarczania elementów zawartości do określonego zasobu.

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
Podczas określania zasad dostarczania DynamicEnvelopeEncryption, musisz upewnij się połączyć element zawartości klucza zawartości typu EnvelopeEncryption. Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Pobierz adres URL dostarczania
Pobierz adres URL dostarczania dla metody dostarczania określony klucz zawartości utworzonej w poprzednim kroku. Klient używa zwróconego adresu URL, aby zażądać klucza AES lub PlayReady licencji w celu odtwarzania zawartości chronionej.

Określ typ adresu URL do pobrania w treści żądania HTTP. W przypadku ochrony zawartości przy użyciu technologii PlayReady, żądanie adresu URL pozyskiwania licencji PlayReady usługi Media Services, używając 1 keyDeliveryType: {"keyDeliveryType": 1}. W przypadku ochrony zawartości przy użyciu szyfrowania koperty żądania na adres URL pozyskiwania kluczy, określając 2-keyDeliveryType: {"keyDeliveryType": 2}.

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
Tworzy następujące żądanie HTTP **AssetDeliveryPolicy** skonfigurowanego Zastosuj szyfrowanie dynamiczne koperty (**DynamicEnvelopeEncryption**) do **HLS** Protokół (w tym przykładzie innych protokołów zostanie zablokowany przesyłania strumieniowego). 

Instrukcje dotyczące wartości, jakie można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

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


### <a name="link-asset-with-asset-delivery-policy"></a>Link zasobu ze wszystkimi zasad dostarczania elementów zawartości
Zobacz [Linku zasobu ze wszystkimi zasad dostarczania elementów zawartości](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Utwórz klucz zawartości typu CommonEncryption i połącz go do elementu zawartości
Podczas określania zasad dostarczania DynamicCommonEncryption, musisz upewnij się połączyć element zawartości klucza zawartości typu CommonEncryption. Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Pobierz adres URL dostarczania
Pobierz adres URL dostarczania dla technologii PlayReady metody dostarczania zawartości klucza utworzonego w poprzednim kroku. Klient używa zwróconego adresu URL żądania licencji PlayReady w celu odtwarzania zawartości chronionej. Aby uzyskać więcej informacji, zobacz [uzyskać adres URL dostarczania](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Tworzenie zasad dostarczania elementów zawartości
Tworzy następujące żądanie HTTP **AssetDeliveryPolicy** skonfigurowany do stosowania dynamicznego szyfrowania common encryption (**DynamicCommonEncryption**) do **Smooth Streaming**protokołu (w tym przykładzie innych protokołów zostanie zablokowany przesyłania strumieniowego). 

Instrukcje dotyczące wartości, jakie można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

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


Jeśli chcesz chronić zawartość przy użyciu technologii Widevine DRM, zaktualizuj wartości AssetDeliveryConfiguration WidevineLicenseAcquisitionUrl (który ma wartość 7) i określ adres URL usługi dostarczania licencji. Aby łatwiej dostarczać licencje Widevine, można użyć następujących partnerów usługi AMS: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Na przykład: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> W przypadku szyfrowania przy użyciu metody Widevine, tylko będzie można dostarczać przy użyciu DASH. Upewnij się określić DASH (2) w Protokół dostarczania elementów zawartości.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Link zasobu ze wszystkimi zasad dostarczania elementów zawartości
Zobacz [Linku zasobu ze wszystkimi zasad dostarczania elementów zawartości](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Typy używane podczas definiowania AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Następujące wyliczenia w tym artykule opisano wartości ustawione przez protokół dostarczania elementów zawartości.

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

Następujące wyliczenia w tym artykule opisano wartości, które można ustawić dla tego typu zasad dostarczania elementu zawartości.  

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

Następujące wyliczenia w tym artykule opisano wartości, których można użyć do konfigurowania metodę dostarczania zawartości klucza do klienta.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Następujące wyliczenia w tym artykule opisano wartości, które można ustawić, aby skonfigurować klucze używane do uzyskania konkretnej konfiguracji do zasad dostarczania elementu zawartości.

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

