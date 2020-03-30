---
title: Odwołanie do schematu języka definicji przepływu pracy
description: Przewodnik po schemacie JSON i składni języka definicji przepływu pracy, który opisuje przepływy pracy w aplikacjach logiki azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283864"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Przewodnik po schemacie języka definicji przepływu pracy w aplikacjach logiki azure

Podczas tworzenia aplikacji logiki w [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md)aplikacja logiki ma podstawową definicję przepływu pracy, która opisuje rzeczywistą logikę, która jest uruchamiana w aplikacji logiki. Ta definicja przepływu pracy używa [JSON](https://www.json.org/) i następuje struktury, która jest sprawdzana przez schemat języka definicji przepływu pracy. To odwołanie zawiera omówienie tej struktury i jak schemat definiuje atrybuty w definicji przepływu pracy.

## <a name="workflow-definition-structure"></a>Struktura definicji przepływu pracy

Definicja przepływu pracy zawsze zawiera wyzwalacz do tworzenia wystąpienia aplikacji logiki, a także jedną lub więcej akcji, które są uruchamiane po uruchomieniu wyzwalacza.

Oto struktura wysokiego poziomu definicji przepływu pracy:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Atrybut | Wymagany | Opis |
|-----------|----------|-------------|
| `definition` | Tak | Element początkowy definicji przepływu pracy |
| `$schema` | Tylko wtedy, gdy zewnętrznie odwołuje się do definicji przepływu pracy | Lokalizacja pliku schematu JSON opisującego wersję językową definicji przepływu pracy, którą można znaleźć tutaj: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nie | Definicje dla jednej lub więcej akcji do wykonania w czasie wykonywania przepływu pracy. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i akcje](#triggers-actions). <p><p>Maksymalna liczba akcji: 250 |
| `contentVersion` | Nie | Numer wersji definicji przepływu pracy, która domyślnie jest "1.0.0.0". Aby ułatwić identyfikowanie i potwierdzanie poprawnej definicji podczas wdrażania przepływu pracy, określ wartość, która ma być używana. |
| `outputs` | Nie | Definicje danych wyjściowych do zwrócenia z uruchomienia przepływu pracy. Aby uzyskać więcej informacji, zobacz [Dane wyjściowe](#outputs). <p><p>Maksymalna liczba wyjść: 10 |
| `parameters` | Nie | Definicje dla jednego lub więcej parametrów, które przekazują wartości do użycia w czasie wykonywania aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Parametry](#parameters). <p><p>Maksymalne parametry: 50 |
| `staticResults` | Nie | Definicje dla jednego lub więcej wyników statycznych zwracanych przez akcje jako makiety wyjść, gdy wyniki statyczne są włączone dla tych akcji. W każdej definicji `runtimeConfiguration.staticResult.name` akcji atrybut odwołuje się `staticResults`do odpowiedniej definicji wewnątrz . Aby uzyskać więcej informacji, zobacz [Wyniki statyczne](#static-results). |
| `triggers` | Nie | Definicje co najmniej jednego wyzwalacza, które powodują tworzenie wystąpienia przepływu pracy. Można zdefiniować więcej niż jeden wyzwalacz, ale tylko za pomocą języka definicji przepływu pracy, a nie wizualnie za pośrednictwem Projektanta aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i akcje](#triggers-actions). <p><p>Maksymalne wyzwalacze: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Wyzwalacze i działania

W definicji przepływu `triggers` pracy `actions` i sekcje zdefiniować wywołania, które mają miejsce podczas wykonywania przepływu pracy. Aby uzyskać informacje na temat składni i więcej informacji o tych sekcjach, zobacz [Wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametry

Cykl życia wdrażania zwykle ma różne środowiska do programowania, testową, przejściową i produkcyjną. Podczas wdrażania aplikacji logiki w różnych środowiskach, prawdopodobnie chcesz użyć różnych wartości, takich jak parametry połączenia, na podstawie potrzeb wdrożenia. Lub może mieć wartości, które mają być ponownie stosowane w całej aplikacji logiki bez twardego kodowania lub często się zmieniają. W `parameters` sekcji definicji przepływu pracy można definiować lub edytować parametry wartości używanych przez aplikację logiki w czasie wykonywania. Należy zdefiniować te parametry najpierw, zanim będzie można odwoływać się do tych parametrów w innym miejscu w definicji przepływu pracy.

Oto ogólna struktura definicji parametrów:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Atrybut | Wymagany | Typ | Opis |
|-----------|----------|------|-------------|
| <*nazwa parametru*> | Tak | Ciąg | Nazwa parametru, który chcesz zdefiniować |
| <*typ parametru*> | Tak | int, float, string, bool, tablica, obiekt, securestring, secureobject <p><p>**Uwaga:** W przypadku wszystkich haseł, kluczy `securestring` `secureobject` i wpisów tajnych należy użyć tych typów, ponieważ `GET` operacja nie zwraca tych typów. Aby uzyskać więcej informacji na temat zabezpieczania parametrów, zobacz [Zalecenia dotyczące zabezpieczeń dla parametrów akcji i danych wejściowych](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Typ parametru |
| <*domyślna wartość parametru*> | Tak | Tak samo jak`type` | Domyślna wartość parametru do użycia, jeśli nie określono żadnej wartości podczas tworzenia wystąpienia przepływu pracy. Atrybut `defaultValue` jest wymagany, aby projektant aplikacji logiki można poprawnie wyświetlić parametr, ale można określić pustą wartość. |
| <*array-with-permitted-parameter-values*> | Nie | Tablica | Tablica z wartościami, które parametr może zaakceptować |
| <*opis parametru*> | Nie | Obiekt JSON | Wszelkie inne szczegóły parametrów, takie jak opis parametru |
||||

Następnie utwórz [szablon usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) dla definicji przepływu pracy, zdefiniuj parametry szablonu, które akceptują żądane wartości podczas wdrażania, zastąp wartości zakodowane odniesieniami do parametrów definicji szablonu lub przepływu pracy, a także przechowuj wartości, które mają być używane podczas wdrażania, w oddzielnym pliku [parametrów](../azure-resource-manager/templates/parameter-files.md). W ten sposób można łatwiej zmienić te wartości za pomocą pliku parametrów bez konieczności aktualizowania i ponownego rozmieszczenia aplikacji logiki. W przypadku informacji, które są poufne lub muszą być zabezpieczone, takich jak nazwy użytkowników, hasła i wpisy tajne, można przechowywać te wartości w usłudze Azure Key Vault i mieć plik parametrów pobrać te wartości z magazynu kluczy. Aby uzyskać więcej informacji i przykładów dotyczących definiowania parametrów na poziomach definicji szablonu i przepływu pracy, zobacz [Omówienie: Automatyzacja wdrażania aplikacji logiki za pomocą szablonów usługi Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Wyniki statyczne

W `staticResults` atrybucie zdefiniuj makietę `outputs` akcji i `status` że akcja zwraca, gdy ustawienie statycznego wyniku akcji jest włączone. W definicji akcji `runtimeConfiguration.staticResult.name` atrybut odwołuje się do nazwy definicji wyników `staticResults`statycznych wewnątrz . Dowiedz się, jak [testować aplikacje logiki z makietami danych, konfigurując wyniki statyczne.](../logic-apps/test-logic-apps-mock-data-static-results.md)

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Atrybut | Wymagany | Typ | Opis |
|-----------|----------|------|-------------|
| <*nazwa statyczna-result-definition*> | Tak | Ciąg | Nazwa definicji wyników statycznych, do których definicja akcji może odwoływać się za pośrednictwem `runtimeConfiguration.staticResult` obiektu. Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Można użyć dowolnej unikatowej nazwy, która ma być. Domyślnie ta unikatowa nazwa jest dołączana z liczbą, która jest zwiększana w razie potrzeby. |
| <*zwracane atrybuty i wartości wyjściowe*> | Tak | Różna | Wymagania dla tych atrybutów różnią się w zależności od warunków. Na przykład, `status` gdy `Succeeded`jest `outputs` , atrybut zawiera atrybuty i wartości zwracane jako makiety dane wyjściowe przez akcję. Jeśli `status` jest `Failed`, `outputs` atrybut zawiera `errors` atrybut, który jest tablicą z `message` co najmniej jednym obiektem błędów, które zawierają informacje o błędzie. |
| <*wartości nagłówka*> | Nie | JSON | Wszelkie wartości nagłówka zwrócone przez akcję |
| <*kod stanu zwrócony*> | Tak | Ciąg | Kod stanu zwrócony przez akcję |
| <*stan akcji*> | Tak | Ciąg | Na przykład status akcji `Succeeded` lub`Failed` |
|||||

Na przykład w tej definicji `runtimeConfiguration.staticResult.name` akcji HTTP `HTTP0` odwołania `staticResults` atrybut wewnątrz atrybutu, gdzie są zdefiniowane makiety danych wyjściowych dla akcji. Atrybut `runtimeConfiguration.staticResult.staticResultOptions` określa, że ustawienie wyników statycznych znajduje się `Enabled` w akcji HTTP.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

Akcja HTTP zwraca dane wyjściowe `HTTP0` `staticResults`w definicji wewnątrz . W tym przykładzie dla kodu stanu `OK`makiety wyjściowej jest . Dla wartości nagłówka makiety wyjściowej jest `"Content-Type": "application/JSON"`. Dla stanu akcji makiety wyjściowej jest `Succeeded`.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Wyrażenia

W JSON można mieć wartości literału, które istnieją w czasie projektowania, na przykład:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Można również mieć wartości, które nie istnieją do czasu wykonywania. Aby reprezentować te wartości, można użyć *wyrażeń,* które są oceniane w czasie wykonywania. Wyrażenie to sekwencja, która może zawierać jedną lub więcej [funkcji,](#functions) [operatorów,](#operators)zmiennych, wartości jawnych lub stałych. W definicji przepływu pracy można użyć wyrażenia w dowolnym miejscu wartości ciągu JSON, prefiksując wyrażenie znakiem (at-sign).\@ Podczas oceny wyrażenia, które reprezentuje wartość JSON, treść wyrażenia jest \@ wyodrębniany przez usunięcie znaku i zawsze powoduje inną wartość JSON.

Na przykład dla wcześniej `customerName` zdefiniowanej właściwości można uzyskać wartość właściwości za pomocą funkcji [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w `accountName` wyrażeniu i przypisać tę wartość do właściwości:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolacja ciągów* umożliwia również użycie wielu wyrażeń \@ wewnątrz ciągów, które{}są zawijane przez znak i nawiasy klamrowe ( ). Oto składnia:

```json
@{ "<expression1>", "<expression2>" }
```

Wynikiem jest zawsze ciąg, dzięki czemu ta `concat()` funkcja jest podobna do funkcji, na przykład: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Jeśli masz ciąg literał, który \@ zaczyna się od \@ znaku, \@ przedrostek znak z innym znakiem jako znak ucieczki:\@\@

Te przykłady pokazują, jak wyrażenia są oceniane:

| Wartość JSON | Wynik |
|------------|--------|
| "Sophia Owen" | Zwróć te postacie: 'Sophia Owen' |
| "tablica[1]" | Zwracanie tych znaków: 'array[1]' |
| "\@\@" | Zwraca te znaki jako jednoznakowy\@ciąg: ' |
| " \@" | Zwraca te znaki jako dwuznakowy \@ciąg: ' |
|||

W poniższych przykładach załóżmy, że definiujesz "myBirthMonth" równe "Styczeń" i "myAge" równe liczbie 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Te przykłady pokazują, jak oceniane są następujące wyrażenia:

| Wyrażenie JSON | Wynik |
|-----------------|--------|
| "\@parametry('myBirthMonth')" | Zwraca ten ciąg: "Styczeń" |
| "\@{parameters('myBirthMonth')}" | Zwraca ten ciąg: "Styczeń" |
| "\@parametry('myAge')" | Zwróć tę liczbę: 42 |
| "\@{parameters('myAge')}" | Zwraca tę liczbę jako ciąg: "42" |
| "Mój wiek \@to {parameters('myAge')}" | Zwróć ten napis: "Mój wiek to 42 lata" |
| "\@concat('Mój wiek to ', string(parameters('myAge')))" | Zwróć ten napis: "Mój wiek to 42 lata" |
| "Mój wiek \@ \@to {parameters('myAge')}" | Zwraca ten ciąg, który zawiera wyrażenie: \@"Mój wiek to {parameters('myAge')}' |
|||

Podczas pracy wizualnej w Logic Apps Designer, można tworzyć wyrażenia za pośrednictwem konstruktora wyrażeń, na przykład:

![Projektant aplikacji logiki > konstruktora wyrażeń](./media/logic-apps-workflow-definition-language/expression-builder.png)

Po zakończeniu wyrażenie pojawia się dla odpowiedniej właściwości w definicji przepływu `searchQuery` pracy, na przykład właściwość w tym miejscu:

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

<a name="outputs"></a>

## <a name="outputs"></a>Dane wyjściowe

W `outputs` sekcji zdefiniuj dane, które przepływ pracy może zwrócić po zakończeniu pracy. Na przykład, aby śledzić określony stan lub wartość z każdego uruchomienia, należy określić, że dane wyjściowe przepływu pracy zwraca te dane.

> [!NOTE]
> Podczas odpowiadania na przychodzące żądania z interfejsu API `outputs`REST usługi nie należy używać programu . Zamiast tego należy `Response` użyć typu akcji. Aby uzyskać więcej informacji, zobacz [Wyzwalacze przepływu pracy i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md).

Oto ogólna struktura definicji wyjścia:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atrybut | Wymagany | Typ | Opis |
|-----------|----------|------|-------------|
| <*nazwa klucza*> | Tak | Ciąg | Nazwa klucza dla wyjściowej wartości zwracanej |
| <*typ klucza*> | Tak | int, float, string, securestring, bool, tablica, obiekt JSON | Typ dla wyjściowej wartości zwracanej |
| <*wartość klucza*> | Tak | Tak samo jak <*typu klucza*> | Wartość zwrotu danych wyjściowych |
|||||

Aby uzyskać dane wyjściowe z przebiegu przepływu pracy, przejrzyj historię uruchamiania aplikacji logiki i szczegóły w witrynie Azure portal lub użyj [interfejsu API REST przepływu pracy.](https://docs.microsoft.com/rest/api/logic/workflows) Można również przekazywać dane wyjściowe do systemów zewnętrznych, na przykład usługi Power BI, aby można było tworzyć pulpity nawigacyjne.

<a name="operators"></a>

## <a name="operators"></a>Operatory

W [wyrażeniach](#expressions) i [funkcjach](#functions)operatorzy wykonują określone zadania, takie jak odwołanie do właściwości lub wartości w tablicy.

| Operator | Zadanie |
|----------|------|
| ' | Aby użyć literału ciągu jako danych wejściowych lub w wyrażeniach i funkcjach, zawiń ciąg tylko pojedynczymi cudzysłowami, `'<myString>'`na przykład . Nie należy używać podwójnych cudzysłowów (""), które są w konflikcie z formatowaniem JSON wokół całego wyrażenia. Przykład: <p>**Tak:** długość('Hello') </br>**Nie**: długość("Cześć") <p>Podczas przekazywania tablic lub liczb nie trzeba zawijać znaki interpunkcyjne. Przykład: <p>**Tak**: długość([1, 2, 3]) </br>**Nie**: długość("[1, 2, 3]") |
| [] | Aby odwołać się do wartości w określonej pozycji (indeks) w tablicy, należy użyć nawiasów kwadratowych. Na przykład, aby uzyskać drugi element w tablicy: <p>`myArray[1]` |
| . | Aby odwołać się do właściwości w obiekcie, należy użyć operatora kropki. Na przykład, aby `name` uzyskać `customer` właściwość dla obiektu JSON: <p>`"@parameters('customer').name"` |
| ? | Aby odwołać się do właściwości null w obiekcie bez błędu środowiska uruchomieniowego, należy użyć operatora znaku zapytania. Na przykład do obsługi wyjść null z wyzwalacza, można użyć tego wyrażenia: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funkcje

Niektóre wyrażenia uzyskać ich wartości z akcji środowiska uruchomieniowego, które mogą jeszcze nie istnieć, gdy definicja przepływu pracy zaczyna działać. Aby odwoływać się do tych wartości lub pracować z tymi wartościami w wyrażeniach, można użyć [*funkcji,*](../logic-apps/workflow-definition-language-functions-reference.md) które zapewnia język definicji przepływu pracy.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [akcjach i wyzwalaczach języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Dowiedz się więcej o programowym tworzeniu aplikacji logicznych i zarządzaniu nimi za pomocą [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
