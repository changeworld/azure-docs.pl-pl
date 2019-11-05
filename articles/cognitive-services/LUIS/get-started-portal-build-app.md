---
title: 'Szybki Start: Tworzenie nowej aplikacji w portalu LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz nową aplikację w portalu LUIS. Utwórz podstawowe elementy aplikacji, intencje i jednostki. Przetestuj aplikację, dostarczając przykładowej wypowiedź użytkownika w panelu test interaktywny, aby uzyskać przewidywany cel. Tworzenie aplikacji jest bezpłatne; nie wymaga subskrypcji platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 6888c53122e649d6a0e91f8ece30101f051c08e8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488782"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Szybki Start: Tworzenie nowej aplikacji w portalu LUIS


[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

W tym przewodniku szybki start utworzysz nową aplikację w [portalu Luis](https://www.luis.ai). Najpierw utworzysz podstawowe elementy aplikacji, **intencje**i **jednostki**. Następnie przetestuj aplikację, dostarczając przykładowej wypowiedź użytkownika w panelu testów interakcyjnych, aby uzyskać przewidywany cel.

Tworzenie aplikacji jest bezpłatne i nie wymaga subskrypcji platformy Azure. Gdy wszystko będzie gotowe do wdrożenia aplikacji, zapoznaj się z [przewodnikiem Szybki Start, aby wdrożyć aplikację](get-started-portal-deploy-app.md). Przedstawiono w nim sposób tworzenia zasobu usługi poznawczej platformy Azure i przypisywania go do aplikacji.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Tworzenie aplikacji

1. Wybierz pozycję **+ Utwórz** na pasku narzędzi kontekstu.

   [![utworzyć nową aplikację w portalu LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. W oknie podręcznym Skonfiguruj aplikację przy użyciu następujących ustawień, a następnie wybierz pozycję **gotowe**.

   |Nazwa ustawienia| Wartość | Przeznaczenie|
   |--|--|--|
   |Nazwa|`myEnglishApp`|Unikatowa nazwa aplikacji LUIS<br>Wymagane|
   |Kultura|**Angielski**|Język wyrażenia długości od użytkowników, **en-us**<br>Wymagane|
   |Opis|`App made with LUIS Portal`|Opis aplikacji<br>Obowiązkowe|
   | | | |

   ![Wprowadź nowe ustawienia aplikacji](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Tworzenie intencji

Po utworzeniu aplikacji LUIS należy utworzyć intencje. Intencje są sposobem na klasyfikowanie tekstu od użytkowników. Na przykład aplikacja kadrs może mieć dwie funkcje. Aby ułatwić użytkownikom:

 1. Znajdź i Zastosuj do zadań
 1. Znajdowanie formularzy do zastosowania w przypadku zadań

Dwie różne _intencje_ dotyczące aplikacji są wyrównane do następujących intencji:

|Intencja|Przykładowy tekst od użytkownika<br>znane jako _wypowiedź_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Aby utworzyć intencje, wykonaj następujące czynności:

1. Po utworzeniu aplikacji znajduje się na stronie **intencje** w sekcji **kompilacja** . Wybierz pozycję**Create new intent** (Utwórz nową intencję).

   [![wybierz przycisk Utwórz nowy cel](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Wprowadź nazwę zamierzenia, `FindForm`a następnie wybierz pozycję **gotowe**.

   ![Wprowadź nazwę zamiaru FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Dodawanie przykładu wypowiedź

Po utworzeniu zamiarów należy dodać przykład wyrażenia długości. Przykład wyrażenia długości jest tekstem, który użytkownik wprowadza w programie Chat bot lub innej aplikacji klienckiej. Umożliwiają one mapowanie zamiaru tekstu użytkownika na LUIS zamiar.

Na potrzeby tego przykładowej `FindForm` aplikacji Przykładowa wyrażenia długości będzie zawierać numer formularza. Aplikacja kliencka wymaga, aby numer formularza spełniał żądanie użytkownika, więc należy go uwzględnić w wypowiedź.

[![wprowadzić przykład wyrażenia długości dla zamiaru FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Dodaj następujące 15 przykładowych wyrażenia długości do zamiaru `FindForm`.

|#|Przykładowe wypowiedzi|
|--|--|
|1|Szukasz elementu HRF-123456|
|2|Gdzie jest HRF-234591?|
|3|HRF-345623, gdzie to|
|4|Czy jest możliwe wysłanie mi HRF-345794|
|5|Czy potrzebuję HRF-234695 do zastosowania w ramach zadania wewnętrznego?|
|6|Czy mój Menedżer musi wiedzieć, że stosujem się do zadania z HRF-234091|
|7|Gdzie mogę wysłać HRF-234918? Czy otrzymuję odpowiedź na wiadomość e-mail, która została odebrana?|
|8|HRF-234555|
|9|Kiedy został HRF-234987 zaktualizowany?|
|10|Do zastosowania w przypadku stanowisk inżynieryjnych należy użyć formy HRF-876345|
|11|Czy została przesłana Nowa wersja HRF-765234 dla mojego otwartego żądania?|
|12|Czy do zadań międzynarodowych jest używany HRF-234234?|
|13|HRF-234598 błąd pisowni|
|14|HRF-234567 można edytować w celu uzyskania nowych wymagań|
|15|HRF-123456, HRF-123123, HRF-234567|

Zgodnie z projektem, te przykładowe wyrażenia długości różnią się w następujący sposób:

* Długość wypowiedź
* interpunkcyjny
* wybór wyrazów
* intensywność czasownika (is, jest, będzie)
* kolejność wyrazów



## <a name="create-a-regular-expression-entity"></a>Tworzenie jednostki wyrażenia regularnego

Aby zwrócić numer formularza w odpowiedzi przewidywania środowiska uruchomieniowego, formularz musi być oznaczony jako jednostka. Ponieważ tekst z numerem formularza jest wysoce strukturalny, można oznaczyć go przy użyciu jednostki wyrażenia regularnego. Utwórz jednostkę, wykonując następujące czynności:

1. Wybierz pozycję **jednostki** z menu po lewej stronie.

1. Na stronie **jednostki** wybierz pozycję **Utwórz nową jednostkę** .

1. Wprowadź nazwę `Human Resources Form Number`, wybierz typ podmiotu **wyrażenia regularnego** , a następnie wprowadź wyrażenie regularne, `hrf-[0-9]{6}`. Ten wpis Dopasowuje znaki literału, `hrf-`i zezwala na dokładnie 6 cyfr.

   ![Wprowadź informacje o jednostce dla jednostki wyrażenia regularnego](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Wybierz pozycję **Done** (Gotowe).

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None

Opcja **Brak** jest zamiarem rezerwowym i nie powinna być pusta. Ta metoda powinna zawierać jeden wypowiedź na każdy 10 przykład wyrażenia długości, który został dodany do innych intencji aplikacji.

Przykładowa metoda wyrażenia długości **nie** powinna być poza domeną aplikacji klienckiej.

1. Wybierz pozycję **intencje** w menu po lewej stronie, a następnie wybierz pozycję **Brak** z listy intencje.

1. Dodaj następujący przykład wyrażenia długości do celu:

   |Brak przykładu zamiaru wyrażenia długości|
   |--|
   |Barking dogs are annoying (Szczekające psy są irytujące)|
   |Order a pizza for me (Zamów dla mnie pizzę)|
   |Penguins in the ocean (Pingwiny w oceanie)|

   W przypadku tej aplikacji kadrowej te przykładowe wyrażenia długości znajdują się poza domeną. Jeśli domena zasobów ludzkich obejmuje zwierzęta, żywność lub ocean, należy użyć innego przykładu wyrażenia długości dla opcji **Brak** .

## <a name="train-the-app"></a>Uczenie aplikacji

W menu w prawym górnym rogu wybierz pozycję **uczenie** , aby zastosować zmiany wprowadzone w modelu zamierzenia do bieżącej wersji aplikacji.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Przyjrzyj się jednostce wyrażenia regularnego w przykładzie wyrażenia długości

1. Sprawdź, czy jednostka została znaleziona w **FindForm** zamiaru, wybierając pozycję **intencje** w menu po lewej stronie. Następnie wybierz pozycję **FindForm** zamierzenia.

   Jednostka jest oznaczona, gdzie pojawia się w przykładzie wyrażenia długości. Jeśli chcesz zobaczyć oryginalny tekst zamiast nazwy jednostki, przełącz **Widok jednostek** z paska narzędzi.

   [![wszystkie przykładowe wyrażenia długości oznaczone obiektami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testowanie nowej aplikacji za pomocą interaktywnego okienka testów

Użyj interaktywnego okienka **testowego** w portalu Luis, aby sprawdzić, czy jednostka została wyodrębniona z nowej wyrażenia długości, która nie była jeszcze widoczna dla aplikacji.

1. Wybierz pozycję **Testuj** w prawym górnym menu.

1. Dodaj nowy wypowiedź, a następnie naciśnij klawisz ENTER:

   ```Is there a form named hrf-234098```

   ![Test New wypowiedź w okienku testów](./media/get-started-portal-build-app/test-new-utterance.png)

   Najbardziej przewidywany cel jest prawidłowo **FindForm** z ponad 90% pewnością (0,977). Jednostka **numeru formularza zasobów ludzkich** jest wyodrębniana z wartością hrf-234098.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z tego przewodnika Szybki Start i przechodzenia do następnego przewodnika Szybki Start wybierz pozycję **Moje aplikacje** w górnym menu nawigacji. Następnie zaznacz pole wyboru po lewej stronie z listy i wybierz pozycję **Usuń** z paska narzędzi kontekstu powyżej listy.

[![usunąć aplikacji z listy Moje aplikacje](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [2. Wdróż aplikację](get-started-portal-deploy-app.md)
