---
title: Jak używać interfejsu API wywołań za pomocą modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać interfejsu API przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1d4013d736d8cfcb75874bc0c86d20b86ab4dd62
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215843"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Jak dodać wywołań interfejsu API modelu uczeń konwersacji

W tym samouczku przedstawiono sposób dodawania wywołań interfejsu API do modelu. Wywołania interfejsu API są funkcje, które zdefiniować i zapisać w Twoim czatbocie oraz tych, które można wywołać uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Samouczek 12 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-12-preview)](http://aka.ms/blis-tutorial-12)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że bot "tutorialAPICalls.ts" jest uruchomiona.

    npm run tutorial-api-calls

## <a name="details"></a>Szczegóły

- Wywołania interfejsu API może odczytywać i modyfikować jednostek.
- Wywołania interfejsów API mają dostęp do obiektu Menedżera pamięci.
- Wywołania interfejsu API możesz skorzystać z argumentami — dzięki temu ponownie przy użyciu tego samego wywołania interfejsu API do obsługi różnych celów.

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web należy kliknąć samouczek-12-APICalls. 

### <a name="entities"></a>Jednostki

Firma Microsoft zdefiniowano jednej jednostki w modelu, określany jako numer.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Wywołania interfejsu API
Kod dla wywołań interfejsu API jest zdefiniowany w tym pliku: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Pierwsze wywołanie zwrotne interfejsu API jest RandomGreeting. Zwraca losowe pozdrowienia zdefiniowanej zmiennej pozdrowienie.
- Wywołanie zwrotne mnożenia interfejsu API: go będzie mnożenia dwóch liczb, dostarczone przez użytkownika. Zwraca wynik mnożenia dwóch liczb. Pokazuje to, że wywołania zwrotne interfejsu API można pobrać dane wejściowe. Należy zauważyć, że ten Menedżer pamięci jest pierwszym argumentem. 
- Wywołanie zwrotne ClearEntities interfejsu API: Czyści numer jednostki, aby zezwolić użytkownikom na wprowadzanie najbliższej liczby. Ilustruje, jak wywołania interfejsu API można manipulować jednostek.

### <a name="actions"></a>Akcje

Utworzyliśmy cztery akcje. 

![](../media/tutorial12_actions.PNG)

- Oprócz "jakiego numeru chcesz pomnożyć przez 12?" czyli communicative akcji, istnieją trzy różne wywołań interfejsu API, które ilustrują typowe wzorce wywołania interfejsu API.

- RandomGreeting: jest to działanie bez oczekiwania. Aby ustawić to, w oknie dialogowym Tworzenie akcji, firma Microsoft wybrany typ akcji API_LOCAL, a następnie wybrane RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Przycisk Odśwież obok interfejsu API jest używany, gdybyśmy wybrali Zatrzymaj bot, a następnie wprowadź zmiany do interfejsów API. Kliknięcie odświeżania czy wczytać najnowsze zmiany.

Poniżej przedstawiono, jak utworzyliśmy mnożenia akcji: po wybraniu API_Local i interfejsu API, możemy wprowadzić jednostki ($number) pierwszej wartości wejściowej (num1string) i wartości (12) drugiej wartości wejściowych (num2string). Zapewnia to poziom pośredni między bot i tego samego wywołania zwrotnego mogą być mapowane na kilka akcji w systemie i różnią się one w sposób przypisywania akcje liczba wywołań interfejsu API.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Okno dialogowe szkolenie

Przejdźmy teraz przez okno dialogowe nauczania.

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "Cześć".
2. Kliknij wynik akcji.
3. Kliknij, aby wybrać RandomGreeting. Spowoduje to wykonanie wywołania losowe pozdrowienia interfejsu API.
3. Kliknij, aby wybrać "jakiego numeru chcesz pomnożyć przez 12?"
4. Wprowadź "8". Następnie kliknij przycisk wynik akcji.
4. Wybierz pozycję "należy pomnożyć $number 12'. Należy pamiętać, wynik mnożenia.
5. Wybierz pozycję "Wyczyść jednostki".
    - `number` Wartość jednostki został wyczyszczony.
3. Kliknij, aby wybrać "jakiego numeru chcesz pomnożyć przez 12?"
4. Kliknij przycisk Done testowania.

![](../media/tutorial12_dialog.PNG)

Teraz wiesz już sposób rejestrowania wywołań zwrotnych interfejsu API, ich typowych wzorców i jak definiować argumenty i skojarzyć wartości i jednostki w nich.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Karty, część 1](./13-cards-1.md)
