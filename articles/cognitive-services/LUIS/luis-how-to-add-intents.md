---
title: Dodawanie intencji — LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj intencje do aplikacji LUIS, aby zidentyfikować grupy pytań lub poleceń, które mają takie same intencje.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467547"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodawanie intencji w celu określenia zamiaru użytkownika wyrażenia długości

Dodaj [intencje](luis-concept-intent.md) do aplikacji Luis, aby zidentyfikować grupy pytań lub poleceń, które mają ten sam zamiar. 

Intencje są zarządzane z sekcji **kompilacja** górnego paska nawigacyjnego, a następnie od **intencji**panelu po lewej stronie. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Dodawanie intencji

1. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję).

1. W oknie dialogowym **Tworzenie nowego zamiaru** wprowadź nazwę zamierzenia, `GetEmployeeInformation`i kliknij przycisk **gotowe**.

    ![Dodaj cel](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Dodawanie przykładu wypowiedź

Przykład wyrażenia długości to tekstowe przykłady pytań lub poleceń użytkownika. Aby nauczyć się Language Understanding (LUIS), musisz dodać przykład wyrażenia długości do zamiaru.

1. Na stronie Szczegóły zamiaru **GetEmployeeInformation** wprowadź odpowiednie wypowiedź, których oczekuje od użytkowników, takich jak `Does John Smith work in Seattle?` w polu tekstowym poniżej nazwy celu, a następnie naciśnij klawisz ENTER.
 
    ![Zrzut ekranu strony szczegółów intencji z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konwertuje wszystkie wyrażenia długości na małe litery i dodaje spacje wokół tokenów, takich jak łączniki.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Błędy przewidywania intencji 

Przykład wypowiedź w zamiarach może mieć błąd prognozowania intencji między zamiarem, a przykładem wypowiedź jest obecnie w, a intencją przewidywania określoną podczas szkolenia. 

Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji **filtru** opcje **oceny** nieprawidłowe i niejasne połączone z opcjami **Widok** **widoku szczegółowego**. 

![Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji filtrowania.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Gdy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.

![! [Kiedy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Każdy wiersz przedstawia ocenę bieżącego szkolenia dla przykładu wypowiedź, najbliższej oceny Rival, która jest różnicą w tych dwóch wskaźnikach. 

### <a name="fixing-intents"></a>Ustalanie intencji

Aby dowiedzieć się, jak naprawić błędy prognozowania intencji, użyj [pulpitu nawigacyjnego podsumowanie](luis-how-to-use-dashboard.md). Pulpit nawigacyjny podsumowania zawiera analizę dla ostatniego szkolenia wersji aktywnej i oferuje najważniejsze sugestie dotyczące naprawienia modelu.  

## <a name="add-a-custom-entity"></a>Dodawanie jednostki niestandardowej

Po dodaniu wypowiedź do zamiaru można wybrać tekst z wypowiedź, aby utworzyć jednostkę niestandardową. Jednostka niestandardowa to sposób znakowania tekstu do wyodrębnienia wraz z poprawnym zamiarem. 

Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki do wypowiedź](luis-how-to-add-example-utterances.md) .

## <a name="entity-prediction-discrepancy-errors"></a>Błędy niezgodności prognoz jednostek 

Jednostka jest podkreślona na czerwono, aby wskazać [niezgodność przewidywania jednostek](luis-how-to-add-example-utterances.md#entity-status-predictions). Ponieważ jest to pierwsze wystąpienie jednostki, nie ma wystarczającej liczby przykładów dla LUIS, aby uzyskać wysoki poziom pewności, że ten tekst jest oznakowany poprawną jednostką. Ta niezgodność jest usuwana podczas uczenia aplikacji. 

![Zrzut ekranu strony szczegółów intencji, niestandardowa nazwa jednostki wyróżniona na niebiesko](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Tekst zostanie wyróżniony kolorem niebieskim, wskazującym jednostkę.  

## <a name="add-a-prebuilt-entity"></a>Dodaj wstępnie utworzoną jednostkę

Aby uzyskać więcej informacji, zobacz [prekompilowany obiekt Entity](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Korzystanie z kontekstowego paska narzędzi

Po wybraniu na liście jednego lub więcej przykładowych wyrażenia długości, zaznaczając pole z lewej strony wypowiedź, pasek narzędzi powyżej listy wypowiedź umożliwia wykonywanie następujących czynności:

* Zamierzenie ponownego przypisania: Przenieś wypowiedźy do innego celu
* Usuń wypowiedź
* Filtry jednostek: Pokaż tylko wyrażenia długości zawierające obiekty filtrowane
* Pokaż tylko wszystkie/błędy: Pokaż wyrażenia długości z błędami przewidywania lub Pokaż wszystkie wyrażenia długości
* Widok jednostek/tokenów: Pokaż widok jednostek z nazwami jednostek lub Pokaż nieprzetworzony tekst wypowiedź
* Lupa: Wyszukiwanie wyrażenia długości zawierających określony tekst

## <a name="working-with-an-individual-utterance"></a>Praca z indywidualnym wypowiedź

Następujące akcje można wykonać w odniesieniu do poszczególnych wypowiedź z menu wielokropka po prawej stronie wypowiedź:

* Edycja: Zmiana tekstu wypowiedź
* Usuń: Usuń wypowiedź z zamiaru. Jeśli nadal chcesz wypowiedź, lepsza metoda polega na przeniesieniu jej do zamiaru **Brak** . 
* Dodaj wzorzec: wzorzec umożliwia wykonywanie wspólnych wypowiedź oraz oznaczanie tekstu, który można przemieścić i ignorowanie, co zmniejsza potrzebę więcej wyrażenia długościów w zamiarach. 

Kolumna **zamierzenia z etykietą** umożliwia zmianę zamiaru wypowiedź.

## <a name="train-your-app-after-changing-model-with-intents"></a>Uczenie aplikacji po zmianie modelu z intencjami

Po dodaniu, przeprowadzeniu edycji lub usunięciu zamiarów możesz [nauczyć](luis-how-to-train.md) i [opublikować](luis-how-to-publish-app.md) aplikację, aby zmiany zostały zastosowane do zapytań punktów końcowych. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dodawania [przykładowej wyrażenia długości](luis-how-to-add-example-utterances.md) z obiektami. 
