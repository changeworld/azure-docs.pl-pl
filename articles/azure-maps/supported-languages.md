---
title: Obsługa lokalizacji w Azure Maps | Microsoft Docs
description: Informacje o obsługiwanych językach dla usług w Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299844"
---
# <a name="localization-support-in-azure-maps"></a>Obsługa lokalizacji w Azure Maps

Azure Maps obsługuje różne języki i widoki na podstawie kraju/regionu. W tym artykule przedstawiono obsługiwane języki i widoki ułatwiające implementację Azure Maps.


## <a name="azure-maps-supported-languages"></a>Obsługiwane języki Azure Maps

Azure Maps zostały zlokalizowane w różnych językach w ramach usług. W poniższej tabeli przedstawiono obsługiwane kody języka dla każdej usługi.  
  

| ID         | Nazwa                   |  Mapy | Search | Wyznaczanie tras | Zdarzenia dotyczące ruchu | Formant mapy JS | Strefa czasowa |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| AF — za      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arabski                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EU-ES      | Baskijski                 |       |    ✓   |         |                   |                |     ✓     |
| BG — BG      | Bułgarski              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| CA-ES      | Kataloński                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Chiński (uproszczony)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Chiński (tradycyjny)  | zh-TW |  zh-TW |  zh-TW  |                   |      zh-TW     |     ✓     |
| HR-HR      | Chorwacki               |       |    ✓   |         |                   |                |     ✓     |
| CS — CZ      | Czeski                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Duński                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-NL      | Holenderski                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NL-to      | Holenderski (belgijski)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | Angielski (Australia)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EN-NZ      | Angielski (Nowa Zelandia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pl GB      | Angielski (Zjednoczone Królestwo) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pl-US      | Angielski (USA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estoński               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Fiński                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr — FR      | Francuski                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr — CA      | Francuski (Kanada)      |       |    ✓   |         |                   |                |     ✓     |
| GL — ES      | Galicyjski               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Niemiecki                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| El-GR      | Grecki                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| -IL      | Hebrajski                 |       |    ✓   |         |         ✓         |                |     ✓     |
| Witaj w usłudze      | Hindi                  |       |        |         |                   |                |     ✓     |
| HU — Węgry      | Węgierski              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Identyfikator ID      | Indonezyjski             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| IT      | Włoski                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japoński               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazachski                 |       |    ✓   |         |                   |                |     ✓     |
| Ko — KR      | Koreański                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| ES — 419     | Łaciński (Ameryka Łacińska) |       |    ✓   |         |                   |                |     ✓     |
| LV — LV      | Łotewski                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt — LT      | Litewski             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS — MY      | Malajski (łaciński)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| NB — nie      | Norweski (bokmål)       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutralne Języki w języku prawdy dla wszystkich regionów w lokalnych skryptach, jeśli są dostępne |   ✓     |        |         |                   |      ✓          |         |
| NGT — Latn   | Neutralna obudowa prawdy-łacińskiego exonyms. Skrypt łaciński zostanie użyty, jeśli jest dostępny |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polski                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Portugalski (Brazylia)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Portugalski (Portugalia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | Rumuński               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru — RU      | Rosyjski                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SR-Cyrl-RS | Serbski (Cyrylica)     |       |    Serbski (cyrylica) (SR-RS)   |         |                   |                |     ✓     |
| sr-latn-RS | Serbski (łaciński)        |       |        |         |                   |                |     ✓     |
| SK-SK      | Słowacka              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SL — SL      | Słoweński              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Hiszpański                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es — MX      | Hiszpański (Meksyk)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| SV — SE     | Szwedzki                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Tajski                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| TR-TR      | Turecki                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Zjednoczone Królestwo-UA      | Ukraiński               |       |    ✓   |         |                   |                |     ✓     |
| VI-VN      | Wietnamski             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Azure Maps obsługiwane widoki

> [!Note]
> Azure Maps zostało wydane w następujących krajach/regionach 1 sierpnia 2019:
>  * Argentyna
>  * Indie
>  * Maroko
>  * Pakistan
>
> Po 1 sierpnia 2019 ustawienie parametru **widoku** spowoduje zdefiniowanie zwróconej zawartości mapy dla nowych regionów/krajów wymienionych powyżej. Zachęcamy do upewnienia się, że skonfigurowano parametr View jako wymagany dla interfejsów API REST i zestawów SDK używanych przez używane usługi.
>  
>
>  **Interfejsy API REST:**
>  
>  Upewnij się, że parametr View został skonfigurowany zgodnie z wymaganiami. Parametr widoku Określa, który zestaw zawartości o treści geopolitycznej jest zwracany przez Azure Maps usług. 
>
>  Azure Maps usługi REST:
>    
>    * Pobierz kafelek mapy
>    * Pobierz obraz mapy 
>    * Pobierz rozmyte wyszukiwanie
>    * Pobierz punkt POI wyszukiwania
>    * Pobierz kategorię wyszukiwania punkt POI
>    * Rozpocznij wyszukiwanie w pobliżu
>    * Pobierz adres wyszukiwania
>    * Pobierz strukturę adresu wyszukiwania
>    * Uzyskaj zwrotny adres wyszukiwania
>    * Pobierz adres wyszukiwania odwrotnie
>    * Opublikuj wyszukiwanie w geometrii
>    * Zapoznawcza partia adresów wyszukiwania
>    * Adres wyszukiwania zwrotnego — wersja zapoznawcza
>    * Publikuj wyszukiwanie wzdłuż trasy
>    * Przeszukiwanie rozmytej partii w wersji zapoznawczej
>
>    
>  **Zestawy**
>
>  Upewnij się, że skonfigurowano parametr View zgodnie z wymaganiami i masz najnowszą wersję zestawu Web SDK i Android SDK. Uwzględnione zestawy SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Parametr **widoku** Azure Maps (określany również jako "parametr regionu użytkownika") to dwuliterowy kod kraju ISO-3166, który będzie pokazywał poprawne mapy dla danego kraju/regionu, określając, który zestaw geopoznawczych treści jest zwracany przez Azure Maps usługi, w tym obramowania i etykiety wyświetlane na mapie. 

Domyślnie parametr widoku jest ustawiony na **ujednolicony**, nawet jeśli nie został zdefiniowany w żądaniu. Jest odpowiedzialny za określenie lokalizacji użytkowników, a następnie ustawienie parametru widoku dla tej lokalizacji. Alternatywnie możesz ustawić opcję "View = Auto", która zwróci dane mapy na podstawie adresu IP żądania.  Parametr widoku w Azure Maps musi być używany zgodnie z obowiązującymi przepisami, włącznie z tymi, które dotyczą mapowania kraju, w którym są dostępne mapy, obrazy i inne dane oraz zawartość innych firm, do których uzyskuje się dostęp za pośrednictwem Azure Maps.


W poniższej tabeli przedstawiono obsługiwane widoki.

| Wyświetl         | Opis                            |  Mapy | Search | kontrolka mapy JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Zjednoczone Emiraty Arabskie (widok arabski)    |   ✓   |        |     ✓          |
| AR           | Argentyna (widok argentyński)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (widok arabski)                 |   ✓   |        |     ✓          |
| IN           | Indie (widok Indyjski)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (widok arabski)                    |   ✓   |        |     ✓          |
| JO           | Jordania (widok arabski)                  |   ✓   |        |     ✓          |
| KW           | Kuwejt (widok arabski)                  |   ✓   |        |     ✓          |
| LB           | Liban (widok arabski)                 |   ✓   |        |     ✓          |
| MA           | Maroko (widok marokański)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (widok arabski)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (widok pakistański)              |   ✓   |    ✓    |     ✓          |
| PS           | Autonomia Palestyńska (widok arabski)    |   ✓   |        |     ✓          |
| QA           | Katar (widok arabski)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudyjska (widok arabski)            |   ✓   |        |     ✓          |
| SY           | Syria (widok arabski)                   |   ✓   |        |     ✓          |
| YE           | Jemen (widok arabski)                   |   ✓   |        |     ✓          |
| Automatycznie         | Zwróć dane mapy na podstawie adresu IP żądania.|   ✓   |    ✓   |     ✓          |
| Zunifikowane      | Ujednolicony widok (inne)                  |   ✓   |   ✓     |     ✓          |
