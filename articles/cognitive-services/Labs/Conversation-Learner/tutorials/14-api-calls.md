---
title: Jak używać wywołań interfejsu API z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać wywołań interfejsu API z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703928"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Jak dodać wywołania interfejsu API do modelu Conversation Learner

W tym samouczku pokazano, jak dodać wywołania interfejsu API do modelu. Wywołania interfejsu API to funkcje, które można definiować i zapisywać w bot, a które Conversation Learner mogą być wywoływane.

## <a name="video"></a>Połączenia wideo

[![Samouczek wywołań interfejsu API — wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby "tutorialAPICalls. TS" bot działa.

    npm run tutorial-api-calls

## <a name="details"></a>Szczegóły

- Wywołania interfejsu API mogą odczytywać jednostki i manipulować nimi.
- Wywołania interfejsu API mają dostęp do obiektu Menedżera pamięci.
- Wywołania interfejsu API mogą przyjmować argumenty.

### <a name="open-the-demo"></a>Otwórz demonstrację

W interfejsie użytkownika sieci Web kliknij pozycję "Importowanie samouczków" i wybierz model o nazwie "samouczek-14-APICalls".

### <a name="entities"></a>Jednostki

Zdefiniowano jedną jednostkę w modelu o nazwie `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Wywołania interfejsu API
Kod wywołań interfejsu API jest zdefiniowany w tym pliku: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Wywołanie zwrotne zwraca losowe powitanie zdefiniowane `greeting` w tablicy. `RandomGreeting`
- `Multiply` Wywołanie zwrotne będzie mnożyć dwie liczby przesłane przez akcję wywołującą ją i zwraca wynik, który może być renderowany w interfejsie użytkownika.
    - Zauważ, że Menedżer pamięci jest pierwszym argumentem. 
    - Zwróć uwagę, że wywołania zwrotne interfejsu API mogą przyjmować wiele danych `num1string` wejściowych `num2string`, w tym przypadku i.
- `ClearEntities` Wywołanie zwrotne czyści jednostkę Number, aby użytkownik mógł wprowadzić kolejną liczbę. 
    - Ilustruje sposób, w jaki wywołania interfejsu API mogą manipulować obiektami.

### <a name="actions"></a>Akcje
Utworzyliśmy cztery akcje. Trzy z nich to akcje interfejsu API "bez oczekiwania" z czwartą to akcja "tekst", która prosi użytkownika o pytanie podobne do tego, co zostało zaobserwowane w innych samouczkach. Aby zobaczyć, jak każdy został utworzony, wykonaj następujące czynności:
1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie kliknij jedną z czterech akcji wymienionych w siatce.
2. Zwróć uwagę na wartości każdego pola w formularzu, który pojawia się.
3. Zwróć uwagę `Refresh` na przycisk obok pola interfejsu API.
    - Jeśli zatrzymasz bot i wprowadzisz zmiany w interfejsach API, gdy strona interfejsu użytkownika jest w stanie up, możesz kliknąć `Refresh` ten przycisk, aby pobrać najnowsze zmiany.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, pomnóż i RandomGreeting
Wszystkie trzy z tych akcji są typu interfejsu API. Każdy z nich bazuje na funkcjach wywołania zwrotnego interfejsu API w celu wykonania pewnej pracy i prawdopodobnie zwraca wartość, która zostanie wyświetlona dla użytkownika.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Jaką liczbę chcesz pomnożyć przez 12"
Jest to akcja "tekst" i po prostu prosi o pytanie użytkownika. Mimo że ta akcja nie działa w rzeczywistości z jednym z wywołań zwrotnych interfejsu API, monituje użytkownika o odpowiedź przy użyciu numeru, który będzie przekroczyć pamięć jednostki, która może być następnie używana przez akcję "pomnóż", która używa jednego z wywołań wywołania interfejsu API.


### <a name="train-dialog"></a>Okno dialogowe uczenia

Przejdźmy do "okna dialogowego szkolenia".

1. W lewym panelu kliknij `Train Dialogs`przycisk, `New Train Dialog` a następnie przycisk.
2. Wpisz "Hello".
3. Kliknij przycisk `Score Actions`.
4. Wybierz pozycję `RandomGreeting`. 
    - Spowoduje to wykonanie losowego wywołania interfejsu API powitania.
    - NIE czeka na odpowiedź użytkownika.
5. Wybierz pozycję `What number to do you want to multiply by 12?`
6. Wpisz numer, dowolną liczbę i tylko liczbę.
    - Zwróć uwagę, że liczba została automatycznie oznaczona jako `number` jednostka.
7. Kliknij przycisk `Score Actions`.
8. `Multiply` Wybierz akcję.
    - Zwróć uwagę na wynik mnożenia przez 12.
    - Zauważ, że pamięć nadal zawiera wprowadzoną wartość`number`
9. `ClearEntities` Wybierz akcję.
    - Zwróć uwagę, że wartość jednostki `number` dla została wyczyszczona z pamięci.
10. Kliknij przycisk `Save`.

Zobaczysz, jak zarejestrować wywołania zwrotne interfejsu API, ich wspólne wzorce oraz jak definiować argumenty i kojarzyć z nimi wartości.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Karty — część 1](./15-cards.md)
