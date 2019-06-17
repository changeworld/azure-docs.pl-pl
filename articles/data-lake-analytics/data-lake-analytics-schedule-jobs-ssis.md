---
title: Planowanie zadań usługi Azure Data Lake Analytics U-SQL przy użyciu usług SSIS
description: Dowiedz się, jak używać programu SQL Server Integration Services można zaplanować zadania U-SQL.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 6894486118f69e682353142be04821e1d28440e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814669"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Planowanie zadań U-SQL przy użyciu programu SQL Server Integration Services (SSIS)

W tym dokumencie przedstawiono umożliwia organizowanie i tworzenie zadań U-SQL przy użyciu programu SQL Server Integration Service (SSIS). 

## <a name="prerequisites"></a>Wymagania wstępne

[Usługa Azure Feature Pack dla usług Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) zapewnia [zadania usługi Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) i [usługi Azure Data Lake Analytics połączenia Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) czy pomaga połączyć się z usługi Azure Data Lake Usługa analizy. Aby użyć tego zadania, upewnij się, że należy zainstalować:

- [Pobierz i zainstaluj program SQL Server Data Tools (SSDT) dla programu Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalowanie platformy Azure Feature Pack dla usług Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Usługa Azure Data Lake Analytics zadania

Zadanie usługi Azure Data Lake Analytics jest umożliwienie użytkownikom przesyłania zadań U-SQL do konta usługi Azure Data Lake Analytics. 

[Dowiedz się, jak skonfigurować zadanie usługi Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Usługi Azure Data Lake Analytics zadania SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Skrypt U-SQL można uzyskać z różnych miejsc, za pomocą wbudowanych funkcji SSIS i zadań w poniższych scenariuszach pokazują, jak można skonfigurować skrypty U-SQL w przypadku innego użytkownika.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenariusz 1 — Użyj wbudowanego skryptu wywołania w funkcji tvf i przechowywanych procs

W usługi Azure Data Lake Analytics Edytor zadań, skonfiguruj **SourceType** jako **DirectInput**i umieszcza instrukcje języka U-SQL do **USQLStatement**.

Łatwą konserwację i zarządzania kodem, tylko umieścić krótki skrypt U-SQL jako wbudowane skrypty na przykład, można wywołać istniejących funkcji zwracającej tabelę i procedur przechowywanych w bazach danych U-SQL. 

![Edytuj skrypt wbudowany języka U-SQL w ramach zadania SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Powiązanym artykule: [Sposób przekazywania parametrów do procedur składowanych](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenariusz 2 — użyj U-SQL plików w usłudze Azure Data Lake Store

Umożliwia także pliki języka U-SQL w usłudze Azure Data Lake Store za pomocą **zadania systemowego Azure Data Lake Store pliku** w pakiety Azure Feature Pack. Takie podejście umożliwia użycie skryptów przechowywanych w chmurze.

Wykonaj poniższe kroki, aby skonfigurować połączenie między zadania systemowego Azure Data Lake Store pliku i Azure Data Lake Analytics zadania.

### <a name="set-task-control-flow"></a>Przepływ sterowania zadania zestawu

W widoku Projekt pakietu SSIS, Dodaj **zadania systemowego Azure Data Lake Store pliku**, **Kontener pętli Foreach** i **Azure Data Lake Analytics zadań** w pętli Foreach Kontener. Zadania Azure Data Lake Store pliku systemu pozwala na pobieranie plików U-SQL na swoim koncie ADLS do folderu tymczasowego. Kontener pętli Foreach i zadanie analizy usługi Azure Data Lake pomóc przesłać każdy plik języka U-SQL, w obszarze folderu tymczasowego do konta usługi Azure Data Lake Analytics, jako zadania U-SQL.

![Użyj plików U-SQL w usłudze Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Skonfiguruj zadania w systemie plików usługi Azure Data Lake Store

1. Ustaw **operacji** do **CopyFromADLS**.
2. Konfigurowanie **AzureDataLakeConnection**, Dowiedz się więcej o [usługi Azure Data Lake Store połączenia Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Set **AzureDataLakeDirectory**. Wskaż folder przechowywania skryptów U-SQL. Użyj ścieżki względnej względną wobec folderu głównego konta usługi Azure Data Lake Store.
4. Ustaw **docelowy** do folderu, który buforuje pobierane skrypty U-SQL. Ścieżka tego folderu będą obowiązywać w kontenerze pętli Foreach do przesłania zadania U-SQL. 

![Skonfiguruj zadania w systemie plików usługi Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Dowiedz się więcej o zadania systemowego Azure Data Lake Store pliku](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfiguruj Kontener pętli Foreach

1. W **kolekcji** ustaw **modułu wyliczającego** do **moduł wyliczający pliku Foreach**.

2. Ustaw **folderu** w obszarze **konfiguracji modułu wyliczającego** grupy do folderu tymczasowego, który zawiera pobrane skryptów U-SQL.

3. Ustaw **pliki** w obszarze **konfiguracji modułu wyliczającego** do `*.usql` tak, aby Kontener pętli wychwytuje tylko pliki, kończąc `.usql`.

    ![Konfiguruj Kontener pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. W **mapowania zmiennej** strony, Dodaj zmienną zdefiniowane przez użytkownika, aby uzyskać nazwę pliku dla każdego pliku języka U-SQL. Ustaw **indeksu** na 0, aby pobrać nazwy pliku. W tym przykładzie należy zdefiniować zmienną o nazwie `User::FileName`. Ta zmienna będzie służyć do dynamicznego uzyskać połączenia pliku skryptu U-SQL i ustaw nazwę zadania U-SQL w Azure Data Lake Analytics zadania.

    ![Skonfiguruj Kontener pętli Foreach, aby pobrać nazwy pliku](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurowanie usługi Azure Data Lake Analytics zadania 

1. Ustaw **SourceType** do **FileConnection**.

2. Ustaw **FileConnection** do pliku połączenia, który wskazuje na plik obiektów zwróciło Kontener pętli Foreach.
    
    Aby utworzyć to połączenie pliku:

   1. Wybierz  **\<nowe połączenie... >** w ustawieniu FileConnection.
   2. Ustaw **typu użycia** do **istniejący plik**i ustaw **pliku** do ścieżki pliku wszystkie istniejące pliki.

       ![Konfiguruj Kontener pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. W **Menedżerowie połączeń** wyświetlić, kliknij prawym przyciskiem myszy utworzony właśnie teraz połączenia pliku, a następnie wybierz **właściwości**.

   4. W **właściwości** okna, rozwiń węzeł **wyrażeń**i ustaw **ConnectionString** do zmiennej, zdefiniowane w kontenerze pętli Foreach, na przykład `@[User::FileName]`.

       ![Konfiguruj Kontener pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Ustaw **AzureDataLakeAnalyticsConnection** do konta usługi Azure Data Lake Analytics, które chcesz przesyłać zadania do. Dowiedz się więcej o [usługi Azure Data Lake Analytics połączenia Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Ustaw konfiguracje innych zadań. [Dowiedz się więcej](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Użyj **wyrażeń** aby dynamicznie ustawić nazwę zadania U-SQL:

    1. W **wyrażeń** Dodaj nową parę klucz wartość wyrażenia dla **JobName**.
    2. Zmienna zdefiniowana w kontenerze pętli Foreach, na przykład, ustaw wartość dla JobName `@[User::FileName]`.
    
        ![Konfigurowanie usług SSIS wyrażenia dla nazwy zadania U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenariusz użycia 3 U-SQL plików w usłudze Azure Blob Storage

Można użyć plików U-SQL w usłudze Azure Blob Storage za pomocą **zadania pobierania obiektów Blob Azure** w pakiety Azure Feature Pack. Takie podejście umożliwia korzystanie ze skryptów w chmurze.

Kroki są podobne do [Scenariusz 2: Użyj plików U-SQL w usłudze Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Zmień zadanie System plików usługi Azure Data Lake Store na zadanie pobierania obiektów Blob platformy Azure. [Więcej informacji na temat zadań pobierania obiektów Blob Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Przepływ sterowania jest jak poniżej.

![Użyj plików U-SQL w usłudze Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenariusz 4-Użyj U-SQL pliki na komputerze lokalnym

Oprócz używania plików U-SQL, przechowywanych w chmurze, umożliwia także pliki na komputerze lokalnym lub wdrożona za pomocą pakietów SSIS.

1. Kliknij prawym przyciskiem myszy **Menedżerowie połączeń** w SSIS projektu, a następnie wybierz **nowego Menedżera połączeń**.

2. Wybierz **pliku** typu, a następnie kliknij przycisk **Dodaj...** .

3. Ustaw **typu użycia** do **istniejący plik**i ustaw **pliku** do pliku na komputerze lokalnym.

    ![Dodawanie pliku połączenia do pliku lokalnego](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Dodaj **usługi Azure Data Lake Analytics** zadań oraz:
    1. Ustaw **SourceType** do **FileConnection**.
    2. Ustaw **FileConnection** połączenia plik utworzony właśnie teraz.

5. Zakończ innych konfiguracji dla Azure Data Lake Analytics zadania.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenariusz 5 użyj U-instrukcję SQL w zmiennej SSIS

W niektórych przypadkach może być konieczne dynamicznie Generuj instrukcje języka U-SQL. Możesz użyć **SSIS zmiennej** z **wyrażenie SSIS** i inne zadania SSIS, takich jak zadania skryptu, które ułatwiają pozyskanie dynamicznie instrukcji U-SQL.

1. Otwórz okno narzędzia zmienne za pośrednictwem **SSIS > zmienne** menu najwyższego poziomu.

2. Dodaj zmienną SSIS i wartości bezpośrednio lub użyć **wyrażenie** do generowania wartości.

3. Dodaj **usługi Azure Data Lake Analytics zadań** oraz:
    1. Ustaw **SourceType** do **zmiennej**.
    2. Ustaw **SourceVariable** do utworzony właśnie teraz dla zmiennej SSIS.

4. Zakończ innych konfiguracji dla Azure Data Lake Analytics zadania.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenariusz 6-Przekaż parametry do skryptu U-SQL

W niektórych przypadkach możesz chcieć dynamicznie ustawić wartość zmiennej języka U-SQL w skrypcie U-SQL. **Mapowanie parametrów** funkcja Azure Data Lake Analytics zadań Pomoc dotyczącą tego scenariusza. Zazwyczaj są dwa przypadki typowy użytkownik:

- Ustaw dane wejściowe i wyjściowe zmiennych ścieżek plików dynamicznie na podstawie, zgodnie z bieżącą datą i godziną.
- Ustaw parametr dla procedur składowanych.

[Dowiedz się więcej o sposobie ustawiania parametrów dla skryptu U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Kolejne kroki

- [Uruchamianie pakietów SSIS na platformie Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Usługa Azure Feature Pack dla usług Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Planowanie zadań U-SQL przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

