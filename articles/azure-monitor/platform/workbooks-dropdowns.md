---
title: Parametry listy rozwijanej skoroszytu Monitora platformy Azure
description: Uprość raportowanie złożone dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom zawierającym parametry listy rozwijanej
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658289"
---
# <a name="workbook-drop-down-parameters"></a>Parametry listy rozwijanej skoroszytu

Listy rozwijane umożliwiają użytkownikowi zbieranie co najmniej jednej wartości wejściowej ze znanego zestawu (na przykład wybierz jedno z żądań aplikacji). Rozwijane zapewniają przyjazny dla użytkownika sposób zbierania dowolnych danych wejściowych od użytkowników. Listy rozwijane są szczególnie przydatne w włączaniu filtrowania w interaktywnych raportach. 

Najprostszym sposobem określenia listy rozwijanej jest podanie listy statycznej w ustawieniu parametrów. Bardziej interesującym sposobem jest dynamiczne uzyskanie listy za pośrednictwem kwerendy KQL. Ustawienia parametrów pozwalają również określić, czy jest to pojedynczy czy wielokrotny wybór, a jeśli jest wybierany z wieloma opcjami, sposób formatowania zestawu wyników (ogranicznik, oferta itp.).

## <a name="creating-a-static-drop-down-parameter"></a>Tworzenie statycznego parametru rozwijanego

1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`Environment`
    2. Typ parametru:`Drop down`
    3. Wymagane:`checked`
    4. Zezwól na: `multiple selection``unchecked`
    5. Pobierz dane z:`JSON`
5. W bloku tekstu JSON Input wstaw ten fragment kodu json:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Naciśnij niebieski `Update` przycisk.
7. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.
8. Środowisko parametr będzie rozwijane z trzech wartości.

    ![Obraz przedstawiający tworzenie statycznego utonięcia](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Tworzenie listy rozwijanej statycznej z grupami elementów
Jeśli wynik kwerendy/json zawiera pole "grupa", na liście rozwijanej zostaną wyświetlone grupy wartości. Postępuj zgodnie z powyższą próbką, ale zamiast tego użyj następującego json:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Tworzenie dynamicznego parametru listy rozwijanej
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz _pozycję Dodaj parametry_ z łączy w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr._
4. W nowym okienku parametrów, które wyskakuje wpisać:
    1. Nazwa parametru:`RequestName`
    2. Typ parametru:`Drop down`
    3. Wymagane:`checked`
    4. Zezwól na: `multiple selection``unchecked`
    5. Pobierz dane z:`Query`
5. W bloku tekstu JSON Input wstaw ten fragment kodu json:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Naciśnij niebieski `Run Query` przycisk.
2. Wybierz "Zapisz" na pasku narzędzi, aby utworzyć parametr.
3. RequestName Parametr będzie rozwijane nazwy wszystkich żądań w aplikacji.

    ![Obraz przedstawiający tworzenie dynamicznego listy rozwijanej](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Odwoływanie się do parametru rozwijanej
### <a name="in-kql"></a>w KQL
1. Dodaj formant kwerendy do skoroszytu i wybierz zasób usługi Application Insights.
2. W edytorze KQL wprowadź ten fragment kodu

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Spowoduje to rozszerzenie czasu oceny kwerendy do:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Uruchom kwerendę, aby wyświetlić wyniki. Opcjonalnie renderuj go jako wykres.

    ![Obraz przedstawiający listy rozwijanej, do którego odwołuje się KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Wartość parametru, etykieta, wybór i grupa
Kwerenda używana w dynamicznym parametrze rozwijanym powyżej zwraca listę wartości, które są wiernie renderowane w liście rozwijanej. Ale co, jeśli chcesz, aby inna nazwa wyświetlana, lub jeden z nich do wyboru? Parametry rozwijane pozwalają na to za pośrednictwem kolumny wartości, etykiety, zaznaczenia i grupy.

W poniższym przykładzie pokazano, jak uzyskać listę zależności usługi Application Insights, których nazwy wyświetlane są stylizowane na emotikony, ma pierwszy wybrany i jest pogrupowany według nazw operacji.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opcje parametrów rozwijanej
| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Wybrana wartość | POBIERZ fabrikamaccount |
| `{DependencyName:label}` | Wybrana etykieta | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | Wybrana wartość | POBIERZ fabrikamaccount |

## <a name="multiple-selection"></a>Wybór wielokrotny
Przykłady do tej pory jawnie ustawić parametr, aby wybrać tylko jedną wartość w rozwijanej. Parametry rozwijane `multiple selection` również obsługują - włączenie to jest `Allow multiple selection` tak proste, jak sprawdzenie opcji. 

Użytkownik ma również możliwość określenia formatu zestawu wyników `delimiter` za `quote with` pomocą ustawień i. Wartość domyślna po prostu zwraca wartości jako kolekcję w tym formularzu: 'a', 'b', 'c'. Mają również możliwość ograniczenia liczby selekcji.

KQL odwołując się do parametru będzie musiał zmienić do pracy z formatem wyniku. Najczęstszym sposobem, aby włączyć `in` go jest za pośrednictwem operatora.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Oto przykład listy rozwijanej wielokrotnego wyboru w pracy:

![Obraz przedstawiający parametr listy rozwijanej wielokrotnego wyboru](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
