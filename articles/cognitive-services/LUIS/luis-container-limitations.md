---
title: Ograniczenia kontenera — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Obsługiwane języki kontenerów usługi LUIS.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507859"
---
# <a name="language-understanding-luis-container-limitations"></a>Ograniczenia kontenera rozumienia języka (LUIS)

Kontenery usługi LUIS mają kilka istotnych ograniczeń. Od nieobsługiwał zależności do podzbioru obsługiwanych języków, w tym artykule opisano te ograniczenia.

## <a name="supported-dependencies-for-latest-container"></a>Obsługiwane zależności dla `latest` kontenera

Najnowszy kontener usługi LUIS, wydany w [//build/ 2019,](https://news.microsoft.com/build2019/)będzie obsługiwał:

* [Nowe wstępnie utworzone domeny:](luis-reference-prebuilt-domains.md)te domeny skoncentrowane na przedsiębiorstwie obejmują jednostki, przykładowe wypowiedzi i wzorce. Rozszerz te domeny na własny użytek.

## <a name="unsupported-dependencies-for-latest-container"></a>Nieobsługiwały zależności `latest` kontenera

Aby [wyeksportować do kontenera,](luis-container-howto.md#export-packaged-app-from-luis)należy usunąć nieobsługiwał zależności z aplikacji usługi LUIS. Podczas próby wyeksportowania do kontenera, portal usługi LUIS raportuje te nieobsługiwały funkcje, które należy usunąć.

Aplikacji usługi LUIS można użyć, jeśli **nie zawiera** ona żadnej z następujących zależności:

Nieobsługiwały konfiguracje aplikacji|Szczegóły|
|--|--|
|Nieobsługiwały kultury kontenerów| holenderski`nl-NL`( )<br>Japoński`ja-JP`( )<br>Niemiecki jest obsługiwany tylko z [tokenizatorem 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Nieobsługiwały się jednostki dla wszystkich kultur|Wstępnie skompilowana encja [KeyPhrase](luis-reference-prebuilt-keyphrase.md) dla wszystkich kultur|
|Nieobsługiwały się jednostkami kultury języka angielskiego (`en-US`)|Wstępnie utworzone jednostki [w lokalizacji geograficznejV2](luis-reference-prebuilt-geographyV2.md)|
|Gruntowanie mowy|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Analiza tonacji|Zależności zewnętrzne nie są obsługiwane w kontenerze.|
|Sprawdzanie pisowni Bing|Zależności zewnętrzne nie są obsługiwane w kontenerze.|

## <a name="languages-supported"></a>Obsługiwane języki

Kontenery usługi LUIS obsługują podzbiór [języków obsługiwanych](luis-language-support.md#languages-supported) przez właściwą usługę LUIS. Kontenery usługi LUIS są w stanie zrozumieć wypowiedzi w następujących językach:

| Język | Ustawienia regionalne | Wstępnie skompilowana domena | Wstępnie skompilowana jednostka | Rekomendacje listy fraz | **[Analiza tekstu](../text-analytics/language-support.md)<br>(Sentyment i<br>Słowa kluczowe)|
|--|--|:--:|:--:|:--:|:--:|
| amerykański angielski | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francuski (Francja) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| francuski (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Niemiecki |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Włoski |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreański |`ko-KR` | ✔️ | ❌ | ❌ | *Tylko fraza kluczowa* |
| portugalski (Brazylia) |`pt-BR` | ✔️ | ✔️ | ✔️ | nie wszystkie podkultury |
| hiszpański (Hiszpania) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Hiszpański (Meksyk)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turecki | `tr-TR` |✔️| ❌ | ❌ | *Tylko sentyment* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]