---
title: Zaawansowane samouczki dotyczące usługi Media Encoder Premium Workflow
description: Ten dokument zawiera wskazówki, które pokazują, jak wykonywać zaawansowane zadania za pomocą przepływu pracy usługi Media Encoder Premium, a także jak tworzyć złożone przepływy pracy za pomocą projektanta przepływu pracy.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251013"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Zaawansowane samouczki dotyczące usługi Media Encoder Premium Workflow
## <a name="overview"></a>Omówienie
Ten dokument zawiera wskazówki, które pokazują, jak dostosować przepływy pracy za pomocą **Projektanta przepływu pracy**. Rzeczywiste pliki przepływu pracy można znaleźć [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>Toc
Poruszane są następujące tematy:

* [Kodowanie MXF do pojedynczej szybkości transmisji bitów MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Uruchamianie nowego przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Korzystanie z wprowadzania pliku multimedialnego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Sprawdzanie strumieni multimediów](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Dodawanie kodera wideo dla . Generowanie plików MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kodowanie strumienia audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multipleksowanie strumieni audio i wideo do kontenera MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zapisywanie pliku MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Tworzenie zasobu usługi Media Services z pliku wyjściowego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testowanie gotowego przepływu pracy lokalnie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodowanie MXF do wielobitowych plików MP4 - aktywne dynamiczne pakowanie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Dodawanie jednego lub więcej dodatkowych wyjść MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurowanie nazw danych wyjściowych plików](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Dodawanie oddzielnej ścieżki dźwiękowej](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Dodawanie pliku SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodowanie MXF do wielobitowego MP4 - ulepszony plan](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Omówienie przepływu pracy w celu poprawy
  * [Konwencje nazewnictwa plików](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Właściwości składnika publikowania w katalogu głównym przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Czy wygenerowane nazwy plików wyjściowych opierają się na opublikowanych wartościach właściwości](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Dodawanie miniatur do wielobitowego wydruku MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Omówienie przepływu pracy w celu dodania miniatur do
  * [Dodawanie kodowania JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Radzenie sobie z konwersją przestrzeni kolorów](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Pisanie miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Wykrywanie błędów w przepływie pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Zakończony przepływ pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Przycinanie wielobitowe mp4 na podstawie czasu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Omówienie przepływu pracy, aby rozpocząć dodawanie przycinania do](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Korzystanie z trymera strumienia](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Zakończony przepływ pracy](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Przedstawiamy składnik skryptowy](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skrypty w ramach przepływu pracy: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Przycinanie wielobitowe mp4 oparte na ramce](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Omówienie planu, aby rozpocząć dodawanie przycinania do](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Korzystanie z kodu XML listy klipów](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modyfikowanie listy klipów ze składnika skryptowego](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Dodawanie właściwości ClippingEnabled convenience](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Kodowanie MXF do pojedynczej szybkości transmisji bitów MP4
W tej sekcji pokazano, jak utworzyć pojedynczą szybkość transmisji bitów . PLIK MP4 z AAC-HE zakodowany audio z . pliku wejściowego MXF.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Uruchamianie nowego przepływu pracy
Otwórz Projektanta przepływu pracy i wybierz pozycję Plik > nowy plan > transkodowania

Nowy przepływ pracy zawiera trzy elementy:

* Podstawowy plik źródłowy
* Kod XML listy klipów
* Plik wyjściowy/zasób  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nowy przepływ pracy kodowania*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Korzystanie z wprowadzania pliku multimedialnego
Aby zaakceptować wejściowy plik multimedialny, rozpoczyna się od dodania składnika Wprowadzanie pliku multimedialnego. Aby dodać składnik do przepływu pracy, poszukaj go w polu wyszukiwania Repozytorium i przeciągnij żądany wpis do okienka projektanta. Powtórz czynność wprowadzania pliku multimedialnego i połącz składnik Podstawowy plik źródłowy z pinem wejściowym Nazwa pliku z wejścia pliku multimedialnego.

![Wejście połączonego pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Wejście połączonego pliku multimedialnego*

Początkowo należy zidentyfikować odpowiedni przykładowy plik do użycia podczas projektowania niestandardowego przepływu pracy. Aby to zrobić, kliknij tło okienka projektanta i poszukaj właściwości Podstawowy plik źródłowy w okienku właściwości po prawej stronie. Kliknij ikonę folderu i wybierz żądany plik do testowania przepływu pracy. Składnik Wprowadzanie pliku multimedialnego sprawdza plik i wypełnia jego piny wyjściowe, aby odzwierciedlić szczegóły kontrolowanego przez niego przykładowego pliku.

![Dane wejściowe zapełnionego pliku multimedialnego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Dane wejściowe zapełnionego pliku multimedialnego*

Teraz, gdy dane wejściowe są wypełniane, następnym krokiem jest skonfigurowanie ustawień kodowania danych wyjściowych. Podobnie jak skonfigurowano podstawowy plik źródłowy, teraz skonfiguruj właściwość Zmienna folderu wyjściowego, tuż pod nią.

![Skonfigurowane właściwości wejścia i wyjścia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Skonfigurowane właściwości wejścia i wyjścia*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Sprawdzanie strumieni multimediów
Często pożądane jest, aby wiedzieć, jak strumień wygląda, jak przepływa przez przepływ pracy. Aby sprawdzić strumień w dowolnym punkcie przepływu pracy, wystarczy kliknąć kod wyjściowy lub wejściowy na dowolnym składniku. W takim przypadku spróbuj kliknąć pin wyjścia Nieskompresowanego wideo z wejścia pliku multimedialnego. Zostanie otwarte okno dialogowe umożliwiające sprawdzenie wychodzącego wideo.

![Sprawdzanie nieskompresowanego pinu wyjściowego wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Sprawdzanie nieskompresowanego pinu wyjściowego wideo*

W tym przypadku pokazuje, że film zawiera wejście 1920x1080 przy 24 klatkach na sekundę w próbkowaniu 4:2:2 dla wideo prawie 2 minuty.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Dodawanie kodera wideo dla . Generowanie plików MP4
Teraz nieskompresowany film wideo i wiele nieskompresowanych pinów wyjściowych audio są dostępne do użycia na wejściu pliku multimedialnego. Aby zakodować przychodzące wideo, do przepływu pracy należy dodać składnik kodowania — w tym przypadku do generowania . mp4.

Aby zakodować strumień wideo do formatu H.264, dodaj składnik AVC Video Encoder do powierzchni projektanta. Ten składnik pobiera rozpakowyczony strumień wideo jako wejście i zapewnia skompresowany strumień wideo AVC na pin wyjścia.

![Niepodłączony koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Niepodłączony koder AVC*

Jego właściwości określają, jak dokładnie dzieje się kodowanie. Przyjrzyjmy się niektórym ważniejszym ustawieniom:

* Szerokość wyjścia i wysokość wyjścia: określa rozdzielczość zakodowanego wideo. W tym przypadku 640x360 jest dobrym ustawieniem.
* Liczba klatek na sekundę: po ustawieniu na przekazywanie, po prostu przyjmie liczbę klatek na sekundę, można to jednak zastąpić. Taka konwersja liczby klatek na sekundę nie jest kompensowane ruchem.
* Profil i poziom: określa profil i poziom AVC. Aby wygodnie uzyskać więcej informacji na temat różnych poziomów i profili, kliknij ikonę znaku zapytania w składniku AVC Video Encoder, a strona pomocy pokaże więcej szczegółów na temat każdego z poziomów. W tym przykładzie użyj profilu głównego na poziomie 3.2 (domyślnie).
* Tryb kontroli szybkości i szybkość transmisji bitów (kb/s): w tym scenariuszu wybierz wyjście o stałej szybkości transmisji bitów (CBR) przy 1200 kb/s
* Format wideo: zawiera informacje o VUI (Video Usability Information), który zostanie zapisany w strumieniu H.264 (informacje po stronie, które mogą być używane przez dekoder w celu zwiększenia wyświetlania, ale nie są niezbędne do prawidłowego dekodowania):
* NTSC (typowe dla USA lub Japonii, przy użyciu 30 kl./s)
* PAL (typowy dla Europy, z 25 kl./s)
* Tryb rozmiaru GOP: ustaw stały rozmiar GOP dla naszych celów z interwałem klucza 2 sekund z zamkniętymi PLIKAMI GOPs. Ustawienie 2 sekund zapewnia zgodność z dynamicznego pakowania usługi Azure Media Services zapewnia.

Aby nakarmić koder AVC, podłącz nieskompresowany pin wyjścia wideo ze składnika Wejście pliku media do nieskompresowanego pinu wejściowego wideo z kodera AVC.

![Podłączony koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Podłączony koder główny AVC*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Kodowanie strumienia audio
W tym momencie oryginalny nieskompresowany strumień audio nadal musi zostać skompresowany. Do kompresji strumienia audio dodaj składnik AAC Encoder (Dolby) do przepływu pracy.

![Niepodłączony koder AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Niepołączony koder AAC*

Teraz jest niezgodność: jest tylko jeden nieskompresowany pin wejściowy audio z kodera AAC, podczas gdy więcej niż prawdopodobne wejście pliku multimedialnego będzie miało dwa różne nieskompresowane strumienie audio dostępne: jeden dla lewego kanału audio i jeden po prawej stronie. (Jeśli masz do czynienia z dźwiękiem przestrzennym, to sześć kanałów). Nie można więc bezpośrednio podłączyć dźwięku ze źródła wejściowego pliku multimedialnego do kodera audio AAC. Składnik AAC oczekuje tak zwanego "przeplatanego" strumienia audio: pojedynczego strumienia, który ma zarówno lewy, jak i prawy kanał przeplatany ze sobą. Gdy wiemy z naszego źródłowego pliku multimedialnego, że ścieżki audio znajdują się w jakiej pozycji w źródle, możemy wygenerować taki przeplatany strumień audio z prawidłowo przypisanymi pozycjami głośników dla lewej i prawej strony.

Po pierwsze, jeden chce wygenerować przeplot strumienia z wymaganych kanałów audio źródła. Składnik Audio Stream Interleaver obsługuje to za nas. Dodaj go do przepływu pracy i podłącz do niego wyjścia audio z wejścia pliku multimedialnego.

![Connected Audio Stream Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Connected Audio Stream Interleaver*

Teraz, gdy mamy przeplot strumienia audio, nadal nie określiliśmy, gdzie przypisać pozycję lewego lub prawego głośnika. Aby to określić, możemy wykorzystać pozycję prelegenta.

![Dodawanie cederadownika pozycji głośnika](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Dodawanie cederadownika pozycji głośnika*

Skonfiguruj nadajnik pozycji głośnika do użytku ze strumieniem wejściowym stereo za pośrednictwem filtru predefiniowanych kodera "Niestandardowy" i ustawienia predefiniowane kanału o nazwie "2.0 (L,R)." (Spowoduje to przypisanie pozycji lewego głośnika do kanału 1, a pozycji prawego głośnika do kanału 2).

Podłącz wyjście wzmacniacza pozycji głośnika do wejścia kodera AAC. Następnie powiedz koderowi AAC, aby pracował z "2.0 (L,R)" Channel Preset, więc wie, że radzi sobie z dźwiękiem stereo jako wejściem.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multipleksowanie strumieni audio i wideo do kontenera MP4
Biorąc pod uwagę nasz strumień wideo zakodowany w AVC i nasz strumień audio zakodowany w AAC, możemy uchwycić oba w . pojemnik MP4. Proces mieszania różnych strumieni w jeden jest nazywany "multipleksing" (lub "muxing"). W tym przypadku przeplatamy strumienie audio i wideo w jednej spójnej. pakiet MP4. Składnik, który koordynuje to dla . Kontener MP4 jest nazywany multiplekserem ISO MPEG-4. Dodaj jeden do powierzchni projektanta i połącz do swoich danych wejściowych zarówno koder wideo AVC, jak i koder AAC.

![Podłączony multiplekser MPEG4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Podłączony multiplekser MPEG4*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Zapisywanie pliku MP4
Podczas zapisywania pliku wyjściowego używany jest składnik Wyjście pliku. Możemy podłączyć to do wyjścia ISO MPEG-4 Multiplexer tak, że jego wyjście zostanie zapisany na dysku. Aby to zrobić, podłącz pin wyjściowy kontenera (MPEG-4) do pinu wejścia zapisu danych wyjściowych pliku.

![Podłączone dane wyjściowe pliku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Podłączone dane wyjściowe pliku*

Nazwa pliku, który jest używany jest określana przez File właściwości. Chociaż ta właściwość może być zakodowana na stałe do danej wartości, najprawdopodobniej chce ją ustawić za pomocą wyrażenia.

Aby przepływ pracy automatycznie określał właściwość nazwa pliku wyjściowego na podstawie wyrażenia, kliknij przycisk obok nazwy pliku (obok ikony folderu). Z menu rozwijanego wybierz "Wyrażenie". Spowoduje to wyświetlenie edytora wyrażeń. Najpierw wyczyść zawartość edytora.

![Pusty edytor wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Pusty edytor wyrażeń*

Edytor wyrażeń umożliwia wprowadzenie dowolnej wartości literału, zmieszanej z jedną lub większą liczrą zmiennymi. Zmienne zaczynają się od znaku dolara. Gdy trafisz na klawisz $, edytor pokazuje pole rozwijane z wyborem dostępnych zmiennych. W naszym przypadku użyjemy kombinacji zmiennej katalogu wyjściowego i zmiennej nazwy pliku wejściowego podstawowego:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Wypełniony edytor wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Wypełniony edytor wyrażeń*

> [!NOTE]
> Aby wyświetlić plik wyjściowy zadania kodowania na platformie Azure, należy podać wartość w edytorze wyrażeń.
>
>

Po potwierdzeniu wyrażenia przez naciśnięcie ok, okno właściwości podgląd, jaka wartość File właściwość jest rozpoznawana w tym momencie w czasie.

![Wyrażenie pliku rozwiązuje dir danych wyjściowych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Wyrażenie pliku rozwiązuje dir danych wyjściowych*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Tworzenie zasobu usługi Media Services z pliku wyjściowego
Podczas gdy napisaliśmy plik wyjściowy MP4, nadal musimy wskazać, że ten plik należy do zasobu wyjściowego, który usługi multimedialne generuje w wyniku wykonywania tego przepływu pracy. W tym celu używany jest węzeł Plik/zasoby wyjściowe na kanwie przepływu pracy. Wszystkie pliki przychodzące do tego węzła stanowią część wynikowego zasobu usługi Azure Media Services.

Podłącz składnik Wyjście pliku do składnika Plik wyjściowy/Składnik Zasobów, aby zakończyć przepływ pracy.

![Zakończony przepływ pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Zakończony przepływ pracy*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Testowanie gotowego przepływu pracy lokalnie
Aby przetestować przepływ pracy lokalnie, naciśnij przycisk odtwarzania na pasku narzędzi u góry. Po zakończeniu wykonywania przepływu pracy sprawdź dane wyjściowe wygenerowane w skonfigurowanym folderze wyjściowym. Zobaczysz gotowy plik wyjściowy MP4, który został zakodowany z pliku źródłowego wejściowego MXF.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Kodowanie MXF do MP4 - wielobitowe dynamiczne opakowanie włączone
W tym instruktażu tworzy się zestaw wielu plików MP4 o szybkości transmisji bitów z kodowanym dźwiękiem AAC z jednego pliku . pliku wejściowego MXF.

Gdy do użycia w połączeniu z funkcjami dynamicznego pakowania oferowanymi przez usługę Azure Media Services jest wymagane wyjście zasobów o wielu szybkościach transmisji bitów, konieczne będzie wygenerowanie wielu plików MP4 wyrównanych do gop, z których każdy ma inną szybkość transmisji bitów i rozdzielczość. Aby to zrobić, [kodowanie MXF w jednym bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) instruktauówka zapewnia nam dobry punkt wyjścia.

![Uruchamianie przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Uruchamianie przepływu pracy*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Dodawanie jednego lub więcej dodatkowych wyjść MP4
Każdy plik MP4 w naszym powstałym zasobie usługi Azure Media Services obsługuje inną szybkość transmisji bitów i rozdzielczość. Dodajmy jeden lub więcej plików wyjściowych MP4 do przepływu pracy.

Aby upewnić się, że wszystkie nasze kodery wideo zostały utworzone przy tych samych ustawieniach, najwygodniej jest powielić już istniejący koder wideo AVC i skonfigurować inną kombinację rozdzielczości i szybkości transmisji bitów (dodajmy jedną z 960 x 540 przy 25 klatkach na sekundę przy 2,5 Mb/s ). Aby zduplikować istniejący koder, skopiuj go na powierzchni projektanta.

Podłącz nieskompresowany pin wyjścia wideo wejścia pliku multimedialnego do naszego nowego komponentu AVC.

![Drugi koder AVC podłączony](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Drugi koder AVC podłączony*

Teraz dostosuj konfigurację naszego nowego emodera AVC do wyjścia 960x540 z prędkością 2,5 Mb/s. (Użyj jego właściwości "Szerokość wyjściowa", "Wysokość wyjściowa" i "Bitrate (kbps)" dla tego.)

Biorąc pod uwagę, że chcemy używać wynikowego zasobu wraz z dynamicznym pakowaniem usługi Azure Media Services, punkt końcowy przesyłania strumieniowego musi być w stanie generować z tych plików MP4 HLS/Fragmented MP4/DASH fragmentów, które są dokładnie wyrównane do siebie w taki sposób, że klienci, którzy przełączają się między różnymi szybkościami transmisji bitów uzyskać jeden płynne ciągłe środowisko wideo i audio. Aby tak się stało, musimy upewnić się, że we właściwościach obu koderów AVC rozmiar GOP ("grupa zdjęć") dla obu plików MP4 jest ustawiony na 2 sekundy, co można wykonać przez:

* ustawienie trybu rozmiaru GOP na stały rozmiar GOP i
* do dwóch sekund.
* również ustawić GOP IDR Control do zamkniętego GOP, aby upewnić się, że wszystkie GOP stoją na własną rękę bez zależności

Aby ułatwić zrozumienie tego przepływu pracy, zmień nazwę pierwszego kodera AVC na "AVC Video Encoder 640x360 1200 kbps" i drugiego kodera AVC "AVC Video Encoder 960x540 2500 kbps".

Teraz dodaj drugi multiplekser ISO MPEG-4 i drugie wyjście pliku. Podłącz multiplekser do nowego kodera AVC i upewnij się, że jego dane wyjściowe są kierowane do wyjścia pliku. Następnie podłącz również wyjście czajnika audio AAC do wejścia nowego multipleksera. Dane wyjściowe pliku z kolei można następnie podłączyć do węzła Plik wyjściowy/Składnik aktywów, aby dodać go do zasobu usługi Media Services, który zostanie utworzony.

![Drugi muxer i wyjście pliku podłączone](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Drugi muxer i wyjście pliku podłączone*

Aby uzyskać zgodność z dynamicznym pakowaniem usługi Azure Media Services, skonfiguruj tryb fragmentu multipleksera na liczbę lub czas trwania gop i ustaw gops na fragment na 1. (Powinno to być ustawienie domyślne).

![Tryby fragmentu Muxera](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Tryby fragmentu Muxera*

Uwaga: możesz powtórzyć ten proces dla innych kombinacji szybkości transmisji bitów i rozdzielczości, które mają zostać dodane do danych wyjściowych zasobu.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurowanie nazw danych wyjściowych plików
Mamy więcej niż jeden plik dodany do zasobu wyjściowego. Zapewnia to potrzebę upewnienia się, że nazwy plików dla każdego z plików wyjściowych różnią się od siebie, a może nawet zastosować konwencję nazewnictwa plików, aby stało się jasne z nazwy pliku, z którą masz do czynienia.

Nazewnictwo danych wyjściowych plików może być kontrolowane za pomocą wyrażeń w projektancie. Otwórz okienko właściwości dla jednego ze składników Wyjście pliku i otwórz edytor wyrażeń dla właściwości Plik. Nasz pierwszy plik wyjściowy został skonfigurowany za pomocą następującego wyrażenia (patrz samouczek przechodzenia z [MXF do pojedynczego wyjścia MP4 o szybkości transmisji bitów bitów):](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Oznacza to, że nasza nazwa pliku jest określana przez dwie zmienne: katalog wyjściowy do zapisu i nazwę bazową pliku źródłowego. Pierwsza z nich jest widoczna jako właściwość w katalogu głównym przepływu pracy, a druga jest określana przez plik przychodzący. Katalog danych wyjściowych jest to, czego używasz do testowania lokalnego; ta właściwość zostanie zastąpiona przez aparat przepływu pracy, gdy przepływ pracy jest wykonywany przez procesor multimediów oparty na chmurze w usłudze Azure Media Services.
Aby nadać obu naszym plikom wyjściowym spójne nazewnictwo danych wyjściowych, zmień pierwsze wyrażenie nazewnictwa plików na:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a drugi do:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Wykonaj pośredni test, aby upewnić się, że oba pliki wyjściowe MP4 są poprawnie wygenerowane.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Dodawanie oddzielnej ścieżki dźwiękowej
Jak zobaczymy później, gdy wygenerujemy plik .ism, aby przejść z naszymi plikami wyjściowymi MP4, będziemy również wymagać tylko audio plik MP4 jako ścieżkę audio dla naszego adaptacyjnego przesyłania strumieniowego. Aby utworzyć ten plik, dodaj dodatkowy muxer do przepływu pracy (MULTIPLEKSER ISO-MPEG-4) i podłącz pin wyjściowy kodera AAC z pinem wejściowym dla ścieżki 1.

![Audio Muxer Dodano](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer Dodano*

Utwórz trzeci składnik Dane wyjściowe pliku, aby wyprowadzić strumień wychodzący z muxera i skonfigurować wyrażenie nazewnictwa plików jako:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer tworzenia wyjścia pliku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer tworzenia wyjścia pliku*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Dodawanie pliku . Plik ISM SMIL
Aby dynamiczne opakowanie działało w połączeniu z plikami MP4 (i tylko dla audio MP4) w naszym zasobie Usługi Media Services, potrzebujemy również pliku manifestu (zwanego również plikiem "SMIL": Zsynchronizowany język integracji multimediów). Ten plik wskazuje usługi Azure Media Services, jakie pliki MP4 są dostępne dla dynamicznego pakowania i które z tych należy wziąć pod uwagę dla przesyłania strumieniowego dźwięku. Typowy plik manifestu dla zestawu plików MP4 z pojedynczym strumieniem audio wygląda następująco:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Plik .ism zawiera w instrukcji switch, odniesienie do każdego z poszczególnych plików wideo MP4 i oprócz tych również jeden (lub więcej) audio plików odniesienia do MP4, który zawiera tylko audio.

Generowanie pliku manifestu dla naszego zestawu mp4 można wykonać za pomocą składnika o nazwie "AMS Manifest Writer". Aby go użyć, przeciągnij go na powierzchnię i połącz piny wyjściowe "Write Complete" z trzech składników wyjścia plików do danych wejściowych modułu zapisującego manifestu AMS. Następnie upewnij się, że dane wyjściowe modułu zapisującego manifestu usługi AMS są podłączane do pliku wyjściowego/zasobu.

Podobnie jak w przypadku innych naszych składników wyjściowych plików, skonfiguruj nazwę wyjściową pliku .ism za pomocą wyrażenia:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nasz gotowy przepływ pracy wygląda jak poniżej:

![Gotowy przepływ pracy MXF do wielobitowego MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Gotowy przepływ pracy MXF do wielobitowego MP4*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Kodowanie MXF do wielobitowego MP4 - ulepszony plan
W [poprzednim przewodniku przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) widzieliśmy, jak pojedynczy zasób wejściowy MXF można przekonwertować na zasób wyjściowy z wieloma bitrate plików MP4, plik MP4 tylko audio i plik manifestu do użytku w połączeniu z usługi Azure Media Services dynamicznepakowanie.

W tym przewodniku pokazano, jak niektóre aspekty mogą być ulepszone i bardziej wygodne.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Omówienie przepływu pracy w celu poprawy
![Wielobitowy przepływ pracy MP4 w celu zwiększenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Wielobitowy przepływ pracy MP4 w celu zwiększenia*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Konwencje nazewnictwa plików
W poprzednim przepływie pracy określiliśmy proste wyrażenie jako podstawę generowania nazw plików wyjściowych. Mamy jednak pewne powielanie: wszystkie poszczególne składniki pliku wyjściowego określiły takie wyrażenie.

Na przykład nasz składnik wyjściowy pliku dla pierwszego pliku wideo jest skonfigurowany za pomocą tego wyrażenia:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Podczas gdy w przypadku drugiego wideo wyjściowego mamy wyrażenie takie jak:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Czy nie byłoby czystsze, mniej podatne na błędy i wygodniejsze, gdybyśmy mogli usunąć niektóre z tego powielania i uczynić rzeczy bardziej konfigurowalny zamiast? Na szczęście możemy: możliwości ekspresji projektanta w połączeniu z możliwością tworzenia niestandardowych właściwości w naszym katalogu głównym przepływu pracy zapewni dodatkową warstwę wygody.

Załóżmy, że będziemy dysk konfiguracji nazwy pliku z szybkości transmisji bitów poszczególnych plików MP4. Te szybkości transmisji bitów będziemy dążyć do skonfigurowania w jednym centralnym miejscu (na głównym naszym wykresie), skąd będą dostępne, aby skonfigurować i zwiększyć generowanie nazw plików. Aby to zrobić, rozpoczynamy od opublikowania właściwości bitrate zarówno z koderów AVC do katalogu głównego naszego przepływu pracy, dzięki czemu staje się dostępny zarówno z katalogu głównego, jak i z koderów AVC. (Nawet jeśli są wyświetlane w dwóch różnych miejscach, istnieje tylko jedna wartość podstawowa.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Właściwości składnika publikowania w katalogu głównym przepływu pracy
Otwórz pierwszy koder AVC, przejdź do właściwości Bitrate (kbps) i z listy rozwijanej wybierz pozycję Publikuj.

![Publikowanie właściwości bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikowanie właściwości bitrate*

Skonfiguruj okno dialogowe publikowania, aby publikować je w katalogu głównym naszego wykresu przepływu pracy, z opublikowaną nazwą "video1bitrate" i czytelną nazwą wyświetlaną "Video 1 Bitrate". Skonfiguruj niestandardową nazwę grupy o nazwie "Przesyłanie strumieniowe szybkości transmisji bitów" i naciśnij pozycję Publikuj.

![Publikowanie właściwości bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Okno dialogowe publikowania właściwości bitrate*

Powtórz to samo dla właściwości bitrate drugiego kodera AVC i nazwij go "video2bitrate" z nazwą wyświetlaną "Video 2 Bitrate", w tej samej grupie niestandardowej "Streaming Bitrates".

Jeśli teraz sprawdzimy właściwości katalogu głównego przepływu pracy, zobaczymy naszą grupę niestandardową z dwiema opublikowanymi właściwościami. Oba odzwierciedlają wartość ich odpowiedniego kodera AVC bitrate.

![Opublikowane rekwizyty szybkości transmisji bitów w katalogu głównym przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Za każdym razem, gdy chcemy uzyskać dostęp do tych właściwości z kodu lub wyrażenia, możemy to zrobić w ten sposób:

* z wbudowanego kodu ze składnika tuż pod katalogiem głównym: node.getPropertyAsString('.. /video1bitrate', null)
* w wyrażeniu: ${ROOT_video1bitrate}

Uzupełnijmy grupę "Streaming Bitrates", publikując na niej również naszą szybkość transmisji bitów ścieżki audio. We właściwościach kodera AAC wyszukaj ustawienie Szybkość transmisji bitów i wybierz pozycję Publikuj z listy rozwijanej obok. Opublikuj w katalogu głównym wykresu o nazwie "audio1bitrate" i nazwie wyświetlanej "Audio 1 Bitrate" w naszej niestandardowej grupie "Streaming Bitrates".

![Okno dialogowe publikowania szybkości transmisji bitów audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Okno dialogowe publikowania szybkości transmisji bitów audio*

![Wynikowe rekwizyty wideo i audio na korzeniu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Wynikowe rekwizyty wideo i audio na korzeniu*

Zmiana dowolnej z tych trzech wartości również ponownie konfiguruje i zmienia wartości na odpowiednich składnikach, z których są połączone (i gdzie są publikowane).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Czy wygenerowane nazwy plików wyjściowych opierają się na opublikowanych wartościach właściwości
Zamiast hardcoding nasze wygenerowane nazwy plików, możemy teraz zmienić nasze wyrażenie nazwy pliku na każdym ze składników wyjście pliku polegać na właściwości bitrate opublikowaliśmy w katalogu głównym wykresu. Począwszy od naszego pierwszego wyjścia pliku, znajdź File właściwości i edytować wyrażenie w ten sposób:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Różne parametry w tym wyrażeniu są dostępne i wprowadzane przez naciśnięcie znaku dolara na klawiaturze w oknie wyrażenia. Jednym z dostępnych parametrów jest nasza właściwość video1bitrate, którą opublikowaliśmy wcześniej.

![Uzyskiwanie dostępu do parametrów w wyrażeniu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Uzyskiwanie dostępu do parametrów w wyrażeniu*

Zrób to samo dla wyjścia pliku dla naszego drugiego filmu:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

i dla wyjścia pliku tylko audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Jeśli teraz zmienimy szybkość transmisji bitów dla dowolnego pliku wideo lub audio, odpowiedni koder zostanie ponownie skonfigurowany, a konwencja nazw plików oparta na szybkości transmisji bitów będzie honorowana automatycznie.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Dodawanie miniatur do wielobitowego wydruku MP4
Począwszy od przepływu pracy, który generuje [wielobitowe wyjście MP4 z wejścia MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)będziemy teraz przyglądać się dodawaniu miniatur do danych wyjściowych.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Omówienie przepływu pracy w celu dodania miniatur do
![Wielobitowy przepływ pracy MP4 do uruchomienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Wielobitowy przepływ pracy MP4 do uruchomienia*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Dodawanie kodowania JPG
Sercem naszej generacji miniatur będzie komponent JPG Encoder, zdolny do wyprowadzania plików JPG.

![Koder JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Koder JPG*

Nie możemy jednak bezpośrednio połączyć naszego strumienia wideo nieskompresowanego z wejścia pliku multimedialnego do kodera JPG. Zamiast tego oczekuje, że zostaną przekazane pojedyncze ramki. Możemy to zrobić za pomocą komponentu Video Frame Gate.

![Podłączanie bramy ramy do kodera JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Podłączanie bramy ramy do kodera JPG*

Brama ramki raz na tyle sekund lub klatek pozwala na przejście klatki wideo. Interwał i przesunięcie czasu, z którym to się dzieje, można konfigurować we właściwościach.

![Właściwości bramka klatki wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Właściwości bramka klatki wideo*

Utwórzmy miniaturę co minutę, ustawiając tryb na Czas (sekundy) i Interwał na 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Radzenie sobie z konwersją przestrzeni kolorów
Chociaż logiczne wydaje się zarówno nieskompresowane piny wideo bramy ramki, jak i wejście pliku multimedialnego, otrzymamy ostrzeżenie, jeśli to zrobimy.

![Błąd przestrzeni kolorów wejściowych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Błąd przestrzeni kolorów wejściowych*

Dzieje się tak dlatego, że sposób, w jaki informacje o kolorze są reprezentowane w naszym oryginalnym strumieniu nieskompresowanego wideo, pochodzącym z naszego MXF, różni się od tego, czego oczekuje koder JPG. W szczególności oczekuje się, że tak zwana "przestrzeń kolorów" "RGB" lub "Skala szarości" będzie przepływać. Oznacza to, że przychodzący strumień wideo bramy klatki wideo musi najpierw zastosować konwersję dotyczącą jego przestrzeni kolorów.

Przeciągnij na przepływ pracy Color Space Converter - Intel i podłącz go do naszej bramy ramki.

![Podłączanie konwertera przestrzeni kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Podłączanie konwertera przestrzeni kolorów*

W oknie właściwości wybierz wpis BGR 24 z listy Ustawienia wstępne.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Pisanie miniatur
Inny niż nasz film MP4, składnik JPG Encoder wyprowadza więcej niż jeden plik. Aby sobie z tym poradzić, można użyć składnika Scen Szukaj JPG File Writer: zajmuje przychodzące miniatury JPG i zapisuje je, każda nazwa pliku jest sufiksem przez inną liczbę. (Liczba zazwyczaj wskazuje liczbę sekund/jednostek w strumieniu, z których została narysowana miniatura).

![Przedstawiamy scenę Szukaj JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Przedstawiamy scenę Szukaj JPG File Writer*

Konfigurowanie właściwości Ścieżka folderu wyjściowego za pomocą wyrażenia: ${ROOT_outputWriteDirectory}

oraz właściwość Prefiks nazwy pliku z:

    ${ROOT_sourceFileBaseName}_thumb_

Prefiks określa sposób nazywania plików miniatur. Są one sufiksem z liczbą wskazującą pozycję kciuka w strumieniu.

![Właściwości modułu zapisującego pliki JPG Wyszukiwanie scen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Właściwości modułu zapisującego pliki JPG Wyszukiwanie scen*

Połącz moduł zapisujący plik JPG wyszukiwania scen z węzłem Plik wyjściowy/Zasób.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Wykrywanie błędów w przepływie pracy
Podłącz wejście konwertera przestrzeni kolorów do surowego nieskompresowanego wyjścia wideo. Teraz wykonaj lokalne uruchomienie testowe dla przepływu pracy. Istnieje duża szansa, że przepływ pracy nagle przestanie wykonywać i wskaże czerwonym konturem składnika, który napotkał błąd:

![Błąd konwertera przestrzeni kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Błąd konwertera przestrzeni kolorów*

Kliknij małą czerwoną ikonę "E" w prawym górnym rogu komponentu Color Space Converter, aby zobaczyć, co jest powodem niepowodzenia próby kodowania.

![Okno dialogowe Konwerter przestrzeni kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Okno dialogowe Konwerter przestrzeni kolorów*

Okazuje się, jak widać, że przychodzący standard przestrzeni kolorów dla konwertera przestrzeni kolorów musi być rec601 dla naszej żądanej konwersji YUV do RGB. Najwyraźniej nasz strumień nie wskazuje jego rec601. (Rec 601 jest standardem kodowania z przeplotem analogowych sygnałów wideo w cyfrowej formie wideo. Określa aktywny region obejmujący 720 próbek luminancji i 360 próbek chrominancji na linię. System kodowania kolorów jest znany jako YCbCr 4:2:2.)

Aby rozwiązać ten problem, wskażemy w metadanych naszego strumienia, że mamy do czynienia z zawartością rec601. W tym celu użyjemy składnika Video Data Type Updater, który umieścimy między naszym źródłem pierwotnym a składnikiem konwersji przestrzeni kolorów. Ten updater typu danych umożliwia ręczną aktualizację niektórych właściwości typu danych wideo. Skonfiguruj go tak, aby wskazywał standard przestrzeni kolorów "Rec 601". Powoduje to, że video data type updater do tagu strumienia z "Rec 601" przestrzeni kolorów, jeśli nie zdefiniowano jeszcze przestrzeni kolorów. (Nie zastąpi żadnych istniejących metadanych, chyba że zaznaczono pole wyboru Zastądnienie).

![Aktualizowanie standardu przestrzeni kolorów w aktualizacji typów danych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizowanie standardu przestrzeni kolorów w aktualizacji typów danych*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Zakończony przepływ pracy
Teraz, gdy nasz przepływ pracy jest gotowy, wykonaj kolejny test, aby zobaczyć, jak przechodzi.

![Gotowy przepływ pracy dla wyjścia wielopłonowego z miniaturami](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Gotowy przepływ pracy dla wyjścia wielopłonowego z miniaturami*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Przycinanie wielobitowe mp4 na podstawie czasu
Począwszy od przepływu pracy, który generuje [wielobitowe wyjście MP4 z wejścia MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)będziemy teraz szukać przycinania źródłowego wideo opartego na znacznikach czasu.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Omówienie przepływu pracy, aby rozpocząć dodawanie przycinania do
![Uruchamianie przepływu pracy w celu dodania przycinania do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Uruchamianie przepływu pracy w celu dodania przycinania do*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Korzystanie z trymera strumienia
Składnik Przycinanie strumienia umożliwia przycinanie początku i zakończenia bazy strumienia wejściowego na informacjach o czasie (sekundy, minuty, ...). Trymer nie obsługuje przycinania opartego na ramce.

![Trymer strumieniowy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Trymer strumieniowy*

Zamiast łączyć kodery AVC i przypisywanie pozycji głośnika bezpośrednio z wejściem pliku multimedialnego, umieścimy między tymi trymerem strumienia. (Jeden dla sygnału wideo i jeden dla przeplotu sygnału audio.)

![Umieść trymer strumieniowy pomiędzy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Umieść trymer strumieniowy pomiędzy*

Skonfigurujmy trymer tak, abyśmy przetwarzali wideo i audio tylko od 15 sekund do 60 sekund w filmie.

Przejdź do właściwości trymera strumienia wideo i skonfiguruj właściwości czas rozpoczęcia (15 s) i czas zakończenia (60 s). Aby upewnić się, że zarówno nasz trymer audio, jak i wideo są zawsze skonfigurowane do tych samych wartości początkowych i końcowych, publikujemy je w katalogu głównym przepływu pracy.

![Publikowanie właściwości czasu rozpoczęcia z trymera strumienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publikowanie właściwości czasu rozpoczęcia z trymera strumienia*

![Okno dialogowe publikowania właściwości dla czasu rozpoczęcia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Okno dialogowe publikowania właściwości dla czasu rozpoczęcia*

![Okno dialogowe publikowania właściwości dla czasu zakończenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Okno dialogowe publikowania właściwości dla czasu zakończenia*

Jeśli teraz sprawdzimy katalog główny naszego przepływu pracy, obie właściwości są starannie wyświetlane i konfigurowalne stamtąd.

![Opublikowane właściwości dostępne w katalogu głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Opublikowane właściwości dostępne w katalogu głównym*

Teraz otwórz właściwości przycinania z trymera audio i skonfiguruj czas rozpoczęcia i zakończenia za pomocą wyrażenia, które odwołuje się do opublikowanych właściwości w katalogu głównym naszego przepływu pracy.

Dla czasu rozpoczęcia przycinania dźwięku:

    ${ROOT_TrimmingStartTime}

i na czas zakończenia:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Zakończony przepływ pracy
![Zakończony przepływ pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Zakończony przepływ pracy*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Przedstawiamy składnik skryptowy
Składniki skryptów mogą wykonywać dowolne skrypty podczas faz wykonywania naszego przepływu pracy. Istnieją cztery różne skrypty, które mogą być wykonywane, każdy z określonymi cechami i ich własne miejsce w cyklu życia przepływu pracy:

* **commandScript**
* **skryptu realizscript**
* **procesInputScript**
* **lifeCycleScript**

Dokumentacja składnika skryptowego jest bardziej szczegółowa dla każdego z powyższych. W [poniższej sekcji](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)składnik skryptów **realizeScript** jest używany do konstruowania xml listy klipów na bieżąco po uruchomieniu przepływu pracy. Ten skrypt jest wywoływany podczas konfiguracji składnika, co zdarza się tylko raz w jego cyklu życia.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Skrypty w ramach przepływu pracy: hello world
Przeciągnij składnik skryptowy na powierzchnię projektanta i zmień jego nazwę (na przykład "SetClipListXML").

![Dodawanie składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie składnika skryptowego*

Podczas sprawdzania właściwości składnika skryptowego zostaną wyświetlone cztery różne typy skryptów, z których każdy można skonfigurować do innego skryptu.

![Właściwości składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Właściwości składnika skryptowego*

Wyczyść procesInputScript i otwórz edytor dla realizeScript. Teraz jesteśmy skonfigurowane i gotowe do rozpoczęcia skryptów.

Skrypty są napisane w Groovy, dynamicznie skompilowanym języku skryptowym dla platformy Java, który zachowuje zgodność z Java. W rzeczywistości większość kodu Java jest prawidłowy kod Groovy.

Napiszmy prosty skrypt hello world groovy w kontekście naszego realizeScript. W edytorze należy wprowadzić następujące informacje:

    node.log("hello world");

Teraz wykonaj lokalny przebieg testowy. Po tym uruchomieniu sprawdź (za pośrednictwem karty System na składniku skryptowym) właściwość Dzienniki.

![Witam dane wyjściowe dziennika świata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Witam dane wyjściowe dziennika świata*

Obiekt węzła, który nazywamy metodą dziennika, odwołuje się do naszego bieżącego "węzła" lub składnika, w ramach których skryptujemy. Każdy składnik jako taki ma możliwość wyprowadzania danych rejestrowania, dostępnych za pośrednictwem karty systemowej. W takim przypadku wyprowadzamy dosłowny ciąg "hello world". Ważne jest, aby zrozumieć tutaj jest to, że może to okazać się nieocenionym narzędziem debugowania, zapewniając wgląd w to, co skrypt faktycznie robi.

Z naszego środowiska skryptów mamy również dostęp do właściwości na innych składnikach. Spróbuj tego:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Nasze okno dziennika pokazuje nam, co następuje:

![Rejestrowanie danych wyjściowych w celu uzyskiwania dostępu do ścieżek węzłów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Rejestrowanie danych wyjściowych w celu uzyskiwania dostępu do ścieżek węzłów*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Przycinanie wielobitowe mp4 oparte na ramce
Począwszy od przepływu pracy, który generuje [wielobitowe wyjście MP4 z wejścia MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)będziemy teraz przyglądać się przycinaniu źródłowego wideo na podstawie liczby klatek.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Omówienie planu, aby rozpocząć dodawanie przycinania do
![Przepływ pracy, aby rozpocząć dodawanie przycinania do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Przepływ pracy, aby rozpocząć dodawanie przycinania do*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Korzystanie z kodu XML listy klipów
We wszystkich poprzednich samouczkach przepływu pracy użyliśmy składnika Wprowadzanie pliku multimedialnego jako naszego źródła wejściowego wideo. W tym konkretnym scenariuszu będziemy jednak używać składnika Źródło listy klipów. Nie powinno to być preferowanym sposobem pracy; Użyj źródła listy klipów tylko wtedy, gdy istnieje prawdziwy powód, aby to zrobić (jak w następnym przypadku, gdzie korzystamy z możliwości przycinania listy klipów).

Aby przełączyć się z naszego wejścia pliku multimedialnego do źródła listy klipów, przeciągnij składnik Źródło listy klipów na powierzchnię projektową i połącz pinezkę XML listy klipów z węzłem XML listy klipów projektanta przepływu pracy. To wypełnia Clip List Source z pinów wyjściowych, zgodnie z naszym wideo wejściowych. Teraz podłącz nieskompresowane piny wideo i nieskompresowane audio ze źródła listy klipów do odpowiednich koderów AVC i interleaver strumienia audio. Teraz usuń wejście pliku multimedialnego.

![Zastąpiono wejście pliku multimedialnego źródłem listy klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Zastąpiono wejście pliku multimedialnego źródłem listy klipów*

Składnik Źródło listy klipów przyjmuje jako wejście "Clip List XML". Po wybraniu pliku źródłowego do przetestowania lokalnie, ta lista klipów xml jest automatycznie wypełniana.

![Automatyczna wypełniana lista klipów XML, właściwość XML](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatyczna wypełniana lista klipów XML, właściwość XML*

Patrząc nieco bliżej xml, tak to wygląda:

![Okno dialogowe Edytowanie listy klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Okno dialogowe Edytowanie listy klipów*

Nie odzwierciedla to jednak możliwości xml listy klipów. Jedną z opcji, którą mamy, jest dodanie elementu "Trim" zarówno pod źródłem wideo, jak i audio, w ten sposób:

![Dodawanie elementu przycinania do listy klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Dodawanie elementu przycinania do listy klipów*

Jeśli zmodyfikujesz xml listy klipów w ten sposób i przeprowadzisz lokalny test, zobaczysz, że film został poprawnie przycięty w zakresie od 10 do 20 sekund w filmie.

W przeciwieństwie do tego, co się dzieje, gdy wykonujesz uruchamianie lokalne jednak ten sam xml listy klipów nie będzie miał taki sam efekt po zastosowaniu w przepływie pracy, który jest uruchamiany w usłudze Azure Media Services. Po uruchomieniu usługi Azure Premium Encoder xml listy klipów jest generowany za każdym razem ponownie, na podstawie pliku wejściowego zadanie kodowania zostało podane. Oznacza to, że wszelkie zmiany, które robimy na xml niestety zostaną zastąpione.

Aby przeciwdziałać wymazaniu xml listy klipów po uruchomieniu zadania kodowania, możemy ponownie wygenerować je w locie tuż po rozpoczęciu naszego przepływu pracy. Takie akcje niestandardowe można podjąć za pośrednictwem tak zwanego "składnika skryptowego". Aby uzyskać więcej informacji, zobacz [Przedstawianie składnika skryptowego](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Przeciągnij składnik skryptowy na powierzchnię projektanta i zmień jego nazwę na "SetClipListXML".

![Dodawanie składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie składnika skryptowego*

Podczas sprawdzania właściwości składnika skryptowego wyświetlane są cztery różne typy skryptów, z których każdy można skonfigurować do innego skryptu.

![Właściwości składnika skryptowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Właściwości składnika skryptowego*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Modyfikowanie listy klipów ze składnika skryptowego
Zanim będziemy mogli przepisać xml listy klipów, który jest generowany podczas uruchamiania przepływu pracy, musimy mieć dostęp do właściwości i zawartości xml listy klipów. Możemy to zrobić w ten sposób:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Rejestrowana jest przychodząca lista klipów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Rejestrowana jest przychodząca lista klipów*

Najpierw potrzebujemy sposobu, aby określić, od którego punktu, do którego punktu chcemy przyciąć wideo. Aby to wygodne dla mniej technicznego użytkownika przepływu pracy, opublikuj dwie właściwości do katalogu głównego wykresu. Aby to zrobić, kliknij prawym przyciskiem myszy powierzchnię projektanta i wybierz "Dodaj właściwość":

* Pierwsza właściwość: "ClippingTimeStart" typu: "TIMECODE"
* Druga właściwość: "ClippingTimeEnd" typu: "TIMECODE"

![Okno dialogowe Dodawanie właściwości do czasu rozpoczęcia przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Okno dialogowe Dodawanie właściwości do czasu rozpoczęcia przycinania*

![Opublikowane rekwizyty czasu przycinania w katalogu głównym przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Opublikowane rekwizyty czasu przycinania w katalogu głównym przepływu pracy*

Skonfiguruj obie właściwości do odpowiedniej wartości:

![Konfigurowanie właściwości początkowych i końcowych przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurowanie właściwości początkowych i końcowych przycinania*

Teraz, z poziomu naszego skryptu, możemy uzyskać dostęp do obu właściwości, takich jak:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno dziennika z rozpoczynaniem i końcem przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno dziennika z rozpoczynaniem i końcem przycinania*

Przesiemajmy ciągi kodów czasowych w bardziej wygodny w użyciu formularz, używając prostego wyrażenia regularnego:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Okno dziennika z wyjściem analizowanego kodu czasowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno dziennika z wyjściem analizowanego kodu czasowego*

Mając te informacje pod ręką, możemy teraz zmodyfikować xml listy klipów, aby odzwierciedlić czas rozpoczęcia i zakończenia żądanego przycinania filmu z dokładnością do klatki.

![Kod skryptu w celu dodania elementów przycięcia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kod skryptu w celu dodania elementów przycięcia*

Zostało to zrobione za pomocą normalnych operacji manipulowania ciągami. Wynikowa zmodyfikowana lista klipów xml jest zapisywana z powrotem do właściwości clipListXML we katalogu głównym przepływu pracy za pomocą metody "setProperty". Okno dziennika po innym przebiegu testowym pokaże nam następujące informacje:

![Rejestrowanie listy klipów wynikowych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Rejestrowanie listy klipów wynikowych*

Wykonaj test, aby zobaczyć, jak strumienie wideo i audio zostały przycięte. Jak zrobisz więcej niż jeden test-run z różnymi wartościami dla punktów przycinania, można zauważyć, że te nie będą brane pod uwagę jednak! Powodem tego jest to, że projektant, w przeciwieństwie do środowiska wykonawczego platformy Azure, NIE zastępuje xml listy klipów co run. Oznacza to, że tylko po raz pierwszy ustawisz punkty in i out, spowoduje, że xml`clipListXML.indexOf("<trim>") == -1`przekształci, wszystkie inne czasy, nasza klauzula strażnika (jeśli( )) uniemożliwi przepływowi pracy dodanie kolejnego elementu przycinania, gdy jest już jeden obecny.

Aby nasz przepływ pracy był wygodny do testowania lokalnie, najlepiej dodać kod do przechowywania domu, który sprawdza, czy element przycinania był już obecny. Jeśli tak, możemy go usunąć przed kontynuowaniem, modyfikując xml z nowymi wartościami. Zamiast używać zwykłych manipulacji ciągów, jest to prawdopodobnie bezpieczniejsze, aby to zrobić za pomocą rzeczywistego xml model obiektu analizowania.

Zanim jednak będziemy mogli dodać taki kod, najpierw musimy dodać kilka instrukcji importu na początku naszego skryptu:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Następnie możemy dodać wymagany kod czyszczenia:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Ten kod wykracza tuż nad punktem, w którym dodajemy elementy przycinania do xml listy klipów.

W tym momencie możemy uruchamiać i modyfikować nasz przepływ pracy, jak chcemy, jednocześnie mając zmiany stosowane w każdym czasie.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Dodawanie właściwości ClippingEnabled convenience
Ponieważ nie zawsze chcesz, aby przycinanie się stało, zakończmy nasz przepływ pracy, dodając wygodną flagę logiczną, która wskazuje, czy chcemy włączyć przycinanie / przycinanie.

Tak jak poprzednio, opublikuj nową właściwość do katalogu głównego naszego przepływu pracy o nazwie "ClippingEnabled" typu "BOOLEAN".

![Opublikowano właściwość umożliwiającą przycinanie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Opublikowano właściwość umożliwiającą przycinanie*

Dzięki poniższej prostej klauzuli osłonowej możemy sprawdzić, czy wymagane jest przycinanie i zdecydować, czy nasza lista klipów jako taka musi zostać zmodyfikowana, czy nie.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a name="complete-code"></a><a id="code"></a>Kompletny kod

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Zobacz także
[Wprowadzenie do kodowania premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak korzystać z kodowania premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kodowanie zawartości na żądanie za pomocą usługi Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formaty i kodeki usługi Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)

[Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Narzędzie Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
