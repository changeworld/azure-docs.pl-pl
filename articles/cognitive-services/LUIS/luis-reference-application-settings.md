---
title: Ustawienia aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Informacje na temat ustawień aplikacji dotyczących aplikacji językowych.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7dec738fca6991cbcbd822c192b96bf6b1cc6d87
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563513"
---
# <a name="application-settings"></a>Ustawienia aplikacji

Te ustawienia aplikacji są przechowywane w wyeksportowanej aplikacji i [aktualizowane](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) przy użyciu interfejsów API REST. [](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) Zmiana ustawień wersji aplikacji resetuje stan szkolenia aplikacji na nieszkolenie.

|Ustawienie|Wartość domyślna|Uwagi|
|--|--|--|
|NormalizePunctuation|Prawda|Usuwa znaki interpunkcyjne.|
|NormalizeDiacritics|Prawda|Usuwa znaki diakrytyczne.|

## <a name="diacritics-normalization"></a>Normalizacja znaków diakrytycznych 

Włącz normalizowanie wypowiedź dla znaków diakrytycznych do pliku aplikacji Luis JSON w `settings` parametrze.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Poniższy wyrażenia długości pokazuje, jak normalizacja znaków diakrytycznych ma wpływ na wyrażenia długości:

|Z atrybutami diakrytycznymi ustawionymi na wartość false|Z atrybutami diakrytycznymi ustawionymi na wartość true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Obsługa języka dla znaków diakrytycznych

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazylijski portugalski `pt-br` (znaki diakrytyczne)

|Znaki diakrytyczne ustawione na wartość false|Znaki diakrytyczne ustawione na wartość true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Znaki `nl-nl` diakrytyczne holenderskich

|Znaki diakrytyczne ustawione na wartość false|Znaki diakrytyczne ustawione na wartość true|
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

Obejmuje to zarówno kulturę francuską, jak i kanadyjską.

|Znaki diakrytyczne ustawione na wartość false|Znaki diakrytyczne ustawione na wartość true|
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

#### <a name="german-de-de-diacritics"></a>Znaki `de-de` diakrytyczne niemieckich

|Znaki diakrytyczne ustawione na wartość false|Znaki diakrytyczne ustawione na wartość true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Włoskie `it-it` znaki diakrytyczne

|Znaki diakrytyczne ustawione na wartość false|Znaki diakrytyczne ustawione na wartość true|
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

Dotyczy to zarówno hiszpańskiej, jak i kanadyjskiej meksykańskiej.

|Znaki diakrytyczne ustawione na wartość false|Znaki diakrytyczne ustawione na wartość true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalizacja interpunkcji

Włącz normalizację wypowiedź dla interpunkcji do pliku aplikacji Luis JSON w `settings` parametrze.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Następujące wyrażenia długości pokazują, jak znaki diakrytyczne mają wpływ na wyrażenia długości:

|Z atrybutami diakrytycznymi ustawionymi na wartość false|Z atrybutami diakrytycznymi ustawionymi na wartość true|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Usunięto interpunkcję

Następujące znaki interpunkcyjne są usuwane z `NormalizePunctuation` wartością ustawioną na true.

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
