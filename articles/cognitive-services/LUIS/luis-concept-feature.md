---
title: Omówienie funkcji usługi LUIS aplikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o funkcjach, które pomagają poprawić wydajność aplikacji usługi LUIS. Funkcje obejmują list frazy i wzorców wyrażeń regularnych rozpoznawania.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 597948947303b7fdf16f24576620d6f39d7c51f4
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887450"
---
# <a name="phrase-list-features-in-luis"></a>Wyrażenie funkcji listy usługi LUIS

W usłudze machine learning *funkcji* wyróżniający cechy lub atrybutu danych, która przestrzega systemu. 

Dodawanie funkcji do języka modelu w celu dostarczanie wskazówek na temat rozpoznawanie dane wejściowe, które chcesz oznaczyć lub klasyfikowanie. Funkcje usługi LUIS rozpoznać intencje i podmioty, funkcje nie są jednak intencji lub jednostek, samodzielnie. Zamiast tego funkcji może zawierać przykłady powiązanych z nimi terminów.  

## <a name="what-is-a-phrase-list-feature"></a>Co to jest funkcja listy fraz?
Lista fraz obejmuje grupy wartości (słów i fraz), które należą do tej samej klasy i muszą być traktowane w podobny sposób (na przykład nazwy miasta lub produktów). Usługa LUIS się o jeden z nich jest automatycznie stosowany do pozostałych. To nie jest zamknięty [listy jednostek](luis-concept-entity-types.md#types-of-entities) (dokładne dopasowania tekstu) dopasowane słów.

Lista fraz dodaje do słownictwa używanego w domenie aplikacji jako drugi sygnał do usługi LUIS o tych słów.

## <a name="how-to-use-phrase-lists"></a>Jak używać listy fraz
W aplikacji agenta podróży Utwórz listę frazy o nazwie "Miast", która zawiera wartości, Londyn, Paryż i Kair. Jeśli etykieta jedną z następujących wartości jako jednostki prostej w [wypowiedź przykład](luis-how-to-add-example-utterances.md#add-simple-entity-label) w intencji, LUIS uczy się rozpoznawać, pozostałe. 

Lista wyrażenie może być zamienne lub -wymienne. *Wymienne* lista frazy to wartości, które są synonimy, a *-wymienne* frazy lista jest przeznaczona dla wartości, które nie są synonimy, ale są podobne w inny sposób. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Wyrażenie zawiera pomoc identyfikowania jednostki exchangeable proste
Fraza Exchangeable listy są dobrym sposobem dostrajaniu aplikacją usługi LUIS. Jeśli aplikacja ma problemy z wypowiedzi na intencje prawidłowe przewidywanie lub rozpoznawania jednostek, zastanów się, czy wypowiedzi zawierać słów nietypowe lub słowa, które mogą być niejednoznaczne w znaczenie. Te wyrazy są dobrymi kandydatami do uwzględnienia na liście frazę.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Wyrażenie zawiera pomoc rozpoznać intencje lepsze zrozumienie kontekstu
Wyświetla frazy na użytek rzadkie, własności i obce słowa. Usługa LUIS może być nie można rozpoznać rzadkie i zastrzeżonych słów, jak również obce słowa (poza kultura aplikacji) i dlatego powinna być dodana do listy fraz. Ta lista frazy powinna być oznaczona jako innego niż wymienne, aby wskazać, że zbiór słów rzadkich formularzy klasę, która LUIS powinien Naucz się rozpoznawać, ale nie są one synonimy lub wymienne ze sobą.

Lista frazy jest instrukcję do usługi LUIS, aby wykonać dopasowywanie strict lub wszystkie warunki na liście wyrażenie zawsze etykiety, dokładnie tak samo. Jest po prostu wskazówką. Na przykład masz listę frazę, która wskazuje, że "Patti" i "Selma" nazwy, ale usługa LUIS mogą nadal korzystać z informacji kontekstowych, aby rozpoznać, czy ich oznacza coś innego w "Rezerwować 2 w Diner firmy Patti na obiad" i "mnie znaleźć jazdy Wskazówki dojazdu Selma, Gruzja". 

Dodawanie listy fraz jest zamiast dodawać więcej wypowiedzi przykład do intencji. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kiedy należy używać list frazy i listę jednostek
Gdy Lista fraz i listy jednostek może mieć wpływ na wypowiedzi we wszystkich intencji, każdy robi to w inny sposób. Użyj listy fraz wpływa na wynik konwersji prognozy. Użyj jednostki listy wpływa na działania funkcji wydobywania podmiotów dopasowania tekstu do dokładnego dopasowania. 

### <a name="use-a-phrase-list"></a>Użyj listy fraz
Z listą frazy LUIS nadal mogą uwzględniać kontekstu i generalize, aby zidentyfikować elementy, które są podobne do, ale nie dokładne dopasowanie, jako elementów na liście. Jeśli potrzebujesz aplikacją usługi LUIS, aby można było do uogólnienia i identyfikowania nowych elementów w kategorii, użyj listy fraz. 

Aby rozpoznawać nowych wystąpień jednostki, takie jak harmonogram spotkań, który powinien rozpoznawać nazwy nowych kontaktów lub aplikację magazynu, która powinna rozpoznać nowe produkty używać innego typu maszyny do opanowania jednostki, takie jak prosty lub Jednostka hierarchicznej. Następnie utwórz frazy listę słów i fraz, które pomaga LUIS Znajdź innymi słowy podobne do jednostki. Ta lista zawiera informacje na temat usługi LUIS, rozpoznawał przykłady jednostki, dodając dodatkowe znaczenie wartość tych słów. 

Wyświetla frazy są podobne słownictwa specyficznego dla domeny, które ułatwić udoskonalanie jakości zrozumieć intencje i podmioty. Wspólne użycie listy fraz jest nazwy własne takie jak nazwy miast. Nazwa miasta może być kilka słów, takich jak łączniki lub apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nie należy używać listy fraz 
Jednostka listy jawnie definiuje każda wartość jednostki może potrwać i identyfikuje tylko wartości, które dokładnie pasować. Jednostka listy może być odpowiednie dla aplikacji, w której wszystkie wystąpienia jednostki są znane i nie zmieniają się często, takich jak elementy żywności, w menu restauracji, które zmieniają się rzadko. Jeśli potrzebujesz dopasowania tekstu do dokładnego dopasowania jednostki, nie należy używać listy fraz. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się, [najlepsze praktyki](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj funkcje](luis-how-to-add-features.md) Aby dowiedzieć się więcej na temat sposobu dodawania funkcji do aplikacji usługi LUIS.
