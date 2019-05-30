---
title: Obsługa języków — interfejs API wyszukiwania wiadomości Bing
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
ms.openlocfilehash: e8a7f6d20ed36c4b3e900602fb52e31dceefae88
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799329"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Obsługa języka i regionu dla interfejsu API wyszukiwania wiadomości Bing

Interfejs API wyszukiwania wiadomości Bing obsługuje wiele kraje/regiony, wiele z więcej niż jednym języku. Określanie kraj/region z zapytaniem służy przede wszystkim, aby zawęzić wyniki wyszukiwania, w oparciu o zainteresowania tego kraju/regionu. Ponadto wyniki mogą zawierać łącza do usługi Bing, a te linki mogą lokalizować Bing środowiska użytkownika zgodnie z określonego kraju/regionu lub języka.

Można określić kraj/region, w którym używana jest `cc` parametr zapytania. Jeśli określisz kraju/regionu, należy także określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależą od countr/region są one podane dla każdego kraju/regionu w tabeli rynkach.

Alternatywnie, można określić przy użyciu rynku `mkt` parametr zapytania i kod z **rynków** tabeli. Określanie na rynek, jednocześnie Określa kraj/region i język preferowany. `setLang` Parametr zapytania może być ustawiona na kod języka w tym przypadku; zazwyczaj jest to ten sam język określony przez `mkt` , chyba że użytkownik chce zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynki dla punkt końcowy wyszukiwania wiadomości

Aby uzyskać `/news/search` punktu końcowego, w poniższej tabeli wymieniono wartości kodu na rynku, które umożliwia określenie `mkt` parametr zapytania. Wyszukiwarka Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  

Kodów listę kraj/region, który można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych krajów](#countrycodes).  

|Kraj/region|Język|Rynek kodu|  
|---------------------|--------------|-----------------|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Austria|Niemiecki|de-AT|
|Szwajcaria|Niemiecki|de-CH|
|Niemcy|Niemiecki|de-DE.|
|Australia|Angielski|EN-AU|
|Kanada|Angielski|EN-CA|
|Zjednoczone Królestwo|Angielski|en-GB|
|Indonezja|Angielski|EN-ID|
|Irlandia|Angielski|EN-IE|
|Indie|Angielski|EN-IN|
|Malezja|Angielski|Moje en|
|Nowa Zelandia|Angielski|EN NZ|
|Republika Filipin|Angielski|en-PH|
|Singapur|Angielski|en-SG|
|Stany Zjednoczone Ameryki|Angielski|en-US|
|Angielski|Ogólne|EN-WW|
|Angielski|Ogólne|en-XA|
|RPA|Angielski|en-ZA|
|Argentyna|Hiszpański|ES AR|
|Chile|Hiszpański|ES-CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone Ameryki|Hiszpański|es-US|
|Hiszpański|Ogólne|es-XL|
|Finlandia|Fiński|fi-FI|  
|Francja|Francuski|fr-BE|
|Kanada|Francuski|fr-CA|
|Belgia|Holenderski|nl-BE|
|Szwajcaria|Francuski|FR-CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT|
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|  
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Japonia|Japoński|ja-JP|  
|Korea Południowa|Koreański|ko-KR|  
|Holandia|Holenderski|NL-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|Portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|Szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynki dla punktu końcowego wiadomości
Aby uzyskać `/news` punktu końcowego, w poniższej tabeli wymieniono wartości kodu na rynku, które umożliwia określenie `mkt` parametr zapytania. Wyszukiwarka Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  

Kodów listę kraj/region, który można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych krajów](#countrycodes).  

|Kraj/region|Język|Rynek kodu|  
|---------------------|--------------|-----------------|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Niemcy|Niemiecki|de-DE.|
|Australia|Angielski|EN-AU|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone Ameryki|Angielski|en-US|
|Angielski|Ogólne|EN-WW|
|Chile|Hiszpański|ES-CL|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone Ameryki|Hiszpański|es-US|
|Finlandia|Fiński|fi-FI|  
|Kanada|Francuski|fr-CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynki dla punktu końcowego popularne wiadomości
Aby uzyskać `/news/trendingtopics` punktu końcowego, w poniższej tabeli wymieniono wartości kodu na rynku, które umożliwia określenie `mkt` parametr zapytania. Wyszukiwarka Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  

Kodów listę kraj/region, który można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych krajów](#countrycodes).  

|Kraj/region|Język|Rynek kodu|  
|---------------------|--------------|-----------------|
|Niemcy|Niemiecki|de-DE.|
|Australia|Angielski|EN-AU|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone Ameryki|Angielski|en-US|
|Kanada|Angielski|EN-CA|
|Indie|Angielski|EN-IN|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr-CA|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej przedstawiono kodów krajów/regionów, które mogą być określone w `cc` parametr zapytania. Lista może ulec zmianie.  

|Kraj/region|Kod kraju|  
|---------------------|------------------|  
|Argentyna|AR|  
|Australia|AU|  
|Austria|AT|  
|Belgia|BE|  
|Brazylia|BR|  
|Kanada|Urząd certyfikacji|  
|Chile|CL|  
|Dania|DK|  
|Finlandia|FI|  
|Francja|PW|  
|Niemcy|DE|  
|SRA Hongkong|HK|  
|Indie|IN|  
|Indonezja|ID|  
|Włochy|it|  
|Japonia|JP|  
|Korea Południowa|KR|  
|Malezja|MY|  
|Meksyk|MX|  
|Holandia|NL|  
|Nowa Zelandia|NZ|  
|Norwegia|NIE|  
|Chińska Republika Ludowa|CN|  
|Polska|PL|  
|Portugalia|PT|  
|Republika Filipin|PH|  
|Rosja|RU|  
|Arabia Saudyjska|SA|  
|RPA|ZA|  
|Hiszpania|ES|  
|Szwecja|SE|  
|Szwajcaria|CH|  
|Tajwan|TW|  
|Turcja|TR|  
|Zjednoczone Królestwo|GB|  
|Stany Zjednoczone Ameryki|USA|

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat punktów końcowych wyszukiwania wiadomości Bing, zobacz [odwołanie do interfejsu API wyszukiwania wiadomości w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
