---
title: Obsługiwane innych krajów i języków interfejsu API wyszukiwania niestandardowego Bing na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, która krajów i języki są obsługiwane przez interfejs API wyszukiwania usługi Bing niestandardowe.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/19/2017
ms.author: v-gedod
ms.openlocfilehash: 7ff309f9b789662c4ebd791dffaa2bc2e440763e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347877"
---
# <a name="bing-custom-search-countries-and-languages"></a>Wyszukiwania usługi Bing niestandardowe krajów i języków

Interfejsu API wyszukiwania usługi Bing niestandardowe obsługuje więcej niż trzech dwanaście krajach, wiele z więcej niż jednym języku. 

Chociaż jest to opcjonalne, należy określić żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Listę parametrów opcjonalnych zapytania, zobacz [parametrów zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Można określić za pomocą kraju `cc` parametr zapytania. Jeśli określisz kraju, należy określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka. Obsługiwane języki zależy od kraju; są one podane dla każdego kraju w **rynkach** tabeli.

`Accept-Language` Nagłówka i `setLang` parametru zapytania wykluczają się wzajemnie — nie należy określać zarówno. Aby uzyskać więcej informacji, zobacz [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

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
|Norwegia|Norweski|no-NO|
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
