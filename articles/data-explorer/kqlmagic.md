---
title: Użyj notesu programu Jupyter do analizy danych w Eksploratorze danych platformy Azure
description: W tym temacie pokazano, jak analizować dane w Eksploratorze danych platformy Azure za pomocą notesu Jupyter i rozszerzenie Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 896a5d13279c15f0035f214da3d5a7d7e6f1861f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045069"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Użyj rozszerzenia notesu Jupyter i Kqlmagic do analizowania danych w Eksploratorze danych platformy Azure

Notes Jupyter to aplikacja sieci web typu open source, która pozwala tworzyć i udostępniać dokumenty zawierające kodu na żywo, równania, wizualizacje i tekst narracji. Użycie obejmuje czyszczenie danych i transformacji, symulacja wartości liczbowych, modelowanie statystyczne, wizualizacji danych i uczenia maszynowego.
[Notes Jupyter](https://jupyter.org/) obsługuje magic funkcje, które rozszerzają możliwości jądra dzięki obsłudze dodatkowych poleceń. Magiczna KQL to polecenie, które rozszerza możliwości języka Python jądra w notesie Jupyter, aby można było uruchomić zapytań w języku Kusto natywnie. Umożliwia łatwe łączenie języka Python i Kusto język zapytań w celu wykonywania zapytań i wizualizuj dane przy użyciu rozbudowane biblioteki Plot.ly zintegrowana z usługą `render` poleceń. Źródła danych na potrzeby uruchamiania zapytań są obsługiwane. Te źródła danych obejmują Eksploratora danych usługi Azure, usługa eksploracji danych szybka i wysoce Skalowalna do dziennika i danych telemetrycznych, a także dzienniki usługi Azure Monitor i Application Insights. Magiczna KQL współpracuje również z rozszerzeniem notesów usługi Azure, laboratorium Jupyter i programu Visual Studio Code Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto e-mail organizacji, które jest członkiem programu Azure Active Directory (AAD).
- Notes Jupyter zainstalowane na komputerze lokalnym lub korzystanie z notesów usługi Azure i klonowanie przykładu [notesu platformy Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Zainstaluj bibliotekę magic KQL

1. Zainstaluj KQL magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Ten krok za pomocą notesów usługi Azure, nie jest wymagane.

1. Magiczna KQL obciążenia:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Nawiąż połączenie z klastrem usługi Azure Data pomocy

Użyj następującego polecenia, aby nawiązać połączenie *przykłady* bazy danych w serwisie *pomocy* klastra. Dla użytkowników innych niż Microsoft AAD, zastąp nazwę dzierżawy `Microsoft.com` z dzierżawą usługi AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Zapytania i wizualizacji

Kwerendy danych przy użyciu [renderowania operator](/azure/kusto/query/renderoperator) i wizualizuj dane przy użyciu biblioteki ploy.ly. Tego zapytania i wizualizacje udostępnia zintegrowane rozwiązanie używa natywnego KQL. Kqlmagic obsługuje większość wykresy z wyjątkiem `timepivot`, `pivotchart`, i `ladderchart`. Renderowanie jest obsługiwane w przypadku wszystkich atrybutów, z wyjątkiem `kind`, `ysplit`, i `accumulate`. 

### <a name="query-and-render-piechart"></a>Diagram kołowy zapytań i renderowania

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Wykres czasu kwerendy i renderowania

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Te wykresy są interaktywne. Wybierz zakres czasu umożliwiającą powiększenie fragmentu w określonym czasie.

### <a name="customize-the-chart-colors"></a>Dostosowywanie kolorów wykresu

Jeśli nie potrzebujesz domyślnej palety kolorów, dostosować wykresów za pomocą opcji palety. Dostępne palety można znaleźć tutaj: [Wybierz paletę kolorów, wykres wyników zapytania magic KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Aby uzyskać listę palety:

    ```python
    %kql --palettes -popup_window
    ```

1. Wybierz `cool` palety kolorów i ponownie renderowania kwerendy:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Sparametryzuj zapytania za pomocą języka Python

Magiczna KQL umożliwia proste wymiany między język zapytania Kusto i Python. Aby dowiedzieć się więcej: [Sparametryzuj zapytania magic KQL za pomocą języka Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Użyj zmiennej języka Python w zapytaniu KQL

Wartość zmiennej języka Python w zapytaniu służy do filtrowania danych:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Wyniki zapytania należy przekonwertować Pandas DataFrame

Możesz uzyskać dostęp do wyników zapytania KQL w Pandas DataFrame. Dostęp do ostatnich wyników zapytania wykonanych przez zmienną `_kql_raw_result_` i prosty sposób konwertowania wyników na Pandas DataFrame w następujący sposób:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Przykład

W wielu scenariuszach analizy warto utworzyć notesy wielokrotnego użytku, które zawierają wiele zapytań i przesłać wyniki z jednego zapytania do kolejnych zapytań. W poniższym przykładzie użyto zmiennej Python `statefilter` do filtrowania danych.

1. Uruchom zapytanie, aby wyświetlić stany 10 najważniejszych maksymalnej `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Uruchom zapytanie, aby wyodrębnić Stan najwyższego i ustaw ją do zmiennej języka Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Uruchamianie zapytania przy użyciu `let` instrukcji, a zmienna Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Uruchom polecenie pomocy:

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Kolejne kroki

Uruchom polecenie Pomoc, aby zapoznać się z następujących notesów próbki, zawierające obsługiwane funkcje:
- [Rozpoczynanie pracy z usługą KQL magic w Eksploratorze danych platformy Azure](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Rozpoczynanie pracy z usługą KQL magic dla usługi Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Rozpoczynanie pracy z usługą magic KQL dla dzienników usługi Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize KQL magic zapytania za pomocą języka Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Wybierz paletę kolorów, wykres wyników zapytania magic KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
