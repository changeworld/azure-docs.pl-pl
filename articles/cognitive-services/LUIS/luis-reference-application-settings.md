---
title: Ustawienia aplikacji — usługa LUIS
description: Ustawienia aplikacji dla aplikacji języka usługi Azure Cognitive Services są przechowywane w aplikacji i portalu.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382199"
---
# <a name="application-settings"></a>Ustawienia aplikacji

Te ustawienia aplikacji są przechowywane w [wyeksportowanej](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikacji i [aktualizowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) za pomocą interfejsów API REST. Zmiana ustawień wersji aplikacji powoduje zresetowanie stanu szkolenia aplikacji do nieprzeszkolonego.

Poznaj [pojęcia](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) znaków diakrutycznych i znaków interpunkcyjnych.

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

## <a name="next-steps"></a>Następne kroki

* Poznaj [pojęcia](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) znaków diakrutycznych i znaków interpunkcyjnych.
