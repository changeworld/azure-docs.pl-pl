---
title: Kopiowanie danych przy użyciu narzędzia do kopiowania danych platformy Azure | Microsoft Docs
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z usługi Azure Blob Storage do usługi SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopiowanie danych z usługi Azure Blob Storage do usługi SQL Database przy użyciu narzędzia do kopiowania danych
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Wersja 2 — wersja zapoznawcza](tutorial-copy-data-tool.md)

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok, który kopiuje dane z usługi Azure Blob Storage do usługi SQL Database. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).
>
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz ogólnie dostępnej wersji 1 usługi Data Factory, zobacz [Wprowadzenie do usługi Data Factory w wersji 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: użyj usługi Blob Storage jako _źródłowego_ magazynu danych. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database**: jako magazynu danych będącego _ujściem_ użyj usługi SQL Database. Jeśli nie masz usługi SQL Database, zobacz instrukcje w temacie [Tworzenie bazy danych SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Przygotuj usługi Blob Storage i SQL Database pod kątem tego samouczka, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom program **Notatnik**. Skopiuj poniższy tekst i zapisz go na dysku w pliku o nazwie **inputEmp.txt**:

    ```
    John|Doe
    Jane|Doe
    ```

2. Utwórz kontener o nazwie **adfv2tutorial** i przekaż plik inputEmp.txt do kontenera. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

1. Poniższy skrypt SQL umożliwia utworzenie tabeli o nazwie **dbo.emp** w Twojej usłudze SQL Database:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Sprawdź, czy ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone na serwerze, na którym działa program SQL Server. To ustawienie umożliwia usłudze Data Factory zapisywanie danych do Twojego wystąpienia programu SQL Server. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

    a. Po lewej stronie wybierz pozycję **Więcej usług**, a następnie wybierz pozycję **Serwery SQL**.

    b. Wybierz swój serwer, a następnie wybierz pozycję **USTAWIENIA** > **Zapora**.

    d. Na stronie **Ustawienia zapory** ustaw wartość opcji **Zezwalaj na dostęp do usług platformy Azure** na **WŁĄCZ**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Z menu po lewej wybierz kolejno pozycje **+ Nowy** > **Dane i analiza** > **Fabryka danych**: 
   
   ![Tworzenie nowej fabryki danych](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
      
     ![Nowa fabryka danych](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:
   
   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych. 
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).

5. W obszarze **wersja** wybierz dla wersji wartość **V2 (wersja zapoznawcza)**.
6. W obszarze **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
7. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
8. Wybierz pozycję **Utwórz**.
9. Na pulpicie nawigacyjnym kafelek **Wdrażanie fabryki danych** pokazuje stan procesu.

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
   
    ![Strona główna fabryki danych](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek narzędzia do kopiowania danych](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. Na stronie **Właściwości** w obszarze **Nazwa zadania**, wprowadź wartość **CopyFromBlobToSqlPipeline**. Następnie wybierz przycisk **Dalej**. Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania. 

    ![Strona właściwości](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz przycisk **Dalej**. Źródło danych znajduje się w usłudze Blob Storage. 

    ![Strona Źródłowy magazyn danych](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage** wykonaj następujące kroki:

    a. W polu **Nazwa połączenia** wprowadź wartość **AzureStorageLinkedService**.

    b. Z listy rozwijanej **Nazwa konta magazynu** wybierz nazwę swojego konta magazynu.

    d. Wybierz opcję **Dalej**. 

    ![Określanie konta magazynu](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    Połączona usługa łączy magazyn danych lub zasoby obliczeniowe z fabryką danych. W takim przypadku tworzysz połączoną usługę magazynu w celu połączenia swojego konta magazynu z magazynem danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa do nawiązywania połączenia z usługą Blob Storage w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie) zawierający dane źródłowe. 

5. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące kroki:
    
    a. Przejdź do folderu **adfv2tutorial/input**.

    b. Wybierz plik **inputEmp.txt**.

    d. Wybierz pozycję **Wybierz**. Możesz również kliknąć dwukrotnie plik **inputEmp.txt**.

    d. Wybierz opcję **Dalej**. 

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. Na stronie **Ustawienia formatu pliku** zwróć uwagę, że narzędzie automatycznie wykrywa ograniczniki kolumn i wierszy. Wybierz opcję **Dalej**. Na tej stronie możesz także wyświetlić podgląd danych i wyświetlić schemat danych wejściowych. 

    ![Ustawienia formatu pliku](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. Na stronie **Docelowy magazyn danych** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Dalej**.

    ![Docelowy magazyn danych](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Na stronie **Określanie usługi Azure SQL Database** wykonaj następujące kroki: 

    a. W polu **Nazwa połączenia** wprowadź wartość **AzureSqlDatabaseLinkedService**.

    b. W polu **Nazwa serwera** wybierz swoje wystąpienie programu SQL Server.

    d. W polu **Nazwa bazy danych** wybierz swoją usługę SQL Database.

    d. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika.

    e. W polu **Hasło** wprowadź hasło użytkownika.

    f. Wybierz opcję **Dalej**. 

    ![Określanie usługi SQL Database](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    Zestaw danych musi być skojarzony z połączoną usługą. Połączona usługa ma parametry połączenia, których usługa Data Factory używa do nawiązywania połączenia z usługą SQL Database w środowisku uruchomieniowym. Zestaw danych określa kontener, folder i plik (opcjonalnie), do którego dane są kopiowane.

9. Na stronie **Mapowanie tabeli** wybierz tabelę **[dbo].[emp]**, a następnie kliknij przycisk **Dalej**. 

    ![Mapowanie tabeli](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Na stronie **Mapowanie schematu** zwróć uwagę, że pierwsza i druga kolumna w pliku wejściowym są mapowane na kolumny **FirstName** i **LastName** tabeli **emp**.

    ![Strona Mapowanie schematu](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia](./media/tutorial-copy-data-tool/settings-page.png)
12. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

    ![Strona podsumowania](./media/tutorial-copy-data-tool/summary-page.png)
13. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-copy-data-tool/deployment-page.png)
14. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Aby powrócić do widoku **Uruchomienia potoków**, wybierz link **Potoki** u góry ekranu. Aby odświeżyć widok, wybierz pozycję **Odśwież**. 

    ![Monitorowanie uruchomień działania](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Wybierz kartę **Edycja** po lewej stronie, aby przełączyć się w tryb edytora. Za pomocą edytora można zaktualizować usługi połączone, zestawy danych i potoki utworzone przez narzędzie. Wybierz pozycję **Kod**, aby wyświetlić kod JSON dla obiektu, który jest obecnie otwarty w edytorze. Aby uzyskać szczegółowe informacje dotyczące edytowania tych jednostek w interfejsie użytkownika usługi Data Factory, zobacz [wersję witryny Azure Portal używaną w tym samouczku](tutorial-copy-data-portal.md).

    ![Karta edytora](./media/tutorial-copy-data-tool/edit-tab.png)
17. Sprawdź, czy dane są wstawiane do tabeli **emp** w usłudze SQL Database.

    ![Weryfikacja danych wyjściowych usługi SQL](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z usługi Blob Storage do usługi SQL Database. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

Aby dowiedzieć się, jak kopiować dane ze środowiska lokalnego do chmury, przejdź do następującego samouczka: 

> [!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-data-tool.md)
