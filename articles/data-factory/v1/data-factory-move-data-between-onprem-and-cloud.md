---
title: Przenoszenie danych — brama zarządzania danymi | Dokumentacja firmy Microsoft
description: Konfigurowanie bramy danych do przenoszenia danych między systemem lokalnym i w chmurze. Użyj bramy zarządzania danymi w usłudze Azure Data Factory, aby przenieść dane.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4eb881992b7e40e0a9d67bd2cee94f1f09958e9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60825984"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Przenoszenie danych między źródłami lokalnymi i w chmurze przy użyciu bramy zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [kopiować dane między lokalnych i w chmurze, za pomocą usługi Data Factory](../tutorial-hybrid-copy-powershell.md).

Ten artykuł zawiera omówienie integracji danych między magazynami danych w środowisku lokalnym i magazynami danych w chmurze przy użyciu usługi fabryka danych. Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykule i innych danych fabryki podstawowe pojęcia dotyczące artykułów: [zestawów danych](data-factory-create-datasets.md) i [potoki](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brama zarządzania danymi
Należy zainstalować bramę zarządzania danymi na maszynie lokalnej umożliwia przenoszenie danych do/z lokalnego magazynu danych. Brama może zostać zainstalowana na tym samym komputerze, do przechowywania danych lub na innym komputerze, tak długo, jak bramy można połączyć się z magazynem danych.

> [!IMPORTANT]
> Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) artykuł, szczegółowe informacje na temat bramy zarządzania danymi. 

Poniższe wskazówki opisano tworzenie fabryki danych obejmującej potok, który przenosi dane ze środowiska lokalnego **programu SQL Server** bazy danych do usługi Azure blob storage. W ramach tego przewodnika zainstalujesz i skonfigurujesz bramę zarządzania danymi na tym komputerze.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Wskazówki: kopiowanie danych lokalnych do chmury
W tym przewodniku należy wykonać poniższe kroki: 

1. Tworzenie fabryki danych.
2. Utwórz bramę zarządzania danymi. 
3. Utwórz połączone usługi dla magazynów danych źródła i ujścia.
4. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych.
5. Tworzenie potoku z działaniem kopiowania do przenoszenia danych.

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka
Przed rozpoczęciem tego instruktażu, musisz mieć następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) artykuł, aby uzyskać szczegółowe informacje.
* **Konto usługi Azure Storage**. Możesz użyć magazynu obiektów blob jako **docelowego/ujścia** magazynu danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).
* **SQL Server**. Użyj lokalnej bazy danych programu SQL Server jako **źródła** magazynu danych w tym samouczku. 

## <a name="create-data-factory"></a>Tworzenie fabryki danych
W tym kroku użyjesz witryny Azure portal, aby utworzyć wystąpienie usługi Azure Data Factory, o nazwie **ADFTutorialOnPremDF**.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób**, kliknij przycisk **rozwiązania inteligentne + analiza**i kliknij przycisk **usługi Data Factory**.

   ![Nowy-> Fabryka danych](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. W **nowa fabryka danych** wpisz **ADFTutorialOnPremDF** dla nazwy.

    ![Dodaj do tablicy startowej](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **Nazwa fabryki danych "ADFTutorialOnPremDF" nie jest dostępna**, Zmień nazwę fabryki danych (na przykład yournameADFTutorialOnPremDF) i spróbuj utworzyć ją ponownie. Użyj tej nazwy zamiast ADFTutorialOnPremDF podczas wykonywania pozostałych kroków w ramach tego samouczka.
   >
   > Nazwa fabryki danych może zostać zarejestrowana jako **DNS** nazwy w przyszłości, a wówczas stanie się widoczna publicznie.
   >
   >
4. Wybierz **subskrypcję Azure**, w ramach której chcesz utworzyć fabrykę danych.
5. Wybierz istniejącą **grupę zasobów** lub utwórz grupę zasobów. Samouczek należy utworzyć grupę zasobów o nazwie: **ADFTutorialResourceGroup**.
6. Kliknij przycisk **Utwórz** na **nowa fabryka danych** strony.

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
   >
   >
7. Po zakończeniu tworzenia zostanie wyświetlona **usługi Data Factory** stronie, jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Tworzenie bramy
1. W **usługi Data Factory** kliknij **autora i wdrażanie** Kafelek, aby uruchomić **edytora** fabryki danych.

    ![Kafelek Utwórz i wdróż](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. W edytorze fabryki danych, kliknij przycisk **... Więcej** na pasku narzędzi, a następnie kliknij przycisk **nowej bramy data gateway**. Alternatywnie możesz kliknąć prawym przyciskiem myszy **bram Data Gateways** w widoku drzewa, a następnie kliknij przycisk **nowej bramy data gateway**.

   ![Nową bramę danych na pasku narzędzi](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. W **Utwórz** wpisz **adftutorialgateway** dla **nazwa**i kliknij przycisk **OK**.     

    ![Utwórz stronę bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > W tym instruktażu utworzysz logicznej bramy z tylko jednym węzłem (maszyna lokalna Windows). Brama zarządzania danymi można skalować poprzez skojarzenie wielu maszyn w środowisku lokalnym za pomocą bramy. Możesz skalować w górę, zwiększając liczbę zadań przenoszenia danych, które można uruchomić jednocześnie w węźle. Ta funkcja jest również dostępna dla logicznej bramy z jednym węzłem. Zobacz [bramy zarządzania danymi skalowanie w usłudze Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artykuł, aby uzyskać szczegółowe informacje.  
4. W **Konfiguruj** kliknij **zainstalować bezpośrednio na tym komputerze**. Ta akcja spowoduje pobranie pakietu instalacyjnego bramy, instaluje, konfiguruje i rejestruje bramy na komputerze.  

   > [!NOTE]
   > Użyj programu Internet Explorer lub przeglądarki internetowej zgodnej z Microsoft ClickOnce.
   >
   > Jeśli używasz przeglądarki Chrome, przejdź do [sklepu internetowego Chrome](https://chrome.google.com/webstore/), wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.
   >
   > Zrób to samo dla programu Firefox (zainstalować dodatek). Kliknij przycisk **Otwórz Menu** przycisk na pasku narzędzi (**trzy poziome linie** w prawym górnym rogu), kliknij przycisk **dodatki**, wyszukaj słowo kluczowe "ClickOnce", wybierz jedną z Rozszerzeń ClickOnce i zainstaluj go.    
   >
   >

    ![Brama — strona konfiguracji](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    W ten sposób jest najprostszym sposobem (jednym kliknięciem) można pobrać, zainstalować, skonfigurować i zarejestrować bramę w jednym kroku jednego. Możesz zobaczyć **Menedżera konfiguracji bramy zarządzania danych Microsoft** aplikacja jest zainstalowana na tym komputerze. Możesz również znaleźć pliku wykonywalnego **ConfigManager.exe** w folderze: **C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared**.

    Możesz również pobrać i zainstalować bramę ręcznie, korzystając z linków na tej stronie i zarejestrować go za pomocą klucza objętego **nowy klucz** pola tekstowego.

    Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) wszystkie szczegółowe informacje dotyczące bramy.

   > [!NOTE]
   > Musi być administratorem na komputerze lokalnym do zainstalowania i skonfigurowania bramy zarządzania danymi pomyślnie. Aby dodać kolejnych użytkowników do **użytkownicy bramy zarządzania danymi** grupy lokalnej Windows. Członkowie tej grupy można użyć narzędzia Menedżera konfiguracji bramy zarządzania danych do skonfigurowania bramy.
   >
   >
5. Poczekaj kilka minut lub poczekaj, aż zostanie wyświetlony następujący komunikat powiadomienia:

    ![Instalacja bramy zakończyła się pomyślnie](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Uruchom **Menedżera konfiguracji bramy zarządzania danych** aplikacji na komputerze. W **wyszukiwania** okna, typ **bramy zarządzania danymi** na dostęp do tego narzędzia. Możesz również znaleźć pliku wykonywalnego **ConfigManager.exe** w folderze: **C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared**

    ![Menedżer konfiguracji bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Upewnij się, że `adftutorialgateway is connected to the cloud service` wiadomości. Pasek Wyświetla dolnej stanu **nawiązanie połączenia z usługą w chmurze** wraz z **zielony znacznik wyboru**.

    Na **Home** karty, możesz również wykonać następujące operacje:

   * **Zarejestruj** bramy za pomocą klucza w witrynie Azure portal, korzystając z przycisku Zarejestruj.
   * **Zatrzymaj** usługę hosta bramy zarządzania danymi uruchomiona na maszynie bramy.
   * **Zaplanuj aktualizacje** do zainstalowania w określonym czasie dnia.
   * Wyświetl podczas bramy **Ostatnia aktualizacja:** .
   * Określ czas, w którym można zainstalować aktualizacji bramy.
8. Przejdź do karty **Ustawienia**. Certyfikatu podanego w **certyfikatu** sekcja jest używana do szyfrowania/odszyfrowywania poświadczeń dla magazynu danych w środowisku lokalnym, który określisz w portalu. (opcjonalnie) Kliknij przycisk **zmiany** zamiast tego użyć własnego certyfikatu. Domyślnie brama używa certyfikatu, który został wygenerowany automatycznie przez usługę Data Factory.

    ![Konfiguracja certyfikatu bramy](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Możesz również wykonać następujące czynności na **ustawienia** karty:

   * Wyświetl lub wyeksportuj certyfikat używany przez bramę.
   * Zmiana punktu końcowego protokołu HTTPS, używany przez bramę.    
   * Ustaw serwer proxy HTTP, który będzie używany przez bramę.     
9. (opcjonalnie) Przełącz się do **diagnostyki** karcie wyboru **Włącz pełne rejestrowanie** opcję, jeśli chcesz włączyć pełne rejestrowanie, używanego rozwiązanie wszelkich problemów z bramą. Rejestrowanie informacji można znaleźć w **Podgląd zdarzeń** w obszarze **Dzienniki aplikacji i usług** -> **bramy zarządzania danymi** węzła.

    ![Karta Diagnostyka](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Można również wykonać następujące czynności w **diagnostyki** karty:

   * Użyj **Testuj połączenie** sekcji ze źródłem danych lokalnych za pomocą bramy.
   * Kliknij przycisk **wyświetlanie dzienników** można znaleźć w dzienniku bramy zarządzania danymi w oknie podglądu zdarzeń.
   * Kliknij przycisk **Wyślij dzienniki** do przekazania pliku zip z dziennikami ostatnich siedmiu dni do firmy Microsoft, aby ułatwić rozwiązywanie problemów związanych z.
10. Na **diagnostyki** na karcie **Testuj połączenie** zaznacz **SqlServer** dla typu magazynu danych, wprowadź nazwę serwera bazy danych, nazwę bazy danych Określ typ uwierzytelniania, wprowadź nazwę użytkownika i hasło, a następnie kliknij przycisk **Test** do sprawdzenia, czy brama może połączyć z bazą danych.
11. Przejdź w przeglądarce sieci web i w **witryny Azure portal**, kliknij przycisk **OK** na **Konfiguruj** strony i następnie na **nowej bramy data gateway** strony.
12. Powinien zostać wyświetlony **adftutorialgateway** w obszarze **bram Data Gateways** w widoku drzewa po lewej stronie.  Jeśli klikniesz go, powinien zostać wyświetlony kod JSON skojarzony.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi: **AzureStorageLinkedService** i **SqlServerLinkedService**. **SqlServerLinkedService** bazę danych programu SQL Server w środowisku lokalnym i **AzureStorageLinkedService** połączona usługa łączy magazyn obiektów blob platformy Azure z fabryką danych. Utworzysz potok w dalszej części tego przewodnika, który kopiuje dane z lokalnej bazy danych programu SQL Server do magazynu obiektów blob platformy Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Dodawanie usługi połączonej z bazą danych programu SQL Server w środowisku lokalnym
1. W **edytora fabryki danych**, kliknij przycisk **nowy magazyn danych** na pasku narzędzi i wybierz **programu SQL Server**.

   ![Nowej połączonej usługi SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. W **edytora JSON** po prawej stronie, wykonaj następujące czynności:

   1. Aby uzyskać **gatewayName**, określ **adftutorialgateway**.    
   2. W **connectionString**, wykonaj następujące czynności:    

      1. Aby uzyskać **servername**, wprowadź nazwę serwera, który hostuje bazę danych programu SQL Server.
      2. Aby uzyskać **databasename**, wprowadź nazwę bazy danych.
      3. Kliknij przycisk **Szyfruj** przycisk na pasku narzędzi. Zostanie wyświetlony aplikacji Menedżer poświadczeń.

         ![Aplikacji Menedżer poświadczeń](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. W **Ustawianie poświadczeń** okno dialogowe, określ typ uwierzytelniania, nazwa użytkownika i hasło, a następnie kliknij przycisk **OK**. Jeśli połączenie zostanie nawiązane, zaszyfrowane poświadczenia są przechowywane w formacie JSON, a okno dialogowe zostanie zamknięte.
      5. Zamknij kartę przeglądarki pusty, który uruchomił okno dialogowe, jeśli nie zostaną automatycznie zamknięte, a następnie wrócić do karty z witryny Azure portal.

         Na maszynie bramy, te poświadczenia są **zaszyfrowanych** przy użyciu certyfikatu, który jest właścicielem usługi Data Factory. Jeśli chcesz użyć certyfikatu, który jest skojarzony z bramy zarządzania danymi w zamian, zobacz Ustaw poświadczenia bezpieczne.    
   3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożenie programu SQL Server połączoną usługę. Powinien zostać wyświetlony połączonej usługi, w widoku drzewa.

      ![Połączonej usługi SQL Server w widoku drzewa](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Dodaj połączonej usługi dla konta usługi Azure storage
1. W **edytora fabryki danych**, kliknij przycisk **nowy magazyn danych** paska poleceń i kliknij przycisk **usługi Azure storage**.
2. Wprowadź nazwę konta usługi Azure storage dla **nazwa konta**.
3. Wprowadź klucz dla konta usługi Azure storage dla **klucz konta**.
4. Kliknij przycisk **Wdróż** wdrażania **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku utworzysz zestawy danych wejściowych i wyjściowych, które reprezentują dane wejściowe i wyjściowe operacji kopiowania (lokalna baza danych programu SQL Server => usługa Azure Blob Storage). Przed utworzeniem zestawów danych wykonaj następujące czynności (szczegółowy opis kroków znajduje się po liście):

* Utwórz tabelę o nazwie **emp** w bazie danych programu SQL Server dodanej jako połączona usługa do fabryki danych i wstaw kilka przykładowych wpisów do tabeli.
* Utwórz kontener obiektów blob o nazwie **adftutorial** na koncie usługi Azure Blob Storage dodanym jako połączona usługa do fabryki danych.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Przygotowanie lokalnego programu SQL Server dla tego samouczka
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

1. W **Edytorze fabryki danych** kliknij opcję **... Więcej**, kliknij przycisk **nowy zestaw danych** na pasku poleceń, a następnie kliknij **tabeli programu SQL Server**.
2. Zastąp kod JSON w prawym okienku następującym tekstem:

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

   * **Typ** ustawiono **SqlServerTable**.
   * **Właściwość tableName** ustawiono **emp**.
   * **linkedServiceName** ustawiono **SqlServerLinkedService** (ta połączona usługa ma utworzona we wcześniejszej części tego przewodnika.).
   * Dla wejściowego zestawu danych, które nie są generowane przez innego potoku w usłudze Azure Data Factory, musisz ustawić **zewnętrznych** do **true**. Oznacza on, że dane wejściowe są generowane zewnętrzne w stosunku do usługi Azure Data Factory. Opcjonalnie możesz określić wszystkie zasady danych zewnętrznych przy użyciu **externalData** element **zasad** sekcji.    

   Zobacz [przenoszenie danych do/z programu SQL Server](data-factory-sqlserver-connector.md) szczegółowe informacje na temat właściwości kodu JSON.
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestaw danych.  

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych

1. W **edytora fabryki danych**, kliknij przycisk **nowy zestaw danych** na pasku poleceń, a następnie kliknij **usługi Azure Blob storage**.
2. Zastąp kod JSON w prawym okienku następującym tekstem:

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

   * **Typ** ustawiono **AzureBlob**.
   * **linkedServiceName** ustawiono **AzureStorageLinkedService** (gdyby ta połączona usługa została utworzona w kroku 2).
   * **folderPath** ustawiono **adftutorial/outfromonpremdf** gdzie outfromonpremdf to folder w kontenerze adftutorial. Tworzenie **adftutorial** kontener, jeśli jeszcze nie istnieje.
   * Parametr **availability** (dostępność) został ustawiony na wartość **hourly** (co godzinę) (parametr **frequency** [częstotliwość] został ustawiony na **hour** [godzinę], a **interval** [interwał] został ustawiony na wartość **1**).  Usługa Data Factory generuje wycinek danych wyjściowych, co godzinę w **emp** tabeli w bazie danych SQL Azure.

   Jeśli nie określisz **fileName** dla **tabeli wyjściowej**, wygenerowane pliki w **folderPath** są nazwane w następującym formacie: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Aby ustawić **folderPath** i **fileName** dynamicznie na podstawie **SliceStart** czasu, należy użyć właściwości partitionedBy. W poniższym przykładzie parametr folderPath używa elementów Year, Month i Day z parametru SliceStart (czas rozpoczęcia przetwarzania wycinka), a parametr fileName używa elementu Hour z parametru SliceStart. Na przykład jeśli wycinek jest generowany dla czasu 2014-10-20T08:00:00, parametr folderName zostaje ustawiony na wikidatagateway/wikisampledataout/2014/10/20, a parametr fileName zostaje ustawiony na wartość 08.csv.

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

    Zobacz [przenoszenie danych do i z usługi Azure Blob Storage](data-factory-azure-blob-connector.md) szczegółowe informacje na temat właściwości kodu JSON.
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestaw danych. Upewnij się, że oba zestawy danych w widoku drzewa jest wyświetlany.  

## <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku opisano tworzenie **potoku** przy użyciu jednego **działania kopiowania** , który używa **EmpOnPremSQLTable** jako dane wejściowe i **OutputBlobTable** jako dane wyjściowe.

1. W edytorze fabryki danych kliknij **... Więcej** i **Nowy potok**.
2. Zastąp kod JSON w prawym okienku następującym tekstem:    

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

   * W sekcji działań jest tylko działania którego **typu** ustawiono **kopiowania**.
   * **Dane wejściowe** dla działania jest ustawiony na **EmpOnPremSQLTable** i **dane wyjściowe** dla działania jest ustawiony na **OutputBlobTable**.
   * W **typeProperties** sekcji **SqlSource** jest określony jako **typ źródła** i **BlobSink** jest określony jako **typ ujścia**.
   * Zapytanie SQL `select * from emp` jest określona dla **sqlReaderQuery** właściwość **SqlSource**.

   Zarówno data/godzina rozpoczęcia, jak i data/godzina zakończenia muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. Czas **end** jest opcjonalny, ale w tym samouczku zostanie użyty.

   Jeśli nie określisz wartości dla właściwości **end**, zostanie ona obliczona jako „**czas rozpoczęcia + 48 godzin**”. Aby uruchomić potok bezterminowo, określ **9/9/9999** jako wartość dla właściwości **end**.

   Definiujesz czas trwania przetwarzania wycinki danych na podstawie **dostępności** właściwości, które zostały zdefiniowane dla każdego zestawu danych usługi Azure Data Factory.

   W tym przykładzie występują 24 wycinki danych, ponieważ poszczególne wycinki są generowane co godzinę.        
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestaw danych (tabela jest prostokątnym zestawem danych). Upewnij się, że potok zostaną wyświetlone w widoku drzewa w obszarze **potoki** węzła.  
4. Teraz, kliknij pozycję **X** dwa razy, aby zamknąć stronę, aby wrócić do **usługi Data Factory** stronie **ADFTutorialOnPremDF**.

**Gratulacje!** Pomyślnie utworzono usługi Azure data factory, usługi połączone, zestawy danych i potoku i zaplanować potok.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Wyświetlanie fabryki danych w widoku diagramu
1. W **witryny Azure portal**, kliknij przycisk **Diagram** kafelków na stronie głównej **ADFTutorialOnPremDF** fabryki danych. :

    ![Diagram Link](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Powinien zostać wyświetlony diagram podobny do tego na poniższej ilustracji:

    ![Widok diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Możesz powiększyć, pomniejszyć, Powiększ do 100%, powiększyć do dopasowania, automatycznie pozycji potoków i zestawów danych i wyświetlić informacje dotyczące elementów powiązanych (powoduje wyróżnienie elementów nadrzędnych i podrzędnych wybranych elementów).  Możesz kliknąć dwukrotnie obiekt (zestaw danych wejściowych/wyjściowych lub potok), aby wyświetlić jego właściwości.

## <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku opisano użycie witryny Azure Portal do monitorowania tego, co dzieje się w fabryce danych Azure. Do monitorowania zestawów danych i potoków można również używać poleceń cmdlet programu PowerShell. Aby uzyskać szczegółowe informacje o monitorowaniu, zobacz [monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md).

1. Na diagramie, kliknij dwukrotnie **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable slices](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Należy zauważyć, że wszystkie dane dzieli się znajdują się w **gotowe** stanu, ponieważ czas trwania potoku (czasu rozpoczęcia do czasu zakończenia) jest w przeszłości. To również, że wstawiono dane w bazie danych programu SQL Server i jest cały czas. Upewnij się, że są wyświetlane żadne wycinki w **wycinki z problemami** sekcji u dołu. Aby wyświetlić wszystkie wycinki, kliknij **Zobacz więcej** w dolnej części listę wycinków.
3. Teraz w **zestawów danych** kliknij **OutputBlobTable**.

    ![Wycinki OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kliknięciu dowolnego wycinka danych na liście i powinien zostać wyświetlony **wycinka danych** strony. Zobaczysz, że uruchomieniach działania dla wycinka. Zobaczysz tylko jedno uruchomienie zwykle działania.  

    ![Blok wycinek danych](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Jeśli wycinek nie znajduje się w stanie **Gotowe**, możesz wyświetlić wycinki strumienia wychodzącego, które nie są w stanie Gotowe i blokują wykonanie bieżącego wycinka na liście **Niegotowe wycinki strumienia wychodzącego**.
5. Kliknij przycisk **uruchomienia działania** z listy na dole, aby wyświetlić **szczegóły uruchamiania działania**.

   ![Strona Szczegóły uruchamiania działania](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobaczysz informacje, takie jak przepustowość, czas trwania i bramy używanej do transferu danych.
6. Kliknij przycisk **X** zamknąć wszystkich stron, dopóki
7. Wróć do strony głównej **ADFTutorialOnPremDF**.
8. (opcjonalnie) Kliknij przycisk **potoki**, kliknij przycisk **ADFTutorialOnPremDF**i przechodzić do szczegółów tabele wejściowe (**zużyto**) lub wyjściowe zestawy danych (**utworzone**).
9. Użyj narzędzi takich jak [Microsoft Storage Explorer](https://storageexplorer.com/) Aby sprawdzić, czy obiekt blob/plik został utworzony dla każdej godziny.

   ![Eksplorator usługi Azure Storage](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) wszystkie szczegółowe informacje dotyczące bramy zarządzania danymi.
* Zobacz [kopiowanie danych z obiektów Blob platformy Azure do bazy danych SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) Aby dowiedzieć się więcej o sposobie używania działania kopiowania do przenoszenia danych z magazynu danych źródłowych do magazynu danych ujścia.
