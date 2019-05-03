---
title: Umiejętności warunkowego wyszukiwanie kognitywne (Azure Search) | Dokumentacja firmy Microsoft
description: Warunkowe umiejętności, która umożliwia filtrowanie, tworzenie wartości domyślne i scalanie wartości.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028428"
---
#   <a name="conditional-skill"></a>Warunkowe umiejętności

**Warunkowego umiejętności** umożliwia różne scenariusze, które wymagają operacja logiczna podjęcie decyzji, dane, które powinny być przypisane do pliku wyjściowego. Te scenariusze obejmują: filtrowanie, przypisywanie wartości domyślne i scalanie danych na podstawie warunku.

Poniższym pseudokodzie wyjaśnia, co wykonuje warunkowego umiejętności:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Umiejętności, to nie jest powiązany z interfejsu API usług Cognitive Services i nie są naliczane dotyczące korzystania z niego. Powinna nadal [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md), jednak aby zastąpić **bezpłatna** resource — Opcja ograniczająca na niewielką liczbę dziennych wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Oceniono pola

To umiejętności jest specjalne, ponieważ jego danych wejściowych są szacowane pola.

Poniżej przedstawiono prawidłowe wartości wyrażenia:

-   Ścieżki adnotacji (muszą być rozdzielone ścieżek w wyrażeniach "$(" and ")") <br/>
    Przykłady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literały (ciągi, liczby, wartość true, false, wartość null) <br/>
    Przykłady:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Wyrażenia, które używają operatora porównania (==,! =, > =, >, < =, <) <br/>
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

-   Wyrażeń liczbowych — operator (+, -, \*, /, %) <br/>
    Przykłady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Ze względu na ocenę obsługiwane warunkowe umiejętności może służyć do scenariuszy przekształceń pomocniczych. Zobacz przykładową [definicji umiejętności 4](#transformation-examples) przykład.

## <a name="skill-inputs"></a>Dane wejściowe umiejętności
W danych wejściowych jest rozróżniana wielkość liter.

| Dane wejściowe      | Opis |
|-------------|-------------|
| warunek   | Jest to wejściowy [obliczane pole](#evaluated-fields) reprezentujący warunek do oceny. Ten warunek powinna być wartość logiczna (true lub false).   <br/>  Przykłady: <br/> "= true" <br/> "= $(/document/language)"fr"==" <br/> "= $(/ stron dokumentu / /\*/language) == $(/ dokumentu/expectedLanguage)" <br/> |
| whenTrue    | Jest to wejściowy [obliczane pole](#evaluated-fields). Wartość zwracana, jeśli warunek zostanie ocenione na wartość true. Stałe ciągów ma zostać zwrócony w ' ' cudzysłowów. <br/>Przykładowe wartości: <br/> "="Umowy""<br/>"= $(/ dokumentu/contractType)" <br/> "= $(/dokumentu/podmioty/\*)" <br/> |
| whenFalse   | Jest to wejściowy [obliczane pole](#evaluated-fields). Wartość zwracana, jeśli warunek zostanie ocenione na wartość false.  <br/>Przykładowe wartości: <br/> "="Umowy""<br/>"= $(/ dokumentu/contractType)" <br/> "= $(/dokumentu/podmioty/\*)" <br/>

## <a name="skill-outputs"></a>Dane wyjściowe umiejętności
Jest pojedynczym wyjściowym o nazwie "output". Jeśli warunek jest prawdziwy zwróci wartość whenFalse, jeśli warunek ma wartość false w przeciwnym razie whenTrue.

## <a name="examples"></a>Przykłady

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Przykładowa definicja umiejętności 1: Filtrowanie dokumentów, aby zwrócić tylko dokumenty "Francuska"

Następujące dane wyjściowe będą zwracać tablicę zdań ("/ dokumentu/frenchSentences"), jeśli jest język dokumentu francuski. Jeśli język nie jest francuski, ta wartość zostanie ustawiona na wartość null.

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
Jeśli "/ dokumentu/frenchSentences" jest używana jako *kontekstu* innego umiejętności umiejętności będą uruchamiane tylko jeśli "/ dokumentu/frenchSentences" nie jest ustawiona na wartość null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Przykładowa definicja umiejętności 2: Ustawianie wartości domyślnej, gdy nie istnieje.

Następujące dane wyjściowe utworzy adnotacji ("/ dokumentu/languageWithDefault"), który jest ustawiony na język dokumentu lub "es", jeśli język nie jest ustawiona.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Przykładowa definicja umiejętności 3: Scalanie wartości z dwóch różnych pól w jedno pole

W tym przykładzie ma pewne zdania *frenchSentiment* właściwości. Zawsze, gdy *frenchSentiment* właściwość ma wartość null, prosimy o poświęcenie używać *englishSentiment* wartości. Firma Microsoft Przypisz dane wyjściowe do elementu członkowskiego, wywoływana po prostu *tonacji* ("/ dokumentu/tonacji / * / tonacji").

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

## <a name="transformation-examples"></a>Przykłady przekształcania
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Przykładowa definicja umiejętności 4: Wykonywanie przekształcenia danych na jedno pole

W tym przykładzie firma Microsoft otrzyma opinii od 0 do 1. Ponadto chcielibyśmy przekształcić je tak, aby od -1 do 1. Jest to przekształcenie matematyczne małych, że firma Microsoft może zrobić, za pomocą warunkowego umiejętności.

W tym konkretnym przykładzie firma Microsoft nigdy nie używaj warunkowego aspekt umiejętności, jako warunek ma zawsze wartość true. 

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
Należy pamiętać, że niektóre parametry są oceniane, dlatego należy zachować szczególną ostrożność, zgodnie z udokumentowanymi wzorzec. Wyrażenia muszą zaczynać się równa się znak "=" i ścieżki muszą być rozdzielone "$(" and ")". Pamiętaj umieścić Twoimi ciągami w "apostrofy", ponieważ pomoże ewaluatora rozróżnienia ciągów i rzeczywiste ścieżki i operatorów. Ponadto upewnij się umieścić odstępów dookoła operatorów (na przykład * w ścieżce ma inne znaczenie niż operator mnożenia).


## <a name="next-steps"></a>Kolejne kroki

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
