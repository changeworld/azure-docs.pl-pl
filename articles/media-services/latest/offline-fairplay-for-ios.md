---
title: Ochrona HLS zawartość w trybie offline Apple FairPlay — Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie i pokazuje, jak używać usługi Azure Media Services do dynamicznego szyfrowania zawartości z użyciem technologii FairPlay firmy Apple HTTP Live Streaming (HLS) w trybie offline.
services: media-services
keywords: HLS, DRM, technologii FairPlay Streaming (FPS), w trybie Offline z systemem iOS 10
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
ms.openlocfilehash: 58315e639a54b67bdef6751004b7a768e4f92224
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937519"
---
# <a name="offline-fairplay-streaming-for-ios"></a>W trybie offline FairPlay Streaming dla systemu iOS 

 Usługa Azure Media Services udostępnia zestaw dobrze zaprojektowanego [usług ochrony zawartości](https://azure.microsoft.com/services/media-services/content-protection/) określające:

- PlayReady firmy Microsoft
- Google Widevine
- Technologia FairPlay firmy Apple
- Szyfrowanie AES-128

Zarządzania prawami cyfrowymi (DRM) / szyfrowania Advanced Encryption Standard (AES) zawartości odbywa się dynamicznie na żądanie dla różnych protokołów transmisji strumieniowej. Usług dostarczania kluczy odszyfrowywania AES/licencji DRM również są udostępniane przez usługi Media Services.

Oprócz ochrony zawartości do przesyłania strumieniowego online za pośrednictwem różnych protokołów transmisji strumieniowej, trybu offline dla chronionej zawartości jest również funkcją często. Obsługa trybu offline jest wymagane w następujących scenariuszach:

* Odtwarzanie, gdy połączenie z Internetem nie jest dostępne, takich jak podczas podróży.
* Niektórzy dostawcy zawartości może nie zezwalaj na dostarczanie licencji DRM poza krawędź kraj/region. Użytkownicy, którzy chcą obejrzeć zawartość podczas podróży poza kraj/region, w trybie offline pobierania jest potrzebna.
* W niektórych krajach/regionach jest nadal ograniczona dostępność internetowych i/lub przepustowości. Użytkownicy mogą zdecydować się na pobieranie najpierw, aby móc obejrzeć zawartość w rozdzielczości, która jest wystarczająco wysoka, środowisko przeglądania zadowalające. W takim przypadku problem zwykle nie jest dostępność sieci, ale ograniczona przepustowość sieci. Ott (OTT) / dostawców platformę wideo online (OVP) żądania pomocy technicznej w trybie offline.

W tym artykule omówiono obsługę trybu offline FairPlay Streaming (FPS), która jest przeznaczony dla urządzeń z systemem iOS 10 lub nowszym. Ta funkcja nie jest obsługiwana dla innych platform firmy Apple, takich jak systemu watchOS, tvOS lub Safari w systemie macOS.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zaimplementowaniem DRM w trybie offline dla technologii FairPlay na urządzeniu z systemem iOS 10 +:

* Zapoznaj się z ochrony zawartości w trybie online dla technologii FairPlay: 

    - [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](fairplay-license-overview.md)
    - [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
    - Przykład .NET, obejmującą konfiguracji przesyłanych strumieniowo kl. / s w trybie online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Uzyskaj zestaw SDK kl. / s z sieci dla deweloperów firmy Apple. Zestaw SDK kl. / s zawiera dwa składniki:

    - SDK serwera kl. / s, która zawiera moduł zabezpieczeń klucza (KSM), przykłady klienta, Specyfikacja i zestaw testów wektorów.
    - Pakiet wdrażania kl. / s, który zawiera specyfikację funkcji D, wraz z instrukcjami dotyczącymi sposobu generowania certyfikatu kl. / s, klucza prywatnego właściwe dla klienta i klucz tajny aplikacji. Firmy Apple wysyła pakiet wdrażania kl. / s tylko dla licencjonowanego dostawcy zawartości.
* Klonuj https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Konieczne będzie modyfikować kodu w [szyfrowania przy użyciu DRM, przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) można dodać konfiguracji technologii FairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w usłudze Azure Media Services

W [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metody, wykonaj następujące czynności:

Usuń komentarz kodu, który konfiguruje opcję zasad technologii FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Ponadto Usuń komentarz kodu, który umożliwia dodanie listy ContentKeyPolicyOptions CBCS ContentKeyPolicyOption

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Włącz tryb offline

Włącz tryb offline, Utwórz niestandardowe StreamingPolicy i użyć jej nazwy, podczas tworzenia StreamingLocator w [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
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

Teraz konto usługi Media Services jest skonfigurowany do dostarczania licencje usługi fairplay: w trybie offline.

## <a name="sample-ios-player"></a>Przykładowe odtwarzacza dla systemu iOS

Obsługa trybu offline kl. / s jest dostępne tylko w systemie iOS 10 i nowszych wersjach. Zestaw SDK serwera kl. / s (w wersji 3.0 lub nowszej) zawiera przykład do trybu offline kl. / s i dokumentów. W szczególności SDK serwera kl. / s (w wersji 3.0 lub nowszej) zawiera dwa poniższe elementy związane z trybu offline:

* Dokument: "W trybie offline odtwarzania za pomocą technologii FairPlay Streaming i HTTP transmisja strumieniowa na żywo." Firmy Apple 14 września 2016 r. W zestawie SDK serwera kl. / s w wersji 4.0 w tym dokumencie są scalane dokumentu głównego kl. / s.
* Przykładowy kod: Przykład HLSCatalog (część pakietu zestawu SDK serwera kl. / s firmy Apple) do trybu offline kl. / s w 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ wersji przesyłania strumieniowego Server SDK \FairPlay. Przykładowa aplikacja HLSCatalog następujące pliki kodu są używane do implementowania funkcji w trybie offline:

    - Plik kodu AssetPersistenceManager.swift: AssetPersistenceManager jest główna klasa, w tym przykładzie, który demonstruje sposób:

        - Pobieranie strumieni HLS, zarządzania, takich jak interfejsy API używane do uruchamiania i anulować pobieranie i usuwanie istniejących zasobów wyłączanie urządzeń.
        - Monitoruj postęp pobierania.
    - Pliki kodu AssetListTableViewController.swift i AssetListTableViewCell.swift: AssetListTableViewController jest głównym interfejsu tego przykładu. Zapewnia listę zasobów, które przykład służy do odtwarzania, Pobierz, usuń lub Anuluj pobieranie. 

Te kroki pokazują, jak skonfigurować uruchamianie odtwarzacza dla systemu iOS. Przy założeniu, że zaczynasz od przykładowej HLSCatalog w zestawie SDK serwera kl. / s w wersji 4.0.1, dokonaj następujących zmian kodu:

W pliku HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, zaimplementuj metodę `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` przy użyciu następującego kodu. Niech "drmUr", będzie zmienna przypisana do adresu URL HLS.

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

W pliku HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, zaimplementuj metodę `requestApplicationCertificate()`. Ta implementacja zależy od tego, czy osadzić certyfikatu (tylko klucz publiczny) z urządzeniem lub obsługi certyfikatów w sieci web. Następującą implementacją używa certyfikatu hostowanej aplikacji używane w przykładach testu. Let "certUrl" jest zmienną, która zawiera adres URL certyfikatu aplikacji.

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

Na potrzeby końcowego testu zintegrowanego zarówno adres URL filmu wideo, jak i adres URL certyfikatu aplikacji znajdują się w sekcji "Testowanie zintegrowane".

W HLSCatalog\Shared\Resources\Streams.plist Dodaj adres URL wideo testu. Dla zawartości Identyfikatora klucza, użyj adres URL pozyskiwania licencji technologii FairPlay przy użyciu protokołu skd jako unikatowe wartości.

![W trybie offline FairPlay dla systemu iOS App strumieni](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Użyj własnych Testuj adres URL wideo, adres URL pozyskiwania licencji technologii FairPlay i adres URL certyfikatu aplikacji, jeśli je skonfigurować. Możesz także nadal do następnej sekcji, która zawiera próbki.

## <a name="integrated-test"></a>Zintegrowane testu

Trzy próbki testu w usłudze Media Services obejmują następujące trzy scenariusze:

* Kl. / s chronione za pomocą wideo, audio i alternatywne ścieżki audio
* Chronione za pomocą wideo i audio, ale nie alternatywną ścieżkę audio kl. / s
* Chronione za pomocą tylko wideo i audio nie kl. / s

Te przykłady można znaleźć [tej wersji demonstracyjnej witryny](https://aka.ms/poc#22), przy użyciu odpowiedniego certyfikatu aplikacji hostowanych w aplikacji sieci web platformy Azure.
W wersji 3 lub w wersji 4 próbki zestawu SDK serwera kl. / s Jeśli główny listy odtwarzania zawiera alternatywny audio w trybie offline jego odtwarza dźwięk tylko. W związku z tym należy oddzielić alternatywne audio. Innymi słowy drugi i trzeci wymienione wcześniej działanie przykładów w trybie online i offline. Przykładowe wymienione najpierw odtwarza dźwięk tylko w trybie offline, gdy online przesyłanie strumieniowe działa prawidłowo.

## <a name="faq"></a>Często zadawane pytania

Poniższe często zadawane pytania dotyczące pomocy w rozwiązywaniu problemów:

- **Dlaczego jest tylko dźwięk odtwarzany ale wideo nie w trybie offline?** To zachowanie jest prawdopodobnie zgodnie z projektem przykładowej aplikacji. Gdy alternatywną ścieżkę audio jest obecny (jest to wymagane dla protokołu HLS) w trybie offline z systemem iOS 10 i domyślnie system iOS 11 alternatywną ścieżkę audio. Aby kompensacji trybu offline kl. / s to zachowanie, należy usunąć alternatywną ścieżkę audio ze strumienia. Aby to zrobić w usłudze Media Services, Dodaj filtr manifestów dynamicznych "tylko dane audio = false." Innymi słowy adres URL HLS kończy się .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Dlaczego on nadal odtwarzania dźwięku tylko bez wideo w trybie offline po dodaniu tylko dane audio = false?** W zależności od content delivery network (CDN) pamięci podręcznej klucza projektu zawartość może być buforowane. Przeczyścić pamięci podręcznej.
- **Jest trybie offline kl. / s również obsługiwane w systemie iOS 11, oprócz systemu iOS 10?** Tak. Tryb offline kl. / s jest obsługiwany dla systemu iOS 10 i systemie iOS 11.
- **Dlaczego nie można odnaleźć dokumentu "W trybie Offline odtwarzanie przy użyciu technologii FairPlay Streaming i HTTP Live Streaming" w zestawie SDK serwera kl. / s?** Od czasu kl. / s Server SDK w wersji 4 w tym dokumencie zostały scalone z "technologii FairPlay Streaming Programming Guide."
- **Co to jest struktura plików pobrane offline na urządzeniach z systemem iOS?** Struktura pobrany plik na urządzeniu z systemem iOS wygląda jak poniższy zrzut ekranu. `_keys` Magazynów folderów pobrane licencji kl. / s przy użyciu pliku jeden magazyn każdej licencji usługi hosta. `.movpkg` Folder przechowuje zawartości audio i wideo. Pierwszy folder o nazwie, która kończy się wraz z kreskami następuje liczbową zawiera zawartości wideo. Wartość liczbowa jest PeakBandwidth wersji wideo. Drugi folder o nazwie, która kończy się wraz z kreskami następuje 0 zawiera zawartość audio. Trzeci folder o nazwie "Dane" zawiera główny listy odtwarzania zawartości kl. / s. Na koniec boot.xml zapewnia pełny opis `.movpkg` zawartości folderu. 

![W trybie offline iOS FairPlay Przykładowa struktura plików aplikacji](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [stosować ochronę przy użyciu algorytmu AES-128](protect-with-aes128.md)
