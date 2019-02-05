---
title: Samouczek dotyczący tworzenia modelu językowego za pomocą usługi Custom Speech Service — Microsoft Cognitive Services | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć model językowy za pomocą usługi Custom Speech Service w usługach Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 0798fb16954d2d0b5e0d614d619cd772670d30c3
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218091"
---
# <a name="tutorial-create-a-custom-language-model"></a>Samouczek: Tworzenie niestandardowego modelu językowego

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

W ramach tego samouczka utworzysz niestandardowy model językowy dla zapytań tekstowych lub wypowiedzi, których w formie tekstu mówionego lub pisanego spodziewasz się od użytkowników w aplikacji. Następnie możesz użyć tego niestandardowego modelu językowego w połączeniu z istniejącymi najnowocześniejszymi modelami mowy firmy Microsoft, aby dodać interakcję głosową do swojej aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Przygotowywanie danych
> * Importowanie zestawu danych językowych
> * Tworzenie niestandardowego modelu językowego

Jeśli nie masz konta usług Cognitive Services, przed rozpoczęciem utwórz [bezpłatne konto](https://cris.ai).

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że Twoje konto usług Cognitive Services jest połączone z subskrypcją, otwierając stronę [Subskrypcje usługi Cognitive Services](https://cris.ai/Subscriptions).

Jeśli żadne subskrypcje nie są wyświetlane, możesz kliknąć przycisk **Uzyskaj bezpłatną subskrypcję**, aby usługi Cognitive Services utworzyły konto dla Ciebie. Możesz też połączyć się z subskrypcją usługi wyszukiwania niestandardowego utworzoną w witrynie Azure Portal, klikając przycisk **Połącz z istniejącą subskrypcją**.

Aby uzyskać informacje na temat tworzenia subskrypcji usługi wyszukiwania niestandardowego w witrynie Azure Portal, zobacz [Create a Cognitive Services APIs account in the Azure portal](../../cognitive-services-apis-create-account.md) (Tworzenie konta interfejsów API usług Cognitive Services w witrynie Azure Portal).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Aby utworzyć niestandardowy model językowy dla aplikacji, musisz podać listę przykładowych wypowiedzi do systemu, na przykład:

*   „Pacjent miał pokrzywkę przez ostatni tydzień”.
*   „Pacjent miał dobrze wygojoną bliznę po operacji przepukliny”.

Zdania nie muszą być pełnymi zdaniami ani być poprawne gramatyczne, lecz powinny dokładnie odzwierciedlać oczekiwane dane głosowe, które system może napotkać we wdrożeniu. Te przykłady powinny odzwierciedlać styl i zawartość zadania, które użytkownicy będą wykonywać w aplikacji.

Dane modelu językowego powinny być zapisane w zwykłym pliku tekstowym przy użyciu kodowania US-ASCII lub UTF-8, w zależności od ustawień regionalnych. W przypadku języka en-US obsługiwane są oba kodowania. W przypadku języka zh-CN obsługiwane jest tylko kodowanie UTF-8 (znacznik kolejności bajtów jest opcjonalny). Plik tekstowy powinien zawierać jeden przykład (zdanie, wypowiedź lub zapytanie) na wiersz.

Jeśli chcesz, aby niektóre zdania miały większą wagę (ważność), możesz dodać je kilka razy do danych. Dobra liczba powtórzeń to 10–100. Jeśli znormalizujesz ją jako 100, możesz łatwo określać wagę zdania względem tej liczby.

Główne wymagania dotyczące danych językowych przedstawiono w poniższej tabeli.

| Właściwość | Wartość |
|----------|-------|
| Kodowanie tekstu | pl-PL: US-ACSII lub UTF-8 lub zh-CN: UTF-8|
| Liczba wypowiedzi w wierszu | 1 |
| Maksymalny rozmiar pliku | 200 MB |
| Uwagi | unikaj powtarzania znaków więcej niż 4 razy, na przykład „aaaaa”|
| Uwagi | bez znaków specjalnych, takich jak „\t” lub innych znaków UTF-8 powyżej U+00A1 z [tabeli znaków Unicode](http://www.utf8-chartable.de/)|
| Uwagi | identyfikatory URI również zostaną odrzucone, ponieważ nie istnieje unikatowy sposób wymowy identyfikatora URI|

Po zaimportowania tekstu jest on znormalizowany jako tekst, aby umożliwić jego przetwarzanie przez system. Istnieją jednak pewne istotne normalizacje, które muszą być wykonane przez użytkownika _przed_ przekazaniem danych. Zobacz [Wytyczne dotyczące transkrypcji](cognitive-services-custom-speech-transcription-guidelines.md), aby określić odpowiedni język podczas przygotowywania danych językowych.

## <a name="import-the-language-data-set"></a>Importowanie zestawu danych językowych

Kliknij przycisk Import (Importuj) w wierszu Acoustic Datasets (Akustyczne zestawy danych). Witryna wyświetli stronę pobierania nowego zestawu danych.

Gdy wszystko będzie gotowe do zaimportowania zestawu danych językowych, zaloguj się do [portalu usługi Custom Speech Service](https://cris.ai).  Następnie kliknij menu rozwijane Custom Speech na wstążce u góry i wybierz pozycję Adaptation Data (Dane adaptacyjne). Jeśli po raz pierwszy przekazujesz dane do usługi Custom Speech Service, zostanie wyświetlona pusta tabela o nazwie Datasets (Zestawy danych).

Aby zaimportować nowy zestaw danych, kliknij przycisk Import (Importuj) w wierszu Language Datasets (Zestawy danych językowych). Witryna wyświetli stronę pobierania zestawu danych. W polach Name i Description wprowadź nazwę i opis, które ułatwią identyfikację zestawu danych w przyszłości. Następnie kliknij przycisk Choose File (Wybierz plik) i wskaż plik tekstowy danych językowych. Następnie kliknij przycisk Import (Importuj), aby przekazać zestaw danych. W zależności od rozmiaru zestawu danych może to potrwać kilka minut.

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

Po zakończeniu importowania nastąpi powrót do tabeli danych językowych, w której będzie wyświetlany wpis odpowiadający przekazanemu zestawowi danych językowych. Zwróć uwagę, że przypisano mu unikatowy identyfikator (identyfikator GUID). Dane będą miały również stan, który odzwierciedla ich bieżący stan. Stan ma wartość Waiting (Oczekiwanie) podczas umieszczania danych w kolejce do przetwarzania, Processing (Przetwarzanie) podczas ich weryfikacji i Complete (Ukończono), gdy są gotowe do użycia. Podczas weryfikacji danych wykonywany jest szereg kontroli tekstu w pliku i normalizacja tekstu danych.

Kiedy stan ma wartość Complete (Ukończono), możesz kliknąć pozycję View Report (Wyświetl raport), aby wyświetlić raport weryfikacji danych językowych. Wyświetlana będzie liczba wypowiedzi, których weryfikacja zakończyła się pomyślnie i nie powiodła się. W poniższym przykładzie weryfikacja dwóch przykładów nie powiodła się z powodu nieprawidłowych znaków (w tym zestawie danych pierwszy przykład zawierał dwa emotikony, a drugi kilka znaków spoza zestawu znaków drukowalnych ASCII).

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

Gdy stan zestawu danych językowych ma wartość Complete (Ukończono), można użyć tego zestawu do utworzenia niestandardowego modelu językowego.

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Tworzenie niestandardowego modelu językowego

Kiedy dane językowe są gotowe, kliknij pozycję Language Models (Modele językowe) w menu rozwijanym Menu, aby rozpocząć proces tworzenia niestandardowego modelu językowego. Ta strona zawiera tabelę o nazwie „Language Models” (Modele językowe) z bieżącymi niestandardowymi modelami językowymi. Jeśli jeszcze nie utworzono żadnych niestandardowych modeli językowych, tabela jest pusta. Bieżące ustawienia regionalne są odzwierciedlone w tytule tabeli. Jeśli chcesz utworzyć model językowy dla innego języka, kliknij pozycję Change Locale (Zmień ustawienia regionalne). Dodatkowe informacje na temat obsługiwanych języków można znaleźć w sekcji dotyczącej sposobu [zmiany ustawień regionalnych](cognitive-services-custom-speech-change-locale.md). Aby utworzyć nowy model, kliknij link Create New (Utwórz nowy) poniżej tytułu tabeli.

Na stronie Create Language Model (Tworzenie modelu językowego) wprowadź nazwę i opis modelu w polach Name i Description, aby łatwiej śledzić ważne informacje dotyczące tego modelu, na przykład używany zestaw danych. Następnie z menu rozwijanego wybierz pozycję Base Language Model (Podstawowy model językowy). Ten model będzie punktem wyjścia dla Twojego dostosowania. Dostępne są dwa podstawowe modele językowe do wyboru. _Microsoft Search and Dictation LM_ (Model językowy dla wyszukiwania i dyktowania firmy Microsoft) jest odpowiedni dla mowy kierowanej do aplikacji, na przykład poleceń, zapytań wyszukiwania lub dyktowania. _Microsoft Conversational LM_ (Model językowy dla konwersacji firmy Microsoft) jest przeznaczony do rozpoznawania mowy w stylu konwersacyjnym. Ten typ mowy jest zazwyczaj kierowany do innej osoby i występuje w centrach telefonicznej obsługi klienta lub na konferencjach.

Po określeniu podstawowego modelu językowego wybierz zestaw danych językowych, którego chcesz użyć na potrzeby dostosowania, korzystając z menu rozwijanego Language Data (Dane językowe).

![try](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Podobnie jak w przypadku tworzenia modelu akustycznego, możesz opcjonalnie wybrać wykonanie testowania offline nowego modelu po zakończeniu przetwarzania. Ponieważ jest to ocena wydajności zamiany mowy na tekst, testowanie offline wymaga akustycznego zestawu danych.

Aby wykonać testowanie offline modelu językowego, zaznacz pole wyboru obok opcji Offline Testing (Testowanie offline). Następnie wybierz model akustyczny z menu rozwijanego. Jeśli nie utworzono żadnego niestandardowego modelu akustycznego, podstawowe modele akustyczne firmy Microsoft będą jedynymi dostępnymi w menu. Jeśli wybrano podstawowy model językowy dla konwersacji, należy w tym miejscu użyć modelu akustycznego dla konwersacji. Jeśli używany jest model językowy dla wyszukiwania i dyktowania, należy wybrać model akustyczny dla wyszukiwania i dyktowania.

Na koniec wybierz zestaw danych akustycznych, którego chcesz użyć do przeprowadzenia oceny.

Gdy wszystko będzie gotowe do rozpoczęcia przetwarzania, naciśnij przycisk Create (Utwórz). Spowoduje to powrót do tabeli modeli językowych. W tabeli pojawi się nowy wpis odpowiadający temu modelowi. Stan odzwierciedla stan modelu, który będzie przechodził przez kilka stanów, w tym Waiting (Oczekiwanie), Processing (Przetwarzanie) i Complete (Ukończono).

Jeśli model osiągnął stan Complete (Ukończono), można go wdrożyć w punkcie końcowym. Kliknięcie pozycji View Result (Wyświetl wynik) spowoduje wyświetlenie wyników testowania offline, jeśli je wykonano.

Jeśli w pewnym momencie zechcesz zmienić nazwę lub opis modelu, możesz użyć linku Edit (Edytuj) w odpowiednim wierszu tabeli modeli językowych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony niestandardowy model językowy do użycia z tekstem. Aby utworzyć niestandardowy model akustyczny do użytku z plikami audio i transkrypcjami, przejdź do samouczka dotyczącego tworzenia modelu akustycznego.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowego modelu akustycznego](cognitive-services-custom-speech-create-acoustic-model.md)
