---
title: Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak dynamicznie szyfrować za pomocą AES-128 i korzystać z usługi dostarczania kluczy.
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
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269733"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Za pomocą usługi Media Services można dostarczać transmisje strumieniowe na żywo HTTP (HLS) i płynne przesyłanie strumieniowe szyfrowane za pomocą usługi AES przy użyciu 128-bitowych kluczy szyfrowania. Usługa Media Services udostępnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania autoryzowanym użytkownikom. Jeśli chcesz, aby usługa Media Services szyfrował zasób, należy skojarzyć klucz szyfrowania z zasobem, a także skonfigurować zasady autoryzacji dla klucza. Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby ustalić, czy użytkownik jest autoryzowany do uzyskania klucza, usługa ocenia zasady autoryzacji określone dla klucza.

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów: [prosty token sieci Web](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) i [token sieci Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy).

Aby móc skorzystać z szyfrowania dynamicznego, należy posiadać element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Należy również skonfigurować zasady dostarczania zasobu (opisane w dalszej części tego artykułu). Następnie na podstawie formatu określonego w adresie URL przesyłanego strumienia serwer przesyłania strumieniowego na żądanie zapewnia, że strumień jest dostarczany za pomocą wybranego protokołu. W rezultacie musisz przechowywać i płacić tylko za pliki w formacie pojedynczego magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta.

Ten artykuł jest przydatny dla deweloperów, którzy pracują nad aplikacjami, które dostarczają nośniki chronione. W tym artykule pokazano, jak skonfigurować usługę dostarczania kluczy za pomocą zasad autoryzacji, tak aby tylko autoryzowani klienci mogli odbierać klucze szyfrowania. Pokazuje również, jak używać szyfrowania dynamicznego.

Aby uzyskać informacje na temat szyfrowania zawartości za pomocą zaawansowanego standardu szyfrowania (AES) w celu dostarczenia do przeglądarki Safari w systemie macOS, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Aby uzyskać omówienie sposobu ochrony zawartości multimedialnej za pomocą szyfrowania AES, zobacz [ten film .](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Dynamiczne szyfrowanie AES-128 i przepływ pracy usługi dostarczania kluczy

Wykonaj następujące ogólne kroki podczas szyfrowania zasobów za pomocą usługi AES przy użyciu usługi dostarczania kluczy usługi Media Services, a także przy użyciu szyfrowania dynamicznego:

1. [Utwórz zasób i przekaż pliki do zasobu](media-services-protect-with-aes128.md#create_asset).

2. [Zakoduj zasób zawierający plik do adaptacyjnego zestawu MP4 z szybkością transmisji bitów](media-services-protect-with-aes128.md#encode_asset).

3. [Utwórz klucz zawartości i skojarz go z zakodowanym zasobem](media-services-protect-with-aes128.md#create_contentkey). W usłudze Media Services klucz zawartości zawiera klucz szyfrowania elementu zawartości.

4. [Skonfiguruj zasady autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy). Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient musi spełnić te zasady, aby klucz zawartości został do niego dostarczony.

5. [Skonfiguruj zasady dostarczania zasobu](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfiguracja zasad dostarczania zawiera adres URL pozyskiwania klucza i wektor inicjowania (IV). (AES-128 wymaga tego samego iv do szyfrowania i odszyfrowywania.) Konfiguracja obejmuje również protokół dostarczania (na przykład MPEG-DASH, HLS, Smooth Streaming lub wszystkie) i typ szyfrowania dynamicznego (na przykład koperta lub brak szyfrowania dynamicznego).

    Dla każdego protokołu dotyczącego danego elementu zawartości można stosować inne zasady. Na przykład dla protokołu Smooth/DASH można zastosować szyfrowanie PlayReady, zaś dla protokołu HLS — szyfrowanie AES Envelope. Wszystkie protokoły, które nie są zdefiniowane w zasadach dostarczania są blokowane z przesyłania strumieniowego. (Przykładem jest dodanie jednej zasady, która określa tylko HLS jako protokół). Wyjątek stanowi sytuacja, gdy w ogóle nie zdefiniowano zasad dostarczania zasobów. Wówczas są dozwolone wszystkie protokoły.

6. [Utwórz lokalizator OnDemand,](media-services-protect-with-aes128.md#create_locator) aby uzyskać adres URL przesyłania strumieniowego.

W tym artykule pokazano [również, jak aplikacja kliencka może zażądać klucza z usługi dostarczania kluczy.](media-services-protect-with-aes128.md#client_request)

Na końcu artykułu można znaleźć pełny [przykład platformy .NET.](media-services-protect-with-aes128.md#example)

Poniższa ilustracja pokazuje opisany wcześniej przepływ pracy. Jest tu używany token do uwierzytelniania.

![Ochrona za pomocą AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

W dalszej części tego artykułu przedstawiono wyjaśnienia, przykłady kodu i łącza do tematów, które pokazują, jak osiągnąć zadania wcześniej opisane.

## <a name="current-limitations"></a>Bieżące ograniczenia
W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Tworzenie zasobu i przekazywanie plików do zasobu
W celu kodowania i przesyłania strumieniowego filmów oraz zarządzania nimi należy najpierw przekazać zawartość do usługi Media Services. Po przekazaniu zawartości jest ona bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego. 

Aby uzyskać więcej informacji, zobacz [Przekazywanie plików na konto usługi Media Services](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Przekodowywanie elementu zawartości zawierającego plik na zestaw MP4 o adaptacyjnej szybkości bitowej
W przypadku szyfrowania dynamicznego należy utworzyć element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Następnie, na podstawie określonego formatu w żądaniu manifestu lub fragmentu, serwer przesyłania strumieniowego na żądanie zapewnia, że otrzymasz strumień w wybranym protokole. Następnie wystarczy przechowywać i płacić za pliki w formacie pojedynczego magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta. Aby uzyskać więcej informacji, zobacz [Omówienie dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan „Uruchomiony”. 
>
>Ponadto, aby korzystać z dynamicznego pakowania i szyfrowania dynamicznego, zasób musi zawierać zestaw adaptacyjnych plików MP4 o szybkości transmisji bitów lub adaptacyjnych plików Smooth Streaming o szybkości transmisji bitów.

Aby uzyskać instrukcje na temat kodowania, zobacz [Kodowanie elementów zawartości przy użyciu standardowego kodera multimediów](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Tworzenie klucza zawartości i kojarzenie go z zakodowanym zasobem
W usłudze Media Services klucz zawartości zawiera klucz, za pomocą którego chcesz zaszyfrować element zawartości.

Aby uzyskać więcej informacji, zobacz [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient (gracz) musi spełniać zasady, zanim klucz może zostać dostarczony do klienta. Zasady autoryzacji klucza zawartości mogą mieć co najmniej jedno ograniczenie autoryzacji, otwarte, ograniczenie tokenu lub ograniczenie adresu IP.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-dotnet-configure-content-key-auth-policy.md).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurowanie zasad dostarczania elementu zawartości
Skonfiguruj zasady dostarczania dla swojego elementu zawartości. Niektóre elementy objęte konfiguracją zasad dostarczania elementów zawartości:

* Adres URL pozyskiwania klucza. 
* Wektor inicjowania (IV) do użycia do szyfrowania koperty. AES-128 wymaga tego samego iv do szyfrowania i odszyfrowywania. 
* Protokół dostarczania elementów zawartości (na przykład MPEG DASH, HLS, Smooth Streaming lub wszystkie z nich).
* Typ szyfrowania dynamicznego (na przykład koperta AES) lub brak szyfrowania dynamicznego. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementu zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Tworzenie lokalizatora OnDemand przesyłania strumieniowego w celu pobrania adresu URL przesyłania strumieniowego
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

Możesz przetestować strumień za pomocą usługi [Azure Media Services Player](https://aka.ms/azuremediaplayer).

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>W jaki sposób klient może zażądać klucza z usługi dostarczania kluczy?
W poprzednim kroku skonstruowano adres URL, który wskazuje plik manifestu. Klient musi wyodrębnić niezbędne informacje z plików manifestu przesyłania strumieniowego, aby złożyć żądanie do usługi dostarczania kluczy.

### <a name="manifest-files"></a>Pliki manifestów
Klient musi wyodrębnić wartość adresu URL (który zawiera również identyfikator klucza zawartości [kid]) z pliku manifestu. Następnie klient próbuje uzyskać klucz szyfrowania z usługi dostarczania kluczy. Klient musi również wyodrębnić wartość IV i użyć jej do odszyfrowania strumienia. Poniższy fragment kodu pokazuje `<Protection>` element manifestu Płynne przesyłanie strumieniowe:

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

W przypadku HLS manifest główny jest podzielony na pliki segmentu. 

Na przykład manifest główny to:\/http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Zawiera listę nazw plików segmentu.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Jeśli otworzysz jeden z plików segmentu w edytorze\/tekstu (np. http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), zawiera #EXT-X-KEY, co oznacza, że plik jest zaszyfrowany.

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
>Jeśli planujesz grać w hls szyfrowany AES w Safari, zobacz [ten blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Żądanie klucza z usługi dostarczania kluczy

Poniższy kod pokazuje, jak wysłać żądanie do usługi dostarczania kluczy usługi Media Services przy użyciu uri dostarczania klucza (który został wyodrębniony z manifestu) i tokenu. (Ten artykuł nie wyjaśnia, jak uzyskać SWTs z STS.)

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

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Chroń swoją zawartość za pomocą AES-128 za pomocą platformy .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko programistyczne i wypełnij plik app.config informacjami o połączeniu, zgodnie z opisem w programie [Media Services development za pomocą platformy .NET](media-services-dotnet-how-to-use.md).

2. Dodaj następujące elementy do appSettings, zgodnie z definicją w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Przykład

Zastąp kod w pliku Program.cs kodem przedstawionym w tej sekcji.
 
>[!NOTE]
>Istnieje limit 1,000,000 zasad dla różnych zasad usługi Media Services (na przykład dla zasad lokalizatora lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni/uprawnień dostępu. Przykładem są zasady dla lokalizatorów przeznaczone do długotrwałego stosowania (nieprzekazywane zasady). Aby uzyskać więcej informacji, zobacz sekcję "Ogranicz zasady dostępu" w [sekcji Zarządzanie zasobami i podmiotami pokrewne za pomocą programu Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Upewnij się, że zaktualizowano zmienne, tak aby wskazywały foldery, w których znajdują się pliki danych wejściowych.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
