---
title: Encje do dopasowania tekstu extact — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodać encję listy, aby pomóc w oznaczaniu odmian wyrazu lub frazy w usłudze LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499478"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Zwiększenie wykrywania encji za pomocą encji listy 
W tym artykule przedstawiono użycie [jednostki listy](luis-concept-entity-types.md) w celu zwiększenia wykrywania jednostek. Jednostki listy nie muszą być oznaczone etykietą, ponieważ są dokładnym dopasowaniem terminów.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie encji listy 
> * Dodawanie znormalizowanych wartości i synonimów
> * Sprawdzanie poprawności ulepszonej identyfikacji jednostki

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Najnowsze [node.js](https://nodejs.org)
> * [HomeAutomation aplikacja USŁUGI LUIS](luis-get-started-create-app.md). Jeśli nie masz utworzonej aplikacji Automatyka domowa, utwórz nową aplikację i dodaj wstępnie utworzone **funkcje HomeAutomation**domeny . Przeszkol i opublikuj aplikację. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (jeśli kwerendy wiele razy), identyfikator aplikacji, identyfikator wersji i [region](luis-reference-regions.md) dla aplikacji usługi LUIS.

> [!Tip]
> Jeśli nie masz jeszcze subskrypcji, możesz zarejestrować się na [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod w tym artykule jest dostępny w [repozytorium GitHub z przykładami platformy Azure.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity) 

## <a name="use-homeautomation-app"></a>Korzystanie z aplikacji HomeAutomation
Aplikacja HomeAutomation zapewnia kontrolę nad urządzeniami, takimi jak światła, systemy rozrywki i sterowanie środowiskiem, takie jak ogrzewanie i chłodzenie. Systemy te mają kilka różnych nazw, które mogą zawierać nazwy producentów, pseudonimy, akronimy i slang. 

Jednym z systemów, który ma wiele nazw w różnych kulturach i demografii jest termostat. Termostat może sterować zarówno systemami chłodzenia, jak i ogrzewania domu lub budynku.

Najlepiej następujące wypowiedzi należy rozwiązać prekompilowana encji **HomeAutomation.Device:**

|#|Wypowiedzi|podmiot zidentyfikowany|wynik|
|--|--|--|--|
|1|włączyć ac|Strona głównaAutomation.Device - "ac"|0.8748562|
|2|podkręcić ciepło|Strona głównaAutomation.Device - "ciepło"|0.784990132|
|3|sprawiają, że zimniej|||

Pierwsze dwa wypowiedzi mapują się na różne urządzenia. Trzecia wypowiedź, "make it colder", nie mapuje do urządzenia, ale zamiast tego żąda wyniku. Usługa LUIS nie wie, że termin "zimniej" oznacza, że termostat jest żądanym urządzeniem. W idealnym przypadku usługa LUIS powinna rozwiązać wszystkie te wypowiedzi na tym samym urządzeniu. 

## <a name="use-a-list-entity"></a>Używanie encji listy
Jednostka HomeAutomation.Device doskonale nadaje się dla niewielkiej liczby urządzeń lub z kilkoma odmianami nazw. W przypadku budynku biurowego lub kampusu nazwy urządzeń wykraczają poza przydatność jednostki HomeAutomation.Device. 

**Jednostka listy** jest dobrym wyborem dla tego scenariusza, ponieważ zestaw warunków dla urządzenia w budynku lub kampusie jest znany zestaw, nawet jeśli jest to ogromny zestaw. Za pomocą jednostki listy, usługa LUIS może odbierać dowolną możliwą wartość w zestawie dla termostatu i rozwiązać go tylko do jednego urządzenia "termostat". 

W tym artykule utworzymy listę jednostek z termostatem. Alternatywne nazwy termostatu w tym artykule to: 

|alternatywne nazwy termostatu|
|--|
| Ac |
| Klimatyzacji|
| a-c|
|Nagrzewnica|
|klawisz|
|Gorętsze|
|Zimno|
|Chłodniejsze|

Jeśli usługa LUIS musi często określać nową alternatywę, lepszym rozwiązaniem jest [lista fraz.](luis-concept-feature.md#how-to-use-phrase-lists)

## <a name="create-a-list-entity"></a>Tworzenie encji listy
Utwórz plik Node.js i skopiuj do niego następujący kod. Zmień wartości authoringKey, appId, versionId i regionu.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Użyj następującego polecenia, aby zainstalować zależności NPM i uruchomić kod, aby utworzyć encję listy:

```console
npm install && node add-entity-list.js
```

Dane wyjściowe przebiegu to identyfikator jednostki listy:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Uczenie modelu
Szkolenie usługi LUIS w celu nowej listy, aby wpłynąć na wyniki kwerendy. Szkolenie jest dwuczęściowym procesem szkolenia, a następnie sprawdzanie statusu, jeśli szkolenie jest wykonywane. Trening z wieloma modelami może potrwać kilka chwil. Poniższy kod trenuje aplikację, a następnie czeka, aż szkolenie zakończy się pomyślnie. Kod używa strategii wait-and-retry, aby uniknąć błędu 429 "Zbyt wiele żądań". 

Utwórz plik Node.js i skopiuj do niego następujący kod. Zmień wartości authoringKey, appId, versionId i regionu.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Użyj następującego polecenia, aby uruchomić kod, aby wyszkolić aplikację:

```console
node train.js
```

Dane wyjściowe przebiegu jest stan każdej iteracji szkolenia modeli usługi LUIS. Następujące wykonanie wymagało tylko jednego sprawdzenia szkolenia:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publikowanie modelu
Opublikuj więc encja listy jest dostępna z punktu końcowego.

Utwórz plik Node.js i skopiuj do niego następujący kod. Zmień wartości klucza końcowego, identyfikatora aplikacji i regionu. Można użyć authoringKey, jeśli nie planujesz wywołać tego pliku poza limit przydziału.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Użyj następującego polecenia, aby uruchomić kod, aby zbadać aplikację:

```console
node publish.js
```

Następujące dane wyjściowe zawiera adres URL punktu końcowego dla wszystkich zapytań. Prawdziwe wyniki JSON będzie zawierać prawdziwe appID. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Zapytanie do aplikacji 
Kwerenda aplikacji z punktu końcowego, aby udowodnić, że jednostka listy pomaga usługi LUIS określić typ urządzenia.

Utwórz plik Node.js i skopiuj do niego następujący kod. Zmień wartości klucza końcowego, identyfikatora aplikacji i regionu. Można użyć authoringKey, jeśli nie planujesz wywołać tego pliku poza limit przydziału.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Użyj następującego polecenia, aby uruchomić kod i zbadać aplikację:

```console
node train.js
```

Dane wyjściowe są wynikami kwerendy. Ponieważ kod dodał pełne pary nazwa/wartość do ciągu **kwerendy,** dane wyjściowe obejmują wszystkie intencje i ich wyniki:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Konkretne urządzenie **termostatu** jest identyfikowane za pomocą zorientowanego na wynik zapytania "podkręć ciepło". Ponieważ oryginalna jednostka HomeAutomation.Device jest nadal w aplikacji, można zobaczyć jej wyniki, jak również. 

Spróbuj innych dwóch wypowiedzi, aby zobaczyć, że są one również zwracane jako termostat. 

|#|Wypowiedzi|jednostka|type|value|
|--|--|--|--|--|
|1|włączyć ac| Ac | Lista urządzeń | Termostat|
|2|podkręcić ciepło|Ciepła| Lista urządzeń |Termostat|
|3|sprawiają, że zimniej|Chłodniejsze|Lista urządzeń|Termostat|

## <a name="next-steps"></a>Następne kroki

Można utworzyć inną encję Lista, aby rozszerzyć lokalizacje urządzeń na pomieszczenia, piętra lub budynki. 
