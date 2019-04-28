---
title: Intencje
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pojedynczy intencji reprezentuje zadanie lub akcję użytkownik chce wykonać. Jest to cel lub celem wyrażone w wypowiedź użytkownika. Definiują zestaw intencji, które odnosi się do akcji, które użytkownicy chcą w aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: e635a11cb99d11befc40703d9f5d2abec8559632
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813455"
---
# <a name="concepts-about-intents-in-your-luis-app"></a>Pojęcia dotyczące intencje w aplikacją usługi LUIS

Intencji reprezentuje zadanie lub akcję użytkownik chce wykonać. Jest to cel lub celem wyrażone w użytkownika [wypowiedź](luis-concept-utterance.md).

Definiują zestaw intencji, które odnosi się do akcji, które użytkownicy chcą w aplikacji. Na przykład aplikacji turystycznej definiuje kilka opcji:

Przeznaczeniu podróży   |   Przykładowe wypowiedzi   | 
------|------|
 BookFlight     |   "Zarezerwuj mnie lot Rio w następnym tygodniu" <br/> "Podnoszenia mnie do Rio na 24th" <br/> "Potrzebuję bilet płaszczyzny niedzielę dalej do Rio de Janeiro"    |
 Powitanie     |   "Hi" <br/>"Hello" <br/>"Good morning"  |
 CheckWeather | "Co to jest dane takie jak pogody w Bostonie?" <br/> "Pokaż prognozy dla tego weekend" |
 Brak         | "Dostać się ze mną przepisu plik cookie"<br>"Czy Lakers wygrać?" |

Wszystkie aplikacje dołączone wstępnie zdefiniowane opcje "[Brak](#none-intent-is-fallback-for-app)", który jest celem rezerwowego. 

## <a name="prebuilt-domains-provide-intents"></a>Ze wstępnie utworzonych domen zapewniają intencji
Oprócz intencji, które definiujesz można użyć wbudowanych intencji z jednego ze wstępnie utworzonych domen. Aby uzyskać więcej informacji, zobacz [korzystać ze wstępnie utworzonych domen w aplikacjach usługi LUIS](luis-how-to-use-prebuilt-domains.md) Aby dowiedzieć się więcej o dostosowywaniu intencji z wbudowanych domeny do użycia w aplikacji.

## <a name="return-all-intents-scores"></a>Zwraca wyniki wszystkich intencji
Wypowiedź należy przypisać do pojedynczego celem. Gdy usługa LUIS otrzymuje wypowiedź w punkcie końcowym, zwraca jeden najważniejsze przeznaczenie tego wypowiedź. Jeśli chcesz wyniki dla wszystkich opcji dla wypowiedź może zapewnić `verbose=true` flagi w ciągu zapytania API [wywołanie punktu końcowego](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Celem w porównaniu do jednostki
Celem reprezentuje akcję chatbot powinno zająć dla użytkownika i opiera się na cały wypowiedź. Jednostka reprezentuje słów i fraz zawartych w wypowiedź. Wypowiedź może mieć tylko jeden górnej oceniania intencji, ale może mieć wiele jednostek. 

<a name="how-do-intents-relate-to-entities"></a> Utwórz opcję podczas użytkownika _zamiar_ będą wyzwalać akcję w aplikacji klienckiej, takich jak wywołania funkcji checkweather(). Następnie należy utworzyć jednostkę reprezentują parametrów wymaganych do wykonania akcji. 

|Przykład intencji   | Jednostka | Jednostki w przykładzie wypowiedzi   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "Lokalizacja", "entity": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "jutro", "Rozwiązanie": "2018-05-23"} | Co pogody, takich jak w `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entity": "ten weekend"} | Pokaż mi prognozy dla `this weekend` | 

## <a name="custom-intents"></a>Niestandardowe intencji

Podobnie tych [wypowiedzi](luis-concept-utterance.md) odpowiadają jednej intencji. Wypowiedzi w zgodne z zamiarami użytkownika, można użyć dowolnego [jednostki](luis-concept-entity-types.md) w aplikacji, ponieważ jednostki nie są specyficzne dla intencji. 

## <a name="prebuilt-domain-intents"></a>Wstępnie utworzone domeny intencji

[Ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md) mają intencji z wypowiedzi.  

## <a name="none-intent"></a>Intencja None

**Brak** ważne jest, aby każda aplikacja i nie powinny mieć zerowej wypowiedzi.

### <a name="none-intent-is-fallback-for-app"></a>Żadna funkcja nie jest rezerwowe dla aplikacji
**Brak** celem jest celem wychwytywania lub rezerwowej. Służy do nauki LUIS wypowiedzi, które nie są istotne w domenie aplikacji (obszar podmiotu). **Brak** intencji powinny mieć od 10 do 20 procent całkowitej wypowiedzi w aplikacji. Nie pozostawiaj brak puste. 

### <a name="none-intent-helps-conversation-direction"></a>Brak elementu intent pomaga kierunek konwersacji
Gdy wypowiedź przewiduje się, jak brak intencji i zwrócone do chatbot za pomocą tego prognozowania bot można zadawać pytań lub Zapewnij menu do kierowania użytkownikowi prawidłowe opcje w chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Nie wypowiedzi żadna intencji pochyla prognozy
Jeśli nie dodasz wypowiedzi dla **Brak** intencji, LUIS wymusza wypowiedź spoza domeny w jednym z opcjami domeny. Będzie to pochylanie wyniki prognozowania, nauczania usługi LUIS niewłaściwego przeznaczenie wypowiedź. 

### <a name="add-utterances-to-the-none-intent"></a>Dodawanie wypowiedzi intencji None
**Brak** celem jest tworzony, ale pozostawić wartość pustą, celowo. Wypełnij ją wypowiedzi, które znajdują się poza domenę. Dobre wypowiedź dla **Brak** jest coś zupełnie poza aplikację, a także branży aplikacji służy. Na przykład aplikacji turystycznej nie należy używać wypowiedzi dla **Brak** , można powiązać z podróży, takie jak zastrzeżenia, rozliczenia, żywności, zakwaterowanie, ładunku, rozrywka porządkowych. 

Jakiego rodzaju wypowiedzi pozostało dla żadnego elementu intent? Uruchom przy użyciu określonego elementu bota nie należy odpowiedzieć, takie "jakiego rodzaju dinozaurów ma niebieski zębów?" Jest to bardzo konkretne pytanie daleko poza aplikacji turystycznej. 

### <a name="none-is-a-required-intent"></a>Brak wymaganego intencji
**Brak** intencji jest celem wymagane i nie może być usunięte lub zmieniono ich nazwy.

## <a name="negative-intentions"></a>Ujemna intencji 
Jeśli chcesz określić zamiarach ujemny i dodatni, takie jak "I **ma** samochód" i "I **nie** ma samochód", można utworzyć dwa intencji (jedną pozytywną i jedną negatywną) i dodawanie wypowiedzi odpowiednie dla Każdy. Lub można utworzyć pojedynczy intencji i oznaczyć te dwa różne terminy dodatnie i ujemne jako jednostka.  

## <a name="intents-and-patterns"></a>Intencje i wzorce

Jeśli masz wypowiedzi przykładu, które mogą być zdefiniowane w części lub całości jako wyrażenie regularne, należy wziąć pod uwagę przy użyciu [jednostki wyrażenia regularnego](luis-concept-entity-types.md#regular-expression-entity) parowania z [wzorzec](luis-concept-patterns.md). 

Jednostki wyrażenia regularnego gwarantuje wyodrębnianie danych, tak, aby odbywa się dopasowanie. Dopasowanie wzorca gwarancję, że zwracany jest dokładna intencji. 

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



## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [jednostek](luis-concept-entity-types.md), które są ważne wyrazy, które dotyczą intencji
* Dowiedz się, jak [Dodaj i Zarządzaj opcjami](luis-how-to-add-intents.md) w aplikacją usługi LUIS.
* Przejrzyj opcje [najlepsze rozwiązania](luis-concept-best-practices.md)
