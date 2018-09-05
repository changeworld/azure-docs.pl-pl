---
title: Jak utworzyć model językowy za pomocą usługi Speech Service — Microsoft Cognitive Services
description: Dowiedz się, jak utworzyć model językowy za pomocą usługi Speech Service w usługach Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 97659bf38b6d06464eee37a33e87d0c528cdcd37
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126942"
---
# <a name="tutorial-create-a-custom-language-model"></a>Samouczek: tworzenie niestandardowego modelu językowego

W tym dokumencie utworzysz niestandardowy model językowy, którego możesz użyć w połączeniu z istniejącymi najnowocześniejszymi modelami mowy firmy Microsoft, aby dodać interakcję głosową do swojej aplikacji.

W dokumencie omówiono następujące kwestie:
> [!div class="checklist"]
> * Przygotowywanie danych
> * Importowanie zestawu danych językowych
> * Tworzenie niestandardowego modelu językowego

Jeśli nie masz konta usług Cognitive Services, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że Twoje konto usług Cognitive Services jest połączone z subskrypcją, otwierając stronę [Subskrypcje usługi Cognitive Services](https://customspeech.ai/Subscriptions).

Możesz połączyć się z subskrypcją usługi Speech Service utworzoną w witrynie Azure Portal, klikając przycisk **Połącz z istniejącą subskrypcją**.

Aby uzyskać informacje o tworzeniu subskrypcji usługi Speech Service w witrynie Azure Portal, zobacz stronę z [wprowadzeniem](get-started.md).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Aby utworzyć niestandardowy model językowy dla aplikacji, musisz podać listę przykładowych wypowiedzi do systemu, na przykład:

*   „Pacjent miał pokrzywkę przez ostatni tydzień”.
*   „Pacjent miał dobrze wygojoną bliznę po operacji przepukliny”.

Zdania nie muszą być pełnymi zdaniami ani być poprawne gramatyczne, lecz powinny dokładnie odzwierciedlać oczekiwane dane głosowe, które system może napotkać we wdrożeniu. Te przykłady powinny odzwierciedlać styl i zawartość zadania, które użytkownicy będą wykonywać w aplikacji.

Dane modelu językowego powinny być zapisane w formacie UTF-8 BOM. Plik tekstowy powinien zawierać jeden przykład (zdanie, wypowiedź lub zapytanie) na wiersz.

Jeśli chcesz, aby niektóre terminy miały większą wagę (znaczenie), możesz dodać kilka wypowiedzi zawierających dany termin do danych. 

Główne wymagania dotyczące danych językowych przedstawiono w poniższej tabeli.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM|
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalny rozmiar pliku | 1,5 GB |
| Uwagi | unikaj powtarzania znaków więcej niż cztery razy, na przykład „aaaaa”|
| Uwagi | bez znaków specjalnych, takich jak „\t” lub innych znaków UTF-8 powyżej U+00A1 z [tabeli znaków Unicode](http://www.utf8-chartable.de/)|
| Uwagi | identyfikatory URI również zostaną odrzucone, ponieważ nie istnieje unikatowy sposób wymowy identyfikatora URI|

Po zaimportowania tekstu jest on znormalizowany jako tekst, aby umożliwić jego przetwarzanie przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych. Zobacz [Wytyczne dotyczące transkrypcji](prepare-transcription.md), aby określić odpowiedni język podczas przygotowywania danych językowych.

## <a name="language-support"></a>Obsługa języków

Następujące języki są obsługiwane w przypadku niestandardowych modeli językowych funkcji **Zamiana mowy na tekst**.

Kliknij, aby uzyskać pełną listę [obsługiwanych języków](supported-languages.md)

## <a name="import-the-language-data-set"></a>Importowanie zestawu danych językowych

Kliknij przycisk Import (Importuj) w wierszu „Language Datasets (Językowe zestawy danych). Witryna wyświetli stronę pobierania nowego zestawu danych.

Gdy wszystko będzie gotowe do zaimportowania zestawu danych językowych, zaloguj się do [portalu usługi Speech Service](https://customspeech.ai).  Następnie kliknij menu rozwijane Custom Speech na wstążce u góry i wybierz pozycję Adaptation Data (Dane adaptacyjne). Jeśli po raz pierwszy przekazujesz dane do usługi Speech Service, zostanie wyświetlona pusta tabela o nazwie Datasets (Zestawy danych).

Aby zaimportować nowy zestaw danych, kliknij przycisk Import (Importuj) w wierszu Language Datasets (Zestawy danych językowych). Witryna wyświetli stronę pobierania zestawu danych. W polach Name i Description wprowadź nazwę i opis, które ułatwią identyfikację zestawu danych w przyszłości, i wybierz ustawienia regionalne. Następnie kliknij przycisk Choose File (Wybierz plik) i wskaż plik tekstowy danych językowych. Następnie kliknij przycisk Import (Importuj), aby przekazać zestaw danych. W zależności od rozmiaru zestawu danych importowanie może potrwać kilka minut.

![try](media/stt/speech-language-datasets-import.png)

Po zakończeniu importowania nastąpi powrót do tabeli danych językowych, w której będzie wyświetlany wpis odpowiadający przekazanemu zestawowi danych językowych. Zwróć uwagę, że przypisano mu unikatowy identyfikator (identyfikator GUID). Dane będą miały również stan, który odzwierciedla ich bieżący stan. Stan ma wartość Waiting (Oczekiwanie) podczas umieszczania danych w kolejce do przetwarzania, Processing (Przetwarzanie) podczas ich weryfikacji i Complete (Ukończono), gdy są gotowe do użycia. Podczas weryfikacji danych wykonywany jest szereg kontroli tekstu w pliku i normalizacja tekstu danych.

Kiedy stan ma wartość Complete (Ukończono), możesz kliknąć pozycję View Report (Wyświetl raport), aby wyświetlić raport weryfikacji danych językowych. Wyświetlana będzie liczba wypowiedzi, których weryfikacja zakończyła się pomyślnie i nie powiodła się. W poniższym przykładzie weryfikacja dwóch przykładów nie powiodła się z powodu nieprawidłowych znaków (w tym zestawie danych pierwszy wiersz zawierał dwa znaki TAB, drugi kilka znaków spoza zestawu znaków drukowalnych ASCII, a trzeci wiersz był pusty).

![try](media/stt/speech-language-datasets-report.png)

Gdy stan zestawu danych językowych ma wartość Complete (Ukończono), można użyć tego zestawu do utworzenia niestandardowego modelu językowego.

![try](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Tworzenie niestandardowego modelu językowego

Kiedy dane językowe są gotowe, kliknij pozycję Language Models (Modele językowe) w menu rozwijanym Menu, aby rozpocząć proces tworzenia niestandardowego modelu językowego. Ta strona zawiera tabelę o nazwie „Language Models” (Modele językowe) z bieżącymi niestandardowymi modelami językowymi. Jeśli jeszcze nie utworzono żadnych niestandardowych modeli językowych, tabela jest pusta. Bieżące ustawienia regionalne są wyświetlane w tabeli obok odpowiedniego wpisu danych.

Przed podjęciem jakiegokolwiek działania należy wybrać odpowiednie ustawienia regionalne. Bieżące ustawienia regionalne są wskazywane w tytule tabeli na wszystkich stronach danych, modelu i wdrożenia. Aby zmienić ustawienia regionalne, kliknij przycisk Change Locale (Zmień ustawienia regionalne) pod tytułem tabeli. Spowoduje to wyświetlenie strony potwierdzenia ustawień regionalnych. Kliknij przycisk OK, aby powrócić do tabeli.

Na stronie Create Language Model (Tworzenie modelu językowego) wprowadź nazwę i opis modelu w polach Name i Description, aby łatwiej śledzić ważne informacje dotyczące tego modelu, na przykład używany zestaw danych. Następnie z menu rozwijanego wybierz pozycję Base Language Model (Podstawowy model językowy). Ten model będzie punktem wyjścia dla Twojego dostosowania. Dostępne są dwa podstawowe modele językowe do wyboru. Model Search and Dictation (Model językowy dla wyszukiwania i dyktowania) jest odpowiedni dla mowy kierowanej do aplikacji, na przykład poleceń, zapytań wyszukiwania lub dyktowania. Model Conversational (Model językowy dla konwersacji) jest przeznaczony do rozpoznawania mowy w stylu konwersacyjnym. Ten typ mowy jest zazwyczaj kierowany do innej osoby i występuje w centrach telefonicznej obsługi klienta lub na konferencjach. Ponadto publicznie dostępny jest również nowy model o nazwie Universal (Uniwersalny). Model uniwersalny ma na celu obsługiwanie wszystkich scenariuszy oraz ewentualne zastąpienie modeli dla wyszukiwania i dyktowania oraz dla konwersacji.

5.  W poniższym przykładzie po określeniu podstawowego modelu językowego wybierz zestaw danych językowych, którego chcesz użyć na potrzeby dostosowania, korzystając z menu rozwijanego Language Data (Dane językowe)

![try](media/stt/speech-language-models-create2.png)

Podobnie jak w przypadku tworzenia modelu akustycznego, możesz opcjonalnie wybrać wykonanie testowania offline nowego modelu po zakończeniu przetwarzania. Oceny modelu wymagają akustycznego zestawu danych.

Aby wykonać testowanie offline modelu językowego, zaznacz pole wyboru obok opcji Offline Testing (Testowanie offline). Następnie wybierz model akustyczny z menu rozwijanego. Jeśli nie utworzono żadnego niestandardowego modelu akustycznego, podstawowe modele akustyczne firmy Microsoft będą jedynymi dostępnymi w menu. Jeśli wybrano podstawowy model językowy dla konwersacji, należy w tym miejscu użyć modelu akustycznego dla konwersacji. Jeśli używany jest model językowy dla wyszukiwania i dyktowania, należy wybrać model akustyczny dla wyszukiwania i dyktowania.

Na koniec wybierz zestaw danych akustycznych, którego chcesz użyć do przeprowadzenia oceny.

Gdy wszystko będzie gotowe do rozpoczęcia przetwarzania, naciśnij przycisk Create (Utwórz), który przeniesie Cię do tabeli modeli językowych. W tabeli pojawi się nowy wpis odpowiadający temu modelowi. Stan odzwierciedla stan modelu, który będzie przechodził przez kilka stanów, w tym Waiting (Oczekiwanie), Processing (Przetwarzanie) i Complete (Ukończono).

Jeśli model osiągnął stan Complete (Ukończono), można go wdrożyć w punkcie końcowym. Kliknięcie pozycji View Result (Wyświetl wynik) spowoduje wyświetlenie wyników testowania offline, jeśli je wykonano.

Jeśli w pewnym momencie zechcesz zmienić nazwę lub opis modelu, możesz użyć linku Edit (Edytuj) w odpowiednim wierszu tabeli modeli językowych.

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Jak rozpoznawać mowę w języku C#](quickstart-csharp-dotnet-windows.md)
- [Dane przykładowe Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
