---
title: Funkcje
titleSuffix: Language Understanding - Azure Cognitive Services
description: Dodawanie funkcji do języka modelu w celu dostarczanie wskazówek na temat rozpoznawanie dane wejściowe, które chcesz oznaczyć lub klasyfikowanie.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 7889f223b607912fd88c798b31ec028f97dfbbd6
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522857"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Wyrażenie funkcji listy aplikacją usługi LUIS

W usłudze machine learning *funkcji* wyróżniający cechy lub atrybutu danych, która przestrzega systemu. 

Dodawanie funkcji do języka modelu w celu dostarczanie wskazówek na temat rozpoznawanie dane wejściowe, które chcesz oznaczyć lub klasyfikowanie. Funkcje usługi LUIS rozpoznać intencje i podmioty, funkcje nie są jednak intencji lub jednostek, samodzielnie. Zamiast tego funkcji może zawierać przykłady powiązanych z nimi terminów.  

## <a name="what-is-a-phrase-list-feature"></a>Co to jest funkcja listy fraz?
Lista frazy to lista słów lub fraz, które są istotne dla twojej aplikacji, więc niż inne słowa w wypowiedzi. Lista fraz dodaje do słownictwa używanego w domenie aplikacji jako dodatkowe sygnał do usługi LUIS o tych słów. Usługa LUIS się o jeden z nich jest automatycznie stosowany do pozostałych. Ta lista nie jest zamknięty [listy jednostek](luis-concept-entity-types.md#types-of-entities) tekstu dokładnie odpowiada.

Nie wyświetla frazy ułatwić wynikające, więc trzeba dodać przykłady wypowiedź, które korzystają z różnych wynikające wszelkie istotne słownictwa wyrazów i fraz.

## <a name="phrase-lists-help-all-models"></a>Wyświetla frazy pomocy wszystkie modele

Wyświetla frazy nie są powiązane z określonym celem lub jednostki, ale są dodawane do wszystkich intencje i podmioty jako znaczne zwiększenie wydajności. Jego celem jest poprawa intencji klasyfikacji wykrywania i jednostki.

## <a name="how-to-use-phrase-lists"></a>Jak używać listy fraz

Utwórz listę frazy, gdy aplikacja ma słów i fraz, które są istotne dla aplikacji, takich jak:

* terminów branżowych
* żargonu
* skróty
* języka specyficznego dla firmy
* język, który jest w innym języku, ale często używanych w aplikacji
* słów kluczowych i fraz w swoje wypowiedzi przykład

Po wprowadzeniu kilka słów i fraz, użyj **zaleca się** funkcję, aby znaleźć powiązanych wartości. Przejrzyj powiązane wartości przed dodaniem do wartości listy fraz.

|Typ listy|Przeznaczenie|
|--|--|
|Wymienne|Synonimy lub słowa, po zmianie na innym słowie na liście, mają tego samego intencji i działania funkcji wydobywania podmiotów.|
|-Wymienne|Słownictwa aplikacji specyficzne dla aplikacji, więcej, niż zazwyczaj innych wyrazów, w tym języku.|

### <a name="interchangeable-lists"></a>Wymienne list

*Wymienne* lista frazy to wartości, które są synonimów. Na przykład jeśli chcesz, aby znaleźć wszystkie jednostki wody i wypowiedzi przykład możesz mieć na przykład: 

* Miast, które znajdują się blisko Great Lakes? 
* Jakie drogowej uruchamia wzdłuż Lake Havasu?
* Gdzie nil rozpoczęcia i zakończenia? 

Każdy wypowiedź należy określić jednostki, niezależnie od tego, treści, wody i przeznaczenie: 

* Miast, które znajdują się blisko [bodyOfWater]?
* Jakie drogowej uruchamia wzdłuż [bodyOfWater]?
* Gdzie [bodyOfWater] rozpoczęcia i zakończenia? 

Ponieważ słów i fraz, treść water to samo i mogą być używane zamiennie w wypowiedzi, użyj **wymienne** ustawienia na liście frazę. 

### <a name="non-interchangeable-lists"></a>Wymienne spoza listy

Lista frazy-wymienne jest sygnałem, co zwiększa przywiązanie wykrywania usługi LUIS. Lista frazy wskazuje słów i fraz, które są bardziej znaczące oznacza innych wyrazów. Dzięki temu zarówno zadać wykrywania intencji i jednostki usługi. Na przykład załóżmy, że masz domeny podmiotu, np. podróży, który jest globalne (znaczenie różnych kultur, ale nadal w jednym języku). Brak słów i fraz, które są istotne dla aplikacji, ale nie są tożsame. 

Inny przykład należy użyć listy-wymienne frazy dla rzadko, własności i obce słowa. Usługa LUIS może być nie można rozpoznać rzadkie i zastrzeżonych słów, jak również obce słowa (poza kultura aplikacji). -Wymienne ustawienie wskazuje, że zbiór słów rzadkich formularzy klasę, która LUIS powinien Naucz się rozpoznawać, ale nie są one synonimy lub wymienne ze sobą.

Nie dodać każdy możliwe słowo lub frazę do listy fraz, dodać kilka słów i fraz w czasie, a następnie ponowne szkolenie i publikowania. 

Wraz ze wzrostem natężenia listy fraz wraz z upływem czasu, może się okazać, niektóre terminy ma wiele form (synonimy). Podziel te, na inną listę frazę, która jest wymienne. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Wyrażenie zawiera pomoc zidentyfikować proste jednostki wymienne
Wymienne frazy listy są dobrym sposobem dostrajaniu aplikacją usługi LUIS. Jeśli aplikacja ma problemy z wypowiedzi na intencje prawidłowe przewidywanie lub rozpoznawania jednostek, zastanów się, czy wypowiedzi zawierać słów nietypowe lub słowa, które mogą być niejednoznaczne w znaczenie. Te wyrazy są dobrymi kandydatami do uwzględnienia na liście frazę.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Wyrażenie zawiera pomoc rozpoznać intencje lepsze zrozumienie kontekstu
Lista frazy jest instrukcję do usługi LUIS, aby wykonać dopasowywanie strict lub wszystkie warunki na liście wyrażenie zawsze etykiety, dokładnie tak samo. Jest po prostu wskazówką. Na przykład masz listę frazę, która wskazuje, że "Patti" i "Selma" nazwy, ale usługa LUIS mogą nadal korzystać z informacji kontekstowych, aby rozpoznać, czy ich oznacza coś innego w "Rezerwować 2 w Diner firmy Patti na obiad" i "mnie znaleźć jazdy Wskazówki dojazdu Selma, Gruzja". 

Dodawanie listy fraz jest zamiast dodawać więcej wypowiedzi przykład do intencji. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kiedy należy używać list frazy i listę jednostek
Gdy Lista fraz i listy jednostek może mieć wpływ na wypowiedzi we wszystkich intencji, każdy robi to w inny sposób. Użyj listy fraz wpływa na wynik konwersji prognozy. Użyj jednostki listy wpływa na działania funkcji wydobywania podmiotów dopasowania tekstu do dokładnego dopasowania. 

### <a name="use-a-phrase-list"></a>Użyj listy fraz
Z listą frazy LUIS nadal mogą uwzględniać kontekstu i generalize, aby zidentyfikować elementy, które są podobne do, ale nie dokładne dopasowanie, jako elementów na liście. Jeśli potrzebujesz aplikacją usługi LUIS, aby można było do uogólnienia i identyfikowania nowych elementów w kategorii, użyj listy fraz. 

Rozpoznać nowe wystąpienia jednostki, takie jak harmonogram spotkań, który powinien rozpoznawać nazwy nowych kontaktów lub aplikację magazynu, która powinna rozpoznać nowe produkty, użyj innego typu maszyny do opanowania jednostki, takie jak proste jednostki. Następnie utwórz frazy listę słów i fraz, które pomaga LUIS Znajdź innymi słowy podobne do jednostki. Ta lista zawiera informacje na temat usługi LUIS, rozpoznawał przykłady jednostki, dodając dodatkowe znaczenie wartość tych słów. 

Wyświetla frazy są podobne słownictwa specyficznego dla domeny, które ułatwić udoskonalanie jakości zrozumieć intencje i podmioty. Wspólne użycie listy fraz jest nazwy własne takie jak nazwy miast. Nazwa miasta może być kilka słów, takich jak łączniki lub apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nie należy używać listy fraz 
Jednostka listy jawnie definiuje każda wartość jednostki może potrwać i identyfikuje tylko wartości, które dokładnie pasować. Jednostka listy może być odpowiednie dla aplikacji, w której wszystkie wystąpienia jednostki są znane i nie zmieniają się często. Przykładami są elementy żywności, w menu restauracji, które zmieniają się rzadko. Jeśli potrzebujesz dopasowania tekstu do dokładnego dopasowania jednostki, nie należy używać listy fraz. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj funkcje](luis-how-to-add-features.md) Aby dowiedzieć się więcej na temat sposobu dodawania funkcji do aplikacji usługi LUIS.
