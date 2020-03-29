---
title: Obsługa języka — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, które kraje/regiony i języki są obsługiwane przez interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881918"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Obsługa języka i regionu interfejsu API wyszukiwania obrazów usługi Bing

Interfejs API wyszukiwania obrazów Bing obsługuje ponad trzy tuziny krajów/regionów, wiele z więcej niż jednym językiem. Określanie kraju/regionu za pomocą kwerendy służy przede wszystkim do zawężania wyników wyszukiwania na podstawie zainteresowań w tym kraju/regionie. Ponadto wyniki mogą zawierać łącza do usługi Bing, a te łącza mogą lokalizować środowisko użytkownika usługi Bing zgodnie z określonym krajem/regionem lub językiem.

Aby określić kraj/region i `mkt` język, ustaw parametr zapytania (rynkowego) na kod z poniższej **tabeli Rynki.** Rynek określa zarówno kraj/region, jak i język. Jeśli użytkownik woli wyświetlać tekst wyświetlania w `setLang` innym języku, ustaw parametr zapytania na odpowiedni kod języka.

Alternatywnie można określić kraj/region `cc` przy użyciu parametru zapytania. Jeśli określisz kraj/region, należy również określić jeden `Accept-Language` lub więcej kodów języków za pomocą nagłówka HTTP. Obsługiwane języki różnią się w zależności od kraju/regionu; dla każdego kraju/regionu w tabeli Rynki.

> [!NOTE]
> Interfejs API obrazów trendów obsługuje obecnie tylko następujące rynki:
> - pl-US (angielski, Stany Zjednoczone)
> - en-CA (angielski, Kanada)
> - en-AU (angielski, Australia)
> - zh-CN (chiński, Chiny)

## <a name="countriesregions"></a>Kraje/regiony

|Kraj/region|Code|
|-------|----|
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
|Chiny|CN|
|Polska|PL|
|Portugalia|PT|
|Filipiny|PH|
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


## <a name="markets"></a>Rynków

|Kraj/region|Język|Kodeks rynku|
|-------|--------|-----------|
|Argentyna|Hiszpański|es-AR|
|Australia|Polski|pl-UA|
|Austria|Niemiecki|de-AT|
|Belgia|Niderlandzki|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|pl-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|es-CL|
|Dania|duński|da-DK|
|Finlandia|fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE|
|SRA Hongkong|Chiński tradycyjny|zh-HK|
|Indie|Polski|pl-IN|
|Indonezja|Polski|en-ID|
|Włochy|Włoski|it-IT|
|Japonia|Japoński|ja-JP|
|Korea|Koreański|ko-KR|
|Malezja|Polski|pl-MY|
|Meksyk|Hiszpański|es-MX|
|Holandia|Niderlandzki|nl-NL|
|Nowa Zelandia|Polski|pl-NZ|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Polski|pl-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|pl-ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|szwedzki|sv-SE|
|Szwajcaria|Francuski|fr-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński tradycyjny|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Stany Zjednoczone|Hiszpański|es-USA|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat punktów końcowych wyszukiwania wiadomości Bing, zobacz [Odwołanie do interfejsu API wyszukiwania obrazów wiadomości w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
