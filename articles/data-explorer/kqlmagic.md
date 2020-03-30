---
title: Analizowanie danych w Eksploratorze danych platformy Azure za pomocą notesu Jupyter
description: W tym temacie pokazano, jak analizować dane w Eksploratorze danych platformy Azure przy użyciu notesu jupytera i rozszerzenia Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064873"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Analizowanie danych w Eksploratorze danych platformy Azure za pomocą notesu jupyter i rozszerzenia Kqlmagic

Jupyter Notebook to aplikacja internetowa typu open source, która umożliwia tworzenie i udostępnianie dokumentów zawierających kod na żywo, równania, wizualizacje i tekst narracyjny. Użycie obejmuje czyszczenie i przekształcanie danych, symulację numeryczną, modelowanie statystyczne, wizualizację danych i uczenie maszynowe.
[Jupyter Notebook](https://jupyter.org/) obsługuje magiczne funkcje, które rozszerzają możliwości jądra, obsługując dodatkowe polecenia. Magia KQL to polecenie, które rozszerza możliwości jądra Pythona w notebooku Jupyter, dzięki czemu można uruchamiać zapytania języka Kusto natywnie. Możesz łatwo połączyć język zapytań Pythona i Kusto, aby wyszukiwać `render` i wizualizować dane za pomocą bogatej biblioteki Plot.ly zintegrowanej z poleceniami. Obsługiwane są źródła danych dla uruchamiania zapytań. Te źródła danych obejmują Usługę Azure Data Explorer, szybką i wysoce skalowalną usługę eksploracji danych dla danych dziennika i telemetrii, a także dzienniki usługi Azure Monitor i usługi Application Insights. Magia KQL współpracuje również z notesami platformy Azure, laboratorium Jupyter lab i rozszerzeniem Jupyter kodu programu Visual Studio.

## <a name="prerequisites"></a>Wymagania wstępne

- Organizacyjne konto e-mail, które jest członkiem usługi Azure Active Directory (AAD).
- Notes Jupyter zainstalowany na komputerze lokalnym lub użyj notesów platformy Azure i sklonuje przykładowy [notes platformy Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Instalowanie magicznej biblioteki KQL

1. Zainstaluj magię KQL:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Podczas korzystania z notesów platformy Azure ten krok nie jest wymagany.

1. Załaduj magię KQL:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Zmień wersję jądra na Python 3.6, klikając jądro > Zmień jądro > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Łączenie się z klastrem Pomocy Eksploratora danych platformy Azure

Użyj następującego polecenia, aby połączyć się z bazą danych *Samples* hostowanym w klastrze *Pomocy.* W przypadku użytkowników usługi AAD firmy `Microsoft.com` innych niż Microsoft należy zastąpić nazwę dzierżawy dzierżawą usługi AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Zapytanie i wizualizacja

Zapytanie danych przy użyciu [operatora renderowania](/azure/kusto/query/renderoperator) i wizualizować dane przy użyciu biblioteki ploy.ly. Ta kwerenda i wizualizacja zapewnia zintegrowane środowisko, które używa natywnego KQL. Kqlmagic obsługuje większość `timepivot`wykresów z wyjątkiem , `pivotchart`i `ladderchart`. Renderowanie jest obsługiwane ze `kind` `ysplit`wszystkimi `accumulate`atrybutami z wyjątkiem , i . 

### <a name="query-and-render-piechart"></a>Zapytanie i renderowanie piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Kwerenda i wykres czasowy renderowania

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Te wykresy są interaktywne. Wybierz zakres czasu, aby powiększyć określony czas.

### <a name="customize-the-chart-colors"></a>Dostosowywanie kolorów wykresu

Jeśli nie podoba Ci się domyślna paleta kolorów, dostosuj wykresy za pomocą opcji palety. Dostępne palety można znaleźć tutaj: [Wybierz paletę kolorów dla wyniku magicznego wykresu zapytania KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Aby uzyskać listę palet:

    ```python
    %kql --palettes -popup_window
    ```

1. Wybierz `cool` paletę kolorów i ponownie s renderuj kwerendę:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametryzacja kwerendy za pomocą języka Python

Magia KQL pozwala na prostą wymianę między językiem zapytań Kusto a Pythonem. Aby dowiedzieć się więcej: [Parametryzowanie magicznego zapytania KQL za pomocą języka Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Używanie zmiennej języka Python w kwerendzie KQL

Można użyć wartości zmiennej Języka Python w kwerendzie do filtrowania danych:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Konwertowanie wyników kwerendy na pandas DataFrame

Można uzyskać dostęp do wyników kwerendy KQL w Pandas DataFrame. Dostęp do ostatnio wykonanych `_kql_raw_result_` wyników kwerendy przez zmienną i łatwo przekonwertować wyniki pandas DataFrame w następujący sposób:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Przykład

W wielu scenariuszach analizy można utworzyć notesy wielokrotnegoużytowania, które zawierają wiele zapytań i przesyłać wyniki z jednego zapytania do kolejnych zapytań. Poniższy przykład używa zmiennej `statefilter` języka Python do filtrowania danych.

1. Uruchom kwerendę, aby wyświetlić 10 najważniejszych stanów z maksymalną wartością: `DamageProperty`

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Uruchom kwerendę, aby wyodrębnić stan najwyższej i ustawić ją w zmiennej Języka Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Uruchom kwerendę `let` przy użyciu instrukcji i zmiennej Języka Python:

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

> [!TIP]
> Aby otrzymywać informacje o wszystkich `%config Kqlmagic`dostępnych konfiguracjach, użyj . Aby rozwiązywać problemy z błędami kusto i przechwytywać je, takie jak problemy z połączeniem i nieprawidłowe kwerendy,`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Następne kroki

Uruchom polecenie pomocy, aby zapoznać się z następującymi przykładowymi notesami zawierającymi wszystkie obsługiwane funkcje:
- [Wprowadzenie do programu Magia KQL dla Eksploratora danych platformy Azure](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Wprowadzenie do magii KQL dla aplikacji Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Wprowadzenie do magii KQL dla dzienników usługi Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametryzowanie magicznego zapytania KQL za pomocą języka Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Wybierz paletę kolorów dla wyniku magicznego wykresu zapytania KQL](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
