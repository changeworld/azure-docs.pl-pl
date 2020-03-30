---
title: Przenoszenie danych — brama zarządzania danymi
description: Skonfiguruj bramę danych, aby przenosić dane między środowiskiem lokalnym a chmurą. Użyj bramy zarządzania danymi w usłudze Azure Data Factory, aby przenieść dane.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: be797f76988c924503e11b6f66cce899b515e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982201"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Przenoszenie danych między źródłami lokalnymi a chmurą za pomocą bramy zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [kopiowanie danych między środowiskiem lokalnym a chmurą przy użyciu usługi Data Factory](../tutorial-hybrid-copy-powershell.md).

Ten artykuł zawiera omówienie integracji danych między lokalnymi magazynami danych a magazynami danych w chmurze przy użyciu usługi Data Factory. Opiera się na [artykule Działania przenoszenia danych](data-factory-data-movement-activities.md) i innych podstawowych pojęć fabryki danych: [zestawy danych](data-factory-create-datasets.md) i [potoki](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brama zarządzania danymi
Aby umożliwić przenoszenie danych do/z lokalnego magazynu danych, należy zainstalować bramę zarządzania danymi na komputerze lokalnym. Bramę można zainstalować na tym samym komputerze co magazyn danych lub na innym komputerze, o ile brama może łączyć się z magazynem danych.

> [!IMPORTANT]
> Zobacz artykuł [Bramy zarządzania danymi, aby](data-factory-data-management-gateway.md) uzyskać szczegółowe informacje na temat bramy zarządzania danymi.

W poniższym przewodniku pokazano, jak utworzyć fabrykę danych za pomocą potoku, który przenosi dane z lokalnej bazy danych **programu SQL Server** do magazynu obiektów blob platformy Azure. W ramach tego przewodnika zainstalujesz i skonfigurujesz bramę zarządzania danymi na tym komputerze.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Instruktaż: kopiowanie danych lokalnych do chmury
W tym instruktażu wykonaj następujące czynności:

1. Tworzenie fabryki danych.
2. Tworzenie bramy zarządzania danymi.
3. Tworzenie połączonych usług dla magazynów danych źródłowych i ujścia.
4. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych.
5. Tworzenie potoku z działaniem kopiowania do przenoszenia danych.

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne samouczka
Przed rozpoczęciem tego przewodnika należy mieć następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Szczegółowe informacje można znaleźć w artykule [Bezpłatna wersja próbna.](https://azure.microsoft.com/pricing/free-trial/)
* **Konto usługi Azure Storage**. Magazyn obiektów blob służy jako **miejsce docelowe/magazyn** danych ujścia w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz [artykuł Tworzenie konta magazynu,](../../storage/common/storage-account-create.md) aby uzyskać kroki, aby je utworzyć.
* **SQL Server**. Użyj lokalnej bazy danych programu SQL Server jako **źródła** magazynu danych w tym samouczku.

## <a name="create-data-factory"></a>Tworzenie fabryki danych
W tym kroku za pomocą portalu Azure można utworzyć wystąpienie usługi Azure Data Factory o nazwie **ADFTutorialOnPremDF**.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij **pozycję Utwórz zasób**, kliknij pozycję **Analiza + analiza**i kliknij pozycję **Fabryka danych**.

   ![Nowy->Fabryka danych](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Na stronie **Nowa fabryka danych** wprowadź **aDFTutorialOnPremDF** dla nazwy.

    ![Dodaj do tablicy startowej](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **Nazwa fabryki danych "ADFTutorialOnPremDF" jest niedostępna,** zmień nazwę fabryki danych (na przykład yournameADFTutorialOnPremDF) i spróbuj utworzyć ponownie. Użyj tej nazwy zamiast usługi ADFTutorialOnPremDF podczas wykonywania pozostałych kroków w tym samouczku.
   >
   > Nazwa fabryki danych może być zarejestrowana jako nazwa **DNS** w przyszłości, a tym samym stają się publicznie widoczne.
   >
   >
4. Wybierz **subskrypcję Azure**, w ramach której chcesz utworzyć fabrykę danych.
5. Wybierz istniejącą **grupę zasobów** lub utwórz grupę zasobów. W tym samouczku utwórz grupę zasobów o **nazwie: ADFTutorialResourceGroup**.
6. Kliknij **pozycję Utwórz** na stronie **Nowa fabryka danych.**

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
   >
   >
7. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych,** jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Tworzenie bramy
1. Na stronie **Fabryka danych** kliknij pozycję **Autor i rozmień** kafelek, aby uruchomić **edytor** dla fabryki danych.

    ![Kafelek Utwórz i wdróż](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. W Edytorze fabryki danych kliknij **... Więcej** informacji na pasku narzędzi, a następnie kliknij pozycję **Nowa brama danych**. Alternatywnie można kliknąć prawym przyciskiem myszy **bramy danych** w widoku drzewa i kliknąć **kolejną bramę danych**.

   ![Nowa brama danych na pasku narzędzi](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Na stronie **Tworzenie** wprowadź **adftutorialgateway** dla **nazwy**i kliknij przycisk **OK**.     

    ![Strona Utwórz bramę](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > W tym instruktażu utworzysz bramę logiczną z tylko jednym węzłem (lokalnym komputerem z systemem Windows). Bramę zarządzania danymi można skalować w poziomie, kojarząc z bramą wiele komputerów lokalnych. Można skalować w górę, zwiększając liczbę zadań przenoszenia danych, które można uruchomić jednocześnie w węźle. Ta funkcja jest również dostępna dla bramy logicznej z jednym węzłem. Zobacz [skalowanie bramy zarządzania danymi w usłudze Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artykuł, aby uzyskać szczegółowe informacje.  
4. Na stronie **Konfigurowanie** kliknij pozycję **Zainstaluj bezpośrednio na tym komputerze**. Ta akcja pobiera pakiet instalacyjny bramy, instaluje, konfiguruje i rejestruje bramę na komputerze.  

   > [!NOTE]
   > Użyj programu Internet Explorer lub przeglądarki sieci Web zgodnej z programem Microsoft ClickOnce.
   >
   > Jeśli korzystasz z Chrome, przejdź do [sklepu internetowego Chrome](https://chrome.google.com/webstore/), wyszukaj za pomocą słowa kluczowego "ClickOnce", wybierz jedno z rozszerzeń ClickOnce i zainstaluj je.
   >
   > Zrób to samo dla Firefoksa (zainstaluj dodatek). Kliknij przycisk **Otwórz menu** na pasku narzędzi **(trzy poziome linie** w prawym górnym rogu), kliknij przycisk **Dodatki**, wyszukaj za pomocą słowa kluczowego "ClickOnce", wybierz jedno z rozszerzeń ClickOnce i zainstaluj je.    
   >
   >

    ![Brama — konfigurowanie strony](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    W ten sposób jest najprostszym sposobem (jednym kliknięciem) do pobrania, zainstalowania, skonfigurowania i zarejestrowania bramy w jednym kroku. Na komputerze może być zainstalowana aplikacja **programu Microsoft Data Management Gateway Configuration Manager.** Plik wykonywalny **ConfigManager.exe** można również znaleźć w folderze: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Możesz również pobrać i zainstalować bramę ręcznie, korzystając z łączy na tej stronie i zarejestrować ją za pomocą klucza pokazanego w polu tekstowym **NOWY KLUCZ.**

    Zobacz artykuł [Bramy zarządzania danymi,](data-factory-data-management-gateway.md) aby uzyskać wszystkie szczegółowe informacje o bramie.

   > [!NOTE]
   > Aby pomyślnie zainstalować i skonfigurować bramę zarządzania danymi, musisz być administratorem na komputerze lokalnym. Do lokalnej grupy systemu Windows **użytkownicy bramy zarządzania danymi** można dodawać dodatkowych użytkowników. Członkowie tej grupy mogą konfigurować bramę za pomocą narzędzia Menedżer konfiguracji bramy zarządzania danymi.
   >
   >
5. Poczekaj kilka minut lub poczekaj, aż pojawi się następujący komunikat z powiadomieniem:

    ![Pomyślna instalacja bramy](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Uruchom aplikację **programu Menedżer konfiguracji bramy zarządzania danymi** na komputerze. W oknie **Wyszukiwanie** wpisz **Bramę zarządzania danymi,** aby uzyskać dostęp do tego narzędzia. Plik wykonywalny **ConfigManager.exe** można również znaleźć w folderze: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Menedżer konfiguracji bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Upewnij się, `adftutorialgateway is connected to the cloud service` że zostanie wyświetlony komunikat. Na pasku stanu u dołu jest wyświetlany **komunikat Połączony z usługą w chmurze** wraz z **zielonym znacznikiem wyboru**.

    Na karcie **Narzędzia** główne można również wykonać następujące operacje:

   * **Zarejestruj** bramę przy użyciu klucza z witryny Azure portal przy użyciu przycisku Zarejestruj.
   * **Zatrzymaj** usługę hosta bramy zarządzania danymi na komputerze bramy.
   * **Zaplanuj zainstalowanie aktualizacji** o określonej porze dnia.
   * Zobacz, kiedy brama została **ostatnio zaktualizowana**.
   * Określ godzinę, w której można zainstalować aktualizację bramy.
8. Przełącz się do karty **Ustawienia.** Certyfikat określony w sekcji **Certyfikat** służy do szyfrowania/odszyfrowywania poświadczeń lokalnego magazynu danych określonego w portalu. (opcjonalnie) Kliknij **przycisk Zmień,** aby zamiast tego użyć własnego certyfikatu. Domyślnie brama używa certyfikatu, który jest generowany automatycznie przez usługę Data Factory.

    ![Konfiguracja certyfikatu bramy](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Na karcie **Ustawienia** można również wykonać następujące czynności:

   * Służy do wyświetlania lub eksportowania certyfikatu używanego przez bramę.
   * Zmień punkt końcowy HTTPS używany przez bramę.    
   * Ustaw serwer proxy HTTP, który ma być używany przez bramę.     
9. (opcjonalnie) Przełącz się do karty **Diagnostyka,** zaznacz opcję **Włącz rejestrowanie pełne,** jeśli chcesz włączyć pełne rejestrowanie, którego można użyć do rozwiązywania problemów z bramą. Informacje o rejestrowaniu można znaleźć w **Podglądzie zdarzeń** w węźle**Bramy zarządzania danymi** **dziennikami** -> aplikacji i usług.

    ![Karta Diagnostyka](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Na karcie **Diagnostyka** można również wykonać następujące czynności:

   * Użyj sekcji **Testuj połączenie** do lokalnego źródła danych przy użyciu bramy.
   * Kliknij **pozycję Wyświetl dzienniki,** aby wyświetlić dziennik bramy zarządzania danymi w oknie Podglądu zdarzeń.
   * Kliknij **przycisk Wyślij dzienniki,** aby przekazać do firmy Microsoft plik zip z dziennikami z ostatnich siedmiu dni, aby ułatwić rozwiązywanie problemów.
10. Na karcie **Diagnostyka** w sekcji **Test connection (Połączenie testowe)** wybierz pozycję **SqlServer** dla typu magazynu danych, wprowadź nazwę serwera bazy danych, nazwę bazy danych, określ typ uwierzytelniania, wprowadź nazwę użytkownika i hasło oraz kliknij przycisk **Testuj,** aby sprawdzić, czy brama może łączyć się z bazą danych.
11. Przełącz się do przeglądarki sieci Web i w **witrynie Azure portal**, kliknij przycisk **OK** na stronie **Konfigurowanie,** a następnie na stronie **Nowa brama danych.**
12. W widoku drzewa po lewej stronie powinna być widoczna **adftutorialgateway** w obszarze **Bramy danych.**  Jeśli klikniesz, powinieneś zobaczyć skojarzony JSON.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi: **AzureStorageLinkedService** i **SqlServerLinkedService**. **SqlServerLinkedService** łączy lokalną bazę danych programu SQL Server i usługę linked **AzureStorageLinkedService** łączy magazyn obiektów blob platformy Azure z fabryką danych. Potok utworzysz później w tym instruktażu, który kopiuje dane z lokalnej bazy danych programu SQL Server do magazynu obiektów blob platformy Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Dodawanie połączonej usługi do lokalnej bazy danych programu SQL Server
1. W **Edytorze fabryki danych**kliknij pozycję **Nowy magazyn danych** na pasku narzędzi i wybierz pozycję SQL **Server**.

   ![Nowa usługa połączona z programem SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. W **edytorze JSON** po prawej stronie wykonaj następujące czynności:

   1. W przypadku **nazwy bramy**należy określić **adftutorialgateway**.    
   2. W **connectionString**wykonaj następujące czynności:    

      1. W przypadku **nazwy serwera**wprowadź nazwę serwera, na którym znajduje się baza danych programu SQL Server.
      2. W przypadku **nazwy bazy danych**wprowadź nazwę bazy danych.
      3. Kliknij przycisk **Zaszyfruj** na pasku narzędzi. Zostanie wyświetlone aplikacji Menedżer poświadczeń.

         ![Aplikacja Menedżera poświadczeń](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. W oknie dialogowym **Ustawianie poświadczeń** określ typ uwierzytelniania, nazwę użytkownika i hasło, a następnie kliknij przycisk **OK**. Jeśli połączenie zakończy się pomyślnie, zaszyfrowane poświadczenia są przechowywane w JSON i okno dialogowe zostanie zamknięte.
      5. Zamknij pustą kartę przeglądarki, która uruchomiła okno dialogowe, jeśli nie jest automatycznie zamknięty i wróć do karty za pomocą witryny Azure portal.

         Na komputerze bramy te poświadczenia są **szyfrowane** przy użyciu certyfikatu, który jest właścicielem usługi Data Factory. Jeśli chcesz użyć certyfikatu skojarzonego z bramą zarządzania danymi, zobacz Bezpieczne ustawianie poświadczeń.    
   3. Kliknij **pozycję Wdrażanie** na pasku poleceń, aby wdrożyć usługę połączeniową programu SQL Server. Usługa połączona powinna być widoczna w widoku drzewa.

      ![Usługa połączona programu SQL Server w widoku drzewa](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Dodawanie połączonej usługi dla konta magazynu platformy Azure
1. W **Edytorze fabryki danych**kliknij pozycję **Nowy magazyn danych** na pasku poleceń i kliknij pozycję Magazyn platformy **Azure**.
2. Wprowadź nazwę konta magazynu platformy Azure dla **nazwy konta**.
3. Wprowadź klucz dla konta magazynu platformy Azure dla **klucza konto**.
4. Kliknij **przycisk Wdrażanie,** aby wdrożyć **usługę AzureStorageLinkedService**.

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych wejściowych i wyjściowych, które reprezentują dane wejściowe i wyjściowe operacji kopiowania (lokalna baza danych programu SQL Server => usługa Azure Blob Storage). Przed utworzeniem zestawów danych wykonaj następujące czynności (szczegółowy opis kroków znajduje się po liście):

* Utwórz tabelę o nazwie **emp** w bazie danych programu SQL Server dodanej jako połączona usługa do fabryki danych i wstaw kilka przykładowych wpisów do tabeli.
* Utwórz kontener obiektów blob o nazwie **adftutorial** na koncie usługi Azure Blob Storage dodanym jako połączona usługa do fabryki danych.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Przygotowanie lokalnego programu SQL Server dla samouczka
1. W bazie danych określonej dla lokalnej połączonej usługi SQL Server (**SqlServerLinkedService**) użyj następującego skryptu SQL w celu utworzenia tabeli **emp** w bazie danych.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Wstaw do tabeli kilka przykładów:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych

1. W **Edytorze fabryki danych**kliknij **... Więcej**, kliknij pozycję **Nowy zestaw danych** na pasku poleceń i kliknij pozycję **Tabela programu SQL Server**.
2. Zastąp JSON w prawym okienku następującym tekstem:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Pamiętaj o następujących kwestiach:

   * **jest** ustawiona na **SqlServerTable**.
   * **nazwa tableName** jest ustawiona na **emp**.
   * **linkedServiceName** jest ustawiona na **SqlServerLinkedService** (utworzono tę połączaną usługę wcześniej w tym instruktażu.).
   * W przypadku wejściowego zestawu danych, który nie jest generowany przez inny potok w usłudze Azure Data Factory, należy ustawić **wartość true na** wartość **true.** Oznacza dane wejściowe są produkowane na zewnątrz usługi Azure Data Factory. Opcjonalnie można określić wszystkie zasady danych zewnętrznych przy użyciu **externalData** elementu w **sekcji Zasady.**    

   Zobacz [Przenoszenie danych do/z programu SQL Server, aby](data-factory-sqlserver-connector.md) uzyskać szczegółowe informacje o właściwościach JSON.
3. Kliknij **pozycję Wdrażanie** na pasku poleceń, aby wdrożyć zestaw danych.  

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych

1. W **Edytorze fabryki danych**kliknij pozycję **Nowy zestaw danych** na pasku poleceń, a następnie kliknij pozycję Magazyn obiektów **Blob platformy Azure**.
2. Zastąp JSON w prawym okienku następującym tekstem:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Pamiętaj o następujących kwestiach:

   * **typ** jest ustawiony na **AzureBlob**.
   * **linkedServiceName** jest ustawiona na **AzureStorageLinkedService** (utworzono tę połączaną usługę w kroku 2).
   * **folderPath** jest ustawiony na **adftutorial/outfromonpremdf** gdzie outfromonpremdf jest folder w kontenerze adftutorial. Utwórz kontener **adftutorial,** jeśli jeszcze nie istnieje.
   * Parametr **availability** (dostępność) został ustawiony na wartość **hourly** (co godzinę) (parametr **frequency** [częstotliwość] został ustawiony na **hour** [godzinę], a **interval** [interwał] został ustawiony na wartość **1**).  Usługa Fabryka danych generuje wycinek danych wyjściowych co godzinę w tabeli **emp** w bazie danych SQL azure.

   Jeśli nie określisz **fileName** dla **tabeli wyjściowej,** wygenerowane pliki w **folderPath** są nazwane w następującym formacie: `Data.<Guid>.txt` (na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Aby ustawić parametry **folderPath** i **fileName** dynamicznie w oparciu o czas **SliceStart**, użyj właściwości partitionedBy. W poniższym przykładzie parametr folderPath używa elementów Year, Month i Day z parametru SliceStart (czas rozpoczęcia przetwarzania wycinka), a parametr fileName używa elementu Hour z parametru SliceStart. Na przykład jeśli wycinek jest generowany dla czasu 2014-10-20T08:00:00, parametr folderName zostaje ustawiony na wikidatagateway/wikisampledataout/2014/10/20, a parametr fileName zostaje ustawiony na wartość 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Zobacz [Przenoszenie danych do/z usługi Azure Blob Storage, aby](data-factory-azure-blob-connector.md) uzyskać szczegółowe informacje o właściwościach JSON.
3. Kliknij **pozycję Wdrażanie** na pasku poleceń, aby wdrożyć zestaw danych. Upewnij się, że oba zestawy danych są widoczne w widoku drzewa.  

## <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku należy utworzyć **potok** z jednym **copy activity,** który używa **EmpOnPremSQLTable** jako dane wejściowe i **OutputBlobTable** jako dane wyjściowe.

1. W Edytorze fabryki danych kliknij **... Więcej**, a następnie kliknij pozycję **Nowy potok**.
2. Zastąp JSON w prawym okienku następującym tekstem:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Zastąp wartość właściwości **start** datą bieżącą, a wartość **end** datą jutrzejszą.
   >
   >

   Pamiętaj o następujących kwestiach:

   * W sekcji Działania istnieje tylko działanie, którego **typ** jest ustawiony na **Kopiuj**.
   * **Dane wejściowe** dla działania jest ustawiona na **EmpOnPremSQLTable** i **dane wyjściowe** dla działania jest **ustawiona na OutputBlobTable**.
   * W sekcji **typeProperties** **sqlsource** jest określony jako **typ źródła** i **BlobSink** jest określony jako **typ**ujścia .
   * Kwerenda `select * from emp` SQL jest określona dla właściwości **sqlReaderQuery** **programu SqlSource**.

   Zarówno data/godzina rozpoczęcia, jak i data/godzina zakończenia muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. Czas **end** jest opcjonalny, ale w tym samouczku zostanie użyty.

   Jeśli nie określisz wartości właściwości **końcowej,** zostanie ona obliczona jako "**start + 48 godzin**". Aby uruchomić potok bezterminowo, określ **9/9/9999** jako wartość dla właściwości **end**.

   Definiujesz czas trwania, w którym wycinki danych są przetwarzane na podstawie właściwości **dostępności,** które zostały zdefiniowane dla każdego zestawu danych usługi Azure Data Factory.

   W tym przykładzie występują 24 wycinki danych, ponieważ poszczególne wycinki są generowane co godzinę.        
3. Kliknij **przycisk Wdrażanie** na pasku poleceń, aby wdrożyć zestaw danych (tabela jest prostokątnym zestawem danych). Upewnij się, że potok jest wyświetlany w widoku drzewa w obszarze **Węzeł Potoki.**  
4. Teraz kliknij przycisk **X** dwa razy, aby zamknąć stronę, aby wrócić do strony **Fabryka danych** dla **aDFTutorialOnPremDF**.

**Gratulacje!** Pomyślnie utworzono fabrykę danych platformy Azure, połączone usługi, zestawy danych i potok i zaplanowano potok.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Wyświetlanie fabryki danych w widoku diagramu
1. W **portalu Azure**kliknij kafelek **Diagram** na stronie głównej fabryki danych **ADFTutorialOnPremDF.** :

    ![Łącze diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Powinien zostać wyświetlony diagram podobny do tego na poniższej ilustracji:

    ![Widok diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Możesz powiększać, pomniejszać, powiększać do 100%, powiększać, aby dopasować, automatycznie pozycjonować potoki i zestawy danych oraz wyświetlać informacje o linii (podświetla elementy wybranych elementów w górę i za nim).  Można dwukrotnie kliknąć obiekt (zestaw danych wejściowych/wyjściowych lub potoku), aby wyświetlić właściwości dla niego.

## <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku opisano użycie witryny Azure Portal do monitorowania tego, co dzieje się w fabryce danych Azure. Do monitorowania zestawów danych i potoków można również używać poleceń cmdlet programu PowerShell. Aby uzyskać szczegółowe informacje na temat monitorowania, zobacz [Monitorowanie i zarządzanie rurociągami](data-factory-monitor-manage-pipelines.md).

1. Na diagramie kliknij dwukrotnie pozycję **EmpOnPremSQLTable**.  

    ![Plasterki EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Należy zauważyć, że wszystkie plasterki danych w górę są w stanie **Gotowy,** ponieważ czas trwania potoku (czas rozpoczęcia do czasu zakończenia) jest w przeszłości. Jest to również dlatego, że zostały wstawione dane do bazy danych programu SQL Server i jest tam cały czas. Upewnij się, że w sekcji Problem plasterki u dołu nie są wyświetlane **żadne plasterki.** Aby wyświetlić wszystkie wycinki, kliknij pozycję **Zobacz więcej** u dołu listy plasterków.
3. Teraz na stronie **Zestawy danych** kliknij pozycję **OutputBlobTable**.

    ![Plasterki OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kliknij dowolny plasterek danych z listy, a na stronie Fragment danych powinna zostać wyświetlona strona **Plasterek danych.** Zobaczysz działanie uruchamia się dla plasterka. Zwykle jest uruchamiane tylko jedno działanie.  

    ![Blok plasterka danych](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Jeśli wycinek nie znajduje się w stanie **Gotowe**, możesz wyświetlić wycinki strumienia wychodzącego, które nie są w stanie Gotowe i blokują wykonanie bieżącego wycinka na liście **Niegotowe wycinki strumienia wychodzącego**.
5. Kliknij **działanie uruchomione** z listy u dołu, aby wyświetlić **szczegóły uruchomienia aktywności**.

   ![Strona Szczegóły przebiegu działania](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zostaną wyświetlenia informacji, takich jak przepływność, czas trwania i brama używana do przesyłania danych.
6. Kliknij **X,** aby zamknąć wszystkie strony, aż
7. powrót do strony głównej usługi **ADFTutorialOnPremDF**.
8. (opcjonalnie) Kliknij **pozycję Potoki**, kliknij pozycję **ADFTutorialOnPremDF**i przejdź do szczegółów tabel wejściowych **(Zużyte)** lub Wyjściowych **(Wyprodukowane).**
9. Użyj narzędzi, takich jak [Microsoft Storage Explorer,](https://storageexplorer.com/) aby sprawdzić, czy obiekt blob/plik jest tworzony dla każdej godziny.

   ![Eksplorator usługi Azure Storage](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Następne kroki
* Zobacz artykuł [Bramy zarządzania danymi,](data-factory-data-management-gateway.md) aby uzyskać wszystkie szczegółowe informacje na temat bramy zarządzania danymi.
* Zobacz [Kopiowanie danych z obiektu blob platformy Azure do usługi Azure SQL,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby dowiedzieć się, jak używać działania kopiowania do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia.
