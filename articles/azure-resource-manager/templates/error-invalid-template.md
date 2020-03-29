---
title: Nieprawidłowe błędy szablonu
description: W tym artykule opisano sposób rozwiązywania błędów nieprawidłowych szablonów podczas wdrażania szablonów usługi Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 65cd69d67933d117b51f37b587b276aec2bd635a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154061"
---
# <a name="resolve-errors-for-invalid-template"></a>Usuwanie błędów związanych z nieprawidłowym szablonem

W tym artykule opisano sposób rozwiązywania błędów nieprawidłowego szablonu.

## <a name="symptom"></a>Objaw

Podczas wdrażania szablonu pojawia się błąd wskazujący:

```
Code=InvalidTemplate
Message=<varies>
```

Komunikat o błędzie zależy od typu błędu.

## <a name="cause"></a>Przyczyna

Ten błąd może wynikać z kilku różnych typów błędów. Zazwyczaj obejmują one składnię lub błąd strukturalny w szablonie.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Rozwiązanie 1 - błąd składni

Jeśli zostanie wyświetlony komunikat o błędzie informujący o nieudanej weryfikacji szablonu, w szablonie może występować problem ze składnią.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ten błąd jest łatwy do wykonania, ponieważ wyrażenia szablonu mogą być skomplikowane. Na przykład następujące przypisanie nazwy dla konta magazynu ma jeden zestaw nawiasów, trzy funkcje, trzy zestawy nawiasów, jeden zestaw pojedynczych cudzysłowów i jedną właściwość:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Jeśli nie podasz pasującej składni, szablon tworzy wartość inną niż intencja.

Po wyświetleniu tego typu błędu należy dokładnie przejrzeć składnię wyrażenia. Należy rozważyć użycie edytora JSON, takich jak [Visual Studio](create-visual-studio-deployment-project.md) lub [Visual Studio Code](use-vs-code-to-create-template.md), który może ostrzegać o błędach składni.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Rozwiązanie 2 - nieprawidłowe długości segmentów

Inny nieprawidłowy błąd szablonu występuje, gdy nazwa zasobu nie jest w poprawnym formacie.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Zasób na poziomie głównym musi mieć jeden mniejszy segment w nazwie niż w typie zasobu. Każdy segment jest zróżnicowany ukośnikiem. W poniższym przykładzie typ ma dwa segmenty, a nazwa ma jeden segment, więc jest prawidłową **nazwą**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Ale następny przykład nie jest **prawidłową nazwą,** ponieważ ma taką samą liczbę segmentów jak typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

W przypadku zasobów podrzędnych typ i nazwa mają taką samą liczbę segmentów. Ta liczba segmentów ma sens, ponieważ pełna nazwa i typ dla dziecka zawiera nazwę nadrzędną i typ. W związku z tym pełna nazwa nadal ma jeden mniejszy segment niż pełny typ.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

Uzyskanie segmentów prawo może być trudne z typami Menedżera zasobów, które są stosowane przez dostawców zasobów. Na przykład zastosowanie blokady zasobów do witryny sieci web wymaga typu z czterema segmentami. W związku z tym nazwa jest trzy segmenty:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Rozwiązanie 3 - parametr jest nieprawidłowy

Jeśli podasz wartość parametru, która nie jest jedną z dozwolonych wartości, zostanie wyświetlony komunikat podobny do następującego błędu:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Sprawdź dokładnie dozwolone wartości w szablonie i podaj ich podczas wdrażania. Aby uzyskać więcej informacji na temat dozwolonych wartości [parametrów,](template-syntax.md#parameters)zobacz sekcja Parametry szablonów usługi Azure Resource Manager .

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Rozwiązanie 4 — zbyt wiele docelowych grup zasobów

Jeśli określisz więcej niż pięć grup zasobów docelowych w jednym wdrożeniu, zostanie wyświetlony ten błąd. Należy rozważyć konsolidację liczby grup zasobów w wdrożeniu lub wdrożenie niektórych szablonów jako oddzielnych wdrożeń. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Rozwiązanie 5 - wykryto zależność cykliczną

Ten błąd pojawia się, gdy zasoby zależą od siebie nawzajem w sposób, który uniemożliwia uruchomienie wdrożenia. Kombinacja współzależności sprawia, że dwa lub więcej zasobów czekać na inne zasoby, które również czekają. Na przykład resource1 zależy od zasobu3, resource2 zależy od zasobu1, a zasób3 zależy od zasobu2. Zazwyczaj można rozwiązać ten problem, usuwając niepotrzebne zależności.

Aby rozwiązać zależność cykliczną:

1. W szablonie znajdź zasób zidentyfikowany w zależności cyklicznej.
2. Dla tego zasobu należy zbadać **dependsOn** właściwości i wszelkie zastosowania funkcji **odwołania,** aby zobaczyć, które zasoby zależy od.
3. Sprawdź te zasoby, aby zobaczyć, na których zasobach zależą. Postępuj zgodnie z zależnościami, dopóki nie zauważysz zasobu, który zależy od oryginalnego zasobu.
5. Dla zasobów zaangażowanych w zależności cykliczne, dokładnie zbadać wszystkie zastosowania **dependsOn** właściwości do identyfikowania żadnych zależności, które nie są potrzebne. Usuń te zależności. Jeśli nie masz pewności, że zależność jest potrzebna, spróbuj ją usunąć.
6. Ponowne wdrożenie szablonu.

Usuwanie wartości z **dependsOn** właściwości może spowodować błędy podczas wdrażania szablonu. Jeśli pojawi się błąd, dodaj zależność z powrotem do szablonu.

Jeśli takie podejście nie rozwiąże zależności cyklicznej, należy rozważyć przeniesienie części logiki wdrażania do zasobów podrzędnych (takich jak rozszerzenia lub ustawienia konfiguracji). Skonfiguruj te zasoby podrzędne do wdrożenia po zasobach zaangażowanych w zależność cykliczną. Załóżmy na przykład, że wdrażasz dwie maszyny wirtualne, ale należy ustawić właściwości na każdej z nich, które odwołują się do drugiej. Można je wdrożyć w następującej kolejności:

1. z o.o.
2. z o.o.
3. Rozszerzenie na vm1 zależy od vm1 i vm2. Rozszerzenie ustawia wartości na vm1, które pobiera z vm2.
4. Rozszerzenie na vm2 zależy od vm1 i vm2. Rozszerzenie ustawia wartości na vm2, które pobiera z vm1.

To samo podejście działa w przypadku aplikacji usługi App Service. Należy rozważyć przeniesienie wartości konfiguracji do zasobu podrzędnego zasobu aplikacji. Można wdrożyć dwie aplikacje sieci web w następującej kolejności:

1. aplikacja internetowa1
2. aplikacja internetowa2
3. config for webapp1 zależy od webapp1 i webapp2. Zawiera ustawienia aplikacji z wartościami z webapp2.
4. config for webapp2 zależy od webapp1 i webapp2. Zawiera ustawienia aplikacji z wartościami z webapp1.
