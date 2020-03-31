---
title: Debug Kusto języka zapytania wbudowanego języka Python przy użyciu kodu VS - Azure Data Explorer
description: Dowiedz się, jak debugować język zapytania Kusto (KQL) wbudowany python przy użyciu kodu VS.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444469"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug Kusto zapytanie języka wbudowanego języka Python przy użyciu kodu VS

Usługa Azure Data Explorer obsługuje uruchamianie kodu języka Python osadzonego w języku zapytań Kusto przy użyciu [wtyczki python().](/azure/kusto/query/pythonplugin) Środowisko wykonawcze wtyczki jest hostowane w piaskownicy, odizolowanym i bezpiecznym środowisku Pythona. Funkcja wtyczki python() rozszerza funkcje natywne języka zapytań Kusto o ogromne archiwum pakietów OSS Python. To rozszerzenie umożliwia uruchamianie zaawansowanych algorytmów, takich jak uczenie maszynowe, sztuczna inteligencja, statystyczne i szeregi czasowe w ramach kwerendy.

Narzędzia języka zapytań Kusto nie są wygodne do tworzenia i debugowania algorytmów Języka Python. W związku z tym należy opracować algorytm w swoim ulubionym środowisku programistycznym zintegrowanym z pythonem, takim jak Jupyter, PyCharm, VS lub VS Code. Po zakończeniu algorytmu należy skopiować i wkleić do KQL. Aby usprawnić i usprawnić ten przepływ pracy, usługa Azure Data Explorer obsługuje integrację między klientami Kusto Explorer lub Web UI i vs code do tworzenia i debugowania wbudowanego kodu języka Python KQL. 

> [!NOTE]
> Ten przepływ pracy może służyć tylko do debugowania stosunkowo małych tabel wejściowych (do kilku MB). W związku z tym może być konieczne ograniczenie danych wejściowych do debugowania.  Jeśli chcesz przetworzyć dużą tabelę, ogranicz `| sample`ją `where rand() < 0.x`do debugowania za pomocą `| take`, lub .

## <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj [Dystrybucję Pythona Anaconda](https://www.anaconda.com/distribution/#download-section). W **obszarze Opcje zaawansowane**wybierz pozycję Dodaj **anakondę do zmiennej środowiskowej PATH**.
2. Instalowanie [kodu programu Visual Studio](https://code.visualstudio.com/Download)
3. Zainstaluj [rozszerzenie Języka Python dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Uruchamianie zapytania w aplikacji klienckiej

1. W aplikacji klienckiej prefiks kwerendy zawierającej wbudowany język Python`set query_python_debug;`
1. Uruchom zapytanie.
    * Kusto Explorer: VS Code jest automatycznie uruchamiany za pomocą skryptu *debug_python.py.*
    * Interfejs użytkownika sieci Web Kusto: 
        1. Pobierz i zapisz *debug_python.py*, *df.txt*i *kargs.txt*. W oknie wybierz pozycję **Zezwalaj**. **Zapisz** pliki w wybranym katalogu. 

            ![Interfejs użytkownika sieci Web pobiera wbudowane pliki python](media/debug-inline-python/webui-inline-python.png)

        1. Kliknij prawym przyciskiem myszy *debug_python.py* i otwórz za pomocą kodu VS. 
        Skrypt *debug_python.py* zawiera wbudowany kod Pythona z kwerendy KQL, poprzedzony kodem szablonu w celu zainicjowania wejściowego elementu dataframe z *pliku df.txt* i słownika parametrów z *pliku kargs.txt*.    
            
1. W programie VS code uruchom debuger kodu VS: **Debugowanie** > **startowe debugowania (F5),** wybierz konfigurację **języka Python.** Debuger uruchomi i automatycznie punkt przerwania do debugowania kodu wbudowanego.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Jak działa debugowanie wbudowanego języka Python w programie VS Code?

1. Kwerenda jest analizowana i wykonywana na serwerze, dopóki nie zostanie osiągnięta wymagana `| evaluate python()` klauzula.
1. Izolowanie języka Python jest wywoływane, ale zamiast uruchamiania kodu, serializuje tabelę wejściową, słownik parametrów i kod i wysyła je z powrotem do klienta.
1. Te trzy obiekty są zapisywane w trzech plikach: *df.txt*, *kargs.txt*i *debug_python.py* w wybranym katalogu (Web UI) lub w katalogu klienta %TEMP% (Kusto Explorer).
1. Kod VS jest uruchamiany, wstępnie załadowany plikiem *debug_python.py,* który zawiera kod prefiksu do inicjowania df i kargs z ich odpowiednich plików, a następnie skrypt Pythona osadzony w kwerendzie KQL.

## <a name="query-example"></a>Przykład kwerendy

1. Uruchom następującą kwerendę KQL w aplikacji klienckiej:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Zobacz tabelę wyników:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Uruchom tę samą kwerendę KQL w aplikacji klienckiej przy użyciu: `set query_python_debug;`

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. Kod VS jest uruchamiany:

    ![uruchom kod VS](media/debug-inline-python/launch-vs-code.png)

1. Debugowanie kodu vs i drukuje "wynik" dataframe w konsoli debugowania:

    ![Debugowanie kodu programu VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Mogą występować różnice między obrazem piaskownicy języka Python a instalacją lokalną. [Sprawdź obraz piaskownicy dla konkretnych pakietów, odwołując się do wtyczki](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
