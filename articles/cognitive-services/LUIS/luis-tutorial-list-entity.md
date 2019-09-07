---
title: Extact obiektów dopasowania tekstu — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodać jednostkę listy, ułatwiające odmiany etykieta usługi LUIS wyrazu lub frazy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: a722ce39a679fa13e1fe849c46b44f786ea5ee42
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390268"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Użyj jednostki listy w celu zwiększenia wykrywania jednostki 
W tym artykule przedstawiono sposób użycia [jednostki listy](luis-concept-entity-types.md) do zwiększenia wykrywania jednostek. Lista jednostek jest konieczne etykietą, ponieważ są one dokładne dopasowanie warunków.  

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie jednostki listy 
> * Dodaj znormalizowane wartości i synonimy
> * Sprawdź poprawność identyfikacji ulepszone jednostki

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Najnowsze [środowiska Node.js](https://nodejs.org)
> * [Aplikacją usługi LUIS HomeAutomation](luis-get-started-create-app.md). Jeśli nie masz aplikacji Home automatyzacji utworzone, Utwórz nową aplikację i dodać domenę wstępnie **HomeAutomation**. Przeszkol i opublikuj aplikację. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (jeśli jest wykonywane zapytanie wiele razy), identyfikator aplikacji, identyfikator wersji i [region](luis-reference-regions.md) dla aplikacji usługi LUIS.

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod w tym artykule jest dostępny w [repozytorium Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity)w witrynie GitHub. 

## <a name="use-homeautomation-app"></a>Użyj aplikacji HomeAutomation
Daje aplikacji HomeAutomation kontroli urządzeń, takich jak światła, rozrywka systemów i środowisko kontroluje takich jak ogrzewania i chłodzenie. Te systemy mają kilka różnych nazw, które mogą obejmować nazwy, pseudonimy, akronimów i żargonu producenta. 

Co system, który ma wiele nazw w różnych kultur i danymi demograficznymi to termostat. Termostat można kontrolować systemu domu lub tworzenie ogrzewania i chłodzenie.

Najlepiej następujące wypowiedzi powinna być rozwiązywana na wstępnie utworzone jednostki **HomeAutomation.Device**:

|#|Wypowiedź|jednostki identyfikowanej|wynik|
|--|--|--|--|
|1|Włącz ac|HomeAutomation.Device — "ac"|0.8748562|
|2|wskazywać ciepła|HomeAutomation.Device — "cieplnej"|0.784990132|
|3|ułatwiają chłodnej|||

Pierwsze dwa wypowiedzi są mapowane na różnych urządzeniach. Trzeci wypowiedź, "ułatwiają chłodnej", nie jest mapowany na urządzeniu, ale zamiast tego żądania wynik. Usługa LUIS nie wie, że termin "chłodnej" oznacza, że termostat jest żądane urządzenie. W idealnym przypadku LUIS powinna być rozpoznawana wszystkie te wypowiedzi na tym samym urządzeniu. 

## <a name="use-a-list-entity"></a>Użyj jednostki listy
Jednostka HomeAutomation.Device to doskonałe rozwiązanie dla małych lub liczby urządzeń za pomocą niewielkie zmiany nazw. Budynek biurowy lub campus nazwy urządzenia rosnąć poza przydatność jednostki HomeAutomation.Device. 

A **listy jednostek** jest dobrym wyborem dla tego scenariusza, ponieważ zestaw warunków dla urządzenia w budynku lub campus jest znanego zestawu, nawet jeśli jest to duży zestaw. Korzystając z obiektami listy, LUIS można odbierać dowolnej możliwej wartości w zestawie termostat i rozwiązać ten problem w dół do tylko jednego urządzenia "termostat". 

W tym artykule opisano tworzenie listy jednostek z termostatem. Alternatywne nazwy dla termostatu w tym artykule są następujące: 

|alternatywne nazwy dla termostat|
|--|
| ac |
| konta|
| -c|
|heater|
|gorąca|
|hotter|
|zimno|
|chłodnej|

Jeśli usługa LUIS musi określić nowe alternatywna często, a następnie [listy fraz](luis-concept-feature.md#how-to-use-phrase-lists) jest lepsza odpowiedź.

## <a name="create-a-list-entity"></a>Tworzenie jednostki listy
Utwórz plik w technologii Node.js i skopiuj następujący kod do niego. Zmień wartości authoringKey "," appId "," versionId "i" region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Aby zainstalować zależności rozwiązania NPM i uruchomić kod, aby utworzyć jednostkę listy, użyj następującego polecenia:

```console
npm install && node add-entity-list.js
```

Dane wyjściowe przebiegu jest identyfikator obiektu listy:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Uczenie modelu
Szkolenie usługi LUIS w kolejności, aby uzyskać nową listę, aby mieć wpływ na wyniki zapytania. Szkolenie jest proces dwuetapowy, szkolenia, jeżeli odbywa się szkolenie Trwa sprawdzanie stanu. Aplikację z wieloma modelami może potrwać kilka chwil do nauczenia. Poniższy kod szkolenie modeli aplikacji, a następnie czeka, aż szkolenia zakończy się pomyślnie. Kod używa strategię oczekiwania i ponawiania, aby uniknąć 429 "zbyt wiele żądań" błąd. 

Utwórz plik w technologii Node.js i skopiuj następujący kod do niego. Zmień wartości authoringKey "," appId "," versionId "i" region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Aby uruchomić kod to w opracowywaniu aplikacji, użyj następującego polecenia:

```console
node train.js
```

Dane wyjściowe przebiegu jest jego stan każdej iteracji szkolenie modeli usługi LUIS. Wykonanie następujących wymagany tylko jeden wyboru szkolenia:

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
Publikowanie, więc jednostka lista jest dostępna z punktu końcowego.

Utwórz plik w technologii Node.js i skopiuj następujący kod do niego. Zmień wartości endpointKey, appId i region. Jeśli nie planujesz wywołanie tego pliku poza limit przydziału, możesz użyć swojej authoringKey.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Aby uruchomić kod do wykonywania zapytań w aplikacji, użyj następującego polecenia:

```console
node publish.js
```

Następujące dane wyjściowe obejmują adres url punktu końcowego dla dowolnego zapytania. Rzeczywiste wyniki JSON obejmuje appID rzeczywistych. 

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

## <a name="query-the-app"></a>Zapytanie aplikacji 
Wyślij zapytanie do aplikacji z punktu końcowego, aby potwierdzić, czy obiektami listy pomaga LUIS określić typ urządzenia.

Utwórz plik w technologii Node.js i skopiuj następujący kod do niego. Zmień wartości endpointKey, appId i region. Jeśli nie planujesz wywołanie tego pliku poza limit przydziału, możesz użyć swojej authoringKey.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Użyj następującego polecenia do uruchomienia kodu i wykonywania zapytań względem aplikacji:

```console
node train.js
```

Dane wyjściowe są wyniki zapytania. Ponieważ kod dodany **pełne** pary nazwa/wartość do ciągu zapytania, dane wyjściowe obejmują wszystkie intencje i ich wyniki:

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

Urządzenie z **termostat** jest identyfikowany za pomocą zorientowane na wynik zapytania "turn się ciepło". Ponieważ oryginalna jednostka HomeAutomation.Device jest nadal w aplikacji, zostaną wyświetlone wyniki także. 

Spróbuj dwóch wypowiedzi będzie również są zwracane jako termostat. 

|#|Wypowiedź|jednostka|type|wartość|
|--|--|--|--|--|
|1|Włącz ac| ac | DevicesList | Termostat|
|2|wskazywać ciepła|ciepła| DevicesList |Termostat|
|3|ułatwiają chłodnej|chłodnej|DevicesList|Termostat|

## <a name="next-steps"></a>Kolejne kroki

Można utworzyć innej jednostki listy, aby rozwinąć lokalizacji urządzenia do pomieszczenia, piętrach lub budynki. 
