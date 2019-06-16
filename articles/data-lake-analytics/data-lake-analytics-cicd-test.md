---
title: Jak przetestować kod usługi Azure Data Lake Analytics
description: Dowiedz się, jak dodać przypadki testowe do języka U-SQL i rozszerzonego kodu C# dla usługi Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 4532e0c6e8095c9d64897410e0492e2135d8a478
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630113"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testowanie kodu Azure Data Lake Analytics

Usługa Azure Data Lake udostępnia języka U-SQL, który łączy deklaratywne SQL za pomocą imperatywne języka C# do przetwarzania danych w dowolnej skali. W tym dokumencie nauczysz się tworzyć przypadki testowe do języka U-SQL i rozszerzonego kodu C# operatory zdefiniowane przez użytkownika (operatora zdefiniowanego przez użytkownika).

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Skrypt U-SQL jest skompilowany i zoptymalizowane pod kątem kod wykonywalny do uruchomienia na maszynach w chmurze lub na komputerze lokalnym. Proces kompilacji i optymalizacji traktuje cały skrypt U-SQL jako całości. Nie można tego zrobić tradycyjny "unit test" dla każdej instrukcji. Jednak przy użyciu języka U-SQL przetestować zestaw SDK i uruchamiania lokalnego zestawu SDK, możesz zrobić testy poziomu skryptów.

### <a name="create-test-cases-for-u-sql-script"></a>Tworzenie przypadków testowych dla skryptu U-SQL

Usługa Azure Data Lake Tools for Visual Studio umożliwia tworzenie przypadków testowych skryptu U-SQL.

1.  Kliknij prawym przyciskiem myszy skrypt U-SQL w Eksploratorze rozwiązań, a następnie wybierz **tworzenie testu jednostkowego**.
2.  Utwórz nowy projekt testowy, lub wstawić przypadek testowy do istniejącego projektu testowego.

    ![Narzędzia Data Lake Tools for Visual Studio — Utwórz projekt testów języka U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Narzędzia Data Lake Tools for Visual Studio — Utwórz Konfiguracja projektu testowego U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Zarządzanie źródłami danych testowych

Podczas testowania skryptów U-SQL, należy przetestować plików wejściowych. Możesz zarządzać dane testowe, konfigurując **Test źródła danych** właściwości projektu w języku U-SQL. 

Gdy wywołujesz `Initialize()` interfejsu w teście U-SQL, dane tymczasowe lokalnego folderu głównego zestawu SDK jest tworzony w katalogu roboczym projektu testowego, a wszystkie pliki i podfoldery (i pliki w podfolderach) w folderze źródłowym danych testowych, które są kopiowane do folder główny tymczasowe danych lokalnych, przed uruchomieniem przypadki testowe skryptu U-SQL. Możesz dodać więcej folderach źródła danych testowych, dzieląc ścieżka folderu danych testowych za pomocą średnika.

![Usługa Data Lake Tools for Visual Studio — Konfigurowanie projektu testowego źródła danych](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Zarządzanie środowiska bazy danych na potrzeby testowania

Jeśli skryptów U-SQL, użyj lub wykonywać zapytania za pomocą języka U-SQL obiekty bazy danych (na przykład podczas wywoływania procedury składowane), a następnie należy zainicjować środowiska bazy danych przed uruchomieniem testów języka U-SQL. `Initialize()` Interfejsu w teście zestawu SDK U-SQL umożliwia wdrożenie wszystkich baz danych, które są przywoływane przez projekt U-SQL do folderu głównego tymczasowe danych lokalnych w katalogu roboczym projektu testowego. 

Dowiedz się więcej o [jak zarządzać odwołania do projektu bazy danych U-SQL projektu U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Sprawdź wyniki testu

`Run()` Interfejsu zwraca wynik wykonania zadania. 0 oznacza powodzenie i 1 oznacza niepowodzenie. Asercja funkcji języka C# umożliwia również sprawdzić dane wyjściowe. 

### <a name="run-test-cases-in-visual-studio"></a>Uruchamianie przypadków testowych w programie Visual Studio

Projekt testowy skryptu U-SQL jest oparty na języku C# środowiska testów jednostkowych. Po skompilowaniu projektu można uruchomić wszystkich przypadków testowych za pomocą **Eksploratora testów > Lista odtwarzania**. Alternatywnie, kliknij prawym przyciskiem myszy plik CS, a następnie wybierz **Uruchom testy**.

## <a name="test-c-udos"></a>Testowanie obiektów udo C#

### <a name="create-test-cases-for-c-udos"></a>Tworzenie przypadków testowych dla obiektów udo C#

Struktura testu jednostkowego języka C# można użyć do testowania usługi C# udo (operatorów zdefiniowanych przez użytkownika). Podczas testowania udo, należy przygotować odpowiadającego **IRowset** obiektów jako dane wejściowe.

Istnieją dwa sposoby tworzenia obiektu IRowset:

- Ładowanie danych z pliku do utworzenia IRowset:

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

- Dane ze zbierania danych umożliwia tworzenie IRowset:

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

### <a name="verify-test-results"></a>Sprawdź wyniki testu

Po wywołaniu funkcji operatora zdefiniowanego przez użytkownika można sprawdzić wyniki za pośrednictwem schematu i weryfikacja wartości wierszy, za pomocą funkcji assert języka C#. Można użyć przykładowego kodu w przykładowy projekt testu jednostkowego języka U-SQL C# operatory zdefiniowane przez użytkownika za pośrednictwem **Plik > Nowy > Projekt** w programie Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Uruchamianie przypadków testowych w programie Visual Studio

Po skompilowaniu projektu testowego wszystkie przypadki testowe można uruchomić jednak **Eksploratora testów > Lista odtwarzania**, lub kliknij prawym przyciskiem myszy plik CS i wybierz pozycję **Uruchom testy**.

## <a name="run-test-cases-in-azure-devops"></a>Uruchamianie przypadków testowych w DevOps platformy Azure

Zarówno **projekty testowe skryptu U-SQL** i **projekty testowe w języku C# operatory zdefiniowane przez użytkownika** dziedziczą projektów testów jednostkowych języka C#. [Zadanie testowe programu Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) DevOps platformy Azure można uruchamiać te przypadki testowe. 

### <a name="run-u-sql-test-cases-in-azure-devops"></a>Uruchamianie przypadków testowych U-SQL w DevOps platformy Azure

Dla testów języka U-SQL, upewnij się, należy załadować `CPPSDK` na komputerze kompilacji, a następnie przekaż `CPPSDK` ścieżkę do USqlScriptTestRunner (cppSdkFolderFullPath: \@"").

**Co to jest CPPSDK?**

CPPSDK jest pakiet Microsoft Visual C++ 14 i zestaw Windows SDK 10.0.10240.0. To środowisko, w którym wymagany przez środowisko uruchomieniowe języka U-SQL. Możesz uzyskać tego pakietu, w obszarze Azure Data Lake Tools dla folderu instalacyjnego programu Visual Studio:

- W przypadku programu Visual Studio 2015 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- W przypadku programu Visual Studio 2017 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Przygotowanie CPPSDK w agencie kompilacji DevOps platformy Azure**

Najczęstszym sposobem przygotowanie zależności CPPSDK w DevOps platformy Azure jest następująca:

1.  ZIP folder, który zawiera biblioteki CPPSDK.
2.  Sprawdź w pliku zip, aby system kontroli źródła. (Plik zip zapewnia zaewidencjonować wszystkie biblioteki w folderze CPPSDK tak, aby niektóre pliki nie są ignorowane przez ".gitignore").   
3.  Rozpakuj plik zip w potoku kompilacji.
4.  Punkt `USqlScriptTestRunner` do tego folderu rozpakowany na maszynie kompilacji.

### <a name="run-c-udo-test-cases-in-azure-devops"></a>Uruchamianie przypadków testowych operatory zdefiniowane przez użytkownika języka C# w DevOps platformy Azure

Dla testów języka C# operatory zdefiniowane przez użytkownika Upewnij się, że odwoływać się do następujących zestawów, które są niezbędne dla obiektów udo. Jeśli odwołujesz się je za pośrednictwem [pakietu Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), upewnij się, wystarczy dodać zadanie przywracania narzędzia NuGet w potoku kompilacji.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
- [Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

