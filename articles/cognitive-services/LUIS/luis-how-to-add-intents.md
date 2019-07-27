---
title: Dodawanie intencji — LUIS
titleSuffix: Azure Cognitive Services
description: Dodawanie intencji z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam intencji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 639079a43e75741d78496939f90324b076b164d5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563694"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodawanie intencji w celu określenia zamiaru użytkownika wyrażenia długości

Dodaj [intencji](luis-concept-intent.md) z aplikacją usługi LUIS do identyfikowania grup pytania lub polecenia, które mają ten sam zamiar. 

Intencji odbywa się na pasku górnym menu nawigacyjnym **kompilacji** sekcji, a następnie w panelu po lewej stronie **intencji**. 

## <a name="add-intent"></a>Dodawanie intencji

1. Na stronie **Intents** (Intencje) wybierz pozycję **Create new intent** (Utwórz nową intencję).

1. W **Utwórz nowy opcję** okna dialogowego wprowadź nazwę elementu intent `GetEmployeeInformation`i kliknij przycisk **gotowe**.

    ![Dodawanie intencji](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Dodaj wypowiedź przykład

Przykład wypowiedzi są przykładami tekst pytania użytkownika lub poleceń. Aby nauczyć Language Understanding (LUIS), należy dodać wypowiedzi przykład do intencji.

1. Na **GetEmployeeInformation** intencji szczegółów wpisz odpowiednie wypowiedź, można oczekiwać od użytkowników, takie jak `Does John Smith work in Seattle?` w polu tekstowym pod nazwę metody konwersji, a następnie naciśnij klawisz Enter.
 
    ![Strona Szczegóły zrzut ekranu z opcjami, z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    Usługa LUIS konwertuje wszystkie wypowiedzi na małe litery i dodaje odstępy dookoła tokenów, takich jak łączniki.

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

## <a name="add-a-custom-entity"></a>Dodaj jednostki niestandardowej

Po dodaniu wypowiedź intencji możesz zaznaczyć tekst z w ramach wypowiedź, aby utworzyć jednostkę niestandardową. Jednostki niestandardowej jest sposobem tekst tag do wyodrębnienia, wraz z poprawną intencji. 

Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki do wypowiedź](luis-how-to-add-example-utterances.md) .

## <a name="entity-prediction-discrepancy-errors"></a>Błędy niezgodności prognozowania jednostki 

Jednostka jest podkreślone na czerwono, aby wskazać [rozbieżność prognozowania jednostki](luis-how-to-add-example-utterances.md#entity-status-predictions). Ponieważ jest to pierwsze wystąpienie jednostki, nie są wystarczająco dużo przykłady dla usługi LUIS mieć o wysokim poziomie pewności oznakowaną ten tekst z poprawną jednostką. Ta niezgodność zostanie usunięta, gdy aplikacja jest uczony. 

![Strona szczegółów zrzut ekranu z opcjami, nazwa jednostki niestandardowej wyróżnione na niebiesko](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Tekst zostanie wyróżniony kolorem niebieskim, wskazując jednostki.  

## <a name="add-a-prebuilt-entity"></a>Dodawanie wstępnie utworzone jednostki

Aby uzyskać informacje, zobacz [wstępnie utworzone jednostki](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Korzystanie z kontekstowych paska narzędzi

Po wybraniu na liście jednego lub więcej przykładowych wyrażenia długości, zaznaczając pole z lewej strony wypowiedź, pasek narzędzi powyżej listy wypowiedź umożliwia wykonywanie następujących czynności:

* Ponowne przypisywanie intencji: przenoszenie utterance(s) do innego zamiaru
* Usuń utterance(s)
* Filtry jednostki: Pokaż tylko wypowiedzi zawierający filtrowane jednostkę
* Pokaż wszystkie / tylko błędy: Pokaż wypowiedzi z błędami prognozy lub Pokaż wszystkie wypowiedzi
* Wyświetl jednostki/tokenów: Pokaż widok jednostek przy użyciu nazwy podmiotu lub pokazać nieprzetworzony tekst wypowiedź
* Lupa: Wyszukaj wypowiedzi zawierających określony tekst

## <a name="working-with-an-individual-utterance"></a>Praca z poszczególnych wypowiedź

Następujące akcje można wykonać na poszczególnych wypowiedź z menu wielokropka z prawej strony wypowiedź:

* Edytuj: Zmień tekst wypowiedź
* Usuń: Usuń wypowiedź z zamiarem. Jeśli chcesz nadal wypowiedź, lepszą metodą jest przenieść ją do **Brak** intencji. 
* Dodaj wzorzec: Wzorzec umożliwia wykonywanie typowych wypowiedź i oznaczanie tekstu, który można przemieścić i ignorowanie tekstu, co zmniejsza potrzebę więcej wyrażenia długości w zamiarach. 

**Etykietą intencji** kolumny pozwala zmienić opcje wypowiedź.

## <a name="train-your-app-after-changing-model-with-intents"></a>Uczenie aplikacji po zmianie modelu z opcjami

Po Dodawanie, edytowanie lub usuwanie intencji, [szkolenie](luis-how-to-train.md) i [publikowania](luis-how-to-publish-app.md) aplikację tak, aby zmiany zostaną zastosowane do endpoint zapytań. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat dodawania [wypowiedzi przykład](luis-how-to-add-example-utterances.md) z jednostkami. 
