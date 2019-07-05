---
title: Obsługa lokalizacji w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Więcej informacji o językach obsługiwanych dla usług Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446184"
---
# <a name="localization-support-in-azure-maps"></a>Obsługa lokalizacji w usługi Azure Maps

Usługi Azure Maps obsługuje różne języki i widoki oparte na kraj/region. Ten artykuł zawiera obsługiwane języki i widoków, aby pomóc w zaimplementowaniu usługi Azure Maps.


## <a name="azure-maps-supported-languages"></a>Usługi Azure Maps obsługiwane języki

Usługi Azure Maps zostały zlokalizowane w różnych językach dla usługi. W poniższej tabeli przedstawiono kody obsługiwanych języków, dla każdej usługi.  
  

| id         | Name (Nazwa)                   |  Maps | Wyszukiwanie | Routing | Zdarzenia ruchu | Kontrolki mapy Javascript | Strefa czasowa |
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

> [!Note]
> Udostępniamy usługi Azure Maps w następujących krajach/regionach na 1 sierpnia 2019 r.:
>  * Argentyna
>  * Indie
>  * Maroko
>  * Pakistan
>
> Po 1 sierpnia 2019 **widoku** ustawienie parametru określi zawartości mapy zwrócony dla nowych krajach/regionach wymienionych powyżej. Firma Microsoft zachęca do upewnij się, że po skonfigurowaniu parametru widoku zgodnie z wymaganiami dla interfejsów API REST i zestawów SDK, które korzystają z usługi.
>  
>
>  **Interfejsy API REST:**
>  
>  Upewnij się, że po skonfigurowaniu parametru widoku zgodnie z potrzebami. Wyświetl parametr określa, który zestaw kwestie geopolityczne sporne zawartości jest zwracana za pośrednictwem usługi Azure Maps. 
>
>  Usługi REST dotyczy usługi Azure Maps:
>    
>    * Pobierz Kafelek mapy
>    * Pobierz obraz mapy 
>    * Pobierz wyszukiwania rozmytego
>    * Pobierz wyszukiwanie punktu orientacyjnego
>    * Pobierz wyszukiwanie punktu orientacyjnego kategorii
>    * Pobierz wyszukiwanie pobliskiej
>    * Uzyskaj adres wyszukiwania
>    * Uzyskaj adres wyszukiwania, struktura
>    * Uzyskaj adres wyszukiwania wstecznego
>    * Pobierz ulica między odwrotnego wyszukiwania adresu
>    * Wyszukaj wpis wewnątrz geometrii
>    * Wpis wyszukiwania adresów usługi Batch w wersji zapoznawczej
>    * Opublikuj Batch odwrotnego wyszukiwania adresu — wersja zapoznawcza
>    * Wyszukaj wpis wzdłuż trasy
>    * Wpis wyszukiwania rozmytego Batch w wersji zapoznawczej
>
>    
>  **Zestawy SDK:**
>
>  Upewnij się, że po skonfigurowaniu parametru widoku zgodnie z wymaganiami, a masz najnowszą wersję zestawu SDK sieci Web i zestawu SDK systemu Android. Wpływ na zestawy SDK:
>
>    * Zestaw SDK sieci Web usługi Azure Maps
>    * Zestaw SDK systemu Android z usługi Azure Maps


Usługi Azure Maps **widoku** parametru (nazywane również "parametr regionu użytkownika") jest literą dwóch jest ISO-3166 numer kierunkowy kraju pokazujące mapy poprawne dla tego kraju/regionu, określając, który zestaw kwestie geopolityczne sporna zawartości zwracany za pomocą usługi Azure Maps, w tym obramowania i etykiety wyświetlanych na mapie. 

Domyślnie widok parametr ma wartość **ujednoliconej**, nawet jeśli jeszcze nie być określona w żądaniu. Jest odpowiedzialny za określenie lokalizacji użytkowników, a następnie ustaw parametr widoku poprawnie dla tej lokalizacji. Również mieć możliwość ustawienia "View = Auto", co spowoduje zwrócenie dane mapy na podstawie adresu IP żądania.  Należy użyć parametru widoku w usługi Azure Maps zgodne z obowiązującym prawem, łącznie z tymi jest udostępniana dotyczących mapowania kraju, w którym mapy, obrazy i inną zawartością danych i innych firm, która jest autoryzowany dostęp za pośrednictwem usługi Azure Maps.


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
| Automatycznie         | Zwróć dane mapy na podstawie adresu IP żądania.|   ✓   |    ✓   |     ✓          |
| Unifikacja      | Ujednolicony widok (inne)                  |   ✓   |   ✓     |     ✓          |
