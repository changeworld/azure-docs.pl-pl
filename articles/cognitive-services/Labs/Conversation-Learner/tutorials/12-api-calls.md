---
title: Sposób użycia interfejsu API Użyj wywołania z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Informacje o sposobie użycia interfejsu API za pomocą aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ec752cbadfac7a47e08ed7b0ffe8bb475969fac5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348641"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-application"></a>Jak dodać wywołania interfejsu API do aplikacji uczeń konwersacji

W tym samouczku przedstawiono sposób dodawania do aplikacji interfejsu API. Wywołania interfejsu API są funkcje, które definiowanie i zapisywanie w Twojej bot, które można wywołać uczeń konwersacji.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot "tutorialAPICalls.ts".

    npm run tutorial-api-calls

## <a name="details"></a>Szczegóły

- Wywołania API może odczytywać i manipulowania jednostek.
- Wywołania API mają dostęp do pamięci obiekt menedżera.
- Wywołania API mogą również czerpać argumentów — to pozwala na ponowne wykorzystanie to samo wywołanie interfejsu API do obsługi różnych celów.

### <a name="open-the-demo"></a>Otwórz pokaz

Polecenie Samouczek-12-APICalls na liście aplikacji w interfejsie użytkownika sieci web. 

### <a name="entities"></a>Jednostki

W aplikacji o nazwie numer zdefiniowaniu jedną jednostkę.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Wywołania API
Kod dla wywołania interfejsu API jest zdefiniowany w tym pliku: C:\<installedpath\>\src\demos\tutorialAPICalls.ts.

![](../media/tutorial12_apicalls.PNG)

- Pierwsze wywołanie zwrotne interfejsu API jest RandomGreeting. Zwraca losowe pozdrowienia zdefiniowana w zmiennej pozdrowienia.
- Wywołanie zwrotne mnożenia interfejsu API: zostanie mnożenia dwóch liczb podanego przez użytkownika. Zwraca wynik iloczyn dwóch liczb. Oznacza to, że wywołania zwrotne interfejsu API może stać się dane wejściowe. Należy pamiętać, że aby Menedżer pamięci jest pierwszym argumentem. 
- Wywołanie zwrotne interfejsu API ClearEntities: Czyści numer jednostki użytkownikowi należy wprowadzić numer dalej. To przedstawia sposób wywołania interfejsu API można manipulować jednostek.

### <a name="actions"></a>Akcje

Utworzono cztery akcje. 

![](../media/tutorial12_actions.PNG)

- Oprócz "jaki numer chcesz pomnożyć przez 12?" czyli communicative akcji, istnieją trzy różne wywołań interfejsu API ilustrujące typowe wzorce wywołania interfejsu API.

- RandomGreeting: jest akcji bez oczekiwania. Ustawienie typu, w oknie dialogowym Tworzenie akcji, firma Microsoft wybrany typ API_LOCAL akcji, a następnie wybrać RandomGreeting. 

![](../media/tutorial12_setupapicall.PNG)

Przycisk Odśwież obok interfejsu API zostanie użyty, jeśli możemy Aby zatrzymać bot i interfejsy API wprowadzać żadnych zmian. Kliknięcie odświeżania czy wybierz najnowsze zmiany.

Oto jak utworzyliśmy mnożenia akcji: po wybraniu API_Local i interfejsu API, możemy wprowadzić jednostki ($number) pierwszej wartości wejściowej (num1string) i wartości (12) drugiej wartości wejściowych (num2string). Zapewnia to poziom pośredni między bot i wywołuje interfejs API, dlatego tego samego wywołania zwrotnego mogą być mapowane na kilka akcji w systemie i różnią się one na sposób przypisywania akcje.

![](../media/tutorial12_actionmultiply.PNG)

### <a name="train-dialog"></a>Okno dialogowe pociągu

Przejdźmy nauczania okna dialogowego.

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
1. Wprowadź "hi".
2. Kliknij przycisk wynik akcji.
3. Kliknij, aby wybrać RandomGreeting. Powoduje uruchomienie wywołania losowe pozdrowienia interfejsu API.
3. Kliknij, aby wybrać "jakiego numeru chcesz pomnożyć przez 12?"
4. Wprowadź "8". Następnie kliknij przycisk wynik akcji.
4. Wybierz opcję "mnożenia $number 12'. Należy pamiętać, wynik mnożenia.
5. Wybierz pozycję "Wyczyść jednostki".
    - Należy zwrócić uwagę na wartość numeru jednostki został wyczyszczony.
3. Kliknij, aby wybrać "jakiego numeru chcesz pomnożyć przez 12?"
4. Kliknij przycisk Done testowania.

![](../media/tutorial12_dialog.PNG)

Ma teraz przedstawiono sposób rejestrowania wywołań zwrotnych interfejsu API, ich typowych wzorców i sposób definiowania argumentów i skojarzyć wartości i jednostki w nich.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Karty, część 1](./13-cards-1.md)
