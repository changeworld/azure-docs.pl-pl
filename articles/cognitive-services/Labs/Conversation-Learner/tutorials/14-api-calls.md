---
title: Jak używać interfejsu API wywołań za pomocą modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać interfejsu API przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f7c7c72703d7c3134dd2acdcc466fc0182fa38a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389935"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Jak dodać wywołań interfejsu API modelu uczeń konwersacji

W tym samouczku przedstawiono sposób dodawania wywołań interfejsu API do modelu. Wywołania interfejsu API są funkcje, które zdefiniować i zapisać w Twoim Czatbocie oraz tych, które można wywołać uczeń konwersacji.

## <a name="video"></a>Połączenia wideo

[![Wersja zapoznawcza samouczek wywołań interfejsu API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że bot "tutorialAPICalls.ts" jest uruchomiona.

    npm run tutorial-api-calls

## <a name="details"></a>Szczegóły

- Wywołania interfejsu API może odczytywać i modyfikować jednostek.
- Wywołania interfejsów API mają dostęp do obiektu Menedżera pamięci.
- Wywołania interfejsu API może potrwać argumentów.

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

W internetowym interfejsie użytkownika kliknij pozycję "Importuj samouczki" i wybierz model o nazwie "Samouczek-14-APICalls".

### <a name="entities"></a>Jednostki

Zdefiniowaliśmy jednej jednostki w modelu o nazwie `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Wywołania interfejsu API
Kod dla wywołań interfejsu API jest zdefiniowany w tym pliku: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- `RandomGreeting` Wywołania zwrotnego zwraca losowe pozdrowienia zdefiniowane w `greeting` tablicy.
- `Multiply` Wywołania zwrotnego zostanie mnożenia dwóch liczb przekazanego przez akcję, która wywołuje ona i zwraca wynik, który może być renderowany w interfejsie użytkownika.
    - Zwróć uwagę, że ten Menedżer pamięci jest pierwszym argumentem. 
    - Należy zauważyć, że wywołania zwrotne interfejsu API może stać się wielu danych wejściowych, w tym przypadku `num1string` i `num2string`.
- `ClearEntities` Wywołania zwrotnego wyczyszczone, liczba jednostek co użytkownik może wprowadzić inny numer. 
    - Ilustruje sposób wywołania interfejsu API można manipulować jednostek.

### <a name="actions"></a>Akcje
Utworzyliśmy cztery akcje. Trzy z nich to operacje interfejsu API "Non-Wait", z czwarty jest akcja "Text", która prosi użytkownika pytania podobne do Zaobserwowaliśmy w innych samouczkach. Aby zobaczyć, jak każdy został utworzony, wykonaj następujące czynności:
1. W lewym panelu kliknij przycisk "Akcje", a następnie kliknij jedną z czterech akcji wymienionych w siatce.
2. Zwróć uwagę na wartości każdego pola w formularzu, które się pojawi.
3. Zwróć uwagę `Refresh` przycisk obok pola interfejsu API.
    - Gdybyśmy wybrali zatrzymać Bot i zmienić do interfejsów API, gdy działa na stronie interfejsu użytkownika, a następnie kliknięcie `Refresh` przycisk, aby wczytać najnowsze zmiany.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, należy pomnożyć i RandomGreeting
Wszystkie trzy te akcje są typem interfejsu API. Każdy korzystają z interfejsu API funkcji wywołania zwrotnego do wykonania pewnej pracy i być może zwracać wartości będą prezentowane użytkownikowi.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Typ numeru chcesz pomnożyć przez 12"
Jest to akcja "Text" i po prostu zadaje pytanie użytkownika. Podczas tej akcji nie faktycznie wchodzić w interakcję z jednym z wywołań zwrotnych interfejsu API, monituje użytkowników na odpowiedź z numerem, które zostaną umieszczone w pamięci jednostki, która może być następnie używane przez "Mnożenia" akcję, która użyj jednej z wywołań zwrotnych interfejsu API.


### <a name="train-dialog"></a>Okno dialogowe szkolenie

Przejdźmy teraz przez "Szkolenia Dialog".

1. Na lewym panelu kliknij `Train Dialogs`, a następnie `New Train Dialog` przycisku.
2. Wpisz "hello".
3. Kliknij przycisk `Score Actions`.
4. Wybierz pozycję `RandomGreeting`. 
    - Spowoduje to wykonanie wywołania losowe pozdrowienia interfejsu API.
    - To nie będzie czekać na odpowiedź użytkownika.
5. Wybierz pozycję `What number to do you want to multiply by 12?`
6. Wpisz liczbę i dowolną liczbę tylko liczbę.
    - Należy zauważyć, że Twój numer została automatycznie oznaczona jako `number` jednostki.
7. Kliknij przycisk `Score Actions`.
8. Wybierz `Multiply` akcji.
    - Zwróć uwagę, wynik mnożenia przez 12.
    - Zwróć uwagę że pamięć jest nadal zawiera wartość wprowadzona dla `number`
9. Wybierz `ClearEntities` akcji.
    - Należy zauważyć, że wartość jednostki `number` został wyczyszczony z pamięci.
10. Kliknij przycisk `Save`.

Teraz wiesz już sposób rejestrowania wywołań zwrotnych interfejsu API, ich typowych wzorców i jak definiować argumenty i skojarzyć wartości i jednostki w nich.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Karty, część 1](./15-cards.md)
