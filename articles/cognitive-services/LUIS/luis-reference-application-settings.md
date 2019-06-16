---
title: Ustawienia aplikacji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, ustawienia aplikacji dotyczące języka opis aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: c19d328c3a5f4dd6dbe14ca94809e42c5655ea72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391146"
---
# <a name="application-settings"></a>Ustawienia aplikacji

Te ustawienia aplikacji są przechowywane w [wyeksportowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikacji i [zaktualizowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) za pomocą interfejsów API REST. Zmiana ustawień wersji aplikacji resetuje stan szkolenia Twojej aplikacji nieprzeszkolonych.

|Ustawienie|Wartość domyślna|Uwagi|
|--|--|--|
|NormalizePunctuation|True|Usuwa znaki interpunkcyjne.|
|NormalizeDiacritics|True|Usuwa znaki diakrytyczne.|

## <a name="diacritics-normalization"></a>Normalizacja znaki diakrytyczne 

Włącz normalizacji wypowiedź dla znaków diakrytycznych do pliku JSON usługi LUIS aplikacji w `settings` parametru.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Następujące wypowiedzi pokazują, jak znaki diakrytyczne normalizacji ma wpływ na wypowiedzi:

|Przy użyciu znaków diakrytycznych ustawiony na wartość false|Przy użyciu znaków diakrytycznych ma wartość true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Obsługa języków w programie znaki diakrytyczne

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portugalski (Brazylia) `pt-br` znaki diakrytyczne

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ustawiony na wartość false|
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

#### <a name="dutch-nl-nl-diacritics"></a>Holenderski `nl-nl` znaki diakrytyczne

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ustawiony na wartość false|
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

#### <a name="french-fr--diacritics"></a>Francuski `fr-` znaki diakrytyczne

Obejmuje to podhodowli francuskim i Kanady.

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ustawiony na wartość false|
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

#### <a name="german-de-de-diacritics"></a>Niemiecki `de-de` znaki diakrytyczne

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ustawiony na wartość false|
|--|--|
|`ä`|`a`|
|`ö `|`o`| 
|`ü `|`u`| 

#### <a name="italian-it-it-diacritics"></a>Włoski `it-it` znaki diakrytyczne

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ustawiony na wartość false|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó `|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Hiszpański `es-` znaki diakrytyczne

Dotyczy to również Meksykańskich kanadyjski i hiszpańskim.

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ustawiony na wartość false|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó `|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Znak interpunkcyjny normalizacji

Włącz normalizacji wypowiedź znaki interpunkcyjne do pliku JSON usługi LUIS aplikacji w `settings` parametru.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Następujące wypowiedzi pokazują, jak znaki diakrytyczne ma wpływ na wypowiedzi:

|Przy użyciu znaków diakrytycznych ustawiony na wartość False|Przy użyciu znaków diakrytycznych ustawiona na wartość True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Bez znaków interpunkcyjnych

Następujące znaki interpunkcyjne zostaje usunięta wraz z `NormalizePunctuation` jest ustawiona na wartość true.

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
