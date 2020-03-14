---
title: Kopiowanie danych z SQL Server do magazynu obiektów BLOB przy użyciu Azure Portal
description: Dowiedz się, jak skopiować dane z lokalnego magazynu danych do chmury przy użyciu własnego środowiska Integration Runtime w usłudze Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 01f2644874da032b95162f3f5721ab9dbea74265
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239070"
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
> * Uruchamianie potoku
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

1. Aby utworzyć tabelę **emp** i wstawić do niej przykładowe dane, uruchom następujący skrypt zapytania w bazie danych. W widoku drzewa kliknij prawym przyciskiem myszy utworzoną bazę danych, a następnie wybierz pozycję **Nowe zapytanie**.

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

### <a name="azure-storage-account"></a>Konto magazynu Azure
W tym samouczku używasz konta usługi Azure Storage ogólnego przeznaczenia (konkretnie usługi Blob Storage) jako magazynu danych: docelowego/ujścia. Jeśli nie masz konta usługi Azure Storage ogólnego przeznaczenia, zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Potok w fabryce danych tworzony w tym samouczku kopiuje dane z lokalnej bazy danych programu SQL Server (źródła) do tej usługi Blob Storage (ujścia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Pobieranie nazwy konta i klucza konta magazynu
W tym samouczku używasz nazwy i klucza swojego konta magazynu. Pobierz nazwę i klucz konta magazynu, wykonując następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu nazwy użytkownika i hasła do konta platformy Azure.

1. W lewym okienku wybierz pozycję **wszystkie usługi**. Zastosuj filtrowanie według słowa kluczowego **Magazyn**, a następnie wybierz pozycję **Konta magazynu**.

    ![Wyszukiwanie kont magazynu](media/doc-common-process/search-storage-account.png)

1. Na liście kont magazynu w razie potrzeby odfiltruj konto magazynu. Następnie wybierz swoje konto magazynu.

1. W oknie **Konto magazynu** wybierz pozycję **Klucze dostępu**.

1. Skopiuj wartości z pól **Nazwa konta magazynu** i **klucz1** i wklej je do Notatnika lub innego edytora do późniejszego użycia z tym samouczkiem.

#### <a name="create-the-adftutorial-container"></a>Tworzenie kontenera adftutorial
W tej sekcji utworzysz kontener obiektów blob o nazwie **adftutorial** w usłudze Blob Storage.

1. W oknie **Konto magazynu** przejdź do widoku **Przegląd**, a następnie wybierz pozycję **Obiekty blob**.

    ![Wybieranie opcji Obiekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. W oknie **Blob Service** wybierz pozycję **Kontener**.

1. W oknie **Nowy kontener** w polu **Nazwa** wprowadź wartość **adftutorial**. Następnie wybierz przycisk **OK**.

1. Kliknij pozycję **adftutorial** na liście kontenerów.

1. Pozostaw otwarte okno **kontenera** **adftutorial**. Będzie ona używana do weryfikacji danych wyjściowych na końcu tego samouczka. Usługa Data Factory automatycznie tworzy folder wyjściowy w tym kontenerze, więc Ty nie musisz go tworzyć.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
W tym kroku utworzysz fabrykę danych i uruchomisz interfejs użytkownika usługi Data Factory, aby utworzyć potok w fabryce danych.

1. Otwórz przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. W menu po lewej stronie wybierz pozycję **Utwórz zasób** > **Analytics** > **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa fabryki danych musi być *globalnie unikatowa*. Jeśli dla pola nazwy zobaczysz poniższy komunikat o błędzie, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Reguły nazewnictwa dla artefaktów usługi Data Factory można znaleźć w artykule [Data Factory — reguły nazewnictwa](naming-rules.md).

   ![Nazwa nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

1. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.
        
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez usługę Data Factory mogą mieścić się w innych regionach.
1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji:

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
1. Wybierz kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory.


## <a name="create-a-pipeline"></a>Tworzenie potoku

1. Na stronie **Zaczynajmy** wybierz pozycję **Utwórz potok**. Potok zostanie utworzony automatycznie. Potok będzie wyświetlony w widoku drzewa i zostanie otwarty jego edytor.

   ![Strona Zaczynajmy](./media/doc-common-process/get-started-page.png)

1. Na karcie **Ogólne** w dolnej części okna **Właściwości** , w polu **Nazwa**wpisz **SQLServerToBlobPipeline**.

   ![Nazwa potoku](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. W oknie Narzędzie **działania** rozwiń pozycję **Przenieś & Przekształć**. Przeciągnij i upuść działanie **Kopiowanie** na powierzchnię projektowania potoku. Ustaw nazwę działania na wartość **CopySqlServerToAzureBlobActivity**.

1. W oknie **Właściwości** przejdź do karty **Źródło** i wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowy zestaw danych** Wyszukaj **SQL Server**. Wybierz pozycję **SQL Server**, a następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Ustawianie właściwości** w polu **Nazwa**wpisz **SqlServerDataset**. W obszarze **połączona usługa**wybierz pozycję **+ Nowy**. W tym kroku tworzone jest połączenie z magazynem danych źródłowych (bazą danych programu SQL Server).

1. W oknie dialogowym **Nowa połączona usługa** Dodaj **nazwę** jako **SqlServerLinkedService**. W obszarze **Połącz za pośrednictwem środowiska Integration Runtime**wybierz pozycję **+ Nowy**.  W tej sekcji utworzysz własne środowisko Integration Runtime i skojarzysz je z maszyną lokalną za pomocą bazy danych programu SQL Server. Własne środowisko Integration Runtime jest składnikiem, który kopiuje dane z bazy danych programu SQL Server na Twojej maszynie do usługi Blob Storage.

1. W oknie dialogowym **konfiguracja Integration Runtime** wybierz pozycję **samodzielny**, a następnie wybierz pozycję **dalej**.

1. W polu Nazwa wprowadź **TutorialIntegrationRuntime**. Następnie wybierz przycisk **Dalej**.

1. W obszarze Ustawienia wybierz **pozycję kliknij tutaj, aby uruchomić instalację ekspresową dla tego komputera**. Ta akcja powoduje zainstalowanie środowiska Integration Runtime na komputerze i zarejestrowanie go w Data Factory. Ewentualnie można użyć opcji instalacji ręcznej w celu pobrania pliku instalacyjnego, uruchomienia go i zarejestrowania środowiska Integration Runtime za pomocą klucza.

1. W oknie **Instalacja ekspresowa środowiska Integration Runtime (Self-hosted)** wybierz przycisk **Zamknij**.

    ![Instalacja ekspresowa środowiska Integration Runtime (Self-hosted)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. W oknie dialogowym **Nowa połączona usługa** upewnij się, że wybrano pozycję **TutorialIntegrationRuntime** w obszarze **Połącz za pośrednictwem środowiska Integration Runtime**. Następnie wykonaj następujące czynności:

    a. W polu **Nazwa** wprowadź wartość **SqlServerLinkedService**.

    b. W polu **Nazwa serwera** wprowadź nazwę lokalnego wystąpienia programu SQL Server.

    c. W polu **Nazwa bazy danych** wprowadź nazwę bazy danych zawierającej tabelę **emp**.

    d. W polu **Typ uwierzytelniania** wybierz odpowiedni typ uwierzytelniania, którego usługa Data Factory powinna używać do nawiązania połączenia z bazą danych SQL Server.

    e. W polach **Nazwa użytkownika** i **Hasło** wprowadź nazwę użytkownika i hasło. Jeśli musisz użyć znaku ukośnika (\\) w nazwie konta użytkownika lub nazwie serwera, poprzedź go znakiem ucieczki (\\). Na przykład: *mydomain\\\\myuser*.

    f. Wybierz pozycję **Testuj połączenie**. Ten krok polega na potwierdzeniu, że Data Factory może nawiązać połączenie z bazą danych SQL Server za pomocą utworzonego przez siebie środowiska Integration Runtime.

    g. Aby zapisać połączoną usługę, wybierz pozycję **Zakończ**.

1. Powinien nastąpić powrót do okna z otwartym zestawem danych źródłowych. Na karcie **Połączenia** w oknie **Właściwości** wykonaj następujące kroki:

    a. Upewnij się, że dla ustawienia **Połączona usługa** jest wyświetlana wartość **SqlServerLinkedService**.

    b. W obszarze **Tabela** wybierz pozycję **[dbo].[emp]** .

1. Przejdź do karty z elementem **SQLServerToBlobPipeline** lub wybierz pozycję **SQLServerToBlobPipeline** w widoku drzewa.

1. Przejdź do karty **Ujście** u dołu okna **Właściwości**, a następnie wybierz pozycję **+ Nowy**.

1. W oknie dialogowym **Nowy zestaw danych** wybierz pozycję **Azure Blob Storage**. Następnie wybierz pozycję **Kontynuuj**.

1. W oknie dialogowym **Wybieranie formatu** wybierz typ formatu danych. Następnie wybierz pozycję **Kontynuuj**.

    ![Wybór formatu danych](./media/doc-common-process/select-data-format.png)

1. W oknie dialogowym **Ustawianie właściwości** wpisz **AzureBlobDataset** w polu Nazwa. Kliknij pozycję **+ Nowy** obok pola tekstowego **Połączona usługa**.

1. W oknie dialogowym **Nowa połączona usługa (Azure Blob Storage)** wprowadź **AzureStorageLinkedService** jako nazwę, a następnie wybierz konto magazynu z listy Nazwa **konta magazynu** . Test connection, a następnie wybierz pozycję **Zakończ** , aby wdrożyć połączoną usługę.
1. Po utworzeniu połączonej usługi nastąpi powrót do strony **Ustawianie właściwości** . Wybierz przycisk **Kontynuuj**.

1. Powinien nastąpić powrót do okna z otwartym zestawem danych ujścia. Na karcie **Połączenie** wykonaj następujące kroki:

    a. Upewnij się, że w polu **Połączona usługa** wybrano wartość **AzureStorageLinkedService**.

    b. W polu **ścieżka pliku**wprowadź **adftutorial/Fromonprem** dla części **kontenera/katalogu** . Jeśli folder wyjściowy nie istnieje w kontenerze adftutorial, usługa Data Factory automatycznie utworzy folder wyjściowy.

    c. W polu część **pliku** wybierz pozycję **Dodaj zawartość dynamiczną**.
    ![wyrażenie dynamiczne do rozpoznawania nazwy pliku](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Dodaj `@CONCAT(pipeline().RunId, '.txt')`, a następnie wybierz pozycję **Zakończ**. Ta akcja spowoduje zmianę nazwy pliku na PipelineRunID. txt.

1. Przejdź na kartę z otwartym potokiem lub wybierz potok w widoku drzewa. Upewnij się, że w polu **Zestaw danych będący ujściem** wybrano wartość **AzureBlobDataset**.

1. Aby zweryfikować poprawność ustawień potoku, wybierz pozycję **Weryfikuj** na pasku narzędzi dla potoku. Aby zamknąć okno **Raport weryfikacji potoku**, wybierz pozycję **Zamknij**.

1. Aby opublikować utworzone jednostki w usłudze Data Factory, wybierz pozycję **Opublikuj wszystko**.

1. Zaczekaj, aż zostanie wyświetlone okienko wyskakujące z komunikatem **Publikowanie powiodło się**. Aby sprawdzić stan publikowania, wybierz link **Pokaż powiadomienia** w górnej części okna. Aby zamknąć okno powiadomienia, wybierz przycisk **X**.


## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku
Wybierz pozycję **Dodaj wyzwalacz** na pasku narzędzi dla potoku, a następnie wybierz pozycję **Wyzwól teraz**.

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **monitorowanie** . Zostanie wyświetlony potok, który został wyzwolony ręcznie w poprzednim kroku.

    ![Monitorowanie uruchomień potoku](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. Zobaczysz tylko uruchomienia działania, ponieważ w potoku jest tylko jedno działanie. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Aby powrócić do widoku uruchomienia potoków, wybierz pozycję **uruchomienia potoku** u góry.

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych
Potok automatycznie tworzy folder wyjściowy o nazwie *fromonprem* w kontenerze obiektów blob `adftutorial`. Upewnij się, że plik *[pipeline().RunId].txt* jest widoczny w folderze wyjściowym.


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Blob Storage. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz własne środowisko Integration Runtime.
> * Tworzenie połączonych usług programu SQL Server i usługi Storage.
> * Tworzenie zestawów danych programu SQL Server i usługi Blob Storage.
> * Tworzenie potoku z działaniem kopiowania do przenoszenia danych.
> * Uruchamianie potoku
> * Monitorowanie uruchomienia potoku.

Lista magazynów danych obsługiwanych przez usługę Data Factory znajduje się w artykule [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Aby dowiedzieć się, jak zbiorczo kopiować dane z lokalizacji źródłowej do docelowej, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Zbiorcze kopiowanie danych](tutorial-bulk-copy-portal.md)
