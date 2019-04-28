---
title: Błędy Azure nieprawidłowy szablon | Dokumentacja firmy Microsoft
description: Opisuje sposób rozwiązania błędów nieprawidłowego szablonu.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: ee4bce38e0fcde93ba0417617ae90dab2eefda67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061395"
---
# <a name="resolve-errors-for-invalid-template"></a>Rozwiązywanie błędów dla szablonu jest nieprawidłowa

W tym artykule opisano sposób rozwiązania błędów nieprawidłowego szablonu.

## <a name="symptom"></a>Objaw

Podczas wdrażania szablonu, jest wyświetlany błąd wskazujący:

```
Code=InvalidTemplate
Message=<varies>
```

Komunikat o błędzie, zależy od typu błędu.

## <a name="cause"></a>Przyczyna

Ten błąd może wynikać z kilku różnych typów błędów. Obejmują one zazwyczaj strukturalnych lub składnia błąd w szablonie.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Rozwiązanie 1 — błąd składni

Jeśli zostanie wyświetlony komunikat o błędzie oznacza sprawdzanie poprawności szablonu nie powiodło się, możesz mieć problem składni w szablonie.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ten błąd jest łatwe, ponieważ wyrażenia szablonu mogą być skomplikowanych. Na przykład następujące przypisanie nazwy dla konta magazynu ma jeden zestaw nawiasów, trzy funkcje, trzech zestawów nawiasów, jeden zestaw apostrofy i jedną właściwość:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Jeśli nie podasz Składnia dopasowania, szablon tworzy wartość, która różni się od zamiaru.

Po otrzymaniu tego rodzaju błąd, należy dokładnie przejrzeć składni wyrażeń. Należy rozważyć użycie edytora JSON, takich jak [programu Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) lub [programu Visual Studio Code](resource-manager-vs-code.md), który może zostać wyświetlone ostrzeżenie dotyczące błędów składniowych.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Rozwiązanie 2 - niepoprawną długość

Inny błąd nieprawidłowy szablon występuje, gdy nazwa zasobu jest w poprawnym formacie.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Zasobu z poziomu katalogu głównego musi mieć jeden mniej segment w nazwie niż w typie zasób. Każdy z segmentów są zróżnicowane według ukośnika. W poniższym przykładzie Typ zawiera dwa segmenty, a nazwa ma jeden segment, dlatego ma **prawidłową nazwę**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Ale w następnym przykładzie **Nieprawidłowa nazwa** ma on taką samą liczbę segmentów jak typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Typ i nazwę dla zasoby podrzędne mają taką samą liczbę segmentów. Ta liczba segmentów ma sens, ponieważ Pełna nazwa i typ dla elementu podrzędnego zawiera nazwy nadrzędnego i typu. W związku z tym Pełna nazwa nadal ma jeden segment mniej niż pełny typ.

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

Wprowadzenie segmenty odpowiednie może być trudne z typami Menedżera zasobów, które są stosowane do dostawcy zasobów. Na przykład stosowanie blokady zasobu w witrynie sieci web wymaga typu z czterech segmentów. W związku z tym nazwa jest trzy segmenty:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Rozwiązanie 3 - parametru jest nieprawidłowa

Jeśli podano wartość parametru, który nie jest jednym z dozwolonych wartości, pojawi się komunikat podobny do poniższego błędu:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Double Sprawdź dozwolonych wartości w szablonie, a następnie podaj jeden podczas wdrażania. Aby dowiedzieć się więcej o wartości dozwolony parametrów, zobacz [sekcji parametrów szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Rozwiązanie 4 - zbyt wiele grup zasobów obiektu docelowego

Jeśli określisz więcej niż pięć grup zasobów obiektu docelowego w pojedyncze wdrożenie, zostanie wyświetlony ten błąd. Należy rozważyć konsolidację liczba grup zasobów w danym wdrożeniu lub wdrażania niektóre szablony jako oddzielnych wdrożeń. Aby uzyskać więcej informacji, zobacz [wdrażania zasobów platformy Azure do więcej niż jedną subskrypcję lub grupę zasobów](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Wykryto rozwiązania 5 - wykryto zależność cykliczną

Ten błąd jest wyświetlany, gdy zasoby są zależne od siebie nawzajem w sposób, który uniemożliwia wdrożenie, uruchamianie. Kombinacja współzależności sprawia, że dwa lub więcej zasobów, poczekaj, aż inne zasoby, które oczekują na również. Na przykład zasób1 zależy od resource3 zasób2 zależy od zasób1 i resource3 zależy od zasób2. Można zwykle rozwiązać ten problem, usuwając zbędne zależności.

Aby rozwiązać zależność cykliczną:

1. W szablonie należy znaleźć zasobu określonego w zależność cykliczną. 
2. Dla tego zasobu, należy zbadać **dependsOn** właściwość i wszelkie przypadki użycia **odwołania** funkcji, aby wyświetlić zasoby zależy. 
3. Sprawdź te zasoby, aby zobaczyć, jakie zasoby są one zależne od. Postępuj zgodnie z zależności, dopóki nie zauważysz, że zasób, który jest zależny od oryginalny zasób.
5. Zasoby związane z zależność cykliczną dokładnie sprawdź wszystkie zastosowania **dependsOn** właściwości, aby zidentyfikować wszelkie zależności, które nie są wymagane. Usuń te zależności. Jeśli nie wiesz, czy zależność jest potrzebny, spróbuj go usunąć. 
6. Ponownie wdróż szablon.

Usuwanie wartości z **dependsOn** właściwości, które mogą powodować błędy podczas wdrażania szablonu. Jeśli wystąpi błąd, Dodaj zależności do szablonu. 

Jeśli takie podejście nie rozwiąże zależność cykliczną, należy wziąć pod uwagę przeniesienie część logiki wdrożenia do zasoby podrzędne (np. rozszerzenia lub ustawienia konfiguracji). Skonfiguruj te zasoby podrzędne wdrożyć po zasoby zaangażowane w zależność cykliczną. Na przykład załóżmy, że wdrożysz dwie maszyny wirtualne, ale należy ustawić właściwości na każdym z nich, które odwołują się do drugiego. Można je wdrożyć w następującej kolejności:

1. vm1
2. vm2
3. Rozszerzenie maszyny vm1 zależy od tego, vm1 i vm2. Rozszerzenie ustawia wartości maszyny vm1, który otrzymuje od maszyny vm2.
4. Rozszerzenia dla maszyny vm2, zależy od vm1 i vm2. Rozszerzenie ustawia wartości dla maszyny vm2, która otrzymuje od vm1.

To samo podejście działa w przypadku aplikacji usługi App Service. Należy rozważyć przeniesienie wartości konfiguracji do zasobu podrzędnego zasób aplikacji. Można wdrożyć dwie aplikacje internetowe w następującej kolejności:

1. webapp1
2. webapp2
3. Konfiguracja dla webapp1 zależy od tego, webapp1 i webapp2. Zawiera ustawienia aplikacji przy użyciu wartości z webapp2.
4. Konfiguracja dla webapp2 zależy od tego, webapp1 i webapp2. Zawiera ustawienia aplikacji przy użyciu wartości z webapp1.
