---
title: Obsługa języka — interfejs API wyszukiwania wizualnego usługi Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów obsługiwanych przez interfejs API wyszukiwania wizualnego Bing. Interfejs API wyszukiwania wizualnego usługi Bing obsługuje ponad trzy tuziny krajów/regionów, wiele z więcej niż jednym językiem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883544"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Obsługa języka i regionu interfejsu API wyszukiwania wizualnego usługi Bing

Interfejs API wyszukiwania wizualnego usługi Bing obsługuje ponad trzy tuziny krajów/regionów, wiele z więcej niż jednym językiem. Każde żądanie powinno zawierać kraj/region użytkownika i wybrany język. Znajomość rynku użytkownika pomaga Bing zwracać odpowiednie wyniki. Jeśli nie określisz kraju/regionu i języka, Bing dołoży wszelkich starań, aby określić kraj/region i język użytkownika. Ponieważ wyniki mogą zawierać łącza do usługi Bing, znajomość kraju/regionu i języka może zapewnić preferowane zlokalizowane środowisko użytkownika usługi Bing, jeśli użytkownik kliknie łącza Bing.

Aby określić kraj/region i `mkt` język, ustaw parametr zapytania (rynkowego) na kod z poniższej **tabeli Rynki.** Rynek określa zarówno kraj/region, jak i język. Jeśli użytkownik woli wyświetlać tekst wyświetlania w `setLang` innym języku, ustaw parametr zapytania na odpowiedni kod języka.

Alternatywnie można określić kraj/region `cc` przy użyciu parametru zapytania. Jeśli określisz kraj/region, należy również określić jeden `Accept-Language` lub więcej kodów języków za pomocą nagłówka HTTP. Obsługiwane języki różnią się w zależności od kraju/regionu; dla każdego kraju w tabeli Rynki.



> [!NOTE]
> Obowiązują następujące ograniczenia rynkowe:
>
> - Adnotacje rozpoznawania obrazów są dostępne tylko w języku angielskim.
> - Przepisy, zakupy i statystyki dołączane do stron są dostępne tylko na rynku w USA.


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
