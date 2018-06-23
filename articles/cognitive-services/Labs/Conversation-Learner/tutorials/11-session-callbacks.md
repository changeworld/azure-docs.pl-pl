---
title: Jak używać wywołania zwrotne sesji z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć wywołania zwrotne sesji z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f8970620c1f0f87ccae13d031092a048144ffb19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348629"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-application"></a>Jak używać wywołania zwrotne sesji z aplikacją uczeń konwersacji

W tym samouczku przedstawiono onSessionStart i onSessionEnd wywołań zwrotnych.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot "tutorialSessionCallbacks.ts".

    npm run tutorial-session-callbacks

## <a name="details"></a>Szczegóły
W tym samouczku opisano pojęcia sesji, jak sesji są obsługiwane przez domyślny, jak można zmienić tego zachowania.

Sesja jest jeden konwersacji z bot. Może mieć wiele włącza, ale nie występowały długie przerwy w konwersacji (na przykład 30 minut).  Zawiera strona pomocy na "Limity" domyślny okres limitu czasu sesji.

W przypadku długich podziały bot będzie przejdź do jego następnej sesji.  Uruchamianie nowej sesji umieszcza powtarzającego się sieci neuronowej stanu początkowego.  Domyślnie także czyści wszystkie wartości jednostki, ale to zachowanie można zmienić (ilustrowane poniżej).

### <a name="open-the-demo"></a>Otwórz pokaz

Polecenie Samouczek-11-SessionCallbacks na liście aplikacji. 

### <a name="entities"></a>Jednostki

Cztery jednostek zdefiniowaniu w aplikacji.

![](../media/tutorial11_entities.PNG)

Jedyną operacją, należy pamiętać, jest to BotName programowe jednostki.  To jest ustawiony przez bot na czas rozpoczęcia sesji.

### <a name="actions"></a>Akcje

Utworzono cztery akcje. 

![](../media/tutorial11_actions.PNG)

Najpierw ten samouczek przedstawia sposób kontrolowania wartości jednostki na początku sesji — na przykład ustawienie jednostki BotName, zanim użytkownik odpowie niczego.

Po drugie ten samouczek przedstawia sposób utrwalić wartości z jednej sesji do następnego.  W tym samouczku przyjęto założenie, że nazwy użytkownika i numer telefonu nie zmieniają się w jednej sesji do następnego, ale ten może zmienić ich lokalizacji.  W związku z tym nazwę i numer telefonu firma Microsoft zachowywane między sesjami, ale lokalizacji zwykłego użytkownika.

### <a name="train-dialog"></a>Okno dialogowe pociągu

Oto przykładowe okno dialogowe. Jest to jedna sesja — to znaczy nie występowały długie przerwy w tym oknie dialogowym.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Kod dla wywołań zwrotnych

Kod dla metody wywołania zwrotnego znajduje się w pliku: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Obie te metody są opcjonalne.

- OnSessionStartCallback: Ta metoda ustawia jednostkę BotName.
- OnSessionEndCallback: należy określić co chcesz wyczyścić. Spowoduje to wyczyszczenie wszystkich jednostek z wyjątkiem nazwy użytkownika i telefonu użytkownika.

### <a name="try-the-bot"></a>Spróbuj bot

Przełącz się do interfejsu użytkownika sieci Web, a następnie kliknij polecenie okna z dziennika.

1. Wprowadź tekst "hello".
2. System: "Witaj, jestem Botty. Co to jest nazwa? " która zawiera nazwę Botty pochodzących z OnSessionStartCallback.
3. Wprowadź "jason".
4. System: "Hi jason. Co to jest numer telefonu? "
5. Wprowadź "555-555-5555".
6. System: "Proszę Botty Twojej lokalizacji jason?"
7. Wpisz "Redmond".

Jest to jedna sesja. Aby uruchomić nową sesję, potrzebujemy do zakończenia tej sesji. 

1. Kliknij przycisk limit czasu sesji. Spowoduje to przeniesienie do następnej sesji.
    - Przycisk "Limit czasu sesji" znajduje się na potrzeby debugowania.  W sesji rzeczywiste długi Wstrzymaj musi wystąpić około 30 minut.  Zawiera strona pomocy na "Limity" domyślny okres limitu czasu sesji.
1. Wprowadź "hi".
2. System: "Proszę Botty Twojej lokalizacji jason?"
    - System ma zapamiętany nazwę i numer telefonu.
2. Wprowadź nową lokalizację: "Seattle".
3. System: ', jason znajdujesz się w Seattle".
4. Kliknij przycisk Done testowania.

Teraz przejdź do okna z dziennika. Należy zauważyć, że ostatnich konwersacji została podzielona na dwie, ponieważ każdy okna dialogowego dziennika odpowiada jednej sesji.  

![](../media/tutorial11_splitdialogs.PNG)

- W pierwszym interakcji Botty jest ustawiona, ale nazwa i numer telefonu nie są.
- Drugi interakcji zawiera nazwę i numer telefonu.

Teraz przejrzane obsługi sesje domyślnie i jak zachowanie domyślne można przesłonić. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołania API](./12-api-calls.md)
