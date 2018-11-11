---
title: Jak używać wywołań zwrotnych sesji z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać wywołań zwrotnych sesji przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 73fb2a3f10476550bbe6bd20c387a1cc2a7727d8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246484"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Jak używać wywołań zwrotnych sesji przy użyciu modelu uczeń konwersacji

W tym samouczku przedstawiono onSessionStart i onSessionEnd wywołania zwrotne.

## <a name="video"></a>Połączenia wideo

[![Samouczek 11 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-11-preview)](https://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby `tutorialSessionCallbacks` bot jest uruchomiona.

    npm run tutorial-session-callbacks

## <a name="details"></a>Szczegóły
W tym samouczku opisano pojęcia sesji obsługi sesji domyślnie i jak można zastąpić to zachowanie.

Sesja jest jeden konwersacji z botem. Może mieć wiele włącza, ale nie występowały długie przerwy w konwersacji (na przykład, 30 minut).  Zobacz stronę pomocy na "Ograniczenia" dla domyślny okres limitu czasu sesji.

W przypadku długich przerw bot będzie przejdź do swojej następnej sesji.  Uruchamianie nowej sesji umieszcza powtarzającego się sieci neuronowej do stanu początkowego.  Domyślnie także czyści wszystkie wartości jednostki, mimo że to zachowanie można zmienić (przedstawiono poniżej).

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modeli kliknij samouczek-11-SessionCallbacks. 

### <a name="entities"></a>Jednostki

Cztery jednostki są zdefiniowane w modelu.

![](../media/tutorial11_entities.PNG)

Jedno, należy pamiętać, jest BotName programowe jednostki.  Ta jednostka zostanie ustawiona przez bota w momencie rozpoczęcia sesji.

### <a name="actions"></a>Akcje

Cztery akcje są zdefiniowane w modelu.

![](../media/tutorial11_actions.PNG)

Po pierwsze, ten samouczek przedstawia sposób kontrolowania wartości jednostki na początku sesji — na przykład z ustawieniem jednostki BotName użytkownika mówi nic.

Po drugie w tym samouczku pokazują, jak zachować wartości z jednej sesji do następnego.  W tym samouczku przyjęto założenie, że nazwa użytkownika i numer telefonu pozostają takie same, z jedną sesję na następny, ale ta lokalizacja może ulec zmianie.  W związku z tym utrzymujemy nazwę i numer telefonu w sesji, ale lokalizacja zwykłego użytkownika.

### <a name="train-dialog"></a>Okno dialogowe szkolenie

Poniżej przedstawiono przykładowe okno dialogowe. Jest to jedna sesja — oznacza to, że istnieją długie przerwy w tym oknie dialogowym.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Kod dla wywołania zwrotne

Kod dla metody wywołania zwrotnego znajduje się w pliku: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Obie te metody są opcjonalne.

- OnSessionStartCallback: Ta metoda ustawia jednostkę BotName.
- OnSessionEndCallback: można określić mają zostać zachowane. Spowoduje to wyczyszczenie wszystkich jednostek, z wyjątkiem nazwy użytkownika i telefonu użytkownika.

### <a name="try-the-bot"></a>Spróbuj robota

Przełącz się do internetowego interfejsu użytkownika, a następnie kliknij polecenie dziennika okien dialogowych.

1. Wpisz "hello".
2. System: "cześć, jestem Botty. Jak się Nazywasz? " mającego przychodzących Botty nazwy z OnSessionStartCallback.
3. Wprowadź "jason".
4. System: "Hi jason. Jaki jest Twój numer telefonu? "
5. Wprowadź "555-555-5555".
6. System: "można stwierdzić, Botty do lokalizacji, jason?"
7. Wpisz "Redmond".

Jest to jedna sesja. Aby rozpocząć nową sesję, należy zakończyć tę sesję. 

1. Kliknij przycisk limit czasu sesji. Spowoduje to przeniesienie do następnej sesji.
    - Przycisk "Limit czasu sesji" znajduje się na potrzeby debugowania.  W ramach rzeczywistego sesji długie Wstrzymaj musi występować około 30 minut.  Zobacz stronę pomocy na "Ograniczenia" dla domyślny okres limitu czasu sesji.
1. Wprowadź "Cześć".
2. System: "można stwierdzić, Botty do lokalizacji, jason?"
    - System ma zapamiętane nazwę i numer telefonu.
2. Wprowadź nową lokalizację: "Seattle".
3. System: "Dlatego jason jesteś w Seattle".
4. Kliknij przycisk Done testowania.

Teraz przejdź z powrotem do okna z dziennika. Należy zauważyć, że ostatni konwersacji została podzielona na dwie, ponieważ każdego okna dialogowego dziennika odpowiada jednej sesji.  

![](../media/tutorial11_splitdialogs.PNG)

- W pierwszej interakcji Botty jest ustawiona, ale nie są nazwa i numer telefonu.
- Drugiej interakcji zawiera nazwę i numer telefonu.

Teraz wiesz, jak sesje są obsługiwane przez domyślny i jak zachowanie domyślne można przesłonić. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołania interfejsu API](./12-api-calls.md)
