---
title: 'Szybki start: Utwórz nową aplikację w portalu usługi LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: W tym przewodniku Szybki Start utworzysz nową aplikację w portalu usługi LUIS. Utwórz podstawowe części aplikacji, intencje i podmioty. Testowanie aplikacji, zapewniając przykład wypowiedź użytkownika, w panelu interaktywne testu do poznania intencji przewidywane. Tworzenie aplikacji jest bezpłatna; nie wymaga subskrypcji platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 5ec38c510fedee7f489b3b100285eeb7c75f4561
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690970"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Szybki start: Utwórz nową aplikację w portalu usługi LUIS

W tym przewodniku Szybki Start tworzysz nową aplikację [portal usługi LUIS](https://www.luis.ai). Najpierw należy utworzyć podstawowe części aplikacji, **intencji**, i **jednostek**. Następnie przetestuj aplikację, zapewniając przykład wypowiedź użytkownika, w panelu interaktywne testu do poznania intencji przewidywane.

Tworzenie aplikacji jest bezpłatne i nie wymaga subskrypcji platformy Azure. Gdy wszystko będzie gotowe do wdrożenia aplikacji, zobacz [szybkiego startu, aby wdrożyć aplikację](get-started-portal-deploy-app.md). Prezentuje sposób tworzenia zasobów platformy Azure Cognitive Service i przypisać je do aplikacji.

## <a name="create-an-app"></a>Tworzenie aplikacji

1. Otwórz [portal usługi LUIS](https://www.luis.ai) w przeglądarce i zaloguj się. Jeśli logowanie po raz pierwszy, musisz utworzyć bezpłatne konto użytkownika portalu usługi LUIS.

1. Wybierz **Utwórz nową aplikację** z paska narzędzi kontekstu.

   [![Utwórz nową aplikację w portalu usługi LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. W oknie podręcznym, należy skonfigurować aplikację z następującymi ustawieniami, a następnie wybierz pozycję **gotowe**.

   |Nazwa ustawienia| Wartość | Przeznaczenie|
   |--|--|--|
   |Name (Nazwa)|`myEnglishApp`|Unikatowa nazwa aplikacji usługi LUIS<br>wymagane|
   |Kultura|**Angielski**|Język wypowiedzi użytkowników, **en-us**<br>wymagane|
   |Opis|`App made with LUIS Portal`|Opis aplikacji<br>opcjonalne|
   | | | |

   ![Wprowadź nowe ustawienia aplikacji](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Tworzenie intencji

Po utworzeniu aplikacji usługi LUIS, musisz utworzyć intencji. Intencji służą do kategoryzowania tekst od użytkowników. Na przykład aplikacja zarządzania zasobami ludzkimi może mieć dwie funkcje. Aby pomóc użytkownikom:

 1. Znajdź i Zastosuj dla zadań
 1. Znajdź formularzy do zastosowania w przypadku zadań

Aplikacja w dwóch różnych _zamiarach_ wyrównane do następujących opcji:

|Intencja|Przykładowy tekst od użytkownika<br>znane jako _wypowiedź_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Aby utworzyć intencji, wykonaj następujące czynności:

1. Po utworzeniu aplikacji znajdują się na **intencji** strony **kompilacji** sekcji. Wybierz pozycję **Create new intent** (Utwórz nową intencję).

   [![Wybierz przycisk Tworzenie nowego elementu intent](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Wprowadź nazwę elementu intent `FindForm`, a następnie wybierz pozycję **gotowe**.

   ![Wprowadź nazwę elementu intent FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Dodaj wypowiedź przykład

Możesz dodać wypowiedzi przykład po utworzeniu intencji. Przykład wypowiedzi to tekst, który użytkownik wprowadza w czatbot lub innych aplikacji. Mapowania ich zamiar użytkownika tekstu na intencje usługi LUIS.

Dla tej przykładowej aplikacji `FindForm` intencji, przykład wypowiedzi będzie zawierać numer formularza. Aplikacja kliencka musi numer formularza do spełnienia żądania użytkownika, więc jest ważne, które mają zostać objęte wypowiedź.

Dodaj następujące wypowiedzi przykład 15 do `FindForm` intencji.

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

Zgodnie z projektem te wypowiedzi przykładu różnią się w następujący sposób:

* Długość wypowiedź
* Znaki interpunkcyjne
* Wybór programu Word
* czasu teraźniejszego zlecenie (jest, został, będzie)
* kolejność słów

[![Podaj przykład wypowiedzi dla intencji FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-a-regular-expression-entity"></a>Tworzenie jednostki wyrażeń regularnych

Aby uzyskać numer formularza zwracany jako część odpowiedzi prognozowania środowiska uruchomieniowego, formularz musi być oznaczona jako jednostka. Ponieważ mają zaawansowaną strukturę tekst w postaci liczb, może być oznaczony za pomocą jednostek wyrażenia regularnego. Tworzenie jednostki wykonując następujące kroki:

1. Wybierz **jednostek** z menu po lewej stronie.

1. Wybierz **Utwórz nową jednostkę** na **jednostek** strony.

1. Wprowadź nazwę `Human Resources Form Number`, wybierz opcję **wyrażenia regularnego** jednostki wpisz, a następnie wprowadź wyrażenie regularne `hrf-[0-9]{6}`. Ten wpis dopasowuje znaki literału `hrf-`i umożliwia dokładnie 6 cyfr.

   ![Wprowadź informacje jednostki dla obiektu wyrażenia regularnego](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Wybierz pozycję **Done** (Gotowe).

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None

**Brak** jest rezerwowego intencji i nie powinny być puste. Celem tego może zawierać jeden wypowiedź dla każdego wypowiedzi przykład 10 zostały dodane do intencji aplikacji.

**Brak** wypowiedzi przykład jego przeznaczenie powinna znajdować się poza domenę aplikacji klienta.

1. Wybierz **intencji** z menu po lewej stronie, a następnie wybierz **Brak** z listy opcji.

1. Dodaj następujące wypowiedzi przykład zamiar:

   |Brak elementu intent przykład wypowiedzi|
   |--|
   |Barking dogs are annoying (Szczekające psy są irytujące)|
   |Order a pizza for me (Zamów dla mnie pizzę)|
   |Penguins in the ocean (Pingwiny w oceanie)|

   Ta aplikacja zarządzania zasobami ludzkimi są wypowiedzi te przykładowe spoza domeny. Jeśli domeny zarządzania zasobami ludzkimi obejmują zwierząt, żywności lub oceanu, a następnie należy użyć wypowiedzi w innym przykładzie dla **Brak** intencji.

## <a name="train-the-app"></a>Uczenie aplikacji

W menu w prawym górnym rogu wybierz **Train** stosowanie intencji i jednostki modelu zmienia się na bieżącą wersję aplikacji.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Przyjrzyj się jednostki wyrażeń regularnych w przykładzie wypowiedzi

1. Sprawdź jednostki znajduje się w **FindForm** intencji, wybierając **intencji** menu po lewej stronie. Następnie wybierz pozycję **FindForm** intencji.

   Jednostka jest oznaczony jako położenia wypowiedzi przykładu. Jeśli chcesz wyświetlić oryginalny tekst, a nie nazwa jednostki, Przełącz **widoku jednostek** na pasku narzędzi.

   [![Wszystkie wypowiedzi przykład oznaczona za pomocą jednostek](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testowanie nowej aplikacji z okienka interaktywne testu

Użyj interakcyjnego **testu** okienku w portalu usługi LUIS, aby sprawdzić, czy jednostki są wyodrębniane z wypowiedzi nowych aplikacji nie zostało jeszcze widoczne.

1. Wybierz **testu** z menu w prawym górnym rogu.

1. Dodaj nowe wypowiedź, a następnie naciśnij klawisz Enter:

   ```Is there a form named hrf-234098```

   ![Testowanie nowych wypowiedź w okienko testowania](./media/get-started-portal-build-app/test-new-utterance.png)

   U góry przewidzieć celem jest poprawnie **FindForm** z ponad 90% zaufania (0.977). **Numer formularza zarządzania zasobami ludzkimi** jednostki jest wyodrębniany z wartością hrf 234098.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy ukończysz pracę z tego przewodnika Szybki Start, a nie ma przejść do kolejnego przewodnika Szybki Start, wybierz **Moje aplikacje** w górnym menu nawigacji. Następnie zaznacz pole wyboru po lewej stronie, aplikacji z listy i wybierz **Usuń** z kontekstu paska narzędzi powyżej listy.

[![Usunąć aplikację z listy aplikacji](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [2. Wdrażanie aplikacji](get-started-portal-deploy-app.md)
