---
title: Konfigurowanie zasad dostarczania zasobów za pomocą narzędzia .NET SDK | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować różne zasady dostarczania zasobów za pomocą narzędzia Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974516"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurowanie zasad dostarczania zasobów za pomocą narzędzia .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Omówienie
Jeśli planujesz dostarczanie zaszyfrowanych zasobów, jednym z kroków w przepływie pracy dostarczania zawartości usługi Media Services jest konfigurowanie zasad dostarczania dla zasobów. Zasady dostarczania zasobów informują usługę Media Services, w jaki sposób mają być dostarczane zasób: do którego protokołu przesyłania strumieniowego powinien być dynamicznie pakowany (na przykład MPEG DASH, HLS, Smooth Streaming lub wszystkie), niezależnie od tego, czy chcesz dynamicznie szyfrować zasobów i sposobu (koperta lub wspólne szyfrowanie).

W tym artykule omówiono, dlaczego i jak tworzyć i konfigurować zasady dostarczania zasobów.

>[!NOTE]
>Po utworzeniu konta AMS **domyślny** punkt końcowy przesyłania strumieniowego jest dodawany do twojego konta w stanie **Zatrzymane.** Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
>Ponadto, aby móc korzystać z dynamicznego pakowania i szyfrowania dynamicznego, zasób musi zawierać zestaw adaptacyjnych plików MP4 o szybkości transmisji bitów lub adaptacyjnych plików Smooth Streaming o szybkości transmisji bitów.

Do tego samego zasobu można zastosować różne zasady. Na przykład można zastosować szyfrowanie PlayReady do szyfrowania Smooth Streaming i AES Envelope do MPEG DASH i HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem jest przypadek, w którym nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli chcesz dostarczyć zaszyfrowany zasób magazynu, musisz skonfigurować zasady dostarczania zasobu. Przed przesyłaniem strumieniowego zasobu serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania. Na przykład, aby dostarczyć zasób zaszyfrowany za pomocą klucza szyfrowania za pomocą klucza szyfrowania Advanced Encryption Standard (AES), ustaw typ zasad na **DynamicEnvelopeEncryption**. Aby usunąć szyfrowanie magazynu i przesłać strumieniowo zasób w wyczyszcz, ustaw typ zasad na **NoDynamicEncryption**. Przykłady, które pokazują, jak skonfigurować te typy zasad.

W zależności od sposobu konfigurowania zasad dostarczania zasobów można dynamicznie pakować, szyfrować i przesyłać strumieniowo następujące protokoły przesyłania strumieniowego: Płynne przesyłanie strumieniowe, HLS i MPEG DASH.

Na poniższej liście przedstawiono formaty używane do przesyłania strumieniowego Smooth, HLS i DASH.

Płynne przesyłanie strumieniowe:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

Hls:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Zagadnienia do rozważenia
* Przed usunięciem AssetDeliveryPolicy należy usunąć wszystkie lokalizatory przesyłania strumieniowego skojarzone z zasobem. Później można utworzyć nowe lokalizatory przesyłania strumieniowego, w razie potrzeby, z nowym AssetDeliveryPolicy.
* Lokalizatora przesyłania strumieniowego nie można utworzyć na zaszyfrowanym zasobie magazynu, gdy nie ustawiono zasad dostarczania zasobów.  Jeśli zasób nie jest zaszyfrowany, system umożliwia utworzenie lokalizatora i strumieniowanie zasobu w postaci wyczyszczonej bez zasad dostarczania zasobów.
* Z jednym zasobem może być skojarzonych wiele zasad dostarczania zasobów, ale można określić tylko jeden sposób obsługi danego składnika AssetDeliveryProtocol.  Znaczenie, jeśli spróbujesz połączyć dwie zasady dostarczania, które określają AssetDeliveryProtocol.SmoothStreaming protokołu, który spowoduje błąd, ponieważ system nie wie, który z nich ma być stosowany, gdy klient sprawia, że żądanie płynnego przesyłania strumieniowego.
* Jeśli masz zasób z istniejącym lokalizatorem przesyłania strumieniowego, nie możesz połączyć nowej zasady z zasobem (możesz odłączyć istniejące zasady od zasobu lub zaktualizować zasady dostarczania skojarzone z zasobem).  Najpierw musisz usunąć lokalizator przesyłania strumieniowego, dostosować zasady, a następnie ponownie utworzyć lokalizator przesyłania strumieniowego.  Podczas ponownego tworzenia lokalizatora przesyłania strumieniowego można użyć tego samego identyfikatora lokalizatora, ale należy upewnić się, że nie spowoduje to problemów dla klientów, ponieważ zawartość może być buforowana przez źródłową lub niższą sieć CDN.

## <a name="clear-asset-delivery-policy"></a>Wyczyść zasady dostarczania zasobów

Następująca metoda **ConfigureClearAssetDeliveryPolicy** określa, aby nie stosować szyfrowania dynamicznego i dostarczać strumień w dowolnym z następujących protokołów: MPEG DASH, HLS i Smooth Streaming protokołów. Można zastosować te zasady do zasobów zaszyfrowanych magazynu.

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz [Typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zasady dostarczania zasobów DynamicCommonEncryption

Następująca metoda **CreateAssetDeliveryPolicy** tworzy **AssetDeliveryPolicy,** która jest skonfigurowana do stosowania dynamicznego szyfrowania wspólnego **(DynamicCommonEncryption**) do płynnego protokołu przesyłania strumieniowego (inne protokoły zostaną zablokowane przed przesyłaniem strumieniowym). Metoda przyjmuje dwa parametry: **Zasób** (zasób, do którego chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości typu **CommonEncryption,** aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#common_contentkey)).

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz [Typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Usługa Azure Media Services umożliwia również dodanie szyfrowania Widevine. W poniższym przykładzie pokazano zarówno PlayReady i Widevine dodawane do zasad dostarczania zasobów.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Podczas szyfrowania za pomocą Widevine, będzie można dostarczyć tylko przy użyciu DASH. Upewnij się, że określono dash w protokole dostarczania zasobów.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zasady dostarczania zasobów DynamicEnvelopeEncryption
Następująca metoda **CreateAssetDeliveryPolicy** tworzy **AssetDeliveryPolicy,** która jest skonfigurowana do stosowania szyfrowania kopert dynamicznych **(DynamicEnvelopeEncryption**) do protokołów Płynne przesyłanie strumieniowe, HLS i DASH (jeśli zdecydujesz się nie określić niektórych protokołów, zostaną one zablokowane w trybie przesyłania strumieniowego). Metoda przyjmuje dwa parametry: **Zasób** (zasób, do którego chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości typu **EnvelopeEncryption,** aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz [Typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typy używane podczas definiowania AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

W poniższym wyliczeniu opisano wartości, które można ustawić dla protokołu dostarczania zasobów.

```csharp
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
```
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyTytytyty

W poniższym wyliczeniu opisano wartości, które można ustawić dla typu zasad dostarczania zasobów.  
```csharp
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
```
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

W poniższym wyliczeniu opisano wartości, których można użyć do skonfigurowania metody dostarczania klucza zawartości do klienta.
  ```csharp  
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
```
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

W poniższym wyliczeniu opisano wartości, które można ustawić w celu skonfigurowania kluczy używanych do uzyskania określonej konfiguracji dla zasad dostarczania zasobów.
```csharp
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
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

