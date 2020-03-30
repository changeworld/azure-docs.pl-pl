---
title: Definicja interfejsu dla umiejętności niestandardowych
titleSuffix: Azure Cognitive Search
description: Niestandardowy interfejs wyodrębniania danych dla umiejętności niestandardowych interfejsu api w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500266"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Jak dodać niestandardową umiejętność do potoku wzbogacania usługi Azure Cognitive Search

[Potok wzbogacania](cognitive-search-concept-intro.md) w usłudze Azure Cognitive Search można zmontować na podstawie [wbudowanych umiejętności poznawczych,](cognitive-search-predefined-skills.md) a także [umiejętności niestandardowych,](cognitive-search-custom-skill-web-api.md) które osobiście tworzysz i dodajesz do potoku. W tym artykule dowiesz się, jak utworzyć niestandardową umiejętność, która udostępnia interfejs umożliwiający uwzględnienie go w potoku wzbogacania interfejsu SI. 

Tworzenie umiejętności niestandardowych umożliwia wstawienie przekształceń unikatowych dla zawartości. Niestandardowa umiejętność jest wykonywana niezależnie, stosując dowolny krok wzbogacenia, którego potrzebujesz. Na przykład można zdefiniować encje niestandardowe specyficzne dla pola, utworzyć niestandardowe modele klasyfikacji w celu rozróżnienia kontraktów i dokumentów biznesowych i finansowych lub dodać umiejętność rozpoznawania mowy, aby dotrzeć głębiej do plików audio dla odpowiedniej zawartości. Aby zapoznać się z przykładem krok po kroku, zobacz [Przykład: Tworzenie umiejętności niestandardowych do wzbogacania si.](cognitive-search-create-custom-skill-example.md)

 Niezależnie od możliwości niestandardowych, które są wymagane, istnieje prosty i przejrzysty interfejs do łączenia umiejętności niestandardowych do reszty potoku wzbogacania. Jedynym wymogiem włączenia do [skillset](cognitive-search-defining-skillset.md) jest możliwość akceptowania danych wejściowych i emitować dane wyjściowe w sposób, który jest zużywalny w ramach skillset jako całości. W tym artykule koncentruje się na formatach danych wejściowych i wyjściowych, które wymaga potoku wzbogacania.

## <a name="web-api-custom-skill-interface"></a>Niestandardowy interfejs umiejętności interfejsu API sieci Web

Niestandardowe punkty końcowe umiejętności WebAPI domyślnie limit czasu, jeśli nie zwracają odpowiedzi w oknie 30 sekund. Potok indeksowania jest synchroniczne i indeksowania spowoduje błąd limitu czasu, jeśli odpowiedź nie zostanie odebrana w tym oknie.  Istnieje możliwość skonfigurowania limitu czasu do 230 sekund, ustawiając parametr limitu czasu:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Upewnij się, że identyfikator URI jest bezpieczny (HTTPS).

Obecnie jedynym mechanizmem interakcji z umiejętnością niestandardową jest interfejs interfejsu interfejsu API sieci Web. Potrzeby interfejsu API sieci Web muszą spełniać wymagania opisane w tej sekcji.

### <a name="1--web-api-input-format"></a>1. Format wprowadzania interfejsu API sieci Web

Interfejs API sieci Web musi akceptować tablicę rekordów do przetworzenia. Każdy rekord musi zawierać "worek właściwości", który jest dane wejściowe dostarczone do interfejsu API sieci Web. 

Załóżmy, że chcesz utworzyć prosty wzbogacacz, który identyfikuje pierwszą datę wymienioną w tekście umowy. W tym przykładzie umiejętność akceptuje pojedynczy plik wejściowy *contractText* jako tekst umowy. Umiejętność ma również jedną wydajność, która jest datą kontraktu. Aby wzbogacić bardziej interesujące, zwrócić ten *kontraktDausz* w kształcie wieloczęściowego typu złożonego.

Interfejs API sieci Web powinien być gotowy do odbierania partii rekordów wejściowych. Każdy element członkowski tablicy *wartości* reprezentuje dane wejściowe dla określonego rekordu. Każdy rekord musi mieć następujące elementy:

+ Element członkowski *recordId,* który jest unikatowym identyfikatorem określonego rekordu. Gdy wzbogacacz zwraca wyniki, musi podać ten *identyfikator rekordu,* aby umożliwić wywołującemu dopasować wyniki rekordu do ich danych wejściowych.

+ Element członkowski *danych,* który jest zasadniczo workiem pól wejściowych dla każdego rekordu.

Aby być bardziej konkretne, w powyższym przykładzie interfejs API sieci Web należy oczekiwać żądań, które wyglądają następująco:

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
W rzeczywistości usługa może zostać wywołana z setkami lub tysiącami rekordów zamiast tylko trzech pokazanych tutaj.

### <a name="2-web-api-output-format"></a>2. Format wyjściowy interfejsu API sieci Web

Format danych wyjściowych jest zestawem rekordów zawierających *recordId*i workiem właściwości 

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

W tym konkretnym przykładzie ma tylko jedno dane wyjściowe, ale można wyprowadzić więcej niż jedną właściwość. 

### <a name="errors-and-warning"></a>Błędy i ostrzeżenia

Jak pokazano w poprzednim przykładzie, można zwrócić komunikaty o błędach i ostrzeżeniach dla każdego rekordu.

## <a name="consuming-custom-skills-from-skillset"></a>Korzystanie z umiejętności niestandardowych z skillset

Podczas tworzenia wzbogacacza interfejsu API sieci Web można opisać nagłówki i parametry HTTP jako część żądania. Poniższy fragment kodu pokazuje, jak parametry żądania i *opcjonalne* nagłówki HTTP mogą być opisane jako część definicji zestawu umiejętności. Nagłówki HTTP nie są wymagane, ale umożliwiają dodanie dodatkowych funkcji konfiguracji do umiejętności i ustawienie ich z definicji zestawu umiejętności.

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

W tym artykule okryj się wymaganiami dotyczącymi interfejsu niezbędnymi do integracji umiejętności niestandardowych z zestawem umiejętności. Kliknij poniższe linki, aby dowiedzieć się więcej o umiejętnościach niestandardowych i składzie zestawu umiejętności.

+ [Power Skills: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Przykład: Tworzenie niestandardowej umiejętności wzbogacania SI](cognitive-search-create-custom-skill-example.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapować wzbogacone pola](cognitive-search-output-field-mapping.md)
