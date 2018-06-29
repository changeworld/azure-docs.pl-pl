---
title: Przenoszenie danych - brama zarządzania danymi | Dokumentacja firmy Microsoft
description: Skonfiguruj bramę danych do przenoszenia danych między firmą i chmurą. Użyj bramy zarządzania danymi w fabryce danych Azure, aby przenieść dane.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1d19cf6ecc9f2bedb6ceaf6312b247670d965b84
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048553"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [kopiowanie danych między lokalnymi i w chmurze przy użyciu fabryki danych](../tutorial-hybrid-copy-powershell.md).

Ten artykuł zawiera omówienie integrację danych lokalnych magazynów danych i magazyny danych chmury przy użyciu fabryki danych. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu i innych danych fabryki podstawowe pojęcia dotyczące artykułów: [zestawów danych](data-factory-create-datasets.md) i [potoki](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Brama zarządzania danymi
Brama zarządzania danymi należy zainstalować na komputerze lokalnym umożliwia przenoszenie danych z lokalnego magazynu danych. Bramę można zainstalować na tym samym komputerze co magazyn danych lub na innym komputerze tak długo, jak bramy można nawiązać połączenia z magazynem danych.

> [!IMPORTANT]
> Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu, aby uzyskać więcej informacji dotyczących bramy zarządzania danymi. 

Poniższe wskazówki opisano tworzenie fabryki danych z potok, który przenosi dane z lokalnymi **programu SQL Server** bazy danych do magazynu obiektów blob platformy Azure. W ramach tego przewodnika zainstalujesz i skonfigurujesz bramę zarządzania danymi na tym komputerze.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Wskazówki: kopiowanie danych lokalnych do chmury
W tym przewodniku możesz wykonać następujące czynności: 

1. Tworzenie fabryki danych.
2. Tworzenie bramy zarządzania danymi. 
3. Tworzenie połączonej usługi dla źródłowy i odbiorczy magazynów danych.
4. Tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych.
5. Tworzenie potoku z działaniem kopiowania do przenoszenia danych.

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka
Przed rozpoczęciem tego przewodnika, musi mieć następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Zobacz [bezpłatnej wersji próbnej](http://azure.microsoft.com/pricing/free-trial/) artykułu, aby uzyskać szczegółowe informacje.
* **Konto magazynu Azure**. Użyj magazynu obiektów blob jako **docelowego/ujście** magazynu danych, w tym samouczku. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **SQL Server**. Użyj lokalnej bazy danych programu SQL Server jako **źródła** magazynu danych w tym samouczku. 

## <a name="create-data-factory"></a>Tworzenie fabryki danych
W tym kroku, użyj portalu Azure można utworzyć wystąpienia fabryki danych Azure o nazwie **ADFTutorialOnPremDF**.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób**, kliknij przycisk **analizy i analiza**i kliknij przycisk **fabryki danych**.

   ![Nowy-> Fabryka danych](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. W **nowa fabryka danych** wprowadź **ADFTutorialOnPremDF** dla nazwy.

    ![Dodawanie do tablicy startowej](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **nazwa fabryki danych "ADFTutorialOnPremDF" nie jest dostępna**, Zmień nazwę fabryki danych (na przykład yournameADFTutorialOnPremDF) i spróbuj ponownie utworzyć. Użyj tej nazwy, zamiast ADFTutorialOnPremDF podczas wykonywania pozostałych czynności w tym samouczku.
   >
   > W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa **DNS**, a wówczas stanie się widoczna publicznie.
   >
   >
4. Wybierz **subskrypcję Azure**, w ramach której chcesz utworzyć fabrykę danych.
5. Wybierz istniejącą **grupę zasobów** lub utwórz grupę zasobów. Samouczek, Utwórz grupę zasobów o nazwie: **ADFTutorialResourceGroup**.
6. Kliknij przycisk **Utwórz** na **nowa fabryka danych** strony.

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
   >
   >
7. Po zakończeniu tworzenia zobacz **fabryki danych** strony, jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Tworzenie bramy
1. W **fabryki danych** kliknij przycisk **tworzenie i wdrażanie** Kafelek, aby uruchomić **edytor** dla fabryki danych.

    ![Kafelek Utwórz i wdróż](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. W edytorze fabryki danych, kliknij polecenie **... Więcej** na pasku narzędzi, a następnie kliknij przycisk **nowej bramy danych**. Alternatywnie możesz kliknąć prawym przyciskiem myszy **bram danych** w widoku drzewa, a następnie kliknij przycisk **nowej bramy danych**.

   ![Nową bramę danych na pasku narzędzi](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. W **Utwórz** wprowadź **adftutorialgateway** dla **nazwa**i kliknij przycisk **OK**.     

    ![Utwórz stronę bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > W tym przewodniku utworzysz logicznej bramy z tylko jednym węzłem (komputera lokalnego systemu Windows). Możesz skalować w poziomie bramy zarządzania danymi można skojarzyć wielu lokalnymi maszynami z bramą. Możesz skalować w górę zwiększając liczbę zadań przepływu danych, które można uruchomić jednocześnie w węźle. Ta funkcja jest również dostępny do logicznego bramy z jednego węzła. Zobacz [skalowanie brama zarządzania danymi w fabryce danych Azure](data-factory-data-management-gateway-high-availability-scalability.md) artykułu, aby uzyskać szczegółowe informacje.  
4. W **Konfiguruj** kliknij przycisk **Zainstaluj bezpośrednio na tym komputerze**. Ta akcja spowoduje pobranie pakietu instalacyjnego bramy, instaluje konfiguruje i rejestruje bramy na komputerze.  

   > [!NOTE]
   > Użyj programu Internet Explorer lub przeglądarki sieci web zgodne Microsoft ClickOnce.
   >
   > Jeśli używasz przeglądarki Chrome, przejdź do [sklepu internetowego Chrome](https://chrome.google.com/webstore/), wyszukaj słowo kluczowe „ClickOnce”, wybierz jedno z rozszerzeń ClickOnce i je zainstaluj.
   >
   > Wykonaj te same czynności dla przeglądarki Firefox (Instalowanie dodatku). Kliknij przycisk **Otwórz Menu** przycisk na pasku narzędzi (**trzy poziome linie** w prawym górnym rogu), kliknij przycisk **dodatki**, wyszukaj ze słowem kluczowym "ClickOnce", wybierz jedną z Rozszerzenia ClickOnce, a następnie zainstaluj go.    
   >
   >

    ![Brama — Konfigurowanie strony](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    W ten sposób jest najprostszym sposobem (jednym kliknięciem), aby pobrać, zainstalować, skonfigurować i zarejestrować bramę w jeden pojedynczy krok. Widać **Menedżera konfiguracji bramy zarządzania danymi firmy Microsoft** aplikacja jest zainstalowana na tym komputerze. Możesz również znaleźć pliku wykonywalnego **ConfigManager.exe** w folderze: **C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared**.

    Można również pobrać i zainstalować bramę ręcznie przy użyciu łącza na tej stronie i zarejestrowanie go za pomocą klucza wyświetlany w **nowy klucz** pola tekstowego.

    Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykułu wszystkie szczegóły dotyczące bramy.

   > [!NOTE]
   > Musi być administratorem na komputerze lokalnym do instalowania i konfigurowania bramy zarządzania danymi pomyślnie. Można dodać użytkowników do **użytkownicy bramy zarządzania danymi** lokalnej grupy systemu Windows. Członkowie tej grupy można użyć narzędzia Menedżera konfiguracji bramy zarządzania danymi, aby skonfigurować bramę.
   >
   >
5. Poczekaj kilka minut lub poczekaj, aż zostanie wyświetlony następujący komunikat powiadomienia:

    ![Pomyślnie zainstalowano bramy](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Uruchom **Menedżera konfiguracji bramy zarządzania danymi** aplikacji na komputerze. W **wyszukiwania** wpisz **brama zarządzania danymi** można uzyskać dostępu do tego narzędzia. Możesz również znaleźć pliku wykonywalnego **ConfigManager.exe** w folderze: **C:\Program Files\Microsoft danych zarządzania Gateway\2.0\Shared**

    ![Menedżer konfiguracji bramy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Upewnij się, że widoczny `adftutorialgateway is connected to the cloud service` wiadomości. Pasek Wyświetla dolnej stanu **podłączone do usługi w chmurze** wraz z **zielony znacznik wyboru**.

    Na **Home** karcie, możesz również wykonać następujące operacje:

   * **Zarejestruj** bramy przy użyciu klucza z portalu Azure za pomocą przycisku Zarejestruj.
   * **Zatrzymaj** usługa hosta bramy zarządzania danymi na komputerze bramy.
   * **Zaplanuj aktualizacje** do zainstalowania w określonym czasie dnia.
   * Widoku, gdy brama została **ostatniej aktualizacji**.
   * Określ czas, w którym można zainstalować aktualizacji z bramą.
8. Przejdź do karty **Ustawienia**. Certyfikatu podanego w **certyfikatu** sekcja jest używana do szyfrowania/odszyfrowywania poświadczeń magazynu danych lokalnych, określonym przez użytkownika w portalu. (opcjonalnie) Kliknij przycisk **zmiany** zamiast tego użyć własnego certyfikatu. Domyślnie bramy, używa certyfikatu, który został wygenerowany automatycznie przez usługę fabryki danych.

    ![Konfiguracja certyfikatu bramy](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Możesz również wykonać następujące czynności na **ustawienia** karty:

   * Umożliwia wyświetlanie i wyeksportuj certyfikat używany przez bramę.
   * Zmień punkt końcowy HTTPS używany przez bramę.    
   * Ustaw serwer proxy HTTP do użycia przez bramę.     
9. (opcjonalnie) Przełącz się do **diagnostyki** karcie wyboru **Włącz pełne rejestrowanie** opcję, jeśli chcesz włączyć pełne rejestrowanie, służącego do rozwiązywania problemów z bramą. Rejestrowanie informacji można znaleźć w **Podgląd zdarzeń** w obszarze **Dzienniki aplikacji i usług** -> **brama zarządzania danymi** węzła.

    ![Karta Diagnostyka](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Można również wykonywać następujące czynności w **diagnostyki** karty:

   * Użyj **Testuj połączenie** sekcji do lokalnego źródła danych przy użyciu bramy.
   * Kliknij przycisk **Wyświetl dzienniki** Aby wyświetlić dziennik brama zarządzania danymi w oknie Podgląd zdarzeń.
   * Kliknij przycisk **Wyślij dzienniki** można przekazać do firmy Microsoft w celu ułatwienia rozwiązywania problemów z problemów związanych z pliku zip z dziennikami ostatnich siedmiu dni.
10. Na **diagnostyki** karcie **Testuj połączenie** zaznacz **SqlServer** dla typu źródła danych, wprowadź nazwę serwera bazy danych, nazwę bazy danych, Określ typ uwierzytelniania, wprowadź nazwę użytkownika i hasło, a następnie kliknij przycisk **Test** Aby sprawdzić, czy brama można połączyć z bazą danych.
11. Przełącz się do przeglądarki sieci web, a w **portalu Azure**, kliknij przycisk **OK** na **Konfiguruj** strony, a następnie na **nowej bramy danych** strony.
12. Powinny pojawić się **adftutorialgateway** w obszarze **bram danych** w widoku drzewa po lewej stronie.  Jeśli klikniesz przycisk, powinna zostać wyświetlona skojarzone JSON.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku, Utwórz dwie połączonej usługi: **AzureStorageLinkedService** i **SqlServerLinkedService**. **SqlServerLinkedService** łączy lokalną bazą danych programu SQL Server i **AzureStorageLinkedService** połączonej usługi łączy magazynu obiektów blob platformy Azure z fabryką danych. Możesz utworzyć potok w dalszej części tego przewodnika, który kopiuje dane z lokalną bazą danych programu SQL Server do magazynu obiektów blob platformy Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Dodaj połączonej usługi do lokalnej bazy danych programu SQL Server
1. W **Edytor fabryki danych**, kliknij przycisk **nowy magazyn danych** na pasku narzędzi i wybierz **programu SQL Server**.

   ![Nowa usługa SQL Server połączone](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. W **edytora JSON** po prawej stronie, wykonaj następujące czynności:

   1. Aby uzyskać **gatewayName**, określ **adftutorialgateway**.    
   2. W **connectionString**, wykonaj następujące czynności:    

      1. Aby uzyskać **servername**, wprowadź nazwę serwera, który jest hostem bazy danych programu SQL Server.
      2. Aby uzyskać **databasename**, wprowadź nazwę bazy danych.
      3. Kliknij przycisk **Szyfruj** przycisk na pasku narzędzi. Zostanie wyświetlony aplikacji Menedżera poświadczeń.

         ![Aplikacja Menedżera poświadczeń](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. W **ustawienie poświadczeń** okno dialogowe, określ typ uwierzytelniania, nazwę użytkownika i hasło, a następnie kliknij przycisk **OK**. Jeśli połączenie zostanie nawiązane, zaszyfrowane poświadczenia są przechowywane w formacie JSON i zamyka okno dialogowe.
      5. Zamknij kartę puste przeglądarki, który uruchomić okno dialogowe, jeśli nie zostaną automatycznie zamknięte i wrócić na kartę przy użyciu portalu Azure.

         Na komputerze bramy te poświadczenia są **zaszyfrowanych** przy użyciu certyfikatu, który jest właścicielem usługi fabryka danych. Jeśli chcesz używać certyfikatu, który jest skojarzony z brama zarządzania danymi w zamian, zobacz [ustawić poświadczenia bezpiecznie](#set-credentials-and-security).    
   3. Kliknij przycisk **Wdróż** na pasku poleceń do wdrożenia programu SQL Server połączonej usługi. Powinny pojawić się połączonej usługi w widoku drzewa.

      ![Usługi SQL Server połączone w widoku drzewa](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Dodaj połączonej usługi dla konta magazynu platformy Azure
1. W **Edytor fabryki danych**, kliknij przycisk **nowy magazyn danych** na pasku poleceń i kliknij przycisk **magazynu Azure**.
2. Wprowadź nazwę konta magazynu Azure dla **nazwa konta**.
3. Wprowadź klucz konta magazynu Azure dla **klucz konta**.
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
2. Zastąp dane JSON w okienku po prawej stronie następujący tekst:

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
   * **tableName** ustawiono **pustych elementów**.
   * **linkedServiceName** ustawiono **SqlServerLinkedService** (tej połączonej usługi ma utworzone we wcześniejszej części tego przewodnika.).
   * Wejściowy zestaw danych nie jest generowany przez inny potok w fabryce danych Azure, należy ustawić **zewnętrznych** do **true**. Wskazuje on, że dane wejściowe jest generowany zewnętrzne względem usługi fabryka danych Azure. Opcjonalnie można określić żadnych zasad danych zewnętrznych przy użyciu **externalData** element **zasad** sekcji.    

   Zobacz [przenoszenie danych do/z programu SQL Server](data-factory-sqlserver-connector.md) szczegółowe informacje na temat właściwości JSON.
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestawu danych.  

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych

1. W **Edytor fabryki danych**, kliknij przycisk **nowy zestaw danych** na pasku poleceń, a następnie kliknij **magazynu obiektów Blob Azure**.
2. Zastąp dane JSON w okienku po prawej stronie następujący tekst:

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
   * **linkedServiceName** ustawiono **AzureStorageLinkedService** (w kroku 2 została utworzona tej połączonej usługi).
   * **folderPath** ustawiono **adftutorial/outfromonpremdf** gdzie outfromonpremdf to folder, w kontenerze adftutorial. Utwórz **adftutorial** kontener, jeśli jeszcze nie istnieje.
   * Parametr **availability** (dostępność) został ustawiony na wartość **hourly** (co godzinę) (parametr **frequency** [częstotliwość] został ustawiony na **hour** [godzinę], a **interval** [interwał] został ustawiony na wartość **1**).  Usługi fabryka danych generuje wycinek danych wyjściowych, co godzinę w **pustych elementów** tabeli w bazie danych SQL Azure.

   Jeśli nie określisz **fileName** dla **tabeli wyników**, wygenerowanych plików w **folderPath** są nazywane w następującym formacie: danych.<Guid>. txt (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Aby ustawić **folderPath** i **fileName** dynamicznie na podstawie **SliceStart** czasu, użyj właściwości partitionedBy. W poniższym przykładzie parametr folderPath używa elementów Year, Month i Day z parametru SliceStart (czas rozpoczęcia przetwarzania wycinka), a parametr fileName używa elementu Hour z parametru SliceStart. Na przykład jeśli wycinek jest generowany dla czasu 2014-10-20T08:00:00, parametr folderName zostaje ustawiony na wikidatagateway/wikisampledataout/2014/10/20, a parametr fileName zostaje ustawiony na wartość 08.csv.

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

    Zobacz [przenoszenie danych do/z magazynu obiektów Blob Azure](data-factory-azure-blob-connector.md) szczegółowe informacje na temat właściwości JSON.
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestawu danych. Upewnij się, że widoczne oba zestawy danych w widoku drzewa.  

## <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku utworzysz **potoku** z jednym **działanie kopiowania** używającą **EmpOnPremSQLTable** jako dane wejściowe i **OutputBlobTable** jako dane wyjściowe.

1. Kliknij w Edytor fabryki danych **... Więcej** i **Nowy potok**.
2. Zastąp dane JSON w okienku po prawej stronie następujący tekst:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on-prem SQL server to blob",
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

   * W sekcji działania jest działanie tylko których **typu** ustawiono **kopiowania**.
   * **Dane wejściowe** dla działania jest ustawiony na **EmpOnPremSQLTable** i **dane wyjściowe** dla działania jest ustawiony na **OutputBlobTable**.
   * W **typeProperties** sekcji **SqlSource** jest określony jako **typ źródła** i ** BlobSink ** jest określony jako **typu sink**.
   * Zapytanie SQL `select * from emp` określono **sqlReaderQuery** właściwość **SqlSource**.

   Zarówno data/godzina rozpoczęcia, jak i data/godzina zakończenia muszą być w [formacie ISO](http://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. Czas **end** jest opcjonalny, ale w tym samouczku zostanie użyty.

   Jeśli nie określisz wartości dla właściwości **end**, zostanie ona obliczona jako „**czas rozpoczęcia + 48 godzin**”. Aby uruchomić potok bezterminowo, określ **9/9/9999** jako wartość dla właściwości **end**.

   Czas trwania przetwarzania wycinków danych na podstawie definiowania **dostępności** właściwości, które zostały zdefiniowane dla każdego zestawu danych z fabryki danych Azure.

   W tym przykładzie występują 24 wycinki danych, ponieważ poszczególne wycinki są generowane co godzinę.        
3. Kliknij przycisk **Wdróż** na pasku poleceń, aby wdrożyć zestawu danych (tabela jest prostokątny zestawu danych). Upewnij się, że potok zostaną wyświetlone w widoku drzewa w **potoki** węzła.  
4. Teraz, kliknij przycisk **X** dwa razy, aby zamknąć stronę, aby wrócić do **fabryki danych** dla strony **ADFTutorialOnPremDF**.

**Gratulacje!** Pomyślnie utworzono fabryki danych Azure, połączone usługi, zestawy danych i potoku i zaplanowane potoku.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Wyświetlanie fabryki danych w widoku diagramu
1. W **portalu Azure**, kliknij przycisk **Diagram** Kafelek na stronie głównej **ADFTutorialOnPremDF** fabryki danych. :

    ![Diagram Link](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Powinien zostać wyświetlony diagram podobny do tego na poniższej ilustracji:

    ![Widok diagramu](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Można powiększać, pomniejszyć, powiększenie do 100%, powiększenia do dopasowania, automatycznie rozmieszczaj potoki i zestawów danych i Pokaż elementy powiązane informacje (wyróżnia elementy nadrzędne i podrzędne wybranego elementu).  Możesz kliknąć dwukrotnie (dataset wejścia/wyjścia lub potoku), aby wyświetlić właściwości dla tego obiektu.

## <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku opisano użycie witryny Azure Portal do monitorowania tego, co dzieje się w fabryce danych Azure. Do monitorowania zestawów danych i potoków można również używać poleceń cmdlet programu PowerShell. Aby uzyskać szczegółowe informacje o monitorowaniu, zobacz [monitorować i zarządzać potoki](data-factory-monitor-manage-pipelines.md).

1. Na diagramie, kliknij dwukrotnie **EmpOnPremSQLTable**.  

    ![Wycinki EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Zwróć uwagę, że wszystkie dane wycinków zapasowej znajdują się w **gotowe** stanu, ponieważ czas trwania procesu (czas rozpoczęcia do czasu zakończenia) przypada w przeszłości. Jest również wstawieniu danych w bazie danych programu SQL Server i jest cały czas. Upewnij się, że brak wycinków widoczne w **wycinków Problem** sekcji u dołu. Aby wyświetlić wszystkie fragmenty, kliknij przycisk **Zobacz więcej** w dolnej części listy wycinków.
3. Teraz, w **zestawów danych** kliknij przycisk **OutputBlobTable**.

    ![Wycinki OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kliknij wycinek żadnych danych z listy i powinna zostać wyświetlona **wycinka danych** strony. Zobaczysz, że działanie jest uruchomione wycinka. Użytkownik widzi tylko jedno działanie Uruchom zwykle.  

    ![Blok wycinek danych](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Jeśli wycinek nie znajduje się w stanie **Gotowe**, możesz wyświetlić wycinki strumienia wychodzącego, które nie są w stanie Gotowe i blokują wykonanie bieżącego wycinka na liście **Niegotowe wycinki strumienia wychodzącego**.
5. Kliknij przycisk **uruchamiania działania** z listy na dole, aby wyświetlić **szczegóły uruchomienia działania**.

   ![Strona szczegółów uruchomienia działania](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Zobaczysz informacje, takie jak przepustowość, czas trwania i bramy używany do transferu danych.
6. Kliknij przycisk **X** zamknąć wszystkie strony, dopóki
7. Wróć do strony głównej **ADFTutorialOnPremDF**.
8. (opcjonalnie) Kliknij przycisk **potoki**, kliknij przycisk **ADFTutorialOnPremDF**oraz szczegółowy tabel wejściowych (**zużyto**) lub wyjściowych zestawów danych (**produkcji**).
9. Użyj narzędzia takiego jak [Eksploratora usługi Storage Microsoft](http://storageexplorer.com/) Aby sprawdzić, czy obiekt blob/plik został utworzony dla każdej godziny.

   ![Eksplorator usługi Azure Storage](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) artykuł, aby wszystkie szczegółowe informacje o brama zarządzania danymi.
* Zobacz [skopiować dane z obiektu Blob Azure do usługi Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) Aby dowiedzieć się więcej o sposobie używania działania kopiowania do przenoszenia danych z magazynu danych źródła do ujścia magazynu danych.
