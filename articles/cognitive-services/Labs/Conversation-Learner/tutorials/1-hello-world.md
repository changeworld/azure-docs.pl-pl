---
title: Jak utworzyć aplikację "Hello World" konwersacji uczeń — kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć aplikację uczeń konwersacji "Hello World".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 30026285ac6dda45d2f5e3718aae741b928cf242
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348684"
---
# <a name="how-to-create-a-hello-world-application-with-conversation-learner"></a>Jak utworzyć aplikację "Hello World" z uczeń konwersacji

Ten samouczek pokazuje, jak rozpocząć pracę z uczeń konwersacji, łącznie z akcjami tworzenia nauczania interakcyjne i poprawiania zarejestrowane okien dialogowych od użytkowników końcowych.

## <a name="requirements"></a>Wymagania
Jeśli nie jest jeszcze, najpierw upewnij się, zostały ukończone wszystkie kroki konfiguracji, w tym tworzenie `.env` plik z sieci LUIS tworzenia klucza.  Zobacz [szybkiego startu](https://github.com/Microsoft/ConversationLearner-Samples) szczegółowe informacje.

Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="steps"></a>Kroki

Uruchom na stronie głównej w Interfejsie użytkownika sieci Web.

### <a name="create-the-app"></a>Tworzymy aplikację.
1. Kliknij przycisk nowej aplikacji
2. W polu nazwy wpisz Hello World
3. Kliknięcie pozycji Utwórz

### <a name="create-an-action"></a>Utwórz działanie

1. Kliknięcie aplikacji Hello World ją uruchomić
2. Kliknij przycisk akcje, a następnie nowa akcja
    - Akcja może być komunikatu tekstowego, który zwraca uczeń konwersacji do użytkownika, wywołanie interfejsu API lub karty.
3. W odpowiedzi wpisz "Hello World!"
    - To jest odpowiedź, który zwróci bot
4. Kliknięcie pozycji Utwórz

Utworzono najpierw, to zrobić bot zwracać odpowiedzi tekstu.

### <a name="train-the-bot"></a>Szkolenie bot

#### <a name="create-the-first-dialog"></a>Tworzenie pierwszego okna dialogowego

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu
2. Wprowadź przykładowy co użytkownik będzie napisane w żebranie konwersacji, na przykład "hello".
3. Kliknij przycisk wynik akcji
4. Wybierz opcję "Witaj świecie!"
    - Spowoduje to utworzenie okna dialogowego Włącz jeden przykład. 
2. Wprowadź "goodbye"
3. Kliknij przycisk wynik akcji
4. Kliknij przycisk Dodaj akcję, a następnie wprowadź "Goodbye"! w odpowiedzi następnie kliknij przycisk "Utwórz"
5. Kliknij przycisk Done nauczania. Spowoduje to zakończenie tego okna dialogowego szkolenia.

Teraz masz jedno okno nauczania w systemie.

#### <a name="continue-teaching-the-bot"></a>Kontynuować nauczania bot
Teraz nie jeden więcej szkoleń i zobacz, jak reaguje bot.

1. Kliknij przycisk Nowy Train okno dialogowe
2. Wprowadź "cześć istnieje"
    - Jest to podobne do okna dialogowego pierwszego i oczekujemy uzyskać dobrą wynik z bot.
2. Kliknij przycisk wynik akcji
    - Położenie i wynik nie może jeszcze być wystarczająco dokładne i wymagać dodatkowych nauczania.
3. Kliknij przycisk Wybierz obok "Hello World!"
4. Następnie wprowadź "bye"
5. Kliknij przycisk wynik akcji
6. Wybierz opcję "Goodbye"!
7. Kliknij przycisk Done nauczania

![](../media/tutorial1_actions.PNG)

Wykonamy innej sesji nauczania, aby zobaczyć, jak działa bot.

Powtórz powyższe kroki przy użyciu "hi" i "byebye" i Zapisz zmiany w pozycji i wynik odpowiedzi robotów, po kliknięciu wynik akcji.

Można teraz Powtórz kroki przy użyciu "howdy" i "good bye" i należy pamiętać, że oceniania ulepszenia pokazuje wyniki wskazujący bot dowiedziała interakcji.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testowanie bot jako użytkownik końcowy

1. Kliknij dziennik okien dialogowych, okno dialogowe następnie nowego dziennika
2. Typ "hello istnieje"
3. Następnie "bye"

Można również spróbuj uruchomić konwersacji "bye", zwracając uwagę bot odpowiedzi.

### <a name="view-conversations-in-the-log-dialogs"></a>Widok konwersacje w dzienniku, okna dialogowe

W oknach dialogowych dziennika, możesz wyświetlić listę konwersacji, zaktualizowania i zapisania interakcji jako szkolenia w oknach dialogowych. W tym:

1. Kliknij dziennik konwersacji
2. Jeśli konwersacja wygląda dobrze, kliknij polecenie ostatnia akcja np. "Goodbye".
3. Kliknij, aby wybrać sugerowane odpowiedzi. 
    - Można również wybrać lub Dodaj inną akcję.
4. Następnie kliknij pozycję gotowe, aby zapisać ten jako okno dialogowe szkolenia.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zaczekaj i akcji bez oczekiwania](./2-wait-vs-nonwait-actions.md)