---
title: Interfejs definicji dla niestandardowych umiejętności w potoku kognitywnych wyszukiwania (Azure Search) | Dokumentacja firmy Microsoft
description: Interfejs wyodrębniania danych niestandardowych umiejętności niestandardowego interfejsu api sieci web w potoku kognitywnych wyszukiwania w usłudze Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f415eb6080a02d25fc47c40b2719544d2ea99c5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Jak dodać niestandardowe umiejętności do potoku kognitywnych wyszukiwania

W tym artykule Dowiedz się jak dodać niestandardowe umiejętności do potoku kognitywnych wyszukiwania. A [potoku indeksowania wyszukiwania kognitywnych](cognitive-search-concept-intro.md) w usłudze Azure Search może odbywać się z [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) i umiejętności niestandardowych, które umożliwiających tworzenie i dodawanie do potoku.

Tworzenie niestandardowych umiejętności umożliwia do wstawienia przekształcenia unikatowy do zawartości. Niestandardowe umiejętności wykonuje niezależnie, niezależnie od kroku wzbogacenia wymagane stosowanie. Na przykład można zdefiniować specyficzne dla pól niestandardowych obiektów, utworzyć modele klasyfikacji niestandardowej do odróżnienia działalności biznesowej i kontrakty finansowe i dokumenty lub Dodawanie umiejętności rozpoznawania mowy, aby uzyskać dostęp do plików dźwiękowych dla odpowiedniej zawartości głębiej. Na przykład krok po kroku, zobacz [przykład: Tworzenie niestandardowego umiejętności](cognitive-search-create-custom-skill-example.md).

 Niezależnie od możliwości niestandardowych potrzebujesz, jest proste i wyczyść interfejs w celu nawiązania niestandardowych umiejętności pozostałego potoku wzbogacenia. Jedynym wymaganiem włączenia [skillset](cognitive-search-defining-skillset.md) jest możliwość akceptuje dane wejściowe i Emituj danych wyjściowych w sposób, w którym są dostępne w ramach skillset jako całość. Ten artykuł koncentruje się na formatów wejściowych i wyjściowych, które wymaga potoku wzbogacenia.

## <a name="web-api-custom-skill-interface"></a>Interfejs umiejętności niestandardowego interfejsu API sieci Web

Za pomocą interfejsu API sieci Web jest obecnie tylko mechanizm interakcji z niestandardowych umiejętności. Interfejs API sieci Web wymaga musi spełniać wymagania opisane w tej sekcji.

### <a name="1--web-api-input-format"></a>1.  Format danych wejściowych interfejsu API sieci Web

Interfejs API sieci Web, musisz zaakceptować tabeli rekordów do przetworzenia. Każdy rekord musi zawierać zbiór"właściwości" oznacza to danych wejściowych dostarczonych do interfejsu API sieci Web. 

Załóżmy, że chcesz utworzyć prosty enricher, określające pierwszą datę wymienionych w tekście kontrakt. W tym przykładzie umiejętności akceptuje pojedynczy dane wejściowe *contractText* jako tekst kontraktu. Umiejętności ma również pojedynczego wyjścia, który jest data zamówienia. Aby enricher bardziej interesujące, zwróć *contractDate* w kształcie wieloczęściowych typu złożonego.

Interfejs API sieci Web powinna być gotowa do odbioru partii rekordów wejściowego. Każdy element członkowski *wartości* tablicy reprezentuje dane wejściowe dla określonego rekordu. Każdy rekord musi mieć następujące elementy:

+ A *recordId* elementu członkowskiego, który jest unikatowym identyfikatorem dla określonego rekordu. Gdy Twoje enricher zwraca wyniki, musi ona to *recordId* w celu umożliwienia wywołującego odpowiadające wyniki rekordu w danych wejściowych.

+ A *danych* elementu członkowskiego, który jest zasadniczo zbiór pól wejściowych dla każdego rekordu.

Bardziej konkretnych na powyższym przykładzie interfejsu API sieci Web oczekiwać żądań, które wyglądają następująco:

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
W rzeczywistości usługi może pobrać wywołana z setkami lub tysiącami rekordów zamiast tylko trzech.

### <a name="2-web-api-output-format"></a>2. Format danych wyjściowych interfejsu API sieci Web

Format danych wyjściowych jest zestaw rekordów zawierający *recordId*i zbioru właściwości 

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

W tym przykładzie określonego ma tylko jedno wyjście, ale można output więcej niż jedną właściwość. 

### <a name="errors-and-warning"></a>Błędy i ostrzeżenia

Jak pokazano w poprzednim przykładzie, mogą zwracać błędach i komunikaty ostrzegawcze dla każdego rekordu.

## <a name="consuming-custom-skills-from-skillset"></a>Korzystanie z niestandardowych umiejętności skillset

Podczas tworzenia enricher interfejsu API sieci Web, można opisać nagłówków HTTP i parametrów jako część żądania. Poniższy fragment pokazuje, jak parametry żądania i nagłówków HTTP może być oznaczony jako część definicji skillset.

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

## <a name="next-steps"></a>Kolejne kroki

+ [Przykład: Tworzenie niestandardowego umiejętności dla interfejsu API tłumaczenie tekstu](cognitive-search-create-custom-skill-example.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Utwórz Skillset (REST)](ref-create-skillset.md)
+ [Sposób mapowania pól wzbogaconego](cognitive-search-output-field-mapping.md)
