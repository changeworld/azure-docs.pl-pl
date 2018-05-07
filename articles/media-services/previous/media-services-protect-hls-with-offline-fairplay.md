---
title: "Ochrona zawartości z firmy Apple w trybie offline, FairPlay - Azure HLS | Dokumentacja firmy Microsoft"
description: "Ten temat zawiera omówienie i pokazuje, jak używać usługi Azure Media Services do dynamicznego szyfrowania zawartości HTTP Live Streaming (HLS) przy użyciu firmy Apple FairPlay w trybie offline."
services: media-services
keywords: "W trybie Offline, iOS FairPlay przesyłania strumieniowego (kl. / s), HLS, DRM, 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: dc38772097dddb7c7135d55598373d7ab544f9ea
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2018
---
# <a name="offline-fairplay-streaming-for-ios"></a>W trybie offline FairPlay przesyłania strumieniowego dla systemu iOS 
 Usługa Azure Media Services zawiera zestaw dobrze zaprojektowanego [zawartości usługi ochrony](https://azure.microsoft.com/services/media-services/content-protection/) ochrona ta:

- Microsoft PlayReady
- Google Widevine
- FairPlay firmy Apple
- Szyfrowanie AES-128

Zarządzania prawami cyfrowymi (DRM) / Advanced Encryption (Standard AES) szyfrowania zawartości jest wykonywane dynamicznie na żądanie dla różnych protokołów przesyłania strumieniowego. DRM licencji/AES odszyfrowywania klucza dostawy usług również są dostarczane przez usługi Media Services.

Oprócz ochrony zawartości do przesyłania strumieniowego online za pośrednictwem różnych protokołów przesyłania strumieniowego, trybu offline dla zawartości chronionej jest również często żądanej funkcji. Potrzebna jest obsługa trybu offline w następujących scenariuszach:

* Odtwarzanie podczas połączenia internetowego nie jest dostępne, takich jak podczas podróży.
* Niektórzy dostawcy zawartości może nie zezwalaj na dostarczania licencji DRM poza obramowania kraju. Jeśli użytkownik chce obejrzeć zawartość podczas podróży poza jego krajem, pobierania w trybie offline są wymagane.
* W niektórych krajach dostępności internet i/lub przepustowości jest nadal ograniczona. Użytkownicy mogą wybrać najpierw Pobierz mogli oglądać zawartość jest wystarczająco duża, środowisko zadowalające wyświetlanie rozdzielczości. W takim przypadku problem zwykle nie jest dostępność sieci, ale ograniczona przepustowość sieci. Tryb failover-górnym rogu (OTT) / dostawców platformy wideo online (OVP) żądania obsługi w trybie offline.

W tym artykule opisano obsługę trybu offline FairPlay przesyłania strumieniowego (kl. / s), przeznaczonego dla urządzeń z systemem iOS, 10 lub nowszej. Ta funkcja nie jest obsługiwana dla innych platform firmy Apple, takie jak watchOS, systemu tvOS lub Safari na macOS.

## <a name="preliminary-steps"></a>Czynności wstępne
Przed zaimplementowaniem DRM w trybie offline dla FairPlay na urządzeniu z systemem iOS 10 +:

* Zapoznanie się z ochrony zawartości w trybie online dla FairPlay. Aby uzyskać więcej informacji zobacz następujące artykuły i przykłady:

    - [Apple FairPlay przesyłania strumieniowego dla usługi Azure Media Services jest ogólnie dostępna](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Ochrona programu HLS zawartości z FairPlay firmy Apple lub Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Przykładowe przesyłania strumieniowego online kl. / s](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Uzyskać firmy Apple Developer Network SDK klatek na Sekundę. Zestaw SDK klatek na Sekundę zawiera dwa składniki:

    - SDK serwera klatek na Sekundę, która zawiera modułu zabezpieczeń klucza (KSM), przykłady klienta specyfikację i zestaw wektorów testu.
    - Pakiet wdrażania kl. / s, który zawiera specyfikację funkcji D, wraz z instrukcjami dotyczącymi sposobu generowania certyfikatu klatek na Sekundę, klucza prywatnego właściwe dla klienta i klucz tajny aplikacji. Apple wystawia pakietu wdrażania klatek na Sekundę tylko licencjonowane dostawców zawartości.

## <a name="configuration-in-media-services"></a>Konfiguracja w usłudze Media Services
Klatek na Sekundę w trybie offline do trybu konfiguracji za pomocą [.NET SDK usługi Media Services](https://www.nuget.org/packages/windowsazure.mediaservices), użyj .NET SDK usługi Media Services w wersji 4.0.0.4 lub później, która zapewnia niezbędne interfejsu API, aby skonfigurować tryb offline kl. / s.
Należy również kod pracy można skonfigurować ochrony zawartości w trybie online klatek na Sekundę. Po uzyskaniu kod, aby skonfigurować ochrony zawartości w trybie online na kl. / s musi się następujące dwie zmiany.

## <a name="code-change-in-the-fairplay-configuration"></a>Zmiana kodu w konfiguracji FairPlay
Pierwsza zmiana jest określenie "Włącz tryb offline" Boolean o nazwie objDRMSettings.EnableOfflineMode, który ma wartość true, jeśli umożliwia on scenariusz DRM w trybie offline. W zależności od tego wskaźnika wprowadź następujące zmiany w konfiguracji FairPlay:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Kod zmiany w konfiguracji zasady dostarczania zasobów
Druga zmiana polega na dodaniu trzeciego klucza do słownika < ciąg AssetDeliveryPolicyConfigurationKey >.
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

Po wykonaniu tego kroku ciąg < Dictionary_AssetDeliveryPolicyConfigurationKey > w zasad dostarczania elementów zawartości klatek na Sekundę zawiera trzy następujące wpisy:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl lub AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, w zależności od czynników, takich jak serwer KSM klatek na Sekundę/klucz używany i czy ponowne użycie tej samej dostarczania zasobów zasady przez wiele zasobów
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Konta usługi Media Services jest skonfigurowany do dostarczania licencje FairPlay w trybie offline.

## <a name="sample-ios-player"></a>Przykładowe iOS Player
Obsługa trybu offline kl. / s jest dostępny tylko w systemie iOS 10 i nowszych. Zestaw SDK serwera kl. / s (w wersji 3.0 lub nowszej) zawiera dokument i próbki dla klatek na Sekundę w trybie offline. W szczególności SDK serwera kl. / s (w wersji 3.0 lub nowszej) zawiera dwa następujące elementy związane z trybu offline:

* Dokument: "w trybie Offline odtwarzania z FairPlay Streaming i HTTP Live przesyłania strumieniowego." Apple, 14 września 2016 roku. W zestawie SDK serwera kl. / s w wersji 4.0 ten dokument jest scalany głównego dokumentu klatek na Sekundę.
* Przykładowy kod: próbka HLSCatalog do trybu offline kl. / s w 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ wersji przesyłania strumieniowego Server SDK \FairPlay. W przykładowej aplikacji HLSCatalog następujące pliki kodu są używane do implementowania funkcji w trybie offline:

    - Plik kodu AssetPersistenceManager.swift: AssetPersistenceManager jest główna klasa w tym przykładzie, który demonstruje sposób:

        - Zarządzanie pobieranie strumienie HLS, takie jak interfejsy API używane do uruchomienia i anulować pobieranie i Usuń istniejące zasoby wyłączyć urządzenia.
        - Monitorowanie postępu pobierania.
    - AssetListTableViewController.swift i AssetListTableViewCell.swift kodu plików: AssetListTableViewController jest głównym interfejsu tego przykładu. Zapewnia listę zasobów, które próbki służy do odtwarzania, Pobierz, usuń lub anulować pobieranie. 

Te kroki pokazują, jak skonfigurować uruchomionych player systemu iOS. Zakładając, że w przypadku uruchomienia próbki HLSCatalog w zestawie SDK serwera klatek na Sekundę wersja 4.0.1, wprowadź następujące zmiany kodu:

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, zaimplementuj metodę `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` przy użyciu następującego kodu. Let "drmUr" jest przypisana do adresu URL HLS zmiennej.

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

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, zaimplementuj metodę `requestApplicationCertificate()`. Ta implementacja zależy od tego, czy osadzić certyfikatu (tylko klucz publiczny) z urządzenia lub hosta certyfikatu w sieci web. Następująca implementacja używa certyfikatu hostowanej aplikacji używane w przykładach testu. Let "certUrl" jest zmienna, która zawiera adres URL aplikacji certyfikatu.

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

Na potrzeby końcowego testu zintegrowane zarówno wideo adresu URL i adresu URL certyfikatu aplikacji znajdują się w sekcji "Test zintegrowane".

W HLSCatalog\Shared\Resources\Streams.plist Dodaj adres URL wideo testu. Dla zawartości Identyfikatora klucza, należy użyć adres URL pozyskiwania licencji FairPlay z protokołem skd jako unikatowych wartości.

![W trybie offline iOS FairPlay strumieni aplikacji](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Użyj własnych wideo testowego adresu URL, adres URL pozyskiwania licencji FairPlay i adres URL certyfikatu aplikacji, jeśli je skonfigurować. Lub przejść do następnej sekcji, która zawiera próbki.

## <a name="integrated-test"></a>Zintegrowane testu
Trzy próbki w usłudze Media Services obejmuje następujące trzy scenariusze:

* Chronione, wideo, audio i alternatywnej ścieżki audio kl. / s
* Chronione, wideo i audio, ale nie alternatywną ścieżkę audio kl. / s
* Chronione, nie audio i wideo tylko kl. / s

Można znaleźć te przykłady w [tej lokacji pokaz](http://aka.ms/poc#22), przy użyciu odpowiedniego certyfikatu aplikacji hostowanej w aplikacji sieci web platformy Azure.
W wersji 3 lub w wersji 4 próbki zestawu SDK serwera kl. / s Jeśli główny listy odtwarzania zawiera alternatywny audio w trybie offline odtwarzania audio tylko. W związku z tym należy usunąć alternatywnego audio. Innymi słowy drugi i trzeci przykłady wymienione wcześniej pracy w trybie online i offline. Przykładowe wymienione najpierw odtwarzany audio tylko w trybie offline podczas online przesyłania strumieniowego działa prawidłowo.

## <a name="faq"></a>Często zadawane pytania
Poniższe często zadawane pytania dotyczące pomocy w rozwiązywaniu problemów:

- **Dlaczego tylko audio odgrywa ale nie wideo w trybie offline?** To zachowanie jest prawdopodobnie zgodnie z założeniami przykładowej aplikacji. Gdy alternatywną ścieżkę audio jest obecnie (czyli w przypadku HLS) w trybie offline, zarówno iOS 10 i 11 systemu iOS domyślnie alternatywną ścieżkę audio. To zachowanie kompensacji klatek na Sekundę w trybie offline, należy usunąć alternatywną ścieżkę audio ze strumienia. Aby to zrobić na usługi Media Services, Dodaj Filtr dynamiczny manifestu "tylko dźwięk = false." Innymi słowy adres URL HLS kończy .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Dlaczego on nadal Odtwórz dźwięk tylko bez wideo w trybie offline po dodaniu tylko dźwięk = false?** W zależności od dostarczania zawartości (CDN) pamięci podręcznej klucza projektem sieci może buforować zawartość. Przeczyścić pamięci podręcznej.
- **Jest w trybie offline kl. / s również obsługiwane w systemie iOS 11 oprócz iOS 10?** Tak. Klatek na Sekundę w trybie offline jest obsługiwana dla systemów iOS 10 i 11 systemu iOS.
- **Dlaczego nie można odnaleźć dokumentu "W trybie Offline odtwarzania z FairPlay Streaming i HTTP transmisji strumieniowej na żywo" w zestawie SDK serwera kl. / s?** Ponieważ kl. / s serwera SDK w wersji 4 tego dokumentu zostały scalone z "FairPlay Streaming przewodnik programowania w języku."
- **Co to ostatni parametr oznaczać w następujący interfejs API na kl. / s w trybie offline?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Dokumentację dla tego interfejsu API, zobacz [metody FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametr reprezentuje czas trwania dzierżawy w trybie offline, z godzinę jako jednostki.
- **Co to jest struktura pliku pobrane offline na urządzeniach z systemem iOS?** Struktura pobrany plik na urządzeniu z systemem iOS wygląda na poniższym zrzucie ekranu. `_keys` Magazynów folderów pobranej licencji kl. / s z jednego magazynu plików dla każdego hosta usługi licencji. `.movpkg` Folder przechowuje zawartości audio i wideo. Pierwszy folder o nazwie, która kończy się kreską następuje liczbową zawartość wideo. Wartość liczbowa jest PeakBandwidth wideo wersji. Drugi folder z nazwą kończącą się z łącznikiem następuje 0 zawartość audio. Trzeci folder o nazwie "Dane" zawiera wzorca listy odtwarzania zawartości klatek na Sekundę. Na koniec boot.xml zapewnia pełny opis `.movpkg` zawartość folderu. 

![W trybie offline z systemem iOS FairPlay Przykładowa struktura pliku aplikacji](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Przykładowy plik boot.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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
Ten dokument zawiera następujące kroki i informacje, których można używać do implementowania klatek na Sekundę w trybie offline:

* Konfiguracja ochrony zawartości Media Services za pośrednictwem interfejsu API platformy .NET usługi Media konfiguruje szyfrowania dynamicznego FairPlay i dostarczania licencji FairPlay w usłudze Media Services.
* Odtwarzacz systemu iOS na podstawie próbki z zestawu SDK serwera klatek na Sekundę konfiguruje player systemu iOS, które można odtwarzać kl. / s zawartości w trybie przesyłania strumieniowego online lub w trybie offline.
* Przykładowe kl. / s wideo są wykorzystywane do testowania trybu offline i online przesyłania strumieniowego.
* Często zadawane pytania dotyczące odpowiedzi na pytania dotyczące klatek na Sekundę w trybie offline.
