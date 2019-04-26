---
title: 'Samouczek: Tworzenie modelu akustycznego za pomocą usługi Speech Service'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć model akustyczny na platformie Azure przy użyciu usług przetwarzania mowy.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: f2a111558fa3f515b797745dc51e32f625bbd91f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539149"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Samouczek: Tworzenie niestandardowego modelu akustycznego

Tworzenie niestandardowego modelu akustycznego jest pomocne, jeśli aplikacja została zaprojektowana do użycia w konkretnym środowisku, np. w samochodzie, wraz z określonymi urządzeniami, w określonych warunkach rejestrowania bądź pod kątem konkretnej populacji użytkowników. Przykłady obejmują akcentowaną mowę, specyficzne szumy w tle lub użycie konkretnego mikrofonu na potrzeby rejestrowania.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Przygotowywanie danych
> * Importowanie zestawu danych akustycznych
> * Tworzenie niestandardowego modelu akustycznego

Jeśli nie masz konta usługi Azure Cognitive Services, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że Twoje konto usług Cognitive Services jest połączone z subskrypcją, otwierając stronę [Subskrypcje usługi Cognitive Services](https://cris.ai/Subscriptions).

Nawiązanie połączenia z subskrypcją usług przetwarzania mowy, który został utworzony w witrynie Azure portal, wybierając **połączyć z istniejącą subskrypcją**.

Aby uzyskać informacji na temat tworzenia subskrypcji usługi mowy w witrynie Azure portal, zobacz [bezpłatnego wypróbowania usługi mowy](get-started.md).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Aby dostosować model akustyczny pod kątem wybranej domeny, wymagana jest kolekcja danych mowy. Kolekcja może zawierać od kilku wypowiedzi do kilkuset godzin mowy. Kolekcja składa się z zestawu plików dźwiękowych z danymi mowy oraz pliku tekstowego z transkrypcjami wszystkich plików dźwiękowych. Dane dźwiękowe powinny być reprezentatywne dla scenariusza, w którym chcesz używać aparatu rozpoznawania.

Na przykład:

* Jeżeli chcesz lepiej rozpoznawać mowę w głośnym środowisku fabryki, pliki dźwiękowe powinny zawierać mowę osób rozmawiających w głośnej fabryce.
* Jeżeli chcesz zoptymalizować wydajność pod kątem jednej osoby mówiącej, &mdash;na przykład aby transkrybować wszystkie audycje z cyklu „fireside chats” (rozmowy przy kominku) Franklina D. Roosevelta&mdash;, pliki dźwiękowe powinny zawierać wiele przykładów tylko tej osoby mówiącej.

Zestaw danych akustycznych na potrzeby dostosowywania modelu akustycznego składa się z dwóch części: (1) to zestaw plików audio zawierających dane mowy i (2) to plik zawierający transkrypcje wszystkich plików audio.

### <a name="audio-data-recommendations"></a>Zalecenia dotyczące danych dźwiękowych

* Wszystkie pliki dźwiękowe w zestawie danych powinny być przechowywane w formacie dźwiękowym WAV (RIFF).
* Dźwięk musi mieć częstotliwość próbkowania 8 kHz lub 16 kHz, a wartości próbek powinny być przechowywane jako nieskompresowane 16-bitowe liczby całkowite ze znakiem (typu short) za pomocą kodowania PCM.
* Obsługiwane są wyłącznie jednokanałowe pliki dźwiękowe (mono).
* Pliki audio mogą mieć długość od 100 mikrosekund do 1 minuty, chociaż idealnie powinny mieć około 10–12 sekund. Idealnie każdy plik dźwiękowy powinien zaczynać i kończyć się co najmniej 100 mikrosekundami ciszy; najczęściej stosuje się ciszę o długości od 500 mikrosekund do 1 sekundy.
* Jeśli w danych występuje hałas w tle, zalecamy umieszczenie w danych przykładów z dłuższymi segmentami ciszy, &mdash;na przykład kilkusekundowymi&mdash;, przed zawartością z mową i/lub po niej.
* Każdy plik dźwiękowy powinien składać się z jednej wypowiedzi &mdash; np. jednego podyktowanego zdania, jednego zapytania lub jednego zwrotu w systemie dialogowym.
* Każdy plik dźwiękowy w zestawie danych powinien mieć unikatową nazwę pliku oraz rozszerzenie wav.
* Zestaw plików dźwiękowych powinien być umieszczony w jednym folderze bez podkatalogów. Cały zestaw plików dźwiękowych powinien być spakowany jako jedno archiwum zip.

> [!NOTE]
> Importy danych za pośrednictwem portalu internetowego są obecnie ograniczone do 2 GB, więc jest to maksymalny rozmiar zestawu danych akustycznych. Ten rozmiar odpowiada około 17 godzinom zarejestrowanego dźwięku przy częstotliwości próbkowania 16 kHz lub 34 godzin zarejestrowanego dźwięku przy częstotliwości próbkowania 8 kHz. Główne wymagania dotyczące danych dźwiękowych przedstawiono w poniższej tabeli:
>

| Właściwość | Wartość |
|---------- |----------|
| Format pliku | RIFF (WAV) |
| Częstotliwość próbkowania | 8000 Hz lub 16 000 Hz |
| Kanały | 1 (mono) |
| Format próbki | PCM, 16-bitowe liczby całkowite |
| Czas trwania pliku | 0,1 s < czas trwania < 12 sekund |
| Otoczenie ciszą | > 0,1 s |
| Format archiwum | zip |
| Maksymalny rozmiar archiwum | 2 GB |

> [!NOTE]
> Nazwy plików powinny zawierać tylko litery łacińskie i być w formacie „nazwa_pliku.rozszerzenie”.

## <a name="language-support"></a>Obsługa języków

Aby uzyskać pełną listę języków obsługiwanych przez niestandardowe modele językowe **zamiany mowy na tekst**, zobacz [Języki obsługiwane na potrzeby usługi Speech Service](language-support.md#speech-to-text).

### <a name="transcriptions-for-the-audio-dataset"></a>Transkrypcje zestawu danych dźwiękowych

Transkrypcje dla wszystkich plików WAV powinny znajdować się w jednym pliku tekstowym (zwykły tekst). Każdy wiersz pliku z transkrypcją powinien zawierać nazwę jednego z plików dźwiękowych, a następnie odpowiednią transkrypcję. Nazwę pliku i transkrypcję należy rozdzielać przy użyciu tabulatora (\t).

  Na przykład:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkrypcja powinna być kodowana za pomocą kodowania UTF-8 ze znacznikiem kolejności bajtów (BOM).

Transkrypcje są normalizowane pod względem tekstu, aby mogły być przetwarzane przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych do usługi Custom Speech Service. Aby użyć odpowiedniego języka podczas przygotowywania transkrypcji, zobacz [Wskazówki dotyczące używania usługi Speech Service](prepare-transcription.md).

Wykonaj kroki w następnej sekcji przy użyciu [portalu usług przetwarzania mowy](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Importowanie zestawu danych akustycznych

Po przygotowaniu plików dźwiękowych i transkrypcji są one gotowe do zaimportowania do portalu internetowego usługi.

Aby zaimportować je, najpierw upewnij się, że zalogowano się [portalu usług przetwarzania mowy](https://cris.ai). Następnie z listy rozwijanej **Custom Speech** na wstążce wybierz pozycję **Adaptation data** (Dane adaptacji). Jeśli po raz pierwszy przekazujesz dane do usługi Custom Speech Service, zostanie wyświetlona pusta tabela o nazwie **Datasets** (Zestawy danych).

W wierszu **Acoustic Datasets** (Zestawy danych akustycznych) wybierz przycisk **Importuj**, a w witrynie zostanie wyświetlona strona umożliwiająca przekazanie nowego zestawu danych.

![Strona Import Acoustic Data (Importowanie danych akustycznych)](media/stt/speech-acoustic-datasets-import.png)

W polach **Name** (Nazwa) i **Description** (Opis) podaj odpowiednie informacje. Przyjazne opisy są przydatne do śledzenia różnych przekazywanych zestawów danych.

W polach **Transcriptions file (.txt)** (Plik transkrypcji — txt) i **Audio files (.zip)** (Pliki dźwiękowe — zip) wybierz polecenie **Browse** (Przeglądaj), a następnie wybierz plik transkrypcji ze zwykłym tekstem i archiwum zip z plikami WAV. Po zakończeniu przygotowania wybierz pozycję **Import** (Importuj), aby przekazać dane. Dane zostaną przekazane. W przypadku większych zestawów danych proces importowania może potrwać kilka minut.

Po zakończeniu przekazywania wróć do tabeli **Acoustic Datasets** (Zestawy danych akustycznych). Zostanie wyświetlona pozycja odpowiadająca zestawowi danych akustycznych. Zwróć uwagę, że przypisano mu unikatowy identyfikator (identyfikator GUID). Zostanie wyświetlony bieżący stan danych: *NotStarted* (Nie rozpoczęto) podczas umieszczania danych w kolejce do przetwarzania, *Running* (Uruchomiono) podczas ich weryfikacji i *Complete* (Zakończono), gdy są gotowe do użycia.

Sprawdzanie poprawności danych obejmuje serię kontroli plików dźwiękowych na potrzeby weryfikacji formatu pliku, długości i częstotliwości próbkowania, a także kontroli plików transkrypcji na potrzeby weryfikacji formatu pliku i wykonania pewnych zadań z zakresu normalizacji tekstu.

Kiedy stan to *Succeeded* (Powodzenie), możesz wybrać opcję **Details** (Szczegóły), aby wyświetlić raport weryfikacji danych akustycznych. Wyświetlana będzie liczba wypowiedzi, których weryfikacja zakończyła się pomyślnie i nie powiodła się. W przykładzie na poniższym obrazie weryfikacja dwóch plików WAV nie powiodła się ze względu na nieprawidłowy format dźwięku. W tym zestawie danych jeden plik ma niepoprawną częstotliwość próbkowania, a format drugiego pliku jest niepoprawny.

![Strona Adaptation of Data Details (Szczegóły adaptowania danych)](media/stt/speech-acoustic-datasets-report.png)

Jeśli chcesz zmienić nazwę lub opis zestawu danych, możesz wybrać link **Edit** (Edytuj) i zmienić wartości. Nie można zmodyfikować pozycji pliku transkrypcji ani plików dźwiękowych.

## <a name="create-a-custom-acoustic-model"></a>Tworzenie niestandardowego modelu akustycznego

Gdy stan zestawu danych akustycznych to *Complete* (Zakończono), można użyć zestawu danych, aby utworzyć niestandardowy model akustyczny. Aby to zrobić, wybierz pozycję **Acoustic Models** (Modele akustyczne) z listy rozwijanej **Custom Speech**. W tabeli z etykietą **Your models** (Twoje modele) zostaną wyświetlone wszystkie Twoje niestandardowe modele akustyczne. Tabela jest pusta przy jej pierwszym wyświetleniu. Tytuł tabeli jest wyświetlany za pomocą bieżących ustawień regionalnych. Obecnie możesz tworzyć modele akustyczne tylko dla języka angielskiego (Stany Zjednoczone).

Aby utworzyć nowy model, wybierz pozycję **Create New** (Utwórz nowy) pod tytułem tabeli. Tak jak wcześniej wprowadź nazwę i opis, aby łatwiej identyfikować ten model. Na przykład pola **Description** (Opis) można użyć do zarejestrowania modelu początkowego i zestawu danych akustycznych, których użyto do utworzenia modelu.

Następnie z listy rozwijanej **Base Acoustic Model** (Podstawowy model akustyczny) wybierz model podstawowy. Model podstawowy stanowi punkt wyjścia dla Twojego dostosowania. Dostępne są dwa podstawowe modele akustyczne do wyboru:
* Model **Microsoft Search and Dictation AM** (Model językowy do wyszukiwania i dyktowania firmy Microsoft) jest odpowiedni dla mowy kierowanej do aplikacji, na przykład poleceń, zapytań wyszukiwania lub dyktowania.
* Model **Microsoft Conversational** (Model językowy dla konwersacji firmy Microsoft) jest przeznaczony do rozpoznawania mowy w stylu konwersacyjnym. Ten typ mowy jest zazwyczaj kierowany do innej osoby i występuje w centrach telefonicznej obsługi klienta lub na konferencjach.

Opóźnienie dla wyników częściowych w modelach konwersacyjnych jest wyższe niż w przypadku modeli wyszukiwania i dyktowania.

> [!NOTE]
> Aktualnie wprowadzamy nowy model **Universal** (Uniwersalny), który będzie przeznaczony do obsługi wszystkich scenariuszy. Wymienione wyżej modele pozostaną publicznie dostępne.

Następnie z listy rozwijanej **Acoustic Data** (Dane akustyczne) wybierz dane akustyczne, których chcesz użyć do wykonania dostosowania.

![Strona Create Acoustic Model (Tworzenie modelu akustycznego)](media/stt/speech-acoustic-models-create2.png)

Po zakończeniu przetwarzania możesz opcjonalnie wybrać przeprowadzenie testowania dokładności nowego modelu. Spowoduje to uruchomienie oceny zamiany mowy na tekst dla wybranego zestawu danych akustycznych przy użyciu niestandardowego modelu akustycznego oraz zwrócenie wyników w formie raportu. Aby wykonać ten test, zaznacz pole wyboru **Accuracy Testing** (Test dokładności). Następnie z listy rozwijanej wybierz model językowy. Jeśli nie utworzono żadnych niestandardowych modeli językowych, na liście rozwijanej będą dostępne tylko podstawowe modele językowe. Aby wybrać najbardziej odpowiedni model językowy, zobacz [Samouczek: Tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md).

Na koniec wybierz zestaw danych akustycznych, którego chcesz użyć do oceny modelu niestandardowego. Jeśli wykonujesz test dokładności, ważne jest wybranie danych akustycznych, które będą różnić się od tych, które zostały użyte do utworzenia modelu, aby uzyskać realistyczną ocenę wydajności modelu. Testowanie dokładności na danych uczenia nie umożliwia oceny wydajności dostosowanego modelu w rzeczywistych warunkach. Wyniki będą zbyt optymistyczne. Należy również zauważyć, że test dokładności jest ograniczony do 1000 wypowiedzi. W przypadku większych zestawów danych akustycznych podczas testu zostanie ocenione tylko pierwsze 1000 wypowiedzi.

Gdy wszystko będzie gotowe do uruchomienia procesu dostosowywania, wybierz pozycję **Create** (Utwórz).

W tabeli modeli akustycznych zostanie wyświetlona nowa pozycja, która odpowiada nowemu modelowi. W tabeli przedstawiono również stan procesu: *Waiting* (Oczekuje), *Processing* (Przetwarzanie) lub *Complete* (Zakończony).

![Strona Acoustic Models (Modele akustyczne)](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznawanie mowy w języku C#](quickstart-csharp-dotnet-windows.md)
- [Dane przykładowe Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
