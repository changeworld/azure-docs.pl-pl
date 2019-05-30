---
title: Umiejętności warunkowego wyszukiwanie kognitywne (Azure Search) | Dokumentacja firmy Microsoft
description: Warunkowe umiejętności umożliwia filtrowanie, tworzenie wartości domyślne i scalanie wartości.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791516"
---
#   <a name="conditional-skill"></a>Warunkowe umiejętności

*Warunkowego umiejętności* umożliwia obsługę scenariuszy usługi Azure Search, które wymagają operacja logiczna do ustalenia danych, które można przypisać do pliku wyjściowego. Te scenariusze obejmują filtrowania, przypisywanie wartości domyślne i scalanie danych na podstawie warunku.

Poniższym pseudokodzie pokazuje, co wykonuje warunkowego umiejętności:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> To umiejętności nie jest powiązany z interfejsu API usługi Azure Cognitive Services, a nie są naliczane dotyczące korzystania z niego. Należy jednak nadal [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md) do przesłonięcia opcję zasobu "Bezpłatny", która ogranicza do niewielkiej liczby wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Oceniono pola

To umiejętności jest specjalne, ponieważ jego danych wejściowych są szacowane pola.

Prawidłowe wartości wyrażenia są następujące elementy:

-   Ścieżki adnotacji (muszą być rozdzielone ścieżek w wyrażeniach "$(" and ")")
 <br/>
    Przykłady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literały (ciągi, liczby, wartość true, false, wartość null) <br/>
    Przykłady:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Wyrażenia, które używają operatorów porównania (==,! =, > =, >, < =, <) <br/>
    Przykłady:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Wyrażenia, które używają operatorów logicznych (& &, ||,!, ^) <br/>
    Przykłady:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Wyrażeń zawierających operatory numeryczne (+, -, \*, /, %) <br/>
    Przykłady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Ponieważ umiejętności warunkowego umożliwia ocenę, można użyć jej w scenariuszach drobnych transformacji. Na przykład zobacz [definicji umiejętności 4](#transformation-example).

## <a name="skill-inputs"></a>Dane wejściowe umiejętności
W danych wejściowych jest rozróżniana wielkość liter.

| Dane wejściowe   | Opis |
|-------------|-------------|
| condition   | Jest to wejściowy [obliczane pole](#evaluated-fields) reprezentujący warunek do oceny. Ten warunek powinna być wyliczana jako wartość logiczna (*true* lub *false*).   <br/>  Przykłady: <br/> "= true" <br/> "= $(/document/language)"fr"==" <br/> "= $(/ stron dokumentu / /\*/language) == $(/ dokumentu/expectedLanguage)" <br/> |
| whenTrue    | To dane wejściowe są [obliczane pole](#evaluated-fields) reprezentujący wartość zwracana, jeśli warunek jest oceniany w celu *true*. Stałe ciągów ma zostać zwrócony w znaki pojedynczego cudzysłowu ("i"). <br/>Przykładowe wartości: <br/> "="Umowy""<br/>"= $(/ dokumentu/contractType)" <br/> "= $(/dokumentu/podmioty/\*)" <br/> |
| whenFalse   | To dane wejściowe są [obliczane pole](#evaluated-fields) reprezentujący wartość zwracana, jeśli warunek jest oceniany w celu *false*. <br/>Przykładowe wartości: <br/> "="Umowy""<br/>"= $(/ dokumentu/contractType)" <br/> "= $(/dokumentu/podmioty/\*)" <br/>

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności
Brak jednego danych wyjściowych, który nosi nazwę "Wyjście". Zwraca wartość *whenFalse* Jeśli warunek jest fałszywy lub *whenTrue* Jeśli warunek jest prawdziwy.

## <a name="examples"></a>Przykłady

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Przykładowa definicja umiejętności 1: Filtrowanie dokumentów, aby zwrócić tylko dokumenty francuska

Następujące dane wyjściowe zwraca tablicę zdań ("/ dokumentu/frenchSentences"), jeśli język dokumentu jest francuski. Jeśli język nie jest francuski, wartość jest równa *null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Jeśli "/ dokumentu/frenchSentences" jest używana jako *kontekstu* innego umiejętności umiejętności działa tylko wtedy, jeśli "/ dokumentu/frenchSentences" nie jest ustawiony na *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Przykładowa definicja umiejętności 2: Ustawianie wartości domyślnej dla wartości, która nie istnieje

Następujące dane wyjściowe tworzy adnotacji ("/ dokumentu/languageWithDefault"), który jest ustawiony język dokumentu lub "es", jeśli język nie jest ustawiona.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Przykładowa definicja umiejętności 3: Scalanie wartości z dwóch pól w jednym

W tym przykładzie ma pewne zdania *frenchSentiment* właściwości. Zawsze, gdy *frenchSentiment* właściwość ma wartość null, chcemy do użycia *englishSentiment* wartości. Firma Microsoft Przypisz dane wyjściowe do elementu członkowskiego, która jest wywoływana *tonacji* ("/ dokumentu/tonacji / * / tonacji").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Przykład transformacji
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Przykładowa definicja umiejętności 4: Przekształcanie danych na jedno pole

W tym przykładzie firma Microsoft otrzyma *tonacji* będącą liczbą zakresu od 0 do 1. Chcemy przekształcić je należeć do zakresu od -1 do 1. Aby wykonywać to przekształcenie pomocnicza, możemy użyć warunkowego umiejętności.

W tym przykładzie firma Microsoft nie używaj warunkowego aspekt umiejętności ponieważ warunek jest zawsze *true*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Specjalne uwagi
Niektóre parametry są oceniane, dlatego należy zachować szczególną ostrożność oparte na wzorcu udokumentowane. Wyrażenia musi rozpoczynać się od znaku równości. Ścieżki muszą być rozdzielone "$(" and ")". Upewnij się, że umieszczanie ciągów w znaki pojedynczego cudzysłowu. Temu ewaluatora rozróżnienia ciągów i rzeczywiste ścieżki i operatorów. Ponadto upewnij się umieścić biały znak dookoła operatorów (na przykład "*" w ścieżce inne znaczenie niż multiply).


## <a name="next-steps"></a>Kolejne kroki

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
