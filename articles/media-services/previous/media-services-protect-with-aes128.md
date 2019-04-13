---
title: Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy | Dokumentacja firmy Microsoft
description: Dostarczanie zawartości szyfrowane przy użyciu kluczy szyfrowania AES 128-bitowy przy użyciu usługi Microsoft Azure Media Services. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. W tym temacie przedstawiono sposób dynamicznego szyfrowania przy użyciu algorytmu AES-128 i korzystania z usługi dostarczania kluczy.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 6f76d6aed8dc5eed3dbf673b265c404f27b0536d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526830"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługa Media Services umożliwia dostarczanie HTTP Live Streaming (HLS) i Smooth Streaming, zaszyfrowany przy użyciu AES przy użyciu kluczy szyfrowania 128-bitowego. Media Services udostępnia również usługa dostarczania kluczy, która dostarcza kluczy szyfrowania do autoryzowanych użytkowników. Jeśli chcesz, aby usługi Media Services, aby zaszyfrować element zawartości, skojarz klucz szyfrowania z zawartości i również skonfigurować zasady autoryzacji klucza. Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania AES. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów: [prosty token sieci Web](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) i [token sieci Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy).

Aby móc skorzystać z szyfrowania dynamicznego, należy posiadać element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Należy także skonfigurować zasadę dostarczania elementu zawartości (opisane w dalszej części tego artykułu). Następnie na podstawie formatu określonego w adresie URL przesyłanego strumienia serwer przesyłania strumieniowego na żądanie zapewnia, że strumień jest dostarczany za pomocą wybranego protokołu. W rezultacie należy przechowywać i opłacać tylko dla plików w jednym formacie magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta.

Ten artykuł jest przydatny dla deweloperów pracujących nad aplikacjami, które dostarczają chronionych. Tego artykułu dowiesz się, jak skonfigurować usługę dostarczania kluczy przy użyciu zasad autoryzacji, tak aby tylko autoryzowani klienci mogli odbierać kluczy szyfrowania. Pokazano również, jak używać szyfrowania dynamicznego.

Aby uzyskać informacje na temat sposobu szyfrowanie zawartości przy użyciu Advanced Encryption Standard (AES) do dostarczania do programu Safari w systemie macOS, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Aby uzyskać omówienie sposobu ochrony Twojej zawartości multimediów przy użyciu szyfrowania AES, zobacz [ten film wideo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Dynamiczne szyfrowanie AES-128 i przepływu pracy usługi dostarczania kluczy

Podczas szyfrowania zasoby za pomocą szyfrowania AES, za pomocą usługi dostarczania kluczy usługi Media Services, a także za pomocą szyfrowania dynamicznego, należy wykonać następujące czynności ogólne:

1. [Utworzenie elementu zawartości i przekaż pliki do niego](media-services-protect-with-aes128.md#create_asset).

2. [Przekoduj element zawartości zawierający plik, aby adaptacyjną szybkością transmisji bitów zestawu plików MP4](media-services-protect-with-aes128.md#encode_asset).

3. [Utwórz klucz zawartości i skojarz go z zakodowanym elementem zawartości](media-services-protect-with-aes128.md#create_contentkey). W usłudze Media Services klucz zawartości zawiera klucz szyfrowania elementu zawartości.

4. [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy). Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient musi spełnić te zasady, aby klucz zawartości został do niego dostarczony.

5. [Skonfiguruj zasadę dostarczania elementu zawartości](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfiguracja zasad dostarczania obejmuje adres URL klucza pozyskiwania i wektor inicjowania (IV). (AES-128 wymaga tych samych IV do szyfrowania i odszyfrowywania). Konfiguracja obejmuje również protokół dostarczania (na przykład MPEG-DASH, HLS, Smooth Streaming lub wszystkie) i typ szyfrowania dynamicznego (na przykład, koperty lub nie szyfrowania dynamicznego).

    Dla każdego protokołu dotyczącego danego elementu zawartości można stosować inne zasady. Na przykład dla protokołu Smooth/DASH można zastosować szyfrowanie PlayReady, zaś dla protokołu HLS — szyfrowanie AES Envelope. Protokoły, które nie są zdefiniowane w zasadach dostarczania są blokowane z przesyłania strumieniowego. (Przykład jest w przypadku dodania jednych zasad jako protokół określający tylko HLS). Wyjątkiem jest przypadek, w którym nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas są dozwolone wszystkie protokoły.

6. [Utwórz Lokalizator OnDemand](media-services-protect-with-aes128.md#create_locator) można pobrać adresu URL przesyłania strumieniowego.

Artykuł przedstawia również [jak aplikacja kliencka może poprosić o klucz usługi dostarczania kluczy](media-services-protect-with-aes128.md#client_request).

Możesz znaleźć kompletna [przykład dla środowiska .NET](media-services-protect-with-aes128.md#example) na końcu tego artykułu.

Poniższa ilustracja pokazuje opisany wcześniej przepływ pracy. Jest tu używany token do uwierzytelniania.

![Ochrona z zastosowaniem standardu AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

W dalszej części tego artykułu zawiera wyjaśnienia, przykłady kodu i linki do tematów, w których pokazano, jak wykonać poprzednio opisane zadania.

## <a name="current-limitations"></a>Bieżące ograniczenia
W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.

## <a id="create_asset"></a>Utworzenie elementu zawartości i przekazywanie plików do elementu zawartości
W celu kodowania i przesyłania strumieniowego filmów oraz zarządzania nimi należy najpierw przekazać zawartość do usługi Media Services. Po przekazaniu zawartości jest ona bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego. 

Aby uzyskać więcej informacji, zobacz [Przekazywanie plików na konto usługi Media Services](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Przekoduj element zawartości zawierający plik na adaptacyjną szybkością transmisji bitów, ustawionych w formacie MP4
W przypadku szyfrowania dynamicznego należy utworzyć element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Następnie na podstawie określonego formatu w żądaniu manifestu lub fragment, serwer przesyłania strumieniowego na żądanie zapewnia, że strumień jest dostarczany za pomocą wybranego protokołu. Następnie wystarczy przechowywać i opłacać pliki w jednym formacie magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta. Aby uzyskać więcej informacji, zobacz [Omówienie dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan „Uruchomiony”. 
>
>Ponadto aby korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, element zawartości musi zawierać zbiór każdego pliku MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów.

Aby uzyskać instrukcje na temat kodowania, zobacz [Kodowanie elementów zawartości przy użyciu standardowego kodera multimediów](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Tworzenie klucza zawartości i kojarzenie go z zakodowanym elementem zawartości
W usłudze Media Services klucz zawartości zawiera klucz, za pomocą którego chcesz zaszyfrować element zawartości.

Aby uzyskać więcej informacji, zobacz [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient (odtwarzacz) musi spełnić te zasady, aby klucz może dostarczony do klienta. Zasady autoryzacji klucza zawartości mogą mieć jeden lub więcej ograniczeń, albo otworzyć, token ograniczenia lub ograniczenia adresów IP.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Konfigurowanie zasad dostarczania elementu zawartości
Skonfiguruj zasady dostarczania dla swojego elementu zawartości. Niektóre elementy objęte konfiguracją zasad dostarczania elementów zawartości:

* Adres URL pozyskiwania kluczy. 
* Wektor inicjalizacji (IV) do użycia podczas szyfrowania koperty. AES-128 wymaga tych samych IV do szyfrowania i odszyfrowywania. 
* Protokół dostarczania elementów zawartości (na przykład MPEG DASH, HLS, Smooth Streaming lub wszystkie z nich).
* Typ szyfrowania dynamicznego (na przykład szyfrowanie AES envelope) lub nie szyfrowania dynamicznego. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementu zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Tworzenie lokalizatora OnDemand przesyłania strumieniowego w celu pobrania adresu URL przesyłania strumieniowego
W przypadku metod Smooth Streaming, DASH lub HLS należy podać użytkownikowi adres URL przesyłania strumieniowego.

> [!NOTE]
> W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.
> 
> 

Aby uzyskać instrukcje dotyczące sposobu publikowania elementów zawartości i tworzenia adresu URL przesyłania strumieniowego, zobacz artykuł [Build a streaming URL](media-services-deliver-streaming-content.md) (Tworzenie adresu URL przesyłania strumieniowego).

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego
Pobierz token testowy, uwzględniając ograniczenia tokenu wybrane w zasadach autoryzacji klucza.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Możesz przetestować strumień za pomocą usługi [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a id="client_request"></a>Jak klient poprosić o klucz usługi dostarczania kluczy
W poprzednim kroku tworzony jest adres URL, który wskazuje plik manifestu. Twój klient musi wyodrębnić wymaganych informacji z przesyłania strumieniowego plików manifestu, aby wysyłać żądania do usługi dostarczania kluczy.

### <a name="manifest-files"></a>Pliki manifestu
Klient musi wyodrębnić adres URL (zawierający zawartość [Kącik] identyfikator klucza) wartość z pliku manifestu. Klient następnie próbuje uzyskać klucz szyfrowania z usługi dostarczania kluczy. Klient musi również wyodrębnić wartość IV i użyć go do odszyfrowywania strumienia. Poniższy fragment kodu przedstawia `<Protection>` elemencie manifestu Smooth Streaming:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

W przypadku HLS manifest główny jest dzielony na pliki segmentu. 

Na przykład to manifest głównego: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Zawiera listę nazw plików segmentu.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Jeśli otworzysz jeden z plików segmentu w edytorze tekstów (na przykład http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), zawiera ona #EXT-X-KEY, co oznacza, że plik jest zaszyfrowany.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Jeśli planujesz odtwarzania HLS z szyfrowaniem AES w przeglądarce Safari, zobacz [ten blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Zażądaj klucza usługi dostarczania kluczy

Poniższy kod przedstawia sposób wysyłania żądania do usługi dostarczania kluczy Media Services przy użyciu klucza dostarczania identyfikatora Uri (który został wyodrębniony z manifestu) i tokenu. (W tym artykule nie opisano sposób uzyskiwania SWTs od usługi STS.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Ochrona zawartości przy użyciu algorytmu AES-128 przy użyciu platformy .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko projektowe i umieść w pliku app.config informacje dotyczące połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md).

2. Dodaj następujące elementy do appSettings, zgodnie z definicją w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Przykład

Zastąp kod w pliku Program.cs kodem przedstawionym w tej sekcji.
 
>[!NOTE]
>Obowiązuje limit 1 000 000 zasad dla różnych zasad usługi Media Services (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego Identyfikatora zasad, jeśli zawsze używasz tych samych dni i uprawnień dostępu. Przykładem są zasady dla lokalizatorów przeznaczone do długotrwałego stosowania (nieprzekazywane zasady). Aby uzyskać więcej informacji, zobacz sekcję "Zasady dostępu Limit" w [zarządzanie zasobami i powiązanymi jednostkami za pomocą zestawu SDK .NET usługi Media Services](media-services-dotnet-manage-entities.md#limit-access-policies).

Upewnij się, że zaktualizowano zmienne, tak aby wskazywały foldery, w których znajdują się pliki danych wejściowych.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
