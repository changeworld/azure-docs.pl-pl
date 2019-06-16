---
title: Samouczki zaawansowane Media Encoder Premium Workflow
description: Ten dokument zawiera wskazówki, które pokazują, jak wykonywać zaawansowane zadania w usłudze Media Encoder Premium Workflow, a także sposób tworzenia złożonych przepływów pracy za pomocą projektanta przepływów pracy.
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
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: d227e3618c138e6661cc4be7caa2b9a3ba1af3f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61241555"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Samouczki zaawansowane Media Encoder Premium Workflow
## <a name="overview"></a>Omówienie
Ten dokument zawiera wskazówki, które pokazują, jak dostosować przepływy pracy za pomocą **projektanta przepływów pracy**. Pliki faktyczny przepływ pracy można znaleźć [tutaj](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TOC
Omówiono następujące tematy:

* [Kodowanie pliku MXF do pojedynczej szybkości transmisji bitów w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Uruchamianie nowego przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Przy użyciu nośnika pliku wejściowego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspekcja strumieni multimediów](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Dodawanie koder wideo dla. Generowanie pliku w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kodowanie strumienia audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Funkcje multipleksowania strumieni Audio i wideo do kontenera w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zapisywanie pliku w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Tworzenie zasobu usługi multimediów z pliku wyjściowego](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testowanie Zakończono przepływu pracy lokalnie](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodowanie pliku MXF do każdego pliku MP4 — metodę dynamicznego tworzenia pakietów, włączone](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Dodanie jednego lub więcej dodatkowych danych wyjściowych w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurowanie nazwy wyjściowego pliku](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Dodawanie oddzielne ścieżkę Audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Dodawanie pliku SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodowanie pliku MXF na metodę MP4 — rozszerzone planu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Omówienie przepływu pracy w celu zwiększenia
  * [Konwencje nazewnictwa plików](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Właściwości publikowania składników na głównego przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Został wygenerowany plik wyjściowy, nazwy polegać na wartości właściwości opublikowane](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Dodawanie miniatur do pliku wyjściowego MP4 metodę](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Omówienie przepływu pracy, aby dodać miniatur do
  * [Dodawanie kodowania JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Obsługa konwersji przestrzeń kolorów](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Pisanie, miniatury](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Wykrywanie błędów w przepływie pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Zakończono przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Na podstawie czasu przycinanie dane wyjściowe metodę w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Omówienie przepływu pracy, aby rozpocząć dodawanie przycinania na](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Za pomocą przycinarka Stream](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Zakończono przepływu pracy](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Wprowadzenie do składników przy użyciu skryptu](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Wykonywanie skryptów w ramach przepływu pracy: Witaj świecie!](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Przycinanie opartych na klatkach dane wyjściowe metodę w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Omówienie planu, aby rozpocząć dodawanie przycinania na](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Przy użyciu listy klipu XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modyfikowanie listy klipu ze składnika inicjowanych przez skrypty](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Dodawanie właściwości wygody ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Kodowanie pliku MXF do pojedynczej szybkości transmisji bitów w formacie MP4
Ta sekcja przedstawia sposób tworzenia pojedynczej szybkości transmisji bitów. Plik MP4 z AAC-HE zakodowany audio z. MXF pliku wejściowego.

### <a id="MXF_to_MP4_start_new"></a>Uruchamianie nowego przepływu pracy
Otwórz projektanta przepływów pracy i wybierz Plik > Nowy obszar roboczy > Transkodowanie planu

Nowy przepływ pracy zawiera trzy elementy:

* Podstawowym pliku źródłowym
* Obcina listę XML
* Plik/elementu zawartości wyjściowej  

![Nowy przepływ pracy kodowania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nowy przepływ pracy kodowania*

### <a id="MXF_to_MP4_with_file_input"></a>Przy użyciu nośnika pliku wejściowego
Aby zaakceptować pliku wejściowego nośnika, jeden rozpoczyna się od Dodawanie składnika Media pliku wejściowego. Aby dodać składnik do przepływu pracy, poszukaj go w polu wyszukiwania w repozytorium i przeciągnij żądnego wpisu na okienka projektanta. Powtórz akcję dla wejściowego pliku multimediów i składników w podstawowym pliku źródłowym Połącz się z wprowadzania numeru pin Filename z wejściowego pliku nośnika.

![Dane wejściowe plików multimedialnych połączone](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Dane wejściowe plików multimedialnych połączone*

Początkowo zidentyfikować odpowiednie przykładowy plik do użycia podczas projektowania niestandardowego przepływu pracy. Aby to zrobić, kliknij tło projektanta okienku i wyszukaj właściwość podstawowy plik źródłowy w okienku po prawej stronie właściwości. Kliknij ikonę folderu, a następnie wybierz żądany plik do testowania przepływu pracy. Składnik Media pliku wejściowego sprawdza plik i wypełnia jego PinY wyjściowe, aby odzwierciedlić szczegóły przykładowy plik, który go inspekcji.

![Dane wejściowe plików multimedialnych wypełnione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Dane wejściowe plików multimedialnych wypełnione*

Teraz, gdy dane wejściowe została wypełniona, następnym krokiem jest skonfigurować ustawienia kodowania danych wyjściowych. Podobnie jak podstawowy plik źródłowy został skonfigurowany, teraz skonfigurować właściwość zmiennej folderu danych wyjściowych, tuż poniżej.

![Skonfigurowane właściwości danych wejściowych i wyjściowych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Skonfigurowane właściwości danych wejściowych i wyjściowych*

### <a id="MXF_to_MP4_streams"></a>Inspekcja strumieni multimediów
Często ma potrzebne wiedzieć, jak strumień wygląda przepływ przez przepływ pracy. Aby przeprowadzić inspekcję strumienia w dowolnym momencie w przepływie pracy, po prostu kliknij danych wyjściowych lub wejściowych numeru pin w któregokolwiek ze składników. W takim przypadku spróbuj kliknięcie numeru pin dane wyjściowe nieskompresowanym wideo z pliku wejściowego nośnika. Okno dialogowe otwiera umożliwiający sprawdzanie wychodzącego wideo.

![Sprawdzanie numeru pin z danych wyjściowych nieskompresowanym wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Sprawdzanie numeru pin z danych wyjściowych nieskompresowanym wideo*

W tym przypadku pokazuje czy wideo zawiera dane wejściowe 1920 x 1080 pikseli na 24 klatek na sekundę w 4:2:2 próbkowania wideo prawie 2 minuty.

### <a id="MXF_to_MP4_file_generation"></a>Dodawanie koder wideo dla. Generowanie pliku w formacie MP4
Teraz nieskompresowanym wideo i wiele wyjście Audio bez kompresji, który kodów PIN są dostępne dla korzystać na nośnik pliku wejściowego. W celu kodowania wideo dla ruchu przychodzącego, jako składnik kodowania musi mają zostać dodane do przepływu pracy — w takim przypadku podczas generowania. Pliki mp4.

Kodowanie strumienia wideo H.264, należy dodać składnik AVC koder wideo na powierzchni projektanta. Ten składnik przyjmuje strumienia wideo Dekompresuj jako dane wejściowe i dostarcza AVC skompresowanego strumienia wideo w danych wyjściowych numer pin.

![Niepołączonych AVC kodera](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Niepołączonych AVC kodera*

Jego właściwości określają, jak kodowanie, dokładnie tak się dzieje. Przyjrzyjmy się zapoznać się z bardziej ważnych ustawień:

* Dane wyjściowe szerokość i wysokość, dane wyjściowe: Określa rozdzielczość zakodowanego filmu. W tym przypadku 640 x 360 jest dobre ustawienie.
* Szybkość klatek: po ustawieniu na przekazywanie tylko przyjmie szybkość klatek źródła, istnieje możliwość jednak to zmienić. Takie konwersji szybkość klatek jest nie ruchu wyrównane.
* Profil i poziom: Określa profil AVC i poziomu. Aby łatwo uzyskać więcej informacji na temat różnych poziomów i profilów, kliknij ikonę znaku zapytania w składniku koder wideo AVC i strona pomocy będzie pokazywanych więcej szczegółów dotyczących poszczególnych poziomów. W tym przykładzie należy użyć profilu główne na poziomie 3.2 (ustawienie domyślne).
* Oceń tryb kontroli i szybkości transmisji bitów (KB/s): w tym scenariuszu zoptymalizowany pod kątem dla stałej szybkości transmisji bitów (CBR) danych wyjściowych w 1200 kb/s
* Format wideo: zawiera informacje o VUI (informacje o wideo użyteczność) pobiera zapisane w strumieniu H.264 (po stronie informacje, które mogą być używane przez dekoder, aby ulepszyć ekran, ale nie są niezbędne do prawidłowo zdekodować):
* NTSC (typowe dla Stanów Zjednoczonych lub Japonii, za pomocą 30 kl. / s)
* PAL (typowe dla Europy, za pomocą 25 kl. / s)
* Tryb rozmiaru GOP: Ustaw o stałym rozmiarze GOP dla naszych potrzeb z interwałem klucz 2 sekund przy użyciu GOPs zamknięte. Ustawienie 2 sekundy gwarantuje, że zapewnia zgodność z dynamicznego tworzenia pakietów usługi Azure Media Services.

Ze źródłem danych kodera AVC, nawiązać pin wyjściowy nieskompresowanym wideo z nośnika plików wejściowych składnika nieskompresowanym wideo numeru pin z kodera AVC.

![Połączone AVC kodera](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Połączone kodera AVC Main*

### <a id="MXF_to_MP4_audio"></a>Kodowanie strumienia audio
W tym momencie oryginalnego strumienia audio nieskompresowanych nadal potrzebuje do skompresowania. Kompresja strumienia audio należy dodać składnik kodera AAC (Dolby) do przepływu pracy.

![Niepołączonych AVC kodera](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Niepołączonych kodera adaptacyjnych kontrolek aplikacji*

Teraz występuje niezgodność: istnieje tylko jednego nieskompresowanych audio wprowadzania numeru pin z kodera adaptacyjnych kontrolek aplikacji, gdy więcej niż prawdopodobnie wejściowy plik nośnika będzie mieć dwie różne nieskompresowanych strumieni audio dostępne: jeden dla po lewej stronie kanału audio i jeden dla po prawej stronie. (Jeśli są zajmujących się dźwięk przestrzenny, który jest sześć kanałów.) Dlatego nie jest możliwe na bezpośrednie łączenie audio ze źródła danych wejściowych plików multimediów do kodera audio AAC. Składnik AAC oczekuje tak zwane "przeplotem" strumień audio: jeden strumień, która ma zarówno po lewej stronie, jak i odpowiednich kanałów z przeplotem ze sobą. Raz wiemy z naszego pliku nośnika źródłowego, ścieżki audio są na pozycji w źródle firma Microsoft może generować takie przeplotem strumienia audio z położeniami poprawnie przypisane osoby mówiącej dla lewy i prawy.

Po pierwsze jeden chce, aby do generowania strumień przeplotem z kanałów audio wymagane źródła. Składnik Audio Stream Interleaver obsługuje to dla nas. Dodaj ją do przepływu pracy i łączenie audio dane wyjściowe z nośnika pliku wejściowego do niego.

![Połączone Interleaver Stream Audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Połączone Interleaver Stream Audio*

Teraz, gdy strumień audio z przeplotem, firma Microsoft nadal nie określ miejsce pozycji lewego lub prawego osoby mówiącej do przypisania. Aby można było określić, możemy wykorzystać przypisujący stanowisko osoby mówiącej.

![Dodawanie przypisujący stanowisko osoby mówiącej](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Dodawanie przypisujący stanowisko osoby mówiącej*

Konfigurowanie użytkownika przypisującego stanowisko osoby mówiącej do użycia przy użyciu stereo strumienia wejściowego, przez filtr ustawienie wstępne kodera "Niestandardowe" i kanał ustawienia o nazwie "w wersji 2.0 (L, R)." (To przypisuje pozycji po lewej stronie osoby mówiącej do kanału 1 i położenie odpowiednie osoby mówiącej do kanału 2.)

Połącz wyjście przypisujący stanowisko osoby mówiącej w danych wejściowych kodera adaptacyjnych kontrolek aplikacji. Następnie należy wskazać kodera adaptacyjnych kontrolek aplikacji do pracy z "w wersji 2.0 (L, R)" kanał zdefiniowane, będzie wówczas traktował, aby poradzić sobie z dźwiękiem stereo jako dane wejściowe.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Funkcje multipleksowania strumieni Audio i wideo do kontenera w formacie MP4
Biorąc pod uwagę nasz AVC zakodowany strumieni wideo i naszych AAC zakodowane strumienia audio, Azure możemy przechwytywać obie wartości do. Kontener w formacie MP4. Łączenie różnych strumieni w jedno proces jest nazywany "Multipleksowanie" (lub "muxing"). W takim przypadku firma Microsoft jest z przeplotem audio i wideo strumieni w jeden spójny. Pakiet w formacie MP4. Składnik, który służy do koordynowania ten formularz. Kontener w formacie MP4, jest nazywany multiplekser ISO MPEG-4. Dodaj go do powierzchni projektanta, a następnie nawiązać połączenie z kodera adaptacyjnych kontrolek aplikacji i koder wideo AVC jego danych wejściowych.

![Połączone MPEG4 multiplekser](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Połączone MPEG4 multiplekser*

### <a id="MXF_to_MP4_writing_mp4"></a>Zapisywanie pliku w formacie MP4
Podczas zapisywania pliku wyjściowego, składnik produkt wyjściowy pliku jest używany. Firma Microsoft to łączenie z danymi wyjściowymi multiplekser ISO MPEG-4 tak, aby jej dane wyjściowe pobiera zapisywane na dysku. Aby to zrobić, należy nawiązać numeru pin z danych wyjściowych kontenera (MPEG-4) zapisu numeru pin pliku danych wyjściowych.

![Połączone dane wyjściowe pliku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Połączone dane wyjściowe pliku*

Nazwa pliku, który jest używany jest określany przez właściwość pliku. Tej właściwości mogą być zapisane na stałe dla danej wartości, najbardziej prawdopodobne chce, aby ustawić go przy użyciu wyrażenia.

Aby automatycznie określić danych wyjściowych przepływu pracy plików właściwości nazwy z wyrażenia, kliknij przycisk obok nazwy pliku (obok ikona folderu). Z menu rozwijanego Wybierz następnie "Wyrażenie". To powoduje wyświetlenie edytora wyrażeń. Najpierw wyczyścić zawartość edytora.

![Pusty edytora wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Pusty edytora wyrażeń*

Edytor wyrażeń pozwala wprowadzić dowolną wartość literału, łączyć z jedną lub więcej zmiennych. Zmienne zaczynać od znaku dolara. Jak można naciśnij klawisz $, w edytorze zawiera pole listy rozwijanej dzięki szerokiemu wyborowi dostępnych zmiennych. W tym przypadku użyjemy kombinacji zmienna katalogu wyjściowego, a zmienna nazwa podstawowa pliku wejściowego:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Wypełnione się Edytor wyrażeń](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Wypełnione się Edytor wyrażeń*

> [!NOTE]
> Aby można było wyświetlić plik wyjściowy zadania kodowania na platformie Azure, musisz podać wartość w edytorze wyrażeń.
>
>

Po potwierdzeniu wyrażenia, naciskając ok, w oknie właściwości podglądy, co w tym momencie wartość rozwiązuje właściwości pliku.

![Wynikiem rozpoznania wyrażenia pliku jest katalog wyjściowy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Wynikiem rozpoznania wyrażenia pliku jest katalog wyjściowy*

### <a id="MXF_to_MP4_asset_from_output"></a>Tworzenie zasobu usługi multimediów z pliku wyjściowego
Gdy firma Microsoft ma zapisywanie pliku wyjściowego MP4, musimy nadal wskazują, że ten plik należy do elementu zawartości wyjściowej, które usługi media services generuje w wyniku wykonywania tego przepływu pracy. W tym celu węzła pliku/elementu zawartości wyjściowej na kanwie przepływu pracy jest używany. Wszystkie pliki przychodzące, w tym węźle należy część wynikowy zasobów usługi Azure Media Services.

Połącz składnik produkt wyjściowy pliku do składnika pliku/elementu zawartości wyjściowej, aby zakończyć przepływ pracy.

![Zakończono przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Zakończono przepływu pracy*

### <a id="MXF_to_MP4_test"></a>Testowanie Zakończono przepływu pracy lokalnie
Aby przetestować przepływ pracy lokalnie, naciśnij przycisk odtwarzania w pasku narzędzi u góry. Sprawdź dane wyjściowe generowane w folderze wyjściowym skonfigurowane, po zakończeniu wykonywania przepływu pracy. Zobaczysz Zakończono pliku wyjściowego MP4, który zostały zakodowane z pliku MXF źródła danych wejściowych.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kodowanie pliku MXF w formacie MP4 — metodę włączono funkcję dynamicznego tworzenia pakietów
Ten przewodnik tworzy zestaw plików MP4 z wieloma szybkości transmisji bitów z AAC zakodowane dźwięk pochodzący z jednej. MXF pliku wejściowego.

Po wyjściowego elementu zawartości o różnych szybkościach transmisji bitów jest pożądane do użycia w połączeniu z funkcjami dynamicznego tworzenia pakietów oferowane przez usługę Azure Media Services, plików wyrównane GOP MP4 z wieloma każdego, różne szybkości transmisji bitów i rozpoznawanie musi zostać wygenerowane. Aby to zrobić, [kodowanie MXF do pojedynczej szybkości transmisji bitów w formacie MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) Instruktaż zapewnia nam dobry punkt wyjścia.

![Uruchamianie przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Uruchamianie przepływu pracy*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Dodanie jednego lub więcej dodatkowych danych wyjściowych w formacie MP4
Każdy plik w formacie MP4 w naszym wynikowy zasobów usługi Azure Media Services obsługuje różne szybkości transmisji bitów i rozwiązanie. Dodajmy co najmniej jeden plik danych wyjściowych w formacie MP4 do przepływu pracy.

Aby upewnić się, że mamy nasz koderów wideo utworzone przy użyciu tych samych ustawień, jest najwygodniejszym Duplikowanie istniejącego AVC koder wideo i skonfigurować inną kombinację rozdzielczości i szybkości transmisji bitów (Dodajmy jeden 960 x 540 na 25 klatek na sekundę przy 2,5 MB/s ). Aby zduplikować istniejących kodera, kopiowania go wkleić na powierzchni projektowej.

Połączenie z naszego nowego składnika AVC wideo nieskompresowanych danych wyjściowych kod pin Media pliku wejściowego.

![Drugi kodera AVC połączone](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Drugi kodera AVC połączone*

Teraz można dostosować konfigurację nasz nowy AVC koder w warstwie służący do wypełniania wyjściowego 960 x 540 2,5 MB/s. (Użyj jego właściwości "szerokość dane wyjściowe", "Dane wyjściowe height" i "Szybkości transmisji bitów (KB/s)" dla tego).

Biorąc pod uwagę chcemy do użycia wynikowy zasobów wraz z dynamicznego tworzenia pakietów usługi Azure Media Services, punkt końcowy przesyłania strumieniowego musi mieć możliwość generowania tych plików MP4 HLS/podzielonej zawartości w formacie MP4/DASH fragmentów, które dokładnie są wyrównywane do siebie nawzajem w sposób, Klienci, którzy są przełączania się między różne szybkości transmisji zapoznania się jednym smooth ciągłe audio i wideo. Aby to zrobić, musimy upewnić się, że we właściwościach zarówno koderów AVC GOP ("group obrazy") rozmiar oba pliki MP4 jest równa 2 sekundy, które może odbywać się za:

* Ustawianie trybu rozmiar GOP GOP stały rozmiar i
* Interwał ramki klucza do dwóch sekund.
* również ustawić formant IDR GOP GOP zamknięte, aby upewnić się, wszystkie GOPs są stojących na ich własnych bez zależności

Aby ułatwić zrozumienie tego przepływu pracy, Zmień nazwę pierwszego kodera AVC do "koder wideo AVC 640 x 360 1200 kb/s" i drugi encoder AVC "koder wideo AVC 960 x 540 2 500 KB/s."

Teraz można dodać drugi multiplekser ISO MPEG-4 i drugi produkt wyjściowy pliku. Nawiązać połączenie z multiplekser Nowa usługa encoder AVC i upewnij się, że dane wyjściowe są kierowane do pliku danych wyjściowych. Następnie również łączyć z AAC kodera dźwięku dane wyjściowe do nowego multiplekser w danych wejściowych. Plik wyjściowy z kolei może następnie być podłączony do węzła pliku/elementu zawartości wyjściowej, aby dodać go do elementu zawartości usługi multimediów, który zostanie utworzony.

![Drugi Muxer i dane wyjściowe pliku połączone](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Drugi Muxer i dane wyjściowe pliku połączone*

Potrzeby utrzymywania zgodności z funkcji dynamicznego tworzenia pakietów usługi Azure Media Services należy skonfigurować multiplekser trybu fragmentu, aby liczba GOP lub czas trwania i ustawić GOPs na segment na 1. (Musi to być wartość domyślna).

![Tryby Muxer fragmentów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Tryby Muxer fragmentów*

Uwaga: możesz powtórzyć ten proces dla szybkości transmisji bitów i rozpoznawanie kombinacje które zostały dodane do wyjściowego elementu zawartości.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurowanie nazwy wyjściowego pliku
Mamy więcej niż jeden pojedynczy plik dodany do elementu zawartości wyjściowej. Zapewnia to potrzebę upewnij się, że nazwy plików dla każdego z plików wyjściowych różnią się od siebie nawzajem i może nawet zastosować konwencję nazewnictwa plików, dzięki czemu jest oczywiste, na podstawie nazwy pliku co w przypadku pracy z.

Nazywanie dane wyjściowe pliku mogą być kontrolowane za pomocą wyrażenia w projektancie. Otwórz okienko właściwości dla poszczególnych składników produkt wyjściowy pliku, a następnie otwórz Edytor wyrażeń dla właściwości pliku. Nasz pierwszy plik wyjściowy został skonfigurowany za pomocą następującego wyrażenia (zapoznaj się z samouczkiem do przechodzenia z [MXF do pojedynczej szybkości transmisji bitów pliku wyjściowego MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Oznacza to, czy naszych filename jest określany przez dwie zmienne: zapis katalogu wyjściowym i nazwa podstawowa pliku źródłowego. Pierwsza jest przedstawiany jako właściwość w folderze głównym przepływu pracy i drugim jest określana przez przychodzących plików. Katalog wyjściowy, które jest używane do testowania lokalnego; Ta właściwość zostanie ona zastąpiona aparatu przepływu pracy, gdy przepływ pracy jest wykonywana przez procesor multimediów oparte na chmurze w usłudze Azure Media Services.
Zapewnienie naszych plików wyjściowych nazewnictwa spójne danych wyjściowych, należy zmienić pierwszego pliku nazewnictwa wyrażenia:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

i drugą do:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Wykonanie pośrednich test, aby upewnić się, czy oba pliki danych wyjściowych w formacie MP4 prawidłowo są generowane.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Dodawanie oddzielne ścieżkę Audio
Jak zobaczymy w później, gdy firma Microsoft generuje plik .ism do korzystania z naszych plików danych wyjściowych w formacie MP4, firma Microsoft będzie również wymagał tylko plik MP4 jako ścieżkę audio do naszych adaptacyjnego przesyłania strumieniowego. Do utworzenia tego pliku, Dodaj dodatkowe muxer do przepływu pracy (ISO-MPEG-4 multiplekser) i połącz pin wyjściowy kodera adaptacyjnych kontrolek aplikacji za pomocą jego wprowadzania numeru pin dla ścieżki 1.

![Audio Muxer dodane](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer dodane*

Utwórz trzecią produkt wyjściowy pliku składnika, dane wyjściowe strumienia wychodzącego z muxer i konfigurowanie nazw wyrażenia w postaci plików:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Muxer audio, tworzenie produkt wyjściowy pliku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Muxer audio, tworzenie produkt wyjściowy pliku*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Dodawanie. Plik SMIL ISM
Do dynamicznego tworzenia pakietów do pracy w połączeniu z plików MP4 (i tylko dane audio w formacie MP4) w zasobie naszej usługi Media Services, należy również plik manifestu (nazywany również plikiem "SMIL": Zsynchronizowany język integrację multimediów). Ten plik wskazuje usługi Azure Media Services pliki MP4, jakie są dostępne dla funkcji dynamicznego tworzenia pakietów, które z tych, które należy wziąć pod uwagę audio przesyłania strumieniowego. Typowy plik manifestu zestawu w formacie MP4 przy użyciu jednego strumienia audio wygląda następująco:

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

Zawiera plik .ism wewnątrz instrukcji switch, odwołanie do każdego z poszczególnych plików wideo MP4 i oprócz tych również jedną (lub więcej) audio odwołania do plików MP4, która zawiera tylko audio.

Generowanie pliku manifestu dla naszego zestawu plików MP4 firmy może odbywać się za pośrednictwem składnik o nazwie "Edytor manifestu usługi AMS." Aby go użyć, przeciągnij go na powierzchnię i nawiązać połączenie z "Zapisu ukończony" PinY wyjściowe z trzech składników produkt wyjściowy pliku danych wejściowych zapisywania manifestu usługi AMS. Następnie upewnij się połączyć dane wyjściowe zapisywania manifestu usługi AMS do pliku/elementu zawartości wyjściowej.

Podobnie jak w przypadku naszych innych plików danych wyjściowych składników, należy skonfigurować Nazwa wyjściowego pliku ISM przy użyciu wyrażenia:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nasze Zakończono przepływ pracy wygląda poniżej:

![Zakończono MXF do przepływu pracy w formacie MP4 metodę](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Zakończono MXF do przepływu pracy w formacie MP4 metodę*

## <a id="MXF_to__multibitrate_MP4"></a>Kodowanie pliku MXF na metodę MP4 — rozszerzone planu
W [poprzednim przewodniku przepływ pracy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) zobaczyliśmy, jak do pojedynczego zasobu danych wejściowych MXF, można przekonwertować na elementu zawartości wyjściowej z plików MP4 z wieloma szybkościami transmisji bitów, tylko dane audio plik MP4 i plik manifestu do użycia w połączeniu z usługi Azure Media Dynamiczne tworzenie pakietów usług.

Ten poradnik pokazuje jak niektóre aspekty można usprawniać i wprowadzone bardziej wygodne.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Omówienie przepływu pracy w celu zwiększenia
![MP4 metodę przepływu pracy w celu zwiększenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*MP4 metodę przepływu pracy w celu zwiększenia*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Konwencje nazewnictwa plików
W poprzedniej przepływu pracy możemy określić proste wyrażenie jako podstawa do generowania nazw plików wyjściowych. Mimo że mamy kilka dublowania: wszystkie dane wyjściowe poszczególnych składników plików określone takie wyrażenie.

Na przykład naszego pliku składnika dane wyjściowe, pierwszego pliku wideo jest skonfigurowany przy użyciu tego wyrażenia:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Natomiast w przypadku drugiego wyjście wideo, mamy wyrażeń, takich jak:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

W takich sytuacjach przydałaby się bardziej przejrzysty, mniej podatne i wygodniejsze błędów można usunąć niektóre ta duplikacja i Postaramy łatwiejszych do skonfigurowania zamiast tego? Na szczęście możemy: projektanta możliwości wyrażenia w połączeniu z możliwość tworzenia niestandardowych właściwości w naszym głównym przepływu pracy zapewnia dodatkową warstwę wygody.

Załóżmy, że utworzyliśmy będzie nazwa pliku konfiguracji z różnych poszczególnych plików MP4. Tych różnych, które firma Microsoft będzie dążyć do skonfigurowania w jednej centralnej lokalizacji (w katalogu głównym naszym wykresie), gdzie będą one dostępne do skonfigurowania i generowania nazwy pliku dysku. Aby to zrobić, Zaczniemy od publikowania właściwości szybkości transmisji bitów z koderów zarówno AVC do katalogu głównego przepływu pracy, tak że staje się dostępny z obu katalogu głównego, jak również od koderów AVC. (Nawet jeśli wyświetlane w dwóch różnych miejsc, istnieje tylko jeden podstawową wartość.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Właściwości publikowania składników na głównego przepływu pracy
Otwórz pierwszy kodera AVC, przejdź do właściwości szybkości transmisji bitów (KB/s) i z listy rozwijanej wybierz polecenie Publikuj.

![Publikowanie właściwość szybkości transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikowanie właściwość szybkości transmisji bitów*

Okno dialogowe publikowania do publikowania w katalogu głównym naszych Wykres przepływu pracy, należy skonfigurować przy użyciu opublikowanych nazwę "video1bitrate" i nazwę wyświetlaną czytelny "Video 1 transmisji bitów". Skonfiguruj niestandardową nazwę grupy o nazwie "Przesyłanie strumieniowe różnych" i kliknę przycisk Publikuj.

![Publikowanie właściwość szybkości transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Okno dialogowe publikowania właściwości szybkości transmisji bitów*

Powtórz takie same dla właściwości szybkości transmisji bitów, drugi kodera AVC i nadaj mu nazwę "video2bitrate" o nazwie wyświetlanej "Transmisji bitów 2 wideo," w tej samej grupy niestandardowe "Różnych przesyłania strumieniowego".

Możemy teraz sprawdzić właściwości głównego przepływu pracy, zobaczymy naszej grupy niestandardowej o dwie właściwości opublikowanych wyświetlane. Oba są odzwierciedlający wartość ich odpowiednich AVC kodera szybkości transmisji bitów.

![Właściwości opublikowanych szybkości transmisji bitów w folderze głównym przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Zawsze, gdy chcemy uzyskać dostęp do tych właściwości z kodu lub wyrażenie, możemy to zrobić tak:

* z wbudowanego kodu ze składnika bezpośrednio poniżej katalogu głównego: node.getPropertyAsString('.. / video1bitrate ", wartość null)
* w wyrażeniu: ${ROOT_video1bitrate}

Przez opublikowanie naszych dźwiękową szybkości transmisji bitów w nim także zakończmy grupy "Różnych przesyłania strumieniowego". We właściwościach kodera adaptacyjnych kontrolek aplikacji Wyszukaj ustawienie szybkości transmisji bitów i wybierz polecenie Publikuj z listy rozwijanej obok niej. Opublikuj w katalogu głównym grafu przy użyciu nazwy "audio1bitrate", a wyświetlana nazwa "Audio 1 Bitrate" w ramach naszej grupy niestandardowe "Różnych przesyłania strumieniowego".

![Okno dialogowe publikowania audio szybkości transmisji bitów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Okno dialogowe publikowania audio szybkości transmisji bitów*

![Wynikowy właściwości audio i wideo w folderze głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Wynikowy właściwości audio i wideo w folderze głównym*

Zmiany dowolnego z tych trzech wartości również Rekonfiguruj i powoduje zmianę wartości na odpowiednie składniki, które są połączone z (i w przypadku, gdy publikowane z poziomu).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Został wygenerowany plik wyjściowy, nazwy polegać na wartości właściwości opublikowane
Zamiast hardcoding naszych nazwy wygenerowanych plików, możemy teraz zmienić nasze wyrażenie nazwy pliku na poszczególnych składników produkt wyjściowy pliku, które zależą od właściwości szybkości transmisji bitów, które opublikowaliśmy w folderze głównym programu graph. Począwszy od naszych pierwszy produkt wyjściowy pliku znaleźć właściwości pliku i Edytuj wyrażenie następująco:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Różne parametry w tym wyrażeniu można uzyskać dostęp i wprowadzanych przez naciśnięcie dolara na klawiaturze znajduje się w oknie wyrażeń. Jedną z dostępnych parametrów jest naszym właściwość video1bitrate opublikowanym wcześniej.

![Uzyskiwanie dostępu do parametrów w wyrażeniu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Uzyskiwanie dostępu do parametrów w wyrażeniu*

Zrób to samo dla pliku danych wyjściowych dla naszej drugiej wideo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

i dane wyjściowe pliku tylko dane audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Jeśli zmienimy teraz szybkości transmisji bitów dla każdego z plików wideo lub audio, odpowiednich koder zostanie ponownie skonfigurowane i Konwencji nazwy pliku na podstawie szybkości transmisji bitów będą uznawane wszystkie automatyczne.

## <a id="thumbnails_to__multibitrate_MP4"></a>Dodawanie miniatur do pliku wyjściowego MP4 metodę
Począwszy od przepływu pracy, który generuje [metodę MP4 wyjściowymi MXF, dane wejściowe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), firma Microsoft będzie analizować do dodawania miniatury w danych wyjściowych.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Omówienie przepływu pracy, aby dodać miniatur do
![MP4 metodę przepływu pracy, aby rozpocząć od](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*MP4 metodę przepływu pracy, aby rozpocząć od*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Dodawanie kodowania JPG
Serce naszych generowanie miniatur będzie składnik kodera JPG, możliwość dane wyjściowe pliki JPG.

![Koder JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Firma Microsoft nie może jednak łączyć się bezpośrednio naszych przesyłania strumieniowego wideo bez kompresji z nośnika pliku wejściowego do kodera JPG. Zamiast tego oczekuje były przekazywane poszczególnych klatek. W efekcie możemy to zrobić za pomocą składnika bramy klatek filmu wideo.

![Łączenie bramy ramki do kodera JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Łączenie bramy ramki do kodera JPG*

Brama ramki co tak wiele sekund lub ramek umożliwia klatki wideo do przekazania. Interwał i przesunięcia, z którym dzieje się tak czasu jest konfigurowany we właściwościach.

![Właściwości bramy ramki wideo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Właściwości bramy ramki wideo*

Utwórzmy miniaturę co minutę przez ustawienie tryb na czas (w sekundach) oraz interwału do 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Obsługa konwersji przestrzeń kolorów
Chociaż wydaje się logiczne, że oba PinY nieskompresowanym wideo brama ramki i Media pliku wejściowego teraz mogą być połączone, otrzymamy wynik Ostrzeżenie, jeśli firma Microsoft może to zrobić.

![Koloru okna wprowadzania komunikat o błędzie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Koloru okna wprowadzania komunikat o błędzie*

Jest to spowodowane sposób, w których są oznaczone kolorem informacji jest reprezentowane w naszych oryginalnego pierwotne nieskompresowanych strumienia wideo, pochodzące z naszych MXF, różni się od kodera JPG, czego oczekuje. W szczególności tak zwane "przestrzeni kolorów" "RGB" lub "Skali szarości" oczekuje się, usługa flow w. Oznacza to, wideo bramy ramki dla ruchu przychodzącego strumienia wideo musi umożliwiać konwersję stosowane dotyczące jego przestrzeń kolorów w pierwszej kolejności.

Przeciągnij przepływu pracy konwertera przestrzeń kolorów - firmy Intel i podłącz go do naszego bramy ramki.

![Łączenie konwertera przestrzeń kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Łączenie konwertera przestrzeń kolorów*

W oknie dialogowym właściwości wybierz BGR 24 wpis z listy ustawienie wstępne.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Pisanie, miniatury
Składnik kodera JPG, inny niż nasze wideo MP4, generuje więcej niż jeden plik. Aby poradzić sobie z tym, składnik zapisywania pliku JPG wyszukiwania sceny może służyć: przyjmuje przychodzących miniatury JPG i zapisuje je automatycznie, nazwę każdego pliku jest sufiks przez inny numer. (Liczba zwykle wskazuje liczbę sekund/jednostek w usłudze stream, jaka była pobierana miniatury.)

![Wprowadzenie do zapisywania pliku JPG wyszukiwania sceny](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Wprowadzenie do zapisywania pliku JPG wyszukiwania sceny*

Konfigurowanie właściwości ścieżki folderu danych wyjściowych przy użyciu wyrażenia: ${ROOT_outputWriteDirectory}

i właściwości prefiks nazwy pliku przy użyciu:

    ${ROOT_sourceFileBaseName}_thumb_

Prefiks Określa, jak są nazywane plików miniatur. Są one sufiks z numerem wskazujący wskaźnika pozycji w strumieniu.

![Właściwości składnika zapisywania pliku JPG wyszukiwania sceny](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Właściwości składnika zapisywania pliku JPG wyszukiwania sceny*

Łączenie zapisywania pliku JPG wyszukiwania sceny z węzłem pliku/elementu zawartości wyjściowej.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Wykrywanie błędów w przepływie pracy
Dane wejściowe konwertera przestrzeń kolorów nawiązać połączenie pierwotne nieskompresowanych wyjście wideo. Teraz można wykonywać lokalny przebieg testowy dla przepływu pracy. Istnieje duże prawdopodobieństwo, przepływ pracy zostanie nagle zatrzymał wykonywanie i oznaczać z czerwone obramowanie składnika, która napotkała błąd:

![Błąd konwertera przestrzeń kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Błąd konwertera przestrzeń kolorów*

Kliknij przycisk mały czerwoną ikonę "E" w górnym prawym rogu składnika konwerter przestrzeń kolorów, aby zobaczyć, co to jest przyczyna kodowania próba nie powiodła się.

![Okno dialogowe błędu konwerter przestrzeń kolorów](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Okno dialogowe błędu konwerter przestrzeń kolorów*

Okazuje się, jak widać, że przychodzące przestrzeni kolorów standardowego dla konwertera przestrzeń kolorów musi być rec601 naszych żądanego konwersji YUV RGB. Najwyraźniej naszych strumienia nie oznacza jego rec601. ("Rec" 601 to standard kodowania z przeplotem analogowy sygnały wideo w formie cyfrowej wideo. Określa on aktywny region, obejmujące 720 jasności oraz 360 próbki chrominance w każdym wierszu. Kolor kodowanie system jest nazywany YCbCr 4:2:2.)

Aby rozwiązać ten problem, firma Microsoft będzie wskazuje na metadane naszych strumień, który jest firma Microsoft zajmujących się rec601 zawartości. W tym celu użyjemy składnika Updater typu danych wideo umieścimy Between źródła raw, jak i składnika konwersja przestrzeni kolorów. Ta updater typu danych umożliwia ręcznej aktualizacji niektórych danych wideo właściwości typu. Skonfigurować tak, aby wskazać kolor miejsca Standard "Rec 601". Powoduje to aktualizacji typu danych wideo do znakowania strumienia z przestrzeni kolorów "Rec 601", jeśli nie było przestrzeń kolorów na jeszcze zdefiniowana. (Nie zastąpi on metadane, chyba że zaznaczono pole wyboru zastąpienie.)

![Aktualizowanie Standard przestrzeń kolorów na Updater typu danych](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizowanie Standard przestrzeń kolorów na Updater typu danych*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Zakończono przepływu pracy
Teraz, gdy nasz przepływ pracy jest zakończone, czy innego testu uruchomienie, aby wyświetlić, przekazuje on.

![Zakończono przepływu pracy dla pliku wyjściowego multi mp4 za pomocą miniatur](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Zakończono przepływu pracy dla pliku wyjściowego multi mp4 za pomocą miniatur*

## <a id="time_based_trim"></a>Na podstawie czasu przycinanie dane wyjściowe metodę w formacie MP4
Począwszy od przepływu pracy, który generuje [metodę MP4 wyjściowymi MXF, dane wejściowe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), firma Microsoft będzie analizować do przycinania źródłowy plik wideo, oparte na sygnatury czasowe.

### <a id="time_based_trim_start"></a>Omówienie przepływu pracy, aby rozpocząć dodawanie przycinania na
![Uruchamianie przepływu pracy, aby dodać przycinania na](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Uruchamianie przepływu pracy, aby dodać przycinania na*

### <a id="time_based_trim_use_stream_trimmer"></a>Za pomocą przycinarka Stream
Składnik przycinarka Stream umożliwi przycięcie początek i koniec strumienia wejściowego podstawy czasu informacji (sekundy, minuty,...). Przycinarka nie obsługuje przycinania opartych na klatkach.

![Przycinarka Stream](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Przycinarka Stream*

Zamiast łącze koderów AVC i przypisujący stanowisko osoby mówiącej do nośnika pliku wejściowego bezpośrednio, przetestujemy między tymi przycinarka strumienia. (Po jednym dla sygnału wideo i jeden dla przeplotem sygnału dźwiękowego.)

![Umieść przycinarka Stream między](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Umieść przycinarka Stream między*

Skonfigurujmy przycinarka, dzięki czemu będzie przetwarzać tylko wideo i audio od 15 sekund do 60 sekund w trakcie filmu wideo.

Przejdź do właściwości przycinarka Stream wideo i skonfiguruj zarówno czas rozpoczęcia (15 s) i czas zakończenia (60 s) właściwości. Aby upewnić się, że zarówno naszych przycinarka audio i wideo są zawsze skonfigurowane do tego samego rozpoczęcia i zakończenia wartości, publikujemy tych do katalogu głównego przepływu pracy.

![Opublikuj właściwości czasu rozpoczęcia z przycinarka Stream](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Opublikuj właściwości czasu rozpoczęcia z przycinarka Stream*

![Publikowanie okno dialogowe właściwości dla czas rozpoczęcia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publikowanie okno dialogowe właściwości dla czas rozpoczęcia*

![Publikowanie okno dialogowe właściwości dla czas zakończenia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publikowanie okno dialogowe właściwości dla czas zakończenia*

Jeśli możemy teraz sprawdzić głównego przepływu pracy, obie te właściwości są starannego wyświetlane i można je konfigurować w tym miejscu.

![Właściwości opublikowane, które są dostępne w folderze głównym](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Właściwości opublikowane, które są dostępne w folderze głównym*

Teraz Otwórz właściwości przycinania z przycinarka audio i skonfigurować zarówno rozpoczęcia i czas zakończenia z wyrażeniem, które odwołuje się do opublikowanych właściwości w katalogu głównym przepływu pracy.

Godzina rozpoczęcia audio przycinania:

    ${ROOT_TrimmingStartTime}

i jego czas zakończenia:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Zakończono przepływu pracy
![Zakończono przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Zakończono przepływu pracy*

## <a id="scripting"></a>Wprowadzenie do składników przy użyciu skryptu
Składniki inicjowanych przez skrypty może wykonywać dowolne skrypty podczas faz wykonywania przepływu pracy. Istnieją cztery różne skrypty, które mogą być wykonywane, każdy z szczególne cechy i miejsca w cyklu życia przepływu pracy:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentacja składnika inicjowanych przez skrypty znajduje się w bardziej szczegółowo dla każdego z powyższych subskrypcji. W [poniższej sekcji](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **realizeScript** składnika obsługi skryptów są używane do konstruowania xml cliplist na bieżąco, gdy przepływ pracy jest uruchamiany. Ten skrypt jest wywoływana podczas instalacji składnika, który odbywa się tylko raz na etapie jej cyklu życia.

### <a id="scripting_hello_world"></a>Wykonywanie skryptów w ramach przepływu pracy: Witaj świecie!
Przeciągnij składnik inicjowanych przez skrypty na powierzchni projektanta i zmienić go (na przykład "SetClipListXML").

![Dodawanie składnika przy użyciu skryptu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie składnika przy użyciu skryptu*

Inspekcji właściwości składnika inicjowanych przez skrypty cztery typy inny skrypt będzie pokazywane, każdy można skonfigurować do różnych skryptu.

![Właściwości składnika przy użyciu skryptu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Właściwości składnika przy użyciu skryptu*

Wyczyść processInputScript, a następnie otwórz edytor dla realizeScript. Obecnie firma Microsoft jest gotowe i gotowe do uruchomienia skryptu.

Skrypty są zapisywane w Groovy, dynamicznie skompilowanej język skryptów dla platformy Java, która zachowuje zgodność z językiem Java. W rzeczywistości większość kodu języka Java jest prawidłowym kodem Groovy.

W kontekście naszych realizeScript Napiszmy hello prosty skrypt groovy świata. W edytorze, wprowadź następujące czynności:

    node.log("hello world");

Teraz wykonywanie testu lokalnego. Po tym przebiegu (za pomocą karty System w składniku inicjowanych przez skrypty) dzienniki właściwość sprawdzić.

![Dane wyjściowe dziennika programu Witaj świecie](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Dane wyjściowe dziennika programu Witaj świecie*

Obiekt węzła, który nazywamy metoda dziennika, odnosi się do naszej bieżącej "węzeł" lub składnika, który możemy one skryptów w obrębie. Każdy składnik ma związku z tym możliwości rejestrowania przetwarzania danych wyjściowych, dostępne za pośrednictwem karty systemu. W tym przypadku dane wyjściowe są przekazywane literał ciągu "hello world". Ważne, aby zrozumieć, poniżej przedstawiono, że można to potwierdzić za bezcenne narzędzie do debugowania, zapewniając wgląd w skrypcie faktycznie czynności.

Z w ramach naszych środowisko obsługi skryptów, mamy także dostęp do właściwości na inne składniki. Wypróbuj:

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

Nasze okno Dziennik pokazuje nam następujące czynności:

![Dane wyjściowe dziennika do uzyskiwania dostępu do ścieżki węzła](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Dane wyjściowe dziennika do uzyskiwania dostępu do ścieżki węzła*

## <a id="frame_based_trim"></a>Przycinanie opartych na klatkach dane wyjściowe metodę w formacie MP4
Począwszy od przepływu pracy, który generuje [metodę MP4 wyjściowymi MXF, dane wejściowe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), firma Microsoft będzie analizować do przycinania źródłowy plik wideo na podstawie liczby klatek.

### <a id="frame_based_trim_start"></a>Omówienie planu, aby rozpocząć dodawanie przycinania na
![Przepływ pracy, aby rozpocząć dodawanie przycinania na](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Przepływ pracy, aby rozpocząć dodawanie przycinania na*

### <a id="frame_based_trim_clip_list"></a>Przy użyciu listy klipu XML
W wszystkie poprzednie samouczki dotyczące przepływu pracy użyliśmy Media plików wejściowych składnika jako źródła danych wejściowych wideo. Dla tego konkretnego scenariusza, będzie używany składnik źródłowy listy klipu zamiast tego. To nie powinna być preferowany sposób pracy tylko klip źródło listy jest używane w przypadku rzeczywistych powód, aby to zrobić (w tym przypadku poniższy, gdzie wprowadzamy, takich jak korzystanie z funkcji przycinania listy clip).

Aby przełączyć się z naszych Media pliku wejściowego klipu źródłem listy, przeciągnij składnik źródłowy listy klipu na powierzchnię projektu i połącz pin XML listy klipu węzła XML listy klipu projektanta przepływów pracy. Spowoduje to wypełnienie źródło listy klipu z PinY wyjściowe zgodnie z naszym wejściowego pliku wideo. Teraz nawiązać połączenie bez kompresji wideo i Audio nieskompresowanego pinów ze źródła listy klipu odpowiednich koderów AVC i Audio Stream Interleaver. Teraz usunąć nośnika pliku wejściowego.

![Zastąpione Media pliku wejściowego klipu źródło listy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Zastąpione Media pliku wejściowego klipu źródło listy*

Składnik źródłowy listy klipu przyjmuje jako dane wejściowe "Klipu Lista XML." Po wybraniu pliku źródłowego na potrzeby testów lokalnie, ten klip listy xml jest wypełniane automatycznie dla Ciebie.

![Automatycznie wypełniony właściwości XML listy klipu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatycznie wypełniony właściwości XML listy klipu*

Wyszukiwanie nieco bliżej xml, to, jak wygląda jak:

![Edytowanie okna dialogowego Lista klipu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Edytowanie okna dialogowego Lista klipu*

To nie odzwierciedlać możliwości xml listy klipu. Jedną z opcji naszym jest dodany element "Przycinanie" w ramach obu audio i wideo źródła, takich jak to:

![Dodanie przycinania element do listy klipu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Dodanie przycinania element do listy klipu*

Zmodyfikowany plik xml listy klipu, takich jak ten powyżej i wykonania lokalnego przebiegu testu, zostanie wyświetlony film wideo został poprawnie spacje od 10 do 20 sekund w trakcie filmu wideo.

Sprzecznie co się stanie po wykonaniu przebiegu lokalnego do tej samej xml cliplist nie będzie zawierało ten sam efekt zastosowania w przepływie pracy, który jest uruchamiany w usłudze Azure Media Services. Po uruchomieniu koder w warstwie Premium Azure cliplist xml jest generowany za każdym razem, gdy ponownie, oparte na pliku wejściowego, którą podano zadania kodowania. Oznacza to, wszelkie zmiany, jaką zrobiliśmy na xml Niestety będzie zastąpione.

Aby wyeliminować xml cliplist wyczyszczenie po uruchomieniu zadania kodowania, firma Microsoft można ponownie wygenerować go na bieżąco zaraz po rozpoczęciu przepływu pracy. Takie akcje niestandardowe mogą być pobierane przez co to jest nazywany "Ze skryptem składnik". Aby uzyskać więcej informacji, zobacz [wprowadzenie do składnika inicjowanych przez skrypty](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Przeciągnij składnik inicjowanych przez skrypty na powierzchni projektanta i zmień jego nazwę na "SetClipListXML."

![Dodawanie składnika przy użyciu skryptu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Dodawanie składnika przy użyciu skryptu*

Inspekcji właściwości składnika inicjowanych przez skrypty cztery typy inny skrypt są pokazywane, każdy można skonfigurować do różnych skryptu.

![Właściwości składnika przy użyciu skryptu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Właściwości składnika przy użyciu skryptu*

### <a id="frame_based_trim_modify_clip_list"></a>Modyfikowanie listy klipu ze składnika inicjowanych przez skrypty
Zanim firma ponownego zapisywania cliplist xml, który jest generowany podczas uruchamiania przepływu pracy, musimy mieć dostęp do właściwości xml cliplist i zawartość. Firma Microsoft może zrobić tak:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Przychodzące listy klipu rejestrowane](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Przychodzące listy klipu rejestrowane*

Najpierw musimy możliwość określenia, w tym momencie, aż do punktu, który chcemy Przytnij wideo. Aby uprościć ten użytkownikowi technical mniej przepływu pracy, należy opublikować dwie właściwości do katalogu głównego wykresu. Aby to zrobić, kliknij prawym przyciskiem myszy powierzchnię projektanta i wybierz pozycję "Dodaj właściwość":

* Pierwsza właściwość: "ClippingTimeStart" o typie: "CZASOWY"
* Drugą właściwością: "ClippingTimeEnd" o typie: "CZASOWY"

![Dodaj okno dialogowe właściwości dla czas rozpoczęcia wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dodaj okno dialogowe właściwości dla czas rozpoczęcia wycinka*

![Opublikowane wycinka czasu właściwości w folderze głównym przepływu pracy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Opublikowane wycinka czasu właściwości w folderze głównym przepływu pracy*

Skonfiguruj obie te właściwości do odpowiedniej wartości:

![Konfigurowanie menu start wycinka i kończyć właściwości](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurowanie menu start wycinka i kończyć właściwości*

Teraz z w ramach naszego skryptu, firma Microsoft można uzyskać dostęp obie te właściwości, w następujący sposób:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno Dziennik przedstawiający początek i koniec wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno Dziennik przedstawiający początek i koniec wycinka*

Umożliwia analizowanie ciągów czasowy, w bardziej wygodne do użycia formularza, za pomocą prostego wyrażenia regularnego:

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

![Okno Dziennik z danych wyjściowych przeanalizowany czasowy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno Dziennik z danych wyjściowych przeanalizowany czasowy*

Dzięki tym informacjom pod ręką możemy teraz zmodyfikować xml cliplist, aby odzwierciedlić czas rozpoczęcia i zakończenia dla żądanego wycinka dokładne klatek filmu.

![Kod skryptu, aby dodać elementy przycinania](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kod skryptu, aby dodać elementy przycinania*

Zostało to zrobione za pomocą ciągu normalnych operacji na strumieniach. Wynikowy kod xml listy klipu są zapisywane właściwość clipListXML w folderze głównym przepływu pracy za pośrednictwem metody "setProperty". Okno Dziennik po uruchomieniu innego testu pokazywałaby nam następujące czynności:

![Rejestrowanie wynikowego listy klipu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Rejestrowanie wynikowego listy klipu*

Do przebiegu testu, aby zobaczyć, jak strumienie audio i wideo została obcięta. Jak odbywa się na więcej niż jeden przebieg testu z różnymi wartościami dla punktów przycinania, można zauważyć, że te nie bierze się pod konta jednak! Dzieje się, projektanta, w przeciwieństwie do środowiska uruchomieniowego platformy Azure, nie powoduje zastąpienia cliplist xml każdego uruchomienia. Oznacza to, że tylko po raz pierwszy ustawiono punktów wejścia i wyjścia, spowoduje, że plik xml, aby przekształcić wszystkich innych przypadkach nasz klauzuli guard (jeśli (`clipListXML.indexOf("<trim>") == -1`)) uniemożliwi Dodawanie innego elementu przycinania, gdy istnieje już już ono przepływu pracy.

Aby uprościć przepływu pracy do testowania lokalnie, najlepsze dodamy niektóre kod zachowywaniu dom, który sprawdza się, jeśli już istniał przycinania elementu. Jeśli tak, możemy go usunąć przed kontynuowaniem, modyfikując plik xml z nowymi wartościami. Zamiast przy użyciu zwykłego działań na ciągach, prawdopodobnie bezpieczniej jest w tym celu za pośrednictwem modelu obiektów xml rzeczywistych analizy.

Zanim jednak możemy dodać taki kod, musimy najpierw Dodaj szereg instrukcji importu na początku naszego skryptu:

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

Dzięki temu możemy dodać wymagany kod czyszczenia:

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

Ten kod przechodzi bezpośrednio nad punktu, jaką możemy dodać przycinania elementy do pliku xml cliplist.

Na tym etapie firma Microsoft można uruchomić i zmodyfikować przepływu pracy jako tak, jak chcemy, aby, mając zmiany zastosowane kiedykolwiek czasu.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Dodawanie właściwości wygody ClippingEnabled
Dowolną może nie zawsze przycinania do wykonania, są teraz Zakończ off przepływu pracy, dodając wygodne flagę logiczną, wskazującą, czy chcemy umożliwić przycinanie / przycinania.

Tak jak poprzednio Opublikuj nową właściwość do katalogu głównego przepływu pracy o nazwie "ClippingEnabled" typu "BOOLEAN".

![Opublikowane właściwości umożliwiające wycinka](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Opublikowane właściwości umożliwiające wycinka*

Za pomocą poniżej klauzuli proste guard, firma Microsoft Sprawdź, czy wymagana jest przycinania i zdecydować, jeśli naszej listy klipu jako takie ma zostać zmodyfikowana, czy nie.

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
[Wprowadzenie do kodowania w usłudze Azure Media Services w warstwie Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak używać kodowania w usłudze Azure Media Services w warstwie Premium](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kodowanie zawartości na żądanie przy użyciu usługi Azure Media](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formaty i kodeki usługi Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)

[Przykładowe pliki przepływu pracy](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Narzędzie Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
