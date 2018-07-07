---
title: Jak przetestować kod usługi Azure Data Lake Analytics | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać przypadki testowe dla rozszerzonego kodu C# i języka U-SQL usługi Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889590"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Jak przetestować kod usługi Azure Data Lake Analytics

Usługa Azure Data Lake udostępnia U-SQL jako języka, który łączy deklaratywne SQL za pomocą imperatywne języka C# do przetwarzania danych w dowolnej skali. W tym dokumencie nauczysz się tworzyć przypadki testowe dla rozszerzonego kodu C# operatory zdefiniowane przez użytkownika (User-defined Operator) i języka U-SQL.

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Skrypt U-SQL jest kompilowany zoptymalizowany do kodu wykonywalnego typowe dla i uruchomić na maszynach w chmurze lub na komputerze lokalnym. Proces kompilacji i optymalizacji traktuje cały skrypt U-SQL jako całość do wykonania. Nie można tego zrobić tradycyjny "unit test" dla każdej instrukcji. Jednak przy użyciu języka U-SQL, test zestawu SDK i uruchamiania zestawu SDK lokalnego, możesz wykonać testy poziomu skryptu.

### <a name="create-test-cases-for-u-sql-script"></a>Tworzenie przypadków testowych dla skryptu U-SQL

Usługa Azure Data Lake Tools for Visual Studio zapewnia dobre środowisko ułatwiające tworzenie przypadków testowych skryptu U-SQL.

1.  Kliknij prawym przyciskiem myszy skrypt U-SQL w Eksploratorze rozwiązań i wybierz polecenie **tworzenie testu jednostkowego**.
2.  Konfiguruj, aby utworzyć nowy projekt testowy, lub wstawić przypadek testowy do istniejącego projektu testowego.

    ![Narzędzia Data Lake Tools for Visual Studio Utwórz projekt testów języka u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Narzędzia Data Lake Tools for Visual Studio Utwórz Konfiguracja projektu testowego u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Zarządzanie źródłami danych testowych

Podczas testowania skryptów U-SQL, potrzebne są pliki wejściowe testu. Możesz zarządzać te dane testowe, konfigurując **Test źródła danych** właściwość projektu w języku U-SQL. Gdy wywołujesz `Initialize()` interfejsu w teście U-SQL SDK, tymczasowego folderu lokalnego katalogu głównego danych jest tworzony w katalogu roboczym projektu testowego, a wszystkie pliki i podfoldery (i pliki w podfolderach) w folderze źródłowym danych testowych, które są kopiowane do tymczasowy folder główny lokalnych danych przed uruchomieniem przypadki testowe skryptu U-SQL. Aby dodać więcej folderach źródła danych testowych, plucie ścieżkę folderu danych testowych za pomocą średnika.

![Narzędzia Data Lake Tools for Visual Studio skonfiguruj projektu testowego źródła danych](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Zarządzanie środowiskiem bazy danych dla testu

Jeśli skrypty U-SQL, użyj lub wykonywać zapytania za pomocą obiektów bazy danych U-SQL, na przykład, wywołanie procedury składowane, musisz zainicjować środowisko bazy danych przed uruchomieniem testów języka U-SQL. `Initialize()` Interfejsu w teście U-SQL SDK umożliwia wdrożenie wszystkich baz danych, które są przywoływane przez projekt U-SQL do tymczasowego folderu lokalnego katalogu głównego danych w katalogu roboczym projektu testowego. 

Dowiedz się więcej o [jak zarządzać bazy danych U-SQL projektów odwołania do projektu U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Sprawdź wyniki testu

`Run()` Interfejsu zwraca wynik wykonania zadania, 0 oznacza powodzenie i 1 oznacza, że nie powiodło się. Asercja funkcji języka C# umożliwia również sprawdzić dane wyjściowe. 

### <a name="execute-test-cases-in-visual-studio"></a>Wykonuj przypadki testowe w programie Visual Studio

Projekt testowy skryptu U-SQL jest oparty na języku C# testów jednostkowych. Po kompilacji projektu, można uruchomić wszystkich przypadków testowych za pomocą **Eksploratora testów > Lista odtwarzania**, lub kliknij prawym przyciskiem myszy plik CS i wybierz pozycję **Uruchom testy**.

## <a name="test-c-udos"></a>Testowanie obiektów udo C#

### <a name="create-test-cases-for-c-udos"></a>Tworzenie przypadków testowych dla obiektów udo C#

Środowisko testów jednostkowych języka C# umożliwia testowanie UDOs(User-Defined Operator) C#. Podczas testowania udo, należy przygotować odpowiadającego **IRowset** obiektu jako dane wejściowe.

Istnieją dwa sposoby tworzenia IRowset:

1.  Ładowanie danych z pliku do utworzenia IRowset

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

2.  Użyj danych ze zbierania danych, aby utworzyć IRowset

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

Po wywołaniu funkcji operatora zdefiniowanego przez użytkownika, można sprawdzić wynik za pośrednictwem schematu i weryfikacji wartości wierszy za pomocą funkcji języka C# potwierdzenia. Przykładowy kod może znajdować się w U-SQL C# operatory zdefiniowane przez użytkownika projektu testu jednostkowego przykładowe za pośrednictwem **Plik > Nowy > Projekt** w programie Visual Studio.

### <a name="execute-test-cases-in-visual-studio"></a>Wykonuj przypadki testowe w programie Visual Studio

Po kompilacji projektu testowego, możesz uruchomić wszystkie przypadki testowe jednak **Eksploratora testów > Lista odtwarzania**, lub kliknij prawym przyciskiem myszy plik CS i wybierz pozycję **Uruchom testy**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Uruchamianie przypadków testowych w Visual Studio Team Service

Zarówno **projekt testowy skryptu U-SQL** i **operatory zdefiniowane przez użytkownika języka C# projekt testowy** dziedziczą projektu testu jednostkowego języka C#. [Zadanie programu Visual Studio Test](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) w Visual Studio Team Service te przypadki testowe można uruchomić. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Uruchamianie przypadków testowych U-SQL w programie Visual Studio Team Service

Dla testów języka U-SQL, upewnij się, należy załadować `CPPSDK` na maszynie kompilacji i przebiegu `CPPSDK` ścieżkę do USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Co to jest CPPSDK?**

CPPSDK jest pakiet zawiera program Microsoft Visual C++ 14, a Windows SDK 10.0.10240.0, czyli środowisko wymagane przez środowisko uruchomieniowe języka U-SQL. Możesz uzyskać tego pakietu w obszarze Azure Data Lake Tools dla folderu instalacyjnego programu Visual Studio:

- W przypadku programu Visual Studio 2015 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- W przypadku programu Visual Studio 2017 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Jak przygotować CPPSDK w agencie kompilacji Visual Studio Team Service**

To popularny sposób przygotowania tej zależności CPPSDK w Visual Studio Team Service:

1.  ZIP folder zawiera CPPSDK biblioteki.
2.  Sprawdź w pliku zip, aby system kontroli źródła. (Plik zip może upewnij się, zaewidencjonować wszystkie biblioteki w folderze CPPSDK lub niektóre pliki są ignorowane przez ".gitignore").
3.  Rozpakuj plik zip w potoku kompilacji.
4.  Punkt `USqlScriptTestRunner` do tego folderu rozpakowany na maszynie kompilacji.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Uruchamianie przypadków testowych operatory zdefiniowane przez użytkownika języka C# w Visual Studio Team Service

C# operatory zdefiniowane przez użytkownika testowego upewnij się, że odwołanie poniżej zestawów, które są wymagane przez udo. Jeśli odwołujesz się je za pośrednictwem [pakietu Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), upewnij się, wystarczy dodać zadanie przywracania narzędzia NuGet w potoku kompilacji.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania usługi Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
- [Użyj projektu bazy danych U-SQL do tworzenia bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

