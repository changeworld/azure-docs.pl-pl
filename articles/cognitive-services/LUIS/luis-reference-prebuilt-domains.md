---
title: Dokumentacja wstępnie utworzonych domen
titleSuffix: Azure
description: Dokumentacja ze wstępnie utworzonych domen, które są wstępnie kolekcjami intencje i podmioty z Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6c0ecf70935d4504472156f6bda726d94f130dbb
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148180"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Dokumentacja ze wstępnie utworzonych domen aplikacją usługi LUIS
Ta dokumentacja zawiera informacje na temat [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md), które są wstępnie utworzonych kolekcji intencje i podmioty, które są oferowane usługi LUIS.

[Domeny niestandardowe](luis-how-to-start-new-app.md), z drugiej strony, rozpoczynać się nie intencje i modeli. Można dodać żadnych wbudowanych domeny intencje i podmioty do modelu niestandardowego.

# <a name="supported-domains-across-cultures"></a>Obsługiwane domeny różnych kultur

Tylko obsługiwane kultury jest język angielski. 

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
|Kalendarz|Kalendarz jest coś o osobiste spotkania i terminy, _nie_ zdarzenia publiczne (takie jak świata w piłce harmonogramy, kalendarze zdarzeń Seattle lub kalendarze ogólnych (np. dnia jest on już dziś, co fall rozpocząć, po Labor Day).|
|Komunikacja|Żądania do nawiązywania połączeń, wysyłanie teksty lub wiadomości błyskawiczne Znajdowanie i dodawanie kontaktów i różnych innych komunikacji żądania dotyczące (zazwyczaj wychodząca). Nazwisko osoby kontaktowej tylko zapytania nie należą do domeny komunikacji.|
|Email|Wiadomość e-mail ma domenę podrzędną domeny komunikacji. Zawiera ona głównie żądań wysyłania i odbierania komunikatów za pośrednictwem wiadomości e-mail.|
|HomeAutomation|Domeny HomeAutomation zapewnia intencje i podmioty, związane z sterowanie urządzeniami inteligentnymi macierzystego. Obsługuje ona głównie polecenia sterowania związane z światła i klimatyzacyjne, ale ma pewne możliwości Generalizacja dla innych urządzeń electric.|
|Uwagi|Uwaga domeny zapewnia intencje i podmioty, do tworzenia notatek i zapisanie elementów dla użytkowników.|
|Miejsca|Umieszcza obejmują firmom, instytucje, restauracje, publicznego miejsca do magazynowania i adresów. Domena obsługuje miejscu znajdowania i dotyczący informacji o miejscu publicznym, takie jak lokalizacja, działających w godzinach i odległość.|
|RestaurantReservation|Domeny rezerwacji restauracji obsługuje intencji do obsługi rezerwacji miejsca do restauracji.|
|ToDo|Domena ToDo zapewnia typy list zadań służący do dodawania, oznaczania i usuwania ich wykonania.|
|ToDo_IPA|Oparte na domenie ToDo, ToDo_IPA jest dostosowaną wersją rozszerzania jednostek w zadań do wykonania. Wersja archiwum IPA zapewnia intencje i podmioty, do obsługi rozpoznawania typu listy zadań do wykonania. Model definiuje trzy rodzaje listy zadań do wykonania: lista artykułów spożywczych, lista zakupów i Lista zadań do wykonania, obejmujące innych przypadkach.|
|Usługi użyteczności publicznej|Narzędzia domena jest domeną ogólne, wśród wszystkich LUIS wstępnie utworzone modele, które zawiera typowe intencje i wypowiedzi w scenariuszach różnica.|
|Pogoda|Domeny pogody koncentruje się na sprawdzanie warunków pogodowych i porady dotyczące lokalizacji i czasu lub Sprawdzanie czasu według warunków pogodowych.|
|Sieć Web|Domeny sieci Web udostępnia intencje i podmioty wyszukiwania dla witryny sieci Web.|
