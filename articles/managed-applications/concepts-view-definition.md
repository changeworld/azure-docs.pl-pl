---
title: Przegląd definicji widoku w usługi Azure Managed Applications | Dokumentacja firmy Microsoft
description: Omówienie pojęć dotyczących tworzenia definicji widoku dla usługi Azure Managed Applications.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478747"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Widok definicji artefaktów w usłudze Azure Managed Applications

Definicja widoku jest opcjonalny artefakt w usłudze Azure Managed Applications. Umożliwia dostosowywanie strony Przegląd i dodać więcej widoków, takich jak metryki i niestandardowe zasoby.

Ten artykuł zawiera omówienie artefaktu definicji widoku i jego możliwości.

## <a name="view-definition-artifact"></a>Wyświetlanie artefaktu definicji

Artefakt definicji widoku, musi nosić **viewDefinition.json** i jest umieszczany na tym samym poziomie co **createUiDefinition.json** i **mainTemplate.json** w zip pakiet, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet ZIP i opublikować definicji aplikacji zarządzanej, zobacz [publikowania definicji aplikacji zarządzanych platformy Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Schemat definicji widoku

**ViewDefinition.json** plik zawiera tylko jeden element najwyższego poziomu `views` właściwość, która jest tablicą widoków. Każdy widok jest wyświetlany w interfejsie użytkownika aplikacji zarządzanej jako element menu oddzielne w spisie treści. Każdy widok zawiera `kind` właściwość, która ustawia typ widoku. Należy określić do jednej z następujących wartości: [Omówienie](#overview), [metryki](#metrics), [CustomResources](#custom-resources). Aby uzyskać więcej informacji, zobacz bieżące [schemat JSON dla viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Przykład JSON definicji widoku:

```json
{
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { },
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Przegląd

`"kind": "Overview"`

Jeśli podasz tego widoku w **viewDefinition.json**, zastępuje ona domyślna strona Omówienie aplikacji zarządzanych.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|nagłówek|Nie|Nagłówek na stronie Przegląd.|
|description|Nie|Opis aplikacji zarządzanej.|
|Polecenia|Nie|Zobacz tablicę dodatkowymi przyciskami paska narzędzi na stronie Przegląd [polecenia](#commands).|

## <a name="metrics"></a>Metryki

`"kind": "Metrics"`

Widok metryk umożliwia zbieranie i agregowanie danych pochodzących z zasobów w aplikacji zarządzanej [metryk usługi Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Nie|Tytuł wyświetlany widok.|
|version|Nie|Wersja platformy, używany do renderowania widoku.|
|Wykresy|Tak|Tablica wykresy metryk strony.|

### <a name="chart"></a>Wykres

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł wykresu.|
|chartType|Nie|Wizualizacja do użycia dla tego wykresu. Domyślnie używa wykresu liniowego. Obsługiwane typy wykresów: `Bar, Line, Area, Scatter`.|
|metrics|Tak|Tablica wskaźników do wykreślenia na tym wykresie. Aby dowiedzieć się więcej o metrykach obsługiwane w witrynie Azure portal, zobacz [metryki obsługiwane z usługą Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metryka

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|name|Tak|Nazwa metryki.|
|aggregationType|Tak|Typ agregacji dla tej metryki. Obsługiwane typy agregacji: `none, sum, min, max, avg, unique, percentile, count`|
|— przestrzeń nazw|Nie|Dodatkowe informacje do użycia podczas określania dostawcy poprawne metryki.|
|resourceTagFilter|Nie|Zasób tagów tablicy (będą rozdzielane `or` programu word) dla metryk, które będą wyświetlane. Ma to zastosowanie, na podstawie filtru typu zasobu.|
|resourceType|Tak|Typ zasobu, dla którego będzie można wyświetlić metryki.|

## <a name="custom-resources"></a>Zasoby niestandardowe

`"kind": "CustomResources"`

Można zdefiniować wiele widoków tego typu. Każdy widok reprezentuje **unikatowy** niestandardowy typ zasobu z dostawcy niestandardowego zdefiniowanego w **mainTemplate.json**. Wprowadzenie do dostawców niestandardowych, zobacz [Azure niestandardowych dostawców (wersja zapoznawcza) — omówienie](custom-providers-overview.md).

W tym widoku, które można wykonać GET PUT, usunąć i OPUBLIKUJ operacji dla danego typu zasobu niestandardowego. Operacje POST może być globalne akcje niestandardowe lub niestandardowe akcje w kontekście tego typu zasobu niestandardowego.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Yes|Tytuł wyświetlany widok. Tytuł powinien być **unikatowy** dla każdego widoku CustomResources w swojej **viewDefinition.json**.|
|version|Nie|Wersja platformy, używany do renderowania widoku.|
|resourceType|Tak|Niestandardowy typ zasobu. Musi być **unikatowy** niestandardowy typ zasobu niestandardowego dostawcy.|
|createUIDefinition|Nie|Tworzenie definicji interfejsu użytkownika schematu dla utworzyć polecenie zasobów niestandardowych. Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md)|
|Polecenia|Nie|Zobacz tablicę dodatkowe przyciski widoku CustomResources [polecenia](#commands).|
|Kolumny|Nie|Tablica kolumn zasobów niestandardowych. Jeśli nie zdefiniowano `name` kolumny będą wyświetlane domyślnie. Kolumna musi zawierać `"key"` i `"displayName"`. Klucz należy podać klucz właściwości, aby wyświetlić w widoku. Jeśli jest zagnieżdżony, użyj kropki jako ogranicznika, na przykład `"key": "name"` lub `"key": "properties.property1"`. Nazwa wyświetlana Podaj nazwę wyświetlaną właściwość do wyświetlenia w widoku. Możesz też podać `"optional"` właściwości. Jeśli ma wartość true, kolumna jest ukryte w widoku domyślnie.|

## <a name="commands"></a>Polecenia

Polecenia jest tablica przycisków na pasku narzędzi dodatkowe, które są wyświetlane na stronie. Każde polecenie reprezentuje akcję POST od dostawcy usługi Azure niestandardowe zdefiniowane w **mainTemplate.json**. Wprowadzenie do dostawców niestandardowych, zobacz [omówienie niestandardowych dostawców Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Yes|Wyświetlana nazwa przycisku polecenia.|
|ścieżka|Yes|Nazwa akcji niestandardowego dostawcy. Akcja musi być zdefiniowany w **mainTemplate.json**.|
|Ikona|Nie|Ikona przycisku polecenia. Lista obsługiwanych ikon jest zdefiniowany w [schematu JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia. Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Kolejne kroki

- Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
- Wprowadzenie do dostawców niestandardowych, zobacz [omówienie niestandardowych dostawców Azure](custom-providers-overview.md).
