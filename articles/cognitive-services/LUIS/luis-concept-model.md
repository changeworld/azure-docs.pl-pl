---
title: Projektowanie z modelami - LUIS
titleSuffix: Azure Cognitive Services
description: Zrozumienie języka zapewnia kilka typów modeli. Niektóre modele mogą być używane w więcej niż jeden sposób.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219996"
---
# <a name="design-with-intent-and-entity-models"></a>Projektowanie z modelami intencji i encji 

Zrozumienie języka zapewnia kilka typów modeli. Niektóre modele mogą być używane w więcej niż jeden sposób. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Authoring wykorzystuje nauczanie maszynowe

Usługa LUIS umożliwia użytkownikom łatwe nauczanie pojęć na komputerze. Maszyna może następnie tworzyć modele (przybliżenia funkcjonalne pojęć, takich jak klasyfikatory i ekstraktory), które mogą być używane do zasilania inteligentnych aplikacji. Podczas gdy usługa LUIS jest obsługiwana przez uczenie maszynowe, zrozumienie uczenia maszynowego nie jest konieczne do korzystania z niego. Zamiast tego nauczyciele maszyn komunikują pojęcia usługi LUIS, pokazując pozytywne i negatywne przykłady koncepcji i wyjaśniając, jak koncepcja powinna być modelowana przy użyciu innych powiązanych pojęć. Nauczyciele mogą również interaktywnie ulepszać model usługi LUIS, identyfikując i naprawiając błędy przewidywania. 

## <a name="v3-authoring-model-decomposition"></a>V3 Tworzenie rozkładu modelu

Usługa LUIS obsługuje _rozkład modelu_ za pomocą interfejsów API tworzących w wersji 3, rozbijając model na mniejsze części. Dzięki temu można tworzyć modele z ufnością w sposób różnych części są konstruowane i przewidywane.

Rozkład modelu ma następujące części:

* [Zamiarów](#intents-classify-utterances)
    * [deskryptory](#descriptors-are-features) dostarczane przez funkcje
* [jednostki nauczane maszynowo](#machine-learned-entities)
    * [podskładentów](#entity-subcomponents-help-extract-data) (również jednostek nauczanych maszynowo)
        * [deskryptory](#descriptors-are-features) dostarczane przez funkcje 
        * ograniczenia zapewniane przez [jednostki](#constraints-are-text-rules) nieuczone maszynowo, takie jak wyrażenia regularne i listy

## <a name="v2-authoring-models"></a>Modele tworzenia v2

Usługa LUIS obsługuje jednostki złożone za pomocą interfejsów API tworzenia w wersji 2. Zapewnia to podobny rozkład modelu, ale nie jest taki sam jak rozkład modelu V3. Zalecana architektura modelu jest przejście do rozkładu modelu w interfejsach API tworzenia V3. 

## <a name="intents-classify-utterances"></a>Intencje klasyfikują wypowiedzi

Intencji klasyfikuje wypowiedzi przykład uczyć usługi LUIS o intencji. Przykładowe wypowiedzi w ramach intencji są używane jako pozytywne przykłady wypowiedź. Te same wypowiedzi są używane jako negatywne przykłady we wszystkich innych intencji.

Należy wziąć pod uwagę aplikację, która musi określić zamiar użytkownika, aby zamówić książkę i aplikację, która potrzebuje adresu wysyłki dla klienta. Ta aplikacja ma dwa `OrderBook` `ShippingLocation`zamiary: i .

Następujący wypowiedź jest **pozytywnym przykładem** `OrderBook` intencji `ShippingLocation` i `None` **negatywnym przykładem** dla i intencji: 

`Buy the top-rated book on bot architecture.`

Wynik dobrze zaprojektowane intencje, z ich wypowiedzi przykład, jest przewidywanie wysokiej intencji. 

## <a name="entities-extract-data"></a>Jednostki wyodrębnić dane

Jednostka reprezentuje jednostkę danych, które mają wyodrębnione z wypowiedź. 

### <a name="machine-learned-entities"></a>Jednostki nauczane maszynowo

Jednostka nauczona maszynowo jest jednostką najwyższego poziomu zawierającą podskładne, które są również jednostkami nauczającymi się maszyn. 

**Użyj jednostki nauczającej maszynowo:**

* gdy podskładne są potrzebne przez aplikację kliencką
* aby pomóc algorytmowi uczenia maszynowego w rozesłania jednostek

Każdy podskładnik może mieć:

* Podskładniki
* ograniczenia (jednostka wyrażenia regularnego lub encja listy)
* deskryptory (funkcje, takie jak lista fraz) 

Przykładem jednostki nauczanych maszyn jest zamówienie biletu lotniczego. Koncepcyjnie jest to pojedyncza transakcja z wieloma mniejszymi jednostkami danych, takimi jak data, godzina, ilość miejsc, typ miejsca, taki jak pierwsza klasa lub autokar, lokalizacja pochodzenia, lokalizacja docelowa i wybór posiłku.


### <a name="entity-subcomponents-help-extract-data"></a>Podskładne jednostek pomagają wyodrębnić dane

Podskład nieskładka jest jednostką podrzędną nauczoną maszyną w jednostce nadrzędnej, której się uczy. 

**Użyj podskładniku do:**

* rozkładu części jednostki nauczanej maszynowo (jednostki nadrzędnej).

Następujące elementy reprezentują jednostkę nauczone maszynowo z tymi wszystkimi oddzielnymi fragmentami danych:

* TravelOrder (jednostka nauczona maszynowo)
    * DateTime (wstępnie utworzony datetimeV2)
    * Lokalizacja (jednostka nauczona maszynowo)
        * Origin (rola znaleziona `from`w kontekście, takich jak )
        * Miejsce docelowe (rola `to`znaleziona w kontekście, takich jak )
    * Siedzenia (jednostka wyuczone maszynowo)
        * Ilość (wstępnie utworzony numer)
        * Jakość (jednostka nauczona maszynowo z deskryptorem listy fraz)
    * Posiłki (jednostka nauczona maszynowo z ograniczeniem podmiotu listy jako wyborów żywieniowych)

Niektóre z tych danych, takich jak lokalizacja pochodzenia i lokalizacja docelowa, powinny być nauczane `from` `to`z kontekstu wypowiedź, być może z takim sformułowaniem jak i . Inne części danych mogą być wyodrębniane`Vegan`z dokładnymi dopasowaniami ciągów ( `Seattle` `Cairo`) lub wstępnie utworzonymi jednostkami (geographyV2 i ). 

Projektujesz sposób dopasowywały i wyodrębniane dane według wybranego modelu i sposobu ich konfigurowania.

### <a name="constraints-are-text-rules"></a>Ograniczenia to reguły tekstowe

Ograniczenie to reguła dopasowywania tekstu, dostarczana przez jednostkę nieuczonej maszynowo, takiej jak encja wyrażenia regularnego lub encja listy. Ograniczenie jest stosowane w czasie przewidywania, aby ograniczyć przewidywanie i zapewnić rozdzielczość jednostki wymagane przez aplikację kliencką. Reguły te można zdefiniować podczas tworzenia podskładła. 

**Użyj ograniczenia:**
* gdy znasz dokładny tekst do wyodrębnienia.

Ograniczenia obejmują:

* [jednostki wyrażeń regularnych](reference-entity-regular-expression.md)
* [lista](reference-entity-list.md) jednostek 
* [wstępnie utworzone](luis-reference-prebuilt-entities.md) jednostki

Kontynuując przykład biletu lotniczego, kody lotnisk mogą znajdować się w jednostce Lista dla dokładnych dopasowań tekstu. 

W przypadku listy lotniskowej wpis listy Seattle `Seattle` jest nazwą miasta, a synonimy Seattle zawierają kod lotniska dla Seattle wraz z pobliskimi miastami:

|`Seattle`Lista synonimów encji|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Jeśli chcesz rozpoznać tylko 3-literowe kody kodów lotnisk, użyj wyrażenia regularnego jako ograniczenia. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intencje a jednostki

Intencja jest pożądanym wynikiem _całej_ wypowiedź, podczas gdy jednostki są fragmenty danych wyodrębnionych z wypowiedź. Zazwyczaj intencje są powiązane z akcjami, które aplikacja kliencka powinna podjąć, a jednostki są informacjami potrzebnymi do wykonania tej akcji. Z punktu widzenia programowania intencji wyzwoli wywołanie metody i jednostek będzie używany jako parametry do tego wywołania metody.

Ten wypowiedź _musi_ mieć intencji i _może_ mieć jednostki:

`Buy an airline ticket from Seattle to Cairo`

Ta wypowiedź ma jeden zamiar:

* Zakup biletu lotniczego

Ten wypowiedź _może_ mieć kilka jednostek:

* Lokalizacje Seattle (pochodzenie) i Kairu (miejsce docelowe)
* Ilość pojedynczego biletu

## <a name="descriptors-are-features"></a>Deskryptory są funkcjami

Deskryptor jest funkcją zastosowaną do modelu w czasie szkolenia, w tym listy fraz i encji. 

**Użyj deskryptora, aby**:

* znaczenie słów i zwrotów wskazanych przez deskryptor
* czy usługa LUIS zaleca nowy tekst lub wyrażenia do polecenia deskryptora
* naprawianie błędu w danych treningu

## <a name="next-steps"></a>Następne kroki

* Zrozumienie [intencji](luis-concept-intent.md) i [encji](luis-concept-entity-types.md). 