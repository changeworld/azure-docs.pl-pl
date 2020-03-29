---
title: Intencje i jednostki — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Pojedynczy zamiar reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub zamierzenie wyrażone w wypowiedzi użytkownika. Zdefiniuj zestaw intencji, który odpowiada działaniom, które użytkownicy chcą podjąć w aplikacji.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220975"
---
# <a name="intents-in-your-luis-app"></a>Intencje w aplikacji usługi LUIS

Intencja reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub cel wyrażony w [wypowiedź](luis-concept-utterance.md)użytkownika .

Zdefiniuj zestaw intencji, który odpowiada działaniom, które użytkownicy chcą podjąć w aplikacji. Na przykład aplikacja turystyczna definiuje kilka intencji:

Intencje aplikacji podróżniczej   |   Przykładowe wypowiedzi   | 
------|------|
 BookFlight     |   "Zarezerwuj mi lot do Rio w przyszłym tygodniu" <br/> "Leć mnie do Rio na 24" <br/> "Potrzebuję biletu lotniczego w najbliższą niedzielę do Rio de Janeiro"    |
 Powitanie     |   "Cześć" <br/>"Hello" <br/>"Dzień dobry"  |
 CheckWeather (właśc. | "Jaka jest pogoda w Bostonie?" <br/> "Pokaż mi prognozę na ten weekend" |
 Brak         | "Get me przepis cookie"<br>"Czy Lakers wygrali?" |

Wszystkie aplikacje pochodzą z predefiniowanym zamiarem "[Brak](#none-intent)", który jest zamiarem rezerwy. 

## <a name="prebuilt-domains-provide-intents"></a>Wstępnie utworzone domeny zapewniają intencje
Oprócz intencji zdefiniowanych można użyć wstępnie utworzonych intencji z jednej ze [wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Zwracanie wyników wszystkich intencji
Przypisać wypowiedź do jednego zamiaru. Gdy usługa LUIS odbiera wypowiedź w punkcie końcowym, domyślnie zwraca górną intencję dla tej wypowiedzi. 

Jeśli chcesz wyniki dla wszystkich intencji dla wypowiedź, można podać flagę na ciąg kwerendy interfejsu API przewidywania. 

|Wersja interfejsu API przewidywania|Flaga|
|--|--|
|Wersja 2|`verbose=true`|
|Wersja V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intencja w porównaniu z encją
Intencji reprezentuje akcję aplikacji należy podjąć dla użytkownika i opiera się na całej wypowiedź. Wypowiedź może mieć tylko jeden najwyższej intencji punktacji, ale może mieć wiele jednostek. 

<a name="how-do-intents-relate-to-entities"></a>

Utwórz zamiar, gdy _intencja_ użytkownika wyzwoli akcję w aplikacji klienckiej, taką jak wywołanie funkcji checkweather(). Następnie utwórz jednostki do reprezentowania parametrów wymaganych do wykonania akcji. 

|Intencja   | Jednostka | Przykładowa wypowiedź   | 
|------------------|------------------------------|------------------------------|
| CheckWeather (właśc. | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Jaka jest pogoda `Seattle` `tomorrow`w ? |
| CheckWeather (właśc. | { "type": "date_range", "entity": "this weekend" } | Pokaż mi prognozę dla`this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Wstępnie utworzone intencje domeny

[Wstępnie utworzone domeny](luis-how-to-use-prebuilt-domains.md) zapewniają intencje z wypowiedzi. 

## <a name="none-intent"></a>Intencja None

Intencja **Brak** jest tworzona, ale celowo pusta. Intencja **Brak** jest wymaganą intencją i nie można jej usunąć ani zmienić nazwy. Wypełnij go wypowiedziami, które znajdują się poza domeną.

Brak **None** intencji jest intencji rezerwowych, ważne w każdej aplikacji i powinien mieć 10% całkowitych wypowiedzi. Służy do uczenia wypowiedzi usługi LUIS, które nie są ważne w domenie aplikacji (obszar tematu). Jeśli nie dodać żadnych wypowiedzi dla **Brak** intencji, usługa LUIS wymusza wypowiedź, która znajduje się poza domeną do jednej z intencji domeny. Spowoduje to wypaczenie wyników przewidywania przez nauczanie usługi LUIS niewłaściwego zamiaru wypowiedź. 

Gdy wypowiedź jest przewidywane jako Brak intencji, aplikacja kliencka może zadać więcej pytań lub podać menu, aby skierować użytkownika do prawidłowych wyborów. 

## <a name="negative-intentions"></a>Negatywne intencje 
Jeśli chcesz określić negatywne i pozytywne intencje, takie jak **"Chcę** samochód" i **"Nie** chcę samochodu", możesz utworzyć dwa zamiary (jeden pozytywny i jeden negatywny) i dodać odpowiednie wypowiedzi dla każdego. Lub można utworzyć pojedynczy zamiar i oznaczyć dwa różne terminy dodatnie i ujemne jako jednostki.  

## <a name="intents-and-patterns"></a>Intencje i wzorce

Jeśli masz przykładowe wypowiedzi, które mogą być zdefiniowane w części lub w całości jako wyrażenie regularne, należy rozważyć użycie [jednostki wyrażenia regularnego](luis-concept-entity-types.md#regular-expression-entity) sparowanego ze [wzorcem](luis-concept-patterns.md). 

Za pomocą jednostki wyrażenia regularnego gwarantuje wyodrębnianie danych, tak aby wzorzec jest dopasowany. Dopasowanie wzorca gwarantuje, że zwracana jest dokładna intencja. 

## <a name="intent-balance"></a>Saldo intencji
Intencje domeny aplikacji powinny mieć równowagę wypowiedzi w każdej intencji. Nie mają jednego zamiaru z 10 wypowiedzi i innego zamiaru z 500 wypowiedzi. To nie jest wyważone. Jeśli masz taką sytuację, przejrzyj intencję z 500 wypowiedzi, aby zobaczyć, czy wiele intencji można zreorganizować na [wzorzec](luis-concept-patterns.md). 

Intencja **Brak** nie jest uwzględniona w saldzie. Ten zamiar powinien zawierać 10% całkowitych wypowiedzi w aplikacji.

## <a name="intent-limits"></a>Limity intencji
Przejrzyj [limity,](luis-boundaries.md#model-boundaries) aby zrozumieć, ile intencji można dodać do modelu. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Jeśli potrzebujesz więcej niż maksymalna liczba intencji 
Najpierw należy rozważyć, czy system używa zbyt wielu intencji. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Czy wiele intencji można połączyć w jeden zamiar z encjami 
Intencje, które są zbyt podobne może utrudnić usługi LUIS do odróżnienia ich. Intencje powinny być wystarczająco zróżnicowane, aby przechwycić główne zadania, o które prosi użytkownik, ale nie muszą przechwytywać każdej ścieżki, którą wykonuje kod. Na przykład BookFlight i FlightCustomerService mogą być oddzielne intencje w aplikacji podróży, ale BookInternationalFlight i BookDomesticFlight są zbyt podobne. Jeśli system musi je odróżnić, użyj jednostek lub innej logiki, a nie intencji. 

### <a name="dispatcher-model"></a>Model dyspozytora
Dowiedz się więcej o łączeniu aplikacji usługi LUIS i QnA maker z [modelem wysyłki.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Poproś o pomoc dla aplikacji o znacznej liczbie intencji
Jeśli zmniejszenie liczby intencji lub podzielenie zamiarów na wiele aplikacji nie działa, skontaktuj się z pomocą techniczną. Jeśli twoja subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure.](https://azure.microsoft.com/support/options/) 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [jednostkach](luis-concept-entity-types.md), które są ważnymi słowami istotnymi dla intencji
* Dowiedz się, jak [dodawać intencje i zarządzać nimi](luis-how-to-add-intents.md) w aplikacji usługi LUIS.
* Przejrzyj [najważniejsze wskazówki dotyczące intencji](luis-concept-best-practices.md)
