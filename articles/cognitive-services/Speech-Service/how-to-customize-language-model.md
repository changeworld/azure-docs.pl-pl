---
title: 'Samouczek: tworzenie modelu językowego przy użyciu usługi Mowa.'
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć model językowy przy użyciu usługi Mowa. Użyj tego niestandardowego modelu językowego w połączeniu z istniejącymi najnowocześniejszymi modelami mowy firmy Microsoft, aby dodać interakcję głosową do swojej aplikacji.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 8276b86df2dc1bc90fc07da262aa0979f7562619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995956"
---
# <a name="tutorial-create-a-custom-language-model"></a>Samouczek: Tworzenie niestandardowego modelu językowego

W tym dokumencie utworzysz niestandardowy model językowy. Następnie możesz użyć tego niestandardowego modelu językowego w połączeniu z istniejącymi najnowocześniejszymi modelami mowy firmy Microsoft, aby dodać interakcję głosową do swojej aplikacji.

W dokumencie omówiono następujące kwestie:
> [!div class="checklist"]
> * Przygotowywanie danych
> * Importowanie zestawu danych językowych
> * Tworzenie niestandardowego modelu językowego

Jeśli nie masz konta usług Cognitive Services, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby się upewnić, że Twoje konto usług Cognitive Services jest połączone z subskrypcją, otwórz stronę [Subskrypcje usługi Cognitive Services](https://customspeech.ai/Subscriptions).

Aby połączyć się z subskrypcją usługi Speech Services utworzoną w witrynie Azure Portal, naciśnij przycisk **Połącz z istniejącą subskrypcją**.

Aby uzyskać informacje o tworzeniu subskrypcji usługi Speech Services w witrynie Azure Portal, zobacz stronę [Wprowadzenie](get-started.md).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Aby utworzyć niestandardowy model językowy dla aplikacji, należy podać listę przykładowych wypowiedzi do systemu, na przykład:

*   „Pacjent miał pokrzywkę przez ostatni tydzień”.
*   „Pacjent miał dobrze wygojoną bliznę po operacji przepukliny”.

Zdania nie muszą być pełnymi zdaniami ani być poprawne gramatyczne, lecz powinny dokładnie odzwierciedlać oczekiwane dane głosowe, które system może napotkać podczas wdrożenia. Te przykłady powinny odzwierciedlać styl i zawartość zadania, które użytkownicy będą wykonywać w aplikacji.

Dane modelu językowego powinny być zapisane w formacie UTF-8 BOM. Plik tekstowy powinien zawierać jeden przykład (zdanie, wypowiedź lub zapytanie) na wiersz.

Jeśli chcesz, aby niektóre terminy miały większą wagę (znaczenie), możesz dodać kilka wypowiedzi do swoich danych zawierających te terminy.

Główne wymagania dotyczące danych językowych przedstawiono w poniższej tabeli.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | UTF-8 BOM|
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalna wielkość pliku | 1,5 GB |
| Uwagi | Unikaj powtarzania znaków więcej niż cztery razy, na przykład „aaaaa”|
| Uwagi | Nie używaj znaków specjalnych, takich jak „\t” lub innych znaków UTF-8 powyżej U+00A1 z [tabeli znaków Unicode](https://www.utf8-chartable.de/)|
| Uwagi | identyfikatory URI również zostaną odrzucone, ponieważ nie istnieje unikatowy sposób wymowy identyfikatora URI|

Po zaimportowania tekstu jest on znormalizowany jako tekst, aby umożliwić jego przetwarzanie przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych. Zobacz [wytyczne dotyczące transkrypcji](prepare-transcription.md), aby określić odpowiedni język, który zostanie użyty podczas przygotowywania danych językowych.

## <a name="language-support"></a>Obsługa języków

Zobacz pełną listę [obsługiwanych języków](language-support.md#text-to-speech) dla niestandardowych modeli językowych funkcji **Zamiana mowy na tekst**.



## <a name="import-the-language-data-set"></a>Importowanie zestawu danych językowych

Naciśnij przycisk **Import** (Importuj) w wierszu **Language Datasets** (Zestawy danych języków). W witrynie zostanie wyświetlona strona pobierania nowego zestawu danych.

Gdy wszystko będzie gotowe do zaimportowania zestawu danych języka, zaloguj się do [portalu usługi Speech Services](https://customspeech.ai). Następnie kliknij menu rozwijane **Custom Speech** na wstążce u góry. Następnie wybierz pozycję **Adaptation Data** (Dane adaptacji). Jeśli po raz pierwszy próbujesz przekazać dane do usługi Speech Services, zostanie wyświetlona pusta tabela o nazwie **Datasets** (Zestawy danych).

Aby zaimportować nowy zestaw danych, naciśnij przycisk **Import** (Importuj) w wierszu **Language Datasets** (Zestawy danych języka). W witrynie zostanie wyświetlona strona do przekazania nowego zestawu danych. W polach **Name** (Nazwa) i **Description** (Opis) wprowadź nazwę i opis, które ułatwią identyfikację zestawu danych w przyszłości, i wybierz ustawienia regionalne.

Następnie naciśnij przycisk **Choose File** (Wybierz plik) i wskaż plik tekstowy danych języka. Naciśnij przycisk **Import** (Importuj), aby przekazać zestaw danych. W zależności od wielkości zestawu danych importowanie może potrwać kilka minut.

![Spróbuj](media/stt/speech-language-datasets-import.png)

Po zakończeniu importowania w danych języka jest wyświetlany wpis odpowiadający zestawowi danych języka. Zwróć uwagę, że przypisano mu unikatowy identyfikator (identyfikator GUID). Dane będą miały również stan, który odzwierciedla ich bieżącą sytuację. Stan ma wartość **Waiting** (Oczekiwanie) podczas umieszczania danych w kolejce do przetwarzania, **Processing** (Przetwarzanie) podczas ich weryfikacji i **Complete** (Ukończono), gdy są gotowe do użycia. Podczas weryfikacji danych wykonywany jest szereg kontroli tekstu w pliku. Przeprowadzana jest również normalizacja tekstu danych.

Kiedy stan ma wartość **Complete** (Ukończono), możesz kliknąć pozycję **View Report** (Wyświetl raport), aby wyświetlić raport weryfikacji danych językowych. Wyświetlana będzie liczba wypowiedzi, których weryfikacja zakończyła się pomyślnie i nie powiodła się. W poniższym przykładzie w dwóch przypadkach weryfikacja nie powiodła się z powodu nieprawidłowych znaków. (W tym zestawie danych pierwsza linia zawierała dwa znaki tabulacji, druga kilka znaków, które nie należą do zestawu znaków drukowania ASCII, a trzecia linia była pusta).

![Spróbuj](media/stt/speech-language-datasets-report.png)

Jeśli stan zestawu danych języka ma wartość **Complete** (Ukończono), można go użyć do utworzenia niestandardowego modelu językowego.

![Spróbuj](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Tworzenie niestandardowego modelu językowego

Gdy dane językowe będą gotowe, wybierz pozycję **Language Models** (Modele językowe) w menu rozwijanym **Menu**, aby rozpocząć proces tworzenia niestandardowego modelu językowego. Ta strona zawiera tabelę o nazwie **Language Models** (Modele językowe) z bieżącymi niestandardowymi modelami językowymi. Jeśli jeszcze nie utworzono żadnych niestandardowych modeli językowych, tabela jest pusta. Bieżące ustawienia regionalne są wyświetlane w tabeli obok odpowiedniego wpisu danych.

Przed podjęciem jakiegokolwiek działania należy wybrać odpowiednie ustawienia regionalne. Bieżące ustawienia regionalne są wskazywane w tytule tabeli na wszystkich stronach danych, modelu i wdrożenia. Aby zmienić ustawienia regionalne, naciśnij przycisk **Change Locale** (Zmień ustawienia regionalne) znajdujący się w obszarze tytułu tabeli.  Spowoduje to przejście do strony potwierdzenia ustawień regionalnych. Naciśnij przycisk **OK**, aby powrócić do tabeli.

Na stronie Create Language Model (Tworzenie modelu językowego) wprowadź nazwę i opis modelu w polach **Name** (Nazwa) i **Description** (Opis), aby łatwiej śledzić ważne informacje dotyczące tego modelu, na przykład używany zestaw danych. Następnie z menu rozwijanego wybierz pozycję **Base Language Model** (Podstawowy model językowy). Ten model stanowi punkt wyjścia dla Twojego dostosowania.

Dostępne są dwa podstawowe modele językowe do wyboru. Model Search and Dictation (Model językowy do wyszukiwania i dyktowania) jest odpowiedni dla mowy kierowanej do aplikacji, na przykład poleceń, zapytań wyszukiwania lub dyktowania. Model Conversational (Model językowy do konwersacji) jest przeznaczony do rozpoznawania mowy w stylu konwersacyjnym. Ten typ mowy jest zazwyczaj kierowany do innej osoby i występuje w centrach telefonicznej obsługi klienta lub na konferencjach. Ponadto publicznie dostępny jest również nowy model o nazwie Universal (Uniwersalny). Modelu uniwersalnego można użyć do obsługi wszystkich scenariuszy oraz do zastąpienia modeli do wyszukiwania i dyktowania oraz do konwersacji.

Jak przedstawiono w poniższym przykładzie, po określeniu podstawowego modelu językowego użyj menu rozwijanego **Language Data** (Dane językowe), aby wybrać zestaw danych języka, którego chcesz użyć na potrzeby dostosowania.

![Spróbuj](media/stt/speech-language-models-create2.png)

Podobnie jak podczas tworzenia modelu akustycznego, możesz opcjonalnie wybrać wykonanie testowania offline nowego modelu po zakończeniu przetwarzania. Oceny modelu wymagają akustycznego zestawu danych.

Aby wykonać testowanie offline modelu językowego, zaznacz pole wyboru obok opcji **Offline Testing** (Testowanie offline). Następnie wybierz model akustyczny z menu rozwijanego. Jeśli nie utworzono żadnego niestandardowego modelu akustycznego, podstawowe modele akustyczne firmy Microsoft będą jedynymi dostępnymi modelami w menu. Jeśli wybrano podstawowy model językowy do konwersacji, należy w tym miejscu użyć modelu akustycznego do konwersacji. Jeśli używany jest model językowy do wyszukiwania i dyktowania, należy wybrać model akustyczny do wyszukiwania i dyktowania.

Na koniec wybierz zestaw danych akustycznych, którego chcesz użyć do przeprowadzenia oceny.

Gdy wszystko będzie gotowe do rozpoczęcia przetwarzania, naciśnij przycisk **Create** (Utwórz). Zostanie wyświetlona tabela modeli językowych. W tabeli pojawi się nowy wpis odpowiadający temu modelowi. Stan odzwierciedla sytuację modelu, który będzie przechodził przez kilka stanów, w tym **Waiting** (Oczekiwanie), **Processing** (Przetwarzanie) i **Complete** (Ukończono).

Jeśli model znajduje się w stanie **Complete** (Ukończono), można go wdrożyć w punkcie końcowym. Wybranie pozycji **View Result** (Wyświetl wynik) spowoduje wyświetlenie wyników testowania offline, jeśli je wykonano.

Jeśli w pewnym momencie zechcesz zmienić nazwę lub opis modelu w polach **Name** (Nazwa) lub **Description** (Opis), możesz użyć linku **Edit** (Edytuj) w odpowiednim wierszu tabeli modeli językowych.

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję wersji próbnej usługi Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Jak rozpoznawać mowę w języku C#](quickstart-csharp-dotnet-windows.md)
- [Dane przykładowe Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
