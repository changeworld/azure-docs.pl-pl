---
title: Przesyłanie strumieniowe fairplay w trybie offline dla systemu iOS za pomocą usługi Azure Media Services w wersji 3
description: W tym temacie przedstawiono omówienie i pokazano, jak korzystać z usługi Azure Media Services do dynamicznego szyfrowania zawartości http live streaming (HLS) za pomocą usługi Apple FairPlay w trybie offline.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 70256046089a59df1de79b78124c5d60fde77080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705942"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Przesyłanie strumieniowe fairplay w trybie offline dla systemu iOS z usługą Media Services w wersji 3

 Usługa Azure Media Services udostępnia zestaw dobrze zaprojektowanych [usług ochrony zawartości,](https://azure.microsoft.com/services/media-services/content-protection/) które obejmują:

- Microsoft PlayReady
- Google Widevine
    
    Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.
- Apple FairPlay
- Szyfrowanie AES-128

Szyfrowanie zawartości do zarządzania prawami cyfrowymi (DRM)/advanced encryption standard (AES) jest wykonywane dynamicznie na żądanie różnych protokołów przesyłania strumieniowego. Usługi dostarczania kluczy odszyfrowywania licencji DRM/AES są również świadczone przez program Media Services.

Oprócz ochrony zawartości do przesyłania strumieniowego online za pośrednictwem różnych protokołów przesyłania strumieniowego, tryb offline dla chronionej zawartości jest również często żądaną funkcją. Obsługa trybu offline jest potrzebna w następujących scenariuszach:

* Odtwarzanie, gdy połączenie internetowe nie jest dostępne, na przykład podczas podróży.
* Niektórzy dostawcy zawartości mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownicy chcą oglądać zawartość podczas podróży poza kraj/region, wymagane jest pobieranie w trybie offline.
* W niektórych krajach/regionach dostępność internetu i/lub przepustowość jest nadal ograniczona. Użytkownicy mogą najpierw pobrać, aby móc oglądać zawartość w rozdzielczości wystarczająco wysokiej, aby zapewnić zadowalające wrażenia wizualne. W takim przypadku problem zazwyczaj nie jest dostępność sieci, ale ograniczona przepustowość sieci. Dostawcy over-the-top (OTT)/online video platform (OVP) żądają obsługi w trybie offline.

W tym artykule omówiono obsługę usługi Przesyłania strumieniowego (FPS) w trybie offline, która jest przeznaczona dla urządzeń z systemem iOS 10 lub nowszym. Ta funkcja nie jest obsługiwana w przypadku innych platform Firmy Apple, takich jak watchOS, tvOS lub Safari w systemie macOS.

> [!NOTE]
> Usługa DRM w trybie offline jest naliczona tylko za złożenie jednego żądania licencji podczas pobierania zawartości. Wszelkie błędy nie są rozliczane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zaimplementowanie pamięci DRM w trybie offline dla programu FairPlay na urządzeniu z systemem iOS 10+:

* Przejrzyj ochronę zawartości online dla FairPlay: 

    - [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](fairplay-license-overview.md)
    - [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
    - Przykład .NET, który zawiera konfigurację przesyłania strumieniowego plików FPS online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Uzyskaj zestaw FPS SDK z sieci Programistów Firmy Apple. FPS SDK zawiera dwa składniki:

    - Zestaw SDK serwera FPS, który zawiera moduł zabezpieczeń klucza (KSM), przykłady klienta, specyfikację i zestaw wektorów testowych.
    - Pakiet wdrażania FPS, który zawiera specyfikację funkcji D, wraz z instrukcjami dotyczącymi generowania certyfikatu FPS, klucza prywatnego specyficznego dla klienta i klucza tajnego aplikacji. Firma Apple wystawia pakiet wdrażania FPS tylko licencjonowanym dostawcom zawartości.
* Klonuj https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Aby dodać konfiguracje FairPlay, należy zmodyfikować kod w [programie Encrypt with DRM przy użyciu platformy .NET.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w usłudze Azure Media Services

W [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metody wykonaj następujące czynności:

Odkomentuj kod, który konfiguruje opcję zasad FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Ponadto, uncomment kod, który dodaje CBCS ContentKeyPolicyOption do listy ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Włączanie trybu offline

Aby włączyć tryb offline, utwórz niestandardową transmisję strumieniowąPolicy i użyj jej nazwy podczas tworzenia lokalizatora strumieniowego w [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Teraz konto usługi Media Services jest skonfigurowane do dostarczania licencji FairPlay w trybie offline.

## <a name="sample-ios-player"></a>Przykładowy odtwarzacz iOS

Obsługa fps w trybie offline jest dostępna tylko w przypadku systemu iOS 10 i nowszych. Zestaw SDK serwera FPS (wersja 3.0 lub nowsza) zawiera dokument i próbkę dla trybu offline FPS. W szczególności FPS Server SDK (wersja 3.0 lub nowsza) zawiera następujące dwa elementy związane z trybem offline:

* Dokument: "Odtwarzanie w trybie offline z przesyłaniem strumieniowym FairPlay i transmisją strumieniową na żywo HTTP". Apple, 14 września 2016 r. W pliku FPS Server SDK w wersji 4.0 dokument ten jest scalany z głównym dokumentem FPS.
* Przykładowy kod: przykładowy HLSCatalog (część SDK serwera FPS firmy Apple) dla trybu offline FPS w pliku SDK \FairPlay Streaming Server W wersji 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. W przykładowej aplikacji HLSCatalog następujące pliki kodu są używane do implementowania funkcji trybu offline:

    - Plik kodu AssetPersistenceManager.swift: AssetPersistenceManager jest główną klasą w tym przykładzie, która pokazuje, jak:

        - Zarządzanie pobieraniem strumieni HLS, takich jak interfejsy API używane do uruchamiania i anulowania pobierania oraz usuwania istniejących zasobów z urządzeń.
        - Monitorowanie postępu pobierania.
    - AssetListTableViewController.swift i AssetListTableViewCell.swift plików kodu: AssetListTableViewController jest głównym interfejsem tego przykładu. Zawiera listę zasobów, których próbka może używać do odtwarzania, pobierania, usuwania lub anulowania pobierania. 

Te kroki pokazują, jak skonfigurować uruchomiony odtwarzacz iOS. Zakładając, że zaczynasz od przykładu HLSCatalog w FPS Server SDK w wersji 4.0.1, należy wprowadzić następujące zmiany kodu:

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift zaimplementuj metodę `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` przy użyciu następującego kodu. Niech "drmUr" być zmienną przypisaną do adresu URL HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift zaimplementuj metodę `requestApplicationCertificate()`. Ta implementacja zależy od tego, czy certyfikat jest osadzony (tylko klucz publiczny) z urządzeniem, czy hostowany certyfikat w sieci Web. W poniższej implementacji użyto certyfikatu aplikacji hostowanego używanego w przykładach testów. Niech "certUrl" być zmienną, która zawiera adres URL certyfikatu aplikacji.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

W przypadku końcowego zintegrowanego testu zarówno adres URL filmu, jak i adres URL certyfikatu aplikacji znajdują się w sekcji "Test zintegrowany".

W pliku HLSCatalog\Shared\Resources\Streams.plist dodaj adres URL testowego filmu. W przypadku identyfikatora klucza zawartości użyj adresu URL nabycia licencji FairPlay z protokołem skd jako unikatową wartością.

![Strumienie aplikacji iOS w trybie offline FairPlay](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Użyj własnego adresu URL testu wideo, adresu URL nabycia licencji FairPlay i adresu URL certyfikatu aplikacji, jeśli masz je skonfigurowane. Lub można przejść do następnej sekcji, która zawiera próbki testowe.

## <a name="integrated-test"></a>Zintegrowany test

Trzy przykłady testów w umiaźnych usługach Media Services obejmują następujące trzy scenariusze:

* FPS chronione, z wideo, audio i alternatywne ścieżki audio
* FPS chronione, z wideo i audio, ale bez alternatywnej ścieżki audio
* FPS chronione, tylko z wideo i bez dźwięku

Te przykłady można znaleźć w [tej witrynie demonstracyjnej,](https://aka.ms/poc#22)z odpowiednim certyfikatem aplikacji hostowanym w aplikacji sieci web platformy Azure.
W przypadku próbki SDK serwera FPS w wersji 3 lub 4, jeśli główna lista odtwarzania zawiera alternatywny dźwięk, w trybie offline odtwarza tylko dźwięk. W związku z tym należy rozebrać alternatywny dźwięk. Innymi słowy, druga i trzecia próbka wymieniona wcześniej działają w trybie online i offline. Przykładowa wyświetlana najpierw najpierw odtwarza dźwięk tylko w trybie offline, podczas gdy przesyłanie strumieniowe online działa poprawnie.

## <a name="faq"></a>Najczęściej zadawane pytania

Następujące często zadawane pytania zapewniają pomoc w rozwiązywaniu problemów:

- **Dlaczego odtwarzany jest tylko dźwięk, ale nie wideo w trybie offline?** To zachowanie wydaje się być według projektu przykładowej aplikacji. Gdy w trybie offline występuje alternatywna ścieżka audio (co ma miejsce w przypadku HLS), zarówno system iOS 10, jak i iOS 11 domyślnie mają alternatywną ścieżkę audio. Aby skompensować to zachowanie w trybie offline FPS, usuń alternatywną ścieżkę audio ze strumienia. Aby to zrobić w umiań multimediów, dodaj filtr manifestu dynamicznego "audio-only=false". Innymi słowy adres URL HLS kończy się na .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Dlaczego nadal odtwarza dźwięk tylko bez wideo w trybie offline po dodaniu tylko audio = false?** W zależności od projektu klucza pamięci podręcznej sieci dostarczania zawartości (CDN) zawartość może być buforowana. Przeczyść pamięć podręczną.
- **Czy tryb offline FPS jest również obsługiwany w iOS 11 oprócz systemu iOS 10?** Tak. Tryb offline FPS jest obsługiwany dla iOS 10 i iOS 11.
- **Dlaczego nie mogę znaleźć dokumentu "Odtwarzanie w trybie offline z przesyłaniem strumieniowym FairPlay i strumieniowaniem na żywo HTTP" w SDK serwera FPS?** Od czasu, gdy program FPS Server SDK w wersji 4, ten dokument został scalony w "FairPlay Streaming Programming Guide".
- **Jaka jest struktura pobranych/offline na urządzeniach z systemem iOS?** Pobrana struktura plików na urządzeniu z systemem iOS wygląda następująco na poniższym zrzucie ekranu. Folder `_keys` przechowuje pobrane licencje FPS z jednym plikiem sklepu dla każdego hosta usługi licencji. W `.movpkg` folderze jest przechowywane treści audio i wideo. Pierwszy folder o nazwie, która kończy się kreską, po której następuje numericzna zawartość wideo. Wartością liczbową jest PeakBandwidth wersji wideo. Drugi folder o nazwie, która kończy się kreską, po której następuje 0, zawiera zawartość audio. Trzeci folder o nazwie "Dane" zawiera główną listę odtwarzania zawartości FPS. Na koniec plik boot.xml zawiera `.movpkg` pełny opis zawartości folderu. 

![Struktura przykładowych plików aplikacji w trybie Offline FairPlay na iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Przykładowy plik boot.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [stosować ochronę przy użyciu algorytmu AES-128](protect-with-aes128.md)
