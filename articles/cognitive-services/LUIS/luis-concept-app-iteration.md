---
title: Projekt aplikacji iteracyjny — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS uczy się najlepiej w cyklu iteracji zmian modelu, przykłady wypowiedź, publikowanie i zbieranie danych z zapytań punktu końcowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422592"
---
# <a name="iterative-app-design-for-luis"></a>Iteracyjne projektowanie aplikacji dla usługi LUIS

Aplikacja do rozumienia języka (LUIS) uczy się i wykonuje najbardziej efektywnie z iteracji. Oto typowy cykl iteracji:

* Tworzenie nowej wersji
* Edytuj schemat aplikacji usługi LUIS. Obejmuje to:
    * Intencje z przykładowymi wypowiedziami
    * Jednostki
    * Funkcje
* Szkolenie, testowanie i publikowanie
    * Test w punkcie końcowym przewidywania dla aktywnego uczenia się
* Zbieranie danych z zapytań o punkty końcowe

![Cykl tworzenia](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Tworzenie schematu usługi LUIS

Schemat aplikacji definiuje, o co prosi użytkownik _(intencja_ lub _intencja)_ i jakie części intencji zawierają szczegółowe informacje (nazywane _jednostkami),_ które są używane do określenia odpowiedzi. 

Schemat aplikacji musi być specyficzny dla domen aplikacji, aby określić słowa i frazy, które są istotne, a także określić typowe porządkowanie wyrazów. 

Przykładowe wypowiedzi reprezentują dane wejściowe użytkownika, takie jak rozpoznana mowa lub tekst, których aplikacja oczekuje w czasie wykonywania. 

Schemat wymaga intencji i _powinny mieć_ jednostki. 

### <a name="example-schema-of-intents"></a>Przykładowy schemat intencji

Najczęściej schemat jest schemat intencji zorganizowane z intencji. Ten typ schematu używa usługi LUIS do określenia intencji użytkownika. 

Typ schematu intencji może mieć jednostki, jeśli pomaga usługi LUIS określić intencji użytkownika. Na przykład jednostki wysyłki (jako deskryptor do intencji) pomaga usługi LUIS określić zamiar wysyłki. 

### <a name="example-schema-of-entities"></a>Przykładowy schemat jednostek

Schemat jednostki koncentruje się na jednostki, czyli dane, które są wyodrębniane z wypowiedzi użytkownika. Na przykład, jeśli użytkownik miał powiedzieć: "Chciałbym zamówić trzy pizze." Istnieją dwa podmioty, które zostaną wyodrębnione: _trzy_ i _pizze_. Są one wykorzystywane do realizacji intencji, która polegała na zleceniu. 

Dla schematu jednostki intencji wypowiedź jest mniej ważne dla aplikacji klienckiej. 

Wspólną metodą organizowania schematu jednostki jest dodanie wszystkich wypowiedzi przykład do **None** intencji. 

### <a name="example-of-a-mixed-schema"></a>Przykład schematu mieszanego

Najbardziej zaawansowany i dojrzały schemat jest schemat intencji z pełnym zakresem jednostek i funkcji. Ten schemat można rozpocząć jako schemat intencji lub jednostki i rozwijać się w celu uwzględnienia pojęć obu, jak aplikacja kliencka potrzebuje tych informacji. 

## <a name="add-example-utterances-to-intents"></a>Dodawanie przykładowych wypowiedzi do intencji

Usługa LUIS potrzebuje kilku przykładowych wypowiedzi w każdej **intencji.** Wypowiedzi przykładu potrzebują wystarczającej odmiany wyboru wyrazu i kolejności wyrazów, aby móc określić, który zamiar wypowiedź jest przeznaczona dla. 

> [!CAUTION]
> Nie należy dodawać wypowiedzi przykład zbiorczo. Zacznij od 15 do 30 konkretnych i różnych przykładów. 

Każdy przykład wypowiedź musi mieć wszelkie **wymagane dane do wyodrębnienia** zaprojektowane i oznaczone za pomocą **jednostek.** 

|Kluczowy element|Przeznaczenie|
|--|--|
|Intencja|**Klasyfikuj** wypowiedzi użytkownika w jednej intencji lub akcji. Przykłady `BookFlight` obejmują `GetWeather`i .|
|Jednostka|**Wyodrębnij** dane z wypowiedź wymagane do ukończenia intencji. Przykłady obejmują datę i godzinę podróży oraz lokalizację.|

Aplikacja usługi LUIS można zaprojektować, aby ignorować wypowiedzi, które nie są istotne dla domeny aplikacji, przypisując wypowiedź do **intencji Brak.**

## <a name="test-and-train-your-app"></a>Testowanie i szkolenie aplikacji

Po 15 do 30 różnych wypowiedzi przykład w każdej intencji, z wymaganych jednostek oznaczonych etykietą, należy przetestować i [trenować](luis-how-to-train.md) aplikacji usługi LUIS. 

## <a name="publish-to-a-prediction-endpoint"></a>Publikowanie w punkcie końcowym prognozowania

Aplikacja usługi LUIS musi zostać opublikowana, aby była dostępna w [regionach punktu końcowego prognozowania](luis-reference-regions.md)listy.

## <a name="test-your-published-app"></a>Testowanie opublikowanej aplikacji

Opublikowaną aplikację usługi LUIS można przetestować z punktu końcowego prognozowania PROTOKOŁU HTTPS. Testowanie z punktu końcowego przewidywania umożliwia usługi LUIS wybrać wszelkie wypowiedzi z niskim zaufaniem do [przeglądu.](luis-how-to-review-endpoint-utterances.md)  

## <a name="create-a-new-version-for-each-cycle"></a>Tworzenie nowej wersji dla każdego cyklu

Każda wersja jest migawką w czasie aplikacji usługi LUIS. Przed wprowadzeniem zmian w aplikacji należy utworzyć nową wersję. Łatwiej jest wrócić do starszej wersji niż próbować usunąć intencje i wypowiedzi do poprzedniego stanu.

Identyfikator wersji składa się ze znaków, cyfr lub '.' i nie może być dłuższy niż 10 znaków.

Wersja początkowa (0.1) jest domyślną wersją aktywną. 

### <a name="begin-by-cloning-an-existing-version"></a>Rozpocznij od sklonowania istniejącej wersji

Sklonuj istniejącą wersję, aby była używana jako punkt wyjścia dla każdej nowej wersji. Po sklonowanie wersji nowa wersja staje się **wersją aktywną.** 

### <a name="publishing-slots"></a>Miejsca do publikowania

Można publikować na etapie i/lub w szczelinach produkcyjnych. Każde gniazdo może mieć inną wersję lub tę samą wersję. Jest to przydatne do sprawdzania zmian przed opublikowaniem w produkcji, która jest dostępna dla botów lub innych aplikacji wywołujących usługę LUIS. 

Wyszkolone wersje nie są automatycznie dostępne w [punkcie końcowym](luis-glossary.md#endpoint)aplikacji usługi LUIS. Należy [opublikować](luis-how-to-publish-app.md) lub ponownie opublikować wersję, aby była dostępna w punkcie końcowym aplikacji usługi LUIS. Można opublikować **w przemieszczania** i **produkcji,** co daje dwie wersje aplikacji dostępne w punkcie końcowym. Jeśli więcej wersji aplikacji muszą być dostępne w punkcie końcowym, należy wyeksportować wersję i ponownie zaimportować ją do nowej aplikacji. Nowa aplikacja ma inny identyfikator aplikacji.

### <a name="import-and-export-a-version"></a>Importowanie i eksportowanie wersji

Wersję można zaimportować na poziomie aplikacji. Ta wersja staje się aktywną wersją i `versionId` używa identyfikatora wersji we właściwości pliku aplikacji. Można również zaimportować do istniejącej aplikacji, na poziomie wersji. Nowa wersja staje się aktywną wersją. 

Wersję można wyeksportować również na poziomie aplikacji lub wersji. Jedyną różnicą jest to, że wersja eksportowana na poziomie aplikacji jest aktualnie aktywną wersją, podczas gdy na poziomie wersji możesz wybrać dowolną wersję do wyeksportowania na stronie **[Ustawienia.](luis-how-to-manage-versions.md)** 

Wyeksportowany plik **nie** zawiera:

* Informacje o maszynach, ponieważ aplikacja jest przeszkolona po zaimportowaniu
* Informacje o współautorze

Aby uzyskać zapas kopii zapasowej schematu aplikacji usługi LUIS, wyeksportuj wersję z [portalu usługi LUIS](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Zarządzanie zmianami współautorów za pomocą wersji i współautorów

Usługa LUIS używa koncepcji współautorów aplikacji, zapewniając uprawnienia na poziomie zasobów platformy Azure. Połącz tę koncepcję z przechowywaniem wersji, aby zapewnić ukierunkowaną współpracę. 

Aby zarządzać zmianami współautorów w aplikacji, należy korzystać z następujących technik.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Zarządzanie wieloma wersjami w tej samej aplikacji

Rozpocznij od [klonowania](luis-how-to-manage-versions.md#clone-a-version) z wersji podstawowej dla każdego autora. 

Każdy autor wprowadza zmiany we własnej wersji aplikacji. Gdy autor jest zadowolony z modelu, wyeksportuj nowe wersje do plików JSON.  

Eksportowane aplikacje, pliki .json lub .lu, można porównać pod kątem zmian. Połącz pliki, aby utworzyć pojedynczy plik nowej wersji. Zmień `versionId` właściwość, aby oznaczać nową scaloną wersję. Zaimportuj tę wersję do oryginalnej aplikacji. 

Ta metoda umożliwia jedną aktywną wersję, wersję jednoscełową i jedną opublikowaną wersję. Wyniki aktywnej wersji można porównać z opublikowaną wersją (etapową lub produkcyjną) w [interaktywnym okienku testowym](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Zarządzanie wieloma wersjami jako aplikacjami

[Wyeksportuj](luis-how-to-manage-versions.md#export-version) wersję podstawową. Każdy autor importuje wersję. Osoba importu, która importuje aplikację jest właścicielem wersji. Po zakończeniu modyfikowania aplikacji wyeksportuj wersję. 

Eksportowane aplikacje to pliki w formacie JSON, które można porównać z bazowym eksportem zmian. Połącz pliki, aby utworzyć pojedynczy plik JSON nowej wersji. Zmień **właściwość versionId** w JSON, aby oznaczać nową scaloną wersję. Zaimportuj tę wersję do oryginalnej aplikacji.

Dowiedz się więcej o tworzeniu informacji od [współpracowników.](luis-how-to-collaborate.md)

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Przejrzyj wypowiedzi punktów końcowych, aby rozpocząć nowy cykl iteracji

Po zakończeniu z cyklu iteracji, można powtórzyć proces. Zacznij od [przeglądania wypowiedzi punktu końcowego przewidywania](luis-how-to-review-endpoint-utterances.md) luis oznaczone niskim poziomem zaufania. Sprawdź te wypowiedzi dla poprawnych przewidywanych intencji i poprawne i kompletne jednostki wyodrębnione. Po przejrzeniu i zaakceptowaniu zmian lista recenzji powinna być pusta.  

## <a name="next-steps"></a>Następne kroki

Poznaj pojęcia dotyczące [współpracy](luis-concept-keys.md).
