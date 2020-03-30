---
title: Jak przetestować kod usługi Azure Data Lake Analytics
description: Dowiedz się, jak dodać przypadki testowe dla języka U-SQL i rozszerzonego kodu C# dla usługi Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913958"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testowanie kodu usługi Azure Data Lake Analytics

Usługa Azure Data Lake udostępnia język [U-SQL.](data-lake-analytics-u-sql-get-started.md) U-SQL łączy deklaratywne SQL z imperatywem C# do przetwarzania danych w dowolnej skali. W tym dokumencie dowiesz się, jak utworzyć przypadki testowe dla U-SQL i rozszerzony kod operatora zdefiniowanego przez użytkownika (UDO) (C#).

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Skrypt U-SQL jest kompilowany i zoptymalizowany pod kątem kodu wykonywalnego do uruchomienia na platformie Azure lub na komputerze lokalnym. Proces kompilacji i optymalizacji traktuje cały skrypt U-SQL jako całość. Nie można wykonać tradycyjnego testu jednostkowego dla każdej instrukcji. Jednak za pomocą SDK testu U-SQL i lokalnego uruchomienia SDK, można wykonać testy na poziomie skryptu.

### <a name="create-test-cases-for-u-sql-script"></a>Tworzenie przypadków testowych dla skryptu U-SQL

Narzędzia usługi Azure Data Lake tools dla programu Visual Studio umożliwiają tworzenie przypadków testowych skryptów U-SQL.

1. Kliknij prawym przyciskiem myszy skrypt U-SQL w Eksploratorze rozwiązań, a następnie wybierz polecenie **Utwórz test jednostkowy**.

1. Utwórz nowy projekt testowy lub wstaw przypadek testowy do istniejącego projektu testowego.

   ![Narzędzia usługi Data Lake Tools for Visual Studio — tworzenie konfiguracji projektu testowego U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Zarządzanie źródłem danych testowych

Podczas testowania skryptów U-SQL, należy przetestować pliki wejściowe. Aby zarządzać danymi testowymi, w **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt U-SQL i wybierz polecenie **Właściwości**. Źródło można wprowadzić w **źródle danych testowych**.

![Narzędzia usługi Data Lake Tools for Visual Studio — konfigurowanie źródła danych testowych projektu](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Po wywołaniu `Initialize()` interfejsu w SDK testu U-SQL tymczasowy folder główny danych lokalnych jest tworzony w katalogu roboczym projektu testowego. Wszystkie pliki i foldery w folderze źródła danych testowych są kopiowane do tymczasowego lokalnego folderu głównego danych przed uruchomieniem przypadków testowych skryptu U-SQL. Można dodać więcej folderów źródła danych testowych, dzieląc ścieżkę folderu danych testowych ze średnikiem.

### <a name="manage-the-database-environment-for-testing"></a>Zarządzanie środowiskiem bazy danych do testowania

Jeśli skrypty U-SQL używają lub kwerendy z obiektami bazy danych U-SQL, należy zainicjować środowisko bazy danych przed uruchomieniem przypadków testowych U-SQL. Takie podejście może być konieczne podczas wywoływania procedur składowanych. Interfejs `Initialize()` w SDK testu U-SQL pomaga wdrożyć wszystkie bazy danych, do których odwołuje się projekt U-SQL do tymczasowego folderu głównego danych lokalnych w katalogu roboczym projektu testowego.

Aby uzyskać więcej informacji na temat zarządzania odwołaniami do projektu bazy danych U-SQL dla projektu U-SQL, zobacz [Odwoływanie się do projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Weryfikowanie wyników badań

Interfejs `Run()` zwraca wynik wykonania zadania. *0* oznacza sukces, a *1* oznacza porażkę. Można również użyć funkcji assert języka C#, aby zweryfikować dane wyjściowe.

### <a name="run-test-cases-in-visual-studio"></a>Uruchamianie przypadków testowych w programie Visual Studio

Projekt testu skryptu U-SQL jest zbudowany na podstawie struktury testów jednostkowych języka C#. Po utworzeniu projektu wybierz pozycję **Przetestuj** > **Eksploratora testów systemu****Windows** > . Przypadki testowe można uruchamiać w **Eksploratorze testów**. Możesz też kliknąć prawym przyciskiem myszy plik cs w teście jednostkowym i wybrać **opcję Uruchom testy**.

## <a name="test-c-udos"></a>Test C# UDOs

### <a name="create-test-cases-for-c-udos"></a>Tworzenie przypadków testowych dla UDO języka C#

Można użyć struktury testów jednostkowych języka C#, aby przetestować operatory zdefiniowane przez użytkownika języka C#(UDOs). Podczas testowania UDO, należy przygotować odpowiednie **IRowset** obiektów jako dane wejściowe.

Istnieją dwa sposoby tworzenia obiektu **IRowset:**

- Ładowanie danych z pliku w celu **utworzenia zestawu IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Użyj danych z kolekcji danych, aby utworzyć **IRowset:**

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Weryfikowanie wyników badań

Po wywołaniu funkcji UDO, można zweryfikować wyniki za pomocą schematu i weryfikacji wartości zestawu wierszy przy użyciu funkcji assert języka C#. Można dodać **U-SQL C# UDO Unit Test Project** do rozwiązania. W tym celu wybierz **pozycję Plik > nowy projekt >** w programie Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Uruchamianie przypadków testowych w programie Visual Studio

Po utworzeniu projektu wybierz pozycję **Przetestuj** > **Eksploratora testów systemu****Windows** > . Przypadki testowe można uruchamiać w **Eksploratorze testów**. Możesz też kliknąć prawym przyciskiem myszy plik cs w teście jednostkowym i wybrać **opcję Uruchom testy**.

## <a name="run-test-cases-in-azure-pipelines"></a>Uruchamianie przypadków testowych w potokach platformy Azure<a name="run-test-cases-in-azure-devops"></a>

Zarówno **projekty testów skryptu U-SQL,** jak i **projekty testów UDO języka C#dziedziczą** projekty testów jednostkowych języka C#. [Zadanie testowe programu Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) w potokach platformy Azure można uruchomić te przypadki testowe.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Uruchamianie przypadków testowych U-SQL w potokach platformy Azure

W przypadku testu U-SQL upewnij `CPPSDK` się, że ładujesz `CPPSDK` komputer `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`kompilacji, a następnie przekaż ścieżkę do .

#### <a name="what-is-cppsdk"></a>Co to jest CPPSDK?

CPPSDK to pakiet zawierający pakiet Microsoft Visual C++ 14 i Windows SDK 10.0.10240.0. Ten pakiet zawiera środowisko, które jest potrzebne przez środowisko wykonawcze U-SQL. Ten pakiet można uzyskać w folderze instalacji usługi Azure Data Lake Tools for Visual Studio:

- W programie Visual Studio 2015 jest on`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- W programie Visual Studio 2017 jest on`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- W programie Visual Studio 2019 jest on`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Przygotowanie CPPSDK w agencie kompilacji potoków platformy Azure

Najczęstszym sposobem przygotowania zależności CPPSDK w potokach platformy Azure jest następujący:

1. Siąz folder zawierający biblioteki CPPSDK.

1. Zaewidencjonuj plik zip w systemie kontroli źródła. Plik zip zapewnia, że zaewidencjonujesz wszystkie biblioteki w folderze CPPSDK, `.gitignore` aby pliki nie były ignorowane z powodu pliku.

1. Rozpaj plik zip w potoku kompilacji.

1. Wskaż `USqlScriptTestRunner` ten rozpakowany folder na komputerze kompilacji.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Uruchamianie przypadków testowych UDO języka C# w potokach platformy Azure

W przypadku testu UDO języka C#upewnij się, że odwołuje się do następujących zestawów, które są potrzebne dla UDO.

- Interfejsy Microsoft.Analytics.Interfaces
- Microsoft.Analytics.typy
- Test microsoft.analytics.unittest

Jeśli odwołujesz się do nich za pośrednictwem [pakietu Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), upewnij się, że dodasz zadanie NuGet Restore w potoku kompilacji.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Uruchamianie skryptu U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
- [Tworzenie bazy danych U-SQL za pomocą projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
