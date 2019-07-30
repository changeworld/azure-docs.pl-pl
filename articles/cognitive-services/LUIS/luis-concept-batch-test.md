---
title: Testy wsadowe — LUIS
titleSuffix: Azure Cognitive Services
description: Umożliwia testowanie partii stale Praca nad aplikacją, aby dostosować go i zwiększyć jej interpretacji języka.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b962fc32cdcde0509cfa60d105022bb208633ae3
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639290"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Testowanie wsadowe przy użyciu 1000 wyrażenia długości w portalu LUIS

Batch testowania weryfikuje Twoje [active](luis-concept-version.md#active-version) uczonego modelu do pomiaru jego dokładności prognozy. Test wsadowy ułatwia przeglądanie dokładności poszczególnych założeń i jednostek w bieżącym przeszkolonym modelu, wyświetlając wyniki z wykresem. Przejrzyj wyniki testów usługi batch, aby podjąć odpowiednie działania w celu zwiększenia dokładności, takie jak dodanie więcej wypowiedzi przykład intencji, jeśli aplikacja często nie można zidentyfikować poprawne intencji.

## <a name="group-data-for-batch-test"></a>Dane grupy dla usługi batch testu

Jest ważne, czy jesteś nowym użytkownikiem usługi LUIS wypowiedzi używane do testowania usługi batch. Jeśli masz zestaw danych wyrażenia długości, Podziel wyrażenia długości na trzy zestawy: przykład wyrażenia długości dodany do intencji, wyrażenia długości otrzymany z opublikowanego punktu końcowego i wyrażenia długości używany do wsadowego testowania LUIS po jego przeszkoleniu. 

## <a name="a-data-set-of-utterances"></a>Zestaw danych wyrażenia długości

Prześlij plik wsadowy wyrażenia długości, nazywany *zestawem danych*, na potrzeby testowania wsadowego. Zestaw danych jest plikiem w formacie JSON zawierającym maksymalnie 1 000 oznaczony niezduplikowaną wyrażenia długości . W aplikacji można testować do 10 zestawów danych. Jeśli chcesz przetestować więcej, Usuń zestaw danych, a następnie Dodaj nowy.

|**reguły**|
|--|
|\* Nie zduplikowane wypowiedzi|
|wyrażenia o 1000 lub mniej|

\* Duplikaty są traktowane jako zgodne dokładnie taki ciąg znaków, nie dopasowania, które są najpierw stokenizowanego. 

## <a name="entities-allowed-in-batch-tests"></a>Dozwolone w partii testów jednostek

Wszystkie jednostki niestandardowe znajdą się w modelu są wyświetlane w filtrze partii jednostek testów, nawet, jeśli brak odpowiednich jednostek w pliku danych usługi batch.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Format pliku wsadowego

Plik wsadowy składa się z wypowiedzi. Każdy wypowiedź musi mieć oczekiwanego prognozowania intencji, wraz z wszelkimi [maszyny do opanowania jednostek](luis-concept-entity-types.md#types-of-entities) oczekujesz, że zostało wykryte. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Szablon składni usługi Batch dla intencji z jednostkami

Uruchom plik wsadowy za pomocą następującego szablonu:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Plik wsadowy używa **startPos** i **endPos** właściwości należy zwrócić uwagę na początku i na końcu jednostki. Wartości są oparte na zerze i powinna nie rozpocząć lub kończy się spacją. To różni się od dzienników zapytań, korzystających z startIndex i endIndex właściwości. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Szablon składni usługi Batch dla intencji bez jednostek

Użyj następującego szablonu, aby uruchomić plik wsadowy bez jednostek:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Jeśli nie chcesz testować jednostek, Dołącz `entities` Właściwość i ustaw wartość jako pustą `[]`tablicę.


## <a name="common-errors-importing-a-batch"></a>Typowe błędy importowania partii

Typowe błędy: 

> * Więcej niż 1000 wypowiedzi
> * Obiekt JSON wypowiedź, która nie ma właściwości jednostki. Właściwość może być pustą tablicą.
> * Wyrazy z etykietą w wielu jednostkach
> * Etykieta jednostki rozpoczęcia lub zakończenia w miejscu.

## <a name="batch-test-state"></a>Stan testu usługi Batch

LUIS śledzi stan ostatniego testu zestawu danych. Obejmuje to data ostatniego uruchomienia rozmiar (liczba wypowiedzi w zadaniu wsadowym) i wynik ostatniego (Liczba pomyślnie przewidywane wypowiedzi).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Wyniki testu usługi Batch

Wynik testu usługi batch jest wykres punktowy, znane jako macierz błędu. Ten wykres jest porównanie sposób 4 wypowiedzi w pliku wsadowego i celem przewidywane bieżącego modelu oraz jednostki. 

Punkty danych na **fałszywie dodatnie** i **fałszywie ujemny** sekcje sygnalizowania błędów, które należy zbadać. Jeśli wszystkie punkty danych znajdują się w sekcjach **prawdziwe pozytywne** i **prawdziwe** , dokładność Twojej aplikacji jest idealna dla tego zestawu danych.

![Cztery sekcje wykresu](./media/luis-concept-batch-test/chart-sections.png)

Ten wykres ułatwia znajdowanie wypowiedzi przewiduje LUIS niepoprawnie oparte na jego bieżący szkolenia. Wyniki są wyświetlane na region wykresu. Wybierać poszczególne punkty na wykresie, aby przejrzeć informacje o wypowiedź lub wybierz nazwę region, aby przejrzeć wyniki wypowiedź w danym regionie.

![Testowanie wsadowe](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Błędy w wynikach

Błędy w teście partii wskazują intencji, które nie są przewidzieć, jak wspomniano w pliku wsadowym. Błędy są oznaczone w sekcjach czerwony wykresu. 

Sekcji dodatnią wartość false wskazuje, że wypowiedź dopasowane przeznaczenie lub jednostki, gdy go nie powinny mieć. Ujemna wartość false wskazuje, że wypowiedź niezgodny przeznaczenie lub jednostki usługi podczas powinien mieć. 

## <a name="fixing-batch-errors"></a>Naprawianie błędów usługi batch

Jeśli występują błędy podczas badania usługi batch, możesz dodać więcej wypowiedzi do intencji lub etykiety więcej wypowiedzi o jednostkę którą chcesz pomóc upewnić rozróżnić intencje usługi LUIS. Jeśli masz dodano wypowiedzi i oznaczone get je i nadal prognozowania błędy podczas badania usługi batch, Rozważ dodanie [listy fraz](luis-concept-feature.md) funkcję za pomocą słownika specyficznego dla domeny do pomocy usługi LUIS uczysz się szybciej. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [test partii](luis-how-to-batch-test.md)
