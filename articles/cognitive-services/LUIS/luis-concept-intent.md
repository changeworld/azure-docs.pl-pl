---
title: Intencje i jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Pojedynczy cel reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub celem wyrażone w wypowiedź użytkownika. Definiują zestaw intencji, które odnosi się do akcji, które użytkownicy chcą w aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280885"
---
# <a name="intents-in-your-luis-app"></a>Intencje w aplikacji LUIS

Intencji reprezentuje zadanie lub akcję użytkownik chce wykonać. Jest to cel lub celem wyrażone w użytkownika [wypowiedź](luis-concept-utterance.md).

Definiują zestaw intencji, które odnosi się do akcji, które użytkownicy chcą w aplikacji. Na przykład aplikacji turystycznej definiuje kilka opcji:

Przeznaczeniu podróży   |   Przykładowe wypowiedzi   | 
------|------|
 BookFlight     |   "Zarezerwuj mnie lot Rio w następnym tygodniu" <br/> "Podnoszenia mnie do Rio na 24th" <br/> "Potrzebuję bilet płaszczyzny niedzielę dalej do Rio de Janeiro"    |
 Powitanie     |   "Hi" <br/>"Hello" <br/>"Good morning"  |
 CheckWeather | "Co to jest dane takie jak pogody w Bostonie?" <br/> "Pokaż prognozy dla tego weekend" |
 Brak         | "Dostać się ze mną przepisu plik cookie"<br>"Czy Lakers wygrać?" |

Wszystkie aplikacje są dostarczane ze wstępnie zdefiniowanym zamiarem "[none](#none-intent)", który jest zamiarem alternatywnym. 

## <a name="prebuilt-domains-provide-intents"></a>Ze wstępnie utworzonych domen zapewniają intencji
Oprócz elementów docelowych, które można zdefiniować, można użyć prebudowanych intencji z jednej z [prekompilowanych domen](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Zwraca wyniki wszystkich intencji
Wypowiedź należy przypisać do pojedynczego celem. Gdy LUIS odbiera wypowiedź w punkcie końcowym, domyślnie zwraca najwyższy zamiar dla tego wypowiedź. 

Jeśli chcesz uzyskać wyniki dla wszystkich intencji dla wypowiedź, możesz podać flagę w ciągu zapytania w interfejsie API przewidywania. 

|Wersja interfejsu API przewidywania|Flaga|
|--|--|
|Wersja 2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Celem w porównaniu do jednostki
Cel reprezentuje akcję, którą aplikacja powinna wykonać dla użytkownika i opiera się na całej wypowiedź. Wypowiedź może mieć tylko jeden górnej oceniania intencji, ale może mieć wiele jednostek. 

<a name="how-do-intents-relate-to-entities"></a>

Utwórz cel, gdy _zamiaru_ użytkownika wywoła akcję w aplikacji klienckiej, taką jak wywołanie funkcji checkweather (). Następnie Utwórz jednostki do reprezentowania parametrów wymaganych do wykonania akcji. 

|Intencja   | Jednostka | Przykładowa wypowiedź   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "Lokalizacja", "entity": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "jutro", "Rozwiązanie": "2018-05-23"} | Co pogody, takich jak w `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entity": "ten weekend"} | Pokaż mi prognozy dla `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Wstępnie utworzone domeny intencji

[Prebudowane domeny](luis-how-to-use-prebuilt-domains.md) zapewniają intencje wyrażenia długości. 

## <a name="none-intent"></a>Intencja None

**Brak** celem jest tworzony, ale pozostawić wartość pustą, celowo. **Brak** intencji jest celem wymagane i nie może być usunięte lub zmieniono ich nazwy. Wypełnij ją wypowiedzi, które znajdują się poza domenę.

Opcja **Brak** jest zamiarem rezerwowym, istotnym dla każdej aplikacji i powinna mieć 10% całkowitej wyrażenia długości. Służy do nauki LUIS wypowiedzi, które nie są istotne w domenie aplikacji (obszar podmiotu). Jeśli nie dodasz wypowiedzi dla **Brak** intencji, LUIS wymusza wypowiedź spoza domeny w jednym z opcjami domeny. Będzie to pochylanie wyniki prognozowania, nauczania usługi LUIS niewłaściwego przeznaczenie wypowiedź. 

Gdy wypowiedź jest przewidywany jako cel, aplikacja kliencka może zadawać więcej pytań lub podać menu, aby skierować użytkownika do prawidłowych opcji. 

## <a name="negative-intentions"></a>Ujemna intencji 
Jeśli chcesz określić zamiarach ujemny i dodatni, takie jak "I **ma** samochód" i "I **nie** ma samochód", można utworzyć dwa intencji (jedną pozytywną i jedną negatywną) i dodawanie wypowiedzi odpowiednie dla Każdy. Lub można utworzyć pojedynczy intencji i oznaczyć te dwa różne terminy dodatnie i ujemne jako jednostka.  

## <a name="intents-and-patterns"></a>Intencje i wzorce

Jeśli masz przykład wyrażenia długości, który można zdefiniować w części lub całości jako wyrażenie regularne, rozważ użycie [jednostki wyrażenia regularnego](luis-concept-entity-types.md#regular-expression-entity) sparowanego ze [wzorcem](luis-concept-patterns.md). 

Użycie jednostki wyrażenia regularnego gwarantuje ekstrakcję danych, tak aby wzorzec był dopasowany. Dopasowywanie do wzorca gwarantuje dokładne przeznaczenie. 

## <a name="intent-balance"></a>Saldo intencji
Przeznaczeniu domeny powinien mieć saldo wypowiedzi w każdej intencji. Nie masz zamiar jednego z 10 wypowiedzi i inną intencji z wypowiedzi 500. Nie jest równoważone. Jeśli masz tę sytuację, zapoznaj się z celem z wypowiedzi 500, aby zobaczyć, jeśli wiele intencji można zreorganizować do [wzorzec](luis-concept-patterns.md). 

**Brak** celem nie jest objęta równowagi. Tym przeznaczeniem powinna zawierać 10% całkowitej wypowiedzi w aplikacji.

## <a name="intent-limits"></a>Limity intencji
Przegląd [limity](luis-boundaries.md#model-boundaries) Aby zrozumieć intencje ile można dodać do modelu. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Jeśli potrzebujesz większej niż maksymalna liczba intencji 
Najpierw należy rozważyć, czy system używa zbyt dużo intencji. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Wiele intencji można łączyć w pojedynczej intencji przy użyciu jednostek 
Intencji, które są zbyt podobne może utrudnić dla usługi LUIS do rozróżnienia między nimi. Intencji powinien być wystarczająco dużo, aby przechwycić głównych zadań, które pyta użytkownika, ale nie potrzebują do przechwytywania każdej ścieżce kodu przyjmuje zróżnicowane. Na przykład oddzielnych intencje w podróży aplikacji może być BookFlight i FlightCustomerService, ale BookInternationalFlight i BookDomesticFlight są zbyt podobne. Jeśli system musi odróżnić je, należy użyć jednostek lub inne logiki zamiast intencji. 

### <a name="dispatcher-model"></a>Dyspozytor modelu
Dowiedz się więcej na temat łączenia aplikacji twórcy LUIS i pytań i odpowiedzi z [modelu wysyłania](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Prosić o pomoc dla aplikacji za pomocą znacznej liczby intencji
Zmniejszenie liczby intencji lub podzielenie Twoje intencje w wielu aplikacjach nie działa, należy się z pomocą techniczną. Jeśli Twoja subskrypcja platformy Azure obejmują usługi pomocy technicznej, skontaktuj się z [technicznej platformy Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [jednostek](luis-concept-entity-types.md), które są ważne wyrazy, które dotyczą intencji
* Dowiedz się, jak [Dodaj i Zarządzaj opcjami](luis-how-to-add-intents.md) w aplikacją usługi LUIS.
* Przejrzyj opcje [najlepsze rozwiązania](luis-concept-best-practices.md)
