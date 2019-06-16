---
title: Obsługa lokalizacji w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Więcej informacji o językach obsługiwanych dla usług Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1928185521419006a487a933e2ecba79894a09d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686786"
---
# <a name="localization-support-in-azure-maps"></a>Obsługa lokalizacji w usługi Azure Maps

Usługi Azure Maps obsługuje różne języki i widoki oparte na kraj/region. Ten artykuł zawiera obsługiwane języki i widoków, aby pomóc w zaimplementowaniu usługi Azure Maps.


## <a name="azure-maps-supported-languages"></a>Usługi Azure Maps obsługiwane języki

Usługi Azure Maps zostały zlokalizowane w różnych językach dla usługi. W poniższej tabeli przedstawiono kody obsługiwanych języków, dla każdej usługi.  
  

| ID         | Name (Nazwa)                   |  Maps | Wyszukiwanie | Routing | Zdarzenia ruchu | Kontrolki mapy Javascript | Strefa czasowa |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arabski                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Baskijski                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Bułgarski              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ES urzędu certyfikacji      | Kataloński                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Chiński (uproszczony)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Chiński (tradycyjny)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | Chorwacki               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Czeski                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Akcelerator deweloperski w wersji DK      | Duński                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-NL      | Holenderski                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Holenderski (Belgia)        |       |    ✓   |         |                   |                |     ✓     |
| EN-AU      | Angielski (Australia)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EN NZ      | Angielski (Nowa Zelandia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Angielski (Wielka Brytania) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Angielski (USA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estoński               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Fiński                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Francuski                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Francuski (Kanada)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galicyjski               |       |    ✓   |         |                   |                |     ✓     |
| de-DE.      | Niemiecki                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | Grecki                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| HE-IL      | Hebrajski                 |       |    ✓   |         |         ✓         |                |     ✓     |
| w      | Hindi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Węgierski              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonezyjski             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| IT-IT      | Włoski                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japoński               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazachski                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Koreański                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Hiszpański Ameryki Łacińskiej |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Łotewski                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Litewski             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | Malajski (łaciński)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norwegian Bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Niezależny od podstaw prawdziwość - językach we wszystkich regionach w skryptów lokalnych, jeśli jest dostępny |   ✓     |        |         |                   |      ✓          |         |
| NGT Latn   | Niezależny od podstaw prawdziwość - exonyms alfabetu łacińskiego. Łacińskim będzie używany, jeśli jest dostępny |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polski                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Portugalski (Brazylia)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Portugalski (Portugalia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO RO      | Rumuński               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Rosyjski                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Serbski (Cyrylica)     |       |    Serbski, Cyrylica (sr r)   |         |                   |                |     ✓     |
| Funkcja SR-Latn-RS | Serbski (łaciński)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Słowacki              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Słoweński              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Hiszpański                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Hiszpański (Meksyk)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Szwedzki                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th TH      | Tajlandzki                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turecki                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ukraiński               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Wietnamski             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Usługi Azure Maps obsługiwane widoki

Azure parametru widoku mapy (nazywane również "parametr regionu użytkownika") jest litera 2 ISO 3166 kod kraju, w które ukazują poprawne mapowań dla tego kraju/regionu, określając, który zestaw kwestie geopolityczne sporna obramowania i etykiety są wyświetlane na mapie.  Domyślnie widok parametr ma wartość **"Unified"** .  Kraje/regiony, które nie znajdują się na liście Widok jest domyślnie do widoku "Unified". Jest odpowiedzialny za określenie lokalizacji użytkowników, a następnie ustaw parametr widoku poprawnie dla tej lokalizacji. Należy użyć parametru widoku w usługi Azure Maps zgodne z obowiązującym prawem, łącznie z tymi jest udostępniana dotyczących mapowania kraju, w którym mapy, obrazy i inną zawartością danych i innych firm, która jest autoryzowany dostęp za pośrednictwem usługi Azure Maps.

W poniższej tabeli przedstawiono obsługiwane widoki.

| Widok         | Opis                            |  Maps | Wyszukiwanie | Kontrolki mapy Javascript |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Zjednoczone Emiraty Arabskie (arabski widok)    |   ✓   |        |     ✓          |
| AR           | Argentyna (argentyńskie widok)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (arabski widok)                 |   ✓   |        |     ✓          |
| IN           | Indie (indyjskich widok)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabski widok)                    |   ✓   |        |     ✓          |
| JO           | Jordania (arabski widok)                  |   ✓   |        |     ✓          |
| KW           | Kuwejt (arabski widok)                  |   ✓   |        |     ✓          |
| LB           | Liban (arabski widok)                 |   ✓   |        |     ✓          |
| MA           | Maroko (marokański widok)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabski widok)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (Widok Pakistanu)              |   ✓   |    ✓    |     ✓          |
| PS           | Autonomia Palestyńska (arabski widok)    |   ✓   |        |     ✓          |
| QA           | Katar (arabski widok)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudyjska (arabski widok)            |   ✓   |        |     ✓          |
| SY           | Syria (arabski widok)                   |   ✓   |        |     ✓          |
| YE           | Jemen (arabski widok)                   |   ✓   |        |     ✓          |
| Unifikacja      | Ujednolicony widok (inne)                  |   ✓   |   ✓     |     ✓          |
