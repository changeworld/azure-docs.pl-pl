---
title: Obsługiwane krajach oraz języki Visual API wyszukiwania usługi Bing | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Dowiedz się, która krajów i języków obsługiwanych przez Visual API wyszukiwania usługi Bing.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348712"
---
# <a name="bing-visual-search-countries-and-languages"></a>Kraje Visual wyszukiwania usługi Bing i języki

Visual API wyszukiwania usługi Bing obsługuje więcej niż trzech dwanaście krajach, wiele z więcej niż jednym języku. Kraju i języka użytkownika, należy uwzględnić każdego żądania. Znajomość rynku użytkownika pomaga Bing zwracają odpowiednie wartości. Jeśli nie określisz kraju i język Bing sprawia, że starań, aby określić język i kraj użytkownika. Wyniki mogą zawierać łącza do usługi Bing, kraju i języka może dostarczyć preferowanych Bing zlokalizowanych środowisko użytkownika kliknięcie łącza Bing.

Aby określić kraju i język, ustaw `mkt` (rynku) parametru zapytania do kodu z **rynkach** w poniższej tabeli. Rynku Określa kraj i języka. Jeśli użytkownik chce zobaczyć wyświetlania tekstu w innym języku, ustaw `setLang` parametr kod odpowiedni język zapytania.

Alternatywnie można określić za pomocą kraju `cc` parametr zapytania. Jeśli określisz kraju, należy określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka HTTP. Obsługiwane języki zależy od kraju; są one podane dla każdego kraju, w tabeli rynkach.



> [!NOTE]
> Stosuje się następujące ograniczenia rynku:
> 
> - Adnotacje rozpoznawania obrazu są dostępne w języku angielskim tylko. 
> - Przepisu, zakupów i stron, tym szczegółowe informacje są dostępne na rynku en US. 


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
