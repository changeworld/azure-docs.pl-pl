---
title: 'Szybki start: Utwórz nową aplikację za pomocą portalu usługi LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: W tym przewodniku Szybki Start utworzysz nową aplikację w portalu usługi LUIS. Utwórz podstawowe części aplikacji, intencje i podmioty, a następnie przetestować, zapewniając przykład wypowiedź użytkownika, w panelu interaktywne testu do poznania intencji przewidywane. Tworzenie aplikacji jest bezpłatna; nie wymaga subskrypcji platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783172"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Szybki start: Utwórz nową aplikację w portalu usługi LUIS

W tym przewodniku Szybki Start utworzysz nową aplikację w [portal usługi LUIS](https://www.luis.ai). Utwórz podstawowe części aplikacji, **intencji** i **jednostek**, następnie przetestować, zapewniając przykład wypowiedź użytkownika, w panelu interaktywne testu do poznania intencji przewidywane.

Tworzenie aplikacji jest bezpłatna; nie wymaga subskrypcji platformy Azure. Gdy jesteś gotowy do wdrożenia aplikacji, a następnie utwórz zasób usługi Azure Cognitive i przypisać je do aplikacji. Ten proces wdrażania [kolejnego przewodnika Szybki Start](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Tworzenie aplikacji 

1. Otwórz [portal usługi LUIS](https://www.luis.ai) w przeglądarce i zaloguj się. Jeśli to Twoja pierwsza logowania, należy utworzyć bezpłatne konto użytkownika portalu usługi LUIS.

1. Wybierz **Utwórz nową aplikację** z paska narzędzi kontekstu.

    [![Utwórz nową aplikację w portalu usługi LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. W oknie podręcznym, należy skonfigurować aplikację z następującymi ustawieniami, a następnie wybierz pozycję **gotowe**.

    |Nazwa ustawienia| Wartość | Przeznaczenie|
    |--|--|--|
    |Name (Nazwa)|`myEnglishApp`|Unikatowa nazwa aplikacji usługi LUIS<br>wymagane|
    |Kultura|**Angielski**|Język wypowiedzi użytkowników, **en-us**<br>wymagane|
    |Opis|`App made with LUIS Portal`|Opis aplikacji<br>opcjonalne|

    ![Wprowadź nowe ustawienia aplikacji](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Tworzenie intencji 

Po utworzeniu aplikacji następnym krokiem jest utworzenie intencji. Intencji służą do kategoryzowania tekst od użytkowników. Jeśli masz aplikację zarządzania zasobami ludzkimi, która ma dwie funkcje: najpierw ułatwiają znajdowanie i zastosowanie w przypadku zadań, a następnie znajdź formy była stosowana do miejsca pracy, te dwa różne _zamiarach_ wyrównane do następujących opcji:

|Intencja|Przykładowy tekst od użytkownika<br>znane jako _wypowiedź_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Po utworzeniu aplikacji znajdują się na **intencji** strony **kompilacji** sekcji. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

    [![Wybierz przycisk Tworzenie nowego elementu intent](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Wprowadź nazwę elementu intent `FindForm` i wybierz **gotowe**.

    ![Wprowadź nazwę elementu intent FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Dodaj przykład wypowiedź 

Po utworzeniu intencji, następnym krokiem jest dodanie wypowiedzi przykład. Jest to tekst, wprowadzony przez użytkownika w czatbot lub innej aplikacji klienta, który mapuje zamiar użytkownika tekstu na intencje usługi LUIS. 

Dla tej przykładowej aplikacji `FindForm` intencji, przykład wypowiedzi będzie zawierać numer formularza, który jest ważne informacje wypowiedź czy aplikacja kliencka potrzebuje w celu fullfil żądanie użytkownika. 

Dodaj następujące wypowiedzi piętnastu przykład do `FindForm` intencji. 

|#|Przykładowe wypowiedzi|
|--|--|
|1|Szukasz hrf 123456|
|2|Gdzie jest formularz zasobów ludzkich w 234591 hrf?|
|3|hrf 345623, gdzie jest ona|
|4|Czy jest możliwe, Wyślij do mnie hrf 345794|
|5|Czy muszę mieć hrf-234695 do zastosowania do wewnętrznego zadania?|
|6|Czy Mój menedżer musi wiedzieć, że jestem I stosowania dla zadania o hrf 234091|
|7|Gdzie wysłać hrf 234918? Uzyskać odpowiedź pocztą e-mail, który został odebrany?|
|8|hrf-234555|
|9|Gdy zaktualizowano hrf 234987?|
|10|Używać formularza hrf-876345 do zastosowania dla inżynierów pozycji|
|11|Została nowa wersja 765234 hrf, przesłane do mojej Otwórz wymagane?|
|12|Używać hrf 234234 międzynarodowych zadań?|
|13|błąd pisowni hrf 234598|
|14|będą edytowane hrf 234567 dla nowych wymagań|
|15|hrf 123456, hrf 123123 hrf 234567|

Te przykładowe wypowiedzi różnią się celowo, w następujący sposób:

* Długość wypowiedź
* Znaki interpunkcyjne
* Wybór programu Word
* czasu teraźniejszego zlecenie (jest, został, będzie)
* kolejność słów

[![Podaj przykład wypowiedzi dla intencji FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Tworzenie jednostki wyrażeń regularnych 

Aby uzyskać numer formularza zwracany jako część odpowiedzi prognozowania środowiska uruchomieniowego, formularza ma być oznaczona jako jednostka. Ponieważ mają zaawansowaną strukturę tekst w postaci liczb, może być oznaczony za pomocą jednostek wyrażenia regularnego. Tworzenie jednostki wykonując następujące kroki. 

1. Wybierz **jednostek** z menu nawigacji po lewej stronie. 

1. Wybierz **Utwórz nową jednostkę** na stronie jednostki.

1. Wprowadź nazwę `Human Resources Form Number`, wybierz opcję **wyrażenia regularnego** jednostki wpisz, a następnie wprowadź wyrażenie regularne `hrf-[0-9]{6}`. To dopasowuje znaki literału `hrf-`i umożliwia dokładnie 6 cyfr. 

    ![Wprowadź informacje jednostki dla obiektu wyrażenia regularnego](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Wybierz pozycję **Done** (Gotowe). 

## <a name="add-example-utterances-to-none-intent"></a>Dodawanie wypowiedzi przykładzie Brak elementu intent

**Brak** jest rezerwowego intencji i nie powinny być puste. Celem tego powinny mieć 1 wypowiedź co 10 w pozostałej części intencji aplikacji. 

**Brak** wypowiedzi przykład jego przeznaczenie powinna znajdować się poza domenę aplikacji klienta. 

1. Wybierz **intencji** menu po lewej stronie, następnie wybierz pozycję **Brak** z listy opcji.

1. Dodaj następujące wypowiedzi przykład zamiar:

    |Brak elementu intent przykład wypowiedzi|
    |--|
    |Barking dogs are annoying (Szczekające psy są irytujące)|
    |Order a pizza for me (Zamów dla mnie pizzę)|
    |Penguins in the ocean (Pingwiny w oceanie)|

    Ta aplikacja zarządzania zasobami ludzkimi są wypowiedzi te przykładowe spoza domeny. Jeśli domeny zarządzania zasobami ludzkimi tym zwierząt, żywności lub oceanu, następnie te wypowiedzi przykład nie należy używać dla **Brak** intencji. 

## <a name="train-the-app"></a>Uczenie aplikacji

W prawym górnym rogu nawigacji wybierz pozycję **Train** stosowanie intencji i jednostki modelu zmienia się na bieżącą wersję aplikacji. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Przyjrzyj się jednostki wyrażeń regularnych w przykładzie wypowiedzi

1. Sprawdź jednostki znajduje się w **FindForm** intencji, wybierając **intencji** menu po lewej stronie, następnie wybierz pozycję **FindForm** intencji. 

    Jednostka jest oznaczony jako położenia wypowiedzi przykładu. Jeśli chcesz wyświetlić oryginalny tekst, a nie nazwa jednostki Przełącz **widoku jednostek** na pasku narzędzi.

    [![Wszystkie wypowiedzi przykład oznaczona za pomocą jednostek](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testowanie nowej aplikacji z okienka interaktywne testu

Użyj interakcyjnego **testu** okienku w portalu usługi LUIS do sprawdzania poprawności jednostki są wyodrębniane z wypowiedzi nowych aplikacji nie zostało jeszcze widoczne.

1. Wybierz **testu** z menu w prawym górnym rogu.

1. Dodaj nowe wypowiedź, a następnie naciśnij klawisz Enter:

    ```Is there a form named hrf-234098```

    ![Testowanie nowych wypowiedź w okienko testowania](./media/get-started-portal-build-app/test-new-utterance.png)

    U góry przewidzieć celem jest poprawnie **FindForm** z ponad 90% zaufania (0.977) i **numer formularza zarządzania zasobami ludzkimi** jednostki jest wyodrębniany z wartością hrf 234098. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli są wykonywane przy użyciu tego przewodnika Szybki Start, nie ma przejść do kolejnego przewodnika Szybki Start wybierz **Moje aplikacje** w górnym menu nawigacji. Następnie zaznacz pole wyboru po lewej stronie aplikacji z listy i wybierz **Usuń** z kontekstu paska narzędzi powyżej listy. 

[![Usunąć aplikację z listy aplikacji](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [2. Wdrażanie aplikacji](get-started-portal-deploy-app.md)