---
title: Samouczki Avanced Media Encoder Premium w przepływie pracy
description: Ten dokument zawiera wskazówki, które przedstawiono sposób wykonywania zaawansowanych zadań z przepływem pracy Premium koder nośników, a także sposób tworzenia złożonych przepływów pracy za pomocą projektanta przepływów pracy.
services: media-services
documentationcenter: ''
author: xstof
manager: cfowler
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 3cba7a6a8bf6fbe199664b738b68ceca3a7746f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789033"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Zaawansowane samouczki Media Encoder Premium w przepływie pracy
## <a name="overview"></a>Przegląd
Ten dokument zawiera wskazówki, które pokazują, jak dostosowywanie przepływów pracy z **projektanta przepływów pracy**. Pliki rzeczywiste przepływu pracy można znaleźć [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TOC
Omówiono następujące tematy:

* [Kodowanie MXF do pojedynczej szybkości transmisji bitów MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Uruchamianie nowego przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Przy użyciu danych wejściowych plików nośnika](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Zapoznanie się strumieni nośnika](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Dodawanie koder wideo dla. Generowanie pliku MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kodowanie strumieniem audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Strumienie multipleksowania Audio i wideo do kontenera MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zapisywanie pliku MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Tworzenie zasobu usługi multimediów z pliku wyjściowego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testowanie Zakończono przepływu pracy lokalnie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodowanie MXF do multibitrate MP4s - dynamicznego tworzenia pakietów włączone](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Dodanie jednego lub więcej dodatkowych danych wyjściowych MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurowanie nazwy plików wyjściowych](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Dodawanie oddzielne ścieżkę Audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Dodawanie pliku SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodowanie MXF do multibitrate MP4 — rozszerzone planu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Omówienie przepływu pracy w celu zwiększenia](#workflow-overview-to-enhance)
  * [Konwencje nazewnictwa plików](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Właściwości składnika publikacji na głównego przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Wygenerowano plik wyjściowy, który nazwy zależne od wartości właściwości opublikowanych](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Dodawanie miniatur do multibitrate MP4 danych wyjściowych](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Omówienie przepływu pracy, aby dodać miniatur](#workflow-overview-to-add-thumbnails-to)
  * [Dodawanie kodowania JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Zajmujących się konwersji przestrzeń kolorów](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Pisanie miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Wykrywanie błędów w przepływie pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Zakończono przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Na podstawie czasu przycinanie multibitrate MP4 danych wyjściowych](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Omówienie przepływu pracy, aby rozpocząć dodawanie przycinanie do](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Przy użyciu przycinarka strumienia](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Zakończono przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Wprowadzenie do składnika przy użyciu skryptu](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Obsługa skryptów w ramach przepływu pracy: Witaj świecie](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Przycinanie multibitrate MP4 dane wyjściowe na podstawie ramki](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Przegląd, aby rozpocząć dodawanie przycinanie do planu](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Przy użyciu listy klip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modyfikowanie listy obiektów ze składnika inicjowanych przez skrypty](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Dodawanie właściwości wygody ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Kodowanie MXF do pojedynczej szybkości transmisji bitów MP4
W tej sekcji przedstawiono sposób tworzenia pojedynczej szybkości transmisji bitów. Plik MP4 o AAC-HE zakodowane audio z. Plik wejściowy MXF.

### <a id="MXF_to_MP4_start_new"></a>Uruchamianie nowego przepływu pracy
Otwórz projektanta przepływów pracy i wybierz Plik > Nowy obszar roboczy > Transkodowanie planu

Nowy przepływ pracy zawiera trzy elementy:

* Podstawowym pliku źródłowym
* Obcina listę XML
* Zawartości pliku wyjściowej  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nowy przepływ pracy kodowania*

### <a id="MXF_to_MP4_with_file_input"></a>Przy użyciu danych wejściowych plików nośnika
Aby zaakceptować pliku wejściowego nośnika, co rozpoczyna się od dodawania składnika nośnika pliku wejściowego. Aby dodać składnik do przepływu pracy, poszukaj jej w polu wyszukiwania w repozytorium i przeciągnij żądaną pozycję okienku projektanta. Powtórz akcji dla nośnika pliku wejściowego i Połącz składnik podstawowy plik źródłowy Filename wprowadzania numeru pin z nośnika pliku wejściowego.

![Pliku multimedialnego połączonych danych wejściowych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Pliku multimedialnego połączonych danych wejściowych*

Początkowo Zidentyfikuj odpowiednie przykładowego pliku do użycia podczas projektowania niestandardowego przepływu pracy. Aby to zrobić, kliknij przycisk tła okienka projektanta i poszukaj właściwości w okienku po prawej stronie właściwości w podstawowym pliku źródłowym. Kliknij ikonę folderu, a następnie wybierz żądany plik do testowania przepływu pracy. Składnik wejście pliku multimediów bada pliku i wypełnia jego końcówek wyjściowych, aby odzwierciedlić szczegóły przykładowy plik, który go inspekcji.

![Dane wejściowe pliku multimedialnego wypełnione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Dane wejściowe pliku multimedialnego wypełnione*

Teraz, gdy dane wejściowe zostanie wypełnione, następnym krokiem jest ustanowienie ustawienia kodowania danych wyjściowych. Podobnie jak podstawowy plik źródłowy został skonfigurowany, teraz skonfigurować właściwość zmiennej folderu wyjściowego, tuż pod.

![Skonfigurowanych właściwości dane wejściowe i wyjściowe](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Skonfigurowanych właściwości dane wejściowe i wyjściowe*

### <a id="MXF_to_MP4_streams"></a>Zapoznanie się strumieni nośnika
Często jest potrzebne wiedzieć, jak strumienia wygląda przepływ przez przepływ pracy. Aby sprawdzić strumienia w dowolnym momencie w przepływie pracy, kliknij danych wyjściowych lub wejściowych numeru pin na wszystkich składników. W takim przypadku spróbuj kliknięcie końcówka wyjściowa nieskompresowanym wideo wejściowego pliku nośnika. Zostanie otwarte okno dialogowe umożliwiający inspekcję ruchu wychodzącego wideo.

![Zapoznanie się końcówka wyjściowa nieskompresowanym wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Zapoznanie się końcówka wyjściowa nieskompresowanym wideo*

W takim przypadku pokazuje czy wideo zawiera dane wejściowe 1920 x 1080 pikseli na 24 klatek na sekundę w 4: próbkowania 2:2 film prawie 2 minuty.

### <a id="MXF_to_MP4_file_generation"></a>Dodawanie koder wideo dla. Generowanie pliku MP4
Teraz nieskompresowanym wideo i wielu nieskompresowanego dźwięku dane wyjściowe dla dostępnych numerów PIN w systemie wejściowy plik nośnika. W celu kodowania wideo dla ruchu przychodzącego, kodowania składnika musi zostać dodany do przepływu pracy — w takim przypadku generowania. Pliki mp4.

Do kodowania strumienia wideo H.264, należy dodać składnik AVC koder wideo na powierzchnię projektanta. Ten składnik przyjmuje Dekompresuj strumienia wideo o jako dane wejściowe, a następnie dostarcza AVC skompresowanego strumienia wideo na jego końcówka wyjściowa.

![Odłączony kodera AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Odłączony kodera AVC*

Jego właściwości określają, jak kodowanie dokładnie wykonywana. Załóżmy są informacje o niektórych ustawień większe znaczenie:

* Dane wyjściowe szerokości i wysokości danych wyjściowych: Określa rozdzielczość zakodowanego wideo. W takim przypadku 640 x 360 jest dobrym ustawienie.
* Szybkość klatek: Jeśli równa passthrough właśnie przyjmie szybkość klatek źródła, jest możliwe zastąpienie to jednak. Takie konwersji szybkość klatek jest nie ruchu — równoważone.
* Profil i poziom: Określa profil AVC i poziom. Można łatwo uzyskać więcej informacji na temat różnych poziomów i profile, kliknij ikonę znaku zapytania dla składnika koder wideo AVC i strona pomocy wyświetli szczegółowe informacje o każdym z poziomów. Na przykład za pomocą profilu Main na poziomie 3.2 (ustawienie domyślne).
* Oceń tryb kontroli i szybkości transmisji bitów (KB/s): w tym scenariuszu wybrać stałej szybkości transmisji bitów (CBR) dane wyjściowe w 1200 kb/s
* Format wideo: zawiera informacje o VUI (wideo użyteczność informacje) pobiera zapisywane do strumienia H.264 (informacje po stronie, które mogą być używane przez dekodera można rozszerzyć wyświetlanie, ale nie są niezbędne do prawidłowo zdekodować):
* NTSC (typowa dla Stanów Zjednoczonych lub Japonii przy użyciu 30 kl. / s)
* PAL (typowa dla Europy, przy użyciu 25 kl. / s)
* Tryb rozmiaru GOP: Ustaw o ustalonym rozmiarze GOP dla naszych celów z klucza interwałem równym 2 sekundy z GOPs zamknięte. Ustawienie 2 sekundy gwarantuje, że zapewnia zgodność z dynamicznego tworzenia pakietów usługi Azure Media Services.

Ze źródłem kodera AVC, końcówka wyjściowa nieskompresowanym wideo z nośnika pliku wejściowego składnika połączyć się z nieskompresowanym wideo numeru pin z AVC kodera.

![Połączone kodera AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Połączone kodera AVC Main*

### <a id="MXF_to_MP4_audio"></a>Kodowanie strumieniem audio
W tym momencie oryginalnego nieskompresowanych strumieniem audio nadal wymaga do skompresowania. Kompresja strumieniem audio należy dodać składnik kodera AAC (Dolby) do przepływu pracy.

![Odłączony kodera AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Odłączony AAC kodera*

Teraz występuje niezgodność: istnieje tylko jeden nieskompresowanych audio wprowadzania numeru pin z kodera AAC podczas więcej niż prawdopodobnie wejściowy plik nośnika będzie dwóch różnych nieskompresowanych strumieni audio dostępne: jeden dla lewego kanału audio i jeden dla prawej. (Jeśli jest zajmujących dźwięk przestrzenny, który jest sześć kanałów). Dlatego nie jest możliwe jest bezpośrednie połączenie audio ze źródła danych wejściowych plików nośnika do kodera audio AAC. Składnik AAC oczekuje tak zwane "przeplotem" strumieniem audio: pojedynczy strumień, który ma po lewej i prawej kanały przeplatana ze sobą. Raz wiemy z naszych pliku nośnika źródłowego ścieżki audio czy w pozycji w źródle firma Microsoft może wygenerować takie przeplotem strumieniem audio z pozycji prelegenta poprawnie przypisany do lewego i prawego.

Najpierw jeden chce Generowanie przeplotem strumienia z kanałów audio wymagane źródła. Składnik Interleaver strumień Audio obsługuje to firmie Microsoft. Dodaj go do przepływu pracy i połącz wyjścia audio wejściowego pliku multimediów do niego.

![Połączone Interleaver strumieniem Audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Połączone Interleaver strumieniem Audio*

Teraz, gdy mamy przeplotem strumieniem audio, firma Microsoft nadal nie Określ, gdzie można przypisać pozycji prelegenta lewego lub prawego do. Aby można było korzystać z tej opcji, możemy wykorzystać Assigner stanowisko osoby mówiącej.

![Dodawanie Assigner stanowisko osoby mówiącej](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Dodawanie Assigner stanowisko osoby mówiącej*

Skonfiguruj prelegenta Assigner pozycji do użycia z stereo strumień wejściowy przez filtr kodera ustawień wstępnych "Custom" oraz ustawienia kanału o nazwie "2.0 (L, R)". (To przypisuje stanowisko osoby mówiącej po lewej stronie kanał 1 i pozycji prawym prelegenta kanału 2.)

Połącz dane wyjściowe Assigner stanowisko osoby mówiącej w danych wejściowych kodera AAC. Następnie należy wskazać kodera AAC do pracy z "2.0 (L, R)" zdefiniowane kanału, który będzie wówczas traktował radzenia sobie z stereo audio jako dane wejściowe.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Strumienie multipleksowania Audio i wideo do kontenera MP4
Podany naszych AVC zakodowanego strumienia wideo i naszych AAC zakodowane strumieniem audio, firma Microsoft może zarówno do przechwytywania. Kontener MP4. Łączenie różnych strumieni w jedno proces jest nazywany "Multipleksowanie" (lub "muxing"). W takim przypadku możemy Cię z przeplotem audio i wideo strumieni w jeden spójny. Pakiet MP4. Składnik, który koordynuje ten formularz. Kontener MP4 jest nazywany multiplekser ISO MPEG-4. Dodaj je do powierzchni projektanta i połącz zarówno koder wideo AVC i koder AAC wejścia.

![Połączone MPEG4 multiplekser](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Połączone MPEG4 multiplekser*

### <a id="MXF_to_MP4_writing_mp4"></a>Zapisywanie pliku MP4
Podczas zapisywania pliku wyjściowego, składnik pliku wyjściowego jest używany. Można nawiązać połączenia to dane wyjściowe multiplekser ISO MPEG-4 tak, aby jego dane wyjściowe pobiera zapisywane na dysku. Aby to zrobić, należy nawiązać końcówka wyjściowa kontenera (MPEG-4) zapisu numeru pin pliku danych wyjściowych.

![Połączone dane wyjściowe pliku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Połączone dane wyjściowe pliku*

Nazwa pliku, który jest używany jest określany przez właściwość pliku. Tej właściwości mogą być zapisane na stałe na daną wartość, najbardziej prawdopodobne chce ustawić ją za pomocą wyrażenia.

Aby automatycznie określić dane wyjściowe przepływu pracy pliku właściwość name z wyrażenia, kliknij przycisk obok nazwy pliku (obok ikony folderu). Z menu rozwijanego wybierz "Wyrażenie". Spowoduje to wyświetlenie edytora wyrażeń. Najpierw usuń zawartość edytora.

![Pusty edytora wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Pusty edytora wyrażeń*

Edytor wyrażeń pozwala na wprowadzenie wartości literału, łączyć z jedną lub więcej zmiennych. Zmienne zaczynać się znak dolara ($). Jak naciśniesz klawisz $ Edytor pokazuje, w polu listy rozwijanej dzięki szerokiemu wyborowi dostępnych zmiennych. W tym przypadku użyjemy kombinację zmiennej katalog danych wyjściowych oraz zmienna nazwy pliku wejściowego podstawowego:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Wypełnione limit edytora wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Wypełnione limit edytora wyrażeń*

> [!NOTE]
> Aby wyświetlić plik wyjściowy kodowania zadania na platformie Azure, należy podać wartość w edytorze wyrażenia.
>
>

Po potwierdzeniu wyrażenie za pomocą ok w oknie właściwości wyświetla podgląd, co w tym momencie wartość rozwiązuje właściwości pliku.

![Wynikiem rozpoznania wyrażenia pliku jest katalog wyjściowy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Wynikiem rozpoznania wyrażenia pliku jest katalog wyjściowy*

### <a id="MXF_to_MP4_asset_from_output"></a>Tworzenie zasobu usługi multimediów z pliku wyjściowego
Gdy firma Microsoft ma zapisywanie pliku wyjściowego MP4, nadal należy wskazać, że ten plik należy do elementu zawartości wyjściowej, które usługi media services generuje wyniku wykonania tego przepływu pracy. W tym celu jest używany węzła zawartości pliku wyjściowej na kanwie przepływu pracy. Wszystkie pliki przychodzące w tym węźle należy część wynikowy zasobów usługi Azure Media Services.

Połącz składnik dane wyjściowe pliku do elementu zawartości pliku wyjściowej na zakończenie przepływu pracy.

![Zakończono przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Zakończono przepływu pracy*

### <a id="MXF_to_MP4_test"></a>Testowanie Zakończono przepływu pracy lokalnie
Aby przetestować przepływ pracy lokalnie, kliknij przycisk play na pasku narzędzi u góry. Po zakończeniu wykonywania przepływu pracy Sprawdź dane wyjściowe generowane w folderze wyjściowym skonfigurowany. Zobaczysz Zakończono pliku wyjściowego MP4 zakodowanego z MXF pliku źródła danych wejściowych.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kodowanie MXF do MP4 - multibitrate włączoną funkcję dynamicznego tworzenia pakietów
W tym przewodniku tworzy zestaw wielu plików MP4 z algorytmem AAC audio z jednej. Plik wejściowy MXF.

Po wyjściowego zasobów wielokrotnej szybkości transmisji bitów jest pożądany do użycia w połączeniu z funkcji dynamicznego tworzenia pakietów oferowanych przez usługi Azure Media Services, wielu plików MP4 wyrównane GOP każdego różne szybkości transmisji bitów i rozdzielczość wymagane ma być generowany przez. Aby to zrobić, [kodowanie MXF do pojedynczej szybkości transmisji bitów MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) wskazówki zapewnia dobry punkt wyjścia.

![Uruchamianie przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Uruchamianie przepływu pracy*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Dodanie jednego lub więcej dodatkowych danych wyjściowych MP4
Każdy plik MP4 w naszym wynikowy zasobów usługi Azure Media Services obsługuje różne szybkości transmisji bitów i rozdzielczość. Dodajmy pliki wyjściowe MP4 co najmniej jeden przepływ pracy.

Upewnij się, że mamy naszych wideo koderów utworzone za pomocą tych samych ustawień, jest najodpowiedniejszym zduplikowane już istniejącą koder wideo AVC i skonfigurować kombinację inną rozdzielczość i szybkość transmisji bitów (Dodajmy jedną 960 x 540 na 25 klatek na sekundę 2,5 MB/s ). Duplikowanie istniejącego kodera, kopiowania wklej go na powierzchnię projektanta.

Połączenie z naszego nowego składnika AVC końcówka wyjściowa wideo nieskompresowanych danych wejściowych pliku nośnika.

![Drugi kodera AVC połączone](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Drugi kodera AVC połączone*

Teraz można dostosować konfigurację dla naszego nowego kodera AVC do wyjściowego 960 x 540 2,5 MB/s. (Użyj właściwości "dane wyjściowe szerokości", "Dane wyjściowe height" i "Szybkości transmisji bitów (KB/s)" to).

Podane chcemy użyć wynikowy zasobów wraz z usługi Azure Media Services dynamicznego tworzenia pakietów, punktu końcowego przesyłania strumieniowego musi być może generować z tych plików MP4 fragmenty HLS/podzielonej zawartości MP4/DASH, dokładnie wyrównane ze sobą w taki sposób, który Klienci, którzy są przełączanie między różne szybkości transmisji bitów Praca pojedynczego smooth ciągłego wideo i audio środowisku. Aby to zrobić, musimy upewnij, że we właściwościach zarówno koderów AVC GOP ("grupa obrazów") rozmiar oba pliki MP4 jest ustawiona na 2 sekundy, które można to zrobić:

* Ustawianie trybu rozmiar GOP GOP stały rozmiar i
* Interwał ramki klucza do dwóch sekund.
* również ustawić formant IDR GOP GOP zamknięty, aby upewnić się, wszystkie GOPs są stałego na ich własnych bez zależności

Aby ułatwić zrozumienie tego przepływu pracy, Zmień pierwszy koder AVC do "koder wideo AVC 640 x 360 1200 kb/s", a drugi koder AVC "koder wideo AVC 960 x 540 2500 KB/s."

Teraz należy dodać drugi multiplekser ISO MPEG-4, a drugi dane wyjściowe pliku. Multiplekser nawiązać nowe koder AVC i upewnij się, że dane wyjściowe są kierowane do pliku wyjściowego. Następnie łączyć AAC kodera audio dane wyjściowe do nowego multiplekser w danych wejściowych. Plik wyjściowy z kolei może następnie być podłączony do węzła zawartości pliku wyjściowej, aby dodać go do zasobów usług nośnika, który zostanie utworzony.

![Drugi Muxer i dane wyjściowe pliku połączone](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Drugi Muxer i dane wyjściowe pliku połączone*

Dla zgodności z dynamicznego tworzenia pakietów usługi Azure Media Services skonfiguruj multiplekser trybu fragmentu, aby liczba GOP lub czas trwania, a następnie ustaw GOPs na segment na 1. (Musi to być wartość domyślna).

![Tryby fragmentu Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Tryby fragmentu Muxer*

Uwaga: możesz Powtórz ten proces dla szybkości transmisji bitów i rozdzielczość kombinacje, które ma zostać dodana do danych wyjściowych zasobów.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurowanie nazwy plików wyjściowych
Istnieje więcej niż jeden pojedynczy plik dodany do elementu zawartości wyjściowej. Zapewnia to potrzebę upewnij się, że nazwy plików dla poszczególnych plików wyjściowych różnią się od siebie i może nawet stosowana Konwencja nazewnictwa plików, dlatego okazuje się od nazwy pliku co w przypadku pracy nad.

Nazywanie pliku danych wyjściowych można sterować za pomocą wyrażenia w projektancie. Otwórz w okienku właściwości dla jednego ze składników danych wyjściowych w pliku i Otwórz Edytor wyrażenie dla właściwości pliku. Nasze pierwszy plik wyjściowy został skonfigurowany za pomocą następującego wyrażenia (zobacz samouczek dotyczący z [MXF do pojedynczej szybkości transmisji bitów danych wyjściowych MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Oznacza to, że nasze filename jest określany przez dwie zmienne: katalog wyjściowy do zapisu w i Nazwa podstawowego pliku źródłowego. Pierwsza jest widoczne jako właściwość katalogu głównego przepływu pracy i jego zależy od plików przychodzących. Katalog wyjściowy, które jest używane do testowania lokalnego; Ta właściwość zostanie ona zastąpiona aparatu przepływu pracy, gdy przepływ pracy jest wykonywane przez procesor multimediów oparte na chmurze w usłudze Azure Media Services.
Aby dać naszych pliki wyjściowe nazewnictwa spójności danych wyjściowych, Zmień pierwszy plik nazewnictwa wyrażenie:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

i drugą do:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Wykonanie pośrednich testu do upewnij się, że poprawnie są generowane zarówno pliki wyjściowe MP4.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Dodawanie oddzielne ścieżkę Audio
Jako zajmiemy się później gdy firma Microsoft generuje plik .ism z naszych pliki wyjściowe MP4, zostanie również wymagamy plik MP4 tylko dźwięk jako ścieżkę audio do naszej adaptacyjnego przesyłania strumieniowego. Do utworzenia tego pliku, Dodaj dodatkowe muxer do przepływu pracy (ISO-MPEG-4 multiplekser) i uzyskuj końcówka wyjściowa koder AAC jego wprowadzania numeru pin dla ścieżki 1.

![Audio Muxer dodane](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer dodane*

Utwórz innych składników danych wyjściowych w pliku wyjściowego strumienia wychodzącego z muxer i skonfigurować plik jako wyrażenia nazw:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer tworzenia pliku wyjściowego](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer tworzenia pliku wyjściowego*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Dodawanie. Plik SMIL ISM
Do dynamicznego tworzenia pakietów do pracy w połączeniu z MP4 plików (i tylko dźwięk MP4) w naszym zasobów usługi Media Services, potrzebujemy również pliku manifestu (skrót pliku "SMIL": synchronizowane języka integrację multimediów). Ten plik wskazuje usługi Azure Media Services, jakie pliki MP4 są dostępne do dynamicznego tworzenia pakietów, które z tych, które należy wziąć pod uwagę przesyłanie strumieniowe audio. Typowy pliku manifestu zestawu MP4 w jednym strumieniem audio wygląda następująco:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
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

Zawiera plik .ism w instrukcji switch, odwołanie do każdego z poszczególnych plików wideo MP4 i oprócz tych odwołań pliku audio również jednej (lub więcej) do MP4, zawiera tylko audio.

Generowanie pliku manifestu dla naszego zestawu w MP4 może odbywać się za pośrednictwem składnik o nazwie "Edytor AMS Manifest." Aby go użyć, przeciągnij go na powierzchnię i nawiązać z trzech składników danych wyjściowych w pliku danych wejściowych modułu zapisującego AMS Manifest "Zapisu ukończone" końcówek wyjściowych. Następnie upewnij się połączyć dane wyjściowe zapisywania manifestu AMS do pliku wyjściowego/zasobów.

Zgodnie z naszym innym składnikom pliku wyjściowego, skonfiguruj nazwę .ism pliku wyjściowego z wyrażeniem:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Wygląd naszego Zakończono przepływu pracy poniżej:

![Zakończono MXF multibitrate MP4 w przepływie pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Zakończono MXF multibitrate MP4 w przepływie pracy*

## <a id="MXF_to__multibitrate_MP4"></a>Kodowanie MXF do multibitrate MP4 — rozszerzone planu
W [przepływu pracy w poprzednim przewodniku](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) możemy przedstawiono sposób pojedynczego zasobu wejściowego MXF mogą być konwertowane na zawartości wyjściowej z plików MP4 wielokrotnej szybkości transmisji bitów, plik MP4 tylko audio i plik manifestu do użycia w połączeniu z usługi Azure Media Dynamiczne tworzenie pakietów usług.

W tym przewodniku przedstawiono sposób niektóre aspekty można usprawniać i wprowadzone wygodniejsze.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Omówienie przepływu pracy w celu zwiększenia
![Przepływ pracy Multibitrate MP4 w celu zwiększenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Przepływ pracy Multibitrate MP4 w celu zwiększenia*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Konwencje nazewnictwa plików
W poprzednim przepływu pracy możemy określony proste wyrażenie jako podstawę do generowania nazw plików wyjściowych. Mimo że mamy powielania niektórych: wszystkie dane wyjściowe poszczególnych składników plików określone takie wyrażenie.

Na przykład składnik dane wyjściowe pliku naszych pierwszy plik wideo jest skonfigurowany za pomocą tego wyrażenia:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Podczas drugiego wyjście wideo zostały wyrażeń, takich jak:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Nie byłoby czyszczący mniej błąd podatnych na błędy i wygodniejsze jeśli firma Microsoft można. Usuń niektóre tego duplikowania wprowadzisz rzeczy przeprowadzanie łatwiejszych do skonfigurowania zamiast tego? Możemy szczęście: projektanta wyrażenie możliwości w połączeniu z możliwość tworzenia niestandardowych właściwości w naszym głównym przepływu pracy zapewni dodatkową warstwę wygody.

Załóżmy, że firma Microsoft będzie dysków konfiguracji filename od szybkości transmisji bitów poszczególnych plików MP4. Te szybkości transmisji bitów, które firma Microsoft będzie Staraj się skonfigurować w jednym miejscu centralnej (w katalogu głównym naszych wykresu), z którym będzie były dostępne do konfigurowania i generowania nazwy pliku dysku. Aby to zrobić, firma Microsoft Rozpocznij od opublikowania właściwości szybkości transmisji bitów z koderów zarówno AVC do katalogu głównego naszych przepływu pracy, tak że staje się dostępny z obu głównego również od koderów AVC. (Nawet jeśli wyświetlane w dwóch różnych miejsc, jest tylko jedna wartość podstawowej).

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Właściwości składnika publikacji na głównego przepływu pracy
Otwórz pierwszy koder AVC, przejdź do właściwości szybkości transmisji bitów (KB/s) i z listy rozwijanej wybierz publikowania.

![Publikowanie właściwości szybkości transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikowanie właściwości szybkości transmisji bitów*

Okno dialogowe publikowania do publikowania w katalogu głównym naszych Wykres przepływu pracy należy skonfigurować z opublikowana nazwa "video1bitrate" i do odczytu wyświetlaną nazwę "Wideo 1 szybkości transmisji bitów". Konfigurowanie niestandardowej nazwy grupy o nazwie "Przesyłania strumieniowego szybkości transmisji bitów" i kliknij przycisk Publikuj.

![Publikowanie właściwości szybkości transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Okno dialogowe publikowania właściwości szybkości transmisji bitów*

Powtórz takie same właściwości szybkości transmisji bitów drugi kodera AVC i nadaj jej nazwę na "video2bitrate" o nazwie wyświetlanej "Wideo 2 szybkości transmisji bitów", w tej samej grupy niestandardowe "Przesyłania strumieniowego szybkości transmisji bitów".

Jeśli mamy teraz sprawdzić właściwości katalogu głównego przepływu pracy, widzimy naszych niestandardową grupę z dwóch właściwości opublikowanych widoczne. Oba zdarzenie odzwierciedla wartość ich odpowiednich AVC kodera szybkości transmisji bitów.

![Właściwości opublikowanych szybkości transmisji bitów w katalogu głównym przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Zawsze, gdy chcemy, aby uzyskać dostęp do tych właściwości, z kodu lub wyrażenie, możemy to zrobić następująco:

* z wbudowanego kodu ze składnika bezpośrednio pod katalogiem głównym: node.getPropertyAsString('.. / video1bitrate ", null)
* w wyrażeniu: ${ROOT_video1bitrate}

Umożliwia zakończenie grupy "Przesyłania strumieniowego szybkości transmisji bitów" publikując naszych ścieżki audio szybkości transmisji bitów w nim również. W ramach właściwości kodera AAC Wyszukaj ustawienie szybkości transmisji bitów, a następnie wybierz z listy rozwijanej obok niej publikowania. Publikowanie do katalogu głównego wykresu o nazwie "audio1bitrate" i nazwie "Audio 1 szybkości transmisji bitów" w naszej grupy niestandardowe "Przesyłania strumieniowego szybkości transmisji bitów" wyświetlanej.

![Okno dialogowe publikowania audio szybkości transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Okno dialogowe publikowania audio szybkości transmisji bitów*

![Wynikowa właściwości audio i wideo w folderze głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Wynikowa właściwości audio i wideo w folderze głównym*

Zmiana któregoś z tych trzech wartości również Rekonfiguruj i zmiany wartości na odpowiednich składników, które są powiązane z (i w przypadku, gdy publikowane z).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Wygenerowano plik wyjściowy, który nazwy zależne od wartości właściwości opublikowanych
Zamiast hardcoding naszych nazwy wygenerowanego pliku możemy można teraz zmienić naszych wyrażenia nazwy pliku na poszczególne składniki dane wyjściowe pliku polegać na właściwościach szybkości transmisji bitów, które możemy opublikowany w katalogu głównym wykresu. Począwszy od pierwszego wyjście pliku znaleźć właściwości pliku i edytować wyrażenie następująco:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Różne parametry w tym wyrażeniu można uzyskać dostępu do i wprowadzone za pomocą dolara na klawiaturze znajduje się w oknie wyrażeń. Jedną z dostępnych parametrów jest właściwością naszych video1bitrate, która możemy wcześniej publikowane.

![Uzyskiwanie dostępu do parametrów w wyrażeniu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Uzyskiwanie dostępu do parametrów w wyrażeniu*

Wykonaj te same dla pliku danych wyjściowych dla naszych drugi wideo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

i dla danych wyjściowych w pliku tylko do dźwięk:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Jeżeli zmienimy teraz szybkości transmisji bitów dla plików wideo lub audio, konfiguracja zostanie zmieniona odpowiednich koder i Konwencji nazwę pliku na podstawie szybkości transmisji bitów będą honorowane wszystkie automatyczne.

## <a id="thumbnails_to__multibitrate_MP4"></a>Dodawanie miniatur do multibitrate MP4 danych wyjściowych
Począwszy od przepływu pracy, który generuje [multibitrate MP4 wyjściowymi MXF wejściowych](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), możemy teraz będzie wyszukiwania do dodawania miniatur do danych wyjściowych.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Omówienie przepływu pracy, aby dodać miniatur
![Multibitrate MP4 przepływu pracy, aby uruchomić z](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4 przepływu pracy, aby uruchomić z*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Dodawanie kodowania JPG
Serca naszych generowanie miniatur będzie składnika kodera JPG, możliwość JPG plików wyjściowych.

![Koder JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Nie można jednak bezpośrednio połączymy naszych strumienia wideo nieskompresowanych wejściowego pliku multimediów do kodera JPG. Zamiast tego oczekuje były przekazywane poszczególnych klatek. To, możemy za pośrednictwem składnika brama ramki wideo.

![Połączenie bramy ramki do kodera JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Połączenie bramy ramki do kodera JPG*

Brama ramki raz na tyle sekund lub ramki umożliwia wideo ramki do przekazania. Interwał i czasu przesunięcia, z którym dzieje się to można skonfigurować we właściwościach.

![Właściwości ramki bramy wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Właściwości ramki bramy wideo*

Umożliwia utworzenie miniatury co minutę przez ustawienie trybu na czas (w sekundach) i interwału do 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Zajmujących się konwersji przestrzeń kolorów
Podczas logiczne wydaje się, że teraz można podłączyć obu numerów PIN nieskompresowanym wideo bramy ramki i wejście pliku multimediów, firma Microsoft będzie wyświetlone ostrzeżenie, jeśli firma Microsoft może to zrobić.

![Koloru okna wprowadzania komunikat o błędzie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Koloru okna wprowadzania komunikat o błędzie*

Jest to spowodowane sposób, w których kolor informacji jest reprezentowane w naszych oryginalnego raw nieskompresowanych strumienia wideo, pochodzące z naszych MXF różni się od co JPG Koder oczekuje. W szczególności tak zwane "przestrzeń kolorów" "RGB" lub "Skali szarości" Oczekiwano przepływu. Oznacza to, że przychodzących strumienia wideo o wideo bramy ramki musi mieć konwersji zastosowana jako pierwsza dotyczące jego przestrzeń kolorów.

Przeciągnij przepływu pracy konwertera miejsca kolor - Intel i podłącz go do naszego bramy ramki.

![Łączenie konwertera miejsca kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Łączenie konwertera miejsca kolorów*

W oknie właściwości wybierz wpis BGR 24 z listy ustawienie wstępne.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Pisanie miniatur
Inne niż filmie MP4, składnik kodera JPG generuje więcej niż jeden plik. Aby poradzić sobie z tym, można użyć składnika zapisywania pliku JPG wyszukiwania sceny: przyjmuje przychodzące miniatur JPG i zapisuje je, przy kończyły przez inną liczbę nazw plików. (Zazwyczaj określającą liczbę sekund/jednostki w strumieniu, który miniatury narysowaniu z liczby.)

![Wprowadzenie do zapisywania pliku JPG wyszukiwania sceny](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Wprowadzenie do zapisywania pliku JPG wyszukiwania sceny*

Skonfiguruj właściwości ścieżki folderu danych wyjściowych z wyrażeniem: ${ROOT_outputWriteDirectory}

i właściwość prefiks nazwy pliku:

    ${ROOT_sourceFileBaseName}_thumb_

Prefiks określa sposób nazywania plików miniatur. Są one sufiks z numerem wskazującą położenie przycisku przewijania w strumieniu.

![Właściwości składnika zapisywania pliku JPG wyszukiwania sceny](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Właściwości składnika zapisywania pliku JPG wyszukiwania sceny*

Moduł zapisujący pliku JPG wyszukiwania sceny łączenie z węzłem zawartości pliku wyjściowej.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Wykrywanie błędów w przepływie pracy
Dane wejściowe konwertera miejsca kolor nawiązać połączenia z pierwotnych nieskompresowanych wyjście wideo. Teraz można przeprowadzać lokalnego uruchomienia przepływu pracy testu. Istnieje duże prawdopodobieństwo nagle zatrzyma, wykonywanie i oznaczać z czerwonego obramowania w składniku wystąpił błąd przepływu pracy:

![Błąd konwertera miejsca kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Błąd konwertera miejsca kolorów*

Kliknij ikonę "E" red małego w górnym prawym rogu składnika konwertera miejsca kolor, aby zobaczyć, co jest przyczyną kodowania próba nie powiodła się.

![Okno dialogowe kolorów konwertera miejsca](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Okno dialogowe kolorów konwertera miejsca*

Monitor przechodzi w stan, jak widać, że przychodzące przestrzeń kolorów na standardowe dla konwertera miejsca kolor musi być rec601 naszych żądanego konwersji YUV RGB. Najwyraźniej naszych strumienia nie oznacza jego rec601. (ZAL 601 jest standardem kodowania naprzemiennych analogowy sygnały wideo w formie cyfrowej wideo. Określa aktywnego regionu, obejmujące 720 jasności oraz 360 próbki chrominance w jednym wierszu. Kolor kodowanie system jest nazywany YCbCr 4:2:2.)

Aby rozwiązać ten problem, firma Microsoft będzie wskazuje na metadane naszych strumienia, który jest firma Microsoft zajmujących się rec601 zawartości. W tym celu użyjemy komponent Updater typu danych wideo, który będzie testujemy Between naszych raw źródła i składnik kolor miejsca konwersji. Tej aktualizacji typu danych umożliwia ręcznej aktualizacji niektórych danych wideo właściwości typu. Skonfiguruj, aby wskazać kolor miejsca Standard "Rec 601". Powoduje to Updater typu danych wideo do znakowania strumienia z przestrzeń kolorów "Rec 601", jeśli nie było żadnych przestrzeń kolorów jeszcze zdefiniowana. (Nie zastąpi on metadane, chyba że zaznaczono pole wyboru zastąpienia.)

![Aktualizowanie kolor Standard miejsca na typ danych aktualizacji](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizowanie kolor Standard miejsca na typ danych aktualizacji*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Zakończono przepływu pracy
Teraz, gdy naszych przepływ pracy został zakończony, czy innego testu Uruchom, aby wyświetlić przekazaniem.

![Zakończono przepływu pracy dla danych wyjściowych mp4 wielu z miniaturami](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Zakończono przepływu pracy dla danych wyjściowych mp4 wielu z miniaturami*

## <a id="time_based_trim"></a>Na podstawie czasu przycinanie multibitrate MP4 danych wyjściowych
Począwszy od przepływu pracy, który generuje [multibitrate MP4 wyjściowymi MXF danych wejściowych](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), teraz będzie możemy wyszukiwania do przycinanie źródła wideo oparte na sygnatury czasowe.

### <a id="time_based_trim_start"></a>Omówienie przepływu pracy, aby rozpocząć dodawanie przycinanie do
![Uruchamianie przepływu pracy, aby dodać przycinanie do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Uruchamianie przepływu pracy, aby dodać przycinanie do*

### <a id="time_based_trim_use_stream_trimmer"></a>Przy użyciu przycinarka strumienia
Składnik przycinarka strumienia umożliwia trim początek i koniec strumienia danych wejściowych na chronometrażu informacji (sekundy, minuty,...). Przycinarka nie obsługuje przycinania na podstawie ramki.

![Przycinarka do strumienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Przycinarka do strumienia*

Zamiast połączenie koderów AVC i assigner stanowisko osoby mówiącej wejście pliku multimediów bezpośrednio, będzie testujemy między tymi przycinarka strumienia. (Po jednej dla sygnału wideo i jeden dla przeplotem sygnału dźwiękowego.)

![Umieść przycinarka strumienia między nimi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Umieść przycinarka strumienia między nimi*

Skonfigurujmy przycinarka, dzięki czemu będą przetwarzane są jedynie wideo i audio między 15 sekund i 60 sekund na wideo.

Przejdź do właściwości przycinarka strumienia wideo i skonfiguruj zarówno czas rozpoczęcia (15 s) i czas zakończenia (60 s) właściwości. Aby upewnić się, że zarówno naszych przycinarka audio i wideo są zawsze konfigurowane do tej samej początkowa i końcowa, firma Microsoft publikuje te do katalogu głównego przepływu pracy.

![Publikowanie właściwości czasu rozpoczęcia z przycinarka strumienia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publikowanie właściwości czasu rozpoczęcia z przycinarka strumienia*

![Okna dialogowego właściwości publikowania dla godziny rozpoczęcia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Okna dialogowego właściwości publikowania dla godziny rozpoczęcia*

![Okna dialogowego właściwości publikowania dla godzina zakończenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Okna dialogowego właściwości publikowania dla godzina zakończenia*

Czy możemy teraz sprawdzić głównego naszych przepływu pracy, obie właściwości są starannie wyświetlane i można skonfigurować z tego miejsca.

![Opublikowanych właściwości dostępne w folderze głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Opublikowanych właściwości dostępne w folderze głównym*

Teraz Otwórz właściwości przycinanie z przycinarka audio i skonfigurować zarówno rozpoczęcia i zakończenia z wyrażeniem, które odwołuje się do opublikowanych właściwości w katalogu głównym naszych przepływu pracy.

Godzina rozpoczęcia audio Przycinanie:

    ${ROOT_TrimmingStartTime}

i jego czas zakończenia:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Zakończono przepływu pracy
![Zakończono przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Zakończono przepływu pracy*

## <a id="scripting"></a>Wprowadzenie do składnika przy użyciu skryptu
Składniki inicjowanych przez skrypty można wykonywanie skryptów dowolnego podczas fazy wykonywania naszych przepływu pracy. Istnieją cztery różne skrypty, które mogą być wykonywane każdego z określonych parametrów i miejsca w cyklu życia przepływu pracy:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentacja składnika inicjowanych przez skrypty przechodzi bardziej szczegółowo dla każdego z powyższych. W [poniższej sekcji](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **realizeScript** skryptów składnika jest używany do tworzenia xml cliplist na bieżąco, podczas uruchamiania przepływu pracy. Ten skrypt jest wywoływana podczas instalacji składnika, który odbywa się tylko raz w jego cyklu życia.

### <a id="scripting_hello_world"></a>Obsługa skryptów w ramach przepływu pracy: Witaj świecie
Przeciągnij składnik inicjowanych przez skrypty na powierzchnię projektanta i jego nazwa zostanie zmieniona (na przykład "SetClipListXML").

![Dodawanie skryptowej składnika](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie skryptowej składnika*

Inspekcji właściwości składnika inicjowanych przez skrypty cztery typy inny skrypt będzie wyświetlane, każdy można skonfigurować do różnych skryptu.

![Przy użyciu skryptu właściwości składnika](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Przy użyciu skryptu właściwości składnika*

Wyczyść processInputScript, a następnie otwórz edytor dla realizeScript. Teraz możemy są ustawione i rozpocząć wykonywanie skryptów.

Skrypty są zapisywane w Groovy, dynamicznie skompilowanej język skryptów dla platformy Java, która zachowuje zgodność z językiem Java. W rzeczywistości większość kodu języka Java jest prawidłowym kodem Groovy.

Napisz skrypt groovy proste hello world w kontekście naszych realizeScript. W edytorze, wprowadź następujące:

    node.log("hello world");

Teraz można wykonać uruchomienia testu lokalnego. Po tym przebiegu inspekcji (za pośrednictwem karcie systemu składnika inicjowanych przez skrypty) właściwości dzienników.

![Witaj świecie dziennika w danych wyjściowych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Witaj świecie dziennika w danych wyjściowych*

Obiekt węzła, który nazywamy metoda rejestrowania, odwołuje się do naszej bieżącego "węzła" lub składnik, który jest firma Microsoft skryptów w. Każdy składnik informatyczny jako takie dane rejestrowania danych wyjściowych, dostępne za pośrednictwem karty systemu. W takim przypadku możemy output literał ciągu "hello world". Ważne zrozumieć, w tym miejscu jest, że może to okazać się nieoceniony narzędzia debugowania, zapewniając wgląd w skrypcie faktycznie czynności.

Z naszym środowisku skryptów, firma Microsoft mają także dostęp do właściwości na inne składniki. Wypróbuj:

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

Nasze okna dziennika znajdują się nami:

![Dane wyjściowe dziennika do uzyskiwania dostępu do ścieżki węzła](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Dane wyjściowe dziennika do uzyskiwania dostępu do ścieżki węzła*

## <a id="frame_based_trim"></a>Przycinanie multibitrate MP4 dane wyjściowe na podstawie ramki
Począwszy od przepływu pracy, który generuje [multibitrate MP4 wyjściowymi MXF wejściowych](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), możemy teraz będzie wyszukiwania do źródła wideo na podstawie liczby ramki przycinania.

### <a id="frame_based_trim_start"></a>Przegląd, aby rozpocząć dodawanie przycinanie do planu
![Przepływ pracy, aby rozpocząć dodawanie przycinanie do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Przepływ pracy, aby rozpocząć dodawanie przycinanie do*

### <a id="frame_based_trim_clip_list"></a>Przy użyciu listy klip XML
Wszystkie poprzednie samouczki przepływu pracy My używamy składnika nośnika pliku wejściowego jako naszych wideo źródła danych wejściowych. W tym scenariuszu określonych, będzie używany składnik źródłowy listy klip zamiast tego. To nie powinna być preferowany sposób pracy; tylko klip źródło listy jest używane w przypadku rzeczywistych powód, aby to zrobić (takich jak w przypadku następujących, gdzie czynione korzystanie z możliwości przycinanie listy klip).

Aby przełączyć się z naszym dane wejściowe plik nośnika źródła listy klip, przeciągnij składnik źródłowy listy klip na powierzchnię projektu i połącz pin XML listy klip węzła XML listy klip projektanta przepływów pracy. Spowoduje to wypełnienie źródła listy klip z końcówek wyjściowych, zgodnie z naszym wejściowego pliku wideo. Teraz nawiązać połączenia nieskompresowanych wideo i Audio nieskompresowanych numerów PIN ze źródła listy klip odpowiednich koderów AVC i Interleaver strumień Audio. Teraz usunąć dane wejściowe z pliku nośnika.

![Zastąpione wejściowy plik nośnika źródła listy Clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Zastąpione wejściowy plik nośnika źródła listy Clip*

Składnik źródłowy listy klip przyjmuje jako dane wejściowe "Klip Lista XML." Po wybraniu pliku źródłowego do przetestowania lokalnie, ten klip listy xml jest wypełniana automatycznie dla Ciebie.

![Wypełnione automatycznie klip XML listy właściwości](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Wypełnione automatycznie klip XML listy właściwości*

Wyszukiwanie nieco zbliżonej do pliku xml, to, jak wygląda następująco:

![Okno dialogowe listy klip Edycja](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Okno dialogowe listy klip Edycja*

To nie odzwierciedlać możliwości xml listy obiektów. Jedną z opcji naszym jest można dodać elementu "Przycinanie" zarówno wideo i audio źródłowej, jak to:

![Dodawanie przycinania element do listy obiektów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Dodawanie przycinania element do listy obiektów*

Jeśli zmodyfikowany plik xml listy klip takie powyżej i wykonaj lokalnego uruchomienia testu, zobaczysz wideo poprawnie został przycięty od 10 do 20 sekund w wideo.

Sprzeczności, co się stanie po wykonaniu przebiegu lokalnego jednak ten sam xml cliplist nie będzie zawierało ten sam efekt, gdy jest stosowany w przepływ pracy uruchamiany w usłudze Azure Media Services. Po uruchomieniu koder Premium Azure cliplist xml jest generowana za każdym razem, gdy ponownie, oparte na otrzymaną zadania kodowania pliku wejściowego. Oznacza to, że wszelkie zmiany w pliku xml nie Niestety zostanie przesłonięte.

Aby wyeliminować xml cliplist są czyszczone po uruchomieniu zadania kodowania, firma Microsoft może odtworzyć go na bieżąco tylko po rozpoczęciu naszych przepływu pracy. Takie akcje niestandardowe mogą być pobierane przez co to jest nazywany "Skryptowej składnika". Aby uzyskać więcej informacji, zobacz [wprowadzenie do składnika inicjowanych przez skrypty](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Przeciągnij składnik inicjowanych przez skrypty na powierzchnię projektanta i zmień jego nazwę na "SetClipListXML."

![Dodawanie skryptowej składnika](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie skryptowej składnika*

Właściwości składnika inicjowanych przez skrypty można sprawdzić, cztery typy inny skrypt są wyświetlane, każdy można skonfigurować do różnych skryptu.

![Przy użyciu skryptu właściwości składnika](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Przy użyciu skryptu właściwości składnika*

### <a id="frame_based_trim_modify_clip_list"></a>Modyfikowanie listy obiektów ze składnika inicjowanych przez skrypty
Zanim firma Microsoft ponownego zapisywania xml cliplist, który jest generowany podczas uruchamiania przepływu pracy, musimy mieć dostęp do właściwości xml cliplist i zawartość. Firma Microsoft może zrobić następująco:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Przychodzące listy klip rejestrowany](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Przychodzące listy klip rejestrowany*

Najpierw musimy możliwość określenia od punktu, który aż do punktu, który chcemy przyciąć wideo. Aby uprościć to użytkownikowi technical mniej przepływu pracy, należy opublikować dwie właściwości do katalogu głównego wykresu. Aby to zrobić, kliknij prawym przyciskiem myszy powierzchnię projektanta i wybierz opcję "Dodaj właściwość":

* Pierwszą właściwością: "ClippingTimeStart" typu: "Kod CZASOWY"
* Drugie właściwości: "ClippingTimeEnd" typu: "Kod CZASOWY"

![Okno dialogowe właściwości Dodawanie czas uruchomienia wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Okno dialogowe właściwości Dodawanie czas uruchomienia wycinka*

![Opublikowane wycinka właściwości czasu w katalogu głównym przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Opublikowane wycinka właściwości czasu w katalogu głównym przepływu pracy*

Skonfiguruj obie właściwości, do odpowiedniej wartości:

![Konfigurowanie uruchomienia wycinka i kończyć właściwości](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurowanie uruchomienia wycinka i kończyć właściwości*

Teraz z naszych skryptu, możemy uzyskać dostęp do obie właściwości, takie jak to:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno Dziennik przedstawiający początek i koniec wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno Dziennik przedstawiający początek i koniec wycinka*

Umożliwia analizowanie ciągów kod czasowy, w bardziej łatwe w użyciu formularza, przy użyciu proste wyrażenie regularne:

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

![Okno Dziennik z danych wyjściowych przeanalizowany kod czasowy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno Dziennik z danych wyjściowych przeanalizowany kod czasowy*

Dzięki tym informacjom wykonywanego mamy teraz zmodyfikować xml cliplist w celu odzwierciedlenia godziny rozpoczęcia i zakończenia dla żądanego wycinka dokładne ramki filmu.

![Kod skryptu, aby dodać elementy przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kod skryptu, aby dodać elementy przycinania*

Zostało to zrobić za pomocą operacje na ciągach normalne manipulowanie. Wynikowy kod xml listy klipu nie są zapisywane we właściwości clipListXML w katalogu głównym przepływu pracy za pomocą metody "setProperty". Okno Dziennik po innego testu będzie zawierać nam następujące informacje:

![Rejestrowanie wynikowej listy clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Rejestrowanie wynikowej listy clip*

Do uruchomienia testu, aby zobaczyć, jak strumienie audio i wideo została obcięta. Jak należy to zrobić więcej niż jednego uruchomienia testu z różnymi wartościami dla punktów przycinanie, można zauważyć, że te będą nie brane pod uwagę jednak! Przyczyną tego jest czy projektanta, w przeciwieństwie do platformy Azure środowiska uruchomieniowego, nie przesłania cliplist xml każdego uruchomienia. Oznacza to, że tylko po raz pierwszy ustawiono punktów wejścia i wyjścia, spowoduje, że xml do przekształcania wszystkich innych przypadkach naszych klauzuli guard (jeśli (clipListXML.indexOf ("<trim>") == -1)) uniemożliwi Dodawanie inny element przycinania w przypadku przepływu pracy już jeden obecny.

Aby uprościć naszych przepływu pracy do testowania lokalnie, najlepiej dodamy niektórych porządkowania DOM kodu, który sprawdza, czy przycinania element został już istnieje. Jeśli tak, możemy go usuń, aby móc kontynuować, modyfikując plik xml przy użyciu nowych wartości. Zamiast przy użyciu zwykłego działań na ciągach, jest prawdopodobnie bezpieczniejsze to zrobić za pośrednictwem modelu obiektu xml rzeczywistym analizy.

Zanim można jednak dodać taki kod, trzeba będzie najpierw dodać liczbę instrukcje importu na początku naszych skryptu:

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

Po to można dodać wymagane kod czyszczenia:

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

Ten kod przechodzi nad punktem jaką dodamy przycinania elementów XML cliplist.

Na tym etapie firma Microsoft mogą uruchamiać i modyfikować naszych przepływu pracy jako ile chcemy przy jednoczesnym zachowaniu nigdy zastosowane zmiany czasu.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Dodawanie właściwości wygody ClippingEnabled
Jak nie zawsze powinny przycinanie na to, są teraz Zakończ poza naszych przepływu pracy przez dodanie wygodny Flaga wartości logicznej, która wskazuje, czy nie chcemy umożliwić przycinanie / wycinka.

Jak wcześniej Opublikuj nową właściwość do katalogu głównego naszych przepływu pracy o nazwie "ClippingEnabled" typu "BOOLEAN".

![Właściwości włączenia wycinka opublikowane](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Właściwości włączenia wycinka opublikowane*

Z poniżej klauzuli guard proste firma Microsoft Sprawdź, czy wymagana jest przycinanie i zdecyduj, czy naszej listy klip jako takie ma zostać zmodyfikowana, lub nie.

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

### <a id="code"></a>Kompletny kod

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

## <a name="also-see"></a>Zobacz też
[Wprowadzenie do kodowania w Azure Media Services w wersji Premium](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak używać kodowania w Azure Media Services w wersji Premium](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kodowanie zawartości na żądanie przy użyciu usługi Azure Media](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formaty i kodeki usługi Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)

[Przykładowe pliki przepływu pracy](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Narzędzia usługi Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
