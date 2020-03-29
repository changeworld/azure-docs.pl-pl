---
title: Najważniejsze wskazówki dotyczące tworzenia aplikacji usługi LUIS
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z najlepszymi rozwiązaniami, aby uzyskać najlepsze wyniki z modelu aplikacji usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280925"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Najważniejsze wskazówki dotyczące tworzenia aplikacji do rozumienia języka (LUIS)
Użyj procesu tworzenia aplikacji, aby utworzyć aplikację usługi LUIS: 

* Tworzenie modeli językowych (intencji i encji)
* Dodaj kilka wypowiedzi przykład szkolenia (15-30 na intencji)
* Publikowanie w punkcie końcowym
* Badanie z punktu końcowego 

Po [opublikowaniu](luis-how-to-publish-app.md)aplikacji użyj cyklu życia rozwoju, aby dodać funkcje, opublikować i przetestować z punktu końcowego. Nie należy rozpoczynać następnego cyklu tworzenia przez dodanie więcej wypowiedzi przykład, ponieważ nie pozwala usługi LUIS dowiedzieć się modelu z rzeczywistych wypowiedzi użytkownika. 

Nie należy rozszerzać wypowiedzi, dopóki bieżący zestaw wypowiedzi przykład i punkt końcowy zwracają pewność, wysokie wyniki prognozowania. Popraw wyniki za pomocą [aktywnego uczenia się](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Czy i nie
Poniższa lista zawiera najważniejsze wskazówki dotyczące aplikacji usługi LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Definiowanie odrębnych intencji](#do-define-distinct-intents)<br>[Dodawanie deskryptorów do intencji](#do-add-descriptors-to-intents) |[Dodawanie wielu przykładowych wypowiedzi do intencji](#dont-add-many-example-utterances-to-intents)<br>[Użyj kilku lub prostych jednostek](#dont-use-few-or-simple-entities) |
|[Znajdź słodki punkt między zbyt ogólne i zbyt specyficzne dla każdego zamiaru](#do-find-sweet-spot-for-intents)|[Używanie usługi LUIS jako platformy szkoleniowej](#dont-use-luis-as-a-training-platform)|
|[Tworzenie aplikacji iteracyjnej za pomocą wersji](#do-build-your-app-iteratively-with-versions)<br>[Tworzenie elementów rozkładu modelu](#do-build-for-model-decomposition)|[Dodawanie wielu przykładowych wypowiedzi tego samego formatu, ignorując inne formaty](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodawanie wzorców w późniejszych iteracjach](#do-add-patterns-in-later-iterations)|[Mieszanie definicji intencji i jednostek](#dont-mix-the-definition-of-intents-and-entities)|
|[Równoważyć wypowiedzi we wszystkich intencji](#balance-your-utterances-across-all-intents) z wyjątkiem None intencji.<br>[Dodawanie przykładowych wypowiedzi do intencji Brak](#do-add-example-utterances-to-none-intent)|[Tworzenie deskryptorów ze wszystkimi możliwymi wartościami](#dont-create-descriptors-with-all-the-possible-values)|
|[Wykorzystaj funkcję sugestii do aktywnego uczenia się](#do-leverage-the-suggest-feature-for-active-learning)|[Dodawanie zbyt wielu wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji za pomocą testowania wsadowego](#do-monitor-the-performance-of-your-app)|[Trenuj i publikuj z każdą dodaną wypowiedź przykładu](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Zdefiniuj różne intencje
Upewnij się, że słownictwo dla każdego zamiaru jest tylko dla tego zamiaru i nie nakłada się z innym zamiarem. Na przykład jeśli chcesz mieć aplikację, która obsługuje usługi podróży, takie jak loty linii lotniczych i hoteli, można wybrać, aby te obszary tematu jako oddzielne intencje lub tego samego zamiaru z jednostek dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwo między dwoma intencjami jest taka sama, połącz intencję i użyj encji. 

Należy wziąć pod uwagę następujące wypowiedzi przykład:

|Przykładowe wypowiedzi|
|--|
|Zarezerwuj lot|
|Zarezerwuj hotel|

`Book a flight`i `Book a hotel` używać tego samego słownictwa . `book a ` Ten format jest taki sam, więc powinien być `flight` taki `hotel` sam zamiar z różnych słów i jako wyodrębnione jednostek. 

## <a name="do-add-descriptors-to-intents"></a>Dodaj deskryptory do intencji

Deskryptory pomagają opisać funkcje dla intencji. Deskryptor może być lista fraz słów, które są istotne dla tej intencji lub jednostki, która jest istotna dla tej intencji. 

## <a name="do-find-sweet-spot-for-intents"></a>Znajdź sweet spot dla intencji
Użyj danych przewidywania z usługi LUIS, aby ustalić, czy intencje nakładają się na siebie. Nakładające się intencje mylić usługi LUIS. Wynik jest, że najlepsze intencji punktacji jest zbyt blisko innego zamiaru. Ponieważ usługa LUIS nie używa dokładnie tej samej ścieżki za pośrednictwem danych do szkolenia za każdym razem, nakładające się intencji ma szansę być pierwszy lub drugi w szkoleniu. Chcesz, aby wynik wypowiedź dla każdego zamiaru być dalej od siebie, więc to flip / flop nie dzieje. Dobre rozróżnienie dla intencji powinno spowodować oczekiwane intencje góry za każdym razem. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Tworzenie aplikacji iteracyjnej za pomocą wersji

Każdy cykl tworzenia powinien znajdować się w nowej [wersji,](luis-concept-version.md)sklonowanej z istniejącej wersji. 

## <a name="do-build-for-model-decomposition"></a>Czy budować dla rozkładu modelu

Rozkład modelu ma typowy proces:

* tworzenie **intencji** na podstawie intencji użytkownika aplikacji klient-klient
* dodawanie wypowiedzi przykładowych 15–30 na podstawie rzeczywistych danych wejściowych użytkownika
* etykieta najwyższego poziomu koncepcji danych w przykładowej wypowiedź
* podziel koncepcję danych na podskładnie
* dodawanie deskryptorów (funkcji) do podskładników
* dodawanie deskryptorów (funkcji) do intencji 

Po utworzeniu intencji i dodano wypowiedzi przykład, w poniższym przykładzie opisano rozkład jednostki. 

Zacznij od zidentyfikowania kompletnych pojęć danych, które chcesz wyodrębnić w wypowiedź. Jest to jednostka, której się uczymy maszynowo. Następnie rozłożyć frazę na jego części. Obejmuje to identyfikowanie podskładników (jako jednostek) wraz z deskryptorami i ograniczeniami. 

Na przykład, jeśli chcesz wyodrębnić adres, można wywołać `Address`najwyższą jednostkę nauczaną maszyną . Podczas tworzenia adresu należy zidentyfikować niektóre z jego podskładów, takie jak adres ulicy, miasto, stan i kod pocztowy. 

Kontynuuj rozkładanie tych elementów, **ograniczając** kod pocztowy do wyrażenia regularnego. Rozłożyć adres ulicy na części numeru ulicy (przy użyciu wstępnie utworzonego numeru), nazwę ulicy i typ ulicy. Typ ulicy można opisać za pomocą listy **deskryptorów,** takich jak aleja, okrąg, droga i pas ruchu.

Interfejs API tworzenia wersji V3 umożliwia rozkład modelu. 

## <a name="do-add-patterns-in-later-iterations"></a>Dodaj wzorce w późniejszych iteracjach

Należy zrozumieć, jak aplikacja zachowuje się przed dodaniem [wzorców,](luis-concept-patterns.md) ponieważ wzorce są ważone bardziej niż wypowiedzi przykład i będzie wypaczać zaufanie. 

Gdy zrozumiesz, jak zachowuje się aplikacja, dodaj wzorce, które mają zastosowanie do aplikacji. Nie trzeba dodawać ich przy każdej [iteracji](luis-concept-app-iteration.md). 

Nie ma żadnych szkód, dodając je na początku projektu modelu, ale łatwiej jest zobaczyć, jak każdy wzorzec zmienia model po modelu jest testowany z wypowiedzi. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Czy równowagi wypowiedzi we wszystkich intencji

Aby prognozy usługi LUIS były dokładne, ilość wypowiedzi przykład w każdej intencji (z wyjątkiem None intencji), musi być stosunkowo równa. 

Jeśli masz intencji z 100 wypowiedzi przykład i intencji z 20 wypowiedzi przykład, 100-wypowiedź intencji będzie miał wyższy wskaźnik przewidywania.  

## <a name="do-add-example-utterances-to-none-intent"></a>Dodaj przykładowe wypowiedzi do intencji Brak

Ten zamiar jest zamiarem rezerwowym, wskazując wszystko poza aplikacją. Dodaj jeden przykład wypowiedź do None intencji dla każdego 10 wypowiedzi przykład w pozostałej części aplikacji usługi LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Wykorzystaj funkcję sugestii do aktywnego uczenia się

Użyj [aktywnego uczenia](luis-how-to-review-endpoint-utterances.md)'s Przegląd wypowiedzi punktu **końcowego** na bieżąco, zamiast dodawania więcej wypowiedzi przykład do intencji. Ponieważ aplikacja stale odbiera wypowiedzi punktów końcowych, ta lista rośnie i zmienia.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitoruj wydajność aplikacji

Monitorowanie dokładności przewidywania przy użyciu zestawu [testów wsadowych.](luis-concept-batch-test.md) 

Zachowaj oddzielny zestaw wypowiedzi, które nie są używane jako [wypowiedzi przykład lub](luis-concept-utterance.md) wypowiedzi punktu końcowego. Kontynuuj ulepszanie aplikacji dla zestawu testowego. Dostosuj zestaw testów, aby odzwierciedlić wypowiedzi rzeczywistych użytkowników. Ten zestaw testów służy do oceny każdej iteracji lub wersji aplikacji. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wielu przykładowych wypowiedzi do intencji

Po opublikowaniu aplikacji dodaj tylko wypowiedzi z aktywnego uczenia się w procesie cyklu życia rozwoju. Jeśli wypowiedzi są zbyt podobne, dodaj wzorzec. 

## <a name="dont-use-few-or-simple-entities"></a>Nie używaj kilku lub prostych jednostek

Jednostki są tworzone do wyodrębniania i przewidywania danych. Ważne jest, że każdy zamiar mają jednostki nauczanych maszynowo, które opisują dane w intencji. Pomaga to usługi LUIS przewidzieć intencji, nawet jeśli aplikacja kliencka nie trzeba używać wyodrębnione jednostki. 

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj usługi LUIS jako platformy szkoleniowej

Usługa LUIS jest specyficzna dla domeny modelu języka. To nie jest przeznaczony do pracy jako ogólna platforma szkolenia języka naturalnego. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nie dodawaj wielu przykładowych wypowiedzi tego samego formatu, ignorując inne formaty

Usługa LUIS oczekuje różnic w wypowiedzi intencji. Wypowiedzi mogą się różnić, mając jednocześnie takie samo znaczenie ogólne. Odmiany mogą obejmować długość wypowiedź, wybór wyrazu i umieszczenie wyrazów. 

|Nie używaj tego samego formatu|Używaj różnych formatów|
|--|--|
|Kup bilet do Seattle<br>Kup bilet do Paryża<br>Kup bilet do Orlando|Kup 1 bilet do Seattle<br>Zarezerwuj dwa miejsca na czerwonym oku do Paryża w najbliższy poniedziałek<br>Chciałbym zarezerwować 3 bilety do Orlando na wiosenną przerwę|

Druga kolumna używa różnych czasowników (kup, rezerwa, książka), różnych ilości (1, dwa, 3) i różnych układów słów, ale wszystkie mają ten sam zamiar zakupu biletów lotniczych na podróż. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nie mieszaj definicji intencji i jednostek

Utwórz intencję dla każdego działania, które podejmie bot. Użyj jednostek jako parametrów, które umożliwiają tę akcję. 

W przypadku bota, który zarezerwuje loty lotnicze, utwórz zamiar **BookFlight.** Nie należy tworzyć intencji dla każdej linii lotniczej lub każdego miejsca docelowego. Użyj tych fragmentów danych jako [jednostek](luis-concept-entity-types.md) i oznacz je w wypowiedzi przykład. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Nie twórz deskryptorów ze wszystkimi możliwymi wartościami

Podaj kilka przykładów na [listach fraz](luis-concept-feature.md) deskryptora, ale nie każde słowo. Usługa LUIS uogólnia kontekst i uwzględnia kontekst. 

## <a name="dont-add-many-patterns"></a>Nie dodawaj wielu wzorów

Nie dodawaj zbyt wielu [wzorców](luis-concept-patterns.md). Usługa LUIS ma szybko uczyć się przy mniejszej liczbie przykładów. Nie przeciążaj niepotrzebnie systemu.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie trenuj i nie publikuj przy każdej wypowiedź przykład

Dodaj 10 lub 15 wypowiedzi przed szkoleniem i publikowaniem. Dzięki temu można zobaczyć wpływ na dokładność przewidywania. Dodawanie pojedynczego wypowiedź może nie mieć widoczny wpływ na wynik. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zaplanować aplikację](luis-how-plan-your-app.md) w aplikacji usługi LUIS.
