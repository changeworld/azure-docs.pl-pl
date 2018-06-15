---
title: Skonfiguruj zasady dostarczania zasobów przy użyciu zestawu .NET SDK | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować zasady dostarczania różnych zasobów z zestawu .NET SDK usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: juliako
ms.openlocfilehash: aee2477e0633974cba42ab26e102323cb9606810
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789453"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Skonfiguruj zasady dostarczania zasobów przy użyciu zestawu .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Przegląd
Jeśli planujesz trwałych dostarczania zaszyfrowane, jeden z kroków w przepływie pracy dostarczania zawartości Media Services konfiguruje zasady dostarczania zasobów. Zasady dostarczania elementu zawartości informuje usługi Media Services sposób dla zawartości dostarczanej: w których przesyłania strumieniowego protokołu powinna zawartości dynamicznie umieszczone (na przykład, MPEG DASH, HLS, Smooth Streaming lub wszystkie), czy ma być dynamicznego szyfrowania zawartości i w jaki sposób (koperty lub szyfrowania common encryption).

W tym artykule omówiono dlaczego i jak utworzyć i skonfigurować zasady dostarczania zasobów.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
>Ponadto aby można było korzystać z dynamicznego tworzenia pakietów i dynamicznego szyfrowania zawartości musi zawierać zestaw o adaptacyjnej szybkości bitowej MP4s lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Różnych zasad można zastosować do tego samego zasobu. Na przykład można zastosować szyfrowanie PlayReady do szyfrowania Smooth Streaming i szyfrowanie AES Envelope MPEG DASH i HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem jest przypadek, w którym nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli chcesz dostarczyć zasób zaszyfrowanych magazynu, należy skonfigurować zasady dostarczania elementu zawartości. Przed zawartości mogą być przesyłane strumieniowo, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieni zawartości przy użyciu zasady dostarczania określony. Na przykład aby dostarczania zawartości zaszyfrowane za pomocą klucza szyfrowania koperty Advanced Encryption (Standard AES), Ustaw typ zasad **DynamicEnvelopeEncryption**. Aby usunąć szyfrowanie magazynu i zasobów niezabezpieczona strumienia, Ustaw typ zasad **NoDynamicEncryption**. Wykonaj przykłady, które pokazują, jak skonfigurować te typy zasad.

W zależności od tego, jak można skonfigurować zasady dostarczania elementu zawartości, można dynamicznie pakietu, szyfrowania i przesyłania strumieniowego następujących protokołów: Smooth Streaming, HLS i MPEG DASH.

Na poniższej liście przedstawiono formaty umożliwia strumienia Smooth, HLS i KRESKI.

Funkcje Smooth Streaming:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

HLS:

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=m3u8-aapl) przesyłania strumieniowego

MPEG DASH

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=mpd-time-csf) przesyłania strumieniowego

## <a name="considerations"></a>Zagadnienia do rozważenia
* Przed usunięciem AssetDeliveryPolicy, należy usunąć wszystkie lokalizatory przesyłania strumieniowego, skojarzone z elementu zawartości. Później można utworzyć nowego lokalizatory przesyłania strumieniowego, w razie potrzeby z nowego AssetDeliveryPolicy.
* Nie można utworzyć Lokalizator przesyłania strumieniowego magazynu trwałego zaszyfrowane, jeśli ustawiono nie zasad dostarczania elementów zawartości.  Jeśli element zawartości nie jest szyfrowany w magazynie, system będzie umożliwiają tworzenie lokalizatora i strumienia zasobów w zwykłym bez zasad dostarczania elementów zawartości.
* Może mieć wiele zasady dostarczania zasobów skojarzonych z pojedynczego zasobu, ale można określić tylko jeden sposób obsługi danego AssetDeliveryProtocol.  Co oznacza, spróbuj połączyć dwie zasady dostarczania, które określają protokół AssetDeliveryProtocol.SmoothStreaming, który spowoduje błąd, ponieważ system nie może określić, które co ma to zastosowanie, gdy klient przesyła żądanie Smooth Streaming.
* Jeśli masz zasób z istniejących Lokalizator przesyłania strumieniowego nie może połączyć nowe zasady w zasobie (możesz odłączyć istniejące zasady z zasobu, lub zaktualizowania zasad dostarczania skojarzone z elementu zawartości).  Należy najpierw usuń Lokalizator przesyłania strumieniowego, Dostosuj zasady, a następnie ponownie utwórz Lokalizator przesyłania strumieniowego.  Można użyć tego samego locatorId podczas ponownego tworzenia Lokalizator przesyłania strumieniowego, ale należy upewnić się, że nie będzie powodować problemy dla klientów od zawartości mogą być buforowane źródła lub podrzędne CDN.

## <a name="clear-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości wyczyść

Następujące **ConfigureClearAssetDeliveryPolicy** metody określa nie dotyczyć szyfrowania dynamicznego i dostarczać strumienia w jednym z następujących protokołów: MPEG DASH, HLS i Smooth Streaming protokołów. Można stosować te zasady do zasobów magazynu szyfrowane.

Aby uzyskać informacje na jakie wartości można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typów używanych podczas definiowania AssetDeliveryPolicy](#types) sekcji.

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

Następujące **CreateAssetDeliveryPolicy** metoda tworzy **AssetDeliveryPolicy** skonfigurowanego do zastosowania dynamicznego szyfrowania common encryption (**DynamicCommonEncryption**) do sprawnego protokołu przesyłania strumieniowego (inne protokoły będzie blokowany przesyłania strumieniowego). Metoda przyjmuje dwa parametry: **zasobów** (zasobów, do której chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości **CommonEncryption** typu dla więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#common_contentkey)).

Aby uzyskać informacje na jakie wartości można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typów używanych podczas definiowania AssetDeliveryPolicy](#types) sekcji.

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

Usługa Azure Media Services umożliwia również dodanie Widevine szyfrowania. W poniższym przykładzie pokazano zarówno PlayReady i Widevine dodawany do zasad dostarczania elementów zawartości.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
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
> W przypadku szyfrowania przy użyciu metody Widevine, tylko będzie mogła dostarczać przy użyciu kreska. Upewnij się określić DASH w Protokół dostarczania elementów zawartości.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zasady dostarczania elementu zawartości DynamicEnvelopeEncryption
Następujące **CreateAssetDeliveryPolicy** metoda tworzy **AssetDeliveryPolicy** skonfigurowanego na zastosowanie szyfrowania dynamicznego koperty (**DynamicEnvelopeEncryption**) do protokołów Smooth Streaming, HLS i KRESKI (Jeśli zdecydujesz nie określać niektóre protokoły, będą blokowani z przesyłania strumieniowego). Metoda przyjmuje dwa parametry: **zasobów** (zasobów, do której chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości **EnvelopeEncryption** typu Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Aby uzyskać informacje na jakie wartości można określić podczas tworzenia AssetDeliveryPolicy, zobacz [typów używanych podczas definiowania AssetDeliveryPolicy](#types) sekcji.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
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

Następujące wyliczenia opisano wartości, które można ustawić dla Protokół dostarczania elementów zawartości.

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

Następujące wyliczenia opisano wartości, które można ustawić dla typu zasady dostarczania elementu zawartości.  
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

Następujące wyliczenia opisano wartości, które służy do konfigurowania metody dostarczania zawartości klucza do klienta.
  ```csharp  
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
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Następujące wyliczenia opisano wartości, które można ustawić, aby skonfigurować klucze służące do pobrania konfiguracji określonych dla zasad dostarczania elementów zawartości.
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

