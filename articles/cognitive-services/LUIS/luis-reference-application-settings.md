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
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275904"
---
# <a name="application-settings"></a>Ustawienia aplikacji

Te ustawienia aplikacji są przechowywane w [wyeksportowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) aplikacji i [zaktualizowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) za pomocą interfejsów API REST. Zmiana ustawień wersji aplikacji resetuje stan szkolenia Twojej aplikacji nieprzeszkolonych.

|Ustawienie|Wartość domyślna|Uwagi|
|--|--|--|
|NormalizePunctuation|Prawda|Usuwa znaki interpunkcyjne.|
|NormalizeDiacritics|Prawda|Usuwa znaki diakrytyczne.|

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

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ma wartość true|
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

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ma wartość true|
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

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ma wartość true|
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

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ma wartość true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Włoski `it-it` znaki diakrytyczne

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ma wartość true|
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

#### <a name="spanish-es--diacritics"></a>Hiszpański `es-` znaki diakrytyczne

Dotyczy to również Meksykańskich kanadyjski i hiszpańskim.

|Znaki diakrytyczne ustawiony na wartość false|Znaki diakrytyczne ma wartość true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
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
