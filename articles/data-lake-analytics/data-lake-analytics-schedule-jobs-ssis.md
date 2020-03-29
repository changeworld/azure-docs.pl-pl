---
title: Planowanie zadań U-SQL usługi Azure Data Lake Analytics przy użyciu usługi SSIS
description: Dowiedz się, jak za pomocą usług SQL Server Integration Services zaplanować zadania U-SQL ze skryptem wbudowanym lub z plików zapytań U-SQL.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 0650fcc5023ac57b193fa23b0dedf65113fd64e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672894"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Planowanie zadań U-SQL przy użyciu usług sql server integration services (SSIS)

W tym dokumencie dowiesz się, jak zorganizować i utworzyć zadania U-SQL przy użyciu usługi integracji programu SQL Server (SSIS). 

## <a name="prerequisites"></a>Wymagania wstępne

[Pakiet funkcji platformy Azure dla usług integracji](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) zapewnia [zadanie usługi Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) i usługę Azure Data Lake Analytics Connection [Manager,](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) która ułatwia łączenie się z usługą Azure Data Lake Analytics. Aby użyć tego zadania, upewnij się, że zostałeś zainstalowany:

- [Pobieranie i instalowanie narzędzi DANYCH programu SQL Server (SSDT) dla programu Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Instalowanie pakietu funkcji platformy Azure dla usług integracyjnych (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Zadanie usługi Azure Data Lake Analytics

Zadanie usługi Azure Data Lake Analytics umożliwia użytkownikom przesyłanie zadań U-SQL do konta usługi Azure Data Lake Analytics. 

[Dowiedz się, jak skonfigurować zadanie usługi Azure Data Lake Analytics](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Zadanie usługi Azure Data Lake Analytics w ssis](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Skrypt U-SQL można uzyskać z różnych miejsc przy użyciu wbudowanych funkcji i zadań SSIS, poniżej scenariusze pokazują, jak można skonfigurować skrypty U-SQL dla różnych przypadków użytkownika.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenariusz 1-Użyj wbudowanego skryptu wywołania tvfs i przechowywane procs

W Edytorze zadań usługi Azure Data Lake Analytics **skonfiguruj program SourceType** jako **DirectInput**i umieść instrukcje U-SQL w **usłudze USQLStatement**.

W celu łatwej konserwacji i zarządzania kodem, tylko umieścić krótki skrypt U-SQL jako skrypty wbudowane, na przykład można wywołać istniejące funkcje tabela wartości i procedur przechowywanych w bazach danych U-SQL. 

![Edytowanie wbudowanego skryptu U-SQL w zadaniu SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Powiązany artykuł: [Jak przekazać parametr do procedur przechowywanych](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenariusz 2-Użyj plików U-SQL w usłudze Azure Data Lake Store

Można również użyć plików U-SQL w sklepie Usługi Azure Data Lake store przy użyciu **zadania systemu plików magazynu usługi Azure Data Lake** store w pakiecie funkcji platformy Azure. Takie podejście umożliwia korzystanie ze skryptów przechowywanych w chmurze.

Wykonaj poniższe kroki, aby skonfigurować połączenie między zadaniami systemu plików usługi Azure Data Lake Store a zadaniem usługi Azure Data Lake Analytics.

### <a name="set-task-control-flow"></a>Ustawianie przepływu sterowania zadaniami

W widoku projektu pakietu SSIS dodaj **zadanie systemu plików magazynu usługi Azure Data Lake**, kontener pętli **foreach** i **zadanie analizy usługi Azure Data Lake w** kontenerze pętli foreach. Zadanie systemu plików usługi Azure Data Lake Store pomaga pobrać pliki U-SQL z konta ADLS do folderu tymczasowego. Kontener pętli Foreach i zadanie usługi Azure Data Lake Analytics pomagają przesłać każdy plik U-SQL w folderze tymczasowym do konta usługi Azure Data Lake Analytics jako zadanie U-SQL.

![Używanie plików U-SQL w magazynie usługi Azure Data Lake](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurowanie zadania systemu plików usługi Azure Data Lake

1. Ustaw **operację** na **CopyFromADLS**.
2. **Skonfiguruj usługę AzureDataLakeConnection**, dowiedz się więcej o [usłudze Azure Data Lake Store Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Ustaw **usługę AzureDataLakeDirectory**. Wskaż folder przechowujący skrypty U-SQL. Użyj ścieżki względnej, która jest względem folderu głównego konta usługi Azure Data Lake Store.
4. Ustaw **miejsce docelowe** na folder, w który buforuje pobrane skrypty U-SQL. Ta ścieżka folderu będzie używana w kontenerze pętli Foreach do przesyłania zadań U-SQL. 

![Konfigurowanie zadania systemu plików usługi Azure Data Lake](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Dowiedz się więcej o zadaniu systemu plików usługi Azure Data Lake .](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017)

### <a name="configure-foreach-loop-container"></a>Konfigurowanie kontenera pętli Foreach

1. Na stronie **Kolekcja** ustaw wystawnik na **Enumerator foreach File Enumerator**. **Enumerator**

2. Ustaw **folder** w grupie **konfiguracji modułu wyliczenia** na folder tymczasowy zawierający pobrane skrypty U-SQL.

3. Ustaw **pliki** w **konfiguracji modułu wyliczającego,** aby `*.usql` kontener `.usql`pętli przechwytywać tylko pliki kończące się na programie .

    ![Konfigurowanie kontenera pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na stronie **Mapowania zmiennych** dodaj zmienną zdefiniowaną przez użytkownika, aby uzyskać nazwę pliku dla każdego pliku U-SQL. Ustaw **indeks** na 0, aby uzyskać nazwę pliku. W tym przykładzie zdefiniuj zmienną o nazwie `User::FileName`. Ta zmienna będzie używana do dynamicznego pobierania połączenia z plikami skryptu U-SQL i ustawiania nazwy zadania U-SQL w zadaniu usługi Azure Data Lake Analytics.

    ![Konfigurowanie kontenera pętli Foreach, aby uzyskać nazwę pliku](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurowanie zadania usługi Azure Data Lake Analytics 

1. Ustaw **typ źródła** na **FileConnection**.

2. Ustaw **połączenie plików** na połączenie z plikiem, które wskazuje obiekty plików zwrócone z kontenera pętli Foreach.
    
    Aby utworzyć to połączenie z plikiem:

   1. Wybierz ** \<pozycję Nowe połączenie...>** w ustawieniu FileConnection.
   2. Ustaw **typ użycia** na **Istniejący plik**i ustaw **ścieżkę** pliku istniejącego pliku.

       ![Konfigurowanie kontenera pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. W widoku **Menedżerowie połączeń** kliknij prawym przyciskiem myszy utworzone połączenie plików, a następnie wybierz polecenie **Właściwości**.

   4. W oknie **Właściwości** rozwiń węzeł **Wyrażenia**i ustaw **connectionstring** na zmienną zdefiniowaną `@[User::FileName]`w kontenerze pętli Foreach, na przykład .

       ![Konfigurowanie kontenera pętli Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Ustaw **usługę AzureDataLakeAnalyticsConnection** na konto usługi Azure Data Lake Analytics, do którego chcesz przesłać zadania. Dowiedz się więcej o [usłudze Azure Data Lake Analytics Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Ustaw inne konfiguracje zadań. [Dowiedz się więcej](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Użyj **wyrażeń,** aby dynamicznie ustawić nazwę zadania U-SQL:

    1. Na stronie **Wyrażenia** dodaj nową parę klucz-wartość wyrażenia dla **JobName**.
    2. Ustaw wartość JobName na zmienną zdefiniowaną w kontenerze `@[User::FileName]`pętli Foreach, na przykład .
    
        ![Konfigurowanie wyrażenia SSIS dla nazwy zadania U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenariusz 3-Użyj plików U-SQL w usłudze Azure Blob Storage

Można użyć plików U-SQL w usłudze Azure Blob Storage przy użyciu **zadania pobierania obiektów Blob platformy Azure** w pakiecie funkcji platformy Azure. Takie podejście umożliwia korzystanie ze skryptów w chmurze.

Kroki są podobne do [scenariusza 2: Użyj plików U-SQL w usłudze Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Zmień zadanie systemu plików usługi Azure Data Lake Store na zadanie pobierania obiektów Blob platformy Azure. [Dowiedz się więcej o zadaniu pobierania obiektów Blob platformy Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Przepływ sterowania jest jak poniżej.

![Używanie plików U-SQL w magazynie usługi Azure Data Lake](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenariusz 4-Użyj plików U-SQL na komputerze lokalnym

Oprócz używania plików U-SQL przechowywanych w chmurze, można również używać plików na komputerze lokalnym lub plików wdrożonych przy użyciu pakietów SSIS.

1. Kliknij prawym przyciskiem myszy **pozycję Menedżerowie połączeń** w projekcie SSIS i wybierz polecenie **Nowy Menedżer połączeń**.

2. Wybierz typ **pliku** i kliknij przycisk **Dodaj...**.

3. Ustaw **typ użycia** na **Istniejący plik**i ustaw **plik** na plik na komputerze lokalnym.

    ![Dodawanie połączenia plików do pliku lokalnego](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Dodaj **zadanie usługi Azure Data Lake Analytics** i:
    1. Ustaw **typ źródła** na **FileConnection**.
    2. Ustaw **połączenie plików** na utworzone właśnie połączenie plików.

5. Zakończ inne konfiguracje dla zadania usługi Azure Data Lake Analytics.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Instrukcja Scenariusz 5-Use U-SQL w zmiennej SSIS

W niektórych przypadkach może być konieczne dynamiczne generowanie instrukcji U-SQL. Można użyć **zmiennej SSIS** z **wyrażeniem SSIS** i innymi zadaniami SSIS, takimi jak zadanie skryptu, aby ułatwić dynamiczne generowanie instrukcji U-SQL.

1. Otwórz okno narzędzia Zmienne za pomocą menu najwyższego poziomu **SSIS > Zmienne.**

2. Dodaj zmienną SSIS i ustaw wartość bezpośrednio lub użyj **wyrażenia** do wygenerowania wartości.

3. Dodaj **zadanie usługi Azure Data Lake Analytics** i:
    1. Ustaw **typ źródła** na **zmienną**.
    2. Ustaw **SourceVariable** do zmiennej SSIS utworzonej właśnie teraz.

4. Zakończ inne konfiguracje dla zadania usługi Azure Data Lake Analytics.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Parametry scenariusza 6-Pass do skryptu U-SQL

W niektórych przypadkach można dynamicznie ustawić wartość zmiennej U-SQL w skrypcie U-SQL. **Funkcja mapowania parametrów** w usłudze Azure Data Lake Task — pomoc w tym scenariuszu. Zazwyczaj istnieją dwa typowe przypadki użytkownika:

- Ustaw zmienne ścieżki pliku wejściowego i wyjściowego dynamicznie na podstawie bieżącej daty i godziny.
- Ustaw parametr dla procedur składowanych.

[Dowiedz się więcej o ustawianiu parametrów skryptu U-SQL](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie pakietów SSIS na platformie Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Pakiet funkcji platformy Azure dla usług integracyjnych (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Planowanie zadań U-SQL przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
