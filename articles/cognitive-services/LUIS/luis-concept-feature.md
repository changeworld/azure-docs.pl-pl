---
title: Funkcje — LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: 1aed6f9a0ceec18ca800e5030ec09bbb8d98cb76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560729"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Funkcje list fraz w aplikacji LUIS

W usłudze machine learning *funkcji* wyróżniający cechy lub atrybutu danych, która przestrzega systemu. 

Dodawanie funkcji do języka modelu w celu dostarczanie wskazówek na temat rozpoznawanie dane wejściowe, które chcesz oznaczyć lub klasyfikowanie. Funkcje usługi LUIS rozpoznać intencje i podmioty, funkcje nie są jednak intencji lub jednostek, samodzielnie. Zamiast tego funkcji może zawierać przykłady powiązanych z nimi terminów.  

## <a name="what-is-a-phrase-list-feature"></a>Co to jest funkcja listy fraz?
Lista fraz jest listą wyrazów lub fraz, które są istotne dla aplikacji, tak samo jak w przypadku innych wyrazów w wyrażenia długości. Lista fraz dodaje do słownictwa domeny aplikacji jako dodatkowy sygnał LUIS o tych słowach. Usługa LUIS się o jeden z nich jest automatycznie stosowany do pozostałych. Ta lista nie jest jednostką zamkniętej [listy](luis-concept-entity-types.md#types-of-entities) z dokładnymi dopasowaniami do tekstu.

Listy fraz nie pomagają w odniesieniu do rdzeni, dlatego należy dodać przykłady wypowiedź, które używają różnych rdzeni dla wszystkich ważnych wyrazów i fraz.

## <a name="phrase-lists-help-all-models"></a>Lista fraz ułatwia wszystkie modele

Listy fraz nie są połączone z konkretnym zamiarem lub jednostką, ale są dodawane jako znaczący wzrost do wszystkich intencji i jednostek. Jego celem jest poprawa wykrywania intencji i klasyfikacji jednostek.

## <a name="how-to-use-phrase-lists"></a>Jak używać listy fraz

Utwórz listę fraz, gdy aplikacja zawiera wyrazy lub frazy, które są ważne dla aplikacji, takie jak:

* warunki branżowe
* żargonu
* skróty
* Język specyficzny dla firmy
* Język pochodzący z innego języka, ale często używany w aplikacji
* kluczowe słowa i frazy w przykładowym wyrażenia długości

Po wprowadzeniu kilku wyrazów lub fraz Użyj opcji **zalecane** , aby znaleźć powiązane wartości. Przejrzyj powiązane wartości przed dodaniem ich do wartości listy fraz.

|Typ listy|Cel|
|--|--|
|Wymienne|Synonimy lub wyrazy, które w przypadku zmiany do innego wyrazu na liście, mają ten sam cel i wyodrębnianie jednostek.|
|Nie zamiennie|Słownictwo aplikacji, charakterystyczne dla aplikacji, inne niż inne słowa w tym języku.|

### <a name="interchangeable-lists"></a>Listy, które są zamienne

Lista  fraz wymiennych jest dla wartości, które są synonimami. Na przykład jeśli chcesz, aby znaleziono wszystkie treści wody i przykład wyrażenia długości: 

* Jakie miasta zbliżają się do wspaniałych jezior? 
* Jakie są działania dotyczące dróg i Lake Havasu?
* Gdzie jest początek i koniec Nile? 

Każdy wypowiedź należy określić dla celów i jednostek, niezależnie od treści wody: 

* Jakie miasta są bliskie [bodyOfWater]?
* Jakie przebiegi drogowe zawiera [bodyOfWater]?
* Gdzie jest początek i koniec [bodyOfWater]? 

Ponieważ słowa lub frazy dla treści wody są synonimami i mogą być używane zamiennie w wyrażenia długości, użyj ustawienia zamiennego na  liście fraz. 

### <a name="non-interchangeable-lists"></a>Listy, które nie są zamienne

Niewymienna lista fraz jest sygnałem, który zwiększa wykrywanie do LUIS. Lista fraz wskazuje wyrazy lub frazy, które są bardziej znaczące. Pomaga to w ustaleniu zamiar i wykrywania jednostek. Załóżmy na przykład, że masz domenę podmiotu, taką jak podróż, która jest globalna (czyli obejmująca między kulturami, ale nadal w jednym języku). Istnieją wyrazy i frazy, które są ważne dla aplikacji, ale nie są synonimami. 

Innym przykładem jest użycie niewymiennej listy fraz dla rzadkich, własnościowych i obcych wyrazów. Usługa LUIS może być nie można rozpoznać rzadkie i zastrzeżonych słów, jak również obce słowa (poza kultura aplikacji). -Wymienne ustawienie wskazuje, że zbiór słów rzadkich formularzy klasę, która LUIS powinien Naucz się rozpoznawać, ale nie są one synonimy lub wymienne ze sobą.

Nie dodawaj każdego możliwego wyrazu lub frazy do listy fraz, Dodaj kilka wyrazów lub fraz jednocześnie, a następnie Przeszkol i Opublikuj. 

Ponieważ lista fraz rośnie wraz z upływem czasu, niektóre terminy mogą zawierać wiele form (synonimy). Podziel je na inną listę wyrazów, która jest zamienna. 

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

Aby można było rozpoznać nowe wystąpienia jednostki, takie jak harmonogram spotkań, który powinien rozpoznawać nazwy nowych kontaktów lub aplikację spisu, która powinna rozpoznawać nowe produkty, należy użyć innego typu jednostki, na którym jest obiektem prostym. Następnie utwórz frazy listę słów i fraz, które pomaga LUIS Znajdź innymi słowy podobne do jednostki. Ta lista zawiera informacje na temat usługi LUIS, rozpoznawał przykłady jednostki, dodając dodatkowe znaczenie wartość tych słów. 

Wyświetla frazy są podobne słownictwa specyficznego dla domeny, które ułatwić udoskonalanie jakości zrozumieć intencje i podmioty. Wspólne użycie listy fraz jest nazwy własne takie jak nazwy miast. Nazwa miasta może być kilka słów, takich jak łączniki lub apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nie należy używać listy fraz 
Jednostka listy jawnie definiuje każda wartość jednostki może potrwać i identyfikuje tylko wartości, które dokładnie pasować. Jednostka listy może być odpowiednie dla aplikacji, w której wszystkie wystąpienia jednostki są znane i nie zmieniają się często. Przykładami są elementy żywności, w menu restauracji, które zmieniają się rzadko. Jeśli potrzebujesz dopasowania tekstu do dokładnego dopasowania jednostki, nie należy używać listy fraz. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj funkcje](luis-how-to-add-features.md) Aby dowiedzieć się więcej na temat sposobu dodawania funkcji do aplikacji usługi LUIS.
