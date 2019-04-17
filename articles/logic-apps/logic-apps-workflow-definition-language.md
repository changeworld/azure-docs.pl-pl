---
title: Odwołanie do schematu dla język definicji przepływów pracy — Azure Logic Apps
description: Przewodnik dla schematu język definicji przepływów pracy w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 04/30/2018
ms.openlocfilehash: d80ffa862546f56e93a338a7a1db031e2cb55990
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616805"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Odwołanie do schematu dla język definicji przepływów pracy w usłudze Azure Logic Apps

Po utworzeniu aplikacji logiki w [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), aplikacja logiki ma podstawową definicję przepływu pracy, opisujący rzeczywiste logikę, która jest uruchamiany w aplikacji logiki. Korzysta z tej definicji przepływu pracy [JSON](https://www.json.org/) i następuje struktury, którego poprawność jest sprawdzana przez schemat języka definicji przepływu pracy. Ta dokumentacja zawiera omówienie tej struktury i jak schemat definiuje elementy w definicji przepływu pracy.

## <a name="workflow-definition-structure"></a>Struktura definicji przepływu pracy

Definicja przepływu pracy, który jest zawsze zawiera wyzwalacz dla wystąpienia aplikacji logiki, a także co najmniej jednej akcji, które są uruchamiane po aktywowaniu wyzwalacza.

Poniżej przedstawiono ogólną strukturę dla definicji przepływu pracy:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| Element | Wymagany | Opis |
|---------|----------|-------------|
| definicja | Yes | Element początkowy dla swojej definicji przepływu pracy |
| $schema | Tylko wtedy, gdy zewnętrznie odwołujące się do definicji przepływu pracy | Lokalizacja pliku schematu JSON, który opisuje wersję język definicji przepływów pracy, który można znaleźć tutaj: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| contentversion — | Nie | Numer wersji dla definicji przepływu pracy, czyli "1.0.0.0" domyślnie. Aby ułatwić identyfikowanie i Potwierdź poprawną definicję, wdrażając przepływu pracy, należy określić wartość do użycia. |
| parameters | Nie | Definicje dla jednego lub więcej parametrów, które przekazują dane do Twojego przepływu pracy <p><p>Maksymalna parametry: 50 |
| wyzwalacze | Nie | Definicje dla co najmniej jeden wyzwalacze, które wystąpienia przepływu pracy. Można zdefiniować więcej niż jeden wyzwalacz, ale tylko za pomocą języka definicji przepływu pracy, nie wizualnie za pomocą projektanta aplikacji logiki. <p><p>Maksymalna wyzwalaczy: 10 |
| Akcje | Nie | Definicje dla co najmniej jedną akcję do wykonania w czasie wykonywania przepływu pracy <p><p>Maksymalną liczbę akcji: 250 |
| wyjścia | Nie | Definicje dla danych wyjściowych, które zwracają z przebiegu przepływu pracy <p><p>Maksymalna dane wyjściowe: 10 |
||||

## <a name="parameters"></a>Parametry

W `parameters` sekcji, określ wszystkie parametry przepływu pracy, które swojej definicji przepływu pracy używa we wdrożeniu akceptuje dane wejściowe. Deklaracji parametru i wartości parametrów są wymagane podczas wdrażania. Przed za pomocą tych parametrów w innych częściach przepływu pracy, upewnij się, że zadeklarować wszystkie parametry w tych sekcjach. 

Poniżej przedstawiono ogólną strukturę dla definicji parametru:

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Element | Wymagane | Typ | Opis |
|---------|----------|------|-------------|
| type | Yes | int, float, string, securestring, bool, tablicę, obiekt JSON, secureobject <p><p>**Uwaga**: W przypadku wszystkich haseł, kluczy i wpisów tajnych, użyj `securestring` i `secureobject` typów, ponieważ `GET` operacji nie zwraca tych typów. Aby uzyskać więcej informacji na temat zabezpieczania parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | Typ parametru |
| defaultValue | Yes | Takie same jak `type` | Domyślna wartość parametru, jeśli wartość nie zostanie określona, gdy tworzy wystąpienie przepływu pracy |
| allowedValues | Nie | Takie same jak `type` | Tablica wartości akceptujące parametr |
| metadane | Nie | Obiekt JSON | Inne szczegóły parametrów, na przykład nazwę lub czytelny opis dla aplikacji logiki lub przepływie lub danych czasu projektowania, używane przez program Visual Studio lub innych narzędzi |
||||

## <a name="triggers-and-actions"></a>Wyzwalacze i akcje

W definicji przepływu pracy `triggers` i `actions` sekcje definiują wywołania, które występują podczas wykonywania Twój przepływ pracy. Informacje o składni i dowiedzieć się więcej o tych sekcji, zobacz [wyzwalaczy przepływu pracy i działań](../logic-apps/logic-apps-workflow-actions-triggers.md).

## <a name="outputs"></a>Dane wyjściowe

W `outputs` sekcji, definiują dane, które może zwracać przepływu pracy, po zakończeniu uruchamiania. Na przykład aby śledzić stan określonego lub wartości z poszczególnymi uruchomieniami, określić czy dane wyjściowe przepływu pracy zwraca dane.

> [!NOTE]
> Podczas odpowiadania na żądania przychodzące z interfejsu API REST usługi, nie używaj `outputs`. Zamiast tego należy użyć `Response` typ akcji. Aby uzyskać więcej informacji, zobacz [wyzwalaczy przepływu pracy i działań](../logic-apps/logic-apps-workflow-actions-triggers.md).

Poniżej przedstawiono ogólną strukturę definicji danych wyjściowych dla:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Element | Wymagane | Typ | Opis |
|---------|----------|------|-------------|
| <*key-name*> | Yes | String | Nazwa klucza dla produktu wyjściowego zwracają wartość |
| type | Yes | int, float, string, securestring, bool, tablicę, obiekt JSON | Typ dla wartości zwracanej w danych wyjściowych |
| wartość | Yes | Takie same jak `type` | Wartość zwracana w danych wyjściowych |
|||||

Aby uzyskać dane wyjściowe z przebiegu przepływu pracy, Przejrzyj historię uruchamiania aplikacji logiki i szczegółowe informacje w witrynie Azure portal lub [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Można również przekazać dane wyjściowe z systemami zewnętrznymi, na przykład usługa Power BI, dzięki czemu można tworzyć pulpity nawigacyjne.

<a name="expressions"></a>

## <a name="expressions"></a>Wyrażenia

Za pomocą formatu JSON może mieć wartości literału, które istnieją w czasie projektowania, na przykład:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Również może mieć wartości, które nie istnieją do czasu wykonywania. Do reprezentowania tych wartości, można użyć *wyrażenia*, które są oceniane w czasie wykonywania. Wyrażenie jest sekwencji, który może zawierać jeden lub więcej [funkcje](#functions), [operatory](#operators), zmiennych, jawne wartości lub stałe. W definicji przepływu pracy, można użyć wyrażenia dowolnym miejscu w wartości ciągu JSON przez dodanie przedrostka wyrażenia ze znakiem w (\@). Podczas obliczania wyrażenia, która reprezentuje wartość JSON, treści wyrażenia jest wyodrębniany, usuwając \@ znaków i zawsze skutkuje inną wartość JSON.

Na przykład uprzednio zdefiniowany `customerName` właściwości, można pobrać wartości właściwości, za pomocą [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) działać w wyrażeniach, a następnie przypisać tę wartość do `accountName` właściwości:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolacja ciągów* również pozwala używać wielu wyrażeń wewnątrz ciągów, które zostaną opakowane przy \@ znaków i nawiasy klamrowe ({}). Poniżej przedstawiono składnię:

```json
@{ "<expression1>", "<expression2>" }
```

Wynik to zawsze ciąg wprowadzania tej możliwości podobne do `concat()` funkcji, na przykład: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Jeśli masz ciąg literału, który rozpoczyna się od \@ znak, prefiks \@ znak z inną \@ znak jako znak ucieczki: \@\@

Poniższe przykłady pokazują, jak są obliczane wyrażenia:

| Wartość JSON | Wynik |
|------------|--------|
| "Sophia Owen" | Zwrócenie następujących znaków: 'Sophia Owen' |
| "tablica [1]" | Zwraca następujące znaki: "array [1]" |
| "\@\@" | Zwraca te znaki jako ciąg jednego znaku: "\@" |
| " \@" | Zwracają te znaki jako ciąg dwóch znaków: " \@" |
|||

W poniższych przykładach Załóżmy, że należy zdefiniować "myBirthMonth" równa się "Stycznia" i "myAge" równą liczbie 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Poniższe przykłady pokazują, jak są obliczane następujących wyrażeń:

| Wyrażenie JSON | Wynik |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Zwraca następujący ciąg: "Stycznia" |
| "\@{parameters('myBirthMonth')}" | Zwraca następujący ciąg: "Stycznia" |
| "\@parameters('myAge')" | Zwróć tę liczbę: 42 |
| "\@{parameters('myAge')}" | Zwróć tę liczbę jako ciąg: "42" |
| "Mój wiek to \@{parameters('myAge')}" | Zwraca następujący ciąg: "Mój wiek to 42" |
| "\@concat ("Mój wiek to", string(parameters('myAge')))" | Zwraca następujący ciąg: "Mój wiek to 42" |
| "Mój wiek to \@ \@{parameters('myAge')}" | Zwróć tego ciągu, który zawiera wyrażenie: "Mój wiek to \@{parameters('myAge')}" |
|||

Podczas pracy wizualnie w Projektancie aplikacji logiki, możesz utworzyć wyrażenia przez Konstruktor wyrażeń na przykład:

![Projektant aplikacji logiki > Konstruktor wyrażeń](./media/logic-apps-workflow-definition-language/expression-builder.png)

Gdy wszystko będzie gotowe, znajduje się wyrażenie dla odpowiednich właściwości w definicji przepływu pracy, na przykład, `searchQuery` właściwości w tym miejscu:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operatory

W [wyrażeń](#expressions) i [funkcje](#functions), operatory wykonywania określonych zadań, takich jak odwołanie do właściwości lub wartość w tablicy.

| Operator | Zadanie |
|----------|------|
| ' | Aby użyć literału ciągu jako dane wejściowe lub w wyrażeniach i funkcje, opakowywanie ciąg tylko pojedynczy cudzysłów, na przykład `'<myString>'`. Nie należy używać podwójnego cudzysłowu (""), które powodują konflikt z formatowaniem JSON wokół całe wyrażenie. Na przykład: <p>**Tak**: length('Hello') </br>**Nie**: length("Hello") <p>Jeśli przekazujesz, tablic lub liczby, nie trzeba zawijanie znaków interpunkcyjnych. Na przykład: <p>**Tak**: długość ([1, 2, 3]) </br>**Nie**: długość ("[1, 2, 3]") |
| [] | Aby odwołać się do wartości w określonym położeniu (indeks) w tablicy, Użyj nawiasów kwadratowych. Na przykład, aby uzyskać drugi element w tablicy: <p>`myArray[1]` |
| . | Aby odwoływać się do właściwości w obiekcie, użyj operatora kropki. Na przykład, aby uzyskać `name` właściwość `customer` obiekt JSON: <p>`"@parameters('customer').name"` |
| ? | Odwoływanie się do wartości null właściwości w obiekcie bez błędów środowiska uruchomieniowego, użyj operatora znaku zapytania. Na przykład do obsługi o wartości null dane wyjściowe z wyzwalaczem, możesz użyć tego wyrażenia: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funkcje

Niektóre wyrażenia, Uzyskaj ich wartości z akcji środowiska uruchomieniowego, które jeszcze nie istnieje podczas uruchamiania swojej definicji przepływu pracy. Aby odwołać się i pracować z tych wartości w wyrażeniach, można użyć [ *funkcje* ](../logic-apps/workflow-definition-language-functions-reference.md) zapewniająca język definicji przepływów pracy.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [język definicji przepływów pracy akcji i wyzwalaczy](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Dowiedz się więcej o programowe tworzenie i zarządzanie aplikacjami logiki za pomocą [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
