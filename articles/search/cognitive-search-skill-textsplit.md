---
title: Umiejętności poznawcze podziału tekstu
titleSuffix: Azure Cognitive Search
description: Podziel tekst na fragmenty lub strony tekstu na podstawie długości w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73479663"
---
# <a name="text-split-cognitive-skill"></a>Umiejętności poznawcze podziału tekstu

**Umiejętność Dzielenie tekstu** dzieli tekst na fragmenty tekstu. Można określić, czy tekst ma być podzielony na zdania, czy na strony o określonej długości. Ta umiejętność jest szczególnie przydatna, jeśli w innych umiejętnościach niższego szczebla obowiązują maksymalne wymagania dotyczące długości tekstu. 

> [!NOTE]
> Ta umiejętność nie jest powiązana z interfejsem API usług Cognitive Services i nie są naliczane opłaty za korzystanie z niej. Należy jednak [dołączyć zasób usług Cognitive Services,](cognitive-search-attach-cognitive-services.md)aby zastąpić opcję **Wolny** zasób, która ogranicza liczbę wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textSplitMode      | "strony" lub "zdania" | 
| maximumPageLength (Maksymalna długość strony) | Jeśli textSplitMode jest ustawiony na "strony", odnosi się to `String.Length`do maksymalnej długości strony mierzonej przez . Minimalna wartość to 100.  Jeśli textSplitMode jest ustawiona na "strony", algorytm spróbuje podzielić tekst na fragmenty, które są co najwyżej "maximumPageLength" w rozmiarze. W takim przypadku algorytm dołoży wszelkich starań, aby złamać zdanie na granicy zdania, więc rozmiar fragmentu może być nieco mniejszy niż "maximumPageLength". | 
| domyślny Kod Języka   | (opcjonalnie) Jeden z następujących kodów języków: `da, de, en, es, fi, fr, it, ko, pt`. Wartość domyślna to angielski (pl). Kilka rzeczy do rozważenia:<ul><li>Jeśli przejdziesz format languagecode-countrycode, używana jest tylko część kodu języka formatu.</li><li>Jeśli języka nie ma na poprzedniej liście, umiejętność podziału przerywa tekst w granicach znaków.</li><li>Podanie kodu języka jest przydatne, aby uniknąć cięcia wyrazu na pół dla języków innych niż białe, takich jak chiński, japoński i koreański.</li><li>Jeśli nie znasz języka (tj. musisz podzielić tekst do wprowadzenia do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), domyślnie angielski (en) powinien być wystarczający. </li></ul>  |


## <a name="skill-inputs"></a>Wprowadzanie umiejętności

| Nazwa parametru       | Opis      |
|----------------------|------------------|
| tekst  | Tekst do podzielenia na podciąg. |
| languageCode  | (Opcjonalnie) Kod języka dokumentu. Jeśli nie znasz języka (tj. musisz podzielić tekst do wprowadzenia do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), można bezpiecznie usunąć to dane wejściowe.  |

## <a name="skill-outputs"></a>Wyniki umiejętności 

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textItems ( textItems ) | Tablica podciągów, które zostały wyodrębnione. |


##  <a name="sample-definition"></a>Przykładowa definicja

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
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
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

## <a name="error-cases"></a>Przypadki błędów
Jeśli język nie jest obsługiwany, generowane jest ostrzeżenie, a tekst jest dzielony na granicach znaków.

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
