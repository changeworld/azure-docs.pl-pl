---
title: Definicja interfejsu dla umiejętności niestandardowe w usłudze wyszukiwania poznawczego — usługa Azure Search
description: Interfejs wyodrębniania danych niestandardowych umiejętności niestandardowego interfejsu api sieci web w usłudze wyszukiwania poznawczego potoku w usłudze Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e181aab3d92d8111a0a7d1d41bbddac20687a547
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668862"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Jak dodać umiejętności niestandardowe do potoku wyszukiwania kognitywnego

A [potokiem indeksowania wyszukiwania kognitywnego](cognitive-search-concept-intro.md) w usłudze Azure Search może odbywać się z [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) także [umiejętności niestandardowe](cognitive-search-custom-skill-web-api.md) , umożliwiających tworzenie i dodawanie do potok. Ten artykuł zawiera informacje dotyczące tworzenia niestandardowych umiejętności, która udostępnia interfejs, co pozwala na uwzględnienie w potoku usłudze wyszukiwania poznawczego. 

Budowanie umiejętności niestandardowe daje możliwość wstawiania przekształcenia unikatowe dla zawartości. Umiejętności niestandardowe wykonuje niezależnie, niezależnie od kroku wzbogacania potrzebujesz. Na przykład można zdefiniować jednostki niestandardowe specyficzne dla pola, tworzyć modele klasyfikacji niestandardowej do odróżnienia biznesowych i kontrakty finansowe i dokumenty lub Dodaj umiejętności rozpoznawania mowy, osiągnąć bardziej do plików audio, dla odpowiedniej zawartości. Aby uzyskać przykład krok po kroku, zobacz [przykładu: Tworzenie niestandardowych umiejętności do wyszukiwania kognitywnego](cognitive-search-create-custom-skill-example.md).

 Niezależnie od możliwości niestandardowych, potrzebujesz, jest prosty i przejrzysty interfejs do łączenia z umiejętności niestandardowe w pozostałej części wzbogacony potok. Jedynym wymaganiem do włączenia [zestawu umiejętności](cognitive-search-defining-skillset.md) jest możliwość akceptują dane wejściowe i Emituj danych wyjściowych w sposób, który jest w użyciu w ramach zestawu umiejętności jako całości. Ten artykuł koncentruje się na formatów wejściowych i wyjściowych, który wymaga wzbogacony potok.

## <a name="web-api-custom-skill-interface"></a>Interfejs umiejętności niestandardowego interfejsu API sieci Web

Niestandardowe WebAPI umiejętności punkty końcowe przez domyślny limit czasu, jeśli nie zwracają odpowiedzi w ciągu 30 drugie okno. Potokiem indeksowania jest synchroniczne, a indeksowanie powoduje wygenerowanie błąd upływu limitu czasu, jeśli odpowiedź nie została odebrana w tym oknie.  Istnieje możliwość Konfigurowanie limitu czasu na maksymalnie 90 sekund, ustawiając parametr limitu czasu:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

Obecnie tylko mechanizm do interakcji z umiejętności niestandardowe jest za pomocą interfejsu API sieci Web. Wymaga interfejsu API sieci Web musi spełniać wymagania opisane w tej sekcji.

### <a name="1--web-api-input-format"></a>1.  Format danych wejściowych interfejsu API sieci Web

Internetowy interfejs API, musisz zaakceptować tablicą rekordów do przetworzenia. Każdy rekord może zawierać zbiór"właściwości" oznacza to wejściowych do interfejsu API sieci Web. 

Załóżmy, że chcesz utworzyć prostą enricher, który identyfikuje pierwszą datę wymienione w tekście kontrakt. W tym przykładzie umiejętności akceptuje pojedynczy dane wejściowe *contractText* jako tekst kontraktu. Umiejętności ma również pojedynczego wyjścia jest data zamówienia. Aby enricher bardziej interesujące, zwracają *contractDate* w kształcie wieloczęściowy typu złożonego.

Interfejs API sieci Web powinna być gotowa do odbierania partii rekordów danych wejściowych. Każdy element członkowski *wartości* tablicy reprezentuje dane wejściowe dla określonego rekordu. Każdy rekord musi mieć następujące elementy:

+ A *recordId* składowej, która jest unikatowym identyfikatorem dla określonego rekordu. Gdy Twoje enricher zwraca wyniki, przekazuje ten *recordId* parzysta obiekt wywołujący, aby dopasować wyniki rekordu, aby ich dane wejściowe.

+ A *danych* elementu członkowskiego, która jest zasadniczo zbiór pól wejściowych dla każdego rekordu.

Bardziej konkretnych, na przykład powyżej, internetowy interfejs API należy się spodziewać żądań, które wyglądają następująco:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
W rzeczywistości usługi może zostać wywołana z setek lub tysięcy rekordów zamiast trzech, pokazano poniżej.

### <a name="2-web-api-output-format"></a>2. Format wyjściowy interfejsu API sieci Web

Format danych wyjściowych to zbiór rekordów zawierających *recordId*i zbioru właściwości 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Tym konkretnym przykładzie ma tylko jedno wyjście, ale można danych wyjściowych więcej niż jednej właściwości. 

### <a name="errors-and-warning"></a>Błędy i ostrzeżenia

Jak pokazano w poprzednim przykładzie, może zwracać błędach i komunikaty ostrzegawcze dla każdego rekordu.

## <a name="consuming-custom-skills-from-skillset"></a>Korzystanie z niestandardowych umiejętności od zestawu umiejętności

Po utworzeniu enricher interfejsu API sieci Web, wystarczy opisać nagłówków HTTP i parametrów, jako część żądania. Poniższy fragment kodu pokazuje, jak parametry żądania i nagłówki HTTP może być oznaczony jako część definicji zestawu umiejętności.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

+ [Przykład: Tworzenie niestandardowych umiejętności do wyszukiwania kognitywnego](cognitive-search-create-custom-skill-example.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
