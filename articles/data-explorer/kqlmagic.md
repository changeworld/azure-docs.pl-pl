---
title: Używanie Jupyter Notebook do analizowania danych na platformie Azure Eksplorator danych
description: W tym temacie pokazano, jak analizować dane na platformie Azure Eksplorator danych przy użyciu Jupyter Notebook i rozszerzenia Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ba21ec6ba82ac4984d3c51fc46f88de2cd56b2b2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933691"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Analizowanie danych na platformie Eksplorator danych Azure za pomocą rozszerzenia Jupyter Notebook i Kqlmagic

Jupyter Notebook to aplikacja sieci Web Open Source, która umożliwia tworzenie i udostępnianie dokumentów zawierających kod na żywo, równania, wizualizacje i tekst opisowy. Użycie obejmuje czyszczenie i Przekształcanie danych, symulację liczbową, modelowanie statystyczne, wizualizację danych i uczenie maszynowe.
[Jupyter Notebook](https://jupyter.org/) obsługuje funkcje Magic, które zwiększają możliwości jądra dzięki obsłudze dodatkowych poleceń. KQL Magic to polecenie, które rozszerza możliwości jądra języka Python w Jupyter Notebook, aby można było uruchamiać zapytania w języku Kusto w sposób natywny. Możesz łatwo łączyć język zapytań Python i Kusto, aby wykonywać zapytania i wizualizować dane przy użyciu zaawansowanej biblioteki `render` Plot.ly zintegrowanej z poleceniami. Źródła danych do uruchamiania zapytań są obsługiwane. Te źródła danych obejmują platformę Azure Eksplorator danych, szybką i wysoce skalowalną usługę eksploracji danych na potrzeby danych dzienników i telemetrii, a także Azure Monitor dzienników i Application Insights. KQL Magic działa również z rozszerzeniem Azure Notebooks, Jupyter Lab i Visual Studio Code Jupyter.

## <a name="prerequisites"></a>Wymagania wstępne

- Organizacyjne konto e-mail, które jest członkiem Azure Active Directory (AAD).
- Jupyter Notebook zainstalowany na komputerze lokalnym lub użyj Azure Notebooks i klonowania przykładowego [notesu platformy Azure](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Zainstaluj KQL Magic Library

1. Zainstaluj KQL Magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > W przypadku korzystania z Azure Notebooks ten krok nie jest wymagany.

1. Załaduj KQL:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Zmień wersję jądra na Python 3,6, klikając jądro > zmienić jądra > Python 3,6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Nawiązywanie połączenia z klastrem pomocy usługi Azure Eksplorator danych

Użyj poniższego polecenia, aby nawiązać połączenie z bazą danych *przykładów* hostowaną w klastrze *pomocy* . W przypadku użytkowników usługi AAD innych niż Microsoft należy zastąpić nazwę `Microsoft.com` dzierżawy dzierżawie usługi AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Zapytanie i wizualizacja

Wykonywanie zapytań dotyczących danych przy użyciu [operatora renderowania](/azure/kusto/query/renderoperator) i wizualizacji danych przy użyciu biblioteki Ploy.ly. To zapytanie i wizualizacja udostępnia zintegrowane środowisko, które korzysta z natywnej KQL. Kqlmagic obsługuje większość wykresów z `timepivot`wyjątkiem `pivotchart`,, `ladderchart`i. Renderowanie jest obsługiwane ze wszystkimi atrybutami `ysplit`z wyjątkiem `accumulate` `kind`,, i. 

### <a name="query-and-render-piechart"></a>Zapytania i renderowanie piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Zapytania i renderowanie timechart

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Te wykresy są interaktywne. Wybierz zakres czasu, aby powiększyć do określonego czasu.

### <a name="customize-the-chart-colors"></a>Dostosuj kolory wykresu

Jeśli nie podoba Ci się domyślna paleta kolorów, Dostosuj wykresy przy użyciu opcji palety. Dostępne palety można znaleźć tutaj: [Wybierz paletę kolorów dla wynikowego wykresu zapytania KQL Magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Aby uzyskać listę palet:

    ```python
    %kql --palettes -popup_window
    ```

1. Wybierz paletę `cool` kolorów i ponownie Renderuj zapytanie:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Sparametryzuj zapytania przy użyciu języka Python

KQL Magic umożliwia prostą wymianę między językiem zapytań Kusto i Python. Aby dowiedzieć się więcej: [Sparametryzuj swoje zapytanie Magic KQL w języku Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Używanie zmiennej języka Python w zapytaniu KQL

Możesz użyć wartości zmiennej języka Python w zapytaniu, aby przefiltrować dane:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Konwertuj wyniki zapytania na Pandas Dataframe

Możesz uzyskać dostęp do wyników zapytania KQL w ramce Dataframe. Uzyskuj dostęp do ostatnich wykonanych wyników zapytania `_kql_raw_result_` według zmiennej i łatwo Konwertuj wyniki do Pandas Dataframe w następujący sposób:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Przykład

W wielu scenariuszach analizy można utworzyć notesy wielokrotnego użytku, które zawierają wiele zapytań i podawanie wyników z jednego zapytania do kolejnych zapytań. W poniższym przykładzie przedstawiono użycie zmiennej `statefilter` języka Python do filtrowania danych.

1. Uruchom zapytanie, aby wyświetlić 10 najważniejszych Stanów z maksymalną `DamageProperty`wartością:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Uruchom zapytanie, aby wyodrębnić najwyższy stan i ustawić go na zmienną języka Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Uruchom zapytanie przy użyciu `let` instrukcji i zmiennej języka Python:

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
> Aby uzyskać informacje o wszystkich dostępnych konfiguracjach `%config KQLmagic`, użyj programu. W celu rozwiązywania problemów i przechwytywania błędów Kusto, takich jak problemy z połączeniem i nieprawidłowe zapytania, należy użyć`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Następne kroki

Uruchom polecenie pomocy, aby poznać następujące przykładowe notesy, które zawierają wszystkie obsługiwane funkcje:
- [Wprowadzenie do usługi KQL Magic for Azure Eksplorator danych](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Wprowadzenie do usługi KQL Magic dla Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Wprowadzenie do KQL Magic dla dzienników Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize swoje zapytanie Magic KQL w języku Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Wybierz paletę kolorów dla wynikowego wykresu zapytania KQL Magic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
