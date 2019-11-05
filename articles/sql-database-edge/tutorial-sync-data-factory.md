---
title: Synchronizuj dane z Azure SQL Database Edge przy użyciu Azure Data Factory | Microsoft Docs
description: Informacje o synchronizowaniu danych między Azure SQL Database Edge i magazynem obiektów blob platformy Azure
keywords: Baza danych SQL Database, synchronizacja danych z usługi SQL Database Edge, Fabryka danych programu SQL Database
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501323"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>Samouczek: synchronizacja danych z SQL Database Edge do usługi Azure Blob Storage za pomocą Azure Data Factory

W tym samouczku użyjesz Azure Data Factory do przyrostowego synchronizowania danych z tabeli w wystąpieniu Azure SQL Database Edge z magazynem obiektów blob platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli baza danych lub tabela nie została jeszcze utworzona w ramach wdrożenia Azure SQL Database Edge, użyj jednej z następujących metod, aby ją utworzyć:

* Użyj [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) lub [Azure Data Studio](/sql/azure-data-studio/download/) , aby nawiązać połączenie z SQL Database Edge i wykonać skrypt SQL w celu utworzenia bazy danych i tabeli.
* Utwórz bazę danych SQL i tabelę przy użyciu narzędzia [sqlcmd](/sql/tools/sqlcmd-utility/) , bezpośrednio łącząc się z modułem SQL Database Edge. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z aparatem bazy danych przy użyciu narzędzia sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Użyj sqlpackage. exe, aby wdrożyć plik dacpac do kontenera krawędzi SQL Database. Może to być zautomatyzowane przez określenie identyfikatora URI pliku sqlpackage jako części konfiguracji żądanych właściwości modułów lub bezpośrednio za pomocą narzędzia klienta sqlpackage. exe do wdrożenia dacpac na SQL Database Edge.

    Aby pobrać pakiet sqlpackage, zobacz [pobieranie i Instalowanie sqlpackage](/sql/tools/sqlpackage-download/). Dostępne są następujące przykładowe polecenia dla elementu sqlpackage. exe, ale zapoznaj się z dokumentacją pakietu sqlpackage, aby uzyskać więcej informacji.

    **Utwórz dacpac**:

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **Zastosuj dacpac**:

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Utwórz tabelę SQL i procedurę służącą do przechowywania i aktualizowania poziomów znaku wodnego

Tabela znaków wodnych służy do przechowywania ostatniej sygnatury czasowej, do której dane zostały już zsynchronizowane z usługą Azure Storage. Procedura składowana języka Transact-SQL (T-SQL) służy do aktualizowania tabeli znaków wodnych po każdej synchronizacji. 

Wykonaj następujące polecenia w wystąpieniu SQL Database Edge:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>Tworzenie przepływu pracy Data Factory

W tej sekcji utworzysz potok Azure Data Factory do synchronizowania danych z tabeli w usłudze Azure SQL Database Edge do usługi Azure Blob Storage.

### <a name="create-data-factory-using-the-data-factory-ui"></a>Tworzenie Data Factory przy użyciu interfejsu użytkownika Data Factory

Utwórz Data Factory przy użyciu instrukcji przedstawionych w tym [samouczku](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Tworzenie potoku Data Factory

1. Na stronie **wprowadzenie** w interfejsie użytkownika Data Factory wybierz kafelek **Tworzenie potoku** .

    ![Data Factory — tworzenie potoku](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na stronie **Ogólne** okna **Właściwości** potoku wprowadź nazwę **PeriodicSync** .

3. Dodaj działanie **Lookup (wyszukiwanie** ), aby uzyskać starą wartość limitu. W **przyborniku działania**rozwiń pozycję **ogólne**, przeciągnij & upuść działanie **Lookup (wyszukiwanie** ) do powierzchni projektanta potoku. Zmień nazwę działania na *OldWatermark*.

    ![Wyszukiwanie starego znaku wodnego](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Przejdź do karty **Ustawienia** , a następnie wybierz pozycję **+ Nowy** dla **źródłowego zestawu danych**. W tym kroku utworzysz zestaw danych reprezentujący dane w tabeli znaków wodnych. Ta tabela zawiera stary limit, który był używany w poprzedniej operacji kopiowania.

5. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Server**i wybierz pozycję **Kontynuuj**.  

6. W oknie **Ustawianie właściwości** dla zestawu danych wprowadź *WatermarkDataset* dla nazwy.

7. W polu **połączona usługa**wybierz pozycję **Nowy**, a następnie wykonaj następujące czynności:

    1. Wprowadź *SQLDBEdgeLinkedService* dla **nazwy**.

    2. Wprowadź szczegóły serwera SQL Database Edge dla **nazwy serwera**.

    3. Wprowadź **nazwę bazy danych** z listy rozwijanej.

    4. Wprowadź **nazwę użytkownika** i **hasło**.

    5. Aby przetestować połączenie z wystąpieniem SQL Database krawędzi, wybierz pozycję **Test connection**.

    6. Wybierz pozycję **Utwórz**.

    ![Utwórz połączoną usługę](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Wybierz **przycisk OK**

8. Na karcie **Ustawienia** wybierz pozycję **Edytuj**.

9. Na karcie **połączenie** wybierz pozycję *[dbo]. [ znaki wodne]* dla **tabeli**. Jeśli chcesz wyświetlić podgląd danych w tabeli, wybierz pozycję **Podgląd danych**.

10. Przejdź do edytora potoku, wybierając kartę potok u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie. W oknie właściwości **działania Lookup**upewnij się, że dla pola **zestaw danych źródłowych** został wybrany **WatermarkDataset** .

11. W przyborniku **działania** rozwiń pozycję **Ogólne**, przeciągnij i upuść inne działanie **wyszukiwania** do powierzchni projektanta potoku, a następnie ustaw nazwę na **NewWatermark** na karcie **Ogólne** w oknie właściwości. To działanie wyszukiwania pobiera nową wartość limitu z tabeli zawierającej dane źródłowe do skopiowania do miejsca docelowego.

12. W oknie właściwości drugiego działania **wyszukiwania** przejdź do karty **Ustawienia** , a następnie wybierz pozycję **Nowy** , aby utworzyć zestaw danych, który wskaże tabelę źródłową, która zawiera nową wartość limitu.

13. W oknie **Nowy zestaw danych** wybierz SQL Database wystąpienie krawędzi, a następnie wybierz pozycję **Kontynuuj**.

    1. W oknie **Ustawianie właściwości** wpisz **SourceDataset** ( **Nazwa**). Wybierz pozycję *SQLDBEdgeLinkedService* dla połączonej usługi.

    2. Wybierz ***tabelę, którą chcesz synchronizować*** dla tabeli. Możesz również określić zapytanie dla tego zestawu danych, jak wspomniano w dalszej części samouczka. Zapytanie ma pierwszeństwo przed tabelą określaną w tym kroku.

    3. Kliknij przycisk **OK**.

14. Przejdź do edytora potoku, wybierając kartę potok u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie. W oknie dialogowym właściwości działania **Lookup** (Wyszukiwanie) upewnij się, że dla pola **Zestaw danych źródłowych** wybrano wartość **SourceDataset**.

15. Wybierz opcję **zapytanie** dla pola **Użyj zapytania** , a następnie wprowadź następujące zapytanie po zaktualizowaniu nazwy tabeli w zapytaniu: wybierasz tylko maksymalną wartość sygnatury czasowej z tabeli. Upewnij się, że zaznaczono również **tylko pierwszy wiersz**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Wybierz zapytanie](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. W przyborniku **działania** rozwiń pozycję **Przenieś & Przekształć**, przeciągnij i upuść działanie **kopiowania** z przybornika działania, a następnie ustaw nazwę na **IncrementalCopy**.

17. Połącz obie działania **wyszukiwania** do działania **kopiowania** , przeciągając **zielony przycisk** dołączony do działań **wyszukiwania** do działania **kopiowania** . Zwolnij przycisk myszy, gdy kolor obramowania działania kopiowania zmieni się na niebieski.

18. Wybierz działanie **kopiowania** i upewnij się, że właściwości działania są wyświetlane w oknie **Właściwości** .

19. Przejdź do karty **Źródło** w oknie **Właściwości** i wykonaj następujące czynności:

    1. Wybierz pozycję **SourceDataset** dla pola **Zestaw danych będący źródłem**.

    2. Wybierz pozycję **Zapytanie** dla pola **Użyj zapytania**.

    3. Wprowadź zapytanie SQL dla pola **zapytania** . Przykładowe zapytanie poniżej

    4. Zapytanie SQL:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Przejdź do karty **ujścia** i wybierz pozycję **+ Nowy** dla pola **zestaw danych ujścia** .

21. W tym samouczku magazyn danych ujścia jest typu **Azure Blob Storage**. Wybierz pozycję **Azure Blob Storage**, a następnie wybierz pozycję **Kontynuuj** w oknie **Nowy zestaw danych** .

22. W oknie **Wybieranie formatu** wybierz typ formatu danych, a następnie wybierz pozycję **Kontynuuj**.

23. W oknie **Ustawianie właściwości** wpisz **SinkDataset** (nazwa). W obszarze połączona usługa wybierz pozycję **+ Nowy**. W tym kroku tworzysz połączenie (usługę połączoną) z magazynem **Azure Blob Storage**.

24. W oknie **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące czynności:

    1. Wprowadź *AzureStorageLinkedService* dla nazwy.

    2. Wybierz konto usługi Azure Storage dla **nazwy konta magazynu** z subskrypcją platformy Azure.

    3. Testuj **połączenie** , a następnie wybierz pozycję **Zakończ**.

25. W oknie **Ustawianie właściwości** upewnij się, że wybrano opcję *AzureStorageLinkedService* dla **połączonej usługi**. Następnie wybierz pozycję **Utwórz** i **przycisk OK**.

26. Na karcie **ujścia** wybierz pozycję **Edytuj**.

27. Przejdź do karty **połączenie** w programie *SinkDataset* i wykonaj następujące czynności:

    1. W polu **ścieżka pliku** wprowadź *asdedatasync/incrementalcopy*, gdzie **adftutorial** jest nazwą kontenera obiektów blob, a **incrementalcopy** to nazwa folderu. Utwórz ten kontener, jeśli nie istnieje, lub zastąp go nazwą istniejącego kontenera. Jeśli folder wyjściowy *incrementalcopy* nie istnieje, usługa Azure Data Factory automatycznie go utworzy. Można również za pomocą przycisku **Przeglądaj** pola **Ścieżka pliku** przejść do folderu w kontenerze obiektów blob.

    2. Dla części **pliku** w polu **ścieżka pliku** wybierz pozycję **Dodaj zawartość dynamiczną [Alt + P]** , a następnie *wprowadź @CONCAT("przyrostowy", Potok (). RunId, ". txt")* w otwartym oknie. Następnie wybierz pozycję **Zakończ**. Nazwa pliku jest generowana dynamicznie przy użyciu wyrażenia. Każde uruchomienie potoku ma unikatowy identyfikator. Działanie kopiowania używa identyfikatora uruchomienia do wygenerowania nazwy pliku.

28. Przejdź do edytora **potoku** , wybierając kartę potok u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie.

29. W przyborniku **Działania** rozwiń pozycję **Ogólne**, a następnie przeciągnij i upuść działanie **Stored Procedure** (Procedura składowana) z przybornika **Działania** na powierzchnię projektanta potoku. **Połącz** zielone (Powodzenie) wyjście działania **kopiowania** z działaniem **procedury składowanej**.

30. Wybierz **działanie procedury składowanej** w projektancie potoku i zmień jego nazwę na *SPtoUpdateWatermarkActivity*.

31. Przejdź do karty **konto SQL** , a następnie wybierz pozycję *SQLDBEdgeLinkedService* dla **połączonej usługi**.

32. Przejdź do karty **Procedura składowana** i wykonaj następujące czynności:

    1. W obszarze **nazwa procedury składowanej**wybierz pozycję *[dbo]. [ usp_write_watermark]* .

    2. Aby określić wartości parametrów procedury składowanej, wybierz pozycję Importuj parametr, a następnie wprowadź następujące wartości parametrów:

    |Nazwa|Typ|Wartość|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity ("NewWaterMark"). Output. firstRow. NewWatermarkvalue}|
    |TableName|Ciąg|@ {Activity ("OldWaterMark"). Output. firstRow. TableName}|

33. Aby sprawdzić poprawność ustawień potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **raport weryfikacji potoku** , wybierz pozycję **>>** .

34. Opublikuj jednostki (usługi połączone, zestawy danych i potoki) w usłudze Azure Data Factory, wybierając przycisk **Opublikuj wszystko**. Poczekaj, aż zostanie wyświetlony komunikat o pomyślnym opublikowaniu.

## <a name="trigger-a-pipeline-on-schedule"></a>Wyzwalanie potoku zgodnie z harmonogramem

1. Na pasku narzędzi potoku wybierz pozycję **Dodaj wyzwalacz**, a następnie wybierz pozycję **Nowy/Edytuj**, a następnie wybierz pozycję **+ Nowy**.

2. Nazwij wyzwalacz za pomocą *HourlySync*, wybierz **Typ** jako harmonogram i ustaw **cykl** na co 1 godzinę.

3. Kliknij przycisk **OK**.

4. Wybierz pozycję **Opublikuj wszystkie**.

5. Wybierz pozycję **Wyzwól teraz**.

6. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczny będzie stan uruchomienia potoku, który został wyzwolony za pomocą wyzwalacza ręcznego. Wybierz przycisk **Odśwież** , aby odświeżyć listę.

## <a name="next-steps"></a>Następne kroki

Potok Azure Data Factory w tym samouczku kopiuje dane z tabeli w wystąpieniu SQL Database Edge do lokalizacji w usłudze Azure Blob Storage z częstotliwością co godzinę. Aby dowiedzieć się więcej o używaniu Data Factory w większej liczbie scenariuszy, zapoznaj się z tymi [samouczkami](../data-factory/tutorial-copy-data-portal.md).
