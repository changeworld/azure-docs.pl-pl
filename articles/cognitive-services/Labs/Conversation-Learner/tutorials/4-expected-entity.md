---
title: Sposób użycia właściwości "Oczekiwano jednostki" uczeń konwersacji akcji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak właściwość "Oczekiwano jednostki" aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348656"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Sposób użycia właściwości "Oczekiwano jednostki" akcji

W tym samouczku przedstawiono pola "Oczekiwano jednostki" akcji.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Pole "Oczekiwano jednostki" akcji umożliwia komunikują się z systemu, czy spodziewasz się odpowiedzi użytkownika do akcji będą do stanu jednostki.

Konkretnie Jeśli pole "Oczekiwano jednostki" akcji ustawiono $entity, a następnie na następny utterance użytkownika, system będzie:

1. Po pierwsze w zwykły sposób, podejmą próbę odnalezienia jednostek przy użyciu modelu wyodrębniania jednostki na podstawie uczenia maszynowego
2. Jeśli następnie — jako heurystyki — Brak jednostek znajdują się w kroku 1, przypisać $entity utterance całego użytkownika.
3. Wywołanie `EntityDetectionCallback` w zwykły sposób i przejdź do wybór akcji.

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź ExpectedEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź nazwę.
3. Kliknięcie pozycji Utwórz

Uwaga typ jednostki jest "custom", ponieważ oznacza to, że jednostka może być uczony.  Istnieją jednostki wbudowanych, co oznacza, że nie można dostosować ich zachowanie — są one zawarte w samouczku innego.

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi wpisz "Co to jest nazwa?".
3. Oczekiwano encji wprowadź $name. Kliknij przycisk Zapisz.
    - Oznacza to, że jeśli monitu to pytanie i odpowiedź użytkownika nie ma żadnych jednostek wykrył, bot powinien przyjęto założenie, że ta jednostka jest całej odpowiedzi użytkownika.
2. Kliknij przycisk akcje, a następnie nową akcję w celu utworzenia drugiej akcji.
3. W odpowiedzi wpisz tekst "Hello $name".
    - Należy pamiętać, że jednostka jest automatycznie dodawane jako disqualifying jednostki. 
4. Klikanie pozycji Zapisz.

Teraz masz dwie akcje.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji i wybierz pozycję "Co to jest nazwa?"
    - Należy pamiętać, że odpowiedź "Hello $name" nie można wybrać, ponieważ jego requies jednostki $name do zdefiniowania i $name nie znajduje się w pamięci w bot.
2. Wprowadź "Dominik". 
    - Należy pamiętać, że nazwa jest podświetlona jako jednostki. Jest to spowodowane heurystyki przez skonfigurowanie powyżej jako jednostka wybrany typ odpowiedzi.
5. Kliknij przycisk wynik akcji
    - Uwaga nazwy wartości jest teraz bot pamięci.
    - "Hello $name" jest teraz dostępna jako odpowiedź. 
6. Wybierz "Hello $name".
7. Kliknij przycisk Done nauczania.

Poniżej przedstawiono dwa przykłady gdzie modelu uczenia maszynowego jednostki wyodrębniania identyfikuje nazwę, więc heurystyki "Oczekiwano jednostki" nie jest wyzwalane.

1. Kliknij przycisk Nowe okno dialogowe pociągu.
2. Wprowadź "mojej nazwy jest Dominik".
    - Należy pamiętać, że go zidentyfikować Dominik jako nazwa jednostki, ponieważ znalazła ten wyraz przed.
2. Kliknij przycisk wynik akcji
3. Wybierz "Hello $name".
4. Wprowadź "mojej nazwy jest susan".
    - Uwaga go identyfikuje susan jako nazwy, ponieważ on widział już tego wzorca.
2. Kliknij przycisk wynik akcji.
2. Wybierz opcję "susan Hello".
3. Kliknij przycisk Done nauczania.

Oto dwa dalsze przykłady, których wyzwala heurystyki "Oczekiwano jednostki", ale jest nieprawidłowy i jak firma Microsoft może wprowadzać poprawki.

1. Wpisz "Zadzwoń do mnie jose".
    - Należy pamiętać, że nie rozpoznaje nazwy jako jednostki.
2. Kliknij jose, a następnie wybierz nazwę.
3. Kliknij przycisk wynik akcji.
4. Wybierz hello $name.
5. Kliknij przycisk Done nauczania.
1. Kliknij przycisk Nowe okno dialogowe pociągu.
2. Wprowadź tekst "hello".
3. W odpowiedzi na "co to jest nazwa" Wprowadź "Używam wywołuje Paweł".
    - Należy pamiętać, że cała fraza zostanie wyróżniona. Jest to spowodowane statystyczne modelu nie znaleziono nazwy, więc heurystyki wywoływane i jako nazwę podmiotu wybrać całej odpowiedzi.
2. Aby naprawić błąd, kliknij frazę zaznaczony, a następnie kliknij czerwony znak x. 
3. Kliknij, aby wybrać Paweł, a następnie kliknij nazwę.
2. Kliknij przycisk wynik akcji
3. Wybierz "Hello $name".
4. Kliknij przycisk Done nauczania.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Można negować jednostek](./5-negatable-entities.md)
