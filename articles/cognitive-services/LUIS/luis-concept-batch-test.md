---
title: Batch test aplikacją usługi LUIS — Azure | Dokumentacja firmy Microsoft
description: Umożliwia testowanie partii stale Praca nad aplikacją, aby dostosować go i zwiększyć jej interpretacji języka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/06/2018
ms.author: v-geberr
ms.openlocfilehash: a51fcc9c0729c18428b51cbdbdf0b6ff13ca1372
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040007"
---
# <a name="batch-testing-in-luis"></a>Testowanie w LUIS usługi Batch

Batch testowania weryfikuje Twoje [active](luis-concept-version.md#active-version) uczonego modelu do pomiaru jego dokładności prognozy. Test usługi batch ułatwia przeglądanie dokładność każdego intencji i jednostek w bieżącym modelu uczonego na wykresie. Przejrzyj wyniki testów usługi batch, aby podjąć odpowiednie działania w celu zwiększenia dokładności, takie jak dodanie więcej wypowiedzi przykład intencji, jeśli aplikacja często nie można zidentyfikować poprawne intencji.

## <a name="group-data-for-batch-test"></a>Dane grupy dla usługi batch testu
Jest ważne, czy jesteś nowym użytkownikiem usługi LUIS wypowiedzi używane do testowania usługi batch. Jeśli masz zestaw wypowiedzi, Podziel wypowiedzi w trzech zestawów: wypowiedzi dodane do intencji, wypowiedzi otrzymane od opublikowanych punktu końcowego i wypowiedzi używany do testów partii usługi LUIS, po jego przygotowaniu. 

## <a name="a-dataset-of-utterances"></a>Zestaw wypowiedzi
Prześlij plik wsadowy w wypowiedzi, znane jako *dataset*, do testowania usługi batch. Zestaw danych jest pliku w formacie JSON zawierającego maksymalnie 1000 etykietą **niepowieloną** wypowiedzi. Można testować maksymalnie 10 zestawów danych w aplikacji. Jeśli potrzebujesz więcej testów, należy usunąć zestaw danych, a następnie dodaj nową.

|**reguły**|
|--|
|* Nie zduplikowane wypowiedzi|
|Żadne elementy podrzędne jednostki hierarchicznych|
|wyrażenia o 1000 lub mniej|

* Duplikaty są traktowane jako zgodne dokładnie taki ciąg znaków, nie dopasowania, które są najpierw stokenizowanego. 

## <a name="entities-allowed-in-batch-tests"></a>Dozwolone w partii testów jednostek
Jednostki zawierają proste, hierarchiczne rodziców i złożone. Wszystkie jednostki z tych typów są wyświetlane w filtrze partii jednostek testów, nawet, jeśli brak odpowiednich jednostek w pliku wsadowym.


<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Format pliku wsadowego
Plik wsadowy składa się z wypowiedzi. Każdy wypowiedź musi mieć oczekiwanego prognozowania intencji, wraz z wszelkimi [maszyny do opanowania jednostek](luis-concept-entity-types.md#types-of-entities) oczekujesz, że zostało wykryte. 

Oto przykład pliku wsadowego za pomocą składni:

```JSON
[
  {
    "text": "Are there any janitorial jobs currently open?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 14,
            "endPos": 23
        }
    ]
  },
  {
    "text": "I would like a fullstack typescript programming with azure job",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 15,
            "endPos": 46
        }
    ]
  },
  {
    "text": "Is there a database position open in Los Colinas?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 11,
            "endPos": 18
        }
    ]
  },
  {
    "text": "Please find database jobs open today in Seattle",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 12,
            "endPos": 19
        }
    ]
  }
]
```

## <a name="batch-syntax-template"></a>Szablon składni partii

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

Plik wsadowy używa **startPos** i **endPos** właściwości należy zwrócić uwagę na początku i na końcu jednostki. Wartości są oparte na zerze i powinna nie rozpocząć lub kończy się spacją. 

To różni się od dzienników zapytań, korzystających z startIndex i endIndex właściwości. 


## <a name="common-errors-importing-a-batch"></a>Typowe błędy importowania partii
Typowe błędy: 

> * Więcej niż 1000 wypowiedzi
> * Wypowiedź obiekt JSON, który nie ma właściwości jednostki
> * Wyrazy z etykietą w wielu jednostkach

## <a name="batch-test-state"></a>Stan testu usługi Batch
Usługa LUIS śledzi stan ostatniego testu każdego zestawu danych. Obejmuje to data ostatniego uruchomienia rozmiar (liczba wypowiedzi w zadaniu wsadowym) i wynik ostatniego (Liczba pomyślnie przewidywane wypowiedzi).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Wyniki testu usługi Batch
Wynik testu usługi batch jest wykres punktowy, znane jako macierz błędu. Ten wykres jest porównanie sposób 4 wypowiedzi w pliku i bieżący model przewidywane intencji i jednostek. 

Punkty danych na **fałszywie dodatnie** i **fałszywie ujemny** sekcje sygnalizowania błędów, które należy zbadać. W przypadku wszystkich punktów danych na **prawdziwie dodatni** i **True ujemna** sekcjach, a następnie dokładność Twojej aplikacji jest doskonałym rozwiązaniem, w tym zestawie danych.

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
