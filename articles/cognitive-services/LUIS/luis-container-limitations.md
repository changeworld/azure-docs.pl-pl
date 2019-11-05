---
title: Ograniczenia kontenera — LUIS
titleSuffix: Azure Cognitive Services
description: Obsługiwane języki kontenera LUIS.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507859"
---
# <a name="language-understanding-luis-container-limitations"></a>Ograniczenia dotyczące kontenerów Language Understanding (LUIS)

Kontenery LUIS mają kilka istotnych ograniczeń. W przypadku nieobsługiwanych zależności, w tym artykule szczegółowo opisano te ograniczenia.

## <a name="supported-dependencies-for-latest-container"></a>Obsługiwane zależności dla kontenera `latest`

Najnowszy kontener LUIS, wydawany w [//build/2019](https://news.microsoft.com/build2019/), będzie obsługiwał:

* [Nowe prebudowane domeny](luis-reference-prebuilt-domains.md): te domeny ukierunkowane na przedsiębiorstwa obejmują jednostki, przykładowe wyrażenia długości i wzorce. Rozwiń te domeny do własnych potrzeb.

## <a name="unsupported-dependencies-for-latest-container"></a>Nieobsługiwane zależności dla kontenera `latest`

Aby [wyeksportować kontener](luis-container-howto.md#export-packaged-app-from-luis), należy usunąć nieobsługiwane zależności z aplikacji Luis. Podczas próby eksportowania dla kontenera Portal LUIS zgłasza te Nieobsługiwane funkcje, które należy usunąć.

Aplikacji LUIS można użyć, jeśli **nie zawiera** ona żadnych z następujących zależności:

Nieobsługiwane konfiguracje aplikacji|Szczegóły|
|--|--|
|Nieobsługiwane kultury kontenerów| Holenderski (`nl-NL`)<br>Japoński (`ja-JP`)<br>Język niemiecki jest obsługiwany tylko w przypadku [1.0.2 tokenizatora](luis-language-support.md#custom-tokenizer-versions).|
|Nieobsługiwane jednostki dla wszystkich kultur|Wstępnie utworzona jednostka [KeyPhrase](luis-reference-prebuilt-keyphrase.md) dla wszystkich kultur|
|Nieobsługiwane jednostki dla kultury angielskiej (`en-US`)|Wstępnie skompilowane jednostki [GeographyV2](luis-reference-prebuilt-geographyV2.md)|
|Napełnianiu mowy|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Analiza tonacji|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Sprawdzanie pisowni Bing|Zależności zewnętrzne nie są obsługiwane w kontenerze.|

## <a name="languages-supported"></a>Obsługiwane języki

Kontenery LUIS obsługują podzbiór [języków obsługiwanych](luis-language-support.md#languages-supported) przez Luis. Kontenery LUIS mogą zrozumieć wyrażenia długości w następujących językach:

| Język | Ustawienia regionalne | Preskompilowana domena | Wstępnie utworzona jednostka | Zalecenia dotyczące listy fraz | [Analiza tekstu](../text-analytics/language-support.md) **<br>(Tonacji i<br>Służąc|
|--|--|:--:|:--:|:--:|:--:|
| Angielski (amerykański) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[chiński](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francuski (Francja) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francuski (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Niemiecki |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Włoski |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreański |`ko-KR` | ✔️ | ❌ | ❌ | Tylko *fraza klucza* |
| Portugalski (Brazylia) |`pt-BR` | ✔️ | ✔️ | ✔️ | nie wszystkie hodowle podrzędne |
| Hiszpański (Hiszpania) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Hiszpański (Meksyk)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turecki | `tr-TR` |✔️| ❌ | ❌ | Tylko *tonacji* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]