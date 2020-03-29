---
title: Obsługa języka — interfejs API wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów obsługiwanych przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220194"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Obsługa języka i regionu interfejsu API wyszukiwania wiadomości Bing

Interfejs API wyszukiwania wiadomości Bing obsługuje wiele krajów/regionów, wiele z więcej niż jednym językiem. Określanie kraju/regionu za pomocą kwerendy służy przede wszystkim do zawężania wyników wyszukiwania na podstawie zainteresowań w tym kraju/regionie. Ponadto wyniki mogą zawierać łącza do usługi Bing, a te łącza mogą lokalizować środowisko użytkownika usługi Bing zgodnie z określonym krajem/regionem lub językiem.

Można określić kraj/region `cc` przy użyciu parametru kwerendy. Jeśli określisz kraj/region, należy również określić jeden `Accept-Language` lub więcej kodów języków za pomocą nagłówka HTTP. Obsługiwane języki różnią się w zależności od licznika/regionu; dla każdego kraju/regionu w tabeli Rynki.

Alternatywnie można określić rynek `mkt` przy użyciu parametru zapytania i kodu z **tabeli Rynki.** Określenie rynku jednocześnie określa kraj/region i preferowany język. Parametr `setLang` kwerendy może być ustawiony na kod języka w tym przypadku; zazwyczaj jest to ten `mkt` sam język określony przez chyba że użytkownik woli, aby zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynki dla punktu końcowego wyszukiwania wiadomości

Dla `/news/search` punktu końcowego w poniższej tabeli wymieniono wartości `mkt` kodu rynku, których można użyć do określenia parametru kwerendy. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, `cc` które można określić w parametrze kwerendy, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynku|  
|---------------------|--------------|-----------------|
|Dania|duński|da-DK|
|Austria|Niemiecki|de-AT|
|Szwajcaria|Niemiecki|de-CH|
|Niemcy|Niemiecki|de-DE|
|Australia|Polski|pl-UA|
|Kanada|Polski|pl-CA|
|Wielka Brytania|Polski|en-GB|
|Indonezja|Polski|en-ID|
|Irlandia|Polski|pl-IE|
|Indie|Polski|pl-IN|
|Malezja|Polski|pl-MY|
|Nowa Zelandia|Polski|pl-NZ|
|Republika Filipin|Polski|pl-PH|
|Singapur|Polski|en-SG|
|Stany Zjednoczone|Polski|pl-PL|
|Polski|Ogólne|pl-WW|
|Polski|Ogólne|pl-XA|
|Republika Południowej Afryki|Polski|pl-ZA|
|Argentyna|Hiszpański|es-AR|
|Chile|Hiszpański|es-CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-USA|
|Hiszpański|Ogólne|es-XL|
|Finlandia|fiński|fi-FI|  
|Francja|Francuski|fr-BE|
|Kanada|Francuski|fr-CA|
|Belgia|Niderlandzki|nl-BE|
|Szwajcaria|Francuski|fr-CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|it-IT|
|SRA Hongkong|Chiński tradycyjny|zh-HK|  
|Tajwan|Chiński tradycyjny|zh-TW|
|Japonia|Japoński|ja-JP|  
|Korea|Koreański|ko-KR|  
|Holandia|Niderlandzki|nl-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|Portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynki dla punktu końcowego wiadomości
Dla `/news` punktu końcowego w poniższej tabeli wymieniono wartości `mkt` kodu rynku, których można użyć do określenia parametru kwerendy. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, `cc` które można określić w parametrze kwerendy, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynku|  
|---------------------|--------------|-----------------|
|Dania|duński|da-DK|
|Niemcy|Niemiecki|de-DE|
|Australia|Polski|pl-UA|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Polski|Ogólne|pl-WW|
|Chile|Hiszpański|es-CL|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-USA|
|Finlandia|fiński|fi-FI|  
|Kanada|Francuski|fr-CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|it-IT|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynki dla punktu końcowego trendów informacyjnych
Dla `/news/trendingtopics` punktu końcowego w poniższej tabeli wymieniono wartości `mkt` kodu rynku, których można użyć do określenia parametru kwerendy. Bing zwraca zawartość tylko dla tych rynków. Lista może ulec zmianie.  

Aby uzyskać listę kodów krajów/regionów, `cc` które można określić w parametrze kwerendy, zobacz [Kody krajów](#countrycodes).  

|Kraj/region|Język|Kod rynku|  
|---------------------|--------------|-----------------|
|Niemcy|Niemiecki|de-DE|
|Australia|Polski|pl-UA|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Kanada|Polski|pl-CA|
|Indie|Polski|pl-IN|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr-CA|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej przedstawiono kody kraju/regionu, `cc` które można określić w parametrze kwerendy. Lista może ulec zmianie.  

|Kraj/region|Kod kraju|  
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
|SRA Hongkong|HK|  
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
|Stany Zjednoczone|USA|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat punktów końcowych wyszukiwania wiadomości Bing, zobacz [Odwołanie do interfejsu API wyszukiwania wiadomości w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
