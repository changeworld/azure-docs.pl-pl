---
title: Etykieta jednostek automatycznie z jednostką listy przy użyciu Nodejs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać podmiot listy ułatwiające LUIS etykiety odmiany wyraz lub frazę.
services: cognitive-services
author: v-geberr
titleSuffix: Azure
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: e8558ecf4a64dbccef6e6367c1447bdcdb005126
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "35347136"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Użyj jednostki listy, aby zwiększyć wykrywania jednostki 
W tym samouczku przedstawiono użycie [listy jednostki](luis-concept-entity-types.md) zwiększające wykrywania jednostki. Listy jednostek nie trzeba być oznaczone jako są dokładne dopasowanie warunków.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Utwórz jednostkę listy 
* Dodawanie wartości znormalizowanych i synonimy
* Sprawdzanie poprawności identyfikacji ulepszone jednostki

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Najnowsze [Node.js](https://nodejs.org)
> * [Aplikacja LUIS HomeAutomation](luis-get-started-create-app.md). Jeśli nie masz utworzona aplikacja automatyzacji Narzędzia główne, Utwórz nową aplikację i Dodaj wbudowane domeny **HomeAutomation**. Szkolenie i publikowanie aplikacji. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (Jeśli zapytanie wielokrotnie), identyfikator aplikacji, identyfikator wersji i [region](luis-reference-regions.md) LUIS aplikacji.

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować dla [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod z tego samouczka jest dostępna w [repozytorium github przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Użycie aplikacji HomeAutomation
Umożliwia aplikacji HomeAutomation kontroli urządzeń, takich jak świateł, rozrywka systemów i środowiska formanty, takie jak ogrzewania i chłodzenia. Te systemy mieć kilka różnych nazw, które mogą obejmować nazwy, pseudonimy, akronimów i żargon producenta. 

Jeden system, który ma wiele nazw w innych kultur i demograficznych jest termostat. Termostacie może kontrolować zarówno chłodzenia i grzewczych domu lub budynku.

W idealnym przypadku następujące zniesławiających powinien być rozpoznawany wbudowane jednostki **HomeAutomation.Device**:

|#|utterance|podmiot|wynik|
|--|--|--|--|
|1|Włącz ac|HomeAutomation.Device — "ac"|0.8748562|
|2|Włącz zapasowej cieplna|HomeAutomation.Device — "cieplna"|0.784990132|
|3|Nadaj chłodnej|||

Pierwsze dwa zniesławiających są mapowane na różnych urządzeniach. Trzeci utterance, "była chłodnej", nie mapują na urządzeniu, ale zamiast tego żądania wynik. LUIS nie może ustalić, czy termin "chłodnej" oznacza, że termostat żądanego urządzenia. W idealnym przypadku LUIS powinna być rozpoznawana wszystkie te zniesławiających do tego samego urządzenia. 

## <a name="use-a-list-entity"></a>Użyj jednostki listy
Jednostka HomeAutomation.Device jest doskonały dla małą liczbą urządzeń lub kilka zmian nazw. Budynek biurowy lub firmy nazwy urządzenia rosnąć poza przydatność HomeAutomation.Device jednostki. 

A **listy jednostki** jest dobrym rozwiązaniem w przypadku tego scenariusza, ponieważ zestaw warunków dla urządzenia w budynku lub firmy jest znanego zestawu, nawet jeśli jest ona dużego zestawu. Przy użyciu jednostek listy, LUIS można odbierać wszystkie możliwe wartości zestawu termostat i rozwiąż go do tylko jednego urządzenia "termostat". 

W tym samouczku będzie utworzyć listę jednostki z termostat. Alternatywne nazwy dla termostat w tym samouczku są: 

|alternatywne nazwy dla termostat|
|--|
| ac |
| konta|
| -c|
|grzejnik|
|dynamicznej|
|hotter|
|chłodni|
|chłodnej|

Jeśli trzeba określić alternatywne nowe często LUIS, a następnie [listy frazy](luis-concept-feature.md#how-to-use-phrase-lists) jest lepsze odpowiedzi.

## <a name="create-a-list-entity"></a>Utwórz jednostkę listy
Utwórz plik Node.js i skopiuj następujący kod do niego. Zmień wartości authoringKey, appId versionId i regionu.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Aby zainstalować zależności NPM i uruchomić kod w celu utworzenia jednostki listy, użyj następującego polecenia:

```Javascript
npm install && node add-entity-list.js
```

Wynik uruchomienia jest identyfikator jednostki listy:

```Javascript
026e92b3-4834-484f-8608-6114a83b03a6
```
## <a name="train-the-model"></a>Uczenie modelu
Szkolenie LUIS Aby nową listę, aby mieć wpływ na wyniki zapytania. Szkolenie jest procesem dwuczęściową szkolenia sprawdzanie stanu, jeśli odbywa się szkolenia. Aplikację z wieloma modelami może potrwać pewien czas w celu przeszkolenia. Poniższy kod przygotowuje aplikacji, a następnie czeka, aż szkolenia zakończy się pomyślnie. Kod używa strategii oczekiwania i ponów, aby uniknąć 429 "jest zbyt wiele żądań" błąd. 

Utwórz plik Node.js i skopiuj następujący kod do niego. Zmień wartości authoringKey, appId versionId i regionu.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Użyj następującego polecenia do uruchomienia kodu w celu przeszkolenia aplikacji:

```Javascript
node train.js
```

Dane wyjściowe Uruchom jest stan każdej iteracji modeli LUIS szkolenia. Wykonanie poniższych wymagany tylko jeden wyboru szkolenia:

```Javascript
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
Publikowanie, jednostka listy jest dostępna z punktu końcowego.

Utwórz plik Node.js i skopiuj następujący kod do niego. Zmień wartości endpointKey, appId i region. AuthoringKey Twojego można użyć, jeśli nie zamierzasz wywołać tego pliku poza limit przydziału.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Użyj następującego polecenia do uruchomienia kodu do aplikacji zapytania:

```Javascript
node publish.js
```

Następujące dane wyjściowe zawiera adres url punktu końcowego dla kwerendy. Rzeczywiste wyniki JSON obejmuje appID prawdziwe. 

```JSON
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
Wyślij zapytanie do aplikacji z punktu końcowego w celu potwierdzenia, czy jednostka listy pomaga LUIS ustalić typu urządzenia.

Utwórz plik Node.js i skopiuj następujący kod do niego. Zmień wartości endpointKey, appId i region. AuthoringKey Twojego można użyć, jeśli nie zamierzasz wywołać tego pliku poza limit przydziału.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Aby uruchomić kod i zapytania aplikacji, użyj następującego polecenia:

```Javascript
node train.js
```

Dane wyjściowe są wyniki zapytania. Ponieważ kod dodane **pełne** pary nazwa/wartość na ciąg zapytania, dane wyjściowe obejmują wszystkie intencje i wyniki:

```JSON
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

Urządzenie z **termostat** o zorientowane na wynik zapytania "Włącz zapasowej cieplna". Ponieważ oryginalna jednostka HomeAutomation.Device jest nadal w aplikacji, spowoduje także jego wyniki. 

Spróbuj dwóch zniesławiających aby zobaczyć, również są zwracane jako termostacie. 

|#|utterance|jednostka|type|wartość|
|--|--|--|--|--|
|1|Włącz ac| ac | DevicesList | Termostat|
|2|Włącz zapasowej cieplna|cieplna| DevicesList |Termostat|
|3|Nadaj chłodnej|chłodnej|DevicesList|Termostat|

## <a name="next-steps"></a>Kolejne kroki

Można utworzyć inną jednostkę listy, aby rozwinąć lokalizacji urządzenia do pomieszczenia, dolnych limitów lub budynków. 
