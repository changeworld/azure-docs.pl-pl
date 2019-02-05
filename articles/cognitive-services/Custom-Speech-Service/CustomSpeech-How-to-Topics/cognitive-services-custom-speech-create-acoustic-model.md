---
title: Samouczek dotyczący tworzenia modelu akustycznego za pomocą usługi Custom Speech Service — Microsoft Cognitive Services | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć model akustyczny za pomocą usługi Custom Speech Service w usługach Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 0e4c21a064cdb0a60aef49482eee4b768112b899
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216425"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Samouczek: Tworzenie niestandardowego modelu akustycznego

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

W ramach tego samouczka utworzysz niestandardowy model akustyczny dla danych mowy, które ma rozpoznawać Twoja aplikacja. Tworzenie niestandardowego modelu akustycznego jest pomocne, jeśli aplikacja została zaprojektowana do użycia w konkretnym środowisku, np. w hałaśliwej fabryce bądź pod kątem konkretnej populacji użytkowników.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Przygotowywanie danych
> * Importowanie zestawu danych akustycznych
> * Tworzenie niestandardowego modelu akustycznego

Jeśli nie masz konta usług Cognitive Services, przed rozpoczęciem utwórz [bezpłatne konto](https://cris.ai).

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że Twoje konto usług Cognitive Services jest połączone z subskrypcją, otwierając stronę [Subskrypcje usługi Cognitive Services](https://cris.ai/Subscriptions).

Jeśli żadne subskrypcje nie są wyświetlane, możesz kliknąć przycisk **Uzyskaj bezpłatną subskrypcję**, aby usługi Cognitive Services utworzyły konto dla Ciebie. Możesz też połączyć się z subskrypcją usługi wyszukiwania niestandardowego utworzoną w witrynie Azure Portal, klikając przycisk **Połącz z istniejącą subskrypcją**.

Aby uzyskać informacje na temat tworzenia subskrypcji usługi wyszukiwania niestandardowego w witrynie Azure Portal, zobacz [Create a Cognitive Services APIs account in the Azure portal](../../cognitive-services-apis-create-account.md) (Tworzenie konta interfejsów API usług Cognitive Services w witrynie Azure Portal).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Aby dostosować model akustyczny pod kątem wybranej domeny, wymagana jest kolekcja danych mowy. Ta kolekcja składa się z zestawu plików dźwiękowych z danymi mowy oraz pliku tekstowego z transkrypcjami wszystkich plików dźwiękowych. Dane dźwiękowe powinny być reprezentatywne dla scenariusza, w którym chcesz używać aparatu rozpoznawania.

Na przykład:

*   Jeżeli chcesz lepiej rozpoznawać mowę w głośnym środowisku fabryki, pliki dźwiękowe powinny zawierać mowę osób rozmawiających w głośnej fabryce.
<a name="Preparing data to customize the acoustic model"></a>
*   Jeżeli przykładowo chcesz zoptymalizować wydajność pod kątem jednej osoby mówiącej, należy transkrybować wszystkie rozmowy Fireside FDR, a pliki dźwiękowe powinny zawierać wiele przykładów tylko tej osoby mówiącej.

Zestaw danych akustycznych na potrzeby dostosowywania modelu akustycznego składa się z dwóch części: (1) to zestaw plików audio zawierających dane mowy i (2) to plik zawierający transkrypcje wszystkich plików audio.

### <a name="audio-data-recommendations"></a>Zalecenia dotyczące danych dźwiękowych

*   Wszystkie pliki dźwiękowe w zestawie danych powinny być przechowywane w formacie dźwiękowym WAV (RIFF).
*   Dźwięk musi mieć częstotliwość próbkowania 8 kHz lub 16 kHz, a wartości próbkowania powinny być przechowywane jako nieskompresowane 16-bitowe liczby całkowite ze znakiem PCM (wartości short).
*   Obsługiwane są wyłącznie jednokanałowe pliki dźwiękowe (mono).
*   Pliki dźwiękowe muszą mieć długość od 100 ms do 1 minuty. Idealnie każdy plik dźwiękowy powinien zaczynać i kończyć się co najmniej 100 ms ciszy; najczęściej stosuje się ciszę o długości od 500 ms do 1 sekundy.
*   Jeśli w danych występuje hałas w tle, zaleca się zastosowanie części przykładów z dłuższymi segmentami ciszy w danych, np. kilkusekundowymi przed i/lub po zawartości z mową.
*   Każdy plik dźwiękowy powinien składać się z jednej wypowiedzi, np. jednego zdania na potrzeby dyktowania, jednego zapytania lub jednego zwrotu w systemie dialogowym.
*   Każdy plik dźwiękowy w zestawie danych powinien mieć unikatową nazwę pliku oraz rozszerzenie „wav”.
*   Zestaw plików dźwiękowych powinien być umieszczony w jednym folderze bez podkatalogów. Cały zestaw plików dźwiękowych powinien być spakowany jako jedno archiwum ZIP.

> [!NOTE]
> Importy danych za pośrednictwem portalu internetowego są obecnie ograniczone do 2 GB, więc jest to maksymalny rozmiar zestaw danych akustycznych. Jest to odpowiednik około 17 godzin zarejestrowanego dźwięku przy częstotliwości próbkowania 16 kHz lub 34 godzin zarejestrowanego dźwięku przy częstotliwości próbkowania 8 kHz. Główne wymagania dotyczące danych dźwiękowych przedstawiono w poniższej tabeli.
>

| Właściwość | Wartość |
|---------- |----------|
| Format pliku | RIFF (WAV) |
| Częstotliwość próbkowania | 8000 Hz lub 16 000 Hz |
| Kanały | 1 (mono) |
| Format próbki | PCM, 16-bitowe liczby całkowite |
| Czas trwania pliku | 0,1 s < czas trwania < 60 sekund |
| Otoczenie ciszą | > 0,1 s |
| Format archiwum | Zip |
| Maksymalny rozmiar archiwum | 2 GB |

### <a name="transcriptions"></a>Transkrypcje

Transkrypcje dla wszystkich plików WAV powinny znajdować się w jednym pliku tekstowym (zwykły tekst). Każdy wiersz pliku z transkrypcją powinien zawierać nazwę jednego z plików dźwiękowych, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t).

  Na przykład:
```
  speech01.wav  speech recognition is awesome

  speech02.wav  the quick brown fox jumped all over the place

  speech03.wav  the lazy dog was not amused
```

Transkrypcje będą normalizowane pod kątem tekstu, aby mogły być przetwarzane przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych do usługi Custom Speech Service. Zapoznaj się z sekcją [wytycznych dotyczących transkrypcji](cognitive-services-custom-speech-transcription-guidelines.md) dla odpowiedniego języka, przygotowując transkrypcje.

Następujące kroki są wykonywane przy użyciu [portalu usługi Custom Speech Service](https://cris.ai). 

## <a name="import-the-acoustic-data-set"></a>Importowanie zestawu danych akustycznych

Po przygotowaniu plików dźwiękowych i transkrypcji są one gotowe do zaimportowania do portalu internetowego usługi.

Aby to zrobić, należy upewnić się, że użytkownik jest zalogowany w [portalu usługi Custom Speech Service](https://cris.ai). Następnie kliknij menu rozwijane Custom Speech na wstążce u góry i wybierz pozycję Adaptation Data (Dane adaptacyjne). Jeśli po raz pierwszy przekazujesz dane do usługi Custom Speech Service, zostanie wyświetlona pusta tabela o nazwie Datasets (Zestawy danych). 

Kliknij przycisk Import (Importuj) w wierszu Acoustic Datasets (Akustyczne zestawy danych). Witryna wyświetli stronę pobierania nowego zestawu danych.

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-import.png)

Wpisz _nazwę_ i _opis_ w odpowiednich polach tekstowych. Są one przydatne do śledzenia różnych przekazywanych zestawów danych. Następnie kliknij opcję „Choose File” (Wybierz plik) w pozycji „Transcription File” (Plik transkrypcji) oraz „WAV Files” (Pliki WAV) i wybierz tekstowy plik transkrypcji (zwykły tekst) oraz archiwum ZIP z plikami WAV. Po zakończeniu kliknij opcję „Import” (Importuj), aby przekazać dane. Następnie Twoje dane zostaną przekazane. W przypadku większych zestawów danych proces może zająć kilka minut.

Po zakończeniu przekazywania nastąpi powrót do tabeli „Acoustic Datasets” (Zestawy danych akustycznych), w której będzie wyświetlany wpis odpowiadający przekazanemu zestawowi danych akustycznych. Zwróć uwagę, że przypisano mu unikatowy identyfikator (identyfikator GUID). Dane będą miały również stan, który odzwierciedla ich bieżący stan. Stan ma wartość Waiting (Oczekiwanie) podczas umieszczania danych w kolejce do przetwarzania, Processing (Przetwarzanie) podczas ich weryfikacji i Complete (Ukończono), gdy są gotowe do użycia.

Sprawdzanie poprawności danych obejmuje serię kontroli plików dźwiękowych na potrzeby weryfikacji formatu pliku, długości i częstotliwości próbkowania, a także kontroli plików transkrypcji na potrzeby weryfikacji formatu pliku i wykonania pewnych zadań z zakresu normalizacji tekstu.

Kiedy stan to „Complete” (Zakończono), możesz kliknąć opcję „Details” (Szczegóły), aby wyświetlić raport weryfikacji danych akustycznych. Wyświetlana będzie liczba wypowiedzi, których weryfikacja zakończyła się pomyślnie i nie powiodła się. W poniższym przykładzie dwa pliki WAV nie przeszły pomyślnie weryfikacji, ponieważ zastosowano nieprawidłowy format dźwiękowy (w tym zestawie danych jeden z plików miał nieprawidłową częstotliwość próbkowania, a drugi nieprawidłowy format pliku).

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-report.png)

Jeśli w pewnym momencie zechcesz zmienić nazwę lub opis zestawu danych, możesz kliknąć link „Edit” (Edytuj) i zmienić te wpisy. Pamiętaj, że nie możesz modyfikować plików dźwiękowych lub transkrypcji.

## <a name="create-a-custom-acoustic-model"></a>Tworzenie niestandardowego modelu akustycznego

Gdy stan zestawu danych akustycznych to „Complete” (Ukończono), zestawu można użyć do utworzenia niestandardowego modelu akustycznego. Aby to zrobić, kliknij przycisk „Acoustic Models” (Modele akustyczne) w menu rozwijanym „Custom Speech”. Zostanie wyświetlona tabela „Your models” (Twoje modele) z listą wszystkich niestandardowych modeli akustycznych. Ta tabela jest pusta, jeżeli jest to pierwsze użycie usługi. Bieżące ustawienia regionalne są wyświetlane w tytule tabeli. Obecnie modele akustyczne można tworzyć wyłącznie w języku angielskim (USA).

Aby utworzyć nowy model, kliknij opcję „Create New” (Utwórz nowy) pod tytułem tabeli. Tak jak wcześniej wprowadź nazwę i opis, aby łatwiej identyfikować ten model. Na przykład pola „Description” (Opis) można użyć do zarejestrowania modelu początkowego i zestawu danych akustycznych, których użyto do tworzenia modelu. Następnie wybierz opcję „Base Acoustic Model” (Podstawowy model akustyczny) z menu rozwijanego. Podstawowy model to model, który jest punktem początkowym dla dostosowania. Dostępne są dwa podstawowe modele akustyczne do wyboru. _Microsoft Search and Dictation AM_ (Model akustyczny dla wyszukiwania i dyktowania firmy Microsoft) jest odpowiedni dla mowy kierowanej do aplikacji, na przykład poleceń, zapytań wyszukiwania lub dyktowania. _Model Microsoft Conversational_ (Model językowy dla konwersacji firmy Microsoft) jest przeznaczony do rozpoznawania mowy w stylu konwersacyjnym. Ten typ mowy jest zazwyczaj kierowany do innej osoby i występuje w centrach telefonicznej obsługi klienta lub na konferencjach. Pamiętaj, że opóźnienie dla wyników częściowych w modelach konwersacyjnych jest wyższe niż w przypadku modeli wyszukiwania i dyktowania.

Następnie wybierz dane akustyczne, których chcesz użyć, aby wykonać dostosowanie przy użyciu menu rozwijanego.

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-create2.png)

Możesz opcjonalnie wybrać wykonanie testowania offline nowego modelu po zakończeniu przetwarzania. Spowoduje to uruchomienie oceny zamiany mowy na tekst dla wybranego zestawu danych akustycznych przy użyciu niestandardowego modelu akustycznego oraz zwrócenie wyników w formie raportu. Aby wykonać ten test, zaznacz pole wyboru „Accuracy Testing” (Test dokładności). Następnie wybierz model językowy z menu rozwijanego. Jeśli nie utworzono żadnych niestandardowych modeli językowych, na liście rozwijanej będą dostępne tylko podstawowe modele językowe. Zobacz [opisy](cognitive-services-custom-speech-create-language-model.md) podstawowych modeli językowych w przewodniku i wybierz model, który będzie najbardziej odpowiedni.

Na koniec wybierz zestaw danych akustycznych, którego chcesz użyć do oceny modelu niestandardowego. Jeśli wykonujesz test dokładności, ważne jest wybranie danych akustycznych, które będą różnić się od tych, które zostały użyte do utworzenia modelu, aby uzyskać realistyczne poczucie wydajności modelu. Należy również zauważyć, że testowanie offline jest ograniczone do 1000 wypowiedzi. W przypadku większych zestawów danych akustycznych podczas testu zostanie oceniony tylko pierwszy 1000 wypowiedzi.

Gdy wszystko będzie gotowe do uruchomienia procesu dostosowywania, naciśnij przycisk „Create” (Utwórz).

Zobaczysz teraz nowy wpis w tabeli modeli akustycznych odpowiadający nowemu modelowi. Stan procesu jest odzwierciedlany w tabeli. Stany to: „Waiting” (Oczekiwanie), „Processing” (Przetwarzanie) i „Complete” (Ukończono).

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony niestandardowy model akustyczny do użycia z plikami dźwiękowymi i transkrypcjami. Aby utworzyć niestandardowy plik językowy do użycia z plikami tekstowymi, przejdź do samouczka na temat tworzenia niestandardowego modelu językowego.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowego modelu językowego](cognitive-services-custom-speech-create-language-model.md)
