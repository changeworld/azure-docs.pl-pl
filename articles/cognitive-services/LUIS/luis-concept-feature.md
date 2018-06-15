---
title: Zrozumieć funkcje w aplikacjach LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o funkcjach, które pomagają zwiększyć wydajność aplikacji LUIS. Funkcje programu to wyrażenie listy i wzorce rozpoznawania wyrażeń regularnych.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356379"
---
# <a name="phrase-list-features-in-luis"></a>Wyrażenie funkcji listy w LUIS

W uczeniu maszynowym *funkcji* wyróżniającą cechy lub atrybut danych obserwujące systemu. 

Dodawanie funkcji do modelu języka zapewnienie podpowiedź rozpoznawanie chcesz etykietę lub klasyfikowania danych wejściowych. Funkcje pomocy LUIS rozpoznawać intencje jednostek, ale funkcje nie są intencje lub jednostek samodzielnie. Zamiast tego funkcji może zawierać przykłady powiązane z nią postanowienia.  

## <a name="what-is-a-phrase-list-feature"></a>Co to jest funkcja listy frazy?
Lista fraz znajduje się grupa wartości (słów ani fraz), które należą do tej samej klasy i muszą być traktowane podobnie (na przykład nazwy miasta lub produktów). Co LUIS uzyskuje informacje o jeden z nich jest automatycznie stosowana do pozostałych. To nie jest zamkniętej [listy jednostki](luis-concept-entity-types.md#types-of-entities) (dokładnie tekstu) dopasowane słów.

Lista fraz dodaje do słownika domeny aplikacji jako drugi sygnał LUIS o te słowa.

## <a name="how-to-use-phrase-lists"></a>Jak używać list frazy
W aplikacji agenta podróży należy utworzyć listę frazy o nazwie "Miast", która zawiera wartości w Londynie, Paryża i Kair. Jeśli etykieta jedną z następujących wartości jako jednostki prostej w [utterance przykład](luis-how-to-add-example-utterances.md#add-simple-entity-label) w zamiar LUIS uzyskuje informacje o innych rozpoznać. 

Lista fraz może być zamienne lub -wymienne. *Wymienne* jest wyrażenie listy wartości, które są synonimy, a *-wymienne* frazy lista jest przeznaczona dla wartości, które nie są synonimy, ale są podobne w inny sposób. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Wyrażenie zawiera pomoc identyfikowanie jednostek prostych skryptów
Wymienne frazy listy są dobrym sposobem dostrajania wydajności aplikacji LUIS. Jeśli aplikacja ma problemy przewidywania zniesławiających do poprawne zamiar lub rozpoznawania jednostek, należy zastanowić czy zniesławiających zawierać słów nietypowe lub słowa, które mogą być niejednoznaczny w rozumieniu. Te słowa, które dobrze nadają się do uwzględnienia na liście frazę.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Wyrażenie zawiera pomoc zidentyfikować intencje przez lepsze zrozumienie kontekstu
Użyj frazy list rzadkich zastrzeżonych i obcego wyrazów. LUIS może być nie można rozpoznać słowa obce (poza kultury aplikacji), a także rzadkie i zastrzeżonych słów i w związku z tym powinny zostać dodane do listy frazę. Ta lista frazy powinna być oznaczona jako nie wymienne, aby wskazać, że zestaw rzadkich wyrazów formularzy klasę, która LUIS powinien Dowiedz się, jak rozpoznać, ale nie są one synonimy lub wymienne ze sobą.

Lista fraz nie jest instrukcję LUIS wykonać dopasowywanie strict lub wszystkie warunki na liście wyrażenie zawsze etykietę, dokładnie tak samo. Jest po prostu wskazówkę. Na przykład mogą być listę frazy, która wskazuje, że "Patti" i "Selma" nazwy, ale do rozpoznaje, że ich znaczenie inny "Rezerwować 2 na jego Patti Diner na obiad" i "pięć me zwiększają LUIS można nadal używać informacje kontekstowe instrukcje Selma Georgia". 

Dodawanie listy frazy stanowi alternatywę do dodawania więcej zniesławiających przykład do celem. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Kiedy należy używać frazy list lub listy jednostek
Gdy zarówno listę frazy i listy jednostek może mieć wpływ zniesławiających we wszystkich lokalizacji docelowych, każdy robi to w inny sposób. Wynik konwersji prognozowania, należy użyć listy frazę. Użyj jednostki listy wpływa na jednostki wyodrębniania tekstu dokładnego dopasowania. 

### <a name="use-a-phrase-list"></a>Użyj listy frazy
Z listą frazy LUIS nadal mogą uwzględniać kontekstu i generalize, aby zidentyfikować elementy, które są podobne do, ale nie dokładnego dopasowania, jako elementy na liście. LUIS aplikację, aby mieć możliwość generalize i zidentyfikować nowych elementów w kategorii, należy użyć listy frazę. 

Aby rozpoznawać nowego wystąpienia jednostki, takie jak harmonogram spotkania, który powinien rozpoznać nazwy nowych kontaktów lub spisu aplikacji, która powinna rozpoznać nowych produktów, użyj innego typu rozpoznane maszyny jednostki, takich jak prostą lub Hierarchiczna jednostki. Następnie utwórz frazy listę słów i wyrażeń ułatwiający LUIS Znajdź innych wyrazów podobne do jednostki. Ta lista prowadzi LUIS rozpoznawanie przykłady jednostki przez dodanie dodatkowych znaczenie wartości wyrazy. 

Wyrażenie listy są podobne do specyficznego dla domeny słownictwo pomoc udoskonalanie jakości opis intencje i jednostek. Typowe użycie listy frazy jest nazwy własne takie jak nazwy miast. Nazwę miejscowości może być kilka słów w tym łączniki lub apostrofy.
 
### <a name="dont-use-a-phrase-list"></a>Nie używaj frazy listy 
Jednostka listy jawnie definiuje każdej wartości jednostki może potrwać, a tylko identyfikuje wartości, które pasują do siebie. Jednostka listy może być odpowiednie dla aplikacji, w którym wszystkie wystąpienia jednostki są znane i nie zmieniają się często, takich jak elementy żywności menu restauracji, który zmienia się rzadko. Tekst dokładnego dopasowania jednostki, należy nie należy używać listy frazę. 

## <a name="best-practices"></a>Najlepsze praktyki
Dowiedz się [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj funkcje](luis-how-to-add-features.md) Aby dowiedzieć się więcej na temat dodawania funkcji do aplikacji LUIS.