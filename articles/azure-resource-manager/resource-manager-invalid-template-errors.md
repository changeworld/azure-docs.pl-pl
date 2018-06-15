---
title: Nieprawidłowy szablon Azure błędy | Dokumentacja firmy Microsoft
description: Opisuje sposób rozwiązania błędów szablonu jest nieprawidłowa.
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
ms.openlocfilehash: 59f07b9ba8116cb1a4b5ab50382d89d01a78853b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357694"
---
# <a name="resolve-errors-for-invalid-template"></a>Rozwiązywanie błędów dla szablonu jest nieprawidłowa

W tym artykule opisano sposób rozwiązania błędów szablonu jest nieprawidłowa.

## <a name="symptom"></a>Objaw

W przypadku wdrażania szablonu, pojawi się o błędzie z informacją:

```
Code=InvalidTemplate
Message=<varies>
```

Komunikat o błędzie zależy od typu błędu.

## <a name="cause"></a>Przyczyna

Ten błąd może wynikać z kilku różnych typów błędów. Wymagają one zazwyczaj strukturalnych lub składnia błąd w szablonie.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Rozwiązanie 1 — błąd składni

Jeśli zostanie wyświetlony komunikat o błędzie wskazuje szablonu nie powiodło się sprawdzanie poprawności, mogą mieć problem składni w szablonie.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ten błąd jest proste wyrażenia szablonu mogą być skomplikowanych. Na przykład następujące przypisanie nazwy dla konta magazynu ma jeden zestaw nawiasów, trzy funkcje trzy zestawy nawiasów, jeden zestaw apostrofy i jedną właściwość:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Jeśli nie podasz Składnia dopasowania szablon tworzy wartość, która różni się od masz zamiar.

Po otrzymaniu tego typu błędu, należy dokładnie przejrzeć składni wyrażeń. Należy wziąć pod uwagę przy użyciu edytora JSON, takich jak [programu Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) lub [Visual Studio Code](resource-manager-vs-code.md), który może zostać wyświetlone ostrzeżenie dotyczące błędy składniowe.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Rozwiązanie 2 - niepoprawną długość

Inny błąd nieprawidłowego szablonu występuje, gdy nazwa zasobu nie jest w nieprawidłowym formacie.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Zasób poziomu głównego musi mieć jeden mniej segment w nazwie niż w typie zasób. Każdy z segmentów odróżnia się kreską ułamkową. W poniższym przykładzie Typ zawiera dwa segmenty, a nazwa ma jeden segment, co powoduje **prawidłową nazwę**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

W następnym przykładzie, ale **Nieprawidłowa nazwa** ponieważ ma taką samą liczbę segmentów jako typu.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Typ i nazwa dla zasoby podrzędne mają taką samą liczbę segmentów. Liczba segmentów ma sens, ponieważ Pełna nazwa i typ dla obiekt podrzędny zawiera nadrzędnego nazwę i typ. W związku z tym pełną nazwę nadal ma jeden segment mniej niż pełny typ.

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

Pobieranie segmenty prawo można trudnych z typami Menedżera zasobów, które są stosowane przez dostawców zasobów. Na przykład stosowania Blokada zasobu do witryny sieci web wymaga typu z czterech segmentów. W związku z tym nazwa jest trzy segmenty:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Rozwiązanie 3 — parametr jest nieprawidłowy

Jeśli należy podać wartość parametru, który nie jest jednym z dozwolonych wartości, pojawi się komunikat podobny do następującego błędu:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Dokładnie sprawdź dozwolone wartości w szablonie i podaj podczas wdrażania. Aby uzyskać więcej informacji o wartości dozwolony parametrów, zobacz [sekcji parametrów szablonów usługi Azure Resource Manager](resource-manager-templates-parameters.md).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Rozwiązanie 4 - zbyt wiele grup zasobów obiektu docelowego

Jeśli określono więcej niż pięć grup zasobów obiektu docelowego w jednym wdrożeniu, ten błąd jest wyświetlany. Należy wziąć pod uwagę konsolidowanie liczbę grup zasobów w danym wdrożeniu albo wdrażanie niektóre szablony jako oddzielnych wdrożeń. Aby uzyskać więcej informacji, zobacz [zasobów Azure wdrożyć więcej niż jedną subskrypcję lub grupy zasobów](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Wykryto rozwiązania 5 - zależność cykliczną

Ten błąd jest wyświetlany, gdy zasoby są zależne od siebie nawzajem w sposób uniemożliwiający wdrożenia uruchamianiu. Kombinacja zależnościami sprawia, że dwa lub więcej zasobów, poczekaj, aż inne zasoby, które są również oczekiwania. Na przykład zasób1 zależy od resource3 zasób2 zależy od zasób1 i resource3 zależy od zasób2. Zazwyczaj można rozwiązać ten problem, usuwając zbędne zależności.

Aby rozwiązać zależność cykliczną:

1. W szablonie można znaleźć zasobu określonego w zależność cykliczną. 
2. Dla tego zasobu, sprawdź **dependsOn** właściwości i wszelkie zastosowania **odwołania** sprawdzić zasobów, do których zależy od funkcji. 
3. Sprawdź, czy te zasoby, aby wyświetlić zasobów, do których one zależą. Wykonaj zależności, dopóki zauważysz z zasobem, który jest zależny od zasobu oryginalnego.
5. Zasoby związane z zależność cykliczną, należy dokładnie zbadać wszystkie użycia **dependsOn** właściwości, aby zidentyfikować wszelkie zależności, które nie są wymagane. Usuń te zależności. Jeśli nie wiesz, czy zależność jest potrzebny, spróbuj go usunąć. 
6. Należy ponownie wdrożyć szablon.

Usuwanie wartości z **dependsOn** właściwości, które mogą powodować błędy podczas wdrażania szablonu. Jeśli wystąpi błąd, Dodaj zależności do szablonu. 

Jeśli takie podejście nie rozwiąże zależność cykliczną, rozważ migrację część logiki wdrażania zasobów podrzędnych (na przykład rozszerzenia lub ustawienia konfiguracji). Skonfiguruj te zasoby podrzędne do wdrożenia po zasoby związane z zależność cykliczną. Na przykład wdrażasz dwóch maszyn wirtualnych, ale należy ustawić na każdej z nich, która odwołuje się do innych właściwości. Można je wdrożyć w następującej kolejności:

1. vm1
2. vm2
3. Rozszerzenie na vm1 zależy od vm1 i maszyny vm2. Rozszerzenie ustawia wartości vm1, który otrzymuje od maszyny vm2.
4. Rozszerzenie maszyny vm2 zależy od vm1 i maszyny vm2. Rozszerzenie ustawia wartości maszyny vm2, który otrzymuje od vm1.

Te same podejście działa w przypadku aplikacji usługi App Service. Rozważ przeniesienie wartości konfiguracji do zasobu podrzędnego zasobów aplikacji. Można wdrożyć dwie aplikacje sieci web w następującej kolejności:

1. webapp1
2. webapp2
3. Konfiguracja webapp1 zależy od webapp1 i webapp2. Zawiera ustawienia aplikacji wartościami z webapp2.
4. Konfiguracja webapp2 zależy od webapp1 i webapp2. Zawiera ustawienia aplikacji wartościami z webapp1.
