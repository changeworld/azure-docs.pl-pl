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
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881918"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania obrazów Bing

Interfejs API wyszukiwania obrazów Bing obsługuje więcej niż trzy dziesiątki krajów/regionów, wiele z więcej niż jednym językiem. Określenie kraju/regionu z zapytaniem służy głównie do uściślenia wyników wyszukiwania w oparciu o zainteresowania w tym kraju/regionie. Ponadto wyniki mogą zawierać linki do usługi Bing, a te linki mogą lokalizować środowisko użytkownika Bing zgodnie z określonym krajem lub regionami lub językiem.

Aby określić kraj/region i język, ustaw `mkt` parametr zapytania (rynek) na kod z tabeli **rynków** poniżej. Rynek określa kraj/region i język. Jeśli użytkownik woli zobaczyć tekst wyświetlany w innym języku, należy ustawić `setLang` parametr zapytania na odpowiedni kod języka.

Alternatywnie możesz określić kraj/region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki różnią się w zależności od kraju/regionu; są one przyznawane dla każdego kraju/regionu w tabeli rynków.

> [!NOTE]
> Interfejs API obrazów trendów obsługuje obecnie tylko następujące rynki:
> - pl-US (angielski, Stany Zjednoczone)
> - EN-CA (angielski, Kanada)
> - en-AU (angielski, Australia)
> - zh-CN (chiński, Chiny)

## <a name="countriesregions"></a>Kraje/regiony

|Country/region|Kod|
|-------|----|
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
|Indonezja|id|
|Włochy|it|
|Japonia|JP|
|Korea Południowa|KR|
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
|RPA|ZA|
|Hiszpania|ES|
|Szwecja|SE|
|Szwajcaria|CH|
|Tajwan|TW|
|Turcja|TR|
|Zjednoczone Królestwo|GB|
|Stany Zjednoczone|USA|


## <a name="markets"></a>Wprowadza

|Country/region|Język|Kod rynkowy|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES-AR|
|Australia|Angielski|EN-AU|
|Austria|niemiecki|de-AT|
|Belgia|Holenderski|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Angielski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES — CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|niemiecki|de-DE.|
|SRA Hongkong|Chiński tradycyjny|zh-HK|
|Indie|Angielski|EN-IN|
|Indonezja|Angielski|pl-ID|
|Włochy|Włoski|IT-IT|
|Japonia|Japoński|ja-JP|
|Korea Południowa|Koreański|ko-KR|
|Malezja|Angielski|pl — MY|
|Meksyk|Hiszpański|es-MX|
|Holandia|Holenderski|NL-NL|
|Nowa Zelandia|Angielski|EN NZ|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Angielski|EN-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|RPA|Angielski|pl-za|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|niemiecki|de-CH|
|Tajwan|Chiński tradycyjny|zh-TW|
|Turcja|Turecki|tr-TR|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone|Angielski|en-US|
|Stany Zjednoczone|Hiszpański|es-US|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o wyszukiwanie wiadomości Bing punktach końcowych, zobacz [News wyszukiwanie obrazów API wersji 7 Reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
