---
title: Pojęcia i przepływ pracy skillset
titleSuffix: Azure Cognitive Search
description: Skillsets to miejsce, w którym autor potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search. Poznaj ważne pojęcia i szczegóły dotyczące składu zestawu umiejętności.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191022"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Pojęcia i kompozycja zestawu umiejętności w usłudze Azure Cognitive Search

Ten artykuł jest przeznaczony dla deweloperów, którzy potrzebują głębszego zrozumienia, jak działa potok wzbogacania i zakłada, że masz koncepcyjne zrozumienie procesu wzbogacania AI. Jeśli jesteś nowy, zacznij od:
+ [Wzbogacanie sztucznej inteligencji w usłudze Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Określ zestaw umiejętności
Zestaw umiejętności to zasób wielokrotnego użytku w usłudze Azure Cognitive Search, który określa kolekcję umiejętności poznawczych używanych do analizowania, przekształcania i wzbogacania zawartości tekstu lub obrazu podczas indeksowania. Tworzenie zestawu umiejętności umożliwia dołączanie wzbogacenia tekstu i obrazu w fazie pozyskiwania danych, wyodrębnianie i tworzenie nowych informacji i struktur z zawartości nieprzetworzonej.

Zestaw umiejętności ma trzy właściwości:

+   ```skills```, nieuiszczona kolekcja umiejętności, dla których platforma określa kolejność wykonywania na podstawie danych wejściowych wymaganych dla każdej umiejętności
+   ```cognitiveServices```, klucz usług poznawczych wymagany do rozliczania umiejętności poznawczych
+   ```knowledgeStore```, konto pamięci, na którym będą wyświetlane wzbogacone dokumenty



Skillsets są autorstwa w JSON. Można tworzyć złożone zestawy umiejętności z zapętlania i [rozgałęzienia](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) przy użyciu [języka wyrażenia](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Język wyrażenia używa notacji ścieżki [wskaźnika JSON](https://tools.ietf.org/html/rfc6901) z kilkoma modyfikacjami w celu zidentyfikowania węzłów w drzewie wzbogacania. A ```"/"``` przechodzi poziom niższy w ```"*"``` drzewie i działa jako for-each operatora w kontekście. Te pojęcia są najlepiej opisane na przykładzie. Aby zilustrować niektóre koncepcje i możliwości, przejdziemy przez [przykładowy zestaw umiejętności.](knowledge-store-connect-powerbi.md) Aby wyświetlić zestaw umiejętności po przejściu do przepływu pracy danych importu, musisz użyć klienta interfejsu API REST, aby [uzyskać zestaw umiejętności](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Drzewo wzbogacania

Aby wyobrazić sobie, jak zestaw umiejętności stopniowo wzbogaca dokument, zacznijmy od tego, jak dokument wygląda przed jakimkolwiek wzbogaceniem. Dane wyjściowe pękania dokumentu zależą od źródła danych i wybranego określonego trybu analizy. Jest to również stan dokumentu, z których [mapowania pól](search-indexer-field-mappings.md) mogą pozyskiwać zawartość podczas dodawania danych do indeksu wyszukiwania.
![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Magazyn wiedzy w diagramie potoku")

Gdy dokument znajduje się w potoku wzbogacania, jest reprezentowany jako drzewo zawartości i skojarzonych wzbogacenia. To drzewo jest tworzone jako dane wyjściowe pękania dokumentu. Format drzewa wzbogacania umożliwia potoku wzbogacania dołączyć metadane do nawet pierwotnych typów danych, nie jest prawidłowy obiekt JSON, ale mogą być rzutowane w prawidłowym formacie JSON. W poniższej tabeli przedstawiono stan dokumentu wprowadzanego do potoku wzbogacania:

|Źródło danych\Tryb analizowania|Domyślne|JSON, JSON Linie & CSV|
|---|---|---|
|Blob Storage|/dokument/treść<br>/dokument/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|Nie dotyczy |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|Nie dotyczy|

 W miarę wykonywania umiejętności dodają nowe węzły do drzewa wzbogacania. Te nowe węzły mogą być następnie używane jako dane wejściowe dla dalszych umiejętności, rzutowanie do magazynu wiedzy lub mapowanie pól indeksu. Wzbogacenia nie są modyfikowalne: po utworzeniu węzłów nie można edytować. Jak skillsets stają się bardziej złożone, więc będzie drzewa wzbogacania, ale nie wszystkie węzły w drzewie wzbogacenia trzeba zrobić to do indeksu lub magazynu wiedzy. 

Można selektywnie utrwalić tylko podzbiór wzbogacenia do indeksu lub magazynu wiedzy.
W pozostałej części tego dokumentu zakładamy, że pracujemy z [przykładem recenzji hoteli,](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)ale te same koncepcje mają zastosowanie do wzbogacania dokumentów ze wszystkich innych źródeł danych.

### <a name="context"></a>Kontekst
Każda umiejętność wymaga kontekstu. Kontekst określa:
+   Liczba wykonań umiejętności na podstawie wybranych węzłów. Dla wartości kontekstu kolekcji ```/*``` typu dodanie na końcu spowoduje, że umiejętność jest wywoływana raz dla każdego wystąpienia w kolekcji. 
+   Gdzie w drzewie wzbogacenia są dodawane wyniki umiejętności. Dane wyjściowe są zawsze dodawane do drzewa jako elementów podrzędnych węzła kontekstu. 
+   Kształt wejść. W przypadku kolekcji wielopoziomowych ustawienie kontekstu na kolekcję nadrzędną wpłynie na kształt danych wejściowych dla umiejętności. Na przykład jeśli masz drzewo wzbogacania z listą krajów, każdy wzbogacony o listę państw zawierających listę kodów pocztowych.

|Kontekst|Dane wejściowe|Kształt wejścia|Wywołanie umiejętności|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Lista wszystkich kodów pocztowych w kraju |Raz na kraj |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Lista kodów pocztowych w stanie | Raz na kombinację kraju i państwa|

### <a name="sourcecontext"></a>ŹródłoContext

Jest `sourceContext` używany tylko w wejściach umiejętności i [projekcjach](knowledge-store-projection-overview.md). Służy do konstruowania wielopoziomowych, zagnieżdżonych obiektów. Może być konieczne utworzenie nowego obiektu, aby przekazać go jako dane wejściowe do umiejętności lub projektu do magazynu wiedzy. Ponieważ węzły wzbogacania mogą nie być prawidłowym obiektem JSON w drzewie wzbogacania i odwoływanie się do węzła w drzewie zwraca tylko ten stan węzła podczas jego tworzenia, przy użyciu wzbogacenia jako dane wejściowe umiejętności lub projekcje wymaga utworzenia dobrze sformułowanego obiektu JSON. Umożliwia `sourceContext` konstruowanie hierarchicznego, anonimowego obiektu typu, który wymagałby wielu umiejętności, jeśli używasz tylko kontekstu. Użycie `sourceContext` jest pokazane w następnej sekcji. Spójrz na dane wyjściowe umiejętności, które wygenerowały wzbogacenie, aby ustalić, czy jest to prawidłowy obiekt JSON, a nie typ pierwotny.

### <a name="projections"></a>Projekcje

Projekcja to proces wybierania węzłów z drzewa wzbogacania, które mają zostać zapisane w magazynie wiedzy. Rzuty są niestandardowymi kształtami dokumentu (zawartością i wzbogaceniami), które mogą być wyprowadzane jako rzuty tabeli lub obiektu. Aby dowiedzieć się więcej o pracy z projekcjami, zobacz [praca z projekcjami](knowledge-store-projection-overview.md).

![Opcje mapowania pól](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opcje mapowania pól dla potoku wzbogacania")

Na powyższym diagramie opisano selektor, z którym pracujesz, na podstawie tego, gdzie znajdujesz się w potoku wzbogacania.

## <a name="generate-enriched-data"></a>Generowanie wzbogaconych danych 

Przejdźmy teraz do recenzji hotelu, możesz wykonać [samouczek,](knowledge-store-connect-powerbi.md) aby utworzyć zestaw umiejętności lub [wyświetlić](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) zestaw umiejętności. Przyjrzymy się:

* jak drzewo wzbogacania ewoluuje wraz z wykonywaniem każdej umiejętności 
* jak kontekst i dane wejściowe działają w celu określenia, ile razy umiejętność jest wykonywana 
* kształt danych wejściowych jest oparty na kontekście. 

Ponieważ używamy trybu analizowania tekstu rozdzielanego dla indeksatora, dokument w procesie wzbogacania reprezentuje pojedynczy wiersz w pliku CSV.

### <a name="skill-1-split-skill"></a>#1 umiejętności: Umiejętność podziału 

![wzbogacenia drzewa po pękaniu dokumentu](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Drzewo wzbogacania po pękaniu dokumentu i przed wykonaniem umiejętności")

W kontekście ```"/document/reviews_text"```umiejętności , ta umiejętność `reviews_text`wykona się raz dla . Dane wyjściowe umiejętności to `reviews_text` lista, na której są podzielone na 5000 segmentów postaci. Dane wyjściowe z podziału `pages` umiejętności jest nazwany i dodaje do drzewa wzbogacania. Funkcja `targetName` ta umożliwia zmianę nazwy umiejętności przed dodaniem do drzewa wzbogacania.

Drzewo wzbogacania ma teraz nowy węzeł umieszczony w kontekście umiejętności. Ten węzeł jest dostępny dla dowolnej umiejętności, projekcji lub mapowania pól wyjściowych.


Węzłem głównym dla wszystkich `"/document"`wzbogacenia jest . Podczas pracy z indeksatorami `"/document"` obiektów blob węzeł `"/document/content"` `"/document/normalized_images"`będzie miał węzły podrzędne i . Podczas pracy z danymi CSV, jak jesteśmy w tym przykładzie, `"/document"`nazwy kolumn będą mapowane na węzły poniżej . Aby uzyskać dostęp do dowolnego wzbogacenia dodane do węzła przez umiejętności, pełna ścieżka do wzbogacenia jest potrzebna. Na przykład, jeśli chcesz użyć tekstu ```pages``` z węzła jako danych wejściowych do ```"/document/reviews_text/pages/*"```innej umiejętności, musisz określić go jako .
 
 ![wzbogacenie drzewa po #1 umiejętności](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Drzewo wzbogacania po wykonaniu #1 umiejętności")

### <a name="skill-2-language-detection"></a>Wykrywanie języka #2 umiejętności
 Podczas gdy umiejętność wykrywania języka jest trzecią (umiejętnością #3) zdefiniowaną w wycieczce umiejętności, jest to kolejna umiejętność do wykonania. Ponieważ nie jest zablokowany przez wymaganie żadnych danych wejściowych, będzie wykonywany równolegle z poprzednią umiejętnością. Podobnie jak umiejętności podziału, który poprzedził go, umiejętności wykrywania języka jest również wywoływana raz dla każdego dokumentu. Drzewo wzbogacania ma teraz nowy węzeł dla języka.
 ![wzbogacenie drzewa po #2 umiejętności](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Drzewo wzbogacania po wykonaniu #2 umiejętności")
 
 ### <a name="skill-3-key-phrases-skill"></a>#3 umiejętności: Umiejętność kluczowych zwrotów 

Biorąc pod ```/document/reviews_text/pages/*``` uwagę kontekst kluczowych fraz umiejętności będą wywoływane raz `pages` dla każdego z elementów w kolekcji. Dane wyjściowe z umiejętności będzie węzeł w ramach skojarzonego elementu strony. 

 Teraz powinieneś być w stanie spojrzeć na resztę umiejętności w skillset i wizualizować, jak drzewo wzbogacenia będzie nadal rosnąć wraz z wykonywaniem każdej umiejętności. Niektóre umiejętności, takie jak umiejętność scalania i umiejętności shaper, również tworzyć nowe węzły, ale tylko używać danych z istniejących węzłów i nie tworzyć netto nowe wzbogacenia.

![wzbogacenia drzewa po wszystkich umiejętnościach](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Drzewo wzbogacania po wszystkich umiejętnościach")

Kolory łączników w drzewie powyżej wskazują, że wzbogacenia zostały utworzone przez różne umiejętności i węzły będą musiały być adresowane indywidualnie i nie będą częścią obiektu zwróconego podczas wybierania węzła nadrzędnego.

## <a name="save-enrichments-in-a-knowledge-store"></a>Zapisywanie wzbogacenia w magazynie wiedzy 

Skillsets również zdefiniować magazyn wiedzy, gdzie wzbogacone dokumenty mogą być rzutowane jako tabele lub obiekty. Aby zapisać wzbogacone dane w magazynie wiedzy, należy zdefiniować zestaw projekcji wzbogaconego dokumentu. Aby dowiedzieć się więcej o magazynie wiedzy, zobacz [omówienie magazynu wiedzy](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projekcje krojenia

Podczas definiowania grupy rzutowania tabeli pojedynczy węzeł w drzewie wzbogacania można podzielić na wiele powiązanych tabel. Jeśli dodasz tabelę ze ścieżką źródłową, która jest elementem podrzędnym istniejącej projekcji tabeli, wynikowy węzeł podrzędny nie będzie elementem podrzędnym istniejącej projekcji tabeli, ale zostanie rzutowany na nową, powiązaną tabelę. Ta technika krojenia umożliwia zdefiniowanie pojedynczego węzła w umiejętności shaper, która może być źródłem dla wszystkich projekcji tabeli. 

### <a name="shaping-projections"></a>Kształtowanie projekcji

Istnieją dwa sposoby definiowania projekcji. Można użyć shaper umiejętności, aby utworzyć nowy węzeł, który jest węzłem głównym dla wszystkich wzbogacenia są rzutowane. Następnie, w projekcjach, można odwoływać się tylko do danych wyjściowych shaper umiejętności. Można również kształtować w linii ową projekcji w samej definicji rzutowania.

Podejście shaper jest bardziej pełne niż kształtowanie w linii, ale zapewnia, że wszystkie mutacje drzewa wzbogacania są zawarte w umiejętności i że wyjście jest obiektem, który może być ponownie. Kształtowanie w linii umożliwia tworzenie potrzebnego kształtu, ale jest obiektem anonimowym i jest dostępne tylko dla projekcji, dla której jest zdefiniowany. Podejścia mogą być używane razem lub oddzielnie. Zestaw umiejętności utworzony dla Ciebie w przepływie pracy portalu zawiera oba te elementy. Używa shaper umiejętności dla projekcji tabeli, ale także używa kształtowania w linii do projekcji tabeli fraz kluczowych.

Aby rozszerzyć przykład, można usunąć kształtowanie w linii i użyć umiejętności shaper, aby utworzyć nowy węzeł dla fraz kluczowych. Aby utworzyć kształt rzutowany na trzy `hotelReviewsDocument`tabele, a mianowicie , `hotelReviewsPages`i `hotelReviewsKeyPhrases`, dwie opcje są opisane w poniższych sekcjach.


#### <a name="shaper-skill-and-projection"></a>Umiejętność i projekcja Shapera 

> [!Note]
> Niektóre kolumny z tabeli dokumentów zostały usunięte z tego przykładu dla zwięzłości.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Z `tableprojection` węzłem zdefiniowanym w powyższej `outputs` sekcji, możemy teraz użyć funkcji `tableprojection` krojenia do projektu części węzła w różnych tabelach:

> [!Note]
> Jest to tylko fragment projekcji w konfiguracji magazynu wiedzy.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Projekcje kształtujące inline

Podejście do kształtowania w linii nie wymaga umiejętności shaper, ponieważ wszystkie kształty potrzebne do projekcji są tworzone w czasie, gdy są potrzebne. Aby rzutować te same dane, co w poprzednim przykładzie, opcja projekcji wbudowanej wyglądałaby następująco:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Jedną z obserwacji z obu podejść jest sposób, w jaki wartości `"Keyphrases"` są rzutowane za pomocą . `"sourceContext"` Węzeł, `"Keyphrases"` który zawiera kolekcję ciągów, sam jest elementem podrzędnym tekstu strony. Jednak ponieważ rzuty wymagają obiektu JSON, a strona jest pierwotnym (ciągiem), `"sourceContext"` służy do zawijania frazy kluczowej do obiektu z właściwością o nazwie. Ta technika umożliwia nawet prymitywy być rzutowane niezależnie.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem, stwórz swój pierwszy zestaw umiejętności z umiejętnościami poznawczymi.

> [!div class="nextstepaction"]
> [Stwórz swój pierwszy zestaw umiejętności](cognitive-search-defining-skillset.md).
