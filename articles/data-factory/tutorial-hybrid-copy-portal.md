---
title: Kopiowanie danych z programu SQL Server do usługi Blob Storage przy użyciu usługi Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak skopiować dane z lokalnego magazynu danych do chmury przy użyciu własnego środowiska Integration Runtime w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: abnarain
ms.openlocfilehash: ad09715f8ccbe20ec6f58d3a4543e0168e9f4cbc
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617719"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Kopiowanie danych z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage
W tym samouczku użyjesz interfejsu użytkownika usługi Azure Data Factory, aby utworzyć potok usługi Data Factory, który kopiuje dane z lokalnej bazy danych programu SQL Server do usługi Azure Blob Storage. Utworzysz własne środowisko Integration Runtime (Self-hosted), służące do przenoszenia danych między lokalnym magazynem danych i magazynem danych w chmurze.

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md). 

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Tworzenie połączonych zestawów programu SQL Server i usługi Azure Storage. 
> * Tworzenie zestawów danych programu SQL Server i usługi Azure Blob.
> * Tworzenie potoku z działaniem kopiowania do przenoszenia danych.
> * Uruchamianie potoku.
> * Monitorowanie uruchomienia potoku.

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Role platformy Azure
Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi mieć przypisaną rolę *współautora* lub *właściciela* albo być *administratorem* subskrypcji platformy Azure. 

Aby wyświetlić swoje uprawnienia do subskrypcji, przejdź do witryny Azure Portal. W prawym górnym rogu wybierz swoją nazwę użytkownika, a następnie wybierz pozycję **Uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. Przykładowe instrukcje dotyczące sposobu dodawania użytkownika do roli można znaleźć w temacie [Manage access using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Zarządzanie dostępem przy użyciu kontroli dostępu na podstawie ról i witryny Azure Portal).

### <a name="sql-server-2014-2016-and-2017"></a>Program SQL Server 2014, 2016 oraz 2017
Podczas pracy z tym samouczkiem użyjesz lokalnej bazy danych programu SQL Server jako *źródłowego* magazynu danych. Potok usługi Data Factory tworzony w tym samouczku kopiuje dane z tej lokalnej bazy danych programu SQL Server (źródła) do usługi Blob Storage (ujścia). Następnie utworzysz tabelę o nazwie **emp** w bazie danych programu SQL Server i wstawisz kilka przykładowych wpisów do tabeli. 

1. Uruchom program SQL Server Management Studio. Jeśli program nie jest jeszcze zainstalowany na używanej maszynie, przejdź do strony [pobierania programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Połącz się z wystąpieniem programu SQL Server przy użyciu swoich poświadczeń. 

1. Utwórz przykładową bazę danych. W widoku drzewa kliknij prawym przyciskiem myszy pozycję **Bazy danych**, a następnie wybierz pozycję **Nowa baza danych**. 
1. W oknie **Nowa baza danych** wprowadź nazwę bazy danych, a następnie wybierz przycisk **OK**. 

1. Aby utworzyć tabelę **emp** i wstawić do niej przykładowe dane, uruchom następujący skrypt zapytania w bazie danych:

   ```
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

1. W widoku drzewa kliknij prawym przyciskiem myszy utworzoną bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

### <a name="azure-storage-account"></a>Konto magazynu Azure
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (konkretnie usługi Blob Storage) jako magazynu danych: docelowego/ujścia. Jeśli nie masz konta usługi Azure Storage ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md). Potok w fabryce danych tworzony w tym samouczku kopiuje dane z lokalnej bazy danych programu SQL Server (źródła) do tej usługi Blob Storage (ujścia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta magazynu. Pobierz nazwę i klucz konta magazynu, wykonując następujące kroki: 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure. 

1. W okienku po lewej stronie wybierz pozycję **Więcej usług**. Zastosuj filtrowanie według słowa kluczowego **Magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie kont magazynu](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Na liście kont magazynu odfiltruj swoje konto magazynu, jeśli to konieczne. Następnie wybierz swoje konto magazynu. 

1. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.


1. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem. 

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial 
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Blob Storage. 

1. W oknie **Konto magazynu** przejdź do widoku **Przegląd**, a następnie wybierz pozycję **Obiekty blob**. 

    ![Wybieranie opcji Obiekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. W oknie **Blob Service** wybierz pozycję **Kontener**. 

    ![Przycisk kontenera](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. W oknie **Nowy kontener** w polu **Nazwa** wprowadź wartość **adftutorial**. Następnie wybierz przycisk **OK**. 

    ![Okno nowego kontenera](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Kliknij pozycję **adftutorial** na liście kontenerów.

    ![Wybór kontenera](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Pozostaw otwarte okno **kontenera** **adftutorial**. Będzie ona używana do weryfikacji danych wyjściowych na końcu tego samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych. 

1. Otwórz przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz pozycję **Utwórz zasób** > **dane + analiza** > **Data Factory**:
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
   
     ![Strona Nowa fabryka danych](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

   ![Nazwa nowej fabryki danych](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
   
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.
        
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez usługę Data Factory mogą mieścić się w innych regionach.
1. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
1. Wybierz pozycję **Utwórz**.
1. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**:

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji:
   
    ![Strona główna fabryki danych](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
1. Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory. 


## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**. Potok zostanie utworzony automatycznie. Potok będzie wyświetlony w widoku drzewa i zostanie otwarty jego edytor. 

   ![Strona Zaczynajmy](./media/tutorial-hybrid-copy-portal/get-started-page.png)

1. Na karcie **Ogólne** u dołu okna **Właściwości** w polu **Nazwa** wprowadź wartość **SQLServerToBlobPipeline**.

   ![Nazwa potoku](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. W przyborniku **Działania** rozwiń pozycję **Przepływ danych**. Przeciągnij i upuść działanie **Kopiowanie** na powierzchnię projektowania potoku. Ustaw nazwę działania na wartość **CopySqlServerToAzureBlobActivity**.

   ![Nazwa działania](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

1. W oknie **Właściwości** przejdź do karty **Źródło** i wybierz pozycję **+ Nowy**.

   ![Karta Źródło](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

1. W oknie **Nowy zestaw danych** wyszukaj pozycję **SQL Server**. Wybierz pozycję **SQL Server**, a następnie wybierz pozycję **Zakończ**. Zostanie wyświetlona nowa karta zatytułowana **SqlServerTable1**. Widoczny będzie także zestaw danych **SqlServerTable1** w widoku drzewa po lewej stronie. 

   ![Wybór pozycji SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

1. Na karcie **Ogólne** u dołu okna **Właściwości** w polu **Nazwa** wprowadź wartość **SqlServerDataset**.

   ![Nazwa zestawu danych będącego źródłem](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

1. Przejdź do karty **Połączenie**, a następnie wybierz pozycję **+Nowy**. W tym kroku tworzone jest połączenie z magazynem danych źródłowych (bazą danych programu SQL Server). 

   ![Połączenie ze źródłowym zestawem danych](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

1. W oknie **Nowa połączona usługa** w polu **Nazwa** dodaj ciąg **SqlServerLinkedService**. Wybierz pozycję **Nowy** w polu **Połącz za pośrednictwem środowiska Integration Runtime**. W tej sekcji utworzysz własne środowisko Integration Runtime i skojarzysz je z maszyną lokalną za pomocą bazy danych programu SQL Server. Własne środowisko Integration Runtime jest składnikiem, który kopiuje dane z bazy danych programu SQL Server na Twojej maszynie do usługi Blob Storage. 

   ![Tworzenie nowego środowiska Integration Runtime](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

1. W oknie **instalatora środowiska Integration Runtime** wybierz pozycję **Sieć prywatna**, a następnie wybierz przycisk **Dalej**. 

   ![Wybór sieci prywatnej](./media/tutorial-hybrid-copy-portal/select-private-network.png)

1. Wprowadź nazwę środowiska Integration Runtime i wybierz przycisk **Dalej**.

    ![Nazwa środowiska Integration Runtime](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

1. W obszarze **Opcja 1: Instalacja ekspresowa** wybierz pozycję **Kliknij tutaj, aby uruchomić instalację ekspresową dla tego komputera**. 

    ![Link instalacji ekspresowej](./media/tutorial-hybrid-copy-portal/click-express-setup.png)

1. W oknie **Instalacja ekspresowa środowiska Integration Runtime (Self-hosted)** wybierz przycisk **Zamknij**. 

    ![Instalacja ekspresowa środowiska Integration Runtime (Self-hosted)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. W oknie **Nowa połączona usługa** upewnij się, że utworzony powyżej element **Integration Runtime** jest wybrany w obszarze **Połącz za pośrednictwem środowiska Integration Runtime**. 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

1. W oknie **Nowa połączona usługa** wykonaj następujące kroki:

    a. W polu **Nazwa** wprowadź wartość **SqlServerLinkedService**.

    b. Upewnij się, że utworzone wcześniej środowisko Integration Runtime jest wyświetlane w polu **Połącz za pomocą środowiska Integration Runtime**.

    c. W polu **Nazwa serwera** wprowadź nazwę lokalnego wystąpienia programu SQL Server. 

    d. W polu **Nazwa bazy danych** wprowadź nazwę bazy danych zawierającej tabelę **emp**.

    e. W polu **Typ uwierzytelniania** wybierz odpowiedni typ uwierzytelniania, którego usługa Data Factory powinna używać do nawiązania połączenia z bazą danych SQL Server.

    f. W polach **Nazwa użytkownika** i **Hasło** wprowadź nazwę użytkownika i hasło. Jeśli musisz użyć znaku ukośnika (\\) w nazwie konta użytkownika lub nazwie serwera, poprzedź go znakiem ucieczki (\\). Na przykład: *mydomain\\\\myuser*.

    g. Wybierz pozycję **Testuj połączenie**. Wykonaj ten krok, aby potwierdzić, że usługa Data Factory może połączyć się z bazą danych SQL Server przy użyciu utworzonego własnego środowiska Integration Runtime.

    h. Aby zapisać połączoną usługę, wybierz pozycję **Zakończ**.

       

1. Powinien nastąpić powrót do okna z otwartym zestawem danych źródłowych. Na karcie **Połączenia** w oknie **Właściwości** wykonaj następujące kroki: 

    a. Upewnij się, że dla ustawienia **Połączona usługa** jest wyświetlana wartość **SqlServerLinkedService**.

    b. W obszarze **Tabela** wybierz pozycję **[dbo].[emp]** .

    ![Informacje o połączeniu ze źródłowym zestawem danych](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

1. Przejdź do karty z elementem **SQLServerToBlobPipeline** lub wybierz pozycję **SQLServerToBlobPipeline** w widoku drzewa. 

    ![Karta potoku](./media/tutorial-hybrid-copy-portal/pipeline-tab.png)

1. Przejdź do karty **Ujście** u dołu okna **Właściwości**, a następnie wybierz pozycję **+ Nowy**. 

    ![Karta Ujście](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**. Następnie wybierz pozycję **Zakończ**. Zostanie otwarta nowa karta dla tego zestawu danych. Zestaw danych powinien być też widoczny w widoku drzewa. 

    ![Wybór usługi Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

1. W polu **Nazwa** wprowadź wartość **AzureBlobDataset**.

    ![Nazwa zestawu danych będącego ujściem](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

1. Przejdź do karty **Połączenie** u dołu okna **Właściwości**. Obok pozycji **Połączona usługa** wybierz pozycję **+ Nowy**. 

    ![Przycisk Nowa połączona usługa](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

1. W oknie **Nowa połączona usługa** wykonaj następujące kroki:

    a. W polu **Nazwa** wprowadź wartość **AzureStorageLinkedService**.

    b. W polu **Nazwa konta magazynu** wybierz konto magazynu.

    c. Aby przetestować połączenie z kontem magazynu, wybierz pozycję **Testuj połączenie**.

    d. Wybierz pozycję **Zapisz**.

    ![Ustawienia połączonej usługi Storage](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

1. Powinien nastąpić powrót do okna z otwartym zestawem danych ujścia. Na karcie **Połączenie** wykonaj następujące kroki: 

    a. Upewnij się, że w polu **Połączona usługa** wybrano wartość **AzureStorageLinkedService**.

    b. Jako część **folder**/ **katalog** w polu **Ścieżka pliku** wprowadź wartość **adftutorial/incrementalcopy**. Jeśli folder wyjściowy nie istnieje w kontenerze adftutorial, usługa Data Factory automatycznie utworzy folder wyjściowy.

    c. W przypadku części **nazwa pliku** w polu **Ścieżka pliku** wybierz pozycję **Dodaj zawartość dynamiczną**.   

    ![dynamiczna wartość nazwy pliku](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Dodaj test `@CONCAT(pipeline().RunId, '.txt')` i wybierz pozycję **Zakończ**. Spowoduje to zmianę nazwy pliku na PipelineRunID.txt. 

    ![wyrażenie dynamiczne do rozpoznawania nazwy pliku](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![Połączenie z zestawem danych będącym ujściem](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

1. Przejdź na kartę z otwartym potokiem lub wybierz potok w widoku drzewa. Upewnij się, że w polu **Zestaw danych będący ujściem** wybrano wartość **AzureBlobDataset**. 

    ![Wybrany zestaw danych ujścia](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

1. Aby zweryfikować poprawność ustawień potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi dla potoku. Aby zamknąć okno **Raport weryfikacji potoku**, wybierz pozycję **Zamknij**. 

    ![Weryfikowanie potoku](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

1. Aby opublikować utworzone jednostki w usłudze Data Factory, wybierz pozycję **Opublikuj wszystko**.

    ![Przycisk Opublikuj](./media/tutorial-hybrid-copy-portal/publish-button.png)

1. Zaczekaj, aż zostanie wyświetlone okienko wyskakujące z komunikatem **Publikowanie powiodło się**. Aby sprawdzić stan publikowania, wybierz link **Pokaż powiadomienia** po lewej stronie. Aby zamknąć okno powiadomienia, wybierz przycisk **X**. 

    ![Publikowanie powiodło się](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku
Wybierz pozycję **Wyzwól** na pasku narzędzi potoku, a następnie wybierz pozycję **Wyzwól teraz**.

![Uruchamianie wyzwolenia potoku](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź na kartę **Monitorowanie**. Zostanie wyświetlony potok ręcznie wyzwolony w poprzednim kroku. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. W potoku jest tylko jedno działanie, dlatego na liście są wyświetlane uruchomienia tylko jednego działania. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Aby powrócić do widoku **Uruchomienia potoków**, wybierz pozycję **Potoki** u góry ekranu.

    ![Monitorowanie uruchomień działania](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych
Potok automatycznie tworzy folder wyjściowy o nazwie *fromonprem* w kontenerze obiektów blob `adftutorial`. Upewnij się, że plik *[pipeline().RunId].txt* jest widoczny w folderze wyjściowym. 

![potwierdzenie nazwy pliku wyjściowego](./media/tutorial-hybrid-copy-portal/sink-output.png)


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Blob Storage. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Tworzenie połączonych usług programu SQL Server i usługi Storage. 
> * Tworzenie zestawów danych programu SQL Server i usługi Blob Storage.
> * Tworzenie potoku z działaniem kopiowania do przenoszenia danych.
> * Uruchamianie potoku.
> * Monitorowanie uruchomienia potoku.

Lista magazynów danych obsługiwanych przez usługę Data Factory znajduje się w artykule [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Aby dowiedzieć się, jak zbiorczo kopiować dane z lokalizacji źródłowej do docelowej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy-portal.md)
