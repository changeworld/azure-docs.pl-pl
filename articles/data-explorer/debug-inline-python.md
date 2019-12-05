---
title: Debuguj język zapytań Kusto w języku Python przy użyciu VS Code-Azure Eksplorator danych
description: Dowiedz się, jak debugować wbudowane środowisko Python Kusto Query Language (KQL) przy użyciu VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: f0bf81ce6392e1650d9986d81a55eac9c416c24d
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822905"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debuguj język zapytań Kusto w języku Python przy użyciu VS Code

Usługa Azure Eksplorator danych obsługuje uruchamianie kodu w języku Python osadzonym w programie Kusto Query Language przy użyciu [wtyczki Python ()](/azure/kusto/query/pythonplugin). Środowisko uruchomieniowe wtyczki jest hostowane w piaskownicy, izolowanym i bezpiecznym środowisku języka Python. Funkcja wtyczki Python () rozszerza natywne funkcje języka zapytań Kusto za pomocą dużego archiwum pakietów OSS Python. To rozszerzenie umożliwia uruchamianie zaawansowanych algorytmów, takich jak uczenie maszynowe, sztuczna inteligencja, statystyki i szeregi czasowe, w ramach zapytania.

Narzędzia języka zapytań Kusto nie są wygodne do tworzenia i debugowania algorytmów języka Python. W związku z tym opracowuj algorytm w ulubionym środowisku deweloperskim zintegrowanym z językiem Python, takim jak Jupyter, platformy PyCharm itd, VS lub VS Code. Po ukończeniu algorytmu skopiuj i wklej do KQL. Aby usprawnić i usprawnić ten przepływ pracy, platforma Azure Eksplorator danych obsługuje integrację między klientami Kusto Explorer lub interfejsem użytkownika sieci Web i VS Code do tworzenia i debugowania wbudowanego kodu języka Python KQL. 

> [!NOTE]
> Tego przepływu pracy można użyć tylko do debugowania stosunkowo małych tabel wejściowych (do kilku MB). W związku z tym może zajść potrzeba ograniczenia danych wejściowych na potrzeby debugowania.  Jeśli konieczne jest przetworzenie dużej tabeli, należy ograniczyć ją do debugowania przy użyciu `| take`, `| sample`lub `where rand() < 0.x`.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj [dystrybucję Anaconda](https://www.anaconda.com/distribution/#download-section)języka Python. W obszarze **Opcje zaawansowane**wybierz pozycję **Dodaj Anaconda do zmiennej środowiskowej my Path**.
2. Instalacja programu [Visual Studio Code](https://code.visualstudio.com/Download)
3. Zainstaluj [rozszerzenie Python dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Uruchamianie zapytania w aplikacji klienckiej

1. W aplikacji klienckiej poprzedź zapytanie zawierające wbudowaną Python z `set query_python_debug;`
1. Uruchom zapytanie.
    * Eksplorator Kusto: VS Code jest uruchamiany automatycznie przy użyciu skryptu *debug_python. PR* .
    * Interfejs użytkownika sieci Web Kusto: 
        1. Pobierz i Zapisz *debug_python. PR*, *DF. txt*i *kargs. txt*. W oknie wybierz pozycję **Zezwalaj**. **Zapisz** pliki w wybranym katalogu. 

            ![Interfejs użytkownika sieci Web pobiera wbudowane pliki języka Python](media/debug-inline-python/webui-inline-python.png)

        1. Kliknij prawym przyciskiem myszy *debug_python. PR* i Otwórz za pomocą programu vs Code. 
        Skrypt *debug_python. PR* zawiera wbudowany kod języka Python, z zapytania KQL poprzedzonego kodem szablonu w celu zainicjowania wejściowej ramki danych z *DF. txt* i słownika parametrów z *kargs. txt*.    
            
1. W programie VS Code uruchom debuger kodu programu VS: **Debug** > **Rozpocznij debugowanie (F5)** , wybierz pozycję Konfiguracja języka **Python** . Debuger zostanie uruchomiony i automatycznie przejdzie do debugowania kodu wbudowanego.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Jak działa wbudowane debugowanie Python w VS Code?

1. Zapytanie jest analizowane i wykonywane na serwerze, dopóki nie zostanie osiągnięta wymagana klauzula `| evaluate python()`.
1. Piaskownica w języku Python jest wywoływana, ale zamiast uruchamiania kodu, serializować tabelę wejściową, słownik parametrów i kod i wysyła je z powrotem do klienta.
1. Te trzy obiekty są zapisywane w trzech plikach: *DF. txt*, *kargs. txt*i *debug_python. PR* w wybranym katalogu (interfejsie użytkownika sieci Web) lub w kliencie% temp% Directory (Kusto Explorer).
1. Program VS Code jest uruchamiany, wstępnie załadowany z plikiem *debug_python. PR* zawierającym kod prefiksu w celu zainicjowania DF i kargs z odpowiednich plików, a następnie skryptu języka Python osadzonego w zapytaniu KQL.

## <a name="query-example"></a>Przykład zapytania

1. Uruchom następujące zapytanie KQL w aplikacji klienckiej:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Zobacz tabelę wyników:

    | x  | X4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Uruchom to samo zapytanie KQL w aplikacji klienckiej przy użyciu `set query_python_debug;`:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code jest uruchamiany:

    ![Uruchom VS Code](media/debug-inline-python/launch-vs-code.png)

1. VS Code debugowanie i wydrukowanie ramki danych "result" w konsoli debugowania:

    ![Debugowanie kodu VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Mogą występować różnice między obrazem piaskownicy języka Python a instalacją lokalną. [Sprawdź obraz piaskownicy dla określonych pakietów, badając wtyczkę](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
