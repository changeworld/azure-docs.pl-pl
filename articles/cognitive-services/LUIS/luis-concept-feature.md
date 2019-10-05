---
title: Funkcje — LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj funkcje do modelu języka, aby przedstawić wskazówki dotyczące sposobu rozpoznawania danych wejściowych, które mają być oznaczone etykietami lub klasyfikacją.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dab4b4c6f41a95623a40e5d3fd859f9613afac27
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949595"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funkcje list fraz w aplikacji LUIS

W uczeniu maszynowym *Funkcja* jest odróżnianą cechą lub atrybutem danych, które są przestrzegane przez system. 

Dodaj funkcje do modelu języka, aby przedstawić wskazówki dotyczące sposobu rozpoznawania danych wejściowych, które mają być oznaczone etykietami lub klasyfikacją. Funkcja LUIS rozpoznaje zarówno intencje, jak i jednostki, ale funkcje nie są intencjami lub obiektami. Zamiast tego funkcje mogą zawierać przykłady powiązanych warunków.  

## <a name="what-is-a-phrase-list-feature"></a>Co to jest funkcja listy fraz?
Lista fraz jest listą wyrazów lub fraz, które są istotne dla aplikacji, tak samo jak w przypadku innych wyrazów w wyrażenia długości. Lista fraz dodaje do słownictwa domeny aplikacji jako dodatkowy sygnał LUIS o tych słowach. LUIS informacje o jednym z nich są również automatycznie stosowane do innych. Ta lista nie jest [jednostką](luis-concept-entity-types.md#types-of-entities) zamkniętej listy z dokładnymi dopasowaniami do tekstu.

Listy fraz nie pomagają w odniesieniu do rdzeni, dlatego należy dodać przykłady wypowiedź, które używają różnych rdzeni dla wszystkich ważnych wyrazów i fraz.

## <a name="phrase-lists-help-all-models"></a>Lista fraz ułatwia wszystkie modele

Listy fraz nie są połączone z konkretnym zamiarem lub jednostką, ale są dodawane jako znaczący wzrost do wszystkich intencji i jednostek. Jego celem jest poprawa wykrywania intencji i klasyfikacji jednostek.

## <a name="how-to-use-phrase-lists"></a>Jak używać list fraz

[Utwórz listę fraz](luis-how-to-add-features.md) , gdy aplikacja zawiera wyrazy lub frazy, które są ważne dla aplikacji, takie jak:

* warunki branżowe
* żargonu
* skróty
* Język specyficzny dla firmy
* Język pochodzący z innego języka, ale często używany w aplikacji
* kluczowe słowa i frazy w przykładowym wyrażenia długości

Po wprowadzeniu kilku wyrazów lub fraz Użyj opcji **zalecane** , aby znaleźć powiązane wartości. Przejrzyj powiązane wartości przed dodaniem ich do wartości listy fraz.

Lista fraz jest dla wartości, które są synonimami. Na przykład jeśli chcesz, aby znaleziono wszystkie treści wody i przykład wyrażenia długości: 

* Jakie miasta zbliżają się do wspaniałych jezior? 
* Jakie są działania dotyczące dróg i Lake Havasu?
* Gdzie jest początek i koniec Nile? 

Każdy wypowiedź należy określić dla celów i jednostek, niezależnie od treści wody: 

* Jakie miasta są bliskie [bodyOfWater]?
* Jakie przebiegi drogowe zawiera [bodyOfWater]?
* Gdzie jest początek i koniec [bodyOfWater]? 

Ponieważ słowa lub frazy dla treści wody są synonimami i mogą być używane zamiennie w wyrażenia długości. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Listy fraz pomagają identyfikować proste, wymienne jednostki
Wymienne listy fraz to dobry sposób na dostosowanie wydajności aplikacji LUIS. Jeśli aplikacja ma problemy z przewidywaniami wyrażenia długości do poprawnego zamiaru lub rozpoznawania jednostek, należy zastanowić się, czy wyrażenia długości zawierają nietypowe słowa lub wyrazy, które mogą być niejednoznaczne w znaczeniu. Słowa te są dobrymi kandydatami do uwzględnienia na liście fraz.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Listy fraz pomagają identyfikować intencje przez lepsze zrozumienie kontekstu
Lista fraz nie jest instrukcją LUIS, aby wykonać ścisłe dopasowanie lub zawsze etykieta wszystkie warunki na liście fraz dokładnie takie same. Jest po prostu wskazówką. Na przykład można mieć listę wyrazów, która wskazuje, że nazwy "Patti" i "Selma" są takie same, ale LUIS nadal mogą używać informacji kontekstowych do rozpoznawania, że oznaczają coś innego w "rezerwacji dla 2 na obiadie przez Patti Diner" i "Znajdź mnie Wskazówki dotyczące Selma, Georgia ". 

Dodawanie listy fraz jest alternatywą dla dodawania więcej przykładowych wyrażenia długości do zamiaru. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kiedy używać list fraz w stosunku do jednostek listy
Mimo że zarówno Lista wyrazów, jak i [jednostki listy](reference-entity-list.md) mogą mieć wpływ wyrażenia długości na wszystkie intencje, każda robi się to w inny sposób. Użyj listy frazy, aby wpływać na ocenę celu. Użyj jednostki listy, aby wpływać na wyodrębnianie jednostek pod kątem dokładnego dopasowania tekstu. 

### <a name="use-a-phrase-list"></a>Korzystanie z listy fraz
Za pomocą listy wyrazów LUIS może nadal korzystać z konta i uogólniać, aby identyfikować elementy, które są podobne do, ale nie dokładne dopasowanie jako elementy na liście. Jeśli potrzebujesz aplikacji LUIS, aby móc uogólniać i identyfikować nowe elementy w kategorii, Użyj listy fraz. 

Aby można było rozpoznać nowe wystąpienia jednostki, takie jak harmonogram spotkań, który powinien rozpoznawać nazwy nowych kontaktów lub aplikację spisu, która powinna rozpoznawać nowe produkty, należy użyć innego typu jednostki, na którym jest obiektem prostym. Następnie utwórz listę wyrazów i frazy, które ułatwiają LUIS znalezienie innych wyrazów podobnych do jednostki. Ta lista przedstawia LUIS do rozpoznawania przykładów jednostki przez dodanie dodatkowego znaczenia do wartości tych wyrazów. 

Listy fraz są podobne do słownictwa specyficznego dla domeny, który ułatwia zwiększenie jakości wiedzy o intencjach i jednostkach. Typowym użyciem listy fraz są poprawne rzeczowniki, takie jak nazwy miast. Nazwa miasta może zawierać kilka wyrazów, w tym łączniki lub apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nie używaj listy fraz 
Jednostka listy jawnie definiuje każdą wartość, którą może wykonać jednostka, i identyfikuje tylko te wartości, które dokładnie pasują do siebie. Jednostka listy może być odpowiednia dla aplikacji, w której są znane wszystkie wystąpienia jednostki i nie zmieniają się często. Przykłady to elementy żywności w menu restauracji, które rzadko zmieniają się. Jeśli potrzebujesz dokładnego dopasowania tekstu do jednostki, nie używaj listy fraz. 

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie funkcji](luis-how-to-add-features.md) , aby dowiedzieć się więcej na temat dodawania funkcji do aplikacji Luis.
