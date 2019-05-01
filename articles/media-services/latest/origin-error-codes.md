---
title: Azure Media Services pakowania i pochodzenia błędy | Dokumentacja firmy Microsoft
description: W tym temacie opisano błędy, które użytkownik może otrzymywać z usługi tworzenia pakietów usługi Azure Media Services.
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
ms.date: 04/28/2019
ms.author: juliako
ms.openlocfilehash: c350a989f23eb667923a53f6eb06dd55905b0fab
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927315"
---
# <a name="media-services-packaging-errors"></a>Błędy pakowania usługi Media Services 

W tym temacie opisano błędy, które użytkownik może otrzymywać z usługi Azure Media Services [tworzenia pakietów usługi](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Niewłaściwe żądanie

Żądanie zawiera nieprawidłowe informacje i zostanie odrzucony, za pomocą tych kodów błędu, jak i z jednego z następujących powodów:

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Adres URL składni lub błąd formatowania. Przykłady obejmują żądania dla nieprawidłowego typu, nieprawidłowy fragment lub nieprawidłową ścieżkę. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Żądanie nie ma szyfrowania tagu w adresie URL. Żądania CMAF wymagają potraktowane jak tag szyfrowania w adresie URL. Inne protokoły, które zostały skonfigurowane z co najmniej jeden typ szyfrowania również wymagać tag szyfrowania uściślania. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Żądanie do magazynu w celu spełnienia żądania nie powiodło się z powodu błędu nieprawidłowe żądanie. |

## <a name="403-forbidden"></a>403 Zabroniony

Żądanie jest niedozwolona z powodu jednej z następujących powodów:

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Niepowodzenie żądania do usługi storage do spełnienia żądania wystąpił błąd uwierzytelniania. Może to nastąpić, jeśli zostały obrócone klucze magazynu, a usługa nie może zsynchronizować klucze magazynu. <br/><br/>Skontaktuj się z platformy Azure obsługują, przechodząc do [Pomoc i obsługa techniczna](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w witrynie Azure portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Błąd operacji magazynu, dostępu nie powiodło się z powodu niewystarczających uprawnień konta. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Żądanie do magazynu w celu spełnienia żądania nie powiodło się, ponieważ konto magazynu jest wyłączone. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Błąd operacji magazynu, dostępu nie powiodło się z powodu błędów ogólnych. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Format danych wyjściowych jest zablokowana z powodu konfiguracji w StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Szyfrowanie jest wymagane dla zawartości, zasady dostarczania jest wymagany do formatu wyjściowego. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Szyfrowanie nie jest ustawiony w ustawieniach zasad dostarczania. |

## <a name="404-not-found"></a>404 — Nie odnaleziono

Operacja próbuje zajmującym się z zasobem, który już nie istnieje. Na przykład zasób mógł już zostać usunięty.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Nie można odnaleźć żądanego śledzenia. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Nie odnaleziono żądanego zasobu. |
|MPE_UNAUTHORIZED |0x80890244 |Dostęp nie ma autoryzacji. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Nie można odnaleźć żądanego sygnatury czasowej. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Nie można odnaleźć żądanego filtr manifestów dynamicznych. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Indeks żądanego fragmentu jest poza prawidłowym zakresem. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Nośnik, którego nie można odnaleźć wpisów można pobrać buforu moov na żywo. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nie można odnaleźć fragmentu na żądany czas dla określonej ścieżki.<br/><br/>Może być o tym, że fragment nie jest w magazynie. Wypróbuj inną warstwę prezentacji, która może zawierać fragmentu. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nie można odnaleźć wpis nośnika dla żądanego szybkości transmisji bitów w manifeście. <br/><br/>Może to być czy odtwarzacz wyświetlone pytanie o wideo śledzenie niektórych szybkości transmisji bitów, który nie był w manifeście.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nie można odnaleźć niektórych metadanych w manifeście lub nie można odnaleźć zmienianie bazy z magazynu. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Błąd operacji magazynu, nie można odnaleźć zasobu. |

## <a name="409-conflict"></a>409 Konflikt

Podany identyfikator zasobu na `PUT` lub `POST` operacja jest już zajęta przez istniejący zasób. Aby rozwiązać ten problem, należy użyć innego Identyfikatora zasobu.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Błąd operacji magazynu, błąd konfliktu.  |

## <a name="410"></a>410

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Transmisja strumieniowa na żywo, gdy filtr, który ma forceEndTimestamp ustawiona na wartość true, początkowy lub końcowy sygnatura czasowa jest spoza bieżącego okna DVR.|

## <a name="412-precondition-failure"></a>Niepowodzenie warunku wstępnego 412

Operacja określony element eTag, która jest inna niż wersja, która jest dostępny na serwerze, oznacza to błąd optymistycznej współbieżności. Ponów żądanie po przeczytaniu najnowszą wersję zasobu i aktualizowanie element eTag dla żądania.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Żądany fragment nie jest gotowa.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Błąd operacji magazynu, Niepowodzenie warunku wstępnego.|

## <a name="415-unsupported-media-type"></a>415 Nieobsługiwany typ nośnika

Format ładunku wysłane przez klienta jest w nieobsługiwanym formacie.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Nie należy stosować szyfrowania na już zaszyfrowaną zawartość.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Szyfrowanie jest nieprawidłowe dla formatu danych wejściowych.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Typ zasad dostarczania jest nieprawidłowy.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Pierwotne ustawienia mogą być współużytkowane przez wiele formatów danych wyjściowych.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Format multimediów lub typ nie jest obsługiwany. Na przykład Media Services nie obsługuje liczba poziomu jakości, który jest ponad 64. W tagu video FLV Media Services nie obsługuje klatki wideo za pomocą wielu dodatki Service Pack i wielu PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Format wejściowy żądany zasób nie jest obsługiwane. Usługa Media Services obsługuje Smooth (aktywne), w formacie MP4 (VoD) i formatów pobierania progresywnego.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Żądany format danych wyjściowych nie jest obsługiwane. Media Services obsługuje Smooth, DASH (CSF, CMAF), HLS (w wersji 3, 4, CMAF) i progresywnego pobierania formatów.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Typ napotkano nieobsługiwany szyfrowania.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Żądany typ nośnika nie jest obsługiwane przez format danych wyjściowych. Obsługiwane typy to wideo, audio lub podtytuł "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Źródło nośnika trwałego zostały zakodowane w formacie nośnika, który nie jest zgodny z formatem danych wyjściowych.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Element zawartości źródłowej zostały zakodowane w formacie wideo, który nie jest zgodny z formatem danych wyjściowych. H.264 AVC, H.265 (— HEVC, hev1 lub hvc1) są obsługiwane.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Element zawartości źródłowej zostały zakodowane przy użyciu formatu audio, który nie jest zgodny z formatem danych wyjściowych. Obsługiwane formaty audio są AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Nie można przekonwertować zasobu chronionego źródła do formatu wyjściowego.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Format ochrony nie jest obsługiwany przez format danych wyjściowych.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Format ochrony nie jest obsługiwany przez format danych wejściowych.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Nieprawidłowy wideo NAL jednostki, na przykład, tylko pierwszy NAL w przykładzie może być AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Nieprawidłowy rozmiar jednostki warstwy NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Nieprawidłowa wartość długości jednostki warstwy NAL.|
|MPE_FILTER_INVALID|0x80890236|Nieprawidłowy filtry manifestów dynamicznych.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Nieprawidłowy lub nieobsługiwany filtr wersje.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Nieprawidłowy typ filtru.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Nieprawidłowy zakres jest określany przez filtr.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Śledź nieprawidłowy atrybut jest określony przez filtr.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Prezentacja nieprawidłowa długość okna jest określony przez filtr.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Nieprawidłowy na żywo wycofanie jest określony przez filtr.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Tylko jeden element absTimeInHNS jest obsługiwany w starszych filtrów.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Brak w ogóle nie więcej strumieni po zastosowaniu filtrów.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Na żywo wycofania wykracza poza okna DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Na żywo wycofania jest większa niż okno prezentacji.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Przekroczono maksymalną domyślne dozwolonych filtry dziesięciu (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Wiele pierwszy operator jakość wideo jest niedozwolone w filtrach połączone żądania.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Musi to być jeden (1) liczby atrybutów jakości pierwszy, szybkości transmisji bitów.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Czas trwania segmentu HLS musi być mniejsze niż 1/3 okna DVR i HLS możesz się wycofać.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Czas trwania fragmentu musi być mniejsza lub równa około 20 sekund lub poziomy jakości danych wejściowych nie są wyrównane do czasu.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Błędu DTS nie można odnaleźć ReservedBox, gdy jest powinien być obecny w DTSSpecficBox podczas pole DTS podczas analizowania.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Błąd specyficznych dla usług DTS, kanałów znajdujący się w DTSSpecficBox podczas analizowania okno usług DTS.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Przykładowe niezgodność typów w DTSSpecficBox błąd specyficznych dla usług DTS.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Specyficzne dla usług DTS, wielu zasobów zostanie ustawiony błąd ale niezgodność typów przykładowe DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Błąd specyficznych dla usług DTS rozmiar strumienia core jest nieprawidłowy.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Błąd specyficznych dla usług DTS rozdzielczość próbki jest nieprawidłowa.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Błąd specyficznych dla usług DTS podrzędnych strumienia rozszerzenie indeksu jest nieprawidłowy.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Używanie usług DTS błędu, numeru bloku podrzędnych strumienia jest nieprawidłowy.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Używanie usług DTS błędu, częstotliwość próbkowania jest nieprawidłowy.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Błąd specyficznych dla usług DTS kod zegara odwołania w rozszerzeniu podrzędnych strumienia jest nieprawidłowy.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Błąd specyficznych dla usług DTS numer prelegentów ponowne mapowanie zestawu jest nieprawidłowa.|

Artykuły szyfrowania i przykłady zobacz:

- [Pojęcia: Usługa content protection](content-protection-overview.md)
- [Pojęcie: Zasady kluczy zawartości](content-key-policy-concept.md)
- [Pojęcie: Zasady przesyłania strumieniowego](streaming-policy-concept.md)
- [Przykład: ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
- [Przykład: ochrona przy użyciu DRM](protect-with-drm.md)

Aby uzyskać wskazówki filtru zobacz:

- [Pojęcia: o nazwie manifesty dynamiczne](filters-dynamic-manifest-overview.md)
- [Koncepcja: filtry](filters-concept.md)
- [Przykład: tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Przykład: tworzenie filtrów za pomocą platformy .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Przykład: tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

Aby uzyskać artykuły na żywo i przykłady zobacz:

- [Pojęcia: omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Pojęcie: Wydarzenia na żywo i na żywo dane wyjściowe](live-events-outputs-concept.md)
- [Przykład: samouczek dotyczący transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 nie zakresu żądania

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Błąd operacji magazynu, a zwrócił błąd http 416, nieprawidłowy zakres.|

## <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera

Podczas przetwarzania żądania usługi Media Services napotka błąd uniemożliwiający przetwarzania za kontynuację.  

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Odebrane i tłumaczenia z usług Winhttp kod błędu ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Odebrane i tłumaczenia z usług Winhttp kod błędu ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Odebrane i tłumaczenia z usług Winhttp kod błędu ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Błąd operacji magazynu, ogólny błąd wewnętrzny w jednej z błędy HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Błąd operacji magazynu, ogólne OperationTimedOut jednego błędy HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Błąd operacji magazynu, inne błędy HTTP 500 niż błąd wewnętrzny lub OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 — usługa niedostępna

Serwer nie może obecnie odbierać żądań. Ten błąd może być spowodowane zbyt żądania do usługi. Usługa Media Services, mechanizm ograniczania ogranicza użycie zasobów dla aplikacji, które wysyłać nadmierne żądania do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciągu kodu błędu, aby uzyskać bardziej szczegółowe informacje dotyczące przyczyny, że stało się błąd 503. Ten błąd nie zawsze oznacza ograniczania przepustowości.
> 

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Błąd operacji magazynu, odebrał błąd zajęty serwer HTTP 503.|

## <a name="provide-feedback"></a>Przekazywanie opinii

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="see-also"></a>Zobacz także

- [Kody błędów kodowania](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Pojęcia dotyczące usługi Azure Media Services](concepts-overview.md)
- [Przydziały i ograniczenia](limits-quotas-constraints.md)

## <a name="next-steps"></a>Kolejne kroki

[Przykład: dostęp do ErrorCode i wiadomości z ApiException przy użyciu platformy .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
