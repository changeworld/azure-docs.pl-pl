---
title: Azure Media Services koncepcji | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie koncepcji usługi multimediów Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: dcefc8bc-e2ea-4b38-a643-9010f4436fb5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: juliako
ms.openlocfilehash: cfc8ac74f626ada30dda72b58e4b4bc7f0d9e806
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-concepts"></a>Azure Media Services pojęcia
Ten temat zawiera omówienie koncepcji usługi Media Services.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Zasoby i magazynu
### <a name="assets"></a>Elementy zawartości
[Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) zawiera (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżek tekstu i pliki napisów) pliki cyfrowe i metadane dotyczące tych plików. Po pliki cyfrowe są przekazywane do elementu zawartości, mogą być używane w Media Services, kodowania i przesyłania strumieniowego przepływów pracy.

Zasób jest mapowana do kontenera obiektów blob na koncie magazynu Azure i pliki w elementach zawartości są przechowywane jako blokowych obiektów blob w tym kontenerze. Stronicowe obiekty BLOB nie są obsługiwane przez usługi Azure Media Services.

Podczas podejmowania decyzji o zawartości nośnika do przekazywania i przechowywać w zasób, zastosuj następujące kwestie:

* Zasób powinien zawierać tylko jedną, unikatową wystąpienie zawartości nośnika. Na przykład pojedynczy edycji epizodu TV, filmów lub anonsu.
* Zasób nie może zawierać wielu wersji lub edycji pliku audiowizualnych. Czy próby przykładem niepoprawne użycie trwałego przechowywać więcej niż jeden odcinek TV, anonsu lub różnych kątów kamery z jednym produkcji wewnątrz elementu zawartości. Przechowywanie wielu wersji lub edycji pliku audiowizualnych w zasób może spowodować problemy podczas przesyłania zadania kodowania, przesyłanie strumieniowe i zabezpieczania dostarczania zasobów później w przepływie pracy.  

### <a name="asset-file"></a>Plik zasobów
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) reprezentuje rzeczywisty plik wideo lub audio, który jest przechowywany w kontenerze obiektów blob. Plik zasobów zawsze jest skojarzony z zasobem i zasobów może zawierać jeden lub wiele plików. Zadanie Media Encoder usługi nie powiedzie się, jeśli obiekt pliku zasobu nie jest skojarzony z pliku cyfrowego w kontenerze obiektów blob.

**AssetFile** wystąpienia oraz plik multimedialna są dwa różne obiekty. Wystąpienia AssetFile zawiera metadanych dotyczących pliku nośnika, a plik nośnika zawiera zawartość multimedialna.

Nie należy próbować zmiany zawartości kontenerów obiektów blob, które zostały wygenerowane przez usługę Media Services bez korzystania z interfejsów API usługi multimediów.

### <a name="asset-encryption-options"></a>Opcje szyfrowania zasobów
W zależności od typu zawartości, który chcesz przekazać, przechowywania i dostarczania Media Services udostępnia różne opcje szyfrowania, które są dostępne.

>[!NOTE]
>Szyfrowanie nie jest stosowane. Jest to wartość domyślna. Korzystając z tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.

Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tę opcję, aby przekazać zawartość.

**StorageEncrypted** — ta opcja służy do szyfrowania zawartości lokalnie za pomocą szyfrowania AES 256-bitową, a następnie przekaż go do magazynu Azure gdzie jest przechowywana szyfrowane, gdy. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a następnie opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest, gdy chcesz zabezpieczyć z wysokiej jakości multimedialnych plików wejściowych pomocą silnego szyfrowania przechowywanych na dysku. 

Aby dostarczyć zasób zaszyfrowanych magazynu, należy skonfigurować zasady dostarczania elementu zawartości będzie wówczas traktował Media Services sposób dostarczania zawartości. Przed zawartości mogą być przesyłane strumieniowo, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieni zawartości za pomocą zasad określonym dostarczania (na przykład AES, PlayReady lub bez szyfrowania). 

**CommonEncryptionProtected** — Użyj tej opcji, jeśli chcesz zaszyfrować (lub Przekaż już zaszyfrowany) zawartości ze wspólnego szyfrowania lub technologii PlayReady DRM (na przykład, Smooth Streaming chronione za pomocą technologii PlayReady DRM).

**EnvelopeEncryptionProtected** — Użyj tej opcji, jeśli chcesz chronić (lub Przekaż już chroniony) HTTP Live Streaming (HLS) szyfrowane z szyfrowania AES (Advanced Standard). Podczas przekazywania HLS już zaszyfrowany z użyciem standardu AES go musi mieć został zaszyfrowany za Transform Manager.

### <a name="access-policy"></a>Zasady dostępu
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definiuje uprawnienia (na przykład odczytu, zapisu i listy) i czas trwania dostępu do zasobów. Zazwyczaj przejdzie obiektu AccessPolicy do lokalizatora, który następnie będzie używany na dostęp do plików objętych zasób.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="blob-container"></a>Kontener obiektów blob
Kontener obiektów blob zawiera grupowanie zestawu obiektów blob. Kontenerów obiektów blob są używane w usłudze Media Services jako granic punktu kontroli dostępu i lokalizatorów dostępu sygnatury dostępu Współdzielonego na zasoby. Konto magazynu Azure może zawierać nieograniczoną liczbę kontenerów obiektów blob. Kontener może przechowywać nieograniczoną liczbę obiektów blob.

>[!NOTE]
> Nie należy próbować zmiany zawartości kontenerów obiektów blob, które zostały wygenerowane przez usługę Media Services bez korzystania z interfejsów API usługi multimediów.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Lokalizatory
[Lokalizator](https://docs.microsoft.com/rest/api/media/operations/locator)s Podaj punktu wejścia do dostępu do plików przechowywanych w zasób. Zasady dostępu jest używana do definiowania uprawnień i czas trwania, że klient ma dostęp do danej zawartości. Lokalizatory może mieć wiele do jednego relacji z zasady dostępu w taki sposób, że lokalizatorów różnych może dostarczać godziny rozpoczęcia różnych i typów połączeń do różnych klientów jednocześnie wszystkie przy użyciu tego samego uprawnienia i ustawienia czasu trwania; Jednak ze względu na ograniczenia zasad dostępu współdzielonego ustawione przez usług magazynu platformy Azure, nie może mieć więcej niż pięć lokalizatorów unikatowy skojarzone z danym zawartości w tym samym czasie. 

Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin używane do przesyłania strumieniowego (na przykład MPEG DASH, HLS lub Smooth Streaming) lub pobrać progresywnie nośników i Locator adres URL SAS używany do przekazywanie lub pobieranie to\from pliki nośnika magazynu Azure. 

>[!NOTE]
>Nie można używać uprawnień listy (AccessPermissions.List) tworząc Lokalizator OnDemandOrigin. 

### <a name="storage-account"></a>Konto magazynu
Dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Konto usługi Media można skojarzyć z co najmniej jedno konto magazynu. Konto może zawierać nieograniczoną liczbę kontenerów, dopóki ich łączny rozmiar jest w obszarze 500TB na konto magazynu.  Usługa Media Services udostępnia poziomu narzędzia zestawu SDK pozwala zarządzać wieloma kontami magazynu i dystrybucji zasobów podczas przekazywania do tych kont na podstawie metryk lub dystrybucji losowe równoważenia obciążenia. Aby uzyskać więcej informacji, zapoznaj się z praca [usługi Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Zadań i zadań
A [zadania](https://docs.microsoft.com/rest/api/media/operations/job) jest zwykle używana do procesu (na przykład indeksu lub zakodować) prezentacji audio i wideo. W przypadku przetwarzania wiele plików wideo, należy utworzyć zadanie dla każdego wideo do zakodowania.

Zadanie zawiera metadane dotyczące przetwarzania do wykonania. Każde zadanie zawiera co najmniej jeden [zadań](https://docs.microsoft.com/rest/api/media/operations/task), które Określ zadanie przetwarzania atomic, jego zasobów wejściowych, wyjściowych zasoby, procesor multimediów i jej powiązane ustawienia. Zadania w ramach danego zadania można je połączyć ze sobą, gdzie elementu zawartości wyjściowej jednego zadania jest podawana jako zasobów wejściowych do następnego zadania. W ten sposób jedno zadanie może zawierać wszystkie niezbędne do przedstawienia nośnika przetwarzania.

## <a id="encoding"></a>Kodowanie
Azure Media Services udostępnia wiele opcji kodowania nośnika w chmurze.

Podczas uruchamiania z programem Media Services, należy zrozumieć różnicę między formatami koderów-dekoderów i plików.
Kodery-dekodery to oprogramowanie, które implementuje algorytmy kompresji i dekompresji formatów plików są kontenery zawierających skompresowane wideo.

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, co pozwala na dostarczanie zawartości o adaptacyjnej szybkości bitowej MP4 lub Smooth Streaming zakodowane w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatach.

Aby móc korzystać z [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md), należy zakodować (źródłowy) mezzanine do zestawu plików MP4 lub Smooth Streaming plików i mieć co najmniej jeden standard lub premium przesyłania strumieniowego punktu końcowego w pracy stan.

Usługa Media Services obsługuje następujące kodery na żądanie, które zostały opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Informacje o obsługiwanych koderów znajdują się w temacie [koderów](media-services-encode-asset.md).

## <a name="live-streaming"></a>Przesyłanie strumieniowe na żywo
W usłudze Azure Media Services kanał reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał otrzymuje na żywo wejściowych strumieni w jeden z dwóch sposobów:

* Na lokalny koder na żywo wysyła wielokrotnej szybkości transmisji bitów RTMP lub Smooth Streaming (pofragmentowany MP4) do kanału. Można użyć następujących koderów na żywo, które udostępniają Smooth Streaming wielokrotnej szybkości transmisji bitów: MediaExcel, Ateme, Wyobraź sobie komunikacji, Envivio, Cisco i Elemental. Następujące kodery na żywo wysyłają pliki RTMP: Adobe Flash kodera na żywo, Telestream Wirecast, Teradek, Haivision i Tricaster koderów. Pozyskiwane strumienie są przekazywane za pośrednictwem kanałów bez żadnych późniejszych transkodowanie i kodowania. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Strumień o pojedynczej szybkości transmisji bitów (w jednym z następujących formatów: RTP (MPEG-TS)), protokołu RTMP lub Smooth Streaming (pofragmentowany MP4)) jest wysyłany do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo z usługi Media Services. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

### <a name="channel"></a>Kanał
W usłudze Media Services [kanału](https://docs.microsoft.com/rest/api/media/operations/channel)s są zobowiązani do przetwarzania zawartości transmisji strumieniowej na żywo. Kanał zawiera ono wejściowy punkt końcowy (adres URL pozyskiwania) następnie podaj transcoder na żywo. Kanał odbiera strumienie wejściowe na żywo z transcoder na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem jednego lub więcej punkty końcowe. Kanały zapewniają również punkt końcowy w wersji zapoznawczej (adres URL w wersji zapoznawczej), używanej do wyświetlania i weryfikowania strumienia przed dalszego przetwarzania i dostarczania.

Po utworzeniu kanału możesz uzyskać adresu URL pozyskiwania i adres URL w wersji zapoznawczej. Aby uzyskać te adresy URL, kanał nie ma być w stanie uruchomienia. Gdy wszystko będzie gotowe uruchomić przekazywanie danych z transcoder na żywo do kanału, kanału musi być uruchomiona. Po uruchomieniu na żywo transcoder wprowadzania danych można wyświetlić podgląd strumienia.

Każde konto usługi Media Services może zawierać wiele kanałów, wiele programów i wielu punkty końcowe. W zależności od potrzeb przepustowości i zabezpieczeń StreamingEndpoint usług mogą być przeznaczone dla jednego lub kilku kanałów. Wszelkie StreamingEndpoint można ściąganie danych z dowolnego kanału.

### <a name="program-event"></a>Program (zdarzenie)
A [programu (zdarzenie)](https://docs.microsoft.com/rest/api/media/operations/program) pozwala na kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami (zdarzeń). Relacja kanału i programu jest podobny do tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.
Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie **ArchiveWindowLength** właściwości. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength określa również dostępny maksymalną ilość czasu klientów można wyszukiwać wstecz od bieżącego położenia na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program (zdarzenie) jest skojarzony z zasobem. Aby opublikować program musi utworzyć lokalizatora dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Aby uzyskać więcej informacji, zobacz:

* [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrona zawartości
### <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
Usługa Azure Media Services umożliwia zabezpieczenie od momentu, gdy opuszczą komputera za pośrednictwem przechowywania, przetwarzania i dostarczania multimediów. Usługa Media Services umożliwia dostarczanie zawartości dynamicznie szyfrowany za pomocą Standard AES (Advanced Encryption) (przy użyciu kluczy szyfrowania 128-bitowe) i szyfrowania common encryption (CENC) za pomocą PlayReady i Widevine DRM. Usługi Media Services udostępnia usługę dostarczania kluczy AES i licencje PlayReady do autoryzowanych klientów.

Obecnie można zaszyfrować następujących formatów przesyłania strumieniowego: HLS, MPEG DASH i Smooth Streaming. Nie można zaszyfrować pobierania progresywnego.

Dla usługi Media Services zaszyfrować element zawartości, należy skojarzyć klucza szyfrowania (CommonEncryption lub EnvelopeEncryption) z zawartości, a także skonfigurować zasady autoryzacji klucza.

Jeśli ma być przesyłana strumieniowo zasób zaszyfrowanych magazynu, musisz skonfigurować zasady dostarczania elementu zawartości, aby określić sposób dostarczania zawartości.

Strumień zleconą przez odtwarzacz usługi Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu koperty (z użyciem standardu AES) lub szyfrowania wspólnych (za pomocą PlayReady lub Widevine). Aby odszyfrować strumienia, odtwarzacza zażąda klucz z usługi dostarczania klucza. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

### <a name="token-restriction"></a>Ograniczenia tokenu
Zasady autoryzacji klucza zawartości może mieć jeden lub więcej ograniczeń: Otwórz, token ograniczenia lub ograniczenia adresów IP. Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę STS (Secure Token Service). Usługa Media Services obsługuje tokenów w formacie proste tokenów sieci Web (SWT) i format tokenu Web JSON (JWT). Usługi Media Services nie zapewnia bezpieczny tokenu usługi. Można utworzyć niestandardowy STS lub korzystać z usługi Microsoft Azure ACS do wydawania tokenów. Usługa tokenu Zabezpieczającego musi być skonfigurowana do utworzenia tokenu podpisany z określonego klucza i problem oświadczenia określony w konfiguracji ograniczenia tokenu. Usługa Media Services klucza dostawy zwróci żądanego (licencji lub klucza) do klienta, jeśli token jest prawidłowy i oświadczeń z tokenu dopasowania tych skonfigurowana dla klucza (lub licencji).

Podczas konfigurowania token ograniczony zasad, należy określić klucz podstawowy weryfikacji, wystawcy i parametry odbiorców. Klucz podstawowy weryfikacji zawiera klucz, który został podpisany token, z, wystawca jest bezpieczne usługi tokenu, który wystawia token. Odbiorców (nazywane również zakres) opisano celem tokenu lub zasobu tokenu zezwala na dostęp do. Usługa Media Services klucza dostawy weryfikuje, czy te wartości w tokenie pasują do wartości w szablonie.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Ochrona zawartości — omówienie](media-services-content-protection-overview.md)
- [Chroń za pomocą AES-128](media-services-protect-with-aes128.md)
- [Chroń za pomocą PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Dostarczanie
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Funkcję dynamicznego tworzenia pakietów
Podczas pracy z usługi Media Services, zaleca się kodowanie plików mezzanine do adaptacyjnej szybkości bitowej MP4 zestawu, a następnie wykonać konwersję zestaw na żądany format za pomocą [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego
StreamingEndpoint reprezentuje przesyłania strumieniowego usługa, która może dostarczać zawartość bezpośrednio do aplikacji klienckich odtwarzacza lub do Content Delivery Network (CDN) w celu dalszej dystrybucji (Azure Media Services udostępnia teraz integracji usługi Azure CDN.) Strumienia wychodzącego z usługą punktu końcowego przesyłania strumieniowego może być strumień na żywo lub element zawartości wideo na żądanie w ramach konta usługi Media Services. Klienci platformy Media Services wybierają **standardowy** punkt końcowy przesyłania strumieniowego lub co najmniej jeden punkt końcowy przesyłania strumieniowego **Premium**, zgodnie ze swoimi potrzebami. Standardowego punktu końcowego przesyłania strumieniowego jest odpowiednia dla większości obciążeń przesyłania strumieniowego. 

Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. Standardowe punkty końcowe przesyłania strumieniowego zapewniają elastyczność do dostarczania zawartości z prawie każdego urządzenia przy użyciu dynamicznego tworzenia pakietów w HLS i MPEG-DASH, Smooth Streaming, a także szyfrowania dynamicznego dla Microsoft PlayReady, Google Widevine Fairplay firmy Apple i AES128.  One również skalować z bardzo mała do bardzo dużych grup odbiorców z tysiącami równoczesnych podglądy dzięki integracji usługi Azure CDN. Jeśli masz zaawansowane obciążenia lub przesyłania strumieniowego wymagania dotyczące pojemności nie pasuje do standardowych przesyłania strumieniowego punktu końcowego przepływności elementów docelowych lub chcesz kontrolować pojemność usługi StreamingEndpoint do obsługi rośnie przepustowość musi, zalecane jest przydzielenie jednostki skalowania (znanej także jako premium jednostki przesyłania strumieniowego).

Zaleca się używania dynamicznego tworzenia pakietów i/lub szyfrowania dynamicznego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Aby uzyskać więcej informacji, zobacz [ten](media-services-portal-manage-streaming-endpoints.md) temat.

Domyślnie może mieć maksymalnie 2 punkty końcowe w ramach konta usługi Media Services przesyłania strumieniowego. Aby poprosić o wyższy limit, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

Rozliczenie jest przeprowadzane tylko gdy Twoje StreamingEndpoint jest w stanie uruchomienia.

### <a name="asset-delivery-policy"></a>Zasady dostarczania elementu zawartości
Jeden z kroków w przepływie pracy dostarczania zawartości Media Services konfiguruje [zasady dostarczania zasobów ](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy), które mają być przesyłane strumieniowo. Zasady dostarczania elementu zawartości informuje usługi Media Services sposób dla zawartości dostarczanej: w których przesyłania strumieniowego protokołu powinna zawartości dynamicznie umieszczone (na przykład, MPEG DASH, HLS, Smooth Streaming lub wszystkie), czy ma być dynamicznego szyfrowania zawartości i w jaki sposób (koperty lub szyfrowania common encryption).

Jeśli masz magazynu trwałego zaszyfrowane, aby mogła być przesłana strumieniowo zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieni zawartości przy użyciu zasady dostarczania określony. Na przykład w celu dostarczania zawartości zaszyfrowane za pomocą klucza szyfrowania Advanced Encryption (Standard AES), ustawić typ zasad DynamicEnvelopeEncryption. Aby usunąć szyfrowanie magazynu i zasobów w wyczyść strumienia, Ustaw typ zasad NoDynamicEncryption.

### <a name="progressive-download"></a>Pobierania progresywnego
Pobierania progresywnego pozwala rozpocząć odtwarzanie multimediów przed całego pliku została pobrana. Można tylko wczytać pliku MP4.

>[!NOTE]
>Musi odszyfrowania zaszyfrowanych zasoby, jeśli chcesz, aby był dostępny do pobierania progresywnego.

Aby zapewnić użytkownikom adresy URL pobierania progresywnego, należy najpierw utworzyć Lokalizator OnDemandOrigin. Utworzenie lokalizatora, stanowi ścieżki podstawowej w zasobie. Następnie należy dołączyć nazwę pliku MP4. Na przykład:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Przesyłanie strumieniowe zawartości do klientów. Aby zapewnić użytkownikom adresów URL przesyłania strumieniowego, trzeba najpierw utworzyć Lokalizator OnDemandOrigin. Utworzenie lokalizatora, stanowi ścieżki podstawowej w zasobie, który zawiera zawartość, którą chcesz przesyłać strumieniowo. Jednak aby można było strumienia tej zawartości należy zmodyfikować tę ścieżkę do dalszego. Aby utworzyć pełny adres URL do przesyłania strumieniowego pliku manifestu, musi łączyć lokalizatora ścieżki wartość i plik manifestu (filename.ism) Nazwa pliku. Następnie dołącz do lokalizatora ścieżki /Manifest i odpowiedni format (w razie potrzeby).

Można również strumienia zawartości za pośrednictwem połączenia SSL. Aby to zrobić, upewnij się, że Twoje adresy URL przesyłania strumieniowego rozpoczynać HTTPS. Obecnie usługa AMS nie obsługuje protokołu SSL z domen niestandardowych.  

>[!NOTE]
>Można tylko strumienia za pośrednictwem protokołu SSL Jeśli punktu końcowego przesyłania strumieniowego, z którego dostarczyć zawartość została utworzona po 10 września 2014 r. Jeśli Twoje adresy URL przesyłania strumieniowego są oparte na punktów końcowych przesyłania strumieniowego utworzone po 10 września, adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu SSL. Jeśli adres URL jest w starym formacie i chcesz mieć możliwość przesyłania strumieniowego za pośrednictwem protokołu SSL, należy utworzyć nowy punkt końcowy przesyłania strumieniowego. Użyj adresów URL tworzone w oparciu nowego punktu końcowego przesyłania strumieniowego, przesyłać strumieniowo zawartość przy użyciu protokołu SSL.

Poniższa lista zawiera opis różnych formatach przesyłania strumieniowego i zawiera przykłady:

* Smooth Streaming

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=mpd-time-csf) przesyłania strumieniowego

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live przesyłania strumieniowego V4 (HLS)

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=m3u8-aapl) przesyłania strumieniowego

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live przesyłania strumieniowego V3 (HLS)

{name}.streaming.mediaservices.windows.net/{locator konta usługi media Nazwa punktu końcowego ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3) przesyłania strumieniowego

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

