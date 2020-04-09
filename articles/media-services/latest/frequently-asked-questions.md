---
title: Często zadawane pytania dotyczące usługi Azure Media Services w wersji 3| Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886827"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Media Services w wersji 3

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Media Services (AMS) w wersji 3.

## <a name="general"></a>Ogólne

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jakie role platformy Azure mogą wykonywać akcje w zasobach usługi Azure Media Services? 

Zobacz [Kontrola dostępu oparta na rolach (RBAC) dla kont usługi Media Services](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Jak przesyłać strumieniowo na urządzenia Apple iOS?

Upewnij się, że masz "(format=m3u8-aapl)" na końcu ścieżki (po części "/manifestu" adresu URL), aby poinformować serwer pochodzenia strumieniowego o zwrocie zawartości HLS do użytku na urządzeniach natywnych Apple iOS (szczegółowe informacje można znaleźć w [dostarczaniu zawartości).](dynamic-packaging-overview.md)

### <a name="how-do-i-configure-media-reserved-units"></a>Jak skonfigurować jednostki zarezerwowane dla nośników?

Zdecydowanie zaleca się, aby w przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services wersji 3 lub Video Indexer, aby przeprowadzać aprowizację konta przy użyciu 10 jednostek MRU typu S3. Jeśli potrzebujesz więcej niż 10 mru S3, otwórz bilet pomocy technicznej za pomocą [witryny Azure portal](https://portal.azure.com/).

Aby uzyskać szczegółowe informacje, zobacz [Skalowanie przetwarzania multimediów za pomocą interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Jaka jest zalecana metoda przetwarzania filmów?

Użyj [przekształceń,](https://docs.microsoft.com/rest/api/media/transforms) aby skonfigurować typowe zadania kodowania lub analizowania klipów wideo. Każda **transformacja** opisuje przepis lub przepływ zadań do przetwarzania plików wideo lub audio. Zadanie [Job](https://docs.microsoft.com/rest/api/media/jobs) to rzeczywiste żądanie do usługi Media Services, aby zastosować **przekształcenie** do danej wejściowej zawartości wideo lub audio. Po utworzeniu transformu można przesyłać zadania przy użyciu interfejsów API usługi Media Services lub dowolnych opublikowanych sks. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transforms-jobs-concept.md).

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Przesłałem, zakodowałem i opublikowałem film. Jaki byłby powód, dla którego film nie jest odtwarzany, gdy próbuję go przesyłać strumieniowo?

Jednym z najczęstszych powodów jest to, że nie masz punktu końcowego przesyłania strumieniowego, z którego próbujesz odtworzyć w stanie Uruchamianie.

### <a name="how-does-pagination-work"></a>Jak działa podział na strony?

Podczas korzystania z podziału na strony, należy zawsze użyć następnego łącza do wyliczenia kolekcji i nie zależy od określonego rozmiaru strony. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Filtrowanie, zamawianie, stronicowanie](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jakie funkcje nie są jeszcze dostępne w usłudze Azure Media Services w wersji 3?

Aby uzyskać szczegółowe informacje, zobacz [luki w operacjach w odniesieniu do interfejsów API w wersji 2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Jaki jest proces przenoszenia konta usługi Media Services między subskrypcjami?  

Aby uzyskać szczegółowe informacje, zobacz [Przenoszenie konta usługi Media Services między subskrypcjami](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Jak zatrzymać transmisję na żywo po zakończeniu transmisji?

Można podejść do niego od strony klienta lub po stronie serwera.

#### <a name="client-side"></a>Po stronie klienta

Aplikacja sieci web powinna monitować użytkownika, jeśli chce zakończyć emisję, jeśli zamyka przeglądarkę. Jest to zdarzenie przeglądarki, które aplikacja sieci web może obsłużyć.

#### <a name="server-side"></a>Po stronie serwera

Możesz monitorować wydarzenia na żywo, subskrybując zdarzenia w usłudze Event Grid. Aby uzyskać więcej informacji, zobacz [schemat zdarzenia eventgrid](media-services-event-schemas.md#live-event-types).

* Możesz [subskrybować](reacting-to-media-services-events.md) poziom strumienia [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) i monitorować, że nie ma żadnych ponownych połączeń przyjść na chwilę, aby zatrzymać i usunąć wydarzenie na żywo.
* Możesz też [subskrybować](reacting-to-media-services-events.md) zdarzenia [pulsu](media-services-event-schemas.md#liveeventingestheartbeat) na poziomie ścieżki. Jeśli wszystkie ścieżki mają przychodzących bitrate spada do 0; lub ostatni sygnatura czasowa nie jest już zwiększenie, a następnie można również bezpiecznie zamknąć wydarzenie na żywo. Zdarzenia pulsu pojawiają się co 20 sekund dla każdego utworu, więc może to być trochę pełne.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Jak wstawić przerwy / filmy i tablice obrazów podczas transmisji na żywo?

Kodowanie na żywo usługi Media Services w wersji 3 nie obsługuje jeszcze wstawiania tablic wideo lub obrazów podczas transmisji na żywo. 

Do przełączania źródłowego wideo można użyć [kodera lokalnego na żywo.](recommended-on-premises-live-encoders.md) Wiele aplikacji umożliwia przełączanie źródeł, w tym Telestream Wirecast, Switcher Studio (na iOS), OBS Studio (darmowa aplikacja) i wiele innych.

## <a name="content-protection"></a>Ochrona zawartości

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Czy należy używać szyfrowania klucza jasnych AES-128 lub systemu DRM?

Klienci często zastanawiają się, czy powinni używać szyfrowania AES, czy systemu DRM. Podstawową różnicą między tymi dwoma systemami jest to, że przy szyfrowaniu AES klucz zawartości jest przesyłany do klienta za pośrednictwem protokołu TLS, dzięki czemu klucz jest szyfrowany podczas przesyłania, ale bez dodatkowego szyfrowania ("w postaci przezroczystej"). W rezultacie klucz używany do odszyfrowywania zawartości jest dostępny dla odtwarzacza klienta i można go przeglądać w śledzenia sieci na kliencie w postaci zwykłego tekstu. Szyfrowanie klucza ściąglowego AES-128 jest odpowiednie w przypadkach użycia, w których przeglądarka jest zaufaną stroną (na przykład szyfrowanie firmowych filmów wideo dystrybuowanych w firmie, które mają być wyświetlane przez pracowników).

Systemy DRM, takie jak PlayReady, Widevine i FairPlay, zapewniają dodatkowy poziom szyfrowania klucza używanego do odszyfrowywania zawartości w porównaniu z wyczyszczenym kluczem AES-128. Klucz zawartości jest szyfrowany do klucza chronionego przez środowisko uruchomieniowe DRM w dodatkowych do szyfrowania poziomu transportu dostarczonego przez protokół TLS. Ponadto odszyfrowywanie jest obsługiwane w bezpiecznym środowisku na poziomie systemu operacyjnego, gdzie jest trudniejsze dla złośliwego użytkownika do ataku. DrM jest zalecane w przypadkach użycia, w których przeglądarka może nie być zaufaną stroną i wymagają najwyższego poziomu zabezpieczeń.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Jak wyświetlić wideo tylko użytkownikom, którzy mają określone uprawnienia, bez korzystania z usługi Azure AD?

Nie trzeba używać żadnego określonego dostawcy tokenu (na przykład usługi Azure AD). Można utworzyć własnego dostawcę [JWT](https://jwt.io/) (tzw STS, Secure Token Service), przy użyciu szyfrowania klucza asymetrycznego. W niestandardowym sts, można dodać oświadczeń na podstawie logiki biznesowej.

Upewnij się, że wystawca, odbiorców i oświadczeń wszystkie pasują dokładnie między tym, co jest w JWT i ContentKeyPolicyRestriction używane w ContentKeyPolicy.

Aby uzyskać więcej informacji, zobacz [Ochrona zawartości przy użyciu szyfrowania dynamicznego usługi Media Services](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Jak i gdzie można uzyskać token JWT przed użyciem go do żądania licencji lub klucza?

1. W przypadku produkcji należy mieć usługi bezpiecznego tokenu (STS) (usługa sieci web), która wystawia token JWT na żądanie HTTPS. Do testowania można użyć kodu pokazanego w metodzie **GetTokenAsync** zdefiniowanej w [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Gracz będzie musiał złożyć żądanie, po uwierzytelnienie użytkownika, do STS dla takiego tokenu i przypisać go jako wartość tokenu. Można użyć [interfejsu API programu Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Na przykład uruchamiania STS, z kluczem symetrycznym i [https://aka.ms/jwt](https://aka.ms/jwt)asymetrycznym, zobacz . 
* Na przykład odtwarzacza opartego na usłudze Azure Media [https://aka.ms/amtest](https://aka.ms/amtest) Player przy użyciu takiego tokenu JWT zobacz (rozwiń łącze "player_settings", aby wyświetlić dane wejściowe tokenu).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Jak autoryzować żądania przesyłania strumieniowego filmów za pomocą szyfrowania AES?

Prawidłowe podejście polega na wykorzystaniu usługi STS (Secure Token Service):

W STS, w zależności od profilu użytkownika, dodaj różne oświadczenia (takie jak "Użytkownik premium", "Użytkownik podstawowy", "Bezpłatny użytkownik próbny"). Z różnych oświadczeń w JWT, użytkownik może zobaczyć różne treści. Oczywiście, dla różnych treści / zasobów, ContentKeyPolicyRestriction będzie miał odpowiednie WymaganeClaims.

Użyj interfejsów API usługi Azure Media Services do konfigurowania dostarczania licencji/kluczy i szyfrowania zasobów (jak pokazano w [tym przykładzie).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Aby uzyskać więcej informacji, zobacz:

- [Omówienie ochrony zawartości](content-protection-overview.md)
- [Projektowanie systemu ochrony zawartości przy użyciu technologii multi-DRM z kontrolą dostępu](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP lub HTTPS?
Aplikacja odtwarzacza ASP.NET MVC musi obsługiwać następujące elementy:

* Uwierzytelnianie użytkowników za pośrednictwem usługi Azure AD, która jest w obszarze HTTPS.
* Wymiana JWT między klientem a usługą Azure AD, która jest w obszarze HTTPS.
* Nabycie licencji DRM przez klienta, które musi być w obszarze HTTPS, jeśli dostarczanie licencji jest dostarczane przez program Media Services. Pakiet produktów PlayReady nie upoważnia protokołu HTTPS do dostarczania licencji. Jeśli serwer licencji PlayReady znajduje się poza usługą Media Services, można użyć protokołu HTTP lub HTTPS.

Aplikacja ASP.NET player używa protokołu HTTPS jako najlepszego rozwiązania, więc program Media Player znajduje się na stronie https. Jednak protokół HTTP jest preferowany do przesyłania strumieniowego, więc należy wziąć pod uwagę problem zawartości mieszanej.

* Przeglądarka nie zezwala na zawartość mieszaną. Ale wtyczki, takie jak Silverlight i wtyczka OSMF dla smooth i DASH pozwalają na to. Zawartość mieszana jest problemem bezpieczeństwa ze względu na zagrożenie możliwością wstrzykiwania złośliwego języka JavaScript, co może spowodować ryzyko naruszenia danych klientów. Przeglądarki domyślnie blokują tę funkcję. Jedynym sposobem obejść to jest po stronie serwera (pochodzenia), umożliwiając wszystkie domeny (niezależnie od HTTPS lub HTTP). To chyba też nie jest dobry pomysł.
* Unikaj mieszanej zawartości. Zarówno aplikacja odtwarzacza, jak i odtwarzacz multimedialny powinny używać protokołu HTTP lub HTTPS. Podczas odtwarzania zawartości mieszanej technologia silverlightSS wymaga wyczyszczenie ostrzeżenia o zawartości mieszanej. Technologia flashSS obsługuje zawartość mieszaną bez ostrzeżenia o zawartości mieszanej.
* Jeśli punkt końcowy przesyłania strumieniowego został utworzony przed sierpniem 2014 r., nie będzie obsługiwać protokołu HTTPS. W takim przypadku należy utworzyć i używać nowego punktu końcowego przesyłania strumieniowego dla protokołu HTTPS.

### <a name="what-about-live-streaming"></a>A co z transmisją na żywo?

Można użyć dokładnie tego samego projektu i implementacji do ochrony przesyłania strumieniowego na żywo w umiaście w umiaście, traktując zasób skojarzony z programem jako zasób VOD. Aby zapewnić ochronę multi-DRM zawartości na żywo, należy zastosować tę samą konfigurację/przetwarzanie do zasobu, tak jakby był to zasób VOD przed skojarzeniem zasobu z wyjściem na żywo.

### <a name="what-about-license-servers-outside-media-services"></a>Co z serwerami licencji poza usługą Media Services?

Często klienci inwestowali w farmę serwerów licencji we własnym centrum danych lub w centrum obsługiwanym przez dostawców usług DRM. Dzięki ochronie zawartości usługi Media Services można pracować w trybie hybrydowym. Zawartość może być hostowana i dynamicznie chroniona w programie Media Services, podczas gdy licencje DRM są dostarczane przez serwery spoza usługi Media Services. W takim przypadku należy wziąć pod uwagę następujące zmiany:

* Sts musi wystawiać tokeny, które są dopuszczalne i mogą być weryfikowane przez farmę serwerów licencji. Na przykład serwery licencji Widevine dostarczone przez firmę Axinom wymagają określonego certyfikatu JWT zawierającego komunikat o uprawnieniach. W związku z tym należy mieć STS do wydania takiego JWT. 
* Nie trzeba już konfigurować usługi dostarczania licencji w programie Media Services. Podczas konfigurowania contentkeypolicies należy podać adresy URL nabycia licencji (dla PlayReady, Widevine i FairPlay).

> [!NOTE]
> Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Usługi multimedialne w wersji 2 vs 3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Czy mogę używać witryny Azure portal do zarządzania zasobami w wersji 3?

Obecnie można użyć [witryny Azure portal](https://portal.azure.com/) do:

* zarządzanie wydarzeniami [na żywo](live-events-outputs-concept.md)w udziale usługi Media Services w wersji 3 , 
* widok (nie zarządzanie) [v3 Aktywa](assets-concept.md), 
* [uzyskać informacje o dostępie do interfejsów API](access-api-portal.md). 

W przypadku wszystkich innych zadań zarządzania (na przykład [przekształceń i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości)](content-protection-overview.md)użyj [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)

### <a name="is-there-an-assetfile-concept-in-v3"></a>Czy istnieje koncepcja AssetFile w wersji 3?

AssetFiles zostały usunięte z interfejsu API usługi AMS w celu oddzielenia usługi Media Services od zależności SDK magazynu. Teraz Magazyn, a nie usługi Media Services, przechowuje informacje, które należy do magazynu. 

Aby uzyskać więcej informacji, zobacz [Migrowanie do usługi Media Services w wersji 3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Gdzie było szyfrowanie pamięci masowej po stronie klienta?

Teraz zaleca się użycie szyfrowania magazynu po stronie serwera (które jest domyślnie włączone). Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage Service dla danych w spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Przesyłanie strumieniowe w trybie offline

### <a name="fairplay-streaming-for-ios"></a>Przesyłanie strumieniowe FairPlay dla systemu iOS

Poniższe często zadawane pytania zapewniają pomoc w rozwiązywaniu problemów z przesyłaniem strumieniowym FairPlay w trybie offline dla systemu iOS:

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Dlaczego odtwarzany jest tylko dźwięk, ale nie wideo w trybie offline?

To zachowanie wydaje się być według projektu przykładowej aplikacji. Gdy w trybie offline występuje alternatywna ścieżka audio (co ma miejsce w przypadku HLS), zarówno system iOS 10, jak i iOS 11 domyślnie mają alternatywną ścieżkę audio. Aby skompensować to zachowanie w trybie offline FPS, usuń alternatywną ścieżkę audio ze strumienia. Aby to zrobić w umiań multimediów, dodaj filtr manifestu dynamicznego "audio-only=false". Innymi słowy adres URL HLS kończy się na .ism/manifest(format=m3u8-aapl,audio-only=false). 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Dlaczego nadal odtwarza dźwięk tylko bez wideo w trybie offline po dodaniu tylko audio = false?

W zależności od projektu klucza pamięci podręcznej sieci dostarczania zawartości (CDN) zawartość może być buforowana. Przeczyść pamięć podręczną.

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>Czy tryb offline FPS jest również obsługiwany w iOS 11 oprócz systemu iOS 10?

Tak. Tryb offline FPS jest obsługiwany dla iOS 10 i iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Dlaczego nie mogę znaleźć dokumentu "Odtwarzanie w trybie offline z przesyłaniem strumieniowym FairPlay i strumieniowaniem na żywo HTTP" w SDK serwera FPS?

Od czasu, gdy program FPS Server SDK w wersji 4, ten dokument został scalony w "FairPlay Streaming Programming Guide".

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Jaka jest struktura pobranych/offline na urządzeniach z systemem iOS?

Pobrana struktura plików na urządzeniu z systemem iOS wygląda następująco na poniższym zrzucie ekranu. Folder `_keys` przechowuje pobrane licencje FPS z jednym plikiem sklepu dla każdego hosta usługi licencji. W `.movpkg` folderze jest przechowywane treści audio i wideo. Pierwszy folder o nazwie, która kończy się kreską, po której następuje numericzna zawartość wideo. Wartością liczbową jest PeakBandwidth wersji wideo. Drugi folder o nazwie, która kończy się kreską, po której następuje 0, zawiera zawartość audio. Trzeci folder o nazwie "Dane" zawiera główną listę odtwarzania zawartości FPS. Na koniec plik boot.xml zawiera `.movpkg` pełny opis zawartości folderu. 

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

### <a name="widevine-streaming-for-android"></a>Widevine streaming dla Androida

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>Jak dostarczać licencje trwałe (z obsługą w trybie offline) dla niektórych klientów/użytkowników i licencje nietrwałe (wyłączone w trybie offline) dla innych? Czy muszę powielać zawartość i używać oddzielnego klucza zawartości?

Ponieważ usługa Media Services w wersji 3 umożliwia zasobu mieć wiele streaminglocators. Możesz mieć

* Jeden ContentKeyPolicy z license_type = "trwałe", ContentKeyPolicyRestriction z roszczeniem na "trwałe", a jego StreamingLocator;
* Inny ContentKeyPolicy z license_type="nonpersistent", ContentKeyPolicyRestriction z oświadczeń na "nonpersistent" i jego StreamingLocator.
* Dwa streamingLocators mają różne ContentKey.

W zależności od logiki biznesowej niestandardowego STS różne oświadczenia są wystawiane w tokenie JWT. Za pomocą tokenu można uzyskać tylko odpowiednią licencję i można odtworzyć tylko odpowiedni adres URL.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Co to jest mapowanie między poziomami zabezpieczeń Widevine i Media Services DRM?

Google "Widevine DRM Architecture Overview" definiuje trzy różne poziomy zabezpieczeń. Jednak w [dokumentacji usługi Azure Media Services na szablonie licencji Widevine](widevine-license-template-overview.md)przedstawiono pięć różnych poziomów zabezpieczeń. W tej sekcji wyjaśniono, jak mapy poziomów zabezpieczeń.

Dokument Google "Widevine DRM Architecture Review" określa następujące trzy poziomy zabezpieczeń:

* Poziom zabezpieczeń 1: Wszystkie przetwarzanie zawartości, kryptografia i kontrola są wykonywane w środowisku zaufanego wykonywania (TEE). W niektórych modelach implementacji przetwarzanie zabezpieczeń może być wykonywane w różnych układach.
* Poziom zabezpieczeń 2: Wykonuje kryptografię (ale nie przetwarzanie wideo) w tee: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane za pośrednictwem oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.
* Poziom zabezpieczeń 3 Nie ma tee na urządzeniu. Można podjąć odpowiednie środki w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również zawierać sprzętowy aparat kryptograficzny, ale to tylko zwiększa wydajność, a nie bezpieczeństwo.

W tym samym czasie w [dokumentacji usługi Azure Media Services w szablonie licencji Widevine](widevine-license-template-overview.md)security_level właściwości content_key_specs może mieć następujące pięć różnych wartości (wymagania dotyczące niezawodności klienta dla odtwarzania):

* Wymagane jest oparte na oprogramowaniu krypto white-box.
* Wymagane jest oprogramowanie krypto i zaciemniony dekoder.
* Kluczowe materiały i operacje kryptograficzne muszą być wykonywane w ramach tee wspieranego sprzętowo.
* Krypto i dekodowanie zawartości muszą być wykonywane w ramach tee wspieranego sprzętowo.
* Krypto, dekodowanie i cała obsługa nośnika (skompresowanego i nieskompresowanego) muszą być obsługiwane w ramach tee wspieranego sprzętowo.

Oba poziomy zabezpieczeń są definiowane przez Google Widevine. Różnica polega na jego poziomie użycia: poziom architektury lub poziom interfejsu API. Pięć poziomów zabezpieczeń są używane w interfejsie API Widevine. Obiekt content_key_specs, który zawiera security_level jest deserializowany i przekazywany do globalnej usługi dostarczania Widevine przez usługę licencjonowania Azure Media Services Widevine. W poniższej tabeli przedstawiono mapowanie między dwoma zestawami poziomów zabezpieczeń.

| **Poziomy zabezpieczeń zdefiniowane w architekturze Widevine** |**Poziomy zabezpieczeń używane w interfejsie API Widevine**|
|---|---| 
| **Poziom zabezpieczeń 1:** Wszystkie przetwarzanie zawartości, kryptografia i kontrola są wykonywane w środowisku zaufanego wykonywania (TEE). W niektórych modelach implementacji przetwarzanie zabezpieczeń może być wykonywane w różnych układach.|**security_level=5**: Krypto, dekodowanie i cała obsługa nośnika (skompresowanego i nieskompresowanego) muszą być obsługiwane w tee ze sprzętem.<br/><br/>**security_level=4**: Krypto i dekodowanie zawartości muszą być wykonywane w ramach tee wspieranego sprzętowo.|
**Poziom zabezpieczeń 2:** Wykonuje kryptografię (ale nie przetwarzanie wideo) w tee: odszyfrowane bufory są zwracane do domeny aplikacji i przetwarzane za pośrednictwem oddzielnego sprzętu wideo lub oprogramowania. Jednak na poziomie 2 informacje kryptograficzne są nadal przetwarzane tylko w ramach TEE.| **security_level=3**: Materiał klucza i operacje kryptograficzne muszą być wykonywane w tee kopii sprzętowej. |
| **Poziom zabezpieczeń 3:** Nie ma tee na urządzeniu. Można podjąć odpowiednie środki w celu ochrony informacji kryptograficznych i odszyfrowanej zawartości w systemie operacyjnym hosta. Implementacja poziomu 3 może również zawierać sprzętowy aparat kryptograficzny, ale to tylko zwiększa wydajność, a nie bezpieczeństwo. | **security_level=2**: Wymagane jest krypto oprogramowania i zaciemniony dekoder.<br/><br/>**security_level=1**: Wymagane jest oparte na oprogramowaniu krypto white-box.|

#### <a name="why-does-content-download-take-so-long"></a>Dlaczego pobieranie zawartości trwa tak długo?

Istnieją dwa sposoby poprawy szybkości pobierania:

* Włącz sieć CDN, aby użytkownicy końcowi częściej trafiali do sieci CDN zamiast punktu końcowego pochodzenia/przesyłania strumieniowego w celu pobrania zawartości. Jeśli użytkownik osiągnie punkt końcowy przesyłania strumieniowego, każdy segment HLS lub dash fragment jest dynamicznie pakowane i szyfrowane. Mimo że to opóźnienie jest w skali milisekund dla każdego segmentu/fragmentu, gdy masz godzinny film wideo, skumulowane opóźnienie może być duże, co powoduje dłuższe pobieranie.
* Zapewnij użytkownikom końcowym możliwość selektywnego pobierania warstw jakości wideo i ścieżek audio zamiast całej zawartości. W trybie offline nie ma sensu pobierać wszystkich warstw jakości. Istnieją dwa sposoby osiągnięcia tego celu:

   * Kontrolowane przez klienta: aplikacja odtwarzacza automatycznie wybiera lub użytkownik wybiera warstwę jakości wideo i ścieżki audio do pobrania;
   * Sterowana usługą: można użyć funkcji manifestu dynamicznego w usłudze Azure Media Services, aby utworzyć (globalny) filtr, który ogranicza listę odtwarzania HLS lub DASH MPD do jednej warstwy jakości wideo i wybranych ścieżek audio. Następnie adres URL pobierania prezentowany użytkownikom końcowym będzie zawierał ten filtr.

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Media Services w wersji 3](media-services-overview.md)
