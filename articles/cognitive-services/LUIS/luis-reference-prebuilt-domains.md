---
title: Wbudowane odwołanie do domeny — LUIS
titleSuffix: Azure Cognitive Services
description: Dokumentacja ze wstępnie utworzonych domen, które są wstępnie kolekcjami intencje i podmioty z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6e701396191ec8bf66ece0e2858b9f32857e7b17
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933582"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Wstępnie skompilowane odwołanie do domeny dla aplikacji LUIS
Ta dokumentacja zawiera informacje na temat [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md), które są wstępnie utworzonych kolekcji intencje i podmioty, które są oferowane usługi LUIS.

[Domeny niestandardowe](luis-how-to-start-new-app.md), z drugiej strony, rozpoczynać się nie intencje i modeli. Można dodać żadnych wbudowanych domeny intencje i podmioty do modelu niestandardowego.

## <a name="supported-domains-across-cultures"></a>Obsługiwane domeny między kulturami

Jedyną obsługiwaną kulturą jest język angielski. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Typ jednostki|description|
|--|--|
|Kalendarz|W kalendarzu znajdują się informacje o spotkaniach osobistych i terminach, a _nie_ na zdarzeniach publicznych (takich jak harmonogramy świata filiżanki, kalendarze zdarzeń Seattle) lub kalendarze generyczne (na przykład dzień dzisiejszy to, co się stało, gdy jest to dzień robocizny).|
|Komunikacja|Żądania wykonania wywołań, wysyłania tekstów lub wiadomości błyskawicznych, znajdowania i dodawania kontaktów oraz różnych żądań związanych z komunikacją (zwykle wychodzących). Zapytania tylko do nazwy kontaktu nie należą do domeny komunikacji.|
|Email|Poczta e-mail jest poddomeną domeny komunikacji. Obejmuje ona głównie żądania wysyłania i odbierania wiadomości za poorednictwem wiadomości e-mail.|
|HomeAutomation|Domena HomeAutomation zapewnia intencje i jednostki związane z kontrolowaniem inteligentnych urządzeń domowych. Jest ona głównie obsługiwana przez polecenie sterowania związane ze światłami i warunkiem oblotu, ale ma pewne możliwości uogólnienia dla innych urządzeń elektrycznych.|
|Uwagi|Uwaga domena zawiera intencje i jednostki do tworzenia notatek i zapisywania elementów dla użytkowników.|
|Miejsca|Miejsca obejmują firmy, instytucje, restauracje, publiczne miejsca i adresy. Domena obsługuje wyszukiwanie i pytania dotyczące informacji o miejscu publicznym, takich jak lokalizacja, godziny operacyjne i odległość.|
|RestaurantReservation|Domena rezerwacji restauracji obsługuje intencje obsługi rezerwacji dla restauracji.|
|Czynność|Domena do zrobienia zawiera typy list zadań, które użytkownicy mogą dodawać, oznaczać i usuwać elementy do wykonania.|
|Usługi użyteczności publicznej|Domena narzędzi to ogólna domena obejmująca wszystkie wstępnie zbudowane modele LUIS, które zawierają typowe intencje i wyrażenia długości w scenariuszach różnicowych.|
|Pogoda|Domena pogody koncentruje się na sprawdzaniu warunków pogodowych i klasyfikatorów z lokalizacją i czasem lub sprawdzaniem czasu według warunków pogodowych.|
|sieć Web|Domena sieci Web zapewnia zamiar i jednostki do wyszukiwania w witrynie sieci Web.|
