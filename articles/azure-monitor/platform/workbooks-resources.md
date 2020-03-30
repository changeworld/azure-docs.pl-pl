---
title: Parametry zasobu skoroszytów monitora Platformy Azure
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658106"
---
# <a name="workbook-resource-parameters"></a>Parametry zasobu skoroszytu

Parametry zasobów umożliwiają pobieranie zasobów w skoroszytach. Jest to przydatne w ustawieniu zakresu, z którego mają być pochodzą dane. Przykładem jest umożliwienie użytkownikom wybrać zestaw maszyn wirtualnych, które wykresy później będzie używać podczas prezentacji danych.

Wartości z selektorów zasobów mogą pochodzić z kontekstu skoroszytu, listy statycznej lub z zapytań programu Azure Resource Graph.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Tworzenie parametru zasobu (zasoby skoroszytu)
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`Applications`
    2. Typ parametru:`Resource picker`
    3. Wymagane:`checked`
    4. Zezwalaj na wielokrotne zaznaczanie:`checked`
5. Pobierz dane z:`Workbook Resources`
6. Uwzględnij tylko typy zasobów:`Application Insights`
7. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

![Obraz przedstawiający tworzenie parametru zasobu przy użyciu zasobów skoroszytu](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Tworzenie parametru zasobu (Azure Resource Graph)
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`Applications`
    2. Typ parametru:`Resource picker`
    3. Wymagane:`checked`
    4. Zezwalaj na wielokrotne zaznaczanie:`checked`
5. Pobierz dane z:`Query`
    1. Typ kwerendy:`Azure Resource Graph`
    2. Subskrypcji:`Use default subscriptions`
    3. W formancie kwerendy dodaj ten fragment kodu
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

![Obraz przedstawiający tworzenie parametru zasobu przy użyciu programu Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Program Azure Resource Graph nie jest jeszcze dostępny we wszystkich chmurach. Upewnij się, że jest on obsługiwany w chmurze docelowej, jeśli wybierzesz takie podejście.

[Dokumentacja usługi Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Tworzenie parametru zasobu (lista JSON)
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`Applications`
    2. Typ parametru:`Resource picker`
    3. Wymagane:`checked`
    4. Zezwalaj na wielokrotne zaznaczanie:`checked`
5. Pobierz dane z:`JSON`
    1. W formancie zawartości dodaj ten fragment kodu json
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Naciśnij niebieski przycisk _aktualizacji._
6. Opcjonalnie ustaw `Include only resource types` do _usługi Application Insights_
7. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.

## <a name="referencing-a-resource-parameter"></a>Odwoływanie się do parametru zasobu
1. Dodaj formant kwerendy do skoroszytu i wybierz zasób usługi Application Insights.
2. Użyj _aplikacji insights_ rozwijanej do powiązania parametru z formantem. W ten sposób ustawia zakres kwerendy do zasobów zwracanych przez parametr w czasie wykonywania.
4. W formancie KQL dodaj ten fragment kodu
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Uruchom kwerendę, aby wyświetlić wyniki. 

![Obraz przedstawiający parametr zasobu, do którego odwołuje się formant kwerendy](./media/workbooks-resources/resource-reference.png)

> Takie podejście może służyć do powiązania zasobów z innymi formantami, takimi jak metryki.

## <a name="resource-parameter-options"></a>Opcje parametrów zasobu
| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `{Applications}` | Wybrany identyfikator zasobu | _/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | Etykieta wybranego zasobu | `acmefrontend` |
| `{Applications:value}` | Wartość wybranego zasobu | _"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication"_ |
| `{Applications:name}` | Nazwa wybranego zasobu | `acmefrontend` |
| `{Applications:resourceGroup}` | Grupa zasobów wybranego zasobu | `acmegroup` |
| `{Applications:resourceType}` | Typ wybranego zasobu | _microsoft.insights/components_ |
| `{Applications:subscription}` | Subskrypcja wybranego zasobu |  |
| `{Applications:grid}` | Siatka przedstawiająca właściwości zasobu. Przydatne do renderowania w bloku tekstu podczas debugowania  |  |

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
