---
title: Kopiowanie danych lokalnych za pomocą narzędzia do kopiowania danych platformy Azure | Microsoft Docs
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 85b721df1e666903c4966ca240c433ded01c06b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopiowanie danych z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage za pomocą narzędzia do kopiowania danych
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Wersja 2 — wersja zapoznawcza](tutorial-hybrid-copy-data-tool.md)

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok, który kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Data Factory](introduction.md).
>
> - Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz ogólnie dostępnej wersji 1 usługi Data Factory, zobacz [Wprowadzenie do usługi Data Factory w wersji 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* albo być *administratorem* subskrypcji platformy Azure. 

Aby wyświetlić swoje uprawnienia do subskrypcji, przejdź do witryny Azure Portal. W prawym górnym rogu wybierz swoją nazwę użytkownika, a następnie wybierz pozycję **Uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. Aby uzyskać przykładowe instrukcje dotyczące dodawania użytkownika do roli, zobacz [Dodawanie ról](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>Program SQL Server 2014, 2016 oraz 2017
Podczas pracy z tym samouczkiem użyjesz lokalnej bazy danych programu SQL Server jako *źródłowego* magazynu danych. Potok usługi Data Factory tworzony w tym samouczku kopiuje dane z tej lokalnej bazy danych programu SQL Server (źródła) do usługi Blob Storage (ujścia). Następnie utworzysz tabelę o nazwie **emp** w bazie danych programu SQL Server i wstawisz kilka przykładowych wpisów do tabeli. 

1. Uruchom program SQL Server Management Studio. Jeśli program nie jest jeszcze zainstalowany na używanej maszynie, przejdź do strony [pobierania programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Połącz się z wystąpieniem programu SQL Server przy użyciu swoich poświadczeń. 

3. Utwórz przykładową bazę danych. W widoku drzewa kliknij prawym przyciskiem myszy pozycję **Bazy danych**, a następnie wybierz pozycję **Nowa baza danych**. 
 
4. W oknie **Nowa baza danych** wprowadź nazwę bazy danych, a następnie wybierz przycisk **OK**. 

5. Aby utworzyć tabelę **emp** i wstawić do niej przykładowe dane, uruchom następujący skrypt zapytania w bazie danych. W widoku drzewa kliknij prawym przyciskiem myszy utworzoną bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Konto magazynu Azure
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (konkretnie usługi Blob Storage) jako magazynu danych: docelowego/ujścia. Jeśli nie masz konta magazynu ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account), aby dowiedzieć się, jak je utworzyć. Potok usługi Data Factory tworzony w tym samouczku kopiuje dane z lokalnej bazy danych programu SQL Server (źródła) do tej usługi Blob Storage (ujścia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta magazynu. Pobierz nazwę i klucz konta magazynu, wykonując następujące kroki: 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure. 

2. W okienku po lewej stronie wybierz pozycję **Więcej usług**. Zastosuj filtrowanie według słowa kluczowego **Magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie kont magazynu](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Na liście kont magazynu odfiltruj swoje konto magazynu, jeśli to konieczne. Następnie wybierz swoje konto magazynu. 

4. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.

    ![Klawisze dostępu](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem. 

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial 
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Blob Storage. 

1. W oknie **Konto magazynu** przełącz się do widoku **Przegląd**, a następnie wybierz pozycję **Obiekty blob**. 

    ![Wybieranie opcji Obiekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. W oknie **Blob Service** wybierz pozycję **Kontener**. 

    ![Przycisk kontenera](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. W oknie **Nowy kontener** w polu **Nazwa** wpisz nazwę **adftutorial**, a następnie wybierz pozycję **OK**. 

    ![Nowy kontener](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Kliknij pozycję **adftutorial** na liście kontenerów.

    ![Wybór kontenera](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Pozostaw otwarte okno **kontenera** dla **adftutorial**. Będzie ona używana do weryfikacji danych wyjściowych na końcu tego samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.

    ![Okno kontenera](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Z menu po lewej stronie wybierz kolejno pozycje **Nowy** > **Dane i analiza** > **Fabryka danych**. 
   
   ![Tworzenie nowej fabryki danych](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
      
     ![Nowa fabryka danych](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).
  
   ![Nazwa nowej fabryki danych](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych. 
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
      Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).
5. W obszarze **Wersja** wybierz pozycję **V2 (wersja zapoznawcza)**.
6. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez usługę Data Factory mogą mieścić się w innych lokalizacjach/regionach.
7. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
8. Wybierz pozycję **Utwórz**.
9. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**:

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
    ![Strona główna fabryki danych](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. Wybierz pozycję **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić interfejs użytkownika usługi Data Factory. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz pozycję **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek narzędzia do kopiowania danych](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Na stronie **Właściwości** narzędzia do kopiowania danych wprowadź w polu **Nazwa zadania** wartość **CopyFromOnPremSqlToAzureBlobPipeline**. Następnie wybierz przycisk **Dalej**. Narzędzie do kopiowania danych tworzy potok o nazwie określonej w tym polu. 
    
   ![Nazwa zadania](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wybierz pozycję **SQL Server**, a następnie wybierz pozycję **Dalej**. Konieczne może być przewinięcie w dół w celu znalezienia pozycji **SQL Server** na liście. 

   ![Wybór pozycji SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. W polu **Nazwa połączenia** wprowadź wartość **SqlServerLinkedService**. Wybierz link **Utwórz środowisko Integration Runtime**. Należy utworzyć środowisko Integration Runtime (Self-hosted), pobrać je na komputer i zarejestrować w usłudze Data Factory. Środowisko Integration Runtime (Self-hosted) kopiuje dane między środowiskiem lokalnym a chmurą.

   ![Tworzenie własnego środowiska Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. W oknie dialogowym **Tworzenie środowiska Integration Runtime** w obszarze **Nazwa** wprowadź wartość **TutorialIntegration Runtime**. Następnie wybierz przycisk **Utwórz**. 

   ![Nazwa środowiska Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Wybierz pozycję **Uruchom instalację ekspresową na tym komputerze**. Ta akcja instaluje na komputerze środowisko Integration Runtime i rejestruje je w usłudze Data Factory. Ewentualnie można użyć opcji instalacji ręcznej w celu pobrania pliku instalacyjnego, uruchomienia go i zarejestrowania środowiska Integration Runtime za pomocą klucza. 

    ![Uruchamianie instalacji ekspresowej dla tego linku komputera](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Uruchom pobraną aplikację. W oknie zostanie wyświetlony stan instalacji ekspresowej. 

    ![Stan instalacji ekspresowej](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Upewnij się, że w polu **Integration Runtime** wybrano pozycję **TutorialIntegrationRuntime**.

    ![Wybrane środowisko Integration Runtime](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Na stronie **Określanie lokalnej bazy danych programu SQL Server** wykonaj następujące czynności: 

    a. Wprowadź wartość **OnPremSqlLinkedService** w obszarze **Nazwa połączenia**.

    b. W polu **Nazwa serwera** wprowadź nazwę lokalnego wystąpienia programu SQL Server.

    d. Wprowadź nazwę swojej lokalnej bazy danych w polu **Nazwa bazy danych**.

    d. Wybierz odpowiedni typ uwierzytelniania w polu **Typ uwierzytelniania**.

    e. Wprowadź nazwę użytkownika z dostępem do lokalnego programu SQL Server w polu **Nazwa użytkownika**.

    f. Wprowadź **hasło** dla użytkownika. 
10. Na stronie **Wybieranie tabel, z których mają być kopiowane dane, lub używanie zapytania niestandardowego** wybierz z listy tabelę **[dbo].[emp]**, a następnie wybierz pozycję **Dalej**. 

    ![Wybór tabeli pustych elementów](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Na stronie **Magazyn danych docelowych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz przycisk **Dalej**.

    ![Wybór usługi Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Na stronie **Określanie konta usługi Azure Blob Storage** wykonaj następujące kroki: 

    a. W polu **Nazwa połączenia** wprowadź wartość **AzureStorageLinkedService**.

    b. W polu **Nazwa konta magazynu** wybierz z listy rozwijanej swoje konto magazynu. 

    d. Wybierz pozycję **Dalej**.

    ![Określanie konta magazynu](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Na stronie **Wybieranie wyjściowego pliku lub folderu** w obszarze **Ścieżka folderu** wprowadź wartość **adftutorial/fromonprem**. Jako jedno z wymagań wstępnych został utworzony kontener **adftutorial**. Jeśli folder wyjściowy nie istnieje, usługa Data Factory automatycznie go utworzy. Możesz również za pomocą przycisku **Przeglądaj** przejść do magazynu obiektów blob i jego kontenerów/folderów. Zauważ, że domyślnie jako nazwa pliku wyjściowego jest ustawiona wartość **dbo.emp**.
        
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Na stronie **Ustawienia formatu pliku** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia formatu pliku](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Na stronie **Podsumowanie** sprawdź wartości wszystkich ustawień, a następnie wybierz pozycję **Dalej**. 

    ![Strona podsumowania](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Na stronie **Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować utworzony potok lub zadanie.

    ![Strona Wdrażanie](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. Na karcie **Monitorowanie** można wyświetlić stan utworzonego potoku. Możesz użyć linków w kolumnie **Akcja**, aby wyświetlić uruchomienia działań skojarzonych z uruchomieniem potoku oraz ponownie uruchamiać potok. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje**, aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Aby przełączyć się z powrotem do widoku **Uruchomienia potoków**, wybierz pozycję **Potoki** u góry ekranu.

    ![Monitorowanie uruchomień działania](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Upewnij się, że w folderze **fromonprem** kontenera **adftutorial** znajduje się plik wyjściowy. 
 
    ![Wyjściowy obiekt blob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Wybierz kartę **Edycja** po lewej stronie, aby przełączyć się w tryb edytora. Za pomocą edytora można zaktualizować usługi połączone, zestawy danych i potoki utworzone przez narzędzie. Wybierz pozycję **Kod**, aby wyświetlić kod JSON skojarzony z jednostką otwartą w edytorze. Aby uzyskać szczegółowe informacje dotyczące sposobu edytowania tych jednostek w interfejsie użytkownika usługi Data Factory, zobacz [wersję witryny Azure Portal używaną w tym samouczku](tutorial-copy-data-portal.md).

    ![Karta Edycja](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Blob Storage. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

Lista magazynów danych obsługiwanych przez usługę Data Factory znajduje się w artykule [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Aby dowiedzieć się, jak zbiorczo kopiować dane z lokalizacji źródłowej do docelowej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy-portal.md)
