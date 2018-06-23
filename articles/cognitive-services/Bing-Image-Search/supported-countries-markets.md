---
title: Obsługiwane innych krajów i języków interfejsu API wyszukiwania obrazu Bing na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, która krajów i języki są obsługiwane przez interfejs API wyszukiwania usługi Bing obrazu.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348952"
---
# <a name="bing-image-search-countries-and-languages"></a>Wyszukiwania usługi Bing obrazu krajów i języków

Interfejs API wyszukiwania usługi Bing obrazu obsługuje więcej niż trzech dwanaście krajach, wiele z więcej niż jednym języku. Określanie kraju z zapytaniem służy głównie w celu ograniczenia wyników wyszukiwania w oparciu zainteresowań w tym kraju. Ponadto wyniki mogą zawierać łącza do usługi Bing i łącza mogą localize Bing środowisko użytkownika zgodnie z określonym kraju lub języka.

Aby określić kraju i język, ustaw `mkt` (rynku) parametru zapytania do kodu z **rynkach** w poniższej tabeli. Rynku Określa kraj i języka. Jeśli użytkownik chce zobaczyć wyświetlania tekstu w innym języku, ustaw `setLang` parametr kod odpowiedni język zapytania.

Alternatywnie można określić za pomocą kraju `cc` parametr zapytania. Jeśli określisz kraju, należy określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależy od kraju; są one podane dla każdego kraju, w tabeli rynkach.

> [!NOTE]
> Interfejs API umożliwia analizę trendów obrazy aktualnie obsługuje tylko następujące rynkach:
> - EN US (angielski, Stany Zjednoczone) 
> - EN-CA (angielski, Kanada) 
> - EN-AU (angielski, Australia) 
> - zh-CN (chińskim, Chiny)

## <a name="countries"></a>Krajach

|Kraj|Kod|
|-------|----|
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
|Hongkong|HK|
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
|Chiny|NAZWA POSPOLITA|
|Polska|PL|
|Portugalia|PT|
|Filipiny|CIĄG|
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


## <a name="markets"></a>Rynkach

|Kraj|Język|Kod rynku|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES AR|
|Australia|Polski|EN-AU|
|Austria|Niemiecki|Niemcy AT|
|Belgia|Holenderski|NL-być|
|Belgia|Francuski|FR — można|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES-CL|
|Dania|Duński|Ciemny da|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE|
|Hongkong|Chiński (tradycyjny)|zh-HK|
|Indie|Polski|EN w|
|Indonezja|Polski|EN-ID|
|Włochy|Włoski|IT-IT|
|Japonia|Japoński|ja-JP|
|Korea|Koreański|ko-KR|
|Malezja|Polski|Moje en|
|Meksyk|Hiszpański|es-MX|
|Holandia|Holenderski|NL-NL|
|Nowa Zelandia|Polski|EN NZ|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Polski|CIĄG en|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|EN-ZA-|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR CH|
|Szwajcaria|Niemiecki|Niemcy CH|
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Polski|pl pl.|
|Stany Zjednoczone|Polski|pl-PL|
|Stany Zjednoczone|Hiszpański|es-US|

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o punktach końcowych wyszukiwania usługi Bing wiadomości, zobacz [odwołania w wersji 7 interfejsu API Search obrazu wiadomości](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
