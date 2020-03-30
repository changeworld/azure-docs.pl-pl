---
title: Warunkowa umiejętność poznawcza
titleSuffix: Azure Cognitive Search
description: Umiejętność warunkowa w usłudze Azure Cognitive Search umożliwia filtrowanie, tworzenie wartości domyślnych i scalanie wartości w definicji zestawu umiejętności.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792044"
---
# <a name="conditional-cognitive-skill"></a>Warunkowa umiejętność poznawcza

Umiejętność **warunkowa** umożliwia scenariusze usługi Azure Cognitive Search, które wymagają operacji logicznej w celu określenia danych do przypisania do danych wyjściowych. Scenariusze te obejmują filtrowanie, przypisywanie wartości domyślnej i scalanie danych na podstawie warunku.

Następujący pseudokod pokazuje, co osiąga umiejętność warunkowa:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Ta umiejętność nie jest powiązana z interfejsem API usług Azure Cognitive Services i nie są naliczane opłaty za korzystanie z niej. Jednak nadal należy [dołączyć zasób usług Cognitive Services,](cognitive-search-attach-cognitive-services.md) aby zastąpić opcję "Wolny" zasób, który ogranicza do niewielkiej liczby wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Umiejętności firmy Microsoft.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Ocenione pola

Ta umiejętność jest szczególna, ponieważ jej dane wejściowe są oceniane pola.

Następujące elementy są prawidłowymi wartościami wyrażenia:

-   Ścieżki adnotacji (ścieżki w wyrażeniach muszą być rozdzielone przez "$(" i ")")
 <br/>
    Przykłady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literały (ciągi, liczby, prawda, fałsz, wartość null) <br/>
    Przykłady:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Wyrażenia, które używają operatorów porównania (==, !=, >=, >, <=, <) <br/>
    Przykłady:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Wyrażenia, które używają operatorów logicznych (&&, ||, !, ^) <br/>
    Przykłady:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Wyrażenia, które używają operatorów liczbowych \*(+, -, , /, %) <br/>
    Przykłady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Ponieważ umiejętność warunkowa obsługuje oceny, można go używać w scenariuszach transformacji pomocniczej. Na przykład zobacz [definicję umiejętności 4](#transformation-example).

## <a name="skill-inputs"></a>Wprowadzanie umiejętności
W danych wejściowych jest rozróżniana wielkość liter.

| Dane wejściowe   | Opis |
|-------------|-------------|
| Warunek   | To dane wejściowe jest [polem ocenianym,](#evaluated-fields) które reprezentuje warunek do oceny. Warunek ten powinien być oceniany na wartość logiczną *(true* lub *false).*   <br/>  Przykłady: <br/> "= prawda" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| kiedyTrue    | To dane wejściowe jest [polem ocenianym,](#evaluated-fields) które reprezentuje wartość do zwrócenia, jeśli warunek jest oceniany na *wartość true*. Ciągi stałe powinny być zwracane w pojedynczych cudzysłowach (' i '). <br/>Przykładowe wartości: <br/> "= 'kontrakt'"<br/>"= $(/dokument/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | To dane wejściowe jest [polem ocenianym,](#evaluated-fields) które reprezentuje wartość do zwrócenia, jeśli warunek jest oceniany na *false*. <br/>Przykładowe wartości: <br/> "= 'kontrakt'"<br/>"= $(/dokument/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>Wyniki umiejętności
Jest jedno wyjście, które jest po prostu nazywane "wyjściem". Zwraca wartość *whenFalse,* jeśli warunek jest false lub *whenTrue,* jeśli warunek jest spełniony.

## <a name="examples"></a>Przykłady

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Przykładowa definicja umiejętności 1: filtrowanie dokumentów w celu zwrócenia tylko dokumentów francuskich

Poniższe dane wyjściowe zwracają tablicę zdań ("/document/frenchSentences"), jeśli językiem dokumentu jest francuski. Jeśli język nie jest francuski, wartość jest ustawiona na *null*.

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
Jeśli "/document/frenchSentences" jest używany jako *kontekst* innej umiejętności, ta umiejętność jest uruchamiana tylko wtedy, gdy "/document/frenchSentences" nie jest *ustawiona*na null .


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Przykładowa definicja umiejętności 2: Ustawianie wartości domyślnej dla wartości, która nie istnieje

Poniższe dane wyjściowe tworzy adnotację ("/document/languageWithDefault"), która jest ustawiona na język dokumentu lub "es", jeśli język nie jest ustawiony.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Przykładowa definicja umiejętności 3: Scalanie wartości z dwóch pól w jeden

W tym przykładzie niektóre zdania mają *właściwość frenchSentiment.* Ilekroć *właściwość frenchSentiment* jest null, chcemy użyć *wartości englishSentiment.* Przypisujemy dane wyjściowe do elementu członkowskiego, który nazywa się *tonacji* ("/document/sentiment/*/sentiment").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Przykładowa definicja umiejętności 4: Transformacja danych w jednym polu

W tym przykładzie otrzymujemy *opinię,* która wynosi od 0 do 1. Chcemy przekształcić ją w przedziale od -1 do 1. Możemy użyć umiejętności warunkowej, aby wykonać tę drobną transformację.

W tym przykładzie nie używamy warunkowego aspektu umiejętności, ponieważ warunek jest zawsze *prawdziwy.*

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

## <a name="special-considerations"></a>Uwagi szczególne
Niektóre parametry są oceniane, więc należy zachować szczególną ostrożność, aby postępować zgodnie z udokumentowanym wzorcem. Wyrażenia muszą zaczynać się od znaku równości. Ścieżka musi być rozdzielona przez "$(" i ")". Upewnij się, że ciągi są umieszczane w pojedynczych cudzysłowach. Pomaga to oceniając odróżnić ciągi i rzeczywiste ścieżki i operatorów. Upewnij się również, że biały znak wokół operatorów (na przykład "*" w ścieżce oznacza coś innego niż mnożyć).


## <a name="next-steps"></a>Następne kroki

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
