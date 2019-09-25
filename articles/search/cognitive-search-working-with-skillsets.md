---
title: Praca z umiejętności — Azure Search
description: Umiejętności to miejsce, w którym tworzysz potok wzbogacenia AI w usłudze wyszukiwania poznawczego, opisując kilka koncepcji i jak działa umiejętności, można tworzyć proste lub złożone umiejętności
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: vikurpad
ms.openlocfilehash: f75e6dece376076d4aa5e33497aff7e4f9f56857
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265693"
---
# <a name="working-with-skillsets"></a>Praca z umiejętności
Ten artykuł jest przeznaczony dla deweloperów, którzy potrzebują dokładniejszego wglądu w sposób działania potoku wzbogacania i przyjęto, że masz koncepcję opisującą proces wyszukiwania poznawczego. Jeśli jesteś nowym elementem wyszukiwania poznawczego, Zacznij od:
+ [Co to jest "wyszukiwanie poznawcze" w Azure Search?](cognitive-search-concept-intro.md)
+ [Co to jest sklep merytoryczny w Azure Search?](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Określ zestawu umiejętności
Zestawu umiejętności to zasób wielokrotnego użytku w Azure Search, który określa zbiór umiejętności poznawczych służących do analizowania, przekształcania i wzbogacania zawartości tekstu lub obrazu podczas indeksowania. Utworzenie zestawu umiejętności umożliwia dołączenie wzbogacania tekstu i obrazów w fazie pozyskiwania danych, wyodrębnianie i tworzenie nowych informacji i struktur z nieprzetworzonej zawartości.

Zestawu umiejętności ma trzy właściwości:

+   ```skills```, nieuporządkowana kolekcja umiejętności, dla których platforma Określa sekwencję wykonywania na podstawie danych wejściowych wymaganych dla każdej umiejętności
+   ```cognitiveServices```klucz usługi poznawczej wymagany do rozliczania wywołanych umiejętności poznawczych
+   ```knowledgeStore```, konto magazynu, w którym będą rzutowane wzbogacone dokumenty



Umiejętności są tworzone w formacie JSON. Można tworzyć złożone umiejętności z użyciem pętli i [rozgałęziania](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-conditional) przy użyciu [języka wyrażeń](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Język wyrażeń używa notacji ścieżki [wskaźnika JSON](https://tools.ietf.org/html/rfc6901) z kilkoma modyfikacjami do identyfikowania węzłów w drzewie wzbogacania. Przechodzi poziom niżej w drzewie i ```"*"``` działa jako operator for-each w kontekście. ```"/"``` Te koncepcje najlepiej opisać na przykład. Aby zilustrować niektóre koncepcje i możliwości, przeprowadzimy Cię przez zestawu umiejętnoście [przeglądów w hotelu](knowledge-store-connect-powerbi.md) . Aby wyświetlić zestawu umiejętności po wykonaniu przepływu pracy Importowanie danych, należy użyć klienta interfejsu API REST w celu [uzyskania zestawu umiejętności](https://docs.microsoft.com/en-us/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Drzewo wzbogacania

Aby Envision, w jaki sposób zestawu umiejętności stopniowo wzbogaca swój dokument, Zacznijmy od tego, jak wygląda dokument przed dowolnymi wzbogacaniem. Dane wyjściowe łamania dokumentu są zależne od źródła danych i wybranego trybu analizy. Jest to również stan dokumentu, z którego [mapowania pól](search-indexer-field-mappings.md) mogą źródłowe zawartość podczas dodawania danych do indeksu wyszukiwania.
![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Magazyn wiedzy w diagramie potoku")

Gdy dokument znajduje się w potoku wzbogacenia, jest reprezentowany jako drzewo zawartości i powiązane wzbogacania. To drzewo jest tworzone jako dane wyjściowe pęknięcia dokumentu. Format drzewa wzbogacania umożliwia potokowi wzbogacanie dołączenie metadanych do nawet pierwotnych typów danych, ale nie jest prawidłowym obiektem JSON, ale można go umieścić w prawidłowym formacie JSON. W poniższej tabeli przedstawiono stan dokumentu wprowadzanego do potoku wzbogacania:

|Tryb Source\Parsing danych|Domyślny|JSON, wiersze JSON & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|ND |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|ND|

 W miarę wykonywania umiejętności Dodaj nowe węzły do drzewa wzbogacania. Te nowe węzły mogą być następnie używane jako dane wejściowe dla umiejętności podrzędnych, projekcja w sklepie wiedzy lub mapowanie do pól indeksu. Wzbogacania nie są modyfikowalne: po utworzeniu węzły nie mogą być edytowane. Ponieważ umiejętności jest bardziej skomplikowany, to drzewo wzbogacania, ale nie wszystkie węzły w drzewie wzbogacania muszą wprowadzić je do indeksu lub sklepu wiedzy. Można wybiórczo utrwalać tylko podzbiór wzbogaceń do indeksu lub sklepu z bazami danych.

Można wybiórczo utrwalać tylko podzbiór wzbogaceń do indeksu lub sklepu z bazami danych.
W pozostałej części tego dokumentu przyjęto założenie, że pracujemy z [przykładami przeglądów hotelu](https://docs.microsoft.com/en-us/azure/search/knowledge-store-connect-powerbi), ale te same koncepcje dotyczą wzbogacania dokumentów ze wszystkich innych źródeł danych.

### <a name="context"></a>Kontekst
Każda umiejętność wymaga kontekstu. Kontekst określa:
+   Liczba wykonań kwalifikacji na podstawie wybranych węzłów. W przypadku wartości kontekstu typu Collection dodanie ```/*``` na końcu spowoduje, że umiejętność zostanie wywołana raz dla każdego wystąpienia w kolekcji. 
+   Gdzie w drzewie wzbogacania są dodawane dane wyjściowe umiejętności. Dane wyjściowe są zawsze dodawane do drzewa jako elementy podrzędne węzła kontekstu. 
+   Kształt danych wejściowych. W przypadku kolekcji wielopoziomowych ustawienie kontekstu dla kolekcji nadrzędnej będzie miało wpływ na kształt danych wejściowych. Na przykład jeśli masz drzewo wzbogacania z listą krajów, z których każda została ulepszona, za pomocą listy Stanów zawierających listę ZipCodes.

|Kontekst|Dane wejściowe|Kształt danych wejściowych|Wywołanie umiejętności|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Lista wszystkich ZipCodes w kraju |Raz na kraj |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Lista ZipCodes w stanie | Raz na kombinację kraju i stanu|

### <a name="sourcecontext"></a>SourceContext

Jest używany tylko w [umiejętnościach](cognitive-search-skill-shaper.md) i [projekcjach](knowledge-store-projection-overview.md)kształtu. `sourceContext` Służy do konstruowania obiektów zagnieżdżonych na wiele poziomów. `sourceContext` Umożliwia konstruowanie obiektu hierarchicznego typu anonimowego, który będzie wymagał wielu umiejętności, jeśli używany jest tylko kontekst. Użycie `sourceContext` jest pokazane w następnej sekcji.

### <a name="projections"></a>Projekcje

Projekcja to proces wyboru węzłów z drzewa wzbogacania, które ma zostać zapisane w sklepie z bazami danych. Projekcje to niestandardowe kształty dokumentu (zawartość i wzbogacanie), które mogą być wyprowadzane jako projekcje tabeli lub obiektu. Aby dowiedzieć się więcej o pracy z projekcjami, zobacz [Praca z projekcjami](knowledge-store-projection-overview.md).

![Opcje mapowania pól](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opcje mapowania pól dla potoku wzbogacania")

Na powyższym diagramie opisano selektor, z którym pracujesz, na podstawie tego, gdzie znajduje się w potoku wzbogacania.

## <a name="generate-enriched-data"></a>Generuj wzbogacone dane 

Teraz przejdźmy do zestawu umiejętności recenzji hotelu, możesz wykonać instrukcje [, aby utworzyć](knowledge-store-connect-powerbi.md) zestawu umiejętności lub [wyświetlić](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) zestawu umiejętności. Będziemy szukać:

* jak drzewo wzbogacania rozwija się z wykonywaniem każdej umiejętności 
* jak kontekst i dane wejściowe działają, aby określić, ile razy jest wykonywane umiejętność 
* kształt danych wejściowych jest oparty na kontekście. 

Ponieważ korzystamy z rozdzielonego trybu analizowania tekstu dla indeksatora, dokument w ramach procesu wzbogacania reprezentuje pojedynczy wiersz w pliku CSV.

### <a name="skill-1-split-skill"></a>#1 kwalifikacji: Podziel umiejętność 

![drzewo wzbogacania po rozpoczęciu dokumentu](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Drzewo wzbogacania po rozpoczęciu dokumentu i przed wykonaniem kwalifikacji")

W kontekście ```"/document/reviews_text"```umiejętności, ta umiejętność będzie wykonywana raz `reviews_text`dla. Dane wyjściowe kwalifikacji to lista, `reviews_text` w której znajduje się w segmentach znaków 5000. Dane wyjściowe z podzielonej umiejętności są `pages` nazwane i dodawane do drzewa wzbogacania. `targetName` Funkcja umożliwia zmianę nazwy danych wyjściowych przed ich dodaniem do drzewa wzbogacania.

Drzewo wzbogacania ma teraz nowy węzeł umieszczony w kontekście umiejętności. Ten węzeł jest dostępny dla dowolnego mapowania pól umiejętności, projekcji lub danych wyjściowych.


Węzeł główny dla wszystkich wzbogacań to `"/document"`. Podczas pracy z indeksatorami `"/document"` obiektów BLOB węzeł będzie miał `"/document/content"` węzły podrzędne i `"/document/normalized_images"`. Podczas pracy z danymi CSV, podobnie jak w tym przykładzie, nazwy kolumn są mapowane na węzły poniżej `"/document"`. Aby uzyskać dostęp do dowolnych wzbogacań dodanych do węzła przez umiejętność, wymagana jest pełna ścieżka do wzbogacania. Na przykład jeśli chcesz użyć tekstu z ```pages``` węzła jako dane wejściowe do innej umiejętności, musisz określić go jako. ```"/document/reviews_text/pages/*"```
 
 ![drzewo wzbogacania po #1 umiejętności](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Drzewo wzbogacania po wykonaniu #1 kwalifikacji")

### <a name="skill-2-language-detection"></a>Umiejętność #2 wykrywania języka
 Chociaż umiejętność wykrywania języka to trzecia (umiejętności #3) umiejętność zdefiniowana w zestawu umiejętności, jest to kolejna umiejętność wykonania. Ponieważ nie jest on blokowany przez wymaganie żadnych danych wejściowych, zostanie wykonany równolegle z poprzednią umiejętnością. Podobnie jak w przypadku podzielonej umiejętności, która je poprzedza, umiejętność wykrywania języka jest również wywoływana jednokrotnie dla każdego dokumentu. Drzewo wzbogacania ma teraz nowy węzeł dla języka.
 ![drzewo wzbogacania po #2 umiejętności](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Drzewo wzbogacania po wykonaniu #2 kwalifikacji")
 
 ### <a name="skill-3-key-phrases-skill"></a>#3 kwalifikacji: Umiejętność kluczowych fraz 

W przypadku kontekstu ```/document/reviews_text/pages/*``` kluczowych fraz kwalifikacji zostanie wywołana jednokrotnie dla każdego elementu `pages` w kolekcji. Dane wyjściowe z umiejętności będą węzłem ze skojarzonym elementem strony. 

 Teraz powinno być możliwe przeszukanie pozostałej części umiejętności w zestawu umiejętności i wizualizowanie sposobu, w jaki drzewo wzbogacania będzie stale rosnąć przy wykonywaniu każdej umiejętności. Niektóre umiejętności, takie jak umiejętność scalania i umiejętność kształtowania, również tworzą nowe węzły, ale używają tylko danych z istniejących węzłów i nie tworzą nowych wzbogaceń netto.

![drzewo wzbogacania po wszystkich umiejętnościach](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Drzewo wzbogacania po wszystkich umiejętnościach")

Kolory łączników w powyższym drzewie wskazują, że wzbogacania zostały utworzone przez różne umiejętności, a węzły muszą być rozkierowane osobno i nie będą częścią obiektu zwracanego podczas wybierania węzła nadrzędnego.

## <a name="save-enrichments-in-a-knowledge-store"></a>Zapisz wzbogacenia w sklepie merytorycznym 

Umiejętności również Zdefiniuj magazyn wiedzy, w którym wzbogacone dokumenty mogą być rzutowane jako tabele lub obiekty. Aby zapisać dane wzbogacone w sklepie z bazami informacji, należy zdefiniować zestaw projekcji wzbogaconego dokumentu. Aby dowiedzieć się więcej o sklepie z bazami danych, zobacz artykuł [co to jest sklep merytoryczny w Azure Search?](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projekcje wycinków

Podczas definiowania grupy projekcji tabeli pojedynczy węzeł drzewa wzbogacania można podzielić na wiele powiązanych tabel. Jeśli dodasz tabelę ze ścieżką źródłową, która jest elementem podrzędnym istniejącej projekcji tabeli, utworzony węzeł podrzędny nie będzie elementem podrzędnym istniejącej projekcji tabeli, ale zamiast tego będzie rzutowany do nowej, powiązanej tabeli. Ta technika tworzenia wycinków pozwala zdefiniować pojedynczy węzeł w umiejętności kształtu, który może być źródłem dla wszystkich projekcji tabeli. 

### <a name="shaping-projections"></a>Kształtowanie projekcji

Istnieją dwa sposoby definiowania projekcji. Możesz użyć umiejętności kształtu, aby utworzyć nowy węzeł, który jest węzłem głównym dla wszystkich wzbogaceń. Następnie w projekcjach można odwoływać się tylko do danych wyjściowych umiejętności kształtu. Można również zawbudowanić kształt projekcji w samej definicji projekcji.

Podejście kształtujące jest bardziej pełne niż kształtowanie wbudowane, ale zapewnia, że wszystkie mutacje drzewa wzbogacania są zawarte w umiejętnościach i że dane wyjściowe są obiektem, którego można użyć ponownie. Kształtowanie wbudowane umożliwia utworzenie kształtu, którego potrzebujesz, ale jest obiektem anonimowym i jest dostępny tylko dla projekcji, dla której jest zdefiniowany. Podejścia można używać razem lub oddzielnie. Zestawu umiejętności utworzone dla Ciebie w przepływie pracy portalu zawiera oba te elementy. Używa ona umiejętności kształtu dla projekcji tabeli, ale używa również kształtu wbudowanego do projekcji tabeli wyrażeń kluczowych.

Aby zwiększyć przykład, można wybrać opcję usunięcia kształtu wbudowanego i użyć umiejętności kształtu do utworzenia nowego węzła dla kluczowych fraz. Aby utworzyć kształt podzielony na trzy tabele, mianowicie `hotelReviewsDocument` `hotelReviewsPages`,,, i `hotelReviewsKeyPhrases`, dwie opcje są opisane w poniższych sekcjach.


#### <a name="shaper-skill-and-projection"></a>Umiejętność i projekcja kształtu 

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

W przypadku `outputs` węzła zdefiniowanego w powyższej sekcji możemy teraz używać funkcji dzielenia `tableprojection` na fragmenty projektu części węzła w różnych tabelach: `tableprojection`

> [!Note]
> Jest to tylko fragment kodu projekcji w ramach konfiguracji magazynu wiedzy.
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

#### <a name="inline-shaping-projections"></a>Projekcje w tekście

Podejście kształtowania wbudowanego nie wymaga umiejętności kształtu, ponieważ wszystkie kształty potrzebne do projekcji są tworzone w czasie, gdy są potrzebne. Aby zaprojektować te same dane co w poprzednim przykładzie, opcja rzutowania wbudowanego będzie wyglądać następująco:

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
  
Jednym ze sposobów podejścia jest to, jak wartości `"Keyphrases"` są rzutowane `"sourceContext"`przy użyciu. `"Keyphrases"` Węzeł, który zawiera kolekcję ciągów, jest samym elementem podrzędnym tekstu strony. Jednak ponieważ projekcje wymagają obiektu JSON, a strona jest pierwotna (ciąg), `"sourceContext"` jest używana do zawijania frazy klucza do obiektu z nazwaną właściwością. Ta technika umożliwia niezależne projekcję elementów podstawowych.

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest utworzenie pierwszej zestawu umiejętności z umiejętnościami poznawczymi.

> [!div class="nextstepaction"]
> [Utwórz swój pierwszy zestawu umiejętności](cognitive-search-defining-skillset.md).
