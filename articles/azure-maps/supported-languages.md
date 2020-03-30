---
title: Pomoc techniczna w celu lokalizacji | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o obsługiwanych językach usług w usługach Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b3733dfc32dae21ddcf4c5f73cddf9ad6b7fc59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334069"
---
# <a name="localization-support-in-azure-maps"></a>Pomoc techniczna aplikacji Localization w usłudze Azure Maps

Usługa Azure Maps obsługuje różne języki i widoki na podstawie kraju/regionu. Ten artykuł zawiera obsługiwane języki i widoki, aby ułatwić prowadzenie implementacji usługi Azure Maps.


## <a name="azure-maps-supported-languages"></a>Obsługiwane języki usługi Azure Maps

Usługa Azure Maps została zlokalizowana w różnych językach w swoich usługach. Poniższa tabela zawiera obsługiwane kody języków dla każdej usługi.  
  

| ID         | Nazwa                   |  Maps | Wyszukiwanie | Routing | Pogoda | Zdarzenia drogowe | Sterowanie mapą JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabski                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesz)    |       |       |         |     ✓    |                   |                |
| bn-in      | Bangla (Indie)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bośniacki                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baskijski                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bułgarski              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Kataloński                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chiński uproszczony   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chiński (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chiński (Tajwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Chorwacki               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Czeski                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | duński                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holenderski (Belgia)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Niderlandzki (Holandia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pl-UA      | Angielski (Australia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pl-NZ      | Angielski (Nowa Zelandia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angielski (Wielka Brytania) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pl-PL      | Angielski (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estoński               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | fiński                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francuski                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | francuski (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galicyjski               |       |    ✓   |         |         |                   |                |
| de-DE      | Niemiecki                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | grecki                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Gudżarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebrajski                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | węgierski              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Islandzki              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonezyjski             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Włoski                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japoński               |       |        |         |     ✓    |                   |                |
| kn-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazachski                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreański                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Hiszpański latynoamerykański |       |    ✓   |         |         |                   |                |
| lv-LV      | Łotewski                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litewski             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedoński             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajski (łaciński)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norweski (bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| Ngt (ł.101        | Neutral Ground Truth - Języki urzędowe dla wszystkich regionów w lokalnych skryptach, jeśli są dostępne |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral Ground Truth - łacińskie egzyny. Skrypt łaciński będzie używany, jeśli jest dostępny |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polski                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | portugalski (Brazylia)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugalski (Portugalia)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Pendżabski                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumuński               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Rosyjski                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbski (cyrylica)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Serbski (łaciński)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Słowacki             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Słoweński              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Hiszpański                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Hiszpański (Meksyk)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | szwedzki                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (Indie)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Indie)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Tajski                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turecki                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukraiński               |       |    ✓   |         |     ✓    |                   |                |
| twój-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbecki                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Wietnamski             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Obsługiwane widoki usługi Azure Maps

> [!Note]
> 1 sierpnia 2019 r. usługa Azure Maps została wydana w następujących krajach/regionach:
>  * Argentyna
>  * Indie
>  * Maroko
>  * Pakistan
>
> Po 1 sierpnia 2019 r. parametr **Widok** zdefiniuje zwróconą zawartość mapy dla nowych regionów/krajów wymienionych powyżej. Parametr usługi Azure Maps **View** (nazywany również "parametrem regionu użytkownika") to dwuliterowy kod kraju ISO-3166, który będzie wyświetlał poprawne mapy dla tego kraju/regionu określające, który zestaw zawartości spornej geopolitycznie jest zwracany za pośrednictwem usług Azure Maps, w tym obramowania i etykiety wyświetlane na mapie. 

Upewnij się, że skonfigurować **View** parametru zgodnie z wymaganiami dla interfejsów API REST i zestawów SDK, które są używane przez usługi.
>  
>
>  **Interfejsy API odpoczynku:**
>  
>  Upewnij się, że w razie potrzeby skonfigurowałeś parametr Widok. Parametr View określa, który zestaw zawartości spornej geopolitycznie jest zwracany za pośrednictwem usług Azure Maps. 
>
>  Usługi REST usługi usługi Azure Maps:
>    
>    * Pobierz kafelek mapy
>    * Pobierz obraz mapy 
>    * Szukaj rozmyte
>    * Pobierz poi wyszukiwania
>    * Pobierz kategorię TRĄka wyszukiwania
>    * Pobierz wyszukiwanie w pobliżu
>    * Uzyskaj adres wyszukiwania
>    * Uzyskaj uporządkowany adres wyszukiwania
>    * Odwróć adres wyszukiwania
>    * Pobierz adres wyszukiwania Reverse Cross Street
>    * Opublikuj wyszukiwanie wewnątrz geometrii
>    * Opublikuj podgląd partii adresu wyszukiwania
>    * Opublikuj podgląd odwrotnej partii adresu wyszukiwania
>    * Opublikuj wyszukiwanie wzdłuż trasy
>    * Post Szukaj Rozmyte Podgląd partii
>
>    
>  **Zestawy sdk:**
>
>  Upewnij się, że skonfigurowałeś **parametr Widok** zgodnie z wymaganiami i masz najnowszą wersję zestawu SDK sieci Web i zestawu SDK systemu Android. SDK, których dotyczy problem:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK

Domyślnie parametr Widok jest ustawiony na **Ujednolicony**, nawet jeśli nie zdefiniowano go w żądaniu. Określ lokalizację użytkowników. Następnie ustaw poprawnie parametr **Widok** dla tej lokalizacji. Alternatywnie można ustawić "View=Auto", który zwróci dane mapy na podstawie adresu IP żądania.  Parametr **Widok** w usłudze Azure Maps musi być używany zgodnie z obowiązującymi przepisami, w tym z przepisami dotyczącymi mapowania kraju, w którym udostępniane są mapy, obrazy i inne dane oraz zawartość innych firm, do których masz prawo dostępu za pośrednictwem usługi Azure Maps.


W poniższej tabeli przedstawiono obsługiwane widoki.

| Widok         | Opis                            |  Maps | Wyszukiwanie | Sterowanie mapą JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Zjednoczone Emiraty Arabskie (widok arabski)    |   ✓   |        |     ✓          |
| AR           | Argentyna (widok argentyński)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrajn (widok arabski)                 |   ✓   |        |     ✓          |
| IN           | Indie (Widok indyjski)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (widok arabski)                    |   ✓   |        |     ✓          |
| JO           | Jordania (widok arabski)                  |   ✓   |        |     ✓          |
| KW           | Kuwejt (widok arabski)                  |   ✓   |        |     ✓          |
| LB           | Liban (widok arabski)                 |   ✓   |        |     ✓          |
| MA           | Maroko (widok marokański)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (widok arabski)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (Widok pakistański)              |   ✓   |    ✓    |     ✓          |
| PS           | Autonomia Palestyńska (widok arabski)    |   ✓   |        |     ✓          |
| QA           | Katar (widok arabski)                   |   ✓   |        |     ✓          |
| SA           | Arabia Saudyjska (widok arabski)            |   ✓   |        |     ✓          |
| SY           | Syria (widok arabski)                   |   ✓   |        |     ✓          |
| Ye           | Jemen (widok arabski)                   |   ✓   |        |     ✓          |
| Auto         | Zwraca dane mapy na podstawie adresu IP żądania.|   ✓   |    ✓   |     ✓          |
| Unified      | Widok ujednolicony (inne)                  |   ✓   |   ✓     |     ✓          |
