---
title: Tekst podzielić umiejętności kognitywnych wyszukiwania (Azure Search) | Dokumentacja firmy Microsoft
description: Podziel tekst na stronach tekstu, w oparciu o długości w potoku wzbogacenia usługi Azure Search lub fragmentów.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791028"
---
#   <a name="text-split-cognitive-skill"></a>Podziel umiejętności kognitywnych tekstu

**Podziału tekstu** umiejętności dzieli tekst do fragmentów tekstu. Można określić, czy chcesz przerwać tekst do zdań lub stron o określonej długości. Ta umiejętności jest szczególnie przydatne w przypadku tekstu wymagania dotyczące długości w innych umiejętności w dół. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry kwalifikacji

Parametry jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textSplitMode      | "Stron" lub "zdania" | 
| maximumPageLength | Jeśli textSplitMode "stron" odnosi się do długość maksymalna strony mierzony przez `String.Length`. Minimalna wartość to 100. | 
| defaultLanguageCode   | (opcjonalnie) Jeden z następujących kodów języków: `da, de, en, es, fi, fr, it, ko, pt`. Domyślnie jest język angielski (en). Kilka rzeczy do uwzględnienia:<ul><li>W przypadku przekazania formatu atrybutu languagecode countrycode, tylko część atrybutu languagecode format jest używany.</li><li>Jeśli język nie jest na poprzedniej liście, umiejętności podziału dzieli tekst na granicach znaków.</li><li>Dostarczanie kod języka jest przydatne w celu uniknięcia Wycinanie wyraz w połowie dla języków innych niż miejsce, takich jak chińskich, japońskich i koreańskich.</li></ul>  |


## <a name="skill-inputs"></a>Dane wejściowe umiejętności

| Nazwa parametru       | Opis      |
|----------------------|------------------|
| Tekst  | Tekst, aby podzielić na podciąg. |
| atrybutu languageCode  | (Opcjonalnie) Kod języka dokumentu.  |

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności 

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textItems | Tablica podciągów, które zostały wyodrębnione. |


##  <a name="sample-definition"></a>Przykład definicji

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>W przypadku wystąpienia błędów
Jeśli język nie jest obsługiwany, generowania ostrzeżeń i tekst jest dzielony na granicach znaków.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
