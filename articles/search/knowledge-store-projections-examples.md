---
title: Definiowanie projekcji w magazynie wiedzy
titleSuffix: Azure Cognitive Search
description: Przykłady typowych wzorców dotyczących sposobu projekcji wzbogaconych dokumentów do magazynu wiedzy do użytku z usługą Power BI lub Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943678"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Prognozy magazynu wiedzy: Jak kształtować i eksportować wzbogacenia

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Interfejs [API REST w wersji 2019-05-06-Preview](search-api-preview.md) udostępnia funkcje w wersji zapoznawczej. Obecnie istnieje ograniczona obsługa portalu i nie ma obsługi sdk .NET.

Projekcje są fizycznym wyrazem wzbogaconych dokumentów w magazynie wiedzy. Efektywne wykorzystanie wzbogaconych dokumentów wymaga struktury. W tym artykule poznasz zarówno strukturę, jak i relacje, ucząc się, jak tworzyć właściwości projekcji, a także jak powiązać dane między utworzonymi typami projekcji. 

Aby utworzyć projekcję, należy kształtować dane przy użyciu [umiejętności Shaper,](cognitive-search-skill-shaper.md) aby utworzyć obiekt niestandardowy lub użyć składni kształtowania w linii wbudowanej w definicji projekcji. 

Kształt danych zawiera wszystkie dane, które mają być wyświetlane, utworzone jako hierarchia węzłów. W tym artykule przedstawiono kilka technik kształtowania danych, dzięki czemu mogą być rzutowane na struktury fizyczne, które sprzyjają raportowaniu, analizie lub przetwarzaniu podrzędnym. 

Przykłady przedstawione w tym artykule można znaleźć w tym [przykładzie interfejsu API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), który można pobrać i uruchomić w kliencie HTTP.

## <a name="introduction-to-the-examples"></a>Wprowadzenie do przykładów

Jeśli znasz [projekcje,](knowledge-store-projection-overview.md)przypomnij sobie, że istnieją trzy typy:

+ Tabele
+ Obiekty
+ Pliki

Projekcje tabel są przechowywane w usłudze Azure Table Storage. Rzuty obiektów i plików są zapisywane w magazynie obiektów blob, gdzie rzuty obiektów są zapisywane jako pliki JSON i mogą zawierać zawartość z dokumentu źródłowego, a także wszelkie dane wyjściowe umiejętności lub wzbogacenia. Potok wzbogacania można również wyodrębnić pliki binarne, takie jak obrazy, te pliki binarne są rzutowane jako rzuty plików. Gdy obiekt binarny jest rzutowany jako rzutowanie obiektu, tylko metadane skojarzone z nim są zapisywane jako obiekt blob JSON. 

Aby zrozumieć przecięcie między kształtowaniem danych i projekcjami, użyjemy następującego zestawu umiejętności jako podstawy do eksplorowania różnych konfiguracji. Ten zestaw umiejętności przetwarza nieprzetworzoną zawartość obrazu i tekstu. Prognozy zostaną zdefiniowane na podstawie zawartości dokumentu i wyników umiejętności, dla scenariuszy, które chcemy wspierać.

> [!IMPORTANT] 
> Podczas eksperymentowania z projekcjami, warto [ustawić właściwość pamięci podręcznej indeksatora,](search-howto-incremental-index.md) aby zapewnić kontrolę kosztów. Edytowanie projekcji spowoduje, że cały dokument zostanie wzbogacony ponownie, jeśli pamięć podręczna indeksatora nie zostanie ustawiona. Po ustawieniu pamięci podręcznej i zaktualizowaniu tylko prognoz wykonania zestawu umiejętności dla wcześniej wzbogaconych dokumentów nie powodują żadnych nowych opłat za usługi Cognitive Services.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Za pomocą tego zestawu `knowledgeStore` umiejętności, z jego null jako `knowledgeStore` podstawa, nasz pierwszy przykład wypełnia obiekt, skonfigurowany z projekcji, które tworzą struktury danych tabelaryczne możemy użyć w innych scenariuszach. 

## <a name="projecting-to-tables"></a>Rzutowanie do tabel

Projekcja do tabel w usłudze Azure Storage jest przydatna do raportowania i analizy przy użyciu narzędzi, takich jak usługa Power BI. Usługa Power BI może odczytywać z tabel i odnajdywać relacje na podstawie kluczy generowanych podczas projekcji. Jeśli próbujesz utworzyć pulpit nawigacyjny, posiadanie powiązanych danych uprości to zadanie. 

Załóżmy, że próbujemy zbudować pulpit nawigacyjny, na którym możemy wizualizować kluczowe frazy wyodrębnione z dokumentów jako chmurę słów. Aby utworzyć odpowiednią strukturę danych, możemy dodać umiejętność Shaper do zestawu umiejętności, aby utworzyć niestandardowy kształt zawierający szczegóły i kluczowe frazy specyficzne dla dokumentu. Niestandardowy kształt zostanie `pbiShape` wywołany w węźle `document` głównym.

> [!NOTE] 
> Prognozy tabel są tabelami usługi Azure Storage, podlegami limitom magazynowania narzuconym przez usługę Azure Storage. Aby uzyskać więcej informacji, zobacz [limity magazynowania tabel](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Warto wiedzieć, że rozmiar jednostki nie może przekraczać 1 MB i pojedyncza właściwość nie może być większa niż 64 KB. Te ograniczenia sprawiają, że tabele dobrym rozwiązaniem do przechowywania dużej liczby małych jednostek.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Tworzenie niestandardowego kształtu za pomocą umiejętności Shaper

Utwórz niestandardowy kształt, który można wyświetlać w magazynie tabel. Bez kształtu niestandardowego projekcja może odwoływać się tylko do jednego węzła (jedna projekcja na wyjście). Tworzenie kształtu niestandardowego umożliwia agregowanie różnych elementów w nową całość logiczną, która może być rzutowana jako pojedyncza tabela lub krojona i rozmieszczana w kolekcji tabel. 

W tym przykładzie kształt niestandardowy łączy metadane i zidentyfikowane jednostki i frazy kluczowe. Obiekt jest `pbiShape` wywoływany i `/document`jest nadrzędny w obszarze . 

> [!IMPORTANT] 
> Jednym z celów kształtowania jest zapewnienie, że wszystkie węzły wzbogacania są wyrażone w dobrze ukształtowanym JSON, który jest wymagany do projekcji w magazynie wiedzy. Jest to szczególnie ważne, gdy drzewo wzbogacania zawiera węzły, które nie są dobrze sformułowane JSON (na przykład, gdy wzbogacenie jest nadrzędne do pierwotnego jak ciąg).
>
> Zwróć uwagę na dwa `KeyPhrases` `Entities`ostatnie węzły i . Są one zawijane w prawidłowy obiekt JSON z . `sourceContext` Jest to wymagane `keyphrases` `entities` jako i są wzbogacenia na prymitywy i muszą być konwertowane na prawidłowe JSON, zanim będą mogły być rzutowane.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Dodaj powyższą umiejętność Shaper do umiejętności. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Teraz, gdy mamy wszystkie dane potrzebne do projektu do tabel, zaktualizuj obiekt knowledgeStore za pomocą definicji tabel. W tym przykładzie mamy trzy tabele, `source` `generatedKeyName` zdefiniowane przez ustawienie `tableName`, i właściwości.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Możesz przetworzyć swoją pracę, wykonując następujące kroki:

1. Ustaw ```storageConnectionString``` właściwość na prawidłowy parametry połączenia konta magazynu ogólnego przeznaczenia w wersji 2.  

1. Zaktualizuj zestaw umiejętności, wystawiając żądanie PUT.

1. Po zaktualizowaniu zestawu umiejętności uruchom indeksator. 

Masz teraz projekcję roboczą z trzema tabelami. Importowanie tych tabel do usługi Power BI powinno spowodować automatyczne odnajdowanie relacji w usłudze Power BI.

Przed przejściem do następnego przykładu, pozwala ponownie aspekty projekcji tabeli, aby zrozumieć mechanikę krojenia i odnoszących się danych.

### <a name="slicing"></a>Krojenie 

Krojenie jest techniką, która dzieli cały skonsolidowany kształt na części składowe. Wynik składa się z oddzielnych, ale powiązanych tabel, z którymi można pracować indywidualnie.

W przykładzie `pbiShape` znajduje się kształt skonsolidowany (lub węzeł wzbogacania). W definicji `pbiShape` projekcji jest pocięta na dodatkowe tabele, ```keyPhrases``` co ```Entities```umożliwia wyciągnięcie części kształtu i . W usłudze Power BI jest to przydatne, ponieważ wiele jednostek i kluczyOdz są skojarzone z każdym dokumentem, a otrzymasz więcej szczegółowych informacji, jeśli można zobaczyć jednostki i keyPhrases jako dane skategoryzowane.

Krojenie niejawnie generuje relację między tabelami ```generatedKeyName``` nadrzędnymi i podrzędnymi, używając tabeli nadrzędnej do utworzenia kolumny o tej samej nazwie w tabeli podrzędnej. 

### <a name="naming-relationships"></a>Nazywanie relacji

Właściwości ```generatedKeyName``` ```referenceKeyName``` i są używane do powiązania danych między tabelami lub nawet między typami projekcji. Każdy wiersz w tabeli podrzędnej/projekcji ma właściwość skierowaną z powrotem do obiektu nadrzędnego. Nazwa kolumny lub właściwości w podrzędnym ```referenceKeyName``` jest z nadrzędnego. Gdy ```referenceKeyName``` usługa nie jest dostarczana, usługa ```generatedKeyName``` domyślnie go z nadrzędnego. 

Usługa Power BI korzysta z tych wygenerowanych kluczy do odnajdywanie relacji w tabelach. Jeśli potrzebujesz kolumny w tabeli podrzędnej o ```referenceKeyName``` nazwie inaczej, ustaw właściwość w tabeli nadrzędnej. Jednym z przykładów ```generatedKeyName``` może być ustawienie jako identyfikator w tabeli pbiDocument i ```referenceKeyName``` jako DocumentID. Spowodowałoby to kolumny w pbiEntities i pbiKeyPhrases tabel zawierających identyfikator dokumentu o nazwie DocumentID.

## <a name="projecting-to-objects"></a>Rzutowanie na obiekty

Rzuty obiektów nie mają tych samych ograniczeń co rzuty tabeli i lepiej nadają się do projekcji dużych dokumentów. W tym przykładzie rzut całego dokumentu na rzut obiektu. Rzuty obiektów są ograniczone do pojedynczego rzutowania w kontenerze i nie mogą być krojona.

Aby zdefiniować rzutowanie obiektu, ```objects``` użyjemy tablicy w rzutach. Nowy kształt można wygenerować za pomocą umiejętności Shaper lub użyć kształtowania wbudowanego rzutowania obiektu. Podczas gdy w przykładzie tabel pokazano podejście tworzenia kształtu i krojenia, w tym przykładzie pokazano użycie kształtowania wbudowanego. 

Kształtowanie w linii to możliwość tworzenia nowego kształtu w definicji danych wejściowych do projekcji. Kształtowanie w linii tworzy anonimowy obiekt, który jest identyczny z `pbiShape`tym, co umiejętność Shaper będzie produkować (w naszym przypadku). Kształtowanie w linii jest przydatne, jeśli definiujesz kształt, którego nie zamierzasz używać ponownie.

Właściwość projections jest tablicą. W tym przykładzie dodajemy nowe wystąpienie projekcji do tablicy, gdzie definicja knowledgeStore zawiera rzutowania wbudowane. Podczas korzystania z projekcji wbudowanych można pominąć umiejętność Shaper.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Rzutowanie do pliku

Rzuty plików są obrazy, które są wyodrębniane z dokumentu źródłowego lub dane wyjściowe wzbogacenia, które mogą być rzutowane z procesu wzbogacania. Projekcje plików, podobnie jak projekcje obiektów, są implementowane jako obiekty blob w usłudze Azure Storage i zawierają obraz. 

Aby wygenerować projekcję `files` pliku, używamy tablicy w obiekcie rzutowania. W tym przykładzie przedstawia wszystkie obrazy wyodrębnione z dokumentu do kontenera o nazwie `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projekcja na wiele typów

Bardziej złożony scenariusz może wymagać projektu zawartości między typami projekcji. Na przykład jeśli chcesz rzutować niektóre dane, takie jak frazy kluczowe i jednostki do tabel, zapisz wyniki OCR tekstu tekstu i układu jako obiekty, a następnie projektuj obrazy jako pliki. 

W tym przykładzie aktualizacje zestawu umiejętności obejmują następujące zmiany:

1. Utwórz tabelę z wierszem dla każdego dokumentu.
1. Utwórz tabelę związaną z tabelą dokumentu z każdą frazą kluczową zidentyfikowaną jako wiersz w tej tabeli.
1. Utwórz tabelę powiązaną z tabelą dokumentów z każdą jednostką zidentyfikowaną jako wiersz w tej tabeli.
1. Utwórz projekcję obiektu z tekstem układu dla każdego obrazu.
1. Utwórz projekcję pliku, wyświetlając każdy wyodrębniony obraz.
1. Utwórz tabelę odsyłacza zawierającą odwołania do tabeli dokumentów, projekcji obiektu z tekstem układu i projekcji pliku.

Zmiany te są odzwierciedlane w definicji knowledgeStore dalej w dół. 

### <a name="shape-data-for-cross-projection"></a>Dane kształtu dla projekcji krzyżowej

Aby uzyskać kształty potrzebne do tych projekcji, zacznij od dodania nowej umiejętności `crossProjection`Shaper, która tworzy kształtowany obiekt o nazwie . 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definiowanie projekcji tabel, obiektów i plików

Z skonsolidowanego obiektu crossProjection możemy podzielić obiekt na wiele tabel, przechwycić dane wyjściowe OCR jako obiekty blob, a następnie zapisać obraz jako pliki (również w magazynie obiektów Blob).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Rzuty obiektów wymagają nazwy kontenera dla każdej projekcji, rzutowania obiektów lub rzutowania plików nie mogą współużytkowania kontenera. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relacje między projekcjami tabeli, obiektów i plików

W tym przykładzie przedstawiono również inną funkcję projekcji. Definiując wiele typów projekcji w tym samym obiekcie rzutowania, istnieje relacja wyrażona w obrębie i między różnymi typami (tabele, obiekty, pliki), co pozwala rozpocząć od wiersza tabeli dla dokumentu i znaleźć cały tekst OCR dla obrazów w tym dokumencie w rzutach obiektu. 

Jeśli nie chcesz, aby dane były powiązane, zdefiniuj rzutowania w różnych obiektach rzutowania. Na przykład poniższy fragment kodu spowoduje, że tabele są powiązane, ale bez relacji między tabelami a rzutami tekstu obiektu (Tekst OCR). 

Grupy rzutowania są przydatne, gdy chcesz wyświetlać te same dane w różnych kształtach dla różnych potrzeb. Na przykład grupa projekcji dla pulpitu nawigacyjnego usługi Power BI i inna grupa projekcji do przechwytywania danych używanych do uczenia modelu uczenia maszynowego opakowanego w umiejętności niestandardowe.

Podczas tworzenia projekcji różnych typów najpierw generowane są rzuty plików i obiektów, a ścieżki są dodawane do tabel.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Typowe problemy

Podczas definiowania projekcji, istnieje kilka typowych problemów, które mogą powodować nieoczekiwane wyniki. Sprawdź te problemy, jeśli dane wyjściowe w magazynie wiedzy nie jest to, czego oczekujesz.

+ Nie kształtowanie wzbogacenia ciągów do prawidłowego JSON. Gdy ciągi są wzbogacone, na przykład `merged_content` wzbogacone frazami kluczowymi, wzbogacona `merged_content` właściwość jest reprezentowana jako element podrzędny w drzewie wzbogacania. Domyślna reprezentacja nie jest dobrze sformułowana JSON. Tak więc w czasie projekcji, upewnij się, aby przekształcić wzbogacenie w prawidłowy obiekt JSON o nazwie i wartości.

+ Pomijając na ```/*``` końcu ścieżki źródłowej. Jeśli źródłem projekcji `/document/pbiShape/keyPhrases`jest , tablica fraz kluczowych jest rzutowana jako pojedynczy obiekt/wiersz. Zamiast tego ustaw ścieżkę `/document/pbiShape/keyPhrases/*` źródłową, aby uzyskać pojedynczy wiersz lub obiekt dla każdej z kluczowych fraz.

+ Błędy składni ścieżki. Selektory ścieżek są rozróżniane i mogą prowadzić do brakujących ostrzeżeń wejściowych, jeśli nie używasz dokładnej wielkości liter dla selektora.

## <a name="next-steps"></a>Następne kroki

Przykłady w tym artykule zademonstrować typowe wzorce na sposób tworzenia projekcji. Teraz, gdy masz dobre zrozumienie pojęć, jesteś lepiej przygotowany do tworzenia prognoz dla konkretnego scenariusza.

Podczas eksplorowania nowych funkcji należy wziąć pod uwagę przyrostowe wzbogacenie jako następny krok. Przyrostowe wzbogacenie opiera się na buforowaniu, co pozwala na ponowne użycie wszelkich wzbogacenia, które w przeciwnym razie nie są dotknięte modyfikacją zestawu umiejętności. Jest to szczególnie przydatne w przypadku potoków, które obejmują OCR i analizy obrazu.

> [!div class="nextstepaction"]
> [Wprowadzenie do przyrostowego wzbogacania i buforowania](cognitive-search-incremental-indexing-conceptual.md)

Aby uzyskać przegląd projekcji, dowiedz się więcej o możliwościach, takich jak grupy i krojenie, oraz o tym, jak [definiujesz je w zestawie umiejętności](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projekcje w magazynie wiedzy](knowledge-store-projection-overview.md)

