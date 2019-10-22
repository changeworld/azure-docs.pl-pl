---
title: Schemat definicji przepływu pracy Azure Logic Apps
description: Dokumentacja schematu dotycząca języka definicji przepływu pracy w Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 64c01baf0852e7e09ce9ffed2d079b47e95f7190
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680079"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Przewodnik odwołujący się do schematu definicji przepływu pracy w Azure Logic Apps

Podczas tworzenia aplikacji logiki w [Azure Logic Apps](../logic-apps/logic-apps-overview.md)aplikacja logiki ma podstawową definicję przepływu pracy opisującą rzeczywistą logikę, która jest uruchamiana w aplikacji logiki. Ta definicja przepływu pracy używa [kodu JSON](https://www.json.org/) i jest zgodna ze strukturą zweryfikowaną przez schemat języka definicji przepływu pracy. Ta dokumentacja zawiera przegląd dotyczący tej struktury oraz sposób definiowania atrybutów przez schemat w definicji przepływu pracy.

## <a name="workflow-definition-structure"></a>Struktura definicji przepływu pracy

Definicja przepływu pracy zawsze zawiera wyzwalacz do tworzenia wystąpienia aplikacji logiki oraz co najmniej jedną akcję, która jest uruchamiana po uruchomieniu wyzwalacza.

Poniżej przedstawiono strukturę wysokiego poziomu dla definicji przepływu pracy:

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

| Atrybut | Wymagane | Opis |
|-----------|----------|-------------|
| `definition` | Tak | Element początkowy definicji przepływu pracy |
| `$schema` | Tylko wtedy, gdy zewnętrznie odwołuje się do definicji przepływu pracy | Lokalizacja pliku schematu JSON opisującego wersję języka definicji przepływu pracy, którą można znaleźć tutaj: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Nie | Definicje jednej lub więcej akcji do wykonania w czasie wykonywania przepływu pracy. Aby uzyskać więcej informacji, zobacz [wyzwalacze i akcje](#triggers-actions). <p><p>Maksymalna liczba akcji: 250 |
| `contentVersion` | Nie | Numer wersji dla definicji przepływu pracy, który domyślnie jest "1.0.0.0". Aby ułatwić identyfikację i potwierdzenie prawidłowej definicji podczas wdrażania przepływu pracy, określ wartość, która ma zostać użyta. |
| `outputs` | Nie | Definicje danych wyjściowych do zwrócenia z przebiegu przepływu pracy. Aby uzyskać więcej informacji, zobacz dane [wyjściowe](#outputs). <p><p>Maksymalna liczba danych wyjściowych: 10 |
| `parameters` | Nie | Definicje dla co najmniej jednego parametru, który przekazuje wartości do użycia w czasie wykonywania aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Parametry](#parameters). <p><p>Maksymalna liczba parametrów: 50 |
| `staticResults` | Nie | Definicje dla co najmniej jednego wyniku statycznego zwracanego przez akcje jako elementy wyjściowe makiety, gdy w tych akcjach są włączone statyczne wyniki. W każdej definicji akcji atrybut `runtimeConfiguration.staticResult.name` odwołuje się do odpowiedniej definicji wewnątrz `staticResults`. Aby uzyskać więcej informacji, zobacz [wyniki statyczne](#static-results). |
| `triggers` | Nie | Definicje dla co najmniej jednego wyzwalacza, który tworzy wystąpienie przepływu pracy. Można zdefiniować więcej niż jeden wyzwalacz, ale tylko w języku definicji przepływu pracy, a nie wizualnie za pomocą projektanta Logic Apps. Aby uzyskać więcej informacji, zobacz [wyzwalacze i akcje](#triggers-actions). <p><p>Maksymalna liczba wyzwalaczy: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Wyzwalacze i akcje

W definicji przepływu pracy sekcje `triggers` i `actions` definiują wywołania, które są wykonywane podczas wykonywania przepływu pracy. Aby poznać składnię i więcej informacji o tych sekcjach, zobacz [wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametry

Cykl życia wdrożenia ma zwykle różne środowiska do projektowania, testowania, przemieszczania i produkcji. W przypadku wdrażania aplikacji logiki w różnych środowiskach można chcieć używać różnych wartości, takich jak parametry połączenia, na podstawie Twoich potrzeb. Możesz też mieć wartości, które mają być ponownie używane w całej aplikacji logiki bez zakodowana, lub które często się zmieniają. W sekcji `parameters` definicji przepływu pracy można definiować lub edytować parametry wartości, które są używane przez aplikację logiki w czasie wykonywania. Należy najpierw zdefiniować te parametry, aby można było odwoływać się do tych parametrów w innym miejscu definicji przepływu pracy.

Oto ogólna struktura definicji parametru:

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

| Atrybut | Wymagane | Typ | Opis |
|-----------|----------|------|-------------|
| *Nazwa parametru* < > | Tak | Ciąg | Nazwa parametru, który ma zostać zdefiniowany |
| <*Typ parametru* > | Tak | int, float, String, bool, Array, Object, SecureString, secureobject <p><p>**Uwaga**: dla wszystkich haseł, kluczy i wpisów tajnych użyj typów `securestring` lub `secureobject`, ponieważ operacja `GET` nie zwraca tych typów. Aby uzyskać więcej informacji na temat zabezpieczania parametrów, zapoznaj się z [zaleceniami dotyczącymi zabezpieczeń i parametrami wejściowymi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Typ parametru |
| <*domyślny parametr-wartość* > | Tak | Taki sam jak `type` | Wartość domyślna parametru, która ma być używana, jeśli żadna wartość nie zostanie określona podczas tworzenia wystąpienia przepływu pracy. Atrybut `defaultValue` jest wymagany, aby projektant aplikacji logiki mógł poprawnie pokazać parametr, ale można określić wartość pustą. |
| <*tablicę z wartościami dozwolonymi parametrami* > | Nie | Tablica | Tablica z wartościami, które parametr może zaakceptować |
| <*parametru-description* > | Nie | Obiekt JSON | Wszystkie inne szczegóły parametrów, takie jak opis parametru |
||||

Następnie Utwórz [szablon Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) dla definicji przepływu pracy, zdefiniuj parametry szablonu, które akceptują wartości, które mają być używane podczas wdrażania, Zastąp wartości stałe wartościami z odwołaniami do parametrów definicji szablonu lub przepływu pracy jako odpowiednie i przechowywanie wartości do użycia we wdrożeniu w osobnym [pliku parametrów](../azure-resource-manager/resource-group-template-deploy.md#parameter-files). Dzięki temu można łatwiej zmieniać te wartości za pomocą pliku parametrów bez konieczności aktualizacji i ponownego wdrażania aplikacji logiki. Aby uzyskać informacje poufne lub zabezpieczone, takie jak nazwy użytkowników, hasła i wpisy tajne, można przechowywać te wartości w Azure Key Vault i mieć plik parametrów pobiera te wartości z magazynu kluczy. Aby uzyskać więcej informacji i przykłady dotyczące definiowania parametrów na poziomach definicji i przepływu pracy, zobacz [Omówienie: Automatyzowanie wdrażania dla aplikacji logiki za pomocą szablonów Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Wyniki statyczne

W atrybucie `staticResults`, zdefiniuj `outputs` makiety akcji i `status`, że akcja zwraca, gdy ustawienie wyniku statycznego akcji jest włączone. W definicji akcji atrybut `runtimeConfiguration.staticResult.name` odwołuje się do nazwy definicji wyniku statycznego wewnątrz `staticResults`. Dowiedz się, w jaki sposób można [testować aplikacje logiki przy użyciu danych makiety przez skonfigurowanie wyników statycznych](../logic-apps/test-logic-apps-mock-data-static-results.md).

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

| Atrybut | Wymagane | Typ | Opis |
|-----------|----------|------|-------------|
| <*nazwy static-Result-definition* > | Tak | Ciąg | Nazwa statycznej definicji wyniku, którą definicja akcji może odwoływać się za pomocą obiektu `runtimeConfiguration.staticResult`. Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Możesz użyć dowolnej unikatowej nazwy. Domyślnie ta unikatowa nazwa jest dołączana do liczby, która jest zwiększana w miarę potrzeb. |
| <*dane wyjściowe — atrybuty i wartości zwracane* > | Tak | Różna | Wymagania dotyczące tych atrybutów różnią się w zależności od różnych warunków. Na przykład gdy `status` jest `Succeeded`, atrybut `outputs` zawiera atrybuty i wartości zwracane jako dane wyjściowe makiety przez akcję. Jeśli `status` jest `Failed`, atrybut `outputs` zawiera atrybut `errors`, który jest tablicą zawierającą co najmniej jeden błąd `message` obiektów, które zawierają informacje o błędzie. |
| <*wartości nagłówka* > | Nie | JSON | Wszystkie wartości nagłówka zwrócone przez akcję |
| <*Stan — zwrócony kod* > | Tak | Ciąg | Kod stanu zwracany przez akcję |
| *akcja < — stan* > | Tak | Ciąg | Stan akcji, na przykład `Succeeded` lub `Failed` |
|||||

Na przykład w tej definicji akcji HTTP `runtimeConfiguration.staticResult.name` atrybut odwołuje się `HTTP0` wewnątrz atrybutu `staticResults`, gdzie są zdefiniowane dane wyjściowe makiety dla akcji. Atrybut `runtimeConfiguration.staticResult.staticResultOptions` określa, że ustawienie wyniku statycznego jest `Enabled` w akcji HTTP.

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

Akcja HTTP zwraca dane wyjściowe w definicji `HTTP0` wewnątrz `staticResults`. W tym przykładzie dla kodu stanu dane wyjściowe makiety są `OK`. W przypadku wartości nagłówka dane wyjściowe makiety są `"Content-Type": "application/JSON"`. W przypadku stanu akcji dane wyjściowe makiety są `Succeeded`.

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

Za pomocą formatu JSON można mieć wartości literałów, które istnieją w czasie projektowania, na przykład:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Można także mieć wartości, które nie istnieją do czasu uruchomienia. Aby przedstawić te wartości, można użyć *wyrażeń*, które są oceniane w czasie wykonywania. Wyrażenie jest sekwencją, która może zawierać jedną lub więcej [funkcji](#functions), [operatorów](#operators), zmiennych, wartości jawnych lub stałych. W definicji przepływu pracy można użyć wyrażenia w dowolnym miejscu w wartości ciągu JSON przez umieszczenie prefiksu wyrażenia z znakiem (\@). Podczas oceniania wyrażenia, które reprezentuje wartość JSON, treść wyrażenia jest wyodrębniana przez usunięcie znaku \@ i zawsze daje w wyniku inną wartość JSON.

Na przykład dla właściwości wcześniej zdefiniowanej `customerName` można uzyskać wartość właściwości przy użyciu funkcji [Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) w wyrażeniu i przypisać tę wartość do właściwości `accountName`:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolacja ciągów* umożliwia także używanie wielu wyrażeń wewnątrz ciągów, które są opakowane przez znak \@ i nawiasy klamrowe ({}). Oto składnia:

```json
@{ "<expression1>", "<expression2>" }
```

Wynik jest zawsze ciągiem, dzięki czemu ta funkcja jest podobna do funkcji `concat()`, na przykład: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Jeśli masz ciąg literału, który rozpoczyna się od znaku \@, poprzedź znak \@ innym znakiem \@ jako znak ucieczki: \@ \@

W poniższych przykładach pokazano, jak są oceniane wyrażenia:

| Wartość JSON | Wynik |
|------------|--------|
| "Sophia Owen" | Zwróć następujące znaki: "Sophia Owen" |
| "Array [1]" | Zwróć następujące znaki: "Array [1]" |
| "\@ \@" | Zwracaj te znaki jako ciąg jednoznakowy: "\@" |
| "\@" | Zwróć te znaki jako ciąg dwubajtowy: "\@" |
|||

Na potrzeby tych przykładów Załóżmy, że definiuje się wartość "myBirthMonth" równą "Styczeń" i "myAge" równą liczbie 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

W poniższych przykładach pokazano, jak są oceniane następujące wyrażenia:

| Wyrażenie JSON | Wynik |
|-----------------|--------|
| "\@parameters (" myBirthMonth ")" | Zwróć ten ciąg: "Styczeń" |
| "\@ {Parameters (" myBirthMonth ")}" | Zwróć ten ciąg: "Styczeń" |
| "\@parameters (" myAge ")" | Zwróć ten numer: 42 |
| "\@ {Parameters (" myAge ")}" | Zwróć tę liczbę jako ciąg: "42" |
| "Mój wiek jest \@ {Parameters (" myAge ")}" | Zwróć ten ciąg: "Mój wiek to 42" |
| "\@concat (" Mój wiek to ", String (parametry (" myAge ")))" | Zwróć ten ciąg: "Mój wiek to 42" |
| "Mój wiek jest \@ \@ {Parameters (" myAge ")}" | Zwróć ten ciąg, który zawiera wyrażenie: "Mój wiek jest \@ {Parameters (" myAge ")}" |
|||

Gdy pracujesz wizualnie w projektancie Logic Apps, możesz tworzyć wyrażenia za pomocą Konstruktora wyrażeń, na przykład:

![Konstruktor Logic Apps projektanta > Expression](./media/logic-apps-workflow-definition-language/expression-builder.png)

Gdy wszystko będzie gotowe, zostanie wyświetlone wyrażenie dla odpowiedniej właściwości w definicji przepływu pracy, na przykład właściwość `searchQuery` tutaj:

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

W sekcji `outputs` Zdefiniuj dane, które mogą być zwracane przez przepływ pracy po zakończeniu działania. Na przykład w celu śledzenia określonego stanu lub wartości z każdego przebiegu, należy określić, że dane wyjściowe przepływu pracy będą zwracane z tych danych.

> [!NOTE]
> Podczas odpowiadania na żądania przychodzące z interfejsu API REST usługi nie należy używać `outputs`. Zamiast tego należy użyć typu akcji `Response`. Aby uzyskać więcej informacji, zobacz [wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md).

Oto ogólna struktura definicji danych wyjściowych:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atrybut | Wymagane | Typ | Opis |
|-----------|----------|------|-------------|
| *Nazwa klucza* < > | Tak | Ciąg | Nazwa klucza dla wyjściowej wartości zwracanej |
| < >*typu klucza* | Tak | int, float, String, SecureString, bool, Array, obiekt JSON | Typ wyjściowej wartości zwracanej |
| <*klucz-wartość* > | Tak | Analogicznie jak*klucz typu* < > | Zwracana wartość wyjściowa |
|||||

Aby uzyskać dane wyjściowe z przebiegu przepływu pracy, przejrzyj historię uruchamiania aplikacji logiki oraz szczegóły w Azure Portal lub Użyj [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Możesz również przekazać dane wyjściowe do systemów zewnętrznych, na przykład Power BI, aby można było tworzyć pulpity nawigacyjne.

<a name="operators"></a>

## <a name="operators"></a>Operatory

W [wyrażeniach](#expressions) i [funkcjach](#functions)operatory wykonują określone zadania, takie jak odwołanie do właściwości lub wartości w tablicy.

| Operator | Zadanie |
|----------|------|
| ' | Aby użyć literału ciągu jako dane wejściowe lub w wyrażeniach i funkcjach, zawiń ciąg tylko w pojedynczym cudzysłowie, na przykład `'<myString>'`. Nie należy używać podwójnych cudzysłowów (""), które powodują konflikt z formatowaniem JSON wokół całego wyrażenia. Na przykład: <p>**Tak**: length ("Hello") </br>**Nie**: Długość ("Witaj") <p>W przypadku przekazywania tablic lub cyfr nie jest wymagane Zawijanie znaków. Na przykład: <p>**Tak**: Długość ([1, 2, 3]) </br>**Nie**: Długość ("[1, 2, 3]") |
| [] | Aby odwołać się do wartości w określonej pozycji (indeks) w tablicy, użyj nawiasów kwadratowych. Na przykład, aby uzyskać drugi element w tablicy: <p>`myArray[1]` |
| . | Aby odwołać się do właściwości w obiekcie, użyj operatora kropki. Na przykład, aby uzyskać Właściwość `name` dla `customer` obiektu JSON: <p>`"@parameters('customer').name"` |
| ? | Aby odwołać się do właściwości o wartości null w obiekcie bez błędu środowiska uruchomieniowego, użyj operatora znaku zapytania. Aby na przykład obsłużyć dane wyjściowe o wartości null z wyzwalacza, można użyć tego wyrażenia: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Niektóre wyrażenia pobierają wartości z akcji środowiska uruchomieniowego, które mogą jeszcze nie istnieć podczas uruchamiania definicji przepływu pracy. Aby odwołać się do tych wartości lub używać ich w wyrażeniach, można użyć [*funkcji*](../logic-apps/workflow-definition-language-functions-reference.md) dostępnych w języku definicji przepływu pracy.

## <a name="next-steps"></a>Następne kroki

* Informacje o [akcjach i wyzwalaczach języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Informacje na temat programistycznego tworzenia aplikacji logiki i zarządzania nimi za pomocą [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
