---
title: Samouczek tworzenia aplikacji LUIS do wyodrębniania danych — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji i jednostki prostej do danych nauczonych maszynowo.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265364"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Samouczek: tworzenie aplikacji używającej jednostki prostej
W ramach tego samouczka utworzysz aplikację demonstrującą sposób wyodrębniania danych nauczonych maszynowo z wypowiedzi za pomocą jednostki **Simple** (prostej).

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek prostych 
> * Tworzenie nowej aplikacji LUIS dla domeny komunikacji z intencją SendMessage
> * Dodawanie intencji _None_ i dodawanie przykładowych wypowiedzi
> * Dodawanie jednostki prostej w celu wyodrębnienia zawartość wiadomości z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="purpose-of-the-app"></a>Przeznaczenie aplikacji
Ta aplikacja pokazuje, jak wydobyć dane z wypowiedzi. Przeanalizujmy następującą wypowiedź z czatbota:

```JSON
Send a message telling them to stop
```

Intencją jest wysłanie wiadomości. Ważnymi danymi w wypowiedzi jest sama wiadomość, `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Przeznaczenie jednostki prostej
Celem jednostki prostej jest nauczenie usługi LUIS, co to jest wiadomość i gdzie można ją znaleźć w wypowiedzi. Część wypowiedzi, która jest wiadomością, może się zmienić dla różnych wypowiedzi i jest zależna od doboru słów i długości wypowiedzi. Usługa LUIS potrzebuje przykładów wiadomości w dowolnej wypowiedzi we wszystkich intencjach.  

Dla tej prostej aplikacji wiadomość będzie znajdować się na końcu wypowiedzi. 

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
1. Zaloguj się w witrynie internetowej usługi [LUIS][LUIS]. Pamiętaj, aby zalogować się w regionie, w którym mają zostać opublikowane punkty końcowe usługi LUIS.

2. W witrynie internetowej usługi [LUIS][LUIS] wybierz pozycję **Create new app** (Utwórz nową aplikację).  

    ![Lista aplikacji LUIS](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. W wyskakującym oknie dialogowym wprowadź nazwę `MyCommunicator`. 

    ![Lista aplikacji LUIS](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. Po zakończeniu tego procesu aplikacja wyświetli stronę **Intents** (Intencje) z intencją **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Zrzut ekranu ze stroną Intents (Intencje) usługi LUIS z intencją None")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Tworzenie nowej intencji

1. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję). 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Zrzut ekranu usługi LUIS z wyróżnionym przyciskiem Create new intent (Utwórz nową intencję)")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Wprowadź nazwę nowej intencji: `SendMessage`. Ta intencja powinna być wybierana za każdym razem, gdy użytkownik chce wysłać wiadomość.

    Tworząc intencję, tworzysz główną kategorię informacji, którą chcesz identyfikować. Nadanie nazwy kategorii umożliwia każdej innej aplikacji, która używa wyników zapytania usługi LUIS, zastosowanie tej nazwy kategorii w celu znalezienia odpowiedniej odpowiedzi lub podjęcia odpowiedniej akcji. Usługa LUIS nie będzie odpowiadać na te pytania — określi jedynie, jakiego rodzaju informacji dotyczy pytanie w języku naturalnym. 

    ![Wprowadź nazwę intencji SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Dodaj siedem wypowiedzi do intencji `SendMessage` — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |Odpowiedz: Mam wiadomość od Ciebie, odpowiedź będę znać jutro|
    |Wyślij wiadomość: Kiedy będziesz w domu?|
    |Wyślij wiadomość, że nie mam czasu|
    |Poinformuj, że to musi być zrobione dzisiaj|
    |Wyślij wiadomość błyskawiczną, że prowadzę i odpowiem później|
    |Napisz wiadomość do Dawida o treści: Kiedy to było?|
    |Wyślij powitanie do Grzegorza|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Zrzut ekranu z usługi LUIS z wprowadzonymi wypowiedziami")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Dodawanie wypowiedzi do intencji None

Aplikacja LUIS obecnie nie zawiera żadnych wypowiedzi dla intencji **None**. Potrzebne są wypowiedzi, na które aplikacja ma nie odpowiadać, dlatego należy dodać wypowiedzi do intencji **None**. Nie zostawiaj jej pustej. 
    
1. Wybierz pozycję **Intents** (Intencje) na lewym panelu. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Zrzut ekranu usługi LUIS z wyróżnionym przyciskiem Intents (Intencje)")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Wybierz intencję **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Zrzut ekranu z wybieraniem intencji None")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji. Niektóre dobre intencje **None** to:

    | Przykładowe wypowiedzi|
    |--|
    |Anuluj!|
    |Do widzenia|
    |Co się dzieje?|
    
    W aplikacji wywołującej usługę LUIS (takiej jak czatbot), gdy usługa LUIS zwraca intencję **None** dla wypowiedzi, bot może zadać pytanie, czy użytkownik chce zakończyć konwersację. Bot może również podać więcej wskazówek umożliwiających kontynuowanie konwersacji, jeśli użytkownik nie chce jej zakończyć. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Zrzut ekranu z usługi LUIS z wypowiedziami dla intencji None")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Tworzenie prostej jednostki do wyodrębnienia wiadomości 
1. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

    ![Wybieranie linku Intents (Intencje)](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. Wybierz pozycję `SendMessage` z listy intencji.

    ![Wybierz intencję SendMessage](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. W wypowiedzi (`Reply with I got your message, I will have the answer tomorrow`) wybierz pierwsze (`I`) i ostatnie słowo treści wiadomości (`tomorrow`). Wszystkie te słowa są wybierane dla wiadomości i u góry wyświetlane jest menu rozwijane.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Zrzut ekranu z wybieraniem słów wiadomości w wypowiedzi")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Wprowadź nazwę jednostki `Message` w polu tekstowym.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Zrzut ekranu wprowadzania nazwy jednostki w polu")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę) z menu rozwijanego. Przeznaczeniem jednostki jest wydobycie tekstu, który stanowi treść wiadomości. W tej aplikacji LUIS wiadomość tekstowa znajduje się na końcu wypowiedzi, ale wypowiedź może być dowolnej długości, tak samo jak wiadomość. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Zrzut ekranu przedstawiający tworzenie nowej jednostki z wypowiedzi")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. W oknie wyskakującym domyślnym typem jednostki jest **Simple** (Prosta), a nazwą jednostki jest `Message`. Zachowaj te ustawienia i wybierz pozycję **Done** (Gotowe).

    ![Weryfikowanie typu jednostki](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Teraz, gdy jednostka została utworzona i oznaczono etykietą jedną wypowiedź, oznacz etykietami resztę wypowiedzi za pomocą tej jednostki. Wybierz wypowiedź, a następnie kliknij pierwsze i ostatnie słowo wiadomości. Z menu rozwijanego wybierz jednostkę `Message`. Wiadomość jest teraz oznaczona w jednostce. Oznacz etykietami wszystkie frazy wiadomości w pozostałych wypowiedziach.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Zrzut ekranu przedstawiający wszystkie wypowiedzi oznaczone etykietami")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    Domyślnym widokiem wypowiedzi jest **Entities view** (Widok jednostek). Wybierz kontrolkę **Entities view** (Widok jednostek) nad wypowiedziami. Widok **Tokens view** (Widok tokenów) wyświetla tekst wypowiedzi. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Zrzut ekranu wypowiedzi w widoku tokenów")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Wybieranie przycisku Train (Ucz)](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Powiadomienie o pomyślnym ukończeniu uczenia](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym punktem końcowym")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. Przejdź na koniec tego adresu URL i wprowadź ciąg `text I'm driving and will be 30 minutes late to the meeting`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić wypowiedzi `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, zawierająca jedynie dwie intencje i jedną jednostkę, zidentyfikowała intencję zapytania w języku naturalnym i zwróciła dane wiadomości. 

W wynikach JSON najwyżej oceniana intencja `SendMessage` ma wynik 0.987501. Wszystkie wyniki należą do zakresu od 1 do 0, im bliżej 1, tym lepiej. Wynikiem dla intencji `None` jest 0.111048922, czyli znaczniej bliżej zera. 

Dane wiadomości mają typ (`Message`) i wartość (`i ' m driving and will be 30 minutes late to the meeting`). 

Twój chatbot ma teraz dość informacji, aby określić akcję główną (`SendMessage`) i parametr tej akcji — tekst wiadomości. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wysłać wiadomość przez interfejs API innej firmy. Jeśli istnieją inne opcje programowe dla bota lub aplikacji wywołującej, usługa LUIS nie obsłuży ich. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak dodać jednostkę hierarchiczną](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
