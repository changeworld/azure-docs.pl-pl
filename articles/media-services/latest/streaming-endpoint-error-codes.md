---
title: Błędy pakowania i pochodzenia usług Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie opisano błędy, które mogą być odbierane z usługi Azure Media Services Streaming Endpoint (Orgin).
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7d3a85e6fcc5b9d1c5ca1511cd7edd05ff5d9ae4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582699"
---
# <a name="streaming-endpoint-origin-errors"></a>Błędy punktu końcowego przesyłania strumieniowego (Origin) 

W tym temacie opisano błędy, które mogą być odbierane z usługi Azure Media Services [Streaming Endpoint](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Zła prośba

Żądanie zawiera nieprawidłowe informacje i jest odrzucane z tymi kodami błędów i z jednego z następujących powodów:

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Składnia adresu URL lub błąd formatu. Przykłady obejmują żądania nieprawidłowego typu, nieprawidłowego fragmentu lub nieprawidłowej ścieżki. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Żądanie nie ma tagu szyfrowania w adresie URL. Żądania CMAF wymagają tagu szyfrowania w adresie URL. Inne protokoły, które są skonfigurowane z więcej niż jednym typem szyfrowania również wymagają tagu szyfrowania do rozróżniania. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Żądanie do magazynu w celu spełnienia żądania nie powiodło się z błędem nieprawidłowego żądania. |

## <a name="403-forbidden"></a>403 Zakazane

Wniosek nie jest dozwolony z jednego z następujących powodów:

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Żądanie do magazynu w celu spełnienia żądania nie powiodło się z błędem uwierzytelniania. Może się tak zdarzyć, jeśli klucze magazynu zostały obrócene, a usługa nie może zsynchronizować kluczy magazynu. <br/><br/>Skontaktuj się z pomocą techniczną platformy Azure, przechodząc do [pomocy + pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w witrynie Azure portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Błąd operacji magazynu, dostęp nie powiódł się z powodu niewystarczających uprawnień konta. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Żądanie do magazynu w celu spełnienia żądania nie powiodło się, ponieważ konto magazynu jest wyłączone. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Błąd operacji magazynu, dostęp nie powiódł się z powodu błędów ogólnych. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Format wyjściowy jest zablokowany ze względu na konfigurację w StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Szyfrowanie jest wymagane dla zawartości, zasady dostarczania jest wymagane dla formatu wyjściowego. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Szyfrowanie nie jest ustawione w ustawieniach zasad dostarczania. |

## <a name="404-not-found"></a>404 — Nie znaleziono

Operacja próbuje działać na zasób, który już nie istnieje. Na przykład zasób mógł już zostać usunięty.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Nie znaleziono żądanej ścieżki. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Nie znaleziono żądanego zasobu. |
|MPE_UNAUTHORIZED |0x80890244 |Dostęp jest nieautoryzowany. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Nie znaleziono żądanego sygnatury czasowej. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Nie znaleziono żądanego dynamicznego filtru manifestu. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Żądany indeks fragmentu wykracza poza prawidłowy zakres. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Nie można znaleźć wpisów na żywo w mediach, aby uzyskać bufor mory. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nie można odnaleźć fragmentu w żądanym czasie dla określonego utworu.<br/><br/>Może to być, że fragment nie jest w magazynie. Spróbuj użyć innej warstwy prezentacji, która może mieć fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nie można odnaleźć wpisu nośnika żądanej szybkości transmisji bitów w manifeście. <br/><br/>Może być tak, że gracz poprosił o ścieżkę wideo o określonej szybkości transmisji bitów, która nie była w manifeście.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nie można znaleźć niektórych metadanych w manifeście lub nie można znaleźć rebase z magazynu. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Błąd operacji magazynu, nie znaleziono zasobu. |

## <a name="409-conflict"></a>409 Konflikt

Identyfikator podany dla zasobu `PUT` w `POST` lub operacji została podjęta przez istniejący zasób. Użyj innego identyfikatora zasobu, aby rozwiązać ten problem.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Błąd operacji magazynu, błąd konfliktu.  |

## <a name="410"></a>410

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|W przypadku przesyłania strumieniowego na żywo, gdy filtr, który ma forceEndTimestamp ustawiony na true, sygnatura czasowa rozpoczęcia lub zakończenia znajduje się poza bieżącym oknem DVR.|

## <a name="412-precondition-failure"></a>412 Awaria warunku wstępnego

Operacja określiła eTag, który różni się od wersji dostępnej na serwerze, czyli optymistyczny błąd współbieżności. Ponów próbę żądania po przeczytaniu najnowszej wersji zasobu i zaktualizowaniu eTagu na żądanie.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Żądany fragment nie jest gotowy.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Błąd operacji magazynowania, błąd warunku wstępnego.|

## <a name="415-unsupported-media-type"></a>415 Nieobsługiwał typu nośnika

Format ładunku wysyłany przez klienta jest w formacie nieobsługiwał.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Nie należy stosować szyfrowania na już zaszyfrowanej zawartości.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Szyfrowanie jest nieprawidłowe dla formatu wejściowego.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Typ zasad dostarczania jest nieprawidłowy.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Oryginalne ustawienia mogą być współużytkowane przez wiele formatów wyjściowych.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Format lub typ nośnika nie jest nieobsługiwał. Na przykład usługi Media Services nie obsługuje liczby poziomów jakości, która jest ponad 64. W tagu wideo FLV program Media Services nie obsługuje klatki wideo z wieloma sps i wieloma pps.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Format wejściowy żądanego zasobu nie jest obsługiwany. Usługi Media Services obsługują formaty pobierania Smooth (live), MP4 (VoD) i Progressive.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Żądany format danych wyjściowych nie jest obsługiwany. Usługi Media Services obsługują formaty pobierania Smooth, DASH(CSF, CMAF), HLS (v3, v4, CMAF) i Progressive.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Napotkano nieobsługicony typ szyfrowania.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Żądany typ nośnika nie jest obsługiwany przez format wyjściowy. Obsługiwane typy to napisy wideo, audio lub "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Nośnik zasobów źródłowych został zakodowany w formacie nośnika, który nie jest zgodny z formatem wyjściowym.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Zasób źródłowy został zakodowany w formacie wideo, który nie jest zgodny z formatem wyjściowym. H.264, AVC, H.265 (HEVC, hev1 lub hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Zasób źródłowy został zakodowany w formacie audio, który nie jest zgodny z formatem wyjściowym. Obsługiwane formaty audio to AAC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Zasobu chronionego ze źródła nie można przekonwertować na format wyjściowy.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Format ochrony nie jest obsługiwany przez format wyjściowy.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Format ochrony nie jest obsługiwany przez format wejściowy.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Nieprawidłowa jednostka NAL wideo, na przykład, tylko pierwszy NAL w próbce może być AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Nieprawidłowy rozmiar jednostki NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Nieprawidłowa wartość długości jednostki NAL.|
|MPE_FILTER_INVALID|0x80890236|Nieprawidłowe dynamiczne filtry manifestu.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Nieprawidłowe lub nieobsługiwały wersje filtrów.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Nieprawidłowy typ filtru.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Nieprawidłowy zakres jest określony przez filtr.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Nieprawidłowy atrybut ścieżki jest określony przez filtr.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Nieprawidłowa długość okna prezentacji jest określona przez filtr.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Nieprawidłowy powrót na żywo jest określony przez filtr.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Tylko jeden element absTimeInHNS jest obsługiwany w starszych filtrów.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Nie ma więcej strumieni w ogóle po zastosowaniu filtrów.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Powrót na żywo jest poza oknem DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Powrót na żywo jest większy niż okno prezentacji.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Przekroczono dziesięć (10) maksymalnych dozwolonych filtrów domyślnych.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Wielu operatorów pierwszej jakości wideo nie jest dozwolone w filtrach żądań łączonych.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Liczba atrybutów szybkości transmisji bitów pierwszej jakości musi być jedna (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Czas trwania segmentu HLS musi być mniejszy niż jedna trzecia okna DVR i HLS wycofać.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Czas trwania fragmentu musi być mniejsza lub równa około 20 sekund lub poziomy jakości wejściowej nie są wyrównane czasowo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Błąd specyficzny dla DTS, nie można znaleźć ReservedBox, gdy powinien być obecny w DTSSpecficBox podczas analizowania pola DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Błąd specyficzny dla DTS, nie znaleziono kanałów w DTSSpecficBox podczas analizowania pola DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Błąd specyficzny dla DTS, niezgodność typu próbki w DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Błąd specyficzny dla DTS, wiele zasobów jest ustawiany, ale niezgodność typu próbki DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Błąd specyficzny dla DTS, rozmiar strumienia rdzenia jest nieprawidłowy.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Błąd specyficzny dla DTS, rozdzielczość próbki jest nieprawidłowa.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Błąd specyficzny dla DTS, indeks rozszerzenia strumienia podrzędnego jest nieprawidłowy.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Błąd specyficzny dla DTS, numer bloku strumienia podrzędnego jest nieprawidłowy.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Błąd specyficzny dla DTS, częstotliwość próbkowania jest nieprawidłowa.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Błąd specyficzny dla DTS, kod zegara odwołania w rozszerzeniu strumienia podrzędnego jest nieprawidłowy.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Błąd specyficzny dla DTS, liczba głośników ponownie mapuje zestaw jest nieprawidłowa.|

Aby zapoznać się z artykułami i przykładami szyfrowania, zobacz:

- [Koncepcja: ochrona treści](content-protection-overview.md)
- [Koncepcja: Zasady dotyczące kluczy zawartości](content-key-policy-concept.md)
- [Koncepcja: Zasady przesyłania strumieniowego](streaming-policy-concept.md)
- [Przykład: ochrona za pomocą szyfrowania AES](protect-with-aes128.md)
- [Przykład: ochrona za pomocą DRM](protect-with-drm.md)

Aby uzyskać wskazówki dotyczące filtru, zobacz:

- [Koncepcja: manifesty dynamiczne](filters-dynamic-manifest-overview.md)
- [Koncepcja: filtry](filters-concept.md)
- [Przykład: tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Przykład: tworzenie filtrów za pomocą platformy .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Przykład: tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

Artykuły na żywo i przykłady można znaleźć na:

- [Koncepcja: przegląd transmisji na żywo](live-streaming-overview.md)
- [Koncepcja: Wydarzenia na żywo i wyjścia na żywo](live-events-outputs-concept.md)
- [Przykład: samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Zakres nie jest sytą

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Błąd operacji magazynu, zwrócony błąd http 416, nieprawidłowy zakres.|

## <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera

Podczas przetwarzania żądania usługi Media Services napotyka na jakiś błąd, który uniemożliwia kontynuowanie przetwarzania.  

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Odebrany i przetłumaczony z kodu błędu winhttp ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Odebrany i przetłumaczony z kodu błędu winhttp ERROR_WINHTTP_CONNECTION_ERROR (0x0000fe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Odebrany i przetłumaczony z kodu błędu winhttp ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Błąd operacji magazynu, ogólne InternalError jednego z błędów HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Błąd operacji magazynu, ogólne OperationTimedOut jednego z błędów HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Błąd operacji magazynu, inne błędy HTTP 500 niż InternalError lub OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 — usługa niedostępna

Serwer nie może obecnie odbierać żądań. Ten błąd może być spowodowany przez nadmierne żądania do usługi. Mechanizm ograniczania przepustowości usługi Media Services ogranicza użycie zasobów dla aplikacji, które sprawiają, że nadmierne żądanie do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciąg kodu błędu, aby uzyskać bardziej szczegółowe informacje o przyczynie, że wystąpił błąd 503. Ten błąd nie zawsze oznacza ograniczanie przepustowości.
> 

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x8089008|Błąd operacji magazynu, odebrano błąd zajęty serwerem HTTP 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="see-also"></a>Zobacz też

- [Kody błędów kodowania](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Pojęcia dotyczące usługi Azure Media Services](concepts-overview.md)
- [Limity przydziału i ograniczenia](limits-quotas-constraints.md)

## <a name="next-steps"></a>Następne kroki

[Przykład: dostęp do kodu errorcode i wiadomości z apiexception z .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
