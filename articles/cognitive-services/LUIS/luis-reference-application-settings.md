---
title: Ustawienia aplikacji — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Ustawienia aplikacji dla aplikacji języka usługi Azure Cognitive Services są przechowywane w aplikacji i portalu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270803"
---
# <a name="application-settings"></a>Ustawienia aplikacji

Te ustawienia aplikacji są przechowywane w [wyeksportowanej](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikacji i [aktualizowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) za pomocą interfejsów API REST. Zmiana ustawień wersji aplikacji powoduje zresetowanie stanu szkolenia aplikacji do nieprzeszkolonego.

|Ustawienie|Wartość domyślna|Uwagi|
|--|--|--|
|NormalizacjaPuncjacja|True|Usuwa znaki interpunkcyjne.|
|NormalizeDiakryty|True|Usuwa znaki diakrytyczne.|

## <a name="diacritics-normalization"></a>Normalizacja znaków diakrutycznych

Włącz normalizację wypowiedź dla znaków diakretycznych do pliku `settings` aplikacji USŁUGI LUIS JSON w parametrze.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Następujące wypowiedzi pokazują, jak normalizacja znaków diakrmtycznych wpływa na wypowiedzi:

|Ze znakami diakrytycznymi ustawionymi na fałszywe|Ze znakami diakrytycznymi ustawionymi na true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Obsługa języków dla znaków diakrutycznych

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazylijskie `pt-br` portugalskie znaki diakrytyczne

|Znaki diakrytyczne ustawione na fałszywe|Znaki diakrytyczne ustawione na prawdziwe|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Holenderskie `nl-nl` znaki diakrytyczne

|Znaki diakrytyczne ustawione na fałszywe|Znaki diakrytyczne ustawione na prawdziwe|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Francuskie `fr-` znaki diakrytyczne

Dotyczy to zarówno francuskich, jak i kanadyjskich subkultur.

|Znaki diakrytyczne ustawione na fałszywe|Znaki diakrytyczne ustawione na prawdziwe|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Niemieckie `de-de` znaki diakrytyczne

|Znaki diakrytyczne ustawione na fałszywe|Znaki diakrytyczne ustawione na prawdziwe|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Włoskie `it-it` znaki diakrytyczne

|Znaki diakrytyczne ustawione na fałszywe|Znaki diakrytyczne ustawione na prawdziwe|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Hiszpańskie `es-` znaki diakrytyczne

Dotyczy to zarówno hiszpańskiego, jak i kanadyjskiego meksyku.

|Znaki diakrytyczne ustawione na fałszywe|Znaki diakrytyczne ustawione na prawdziwe|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizacja interpunkcyjna

Włącz normalizację wypowiedź dla znaków interpunkcyjnych do `settings` pliku aplikacji usługi LUIS JSON w parametrze.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Następujące wypowiedzi pokazują, jak znaki interpunkcyjne wpływają na wypowiedzi:

|Z interpuncją ustawioną na False|Z interpunkcji ustawioną na True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Znaki interpunkcyjne usunięte

Poniższa interpunkcja jest `NormalizePunctuation` usuwana za pomocą jest ustawiona na true.

|Znaki interpunkcyjne|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
