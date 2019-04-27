---
title: Ochrona HLS zawartość w trybie offline Apple FairPlay — Azure | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie i pokazuje, jak używać usługi Azure Media Services do dynamicznego szyfrowania zawartości z użyciem technologii FairPlay firmy Apple HTTP Live Streaming (HLS) w trybie offline.
services: media-services
keywords: HLS, DRM, technologii FairPlay Streaming (FPS), w trybie Offline z systemem iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 31da745727d567e1b3b85b3508df368d78ad84a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557276"
---
# <a name="offline-fairplay-streaming-for-ios"></a>W trybie offline FairPlay Streaming dla systemu iOS 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Wersja 3](../latest/offline-fairplay-for-ios.md)
> * [Wersja 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługa Azure Media Services udostępnia zestaw dobrze zaprojektowanego [usług ochrony zawartości](https://azure.microsoft.com/services/media-services/content-protection/) określające:

- PlayReady firmy Microsoft
- Google Widevine
- Technologia FairPlay firmy Apple
- Szyfrowanie AES-128

Zarządzania prawami cyfrowymi (DRM) / szyfrowania Advanced Encryption Standard (AES) zawartości odbywa się dynamicznie na żądanie dla różnych protokołów transmisji strumieniowej. Usług dostarczania kluczy odszyfrowywania AES/licencji DRM również są udostępniane przez usługi Media Services.

Oprócz ochrony zawartości do przesyłania strumieniowego online za pośrednictwem różnych protokołów transmisji strumieniowej, trybu offline dla chronionej zawartości jest również funkcją często. Obsługa trybu offline jest wymagane w następujących scenariuszach:

* Odtwarzanie, gdy połączenie z Internetem nie jest dostępne, takich jak podczas podróży.
* Niektórzy dostawcy zawartości może nie zezwalaj na dostarczanie licencji DRM poza obramowania kraju. Użytkownicy, którzy chcą obejrzeć zawartość podczas podróży poza jego krajem, pobierania w trybie offline jest potrzebna.
* W niektórych krajach jest nadal ograniczona dostępność internetowych i/lub przepustowości. Użytkownicy mogą zdecydować się na pobieranie najpierw, aby móc obejrzeć zawartość w rozdzielczości, która jest wystarczająco wysoka, środowisko przeglądania zadowalające. W takim przypadku problem zwykle nie jest dostępność sieci, ale ograniczona przepustowość sieci. Ott (OTT) / dostawców platformę wideo online (OVP) żądania pomocy technicznej w trybie offline.

W tym artykule omówiono obsługę trybu offline FairPlay Streaming (FPS), która jest przeznaczony dla urządzeń z systemem iOS 10 lub nowszym. Ta funkcja nie jest obsługiwana dla innych platform firmy Apple, takich jak systemu watchOS, tvOS lub Safari w systemie macOS.

## <a name="preliminary-steps"></a>Wstępne kroki
Przed zaimplementowaniem DRM w trybie offline dla technologii FairPlay na urządzeniu z systemem iOS 10 +:

* Zapoznanie się z ochrony zawartości dla technologii FairPlay w trybie online. Aby uzyskać więcej informacji zobacz następujące artykuły i przykłady:

    - [Apple FairPlay Streaming dla usługi Azure Media Services jest ogólnie dostępna](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Ochrona zawartości przy użyciu technologii FairPlay firmy Apple lub Microsoft PlayReady przez moduł HLS](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Przykład przesyłania strumieniowego online kl. / s](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Uzyskaj zestaw SDK kl. / s z sieci dla deweloperów firmy Apple. Zestaw SDK kl. / s zawiera dwa składniki:

    - SDK serwera kl. / s, która zawiera moduł zabezpieczeń klucza (KSM), przykłady klienta, Specyfikacja i zestaw testów wektorów.
    - Pakiet wdrażania kl. / s, który zawiera specyfikację funkcji D, wraz z instrukcjami dotyczącymi sposobu generowania certyfikatu kl. / s, klucza prywatnego właściwe dla klienta i klucz tajny aplikacji. Firmy Apple wysyła pakiet wdrażania kl. / s tylko dla licencjonowanego dostawcy zawartości.

## <a name="configuration-in-media-services"></a>Konfiguracja w usłudze Media Services
Dla konfiguracji trybu offline kl. / s za pośrednictwem [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)Użyj Media Services .NET SDK w wersji 4.0.0.4 lub nowszej, który zapewnia niezbędne interfejsu API, aby skonfigurować tryb offline kl. / s.
Należy również kod pracy, aby skonfigurować ochronę zawartości w trybie online kl. / s. Po uzyskaniu kod, aby skonfigurować tryb online funkcja content protection kl. / s, potrzebne są następujące dwie zmiany.

## <a name="code-change-in-the-fairplay-configuration"></a>Zmiany kodu w konfiguracji technologii FairPlay
Pierwsza zmiana jest określenie "Włącz tryb offline" atrybut typu wartość logiczna, o nazwie objDRMSettings.EnableOfflineMode, który ma wartość true, jeśli umożliwia on scenariusz DRM w trybie offline. W zależności od tego wskaźnika wprowadź następującą zmianę w konfiguracji technologii FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Zmiana kodu konfiguracją zasad dostarczania elementów zawartości
Drugi zmiana ma na celu Dodaj trzeci klucz do słownika < ciąg AssetDeliveryPolicyConfigurationKey >.
Dodaj AssetDeliveryPolicyConfigurationKey, jak pokazano poniżej:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Po wykonaniu tego kroku ciąg < Dictionary_AssetDeliveryPolicyConfigurationKey > w zasad dostarczania elementów zawartości kl. / s zawiera trzy następujące wpisy:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl lub AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, w zależności od czynników, takich jak serwer KSM kl. / s/klucz używany i czy ponowne użycie tego samego dostarczania elementu zawartości zasady dla wielu zasobów
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Teraz konto usługi Media Services jest skonfigurowany do dostarczania licencje usługi fairplay: w trybie offline.

## <a name="sample-ios-player"></a>Przykładowe odtwarzacza dla systemu iOS
Obsługa trybu offline kl. / s jest dostępne tylko w systemie iOS 10 i nowszych wersjach. Zestaw SDK serwera kl. / s (w wersji 3.0 lub nowszej) zawiera przykład do trybu offline kl. / s i dokumentów. W szczególności SDK serwera kl. / s (w wersji 3.0 lub nowszej) zawiera dwa poniższe elementy związane z trybu offline:

* Dokument: "W trybie offline odtwarzania za pomocą technologii FairPlay Streaming i HTTP transmisja strumieniowa na żywo." Firmy Apple 14 września 2016 r. W zestawie SDK serwera kl. / s w wersji 4.0 w tym dokumencie są scalane dokumentu głównego kl. / s.
* Przykładowy kod: Przykład HLSCatalog do trybu offline kl. / s w 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ wersji przesyłania strumieniowego Server SDK \FairPlay. Przykładowa aplikacja HLSCatalog następujące pliki kodu są używane do implementowania funkcji w trybie offline:

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

![W trybie offline FairPlay dla systemu iOS App strumieni](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

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
- **Co to ostatni parametr oznaczać w następujący interfejs API w trybie offline kl. / s?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Aby uzyskać dokumentację dla tego interfejsu API, zobacz [metoda FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametr reprezentuje czas trwania w trybie offline wypożyczeń, za pomocą godzinę jako jednostka.
- **Co to jest struktura plików pobrane offline na urządzeniach z systemem iOS?** Struktura pobrany plik na urządzeniu z systemem iOS wygląda jak poniższy zrzut ekranu. `_keys` Magazynów folderów pobrane licencji kl. / s przy użyciu pliku jeden magazyn każdej licencji usługi hosta. `.movpkg` Folder przechowuje zawartości audio i wideo. Pierwszy folder o nazwie, która kończy się wraz z kreskami następuje liczbową zawiera zawartości wideo. Wartość liczbowa jest PeakBandwidth wersji wideo. Drugi folder o nazwie, która kończy się wraz z kreskami następuje 0 zawiera zawartość audio. Trzeci folder o nazwie "Dane" zawiera główny listy odtwarzania zawartości kl. / s. Na koniec boot.xml zapewnia pełny opis `.movpkg` zawartości folderu. 

![W trybie offline iOS FairPlay Przykładowa struktura plików aplikacji](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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

## <a name="summary"></a>Podsumowanie
Ten dokument zawiera następujące kroki i informacje, których można użyć do wdrożenia w trybie offline kl. / s:

* Konfiguracja ochrony zawartości Media Services za pośrednictwem interfejsu API .NET usługi Media konfiguruje dynamiczne szyfrowanie FairPlay i dostarczanie licencji technologii FairPlay w usłudze Media Services.
* Odtwarzacza dla systemu iOS na podstawie próbki z zestawu SDK serwera kl. / s w konfiguruje odtwarzacza dla systemu iOS, który można odtworzyć kl. / s zawartości w trybie przesyłania strumieniowego online lub w trybie offline.
* Przykładowe kl. / s wideo są używane do testowania trybu offline i online przesyłania strumieniowego.
* Tutaj często zadawane pytania dotyczące trybu offline kl. / s.
