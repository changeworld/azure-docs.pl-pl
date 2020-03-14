---
title: Obsługa języka — interfejs API wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220194"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania wiadomości Bing

Interfejs API wyszukiwania wiadomości Bing obsługuje wiele krajów/regionów, wiele z więcej niż jednym językiem. Określenie kraju/regionu z zapytaniem służy głównie do uściślenia wyników wyszukiwania w oparciu o zainteresowania w tym kraju/regionie. Ponadto wyniki mogą zawierać linki do usługi Bing, a te linki mogą lokalizować środowisko użytkownika Bing w zależności od określonego kraju/regionu lub języka.

Możesz określić kraj/region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu `Accept-Language` nagłówku HTTP. Obsługiwane języki różnią się w zależności od liczby/regionu; są one przyznawane dla każdego kraju/regionu w tabeli rynków.

Alternatywnie można określić rynek przy użyciu `mkt` parametru zapytania i kodu z tabeli **rynków** . Określenie rynku jednocześnie określa kraj/region i preferowany język. W tym przypadku parametr zapytania `setLang` może być ustawiony na kod języka; zwykle jest to ten sam język określony przez `mkt`, chyba że użytkownik woli zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynki dla punktu końcowego wyszukiwania wiadomości

W poniższej tabeli przedstawiono wartości kodów rynkowych, których można użyć do określenia `mkt` parametru zapytania dla punktu końcowego `/news/search`. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w `cc` parametr zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Austria|Niemiecki|de-AT|
|Szwajcaria|Niemiecki|de-CH|
|Niemcy|Niemiecki|de-DE.|
|Australia|Polski|EN-AU|
|Kanada|Polski|EN-CA|
|Wielka Brytania|Polski|en-GB|
|Indonezja|Polski|pl-ID|
|Irlandia|Polski|EN-IE|
|Indie|Polski|EN-IN|
|Malezja|Polski|pl — MY|
|Nowa Zelandia|Polski|EN NZ|
|Republika Filipin|Polski|EN-PH|
|Singapur|Polski|EN-SG|
|Stany Zjednoczone|Polski|pl-pl|
|Polski|Główny|pl-WW|
|Polski|Główny|pl-XA|
|Republika Południowej Afryki|Polski|pl-za|
|Argentyna|Hiszpański|ES-AR|
|Chile|Hiszpański|ES — CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-US|
|Hiszpański|Główny|es-XL|
|Finlandia|Fiński|fi-FI|  
|Francja|Francuski|fr-BE|
|Kanada|Francuski|fr-CA|
|Belgia|holenderski|nl-BE|
|Szwajcaria|Francuski|FR-CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT|
|Hongkong|Chiński tradycyjny|zh-HK|  
|Tajwan|Chiński tradycyjny|zh-TW|
|Japonia|Japoński|ja-JP|  
|Korea|Koreański|ko-KR|  
|Holandia|holenderski|NL-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|Szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynki dla punktu końcowego aktualności News
W poniższej tabeli przedstawiono wartości kodów rynkowych, których można użyć do określenia `mkt` parametru zapytania dla punktu końcowego `/news`. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w `cc` parametr zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Niemcy|Niemiecki|de-DE.|
|Australia|Polski|EN-AU|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-pl|
|Polski|Główny|pl-WW|
|Chile|Hiszpański|ES — CL|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-US|
|Finlandia|Fiński|fi-FI|  
|Kanada|Francuski|fr-CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT|
|Brazylia|portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynki dla punktu końcowego trendów Aktualności
W poniższej tabeli przedstawiono wartości kodów rynkowych, których można użyć do określenia `mkt` parametru zapytania dla punktu końcowego `/news/trendingtopics`. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, które można określić w `cc` parametr zapytania, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynkowy|  
|---------------------|--------------|-----------------|
|Niemcy|Niemiecki|de-DE.|
|Australia|Polski|EN-AU|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-pl|
|Kanada|Polski|EN-CA|
|Indie|Polski|EN-IN|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr-CA|
|Brazylia|portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej znajdują się kody kraju/regionu, które można określić w parametrze zapytania `cc`. Lista może ulec zmianie.  

|Kraj/region|Numer kierunkowy kraju|  
|---------------------|------------------|  
|Argentyna|AR|  
|Australia|AU|  
|Austria|AT|  
|Belgia|BE|  
|Brazylia|BR|  
|Kanada|CA|  
|Chile|CL|  
|Dania|DK|  
|Finlandia|FI|  
|Francja|PW|  
|Niemcy|DE|  
|Hongkong|HK|  
|Indie|IN|  
|Indonezja|ID|  
|Włochy|IT|  
|Japonia|JP|  
|Korea|KR|  
|Malezja|MY|  
|Meksyk|MX|  
|Holandia|NL|  
|Nowa Zelandia|NZ|  
|Norwegia|NO|  
|Chińska Republika Ludowa|CN|  
|Polska|PL|  
|Portugalia|PT|  
|Republika Filipin|PH|  
|Rosja|RU|  
|Arabia Saudyjska|SA|  
|Republika Południowej Afryki|ZA|  
|Hiszpania|ES|  
|Szwecja|SE|  
|Szwajcaria|CH|  
|Tajwan|TW|  
|Turcja|TR|  
|Wielka Brytania|GB|  
|Stany Zjednoczone|US|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o wyszukiwanie wiadomości Bing punktach końcowych, zobacz [Wyszukiwanie wiadomości API wersji 7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
