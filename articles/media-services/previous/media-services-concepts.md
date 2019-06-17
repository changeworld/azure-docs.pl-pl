---
title: Pojęcia dotyczące usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie koncepcji usługi Azure Media
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 2b28dde812dcce120c951730c27809f7f024e122
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681556"
---
# <a name="azure-media-services-concepts"></a>Pojęcia dotyczące usługi Azure Media Services 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

Ten temat zawiera przegląd najważniejszych założeń usługi Media Services.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Zasoby i magazynu
### <a name="assets"></a>Elementy zawartości
[Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) zawiera pliki cyfrowe (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów) oraz metadane dotyczące tych plików. Przekazane pliki cyfrowe do elementu zawartości mogą one używane w usłudze Media Services, kodowanie i przesyłanie strumieniowe przepływów pracy.

Element zawartości jest mapowany na kontener obiektów blob na koncie usługi Azure Storage i pliki w elemencie zawartości są przechowywane jako blokowe obiekty BLOB w kontenerze. Stronicowe obiekty BLOB nie są obsługiwane przez usługę Azure Media Services.

Podczas podejmowania decyzji o usuwanej zawartości multimediów, przekazywanie i przechowywanie w zasobie, obowiązują następujące zastrzeżenia:

* Element zawartości może zawierać tylko pojedynczą, unikatowe wystąpienie zawartości multimedialnej. Na przykład pojedynczy edycji odcinek TV, filmu lub anonsu.
* Element zawartości nie może zawierać wielu wersji i edycji pliku audiowizualnych. Jednym z przykładów niepoprawne użycie elementu zawartości, czy próba przechowywać więcej niż jeden odcinek TV, anonsu lub kątów kamery z jednym produkcji wewnątrz elementu zawartości. Przechowywanie wielu wersji i edycji pliku audiowizualnych w zasobie może powodować trudności przesyłania zadań kodowania, przesyłanie strumieniowe i zabezpieczanie dostarczania elementu zawartości w dalszej części przepływu pracy.  

### <a name="asset-file"></a>Plik elementu zawartości
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) reprezentuje rzeczywisty plik wideo lub audio, który jest przechowywany w kontenerze obiektów blob. Plik zasobów jest zawsze skojarzone z elementem zawartości i zasobów może zawierać jeden lub wiele plików. Zadanie Media Services Encoder kończy się niepowodzeniem, jeśli obiekt pliku zasobów nie jest skojarzony z plikiem cyfrowych w kontenerze obiektów blob.

**AssetFile** wystąpienia i plik multimedialna znajdują się dwa różne obiekty. Wystąpienie AssetFile zawiera metadane dotyczące plików multimedialnych, a plik multimedialny zawiera zawartość multimedialna.

Nie należy próbować zmienić zawartość kontenerów obiektów blob, które zostały wygenerowane przez usługę Media Services bez korzystania z interfejsów API usługi Media.

### <a name="asset-encryption-options"></a>Opcje szyfrowania elementu zawartości
W zależności od typu zawartości, który chcesz przekazać, przechowywania i dostarczania Media Services udostępnia różne opcje szyfrowania, które można wybierać.

>[!NOTE]
>Szyfrowanie nie jest stosowane. Jest to wartość domyślna. Przy użyciu tej opcji zawartość nie jest chroniona w trakcie przesyłania lub przechowywania w magazynie.

Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tę opcję, aby przekazać zawartość.

**StorageEncrypted** — ta opcja umożliwia szyfrowanie zawartości lokalnie przy użyciu szyfrowania 256-bitowego szyfrowania AES, a następnie przekaż go do usługi Azure Storage gdzie są przechowywane, szyfrowane w stanie spoczynku. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Głównym zastosowaniem szyfrowania magazynu jest, gdy chcesz zabezpieczyć swoje wysokiej jakości multimedialnych plików wejściowych za pomocą silnego szyfrowania magazynowanych na dysku. 

W celu dostarczania elementu zawartości zaszyfrowanej magazynu, należy skonfigurować zasady dostarczania elementu zawartości, Media Services wie, jak chcesz dostarczanie zawartości. Zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość za pomocą zasad określony dostarczania (na przykład: AES, PlayReady lub bez szyfrowania). 

**CommonEncryptionProtected** — Użyj tej opcji, jeśli chcesz zaszyfrować (lub przekazać, jest już zaszyfrowane) zawartości za pomocą wspólnego szyfrowania lub technologii PlayReady DRM (na przykład, Smooth Streaming chronione za pomocą usług PlayReady DRM).

**EnvelopeEncryptionProtected** — Użyj tej opcji, jeśli chcesz chronić (lub przekazać już chroniony) HTTP Live Streaming (HLS) zaszyfrowany za pomocą Advanced Encryption Standard (AES). Jeśli przekazujesz HLS już zaszyfrowany za pomocą szyfrowania AES go musi mieć został zaszyfrowany za Transform Manager.

### <a name="access-policy"></a>Zasady dostępu
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definiuje uprawnienia (na przykład odczytu, zapisu i listy) i czasu trwania dostępu do elementu zawartości. Zazwyczaj przejdzie obiektu AccessPolicy do lokalizacji, która będzie następnie używana do dostępu do plików znajdujących się w zasobie.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="blob-container"></a>Kontener obiektów blob
Kontener obiektów blob zawiera grupowanie zestawu obiektów blob. Kontenery obiektów blob są używane w usłudze Media Services jako granic punktu kontroli dostępu i lokalizatory sygnatury dostępu współdzielonego (SAS) na zasoby. Konto usługi Azure Storage może zawierać nieograniczoną liczbę kontenerów obiektów blob. Kontener może przechowywać nieograniczoną liczbę obiektów blob.

>[!NOTE]
> Nie należy próbować zmienić zawartość kontenerów obiektów blob, które zostały wygenerowane przez usługę Media Services bez korzystania z interfejsów API usługi Media.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Lokalizatory
[Lokalizator](https://docs.microsoft.com/rest/api/media/operations/locator)s zapewnia punkt wejścia, dostępu do plików znajdujących się w zasobie. Zasady dostępu jest używana do definiowania uprawnień i czas trwania, że klient ma dostęp do danego zasobu. Lokalizatory może mieć wiele-do jednej relacji z zasad dostępu w taki sposób, że różne lokalizatorów można udostępnić godziny rozpoczęcia różnych i typów połączeń do różnych klientów podczas za pomocą tego samego uprawnienia i ustawienia czasu trwania; Jednak ze względu na ograniczenia zasad dostępu współdzielonego ustawione przez usługi magazynu platformy Azure, nie może mieć więcej niż pięć unikatowe lokalizatory skojarzone z danym elementem zawartości w tym samym czasie. 

Usługa Media Services obsługuje dwa typy lokalizatorów: Lokalizatory OnDemandOrigin używane do przesyłania strumieniowego (na przykład MPEG DASH, HLS lub Smooth Streaming) lub pobrać progresywnie multimediów oraz adres URL sygnatury dostępu Współdzielonego lokalizatory umożliwia przekazywanie lub pobieranie to\from plików multimediów usługi Azure storage. 

>[!NOTE]
>Listy uprawnień (AccessPermissions.List) nie stosuje się, tworząc Lokalizator OnDemandOrigin. 

### <a name="storage-account"></a>Konto magazynu
Dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Konto usługi multimediów można skojarzyć z co najmniej jedno konto magazynu. Konto może zawierać nieograniczoną liczbę kontenerów, tak długo, jak ich łączny rozmiar jest w obszarze 500TB na konto magazynu.  Usługa Media Services udostępnia poziomu narzędzi zestawu SDK, które umożliwiają zarządzanie wieloma kontami magazynu i dystrybucji zawartości podczas przekazywania do tych kont na podstawie metryk lub losowa dystrybucja równoważenia obciążenia. Aby uzyskać więcej informacji, zapoznaj się z praca [usługi Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Zadania i podzadania
A [zadania](https://docs.microsoft.com/rest/api/media/operations/job) jest zazwyczaj używana do procesu (na przykład, indeksu lub zakodować) prezentacji audio/wideo. W przypadku przetwarzania wielu filmów wideo, należy utworzyć zadanie dla każdego pliku wideo do zakodowania.

Zadanie zawiera metadane dotyczące przetwarzania do wykonania. Każde zadanie zawiera jeden lub więcej [zadań](https://docs.microsoft.com/rest/api/media/operations/task)s określające zadanie przetwarzania atomic, aktywów danych wejściowych, danych wyjściowych, zasoby, procesor multimediów i jego skojarzone ustawienia. Zadania w ramach danego zadania można łączyć w łańcuch, gdzie elementu zawartości wyjściowej, gdy jedno zadanie jest podawana jako danych wejściowych zasobu do następnego zadania. W ten sposób jedno zadanie może zawierać wszystkie niezbędne do przedstawienia media przetwarzania.

## <a id="encoding"></a>Kodowanie
Usługa Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Gdy zaczynasz pracę z usługą Media Services, należy zrozumieć różnicę między formatami koderów-dekoderów i plików.
Kodery-dekodery to oprogramowanie, które implementuje algorytmy kompresji i dekompresji formaty plików są kontenery zawierające skompresowane wideo.

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać zawartość z adaptacyjną szybkością transmisji bitów w formacie MP4 lub Smooth Streaming, kodowane w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatów przesyłania strumieniowego.

Aby móc korzystać z [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md), musisz Koduj plik (źródłowy) mezzanine do zestawu plików MP4 lub Smooth Streaming plików i ma co najmniej jeden standardowy lub premium końcowy przesyłania strumieniowego w Rozpoczęto stanu.

Usługa Media Services obsługuje następujące kodery na żądanie, które są opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Aby uzyskać informacje o obsługiwanych koderów, zobacz [koderów](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo
W usłudze Azure Media Services kanał reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał odbiera strumieni danych wejściowych w jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła różnych szybkościach transmisji bitów RTMP lub Smooth Streaming (pofragmentowany plik MP4) do kanału. Można użyć następujących koderów na żywo, które zwracają Smooth Streaming o różnych szybkościach transmisji bitów: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco i Elemental. Następujące kodery na żywo danych wyjściowych RTMP: Kodery Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision i Tricaster. Pozyskiwane strumienie są przekazywane za pośrednictwem kanałów bez dalszego transkodowania i kodowania. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Strumień o pojedynczej szybkości transmisji bitów (w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany plik MP4)) są wysyłane do kanału, który jest skonfigurowany do przeprowadzania kodowania na żywo za pomocą usługi Media Services. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

### <a name="channel"></a>Kanał
W usłudze Media Services [kanału](https://docs.microsoft.com/rest/api/media/operations/channel)s są zobowiązani do przetwarzania zawartości transmisji strumieniowej na żywo. Kanał oferuje wejściowy punkt końcowy (adres URL pozyskiwania) następnie podaj transkoder na żywo. Kanał odbiera strumieni danych wejściowych z transkoder na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem co najmniej jeden punkty. Kanały zapewniają również punkt końcowy (wersja zapoznawcza) (adres URL w wersji zapoznawczej), która umożliwia podgląd i weryfikować strumień przed dalszym przetwarzaniem i dostarczaniem.

Po utworzeniu kanału można uzyskać adresu URL pozyskiwania i adres URL w wersji zapoznawczej. Aby uzyskać te adresy URL, kanał nie ma być w stanie uruchomionym. Gdy wszystko jest gotowe rozpocząć wypychanie danych z transkoder na żywo do kanału, kanał musi zostać uruchomiona. Po uruchomieniu na żywo transkoder dane wprowadzane można wyświetlać podgląd strumienia.

Każde konto usługi Media Services może zawierać wielu kanałów, wiele programów i wielu punkty. W zależności od potrzeb przepustowości i bezpieczeństwa usługi StreamingEndpoint mogą być przeznaczone do obsługi co najmniej jednego kanału. Wszelkie StreamingEndpoint ściągnąć z dowolnym kanału.

### <a name="program-event"></a>Program (zdarzenie)
A [Program (zdarzenie)](https://docs.microsoft.com/rest/api/media/operations/program) pozwala na kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami (zdarzenia). Relacja kanału i programu jest podobny do tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.
Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie **ArchiveWindowLength** właściwości. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength również mówią, że maksymalną ilość czasu klientów można wyszukać w czasie od bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program (zdarzenie) jest skojarzony z elementem zawartości. Aby opublikować program, należy utworzyć Lokalizator dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Aby uzyskać więcej informacji, zobacz:

* [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrona zawartości
### <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services umożliwia dostarczanie zawartości dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES) (przy użyciu kluczy szyfrowania 128-bitowe) i szyfrowania common encryption (CENC) przy użyciu technologii PlayReady i/lub Widevine DRM. Media Services udostępnia również usługę dostarczania kluczy szyfrowania AES i licencje usługi PlayReady do autoryzowanych klientów.

Obecnie można szyfrować w formatach przesyłania strumieniowego: HLS, MPEG DASH i Smooth Streaming. Nie można zaszyfrować pobierania progresywnego.

Usługi Media Services zaszyfrować element zawartości, należy skojarzyć klucza szyfrowania (CommonEncryption lub EnvelopeEncryption) z elementów zawartości, a także skonfigurować zasady autoryzacji klucza.

Jeśli chcesz przesyłać strumieniowo do zasobu zaszyfrowanego magazynu, należy skonfigurować zasady dostarczania elementu zawartości, aby określić sposób dostarczania elementów zawartości.

Zleconą strumienia za pomocą odtwarzacza Media Services używa określonego klucza dynamiczne szyfrowanie zawartości przy użyciu szyfrowania koperty (przy użyciu standardu AES) lub szyfrowania common encryption (za pomocą usług PlayReady lub Widevine). Aby odszyfrować strumienia, odtwarzacza zażąda klucz usługi dostarczania kluczy. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa oblicza zasad autoryzacji, które podane dla klucza.

### <a name="token-restriction"></a>Ograniczenia tokenu
Zasady autoryzacji klucza zawartości mogą mieć jeden lub więcej ograniczeń: otworzyć, token ograniczenia lub ograniczenia adresów IP. Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę STS (Secure Token Service). Usługa Media Services obsługuje tokenów w formacie proste tokenów sieci Web (SWT) i format tokenu Web JSON (JWT). Usługa Media Services nie zapewnia zabezpieczenia usługi tokenu. Można tworzyć niestandardowych usług STS. Usługa STS musi być skonfigurowany do utworzenia tokenu, który został podpisany przy użyciu określonego klucza i problem oświadczenia określona w konfiguracji ograniczenia tokenu. Usługę dostarczania kluczy usługi Media Services zwraca żądany kluczy (lub licencji) do klienta, jeśli token jest prawidłowy i oświadczenia w token dopasowania tych skonfigurowane dla kluczy (lub licencji).

Podczas konfigurowania tokenu ograniczone zasad, należy określić podstawowy klucz weryfikacji, wystawcy i parametry odbiorców. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany za pomocą, wystawca jest usługa bezpiecznych tokenów, który wystawia token. Grupy odbiorców (nazywane również zakres) opisuje przeznaczenie tokenu lub zasób tokenu autoryzuje dostępu do. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasuje do wartości w szablonie.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Ochrona zawartości — omówienie](media-services-content-protection-overview.md)
- [Ochrona przy użyciu algorytmu AES-128](media-services-protect-with-aes128.md)
- [Ochrona za pomocą usług PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Dostarczanie
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Dynamiczne tworzenie pakietów
Podczas pracy z usługą Media Services, zaleca się kodowanie plików mezzanine do adaptacyjną szybkością transmisji bitów zestawu w formacie MP4, a następnie wykonać konwersję zestawu za pomocą żądany format [funkcję dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego
StreamingEndpoint reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub aby Content Delivery Network (CDN) w celu dalszego rozpowszechniania (usługi Azure Media Services udostępnia teraz integracji usługi Azure CDN.) Strumień wychodzący usługi punktu końcowego przesyłania strumieniowego może być strumień na żywo lub element zawartości wideo na żądanie w ramach konta usługi Media Services. Klienci platformy Media Services wybierają **standardowy** punkt końcowy przesyłania strumieniowego lub co najmniej jeden punkt końcowy przesyłania strumieniowego **Premium**, zgodnie ze swoimi potrzebami. Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. 

Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. Standardowe punkty końcowe przesyłania strumieniowego oferują elastyczność, do dostarczania zawartości do niemal każdego urządzenia, za pomocą funkcji dynamicznego tworzenia pakietów w HLS, MPEG-DASH i Smooth Streaming także dynamiczne szyfrowanie PlayReady firmy Microsoft, Google Widevine, Fairplay firmy Apple i AES128.  Mogą również skalowanie usług od bardzo małych do bardzo dużej liczby odbiorców za pomocą tysięcy równoczesnych przeglądarki za pomocą integracji z usługą Azure CDN. Jeśli masz zaawansowanych obciążeń lub wymagań dotyczących wydajności przesyłania strumieniowego nie pasują do standardowych cele przepływności punktu końcowego przesyłania strumieniowego albo chcesz kontrolować wydajność usługi StreamingEndpoint w celu obsługi coraz musi przepustowości, zaleca się Przydzielenie jednostek skalowania (znany także jako jednostki przesyłania strumieniowego premium).

Zaleca się używania funkcji dynamicznego tworzenia pakietów i/lub szyfrowania dynamicznego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Aby uzyskać więcej informacji, zobacz [ten](media-services-portal-manage-streaming-endpoints.md) temat.

Domyślnie możesz mieć maksymalnie 2 punkty końcowe w ramach konta usługi Media Services przesyłania strumieniowego. Aby zażądać wyższy limit, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

Opłaty są naliczane tylko wtedy, gdy usługi StreamingEndpoint jest w stanie uruchomienia.

### <a name="asset-delivery-policy"></a>Zasady dostarczania elementu zawartości
Jedną z czynności w przepływie pracy dostarczania zawartości usługi Media Services służy do konfigurowania [zasady dostarczania zasobów](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy), którą chcesz przesyłane strumieniowo. Zasady dostarczania elementu zawartości informuje Media Services, jak chcesz uzyskać element zawartości został dostarczony: do protokołu przesyłania strumieniowego powinny element zawartości można dynamicznie spakować (na przykład, między innymi MPEG DASH, HLS, Smooth Streaming lub wszystkie), czy chcesz szyfrować dynamicznie element zawartości i w jaki sposób (koperty lub szyfrowania common encryption).

Jeśli masz Magazyn trwały szyfrowane, zanim może być przesyłany strumieniowo element zawartości, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu zasad dostarczania określony. Na przykład w celu dostarczania elementów zawartości szyfrowane przy użyciu klucza szyfrowania Advanced Encryption Standard (AES), ustawić typ zasad DynamicEnvelopeEncryption. Aby usunąć szyfrowania magazynu i przesyłanie strumieniowe zawartości niezaszyfrowane, Ustaw typ zasad NoDynamicEncryption.

### <a name="progressive-download"></a>Pobierania progresywnego
Pobierania progresywnego umożliwia rozpoczęcie odtwarzania multimediów, zanim cały plik został pobrany. Można tylko progresywnie pliku MP4.

>[!NOTE]
>Musi odszyfrować zaszyfrowanej zasoby, jeśli chcesz je ma być dostępna dla pobierania progresywnego.

Aby zapewnić użytkownikom adresy URL pobierania progresywnego, trzeba najpierw utworzyć Lokalizator OnDemandOrigin. Tworzenie lokalizatora, daje ścieżki podstawowej do elementu zawartości. Następnie należy dołączyć nazwę pliku w formacie MP4. Na przykład:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Przesyłanie strumieniowe zawartości do klientów. Aby zapewnić użytkownikom adresów URL przesyłania strumieniowego, trzeba najpierw utworzyć Lokalizator OnDemandOrigin. Tworzenie lokalizatora, zapewnia ścieżki podstawowej do zasobu z zawartością, który ma być przesyłana strumieniowo. Jednak aby móc przesyłać strumieniowo zawartość należy zmodyfikować tę ścieżkę do dalszych. Do konstruowania pełny adres URL do pliku manifestu przesyłania strumieniowego, należy połączyć wartość ścieżki lokalizatora i manifest (filename.ism) Nazwa pliku. Następnie dołącz do ścieżki lokalizatora /Manifest i odpowiedni format (jeśli jest to konieczne).

Można również przesyłać strumieniowo zawartość, za pośrednictwem połączenia SSL. Aby to zrobić, upewnij się, że Twoje adresy URL przesyłania strumieniowego uruchomić przy użyciu protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu SSL z zastosowaniem domen niestandardowych.  

>[!NOTE]
>Można tylko przesyłanie strumieniowe za pośrednictwem protokołu SSL Jeśli utworzono punkt końcowy przesyłania strumieniowego, z którego umożliwia dostarczanie zawartości po 10 września 2014 r. Jeśli Twoje adresy URL przesyłania strumieniowego są oparte na punkty końcowe przesyłania strumieniowego utworzonych po 10 września, adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego, które zawierają "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu SSL. Jeśli adres URL jest w starym formacie i chcesz mieć możliwość przesyłania strumieniowego za pośrednictwem protokołu SSL, należy utworzyć nowy punkt końcowy przesyłania strumieniowego. Użyj adresów URL, utworzone w oparciu o nowy punkt końcowy przesyłania strumieniowego do przesyłania strumieniowego zawartości za pośrednictwem protokołu SSL.

Poniższa lista zawiera opis różnych formatów przesyłania strumieniowego i przedstawiono przykłady:

* Smooth Streaming

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live przesyłania strumieniowego V4 (HLS)

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live przesyłania strumieniowego (HLS) V3

{przesyłania strumieniowego punktu końcowego nazwie pobicia konta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

