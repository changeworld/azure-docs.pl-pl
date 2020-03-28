---
title: Synchronizowanie danych z usługi Azure SQL Database Edge przy użyciu usługi Azure Data Factory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o synchronizowaniu danych między usługą Azure SQL Database Edge a magazynem obiektów blob platformy Azure
keywords: krawędź bazy danych sql, synchronizacja danych z krawędzi bazy danych SQL, fabryka danych krawędzi bazy danych SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: e6fd9e6431137708ba93328a8ed1359b93b4ee1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851710"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Samouczek: synchronizowanie danych z usługi SQL Database Edge do magazynu obiektów blob platformy Azure przy użyciu usługi Azure Data Factory

W tym samouczku użyjesz usługi Azure Data Factory do stopniowego synchronizowania danych z magazynem obiektów blob platformy Azure z tabeli w wystąpieniu usługi Azure SQL Database Edge.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie utworzono jeszcze bazy danych lub tabeli we wdrożeniu usługi Azure SQL Database Edge, użyj jednej z następujących metod, aby utworzyć jedną z następujących metod:

* Użyj [sql server management studio](/sql/ssms/download-sql-server-management-studio-ssms/) lub usługi Azure Data [Studio,](/sql/azure-data-studio/download/) aby połączyć się z usługą SQL Database Edge. Uruchom skrypt SQL, aby utworzyć bazę danych i tabelę.
* Utwórz bazę danych i tabelę SQL przy użyciu [SQLCMD](/sql/tools/sqlcmd-utility/) bezpośrednio łącząc się z modułem sql database edge. Aby uzyskać więcej informacji, zobacz [Łączenie się z aparatem baz danych przy użyciu sqlcmd](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Użyj programu SQLPackage.exe, aby wdrożyć plik pakietu DAC w kontenerze usługi SQL Database Edge. Ten proces można zautomatyzować, określając identyfikator URI pliku SqlPackage jako część żądanej konfiguracji właściwości modułu. Można również bezpośrednio użyć narzędzia klienta SqlPackage.exe do wdrożenia pakietu DAC do krawędzi bazy danych SQL Database.

    Aby uzyskać informacje dotyczące pobierania programu SqlPackage.exe, zobacz [Pobieranie i instalowanie pakietu sqlpackage](/sql/tools/sqlpackage-download/). Poniżej przedstawiono kilka przykładowych poleceń dla programu SqlPackage.exe. Aby uzyskać więcej informacji, zobacz dokumentację programu SqlPackage.exe.

    **Tworzenie pakietu DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Stosowanie pakietu DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Tworzenie tabeli i procedury SQL w celu przechowywania i aktualizowania poziomów znaku wodnego

Tabela znaków wodnych służy do przechowywania ostatniej sygnatury czasowej, do której dane zostały już zsynchronizowane z usługą Azure Storage. Procedura składowana Transact-SQL (T-SQL) służy do aktualizowania tabeli znaków wodnych po każdej synchronizacji.

Uruchom te polecenia w wystąpieniu usługi SQL Database Edge:

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

## <a name="create-a-data-factory-pipeline"></a>Tworzenie potoku fabryki danych

W tej sekcji utworzysz potok usługi Azure Data Factory w celu synchronizacji danych z magazynem obiektów blob platformy Azure z tabeli w usłudze Azure SQL Database Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Tworzenie fabryki danych przy użyciu interfejsu użytkownika fabryki danych

Utwórz fabrykę danych, postępując zgodnie z instrukcjami zawartymi w [tym samouczku](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Tworzenie potoku fabryki danych

1. Na stronie **Rozpocznijmy pracę** interfejsu użytkownika fabryki danych wybierz pozycję **Utwórz potok**.

    ![Tworzenie potoku fabryki danych](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na stronie **Ogólne** okna **Właściwości** potoku wprowadź **polecenie Okresowysynk dla** nazwy.

3. Dodaj działanie odnośnika, aby uzyskać starą wartość znaku wodnego. W okienku **Działania** rozwiń pozycję **Ogólne** i przeciągnij działanie **odnośnika** na powierzchnię projektanta potoku. Zmień nazwę działania na **OldWatermark**.

    ![Dodawanie starego wyszukiwania znaku wodnego](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Przełącz się na kartę **Ustawienia** i wybierz pozycję **Nowy** dla **źródłowego zestawu danych**. Teraz utworzysz zestaw danych do reprezentowania danych w tabeli znaków wodnych. Ta tabela zawiera stary limit, który był używany w poprzedniej operacji kopiowania.

5. W oknie **Nowy zestaw danych** wybierz pozycję Azure SQL **Server**, a następnie wybierz pozycję **Kontynuuj**.  

6. W oknie **Ustaw właściwości** zestawu danych w obszarze **Nazwa**wprowadź zestaw danych **znaku wodnego**.

7. W przypadku **usługi połączonej**wybierz pozycję **Nowy**, a następnie wykonaj następujące czynności:

    1. Pod **nazwą**wprowadź **SQLDBEdgeLinkedService**.

    2. W obszarze **Nazwa serwera**wprowadź szczegóły serwera usługi SQL Database Edge.

    3. Wybierz **nazwę bazy danych** z listy.

    4. Wprowadź **nazwę użytkownika** i **hasło**.

    5. Aby przetestować połączenie z wystąpieniem usługi SQL Database Edge, wybierz opcję **Testuj połączenie**.

    6. Wybierz **pozycję Utwórz**.

    ![Tworzenie usługi połączonej](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Kliknij przycisk **OK**.

8. Na karcie **Ustawienia** wybierz pozycję **Edytuj**.

9. Na karcie **Połączenie** wybierz pozycję **[dbo].[ znak wodny]** dla **tabeli**. Jeśli chcesz wyświetlić podgląd danych w tabeli, wybierz opcję **Podgląd danych**.

10. Przełącz się do edytora potoku, wybierając kartę potoku u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie. W oknie właściwości działania odnośnika upewnij się, że **zestaw danych watermarkdataset** jest zaznaczony na liście **Zestaw danych źródłowych.**

11. W okienku **Działania** rozwiń pozycję **Ogólne** i przeciągnij inne działanie **odnośnika** na powierzchnię projektanta potoku. Ustaw nazwę na **NewWatermark** na karcie **Ogólne** okna właściwości. To działanie odnośnika pobiera nową wartość znaku wodnego z tabeli zawierającej dane źródłowe, dzięki czemu można ją skopiować do miejsca docelowego.

12. W oknie właściwości dla drugiego działania odnośnika przełącz się na kartę **Ustawienia** i wybierz pozycję **Nowy,** aby utworzyć zestaw danych wskazujący tabelę źródłową zawierającą nową wartość znaku wodnego.

13. W oknie **Nowy zestaw danych** wybierz **wystąpienie krawędź bazy danych SQL**, a następnie wybierz pozycję **Kontynuuj**.

    1. W oknie **Ustaw właściwości** w obszarze **Nazwa**wprowadź pozycję **SourceDataset**. W obszarze **Usługa połączona**wybierz pozycję **SQLDBEdgeLinkedService**.

    2. W obszarze **Tabela**zaznacz tabelę, którą chcesz zsynchronizować. Można również określić kwerendę dla tego zestawu danych, zgodnie z opisem w dalszej części tego samouczka. Kwerenda ma pierwszeństwo przed tabelą określoną w tym kroku.

    3. Kliknij przycisk **OK**.

14. Przełącz się do edytora potoku, wybierając kartę potoku u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie. W oknie właściwości działania odnośnika upewnij się, że zestaw danych source jest **zaznaczony** na liście **Źródłowy zestaw danych.**

15. Wybierz **pozycję Kwerenda** w obszarze **Użyj kwerendy**. Zaktualizuj nazwę tabeli w następującej kwerendzie, a następnie wprowadź kwerendę. Wybierasz tylko maksymalną wartość `timestamp` z tabeli. Pamiętaj, aby wybrać **tylko pierwszy wiersz**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![zaznaczanie kwerendy](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. W okienku **Działania** rozwiń pozycję **Przenieś & przekształcanie** i przeciągnij działanie **Kopiuj** z okienka **Działania** na powierzchnię projektanta. Ustaw nazwę działania na **Przyrostowa Kopia**.

17. Połącz oba działania wyszukiwania z działaniem kopiowania, przeciągając do działania kopiowania zielony przycisk dołączony do działania wyszukiwania. Zwolnij przycisk myszy, gdy zobaczysz, że kolor obramowania aktywności Kopiowanie zmieni kolor na niebieski.

18. Wybierz działanie kopiowania i upewnij się, że w oknie **Właściwości** widać właściwości działania.

19. Przełącz się do karty **Źródło** w oknie **Właściwości** i wykonaj następujące czynności:

    1. W polu **Zestaw danych Źródło** wybierz pozycję **SourceDataset**.

    2. W obszarze **Użyj kwerendy**wybierz pozycję **Kwerenda**.

    3. Wprowadź kwerendę SQL w polu **Kwerenda.** Oto przykładowe zapytanie:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Na karcie **Zlew zniechęcenie** wybierz pozycję **Nowy** w obszarze **Zestaw danych ujścia**.

21. W tym samouczku magazyn danych ujścia jest magazyn danych magazynu obiektów Blob platformy Azure. Wybierz **pozycję Magazyn obiektów Blob platformy Azure**, a następnie wybierz pozycję **Kontynuuj** w oknie Nowy **zestaw danych.**

22. W oknie **Wybierz format** wybierz format danych, a następnie wybierz pozycję **Kontynuuj**.

23. W oknie **Ustaw właściwości** w obszarze **Nazwa**wprowadź **polecenie SinkDataset**. W obszarze **Połączona usługa**wybierz pozycję **Nowy**. Teraz utworzysz połączenie (połączony serwis) z magazynem obiektów Blob platformy Azure.

24. W oknie **Nowa usługa połączona (magazyn obiektów blob platformy Azure)** wykonaj następujące kroki:

    1. W polu **Nazwa** wprowadź usługę **AzureStorageLinkedService**.

    2. W obszarze **Nazwa konta usługi Storage**wybierz konto magazynu platformy Azure dla subskrypcji platformy Azure.

    3. Przetestuj połączenie, a następnie wybierz pozycję **Zakończ**.

25. W oknie **Ustaw właściwości** upewnij się, że **usługa AzureStorageLinkedService** została wybrana w obszarze **Usługa połączona**. Wybierz **pozycję Utwórz** i **OK**.

26. Na karcie **Zlew** wybierz pozycję **Edytuj**.

27. Przejdź do karty **Połączenie** sinkDataset i wykonaj następujące kroki:

    1. W **obszarze Ścieżka pliku**wprowadź *asdedatasync/incrementalcopy*, gdzie *asdedatasync* jest nazwą kontenera obiektu blob, a *przyrostowa kopia* jest nazwą folderu. Utwórz kontener, jeśli nie istnieje, lub użyj nazwy istniejącego. Usługa Azure Data Factory automatycznie tworzy *przyrostową kopia* folderu wyjściowego, jeśli nie istnieje. Można również za pomocą przycisku **Przeglądaj** pola **Ścieżka pliku** przejść do folderu w kontenerze obiektów blob.

    2. W części **Plik** **ścieżki Plik**wybierz pozycję Dodaj **zawartość dynamiczną [Alt+P],** a następnie wprowadź ** @CONCAT("Przyrostowa-", potok(). RunId, '.txt')** w oknie, które zostanie otwarte. Wybierz **pozycję Zakończ**. Nazwa pliku jest dynamicznie generowana przez wyrażenie. Każde uruchomienie potoku ma unikatowy identyfikator. Działanie kopiowania używa identyfikatora uruchomienia do wygenerowania nazwy pliku.

28. Przełącz się do edytora potoku, wybierając kartę potoku u góry lub wybierając nazwę potoku w widoku drzewa po lewej stronie.

29. W okienku **Działania** rozwiń rozwiń **Ogólne** i przeciągnij działanie **Procedura składowana** z okienka **Działania** na powierzchnię projektanta potoku. Połącz zielone (sukces) dane wyjściowe działania Kopiowanie do działania procedura składowana.

30. Wybierz **opcję Działanie procedury składowanej** w projektancie potoku i zmień jego nazwę na **SPtoUpdateWatermarkActivity**.

31. Przełącz się na kartę **Konto SQL** i wybierz ***QLDBEdgeLinkedService** w obszarze **Usługa połączona**.

32. Przełącz się na kartę **Procedura przechowywana** i wykonaj następujące czynności:

    1. W obszarze **Nazwa procedury składowanej**wybierz **[dbo].[ usp_write_watermark]**.

    2. Aby określić wartości parametrów procedury składowanej, wybierz **polecenie Importuj parametr** i wprowadź następujące wartości parametrów:

    |Nazwa|Typ|Wartość|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Ciąg|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Aby sprawdzić poprawność ustawień potoku, wybierz **pozycję Sprawdź poprawność** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno Raport **>>** sprawdzania poprawności **potoku,** wybierz opcję .

34. Opublikuj jednostki (połączone usługi, zestawy danych i potoki) w usłudze Azure Data Factory, wybierając przycisk **Opublikuj wszystko.** Poczekaj, aż zostanie wyświetlony komunikat potwierdzający, że operacja publikowania powiodła się.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Wyzwalanie potoku na podstawie harmonogramu

1. Na pasku narzędzi potoku wybierz pozycję **Dodaj wyzwalacz,** wybierz pozycję **Nowy/Edytuj**, a następnie wybierz pozycję **Nowy**.

2. Nazwij wyzwalacz **HourlySync**. W obszarze **Typ**wybierz pozycję **Zaplanuj**. Ustaw **cykl** na co 1 godzinę.

3. Kliknij przycisk **OK**.

4. Wybierz pozycję **Opublikuj wszystkie**.

5. Wybierz **wyzwalacz teraz**.

6. Przejdź do karty **Monitorowanie** po lewej stronie. Widoczny będzie stan uruchomienia potoku, który został wyzwolony za pomocą wyzwalacza ręcznego. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

## <a name="next-steps"></a>Następne kroki

Potok usługi Azure Data Factory w tym samouczku kopiuje dane z tabeli w wystąpieniu usługi SQL Database Edge do lokalizacji w magazynie obiektów Blob platformy Azure raz na godzinę. Aby dowiedzieć się więcej o używaniu usługi Data Factory w innych scenariuszach, zobacz te [samouczki](../data-factory/tutorial-copy-data-portal.md).
