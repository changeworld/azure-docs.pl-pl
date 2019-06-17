---
title: Konfigurowanie zasad dostarczania elementów zawartości przy użyciu zestawu .NET SDK | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono konfigurowanie zasad dostarczania elementów zawartości innego przy użyciu usługi Azure Media Services .NET SDK.
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
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465613"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurowanie zasad dostarczania elementów zawartości przy użyciu zestawu SDK platformy .NET
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Omówienie
Jeśli planujesz zasoby dostarczanie zaszyfrowanych jedną z czynności w przepływie pracy dostarczania zawartości usługi Media Services służy do konfigurowania zasad dostarczania zasobów. Zasady dostarczania elementu zawartości informuje Media Services, jak chcesz uzyskać element zawartości został dostarczony: do protokołu przesyłania strumieniowego powinny element zawartości można dynamicznie spakować (na przykład, między innymi MPEG DASH, HLS, Smooth Streaming lub wszystkie), czy chcesz szyfrować dynamicznie element zawartości i w jaki sposób (koperty lub szyfrowania common encryption).

W tym artykule omówiono, dlaczego i jak tworzenie i konfigurowanie zasad dostarczania elementów zawartości.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
>Ponadto aby można było korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego element zawartości musi zawierać zestaw każdego pliku MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Różne zasady można zastosować do tego samego zasobu. Na przykład można zastosować szyfrowanie PlayReady Smooth Streaming i szyfrowanie AES Envelope szyfrowanie MPEG DASH i HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem jest przypadek, w którym nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli użytkownik chce dostarczać zaszyfrowane trwałego magazynu, należy skonfigurować zasady dostarczania elementu zawartości. Zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu zasad dostarczania określony. Na przykład do dostarczania elementów zawartości szyfrowane przy użyciu klucza szyfrowania koperty Advanced Encryption Standard (AES), Ustaw typ zasad na **DynamicEnvelopeEncryption**. Aby usunąć szyfrowania magazynu i przesyłanie strumieniowe zawartości niezaszyfrowane, Ustaw typ zasad **NoDynamicEncryption**. Postępuj zgodnie z przykładami pokazującymi sposób konfigurowania następujących typów zasad.

W zależności od tego, jak można skonfigurować zasady dostarczania elementu zawartości można dynamicznie pakietu, szyfrowanie i przesyłanie strumieniowe następujących protokołów: Smooth Streaming, HLS i MPEG DASH.

Na poniższej liście przedstawiono formaty umożliwiają przesyłanie strumieniowe Smooth, HLS i KRESKI.

Zestaw Smooth Streaming:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

HLS:

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Zagadnienia do rozważenia
* Przed usunięciem AssetDeliveryPolicy, należy usunąć wszystkie lokalizatory przesyłania strumieniowego, skojarzone z elementem zawartości. Później można utworzyć nowego lokalizatory przesyłania strumieniowego, w razie potrzeby, za pomocą nowego AssetDeliveryPolicy.
* Nie można utworzyć Lokalizator przesyłania strumieniowego magazynu trwałego zaszyfrowane, jeśli skonfigurowano nie zasad dostarczania elementów zawartości.  Jeśli zasób nie jest szyfrowany w magazynie, system będzie można utworzyć i przesyłanie strumieniowe zasób w zabezpieczeniu bez zasad dostarczania elementu zawartości.
* Może mieć wiele zasad dostarczania elementów zawartości skojarzone z pojedynczego zasobu, ale można określić tylko jeden sposób obsługi danego AssetDeliveryProtocol.  Co oznacza, Jeśli spróbujesz połączyć dwie zasady dostarczania, które określają protokół AssetDeliveryProtocol.SmoothStreaming, który spowoduje wystąpienie błędu, ponieważ system nie może określić, którego, w którym ma się są stosowane, gdy klient wysyła żądanie Smooth Streaming.
* Jeśli masz element zawartości za pomocą Lokalizator przesyłania strumieniowego nie można połączyć nowych zasad do elementu zawartości (możesz odłączyć istniejące zasady z niego, lub zaktualizowania zasad dostarczania, skojarzony element zawartości).  Musisz najpierw usuń Lokalizator przesyłania strumieniowego, Dostosuj zasady, a następnie ponownie utwórz Lokalizator przesyłania strumieniowego.  Podczas ponownego tworzenia Lokalizator przesyłania strumieniowego, ale należy upewnić się, że, nie będzie powodować problemy dla klientów od zawartości mogą być buforowane przez źródło lub podrzędnego usługi CDN, możesz użyć tego samego locatorId.

## <a name="clear-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości wyczyść

Następujące **ConfigureClearAssetDeliveryPolicy** metody określa nie chcesz stosować szyfrowania dynamicznego i że dostarczymy strumienia w dowolnym z następujących protokołów:  Protokoły MPEG DASH, HLS i Smooth Streaming. Można zastosować tę zasadę do swoich zasobów szyfrowany w magazynie.

Instrukcje dotyczące wartości, jakie można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości DynamicCommonEncryption

Następujące **CreateAssetDeliveryPolicy** metoda tworzy **AssetDeliveryPolicy** skonfigurowany do stosowania dynamicznego szyfrowania common encryption (**DynamicCommonEncryption**) do sprawnego protokołu przesyłania strumieniowego (inne protokoły zostanie zablokowany przesyłania strumieniowego). Ta metoda przyjmuje dwa parametry: **Zasób** (zasobów, do której chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości **CommonEncryption** typu, aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#common_contentkey)).

Instrukcje dotyczące wartości, jakie można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.

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

Usługa Azure Media Services umożliwia również dodanie Widevine szyfrowania. Poniższy przykład demonstruje, PlayReady i Widevine dodawany do zasad dostarczania elementów zawartości.

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
> W przypadku szyfrowania przy użyciu metody Widevine, tylko będzie można dostarczać przy użyciu DASH. Upewnij się określić DASH w Protokół dostarczania elementów zawartości.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości DynamicEnvelopeEncryption
Następujące **CreateAssetDeliveryPolicy** metoda tworzy **AssetDeliveryPolicy** skonfigurowanego Zastosuj szyfrowanie dynamiczne koperty (**DynamicEnvelopeEncryption**) na potrzeby protokołu Smooth Streaming, HLS i KRESKI (Jeśli zdecydujesz nie określać niektóre protokoły, będą blokowani z przesyłania strumieniowego). Ta metoda przyjmuje dwa parametry: **Zasób** (zasobów, do której chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości **EnvelopeEncryption** typu, aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Instrukcje dotyczące wartości, jakie można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typy używane podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

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

## <a id="types"></a>Typy używane podczas definiowania AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

Następujące wyliczenia w tym artykule opisano wartości ustawione przez protokół dostarczania elementów zawartości.

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
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

Następujące wyliczenia w tym artykule opisano wartości, które można ustawić dla tego typu zasad dostarczania elementu zawartości.  
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
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

Następujące wyliczenia w tym artykule opisano wartości, których można użyć do konfigurowania metodę dostarczania zawartości klucza do klienta.
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
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Następujące wyliczenia w tym artykule opisano wartości, które można ustawić, aby skonfigurować klucze używane do uzyskania konkretnej konfiguracji do zasad dostarczania elementu zawartości.
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
## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

