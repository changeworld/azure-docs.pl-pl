---
title: Obsługa języka — interfejs API wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wizualnego Bing. Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzy dziesiątki krajów/regionów, wiele z więcej niż jednym językiem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883544"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania wizualnego Bing

Interfejs API wyszukiwania wizualnego Bing obsługuje więcej niż trzy dziesiątki krajów/regionów, wiele z więcej niż jednym językiem. Każde żądanie powinno zawierać kraj/region i język wyboru użytkownika. Wiedząc, że rynek użytkownika pomaga w usłudze Bing zwracać odpowiednie wyniki. Jeśli nie określisz kraju/regionu i języka, Bing najlepiej nadaje się do ustalenia kraju/regionu i języka użytkownika. Ze względu na to, że wyniki mogą zawierać linki do usługi Bing, wiedząc, że kraj/region i język mogą zapewnić preferowany, zlokalizowany interfejs użytkownika Bing, jeśli użytkownik kliknie linki Bing.

Aby określić kraj/region i język, ustaw `mkt` parametr zapytania (rynek) na kod z tabeli **rynków** poniżej. Rynek określa kraj/region i język. Jeśli użytkownik woli zobaczyć tekst wyświetlany w innym języku, należy ustawić `setLang` parametr zapytania na odpowiedni kod języka.

Alternatywnie możesz określić kraj/region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju/regionu należy również określić jeden lub więcej kodów języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki różnią się w zależności od kraju/regionu; są one przyznawane dla każdego kraju w tabeli rynków.



> [!NOTE]
> Obowiązują następujące ograniczenia dotyczące rynku:
>
> - Adnotacje rozpoznawania obrazów są dostępne tylko w języku angielskim.
> - Przepis, zakupy i strony — w tym szczegółowe informacje są dostępne tylko na rynku en-US.


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
