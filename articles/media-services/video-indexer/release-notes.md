---
title: Informacje o wersji Azure Media Services Video Indexer | Microsoft Docs
description: Aby zachować aktualność najnowszych zmian, ten artykuł zawiera najnowsze aktualizacje Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 0583c0093d240026e3ebcdad7b14494f07986ec2
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968731"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Informacje o wersji Azure Media Services Video Indexer

Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje na temat:

* Najnowsze wersje
* Znane problemy
* Poprawki błędów
* Przestarzałe funkcje

## <a name="october-2019"></a>Październik 2019
 
* Wyszukaj animowane znaki w galerii

    W przypadku indeksowania animowanych znaków można teraz wyszukiwać je w postaci szpaltowej wideo na koncie. Aby uzyskać więcej informacji, zobacz [rozpoznawanie znaków animowanych](animated-characters-recognition.md).

## <a name="september-2019"></a>Wrzesień 2019 r.
 
Wiele zaliczek zapowiedziano w IBC 2019:
 
* Rozpoznawanie znaków animowanych (publiczna wersja zapoznawcza)

    Możliwość wykrywania grup AD rozpoznaje znaki w animowanej zawartości, poprzez integrację z niestandardową wizją. Aby uzyskać więcej informacji, zobacz [wykrywanie znaków animowanych](animated-characters-recognition.md).
* Identyfikacja w wielu językach (publiczna wersja zapoznawcza)

    Wykrywaj segmenty w wielu językach w ścieżce audio i twórz na ich podstawie wielojęzyczne transkrypcje. Wstępna pomoc techniczna: angielski, hiszpański, niemiecki i francuski. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrypcja zawartości w wielu językach](multi-language-identification-transcription.md).
* Wyodrębnianie jednostek nazwanych dla osób i lokalizacji

    Wyodrębnianie marek, lokalizacji i osób z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* Klasyfikacja typu zrzutu redakcyjnego

    Tagowanie zrzutów z typami redakcyjnymi, takimi jak zamknięcie, średnie zrzuty, dwa zrzuty, wewnętrzne, zewnętrzne itd. Aby uzyskać więcej informacji, zobacz [wykrywanie typów zrzutów redakcyjnych](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Temat inferencing — teraz obejmujący poziom 2
    
    Temat inferencing model obsługuje teraz bardziej szczegółowy stopień szczegółowości taksonomii IPTC. Przeczytaj wszystkie szczegółowe informacje o [Azure Media Services nowych innowacyjności opartych na AI](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Sierpień 2019 r.
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer wdrożony w programie Południowe Zjednoczone Królestwo

Teraz możesz utworzyć konto płatne Video Indexer w regionie Południowo-południe.

### <a name="new-editorial-shot-type-insights-available"></a>Dostępne są nowe szczegółowe informacje o typie zrzutu redakcyjnego

Nowe Tagi dodawane do zrzutów wideo udostępniają redakcyjne "typy zrzutów", aby identyfikować je za pomocą wspólnych fraz redakcyjnych używanych w przepływie pracy tworzenia zawartości, takich jak: Extreme Closeup, Closeup, Wide, medium, dwa zrzuty, na zewnątrz, w lewo i w prawo (dostępne w JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Dostępne są nowe osoby i lokalizacje

Video Indexer identyfikuje nazwane lokalizacje i osoby za pośrednictwem przetwarzania języka naturalnego (NLP) na podstawie OCR i transkrypcji wideo. Video Indexer używa algorytmu uczenia maszynowego do rozpoznawania, gdy określone lokalizacje (na przykład Eiffel Tower) lub osoby (na przykład Jan Nowak) są wywoływane w filmie wideo.

### <a name="keyframes-extraction-in-native-resolution"></a>Wyodrębnianie klatek kluczowych w rozdzielczości natywnej

Ramki kluczowe wyodrębnione przez Video Indexer są dostępne w oryginalnej rozdzielczości wideo.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>Zaplanowanie szkolenia niestandardowych modeli czołowych z obrazów

Uczenie się zdjęć z obrazów przenoszonych z trybu podglądu do GA (dostępne za pośrednictwem interfejsu API i portalu).

> [!NOTE]
> Nie ma wpływu na ceny związane z przejściem "wersja zapoznawcza do wersji zapoznawczej".

### <a name="hide-gallery-toggle-option"></a>Ukryj opcję przełączania galerii

Użytkownik może wybrać opcję ukrycia karty Galeria w portalu (podobnie jak w przypadku ukrycia karty przykłady).
 
### <a name="maximum-url-size-increased"></a>Zwiększono maksymalny rozmiar adresu URL

Obsługa ciągu zapytania URL o 4096 (zamiast 2048) na indeksowaniu wideo.
 
### <a name="support-for-multi-lingual-projects"></a>Obsługa projektów wielojęzycznych

Projekty można teraz tworzyć w oparciu o wideo indeksowane w różnych językach (tylko interfejsy API).

## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="editor-as-a-widget"></a>Edytor jako widżet

Edytor Video Indexer AI jest teraz dostępny jako widżet, który ma zostać osadzony w aplikacjach klienta.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów w portalu

Klienci mogą udostępniać formaty plików VTT, narzędzia SRT i TTML jako dane wejściowe dla modeli języka na stronie dostosowywania portalu.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer wdrożone na Japonia Wschodnia

Teraz można utworzyć konto płatne Video Indexer w regionie Japonia Wschodnia.

### <a name="create-and-repair-account-api-preview"></a>Tworzenie i naprawa interfejsu API konta (wersja zapoznawcza)

Dodano nowy interfejs API, który umożliwia [zaktualizowanie punktu końcowego lub klucza połączenia usługi Azure Media Service](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Ulepsz obsługę błędów podczas przekazywania 

Komunikat opisowy jest zwracany w przypadku nieprawidłowych konfiguracji bazowego konta Azure Media Services.

### <a name="player-timeline-keyframes-preview"></a>Klatki kluczowe programu Player — wersja zapoznawcza 

Możesz teraz zobaczyć podgląd obrazu za każdym razem na osi czasu odtwarzacza.

### <a name="editor-semi-select"></a>Wybór częściowo edytora

Teraz można wyświetlić podgląd wszystkich szczegółowych informacji, które są wybrane w wyniku wybrania konkretnego przedziału czasu wglądu w edytorze.

## <a name="may-2019"></a>Maj 2019 r.

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizowanie niestandardowego modelu języka z pliku napisów

[Tworzenie niestandardowego modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) i [Aktualizowanie niestandardowych modeli języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) interfejsy API obsługują teraz formaty plików VTT, narzędzia SRT i ttml jako dane wejściowe dla modeli języka.

Podczas wywoływania [interfejsu API aktualizowania transkrypcji wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)transkrypcja jest dodawana automatycznie. Model szkoleniowy skojarzony z filmem wideo jest również aktualizowany automatycznie. Aby uzyskać informacje na temat dostosowywania i uczenia modeli języka, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nowe formaty pobierania transkrypcji — TXT i CSV

Oprócz obsługiwanego formatu napisów (narzędzia SRT, VTT i TTML), Video Indexer teraz obsługuje pobieranie transkrypcji w formatach TXT i CSV.

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)
