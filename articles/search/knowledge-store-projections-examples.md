---
title: Definiowanie prognoz w sklepie z bazami danych
titleSuffix: Azure Cognitive Search
description: Przykłady typowych wzorców dotyczących sposobu tworzenia projektów wzbogaconych w sklepie z bazami danych do użycia w Power BI lub platformie Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165516"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Projekcje magazynu wiedzy: jak kształtować i eksportować wzbogacanie do sklepu merytorycznego

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zapewnia funkcje w wersji zapoznawczej. Dostępna jest obecnie ograniczona obsługa portalu i nie ma obsługi zestawu SDK platformy .NET.

Projekcje to fizyczne wyrażenie ulepszonych dokumentów w sklepie z bazami danych. Efektywne korzystanie z ulepszonych dokumentów wymaga struktury. W tym artykule przedstawiono strukturę i relacje, uczenie się, jak tworzyć właściwości projekcji, a także sposób powiązania danych z tworzonych typów projekcji. 

Aby utworzyć projekcję, należy kształtować dane za pomocą umiejętności kształtu, aby utworzyć obiekt niestandardowy lub użyć składni kształtowania wbudowanego. Kształt danych zawiera wszystkie dane, które mają być przeznaczone do projektu. W tym dokumencie przedstawiono przykład każdej opcji, ale można wybrać opcję użycia jednej z opcji tworzenia projekcji.


Istnieją trzy typy projekcji:
+ Tabele
+ Obiekty
+ Pliki

Projekcje tabeli są przechowywane w usłudze Azure Table Storage. Projekcje obiektów i plików są zapisywane w magazynie obiektów blob, projekcje obiektów są zapisywane jako pliki JSON i mogą zawierać zawartość z dokumentu oraz wszelkie dane wyjściowe lub wzbogacania umiejętności. Potok wzbogacania umożliwia również wyodrębnienie plików binarnych, takich jak obrazy, te pliki binarne są rzutowane jako projekcje plików. Gdy obiekt binarny jest rzutowany jako projekcja obiektu, tylko metadane skojarzone z nim są zapisywane jako obiekt BLOB JSON. 

Aby zrozumieć wspólną część między kształtami i projekcjami danych, użyjemy następującej zestawu umiejętności jako podstawy do eksplorowania różnych konfiguracji. Ta zestawu umiejętności przetwarza nieprzetworzoną zawartość obrazu i tekstu. Projekcje zostaną zdefiniowane na podstawie zawartości dokumentu i danych wyjściowych umiejętności dla scenariuszy, które chcemy obsługiwać.

Alternatywnie można pobrać i użyć [przykładowego interfejsu API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) ze wszystkimi wywołaniami w tym instruktażu.

> [!IMPORTANT] 
> Podczas eksperymentowania z projekcjami warto [ustawić właściwość buforu indeksatora](search-howto-incremental-index.md) , aby zapewnić kontrolę kosztów. Edytowanie projekcji spowoduje, że cały dokument zostanie ponownie wzbogacony, jeśli nie zostanie ustawiona pamięć podręczna indeksatora. Gdy pamięć podręczna jest ustawiona i tylko projekcje zostały zaktualizowane, zestawu umiejętności wykonania dla wcześniej wzbogaconych dokumentów nie spowoduje żadnych opłat Cognitive Services.


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

Teraz można dodać obiekt `knowledgeStore` i skonfigurować projekcje dla każdego z tych scenariuszy zgodnie z wymaganiami. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projekcja do tabel w scenariuszach takich jak Power BI

> [!NOTE] 
> Ponieważ magazyn wiedzy jest kontem usługi Azure Storage, projekcje tabeli są tabelami usługi Azure Storage i podlegają limitom magazynu w tabelach, aby uzyskać więcej informacji, zobacz [limity magazynu tabel](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Warto wiedzieć, że rozmiar jednostki nie może przekroczyć 1 MB, a jedna właściwość nie może być większa niż 64 KB. Te ograniczenia sprawiają, że tabele są dobrym rozwiązaniem do przechowywania dużej liczby małych jednostek.

Power BI można odczytywać z tabel i odnajdywać relacje na podstawie kluczy tworzonych przez projekcje ze sklepu wiedzy, dzięki czemu tabele są dobrym rozwiązaniem dla danych projektu podczas próby skompilowania pulpitu nawigacyjnego na ulepszonych danych. Przy założeniu, że próbujemy utworzyć pulpit nawigacyjny, w którym możemy wizualizować kluczowe frazy wyodrębnione z dokumentów jako chmurę wyrazów, możemy dodać umiejętność kształtu do zestawu umiejętności w celu utworzenia niestandardowego kształtu, który ma szczegóły dotyczące określonego dokumentu i kluczowe frazy. Dodaj umiejętność kształtu do zestawu umiejętności, aby utworzyć nowe wzbogacanie o nazwie ```pbiShape``` na ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Tworzenie niestandardowego kształtu przy użyciu umiejętności kształtu

Utwórz niestandardowy kształt, który można projektować w usłudze Table Storage. Bez niestandardowego kształtu projekcja może odwoływać się tylko do jednego węzła (jedno rzutowanie na dane wyjściowe). Tworzenie niestandardowego kształtu pozwala agregować różne elementy w nowej logicznej całości, która może być rzutowana jako pojedyncza tabela lub przecięta i rozłożona w kolekcji tabel. W tym przykładzie kształt niestandardowy łączy metadane i identyfikuje jednostki i kluczowe frazy. Obiekt ma nazwę pbiShape i ma element nadrzędny w obszarze `/document`. 

> [!IMPORTANT] 
> Ścieżki źródłowe dla wzbogacania są wymagane do prawidłowego sformułowania obiektów JSON, zanim będą mogły być rzutowane. Drzewo wzbogacania może reprezentować wzbogacenia, które nie są poprawnie sformułowane w formacie JSON, na przykład gdy wzbogacanie jest nadrzędne do primitve, takiego jak ciąg. Należy zauważyć, jak `KeyPhrases` i `Entities` są opakowane w prawidłowy obiekt JSON z `sourceContext`em, jest to wymagane jako `keyphrases` i `entities` są wzbogacanymi na elementy pierwotne i muszą zostać przekonwertowane na prawidłowy kod JSON, zanim będą mogły być rzutowane.

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
Dodaj właśnie zdefiniowane umiejętności kształtu do listy umiejętności w zestawu umiejętności. 

Teraz, gdy mamy wszystkie dane, które są konieczne do projektu w tabelach, zaktualizuj obiekt knowledgeStore przy użyciu definicji tabeli. 

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

Ustaw właściwość ```storageConnectionString``` na prawidłowe parametry połączenia konta magazynu ogólnego przeznaczenia w wersji 2. W tym scenariuszu definiujemy trzy tabele w obiekcie projekcji przez ustawienie właściwości ```tableName```, ```source``` i ```generatedKeyName```. Teraz możesz zaktualizować zestawu umiejętności, wydając żądanie PUT.

### <a name="slicing"></a>Tworzenia wycinków 

Podczas rozpoczynania od skonsolidowanego kształtu, gdzie cała zawartość, która ma być rzutowana, znajduje się w pojedynczym kształcie (lub węźle wzbogacania), podział umożliwia oddzielenie pojedynczego węzła na wiele tabel lub obiektów. W tym miejscu ```pbiShape``` obiekt jest podzielony na wiele tabel. Funkcja dzielenia wycinków umożliwia ściąganie części kształtu, ```keyPhrases``` i ```Entities``` w oddzielnych tabelach. Jest to przydatne w przypadku, gdy do każdego dokumentu są skojarzone wiele jednostek i frazy kluczowe. Niejawne cięcie wycinka generuje relację między tabelami nadrzędnymi i podrzędnymi przy użyciu ```generatedKeyName``` w tabeli nadrzędnej w celu utworzenia kolumny o tej samej nazwie w tabeli podrzędnej. 

### <a name="naming-relationships"></a>Relacje nazw
Właściwości ```generatedKeyName``` i ```referenceKeyName``` służą do powiązania danych między tabelami, a nawet w ramach typów projekcji. Każdy wiersz w podrzędnej tabeli/projekcji ma właściwość wskazującą z powrotem do elementu nadrzędnego. Nazwa kolumny lub właściwości w elemencie podrzędnym jest ```referenceKeyName``` z elementu nadrzędnego. Gdy ```referenceKeyName``` nie zostanie podana, usługa domyślnie przyjmowana jest do ```generatedKeyName``` z elementu nadrzędnego. Usługi PowerBI bazują na tych generowanych kluczach w celu odnajdywania relacji w tabelach. Jeśli potrzebujesz kolumny w tabeli podrzędnej o nazwie inaczej, ustaw właściwość ```referenceKeyName``` w tabeli nadrzędnej. Przykładem może być ustawienie ```generatedKeyName``` jako identyfikatora w tabeli pbiDocument i ```referenceKeyName``` jako element DocumentID. Spowoduje to przepełnienie kolumny w tabelach pbiEntities i pbiKeyPhrases zawierających identyfikator dokumentu o nazwie element DocumentID.

Zapisz zaktualizowany zestawu umiejętności i uruchom indeksator, ale masz już projekcję działającą z trzema tabelami. Zaimportowanie tych tabel do Power BI powinno spowodować Power BI automatycznego odnajdywania relacji.

## <a name="projecting-to-objects"></a>Projekcja do obiektów

Projekcje obiektów nie mają tych samych ograniczeń co projekcje tabeli, są lepiej dopasowane do projekcji dużych dokumentów. W tym przykładzie cały dokument jest rzutowany na projekcję obiektu. Projekcje obiektów są ograniczone do pojedynczego rzutu w kontenerze.
Aby zdefiniować projekcję obiektu, będziemy używać tablicy ```objects``` w projekcjach. Można wygenerować nowy kształt przy użyciu umiejętności kształtu lub użyć wbudowanego kształtu projekcji obiektu. Chociaż przykładem tabeli przedstawiono podejście do tworzenia kształtu i rozdzielania, w tym przykładzie zademonstrowano użycie kształtu wbudowane. Kształtowanie wbudowane umożliwia tworzenie nowego kształtu w definicji wejść do projekcji. Kształtowanie wbudowane tworzy obiekt anonimowy, który jest identyczny jak podobny kształt kształtu. Kształtowanie wbudowane jest przydatne w przypadku definiowania kształtu, którego nie planujesz użyć ponownie.
Właściwość projekcje jest tablicą, w tym przykładzie dodajemy nowe wystąpienie projekcji do tablicy. Zaktualizuj definicję knowledgeStore z projekcjami zdefiniowanymi w tekście, ale nie potrzebujesz umiejętności kształtu podczas korzystania z projekcji wbudowanych.

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
## <a name="file-projections"></a>Projekcje plików

Projekcje plików to obrazy, które są wyodrębniane z dokumentu źródłowego lub dane wyjściowe wzbogacenia, które mogą być rzutowane z procesu wzbogacania. Projekcje plików, podobnie jak projekcje obiektów, są implementowane jako obiekty blob i zawierają obraz. Aby wygenerować projekcję pliku, używamy tablicy ```files``` w obiekcie projekcji. Ten przykład projektuje wszystkie obrazy wyodrębnione z dokumentu do kontenera o nazwie `samplefile`.

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

## <a name="projecting-to-multiple-types"></a>Projekcja do wielu typów

Bardziej skomplikowany scenariusz może wymagać zaprojektowania zawartości dla różnych typów projekcji. Na przykład jeśli chcesz umieścić w projekcie pewne dane, takie jak kluczowe frazy i jednostki, Zapisz wyniki OCR tekstu i układu jako obiekty i Zaprojektuj obrazy jako pliki. Ta aktualizacja zestawu umiejętności będzie:

1. Utwórz tabelę z wierszem dla każdego dokumentu.
2. Utwórz tabelę powiązaną z tabelą dokumentu z każdą frazą klucza identyfikowaną jako wiersz w tej tabeli.
3. Utwórz tabelę powiązaną z tabelą dokumentów z każdą jednostką identyfikowaną jako wiersz w tej tabeli.
4. Utwórz rzutowanie obiektu z tekstem układu dla każdego obrazu.
5. Utwórz projekcję pliku, wystając każdy wyodrębniony obraz.
6. Utwórz tabelę odsyłaczy, która zawiera odwołania do tabeli dokumentów, projekcji obiektów z tekstem układu i projekcją pliku.

Zacznij od dodania nowej umiejętności kształtu do tablicy umiejętności, która tworzy obiekt kształtowy. 

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

Projekcje obiektów wymagają nazwy kontenera dla każdego rzutu, projekcji obiektów lub projekcji plików nie mogą udostępniać kontenera. 

### <a name="relationships"></a>Relacje

W tym przykładzie przedstawiono również inną funkcję projekcji, przez zdefiniowanie wielu typów projekcji w obrębie tego samego obiektu projekcji, istnieje relacja wyrażona w obrębie i między różnymi typami (tabelami, obiektami, plikami) projekcji, umożliwiając Możesz rozpocząć od wiersza tabeli dla dokumentu i znaleźć cały tekst OCR dla obrazów znajdujących się w tym dokumencie w projekcji obiektu. Jeśli nie chcesz, aby dane były powiązane, zdefiniuj projekcje w różnych obiektach projekcji, na przykład poniższy fragment kodu spowoduje pokrewne tabele, ale nie ma relacji między tabelami i projekcjami tekstu OCR. Grupy projekcji są przydatne, gdy chcesz zaprojektować te same dane w różnych kształtach dla różnych potrzeb. Na przykład grupa projekcji dla pulpitu nawigacyjnego Power BI i innej grupy projekcji służącej do uczenia modelu AI dla umiejętności.
Podczas kompilowania projekcji różnego typu, projekcje pliku i obiektu są generowane jako pierwsze, a ścieżki są dodawane do tabel.

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

W tych przykładach przedstawiono typowe wzorce dotyczące korzystania z projekcji, dlatego warto również zapoznać się z pojęciami dotyczącymi tworzenia projekcji w konkretnym scenariuszu.

## <a name="common-issues"></a>Typowe problemy

Podczas definiowania projekcji istnieje kilka typowych problemów, które mogą spowodować nieoczekiwane wyniki.

1. Nie kształtowanie wzbogacania ciągów. Gdy ciągi są wzbogacane, na przykład ```merged_content``` wzbogacone przy użyciu kluczowych fraz, wzbogacona właściwość jest reprezentowana jako element podrzędny merged_content w drzewie wzbogacania. Ale w czasie rzutowania należy to zrobić w postaci prawidłowego obiektu JSON o nazwie i wartości.
2. Pomijanie ```/*``` na końcu ścieżki źródłowej. Jeśli na przykład, Źródło projekcji jest ```/document/pbiShape/keyPhrases``` tablica wyrażeń kluczowych jest rzutowana jako pojedynczy obiekt/wiersz. Ustawienie ścieżki źródłowej na ```/document/pbiShape/keyPhrases/*``` daje pojedynczy wiersz lub obiekt dla każdej frazy kluczowej.
3. Selektory ścieżki uwzględniają wielkość liter i mogą prowadzić do brakujących ostrzeżeń wejściowych, jeśli nie używasz dokładnej wielkości liter.

