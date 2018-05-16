---
title: Pozorowanie odpowiedzi interfejsu API za pomocą witryny Azure Portal | Microsoft Docs
description: Ten samouczek pokazuje, jak za pomocą usługi API Management (APIM) ustawić zasady dla interfejsu API, tak aby zwracał on pozorowane odpowiedzi. Ta metoda pozwala deweloperom na kontynuowanie implementowania i testowania wystąpienia usługi API Management w sytuacji, gdy nie ma dostępnego zaplecza wysyłającego prawdziwe odpowiedzi.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4383ce3788f6fade5299d69ef99b80221c58d9e7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="mock-api-responses"></a>Pozorowanie odpowiedzi interfejsu API

Interfejsy API mogą zostać zaimportowane do interfejsu API usługi APIM lub utworzone i zarządzane ręcznie. Kroki opisane w tym samouczku pokazują, jak za pomocą usługi APIM utworzyć pusty interfejs API i zarządzać nim ręcznie. Samouczek pokazuje, jak ustawić zasady dla interfejsu API, aby zwracał pozorowane odpowiedzi. Ta metoda pozwala deweloperom na kontynuowanie implementowania i testowania wystąpienia usługi APIM nawet wtedy, gdy nie ma dostępnego zaplecza wysyłającego prawdziwe odpowiedzi. Możliwość pozorowania odpowiedzi może być przydatna w kilku scenariuszach:

+ Kiedy najpierw projektowana jest fasada interfejsu API, a implementacja zaplecza powstaje później. Lub kiedy zaplecze jest opracowywane równolegle.
+ Gdy zaplecze tymczasowo nie działa lub nie można go przeskalować.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie testowego interfejsu API 
> * Dodawanie operacji do testowego interfejsu API
> * Włączanie pozorowania odpowiedzi
> * Testowanie pozorowanego interfejsu API

![Pozorowana odpowiedź operacji](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tworzenie testowego interfejsu API 

Kroki opisane w tej sekcji pokazują, jak utworzyć pusty interfejs API bez zaplecza. W tej sekcji pokazano również, jak dodać operację do interfejsu API. Wywołanie operacji po wykonaniu kroków w tej sekcji powoduje błąd. Błędów nie będzie po ukończeniu kroków opisanych w sekcji „Włączanie pozorowania odpowiedzi”.

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Z menu po lewej stronie wybierz pozycję **+ Dodaj interfejs API**.
3. Z listy wybierz pozycję **Pusty interfejs API**.
4. W polu **Nazwa wyświetlana** wprowadź tekst „*Testowy interfejs API*”.
5. W polu **Produkty** wprowadź wartość „*Bez ograniczeń*”.
6. Wybierz pozycję **Utwórz**.

## <a name="add-an-operation-to-the-test-api"></a>Dodawanie operacji do testowego interfejsu API

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Kliknij przycisk **+ Dodaj operację**.

    ![Pozorowana odpowiedź operacji](./media/mock-api-responses/mock-api-responses02.png)

    |Ustawienie|Wartość|Opis|
    |---|---|---|
    |**Adres URL** (czasownik HTTP)|GET|Możesz wybrać jeden z wstępnie zdefiniowanych czasowników HTTP.|
    |**Adres URL** |*/test*|Ścieżka adresu URL dla interfejsu API. |
    |**Nazwa wyświetlana**|*Wywołanie testowe*|Nazwa, która jest wyświetlana w **portalu deweloperów**.|
    |**Opis**||Podaj opis operacji, który będzie służyć do zapewnienia dokumentacji dla deweloperów używających tego interfejsu API w **portalu deweloperów**.|
    |Karta **Zapytanie**||Pozwala dodać parametry zapytania. Oprócz podawania nazwy i opisu możesz podać wartości, które mogą być przypisane do tego parametru. Jedna z wartości może być oznaczona jako domyślna (opcjonalnie).|
    |Karta **Żądanie**||Pozwala zdefiniować typy zawartości żądania, przykłady i schematy. |
    |Karta **Odpowiedź**|Zobacz kroki pod tą tabelą.|Zdefiniuj kody stanów odpowiedzi, typy zawartości, przykłady i schematy.|

3. Wybierz kartę **Odpowiedź** znajdującą się pod adresem URL, nazwą wyświetlaną i opisem pola.
4. Kliknij pozycję **+ Dodaj odpowiedź**.
5. Wybierz z listy pozycję **200 OK**.
6. Pod nagłówkiem **Reprezentacje** po prawej stronie wybierz pozycję **+ Dodaj reprezentację**.
7. Wprowadź tekst „*application/json*” w polu wyszukiwania i wybierz typ zawartości **application/json**.
8. W polu tekstowym **Przykład** wprowadź tekst „*{ 'sampleField' : 'test' }*”.
9. Wybierz pozycję **Zapisz**.

## <a name="enable-response-mocking"></a>Włączanie pozorowania odpowiedzi

1. Wybierz interfejs API utworzony w kroku „Tworzenie testowego interfejsu API”.
2. Wybierz dodaną operację testową.
2. W oknie po prawej stronie kliknij kartę **Projekt**.
3. W oknie **Przychodzące przetwarzanie** kliknij ikonę ołówka.
4. Na karcie **Pozorowanie** wybierz opcję **Odpowiedzi statyczne** dla wartości **Zachowanie pozorowane**.
5. W polu tekstowym **API Management returns the following response:** (Usługa API Management zwraca następującą odpowiedź:) wpisz tekst **200 OK, application/json**. Taki wybór oznacza, że Twój interfejs API powinien zwrócić przykład odpowiedzi zdefiniowany w poprzedniej sekcji.
6. Wybierz pozycję **Zapisz**.

## <a name="test-the-mocked-api"></a>Testowanie pozorowanego interfejsu API

1. Wybierz interfejs API utworzony w kroku „Tworzenie testowego interfejsu API”.
2. Otwórz kartę **Test**.
3. Upewnij się, że wybrany jest interfejs API **Wywołanie testowe**.

    > [!TIP]
    > Żółty pasek z tekstem **Mocking is enabled** (Pozorowanie jest włączone) wskazuje, że odpowiedzi zostały zwrócone z usługi API Management zgodnie z zasadami pozorowania, a nie z rzeczywistego zaplecza.

3. Wybierz pozycję **Wyślij**, aby wykonać wywołanie testowe.
4. W obszarze **Odpowiedź HTTP** zostanie wyświetlony kod JSON podany jako przykład w pierwszej sekcji tego samouczka.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie testowego interfejsu API
> * Dodawanie operacji do testowego interfejsu API
> * Włączanie pozorowania odpowiedzi
> * Testowanie pozorowanego interfejsu API

Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
