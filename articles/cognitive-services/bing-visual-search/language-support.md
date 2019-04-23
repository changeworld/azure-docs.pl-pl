---
title: Obsługa języków — interfejs API wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wizualnego Bing. Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzech tuzina kraje/regiony, wiele z więcej niż jednym języku.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 511a95eaea65c1a0e67973ef56942bc05bb43f81
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002792"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Obsługa języka i regionu dla interfejsu API wyszukiwania wizualnego Bing

Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzech tuzina kraje/regiony, wiele z więcej niż jednym języku. Każde żądanie powinno obejmować kraj/region i język wybranego użytkownika. Wiedząc rynku użytkownika pomaga Bing odpowiednich wyników. Jeśli nie określisz kraj/region i język Bing sprawia, że najlepszy nakład pracy, aby określić kraj/region i język użytkownika. Ponieważ wyniki mogą zawierać łącza do usługi Bing, wiedząc, kraj/region i język może dostarczyć preferowanych Bing zlokalizowane środowisko użytkownika kliknięcie łącza Bing.

Aby określić kraj/region i język, ustaw `mkt` parametr zapytania (rynek) do kodu ze **rynków** w poniższej tabeli. Rynek Określa kraj/region i język. Jeśli użytkownik chce zobaczyć wyświetlania tekstu w innym języku, należy ustawić `setLang` parametru w kodzie odpowiedni język zapytania.

Alternatywnie można określić za pomocą kraju/regionu `cc` parametr zapytania. Jeśli określisz kraju/regionu, należy także określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależą od kraju/regionu są one podane dla każdego kraju w tabeli rynkach.



> [!NOTE]
> Obowiązują następujące ograniczenia na rynku:
>
> - Adnotacje rozpoznawania obrazów dostępnych w języku angielskim tylko.
> - Przepisu, zakupy i wgląd w tym strony są dostępne na rynku en US.


## <a name="countries"></a>Kraje

|Kraj/region|Kod|
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
|Zjednoczone Królestwo|GB|
|Stany Zjednoczone|USA|


## <a name="markets"></a>Rynki

|Kraj/region|Język|Rynek kodu|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES AR|
|Australia|Polski|EN-AU|
|Austria|Niemiecki|de-AT|
|Belgia|Holenderski|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES-CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE.|
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|
|Indie|Polski|EN-IN|
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
|Filipiny|Polski|en-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|en-ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Turcja|Turecki|tr-TR|
|Zjednoczone Królestwo|Polski|en-GB|
|Stany Zjednoczone|Polski|en-US|
|Stany Zjednoczone|Hiszpański|es-US|
