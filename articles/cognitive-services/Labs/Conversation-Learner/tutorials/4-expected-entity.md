---
title: Jak używać właściwości "Oczekiwano jednostki" uczeń konwersacji akcje — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać właściwości "Oczekiwano jednostki" modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a43c52143f936eaefd4383714b1c67b6b74d34e8
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378673"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak użyć właściwości "Oczekiwano jednostki" w akcji

Ten samouczek przedstawia pole "Oczekiwano jednostki" w akcji.

## <a name="video"></a>Połączenia wideo

[![Samouczek 4 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-04-preview)](http://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Użyj pola "Oczekiwano jednostki" akcji do komunikowania się do systemu, możesz spodziewać się, że odpowiedzi użytkownika do akcji będzie dotyczyć stanu jednostki.

Konkretnie Jeśli pole "Oczekiwano jednostki" akcję ustawiono $entity, a następnie na następny wypowiedź użytkownika, system wykonują następujące czynności:

1. Najpierw należy w zwykły sposób, podejmą próbę odnalezienia jednostki przy użyciu modelu wyodrębniania jednostki oparte na uczeniu maszynowym
2. Jeśli brak jednostek zostaną znalezione w kroku 1, następnie — jako heurystyki — przypisać $entity wypowiedź całego użytkownika.
3. Wywołaj `EntityDetectionCallback` w zwykły sposób i przejdź do wybór akcji.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź ExpectedEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź nazwę.
3. Kliknięcie pozycji Utwórz

> [!NOTE]
> Typ jednostki jest "custom". Ta wartość oznacza, że jednostki może być uczony.  Istnieją wstępnie utworzone jednostki, co oznacza, że nie można zmienić ich zachowania.  Te jednostki są objęte [samouczek jednostek dostosowywalnego](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nową akcję.
2. W odpowiedzi wpisz "Jak się Nazywasz?".
3. Wprowadź $name w jednostki z oczekiwaniami. Kliknij pozycję Zapisz.
    - Ta wartość oznacza, że jeśli to pytanie jest zadawane i odpowiedź użytkownika nie ma żadnych jednostek wykrył, bot należy założyć, że całej odpowiedzi użytkownika jest tej jednostki.
2. Kliknij pozycję operacje, a następnie nową akcję do utworzenia drugiej akcji.
3. W odpowiedzi wpisz "Hello $name".
    - Należy pamiętać, że jednostka jest automatycznie dodawany jako wymaganych jednostek. 
4. Kliknij pozycję Zapisz.

Masz teraz dwie akcje.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Jak się Nazywasz?"
    - Nie można wybrać odpowiedź "Hello $name", ponieważ wymaga, aby jednostki $name należy zdefiniować i $name nie znajduje się w pamięci botów.
2. Wprowadź "david". 
    - Nazwa jest podświetlona jako jednostka. Jest to ze względu na Algorytm heurystyczny, które nam się skonfigurować powyżej do wybrania odpowiedzi jako jednostka.
5. Kliknij wynik akcji
    - Wartość nazwy jest teraz pamięci botów.
    - "Hello $name" jest teraz dostępny jako odpowiedzi. 
6. Wybierz pozycję "Hello $name".
7. Kliknij przycisk Gotowe, nauczania.

Poniżej przedstawiono dwa przykłady gdzie model Ekstrakcja jednostek uczenia maszynowego identyfikuje nazwę, więc nie zostanie wyzwolony, Algorytm heurystyczny "Oczekiwano jednostki".

1. Kliknij okno dialogowe Nowy pociągu.
2. Wprowadź "Moja nazwa jest david".
    - Modelu identyfikuje david jako nazwa jednostki, ponieważ ma ona widoczne ten wyraz przed.
2. Kliknij wynik akcji
3. Wybierz pozycję "Hello $name".
4. Wprowadź "Moja nazwa jest susan".
    - Modelu identyfikuje susan jako nazwę, ponieważ on ten wzorzec ma już widoczne.
2. Kliknij wynik akcji.
2. Wybierz pozycję "Hello susan".
3. Kliknij przycisk Gotowe, nauczania.

W poniższych przykładach wyzwala heurystyki "Oczekiwano jednostki", ale jest niepoprawny. Następnie w przykładach pokazano, jak poprawić.

1. Wpisz "Zadzwoń do mnie jose".
    - Model nie może rozpoznać nazwę jako jednostka.
2. Kliknij pozycję jose, a następnie wybierz nazwę.
3. Kliknij wynik akcji.
4. Wybierz hello $name.
5. Kliknij przycisk Gotowe, nauczania.
1. Kliknij okno dialogowe Nowy pociągu.
2. Wpisz "hello".
3. W odpowiedzi na "jak się Nazywasz" Wprowadź "Mnie o Piotr".
    - Cała fraza zostanie wyróżniona. Jest to spowodowane statystyczne modelu nie znaleziono nazwy, więc heurystyki zadziałał i jako nazwę podmiotu wybrać całej odpowiedzi.
2. Aby go rozwiązać, kliknij wyróżniony frazy, a następnie kliknij czerwony znak x. 
3. Kliknij, aby wybrać frank, a następnie kliknij nazwę.
2. Kliknij wynik akcji
3. Wybierz pozycję "Hello $name".
4. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Jednostki można negować](./5-negatable-entities.md)
