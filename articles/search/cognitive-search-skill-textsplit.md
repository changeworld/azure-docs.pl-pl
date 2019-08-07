---
title: Umiejętność wyszukiwania poznawczego w postaci tekstu — Azure Search
description: Przerwij tekst w fragmentach lub stronach tekstu na podstawie długości w potoku wzbogacenia Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 8fb7ff51507212dfb162c09fcee469d6f154f3c3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840888"
---
#   <a name="text-split-cognitive-skill"></a>Umiejętność rozdzielania tekstu

Umiejętności **podziału tekstu** dzieli tekst na fragmenty tekstu. Można określić, czy tekst ma być podzielony na zdania, czy na strony o określonej długości. Ta umiejętność jest szczególnie przydatna, jeśli istnieją maksymalne wymagania dotyczące długości tekstu w innych umiejętnościach. 

> [!NOTE]
> Ta umiejętność nie jest powiązana z interfejsem API Cognitive Services i nie jest naliczana opłata za korzystanie z niego. Mimo to należy [dołączyć zasób Cognitive Services](cognitive-search-attach-cognitive-services.md), aby zastąpić opcję bezpłatnych zasobów , która ogranicza liczbę codziennych wzbogacań dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametry umiejętności

W parametrach jest rozróżniana wielkość liter.

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textSplitMode      | "Strony" lub "zdania" | 
| maximumPageLength | Jeśli textsplitmode ma wartość "Pages", odnosi się do maksymalnej długości strony, która jest `String.Length`mierzona przez. Wartość minimalna to 100.  Jeśli element textsplitmode ma wartość "Pages", algorytm podejmie próbę podzielenia tekstu na fragmenty o rozmiarze co najwyżej "maximumPageLength". W takim przypadku algorytm będzie optymalnie dzielić zdanie na granicy zdania, więc rozmiar fragmentu może być nieco mniejszy niż "maximumPageLength". | 
| defaultLanguageCode   | obowiązkowe Jeden z następujących kodów języka: `da, de, en, es, fi, fr, it, ko, pt`. Wartość domyślna to angielski (EN). Kilka kwestii, które należy wziąć pod uwagę:<ul><li>Jeśli przekażesz format languagecode-CountryCode, zostanie użyta tylko część languagecode formatu.</li><li>Jeśli język nie znajduje się na poprzedniej liście, podzielona umiejętność zrywa tekst w granicach znaku.</li><li>Podawanie kodu języka jest przydatne, aby uniknąć wycinania wyrazu w połowie dla języków bez miejsca, takich jak chiński, japoński i koreański.</li></ul>  |


## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa parametru       | Opis      |
|----------------------|------------------|
| text  | Tekst, który ma zostać podzielony na podciąg. |
| languageCode  | Obowiązkowe Kod języka dla dokumentu.  |

## <a name="skill-outputs"></a>Wyniki umiejętności 

| Nazwa parametru     | Opis |
|--------------------|-------------|
| textitems | Tablica podciągów, które zostały wyodrębnione. |


##  <a name="sample-definition"></a>Definicja Przykładowa

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
Jeśli język nie jest obsługiwany, generowane jest ostrzeżenie, a tekst jest dzielony w granicach znaków.

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
