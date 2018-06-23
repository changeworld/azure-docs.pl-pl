---
title: Obsługiwane innych krajów i języków interfejsu API wyszukiwania wiadomości Bing na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, która krajów i języki są obsługiwane przez interfejs API wyszukiwania usługi Bing obrazu.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 80326d66e509b64efd5d386fe793bc9942b29ae3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347217"
---
# <a name="bing-news-search-countries-and-languages"></a>Kraje wyszukiwania wiadomości Bing i języki

Interfejs API wyszukiwania usługi Bing wiadomości obsługuje wiele krajów, wiele z więcej niż jednym języku. Określanie kraju z zapytaniem służy głównie w celu ograniczenia wyników wyszukiwania w oparciu zainteresowań w tym kraju. Ponadto wyniki mogą zawierać łącza do usługi Bing i łącza mogą localize Bing środowisko użytkownika zgodnie z określonym kraju lub języka.

Można określić za pomocą kraju `cc` parametr zapytania. Jeśli określisz kraju, należy określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależy od kraju; są one podane dla każdego kraju, w tabeli rynkach.

Alternatywnie można określić za pomocą rynku `mkt` parametr zapytania i kod z **rynkach** tabeli. Określanie jednocześnie na rynku Określa kraj i określ preferowany język. `setLang` Parametru zapytania mogą być ustawione na kod języka w takim przypadku; zazwyczaj jest to ten sam język określony przez `mkt` , chyba że użytkownik chce zobaczyć Bing w innym języku.

## <a name="supported-markets-for-news-search-endpoint"></a>Obsługiwane rynkach dla punktu końcowego wyszukiwania wiadomości

Aby uzyskać `/news/search` punktu końcowego, w poniższej tabeli wymieniono wartości kodów rynku może używanych do określania `mkt` parametr zapytania. Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  
  
Lista kraju kodów, które można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych](#countrycodes).  
  
|Kraj/region|Język|Kod rynku|  
|---------------------|--------------|-----------------| 
|Dania|Duński|Ciemny da|
|Austria|Niemiecki|Niemcy AT| 
|Szwajcaria|Niemiecki|Niemcy CH|
|Niemcy|Niemiecki|de-DE|
|Australia|Polski|EN-AU|
|Kanada|Polski|EN-CA|
|Wielka Brytania|Polski|pl pl.|
|Indonezja|Polski|EN-ID|
|Irlandia|Polski|EN-IE|
|Indie|Polski|EN w|
|Malezja|Polski|Moje en|
|Nowa Zelandia|Polski|EN NZ|
|Republika Filipin|Polski|CIĄG en|
|Singapur|Polski|EN-SG|
|Stany Zjednoczone|Polski|pl-PL|
|Polski|Ogólne|EN TT|
|Polski|Ogólne|EN XA|
|Republika Południowej Afryki|Polski|EN-ZA-|
|Argentyna|Hiszpański|ES AR|
|Chile|Hiszpański|ES-CL|
|Hiszpania|Hiszpański|es-ES|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-US| 
|Hiszpański|Ogólne|ES XL|
|Finlandia|Fiński|fi-FI|  
|Francja|Francuski|FR — można|
|Kanada|Francuski|fr-CA| 
|Belgia|Holenderski|NL-być|
|Szwajcaria|Francuski|FR CH|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT| 
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|  
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Japonia|Japoński|ja-JP|  
|Korea|Koreański|ko-KR|  
|Holandia|Holenderski|NL-NL|  
|Chińska Republika Ludowa|Chiński|zh-CN|  
|Brazylia|Portugalski|pt-BR|
|Rosja|Rosyjski|ru-RU|  
|Szwecja|Szwedzki|sv-SE|  
|Turcja|Turecki|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Obsługiwane rynkach dla punktu końcowego wiadomości
Aby uzyskać `/news` punktu końcowego, w poniższej tabeli wymieniono wartości kodów rynku może używanych do określania `mkt` parametr zapytania. Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  
  
Lista kraju kodów, które można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych](#countrycodes).  
  
|Kraj/region|Język|Kod rynku|  
|---------------------|--------------|-----------------| 
|Dania|Duński|Ciemny da|
|Niemcy|Niemiecki|de-DE|
|Australia|Polski|EN-AU|
|Wielka Brytania|Polski|pl pl.|
|Stany Zjednoczone|Polski|pl-PL|
|Polski|Ogólne|EN TT|
|Chile|Hiszpański|ES-CL|
|Meksyk|Hiszpański|es-MX|
|Stany Zjednoczone|Hiszpański|es-US| 
|Finlandia|Fiński|fi-FI|  
|Kanada|Francuski|fr-CA|
|Francja|Francuski|fr-FR|  
|Włochy|Włoski|IT-IT| 
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Obsługiwane rynkach dla punktu końcowego trendów wiadomości
Aby uzyskać `/news/trendingtopics` punktu końcowego, w poniższej tabeli wymieniono wartości kodów rynku może używanych do określania `mkt` parametr zapytania. Bing zwraca zawartość tylko tych rynkach. Lista może ulec zmianie.  
  
Lista kraju kodów, które można określić w `cc` parametr zapytania, zobacz [numerów kierunkowych](#countrycodes).  
  
|Kraj/region|Język|Kod rynku|  
|---------------------|--------------|-----------------| 
|Niemcy|Niemiecki|de-DE|
|Australia|Polski|EN-AU|
|Wielka Brytania|Polski|pl pl.|
|Stany Zjednoczone|Polski|pl-PL|
|Kanada|Polski|EN-CA|
|Indie|Polski|EN w|
|Francja|Francuski|fr-FR|
|Kanada|Francuski|fr-CA|
|Brazylia|Portugalski|pt-BR|
|Chińska Republika Ludowa|Chiński|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Kody krajów  

Poniżej przedstawiono kodów kraju, które mogą być określone w `cc` parametr zapytania. Lista może ulec zmianie.  
  
|Kraj/region|Kod kraju|  
|---------------------|------------------|  
|Argentyna|AR|  
|Australia|AU|  
|Austria|AT|  
|Belgia|MOŻNA|  
|Brazylia|BRAZYLIA|  
|Kanada|Urząd certyfikacji|  
|Chile|CL|  
|Dania|K|  
|Finlandia|FI|  
|Francja|PW|  
|Niemcy|DE|  
|SRA Hongkong|HK|  
|Indie|W|  
|Indonezja|ID|  
|Włochy|IT|  
|Japonia|JP|  
|Korea|KR|  
|Malezja|MOJE|  
|Meksyk|MX|  
|Holandia|NL|  
|Nowa Zelandia|NZ|  
|Norwegia|NIE|  
|Chińska Republika Ludowa|NAZWA POSPOLITA|  
|Polska|PL|  
|Portugalia|PT|  
|Republika Filipin|CIĄG|  
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

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o punktach końcowych wyszukiwania usługi Bing wiadomości, zobacz [odwołania w wersji 7 interfejsu API wyszukiwania wiadomości](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).