---
title: 'Samouczek: tworzenie potoku usługi Azure Data Factory w celu kopiowania danych (witryna Azure Portal) | Microsoft Docs'
description: W tym samouczku opisano korzystanie z witryny Azure Portal w celu utworzenia potoku usługi Azure Data Factory z działaniem kopiowania, aby skopiować dane z magazynu obiektów blob Azure do bazy danych Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 168e5eea2004a31b108a8a00d883298f8e2198ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60566526"
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Samouczek: korzystanie z witryny Azure Portal, aby utworzyć potok usługi Data Factory w celu kopiowania danych 
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API programu .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 

W tym artykule wyjaśniono, jak używać witryny [Azure Portal](https://portal.azure.com) do tworzenia fabryki danych obejmującej potok, który kopiuje dane z magazynu obiektów blob w usłudze Azure do bazy danych Azure SQL Database. Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem instrukcji z tego samouczka zapoznaj się z artykułem [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md).   

W tym samouczku opisano tworzenie potoku z jednym działaniem: działaniem kopiowania. Działanie kopiowania kopiuje dane z obsługiwanego magazynu danych do obsługiwanego magazynu danych ujścia. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Działanie jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Więcej informacji o działaniu kopiowania znajduje się w artykule dotyczącym [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md).

Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [wielu działań w potoku](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: przekształcanie danych przy użyciu klastra Hadoop).

## <a name="prerequisites"></a>Wymagania wstępne
Przed wykonaniem instrukcji z tego samouczka należy spełnić wymagania wstępne podane w artykule dotyczącym [wymagań wstępnych samouczka](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="steps"></a>Kroki
Poniżej przedstawiono kroki do wykonania w ramach tego samouczka:

1. Utworzenie **fabryki danych** na platformie Azure. W tym kroku opisano tworzenie fabryki danych o nazwie ADFTutorialDataFactory. 
2. Utworzenie **połączonych usług** w fabryce danych. W tym kroku utworzysz dwie połączone usługi typu: Azure Storage i Azure SQL Database. 
    
    Polecenie AzureStorageLinkedService łączy konto usługi Azure Storage z fabryką danych. W ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) utworzono kontener i przekazano dane na to konto magazynu.   

    Polecenie AzureSqlLinkedService łączy bazę danych Azure SQL Database z fabryką danych. W tej bazie danych są przechowywane dane skopiowane z magazynu obiektów blob. W ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) w tej bazie danych została utworzona tabela SQL.   
3. Utworzenie wejściowych i wyjściowych **zestawów danych** w fabryce danych.  
    
    Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. Natomiast wejściowy zestaw danych obiektów blob określa kontener oraz folder, który zawiera dane wejściowe.  

    W analogiczny sposób połączona usługa Azure SQL Database określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z bazą danych Azure SQL Database. Wyjściowy zestaw danych tabeli SQL określa tabelę w bazie danych, do której są kopiowane dane z magazynu obiektów blob.
4. Utworzenie **potoku** w fabryce danych. W tym kroku jest tworzony potok za pomocą działania kopiowania.   
    
    Działanie kopiowania kopiuje dane z obiektu blob w magazynie obiektów blob platformy Azure do tabeli w bazie danych Azure SQL Database. Działania kopiowania w potoku można użyć do kopiowania danych z dowolnego obsługiwanego źródła do dowolnej obsługiwanej lokalizacji docelowej. Listę obsługiwanych magazynów danych można znaleźć w artykule [Działania związane z przenoszeniem danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Monitorowanie potoku. Ten krok polega na **monitorowaniu** wycinków wejściowych i wyjściowych zestawów danych przy użyciu witryny Azure Portal. 

## <a name="create-data-factory"></a>Tworzenie fabryki danych
> [!IMPORTANT]
> Jeśli jeszcze nie zostało to zrobione, spełnij [wymagania wstępne dotyczące samouczka](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Na przykład działanie kopiowania może służyć do skopiowania danych ze źródła do docelowego magazynu danych, a działanie programu Hive w usłudze HDInsight do uruchomienia skryptu programu Hive, który przekształci dane wejściowe w dane wyjściowe produktu. Zacznijmy tworzenie fabryki danych w tym kroku.

1. Po zalogowaniu się w witrynie [Azure Portal](https://portal.azure.com/) kliknij pozycję **Utwórz zasób** w menu po lewej stronie, a następnie pozycje **Dane + analiza** i **Fabryka danych**. 
   
   ![Nowy-> Fabryka danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. W bloku **Nowa fabryka danych**:
   
   1. Wprowadź wartość **ADFTutorialDataFactory** dla opcji **Nazwa**. 
      
         ![Blok Nowa fabryka danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Nazwa fabryki danych jest niedostępna](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
   3. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
      
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
          W niektórych krokach w tym samouczku zakłada się, że dla grupy zasobów używasz nazwy: **ADFTutorialResourceGroup**. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
   4. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko regiony obsługiwane przez usługę Data Factory.
   5. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
   6. Kliknij pozycję **Utwórz**.
      
      > [!IMPORTANT]
      > Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
      > 
      > W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa DNS, a wówczas stanie się widoczna publicznie.                
      > 
      > 
3. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Po zakończeniu tworzenia zostanie wyświetlony blok **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
   ![Strona główna fabryki danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku nie przedstawiono korzystania z żadnych usług obliczeniowych, takich jak Azure HDInsight czy Azure Data Lake Analytics. Zostają użyte dwa magazyny danych typu Azure Storage (źródło) i Azure SQL Database (lokalizacja docelowa). 

W związku z tym tworzy się dwie połączone usługi o nazwie AzureStorageLinkedService i AzureSqlLinkedService typu: AzureStorage i AzureSqlDatabase.  

Polecenie AzureStorageLinkedService łączy konto usługi Azure Storage z fabryką danych. Na tym koncie magazynu utworzono kontener i przekazano na nie dane w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Polecenie AzureSqlLinkedService łączy bazę danych Azure SQL Database z fabryką danych. W tej bazie danych są przechowywane dane skopiowane z magazynu obiektów blob. Tabelę emp w tej bazie danych utworzono w ramach [wymagań wstępnych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).  

### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
W tym kroku opisano łączenie konta usługi Azure Storage z fabryką danych. W tej sekcji określa się nazwę i klucz konta magazynu platformy Azure.  

1. W bloku **Fabryka danych** kliknij kafelek **Utwórz i wdróż**.
   
   ![Kafelek Utwórz i wdróż](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Zostanie wyświetlony **Edytor fabryki danych** — jak widać na poniższej ilustracji: 

    ![Edytor fabryki danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. W edytorze kliknij przycisk **Nowy magazyn danych** na pasku narzędzi i z menu rozwijanego wybierz opcję **Magazyn Azure**. W okienku po prawej stronie powinien zostać wyświetlony szablon JSON do tworzenia połączonej usługi magazynu Azure. 
   
    ![Przycisk Nowy magazyn danych w edytorze](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Zastąp parametry `<accountname>` i `<accountkey>` wartościami nazwy konta i klucza konta magazynu Azure. 
   
    ![Skrypt JSON w edytorze usługi Blob Storage](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Kliknij pozycję **Wdróż** na pasku narzędzi. W widoku drzewa powinna być widoczna wdrożona usługa **AzureStorageLinkedService**. 
   
    ![Przycisk Wdróż w edytorze usługi Blob Storage](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Aby uzyskać więcej informacji na temat właściwości JSON w definicji połączonej usługi, zobacz artykuł dotyczący [łącznika usługi Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Tworzenie połączonej usługi dla usługi Azure SQL Database
W tym kroku opisano łączenie bazy danych Azure SQL Database z fabryką danych. W tej sekcji określa się nazwę serwera usługi Azure SQL, nazwę bazy danych, nazwę użytkownika i hasło użytkownika. 

1. W **Edytorze fabryki danych** kliknij przycisk **Nowy magazyn danych** na pasku narzędzi i wybierz opcję **Azure SQL Database** z menu rozwijanego. W okienku po prawej stronie powinien zostać wyświetlony szablon JSON do tworzenia połączonej usługi serwera SQL Azure.
2. Zastąp parametry `<servername>`, `<databasename>`, `<username>@<servername>` i `<password>`, wpisując nazwy serwera SQL Azure, bazy danych i konta użytkownika oraz hasło. 
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć usługę **AzureSqlLinkedService**.
4. Upewnij się, że w widoku drzewa w obszarze **Połączone usługi** jest wyświetlana nazwa usługi **AzureSqlLinkedService**.  

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz artykuł dotyczący [łącznika usługi Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Tworzenie zestawów danych
W poprzednim kroku zostały utworzone połączone usługi używane do połączenia konta usługi Azure Storage i bazy danych Azure SQL Database z fabryką danych. W tym kroku zostaną zdefiniowane dwa zestawy danych o nazwach InputDataset i OutputDataset zawierające dane wejściowe i wyjściowe przechowywane w magazynach danych, do których odwołują się usługi AzureStorageLinkedService i AzureSqlLinkedService.

Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. Natomiast wejściowy zestaw danych obiektów blob (InputDataset) określa kontener oraz folder, który zawiera dane wejściowe.  

W analogiczny sposób połączona usługa Azure SQL Database określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z bazą danych Azure SQL Database. Wyjściowy zestaw danych tabeli SQL (OutputDataset) określa tabelę w bazie danych, do której są kopiowane dane z magazynu obiektów blob. 

### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
W tym kroku opisano tworzenie zestawu danych o nazwie InputDataset wskazującego na plik obiektów blob (emp.txt) w katalogu głównym kontenera obiektów blob (adftutorial) w usłudze Azure Storage reprezentowany przez połączoną usługę AzureStorageLinkedService. Jeśli nie określisz wartości obiektu fileName lub ją pominiesz, dane ze wszystkich obiektów blob w folderze wejściowym zostaną skopiowane do lokalizacji docelowej. W tym samouczku wartość obiektu fileName jest określona. 

1. W **edytorze** fabryki danych kliknij kolejno polecenia  **Więcej**, **Nowy zestaw danych** i **Azure Blob Storage** w menu rozwijanym. 
   
    ![Menu Nowy zestaw danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Zastąp kod JSON w prawym okienku następującym fragmentem kodu JSON: 
   
    ```json
    {
      "name": "InputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```   

    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

    | Właściwość | Opis |
    |:--- |:--- |
    | type | Właściwość typu jest ustawiona na wartość **AzureBlob**, ponieważ dane znajdują się w magazynie obiektów blob na platformie Azure. |
    | linkedServiceName | Odnosi się do utworzonego wcześniej elementu **AzureStorageLinkedService**. |
    | folderPath | Określa **kontener** obiektów blob oraz **folder**, który zawiera wejściowe obiekty blob. W tym samouczku kontenerem obiektów blob jest adftutorial, a folderem — katalog główny. | 
    | fileName | Ta właściwość jest opcjonalna. Jeśli pominiesz tę właściwość, zostaną wybrane wszystkie pliki z folderu folderPath. W tym samouczku dla fileName określono plik **emp.txt**, więc tylko on zostanie wybrany do przetwarzania. |
    | format -> type |Plik wejściowy jest w formacie tekstowym, więc należy użyć właściwości **TextFormat**. |
    | columnDelimiter | Kolumny w pliku wejściowym są rozdzielane **przecinkami (`,`)**. |
    | frequency/interval | Właściwość frequency (częstotliwość) jest ustawiona na wartość **Hour** (Godzina), a wartość interwału wynosi **1**, co oznacza, że wycinki wejściowe są dostępne **co godzinę**. Innymi słowy, usługa Data Factory szuka danych wejściowych co godzinę w folderze głównym określonego kontenera obiektów blob (**adftutorial**). Wyszukuje dane między godzinami rozpoczęcia i zakończenia potoku, a nie przed nimi ani po nich.  |
    | external | Ta właściwość ma wartość **true** (prawda), jeśli dane nie są generowane przez ten potok. Dane wejściowe w tym samouczku znajdują się w pliku emp.txt, który nie jest generowany w tym potoku, więc możemy ustawić tę właściwość na true. |

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz [artykuł dotyczący łącznika obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties).      
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć zestaw danych **InputDataset**. Upewnij się, że zestaw **InputDataset** jest wyświetlany w widoku drzewa.

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Połączona usługa Azure SQL Database określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z bazą danych Azure SQL Database. Wyjściowy zestaw danych tabeli SQL (OutputDataset) tworzony w tym kroku określa tabelę w bazie danych, do której są kopiowane dane z magazynu obiektów blob.

1. W **edytorze** fabryki danych kliknij kolejno polecenia  **Więcej**, **Nowy zestaw danych** i **SQL Azure** w menu rozwijanym. 
2. Zastąp kod JSON w prawym okienku następującym fragmentem kodu JSON:

    ```json   
    {
      "name": "OutputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```     

    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

    | Właściwość | Opis |
    |:--- |:--- |
    | type | Właściwość typu jest ustawiona na **AzureSqlTable**, ponieważ dane są kopiowane do tabeli w bazie danych Azure SQL Database. |
    | linkedServiceName | Odnosi się do utworzonego wcześniej elementu **AzureSqlLinkedService**. |
    | tableName | Określa **tabelę** , do której są kopiowane dane. | 
    | frequency/interval | Właściwość frequency (częstotliwość) jest ustawiona na wartość **Hour** (Godzina), a wartość interwału wynosi **1**, co oznacza, że wycinki wyjściowe są tworzone **co godzinę** między godziną rozpoczęcia i zakończenia potoku, a nie przed tą godziną lub po niej.  |

    Tabela emp bazy danych zawiera trzy kolumny — **ID**, **FirstName** i **LastName**. ID to kolumna tożsamości, więc należy określić tylko wartości **FirstName** i **LastName**.

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz [artykuł dotyczący łącznika usługi Azure SQL](data-factory-azure-sql-connector.md#dataset-properties).
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć zestaw danych **OutputDataset**. Upewnij się, że zestaw **OutputDataset** jest wyświetlany w widoku drzewa w obszarze **Datasets**. 

## <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku opisano tworzenie potoku za pomocą **działania kopiowania**, w którym parametr **InputDataset** jest używany jako dane wejściowe, a parametr **OutputDataset** jako dane wyjściowe.

Obecnie harmonogram jest prowadzony przy użyciu wyjściowego zestawu danych. W tym samouczku wyjściowy zestaw danych jest konfigurowany do tworzenia wycinka co godzinę. Potok ma godzinę rozpoczęcia i zakończenia, między którymi następuje jeden dzień różnicy (dokładnie 24 godziny). Potok tworzy więc 24 wycinki wyjściowego zestawu danych. 

1. W **edytorze** fabryki danych kliknij kolejno polecenia  **Więcej** i **Nowy potok**. Możesz również kliknąć prawym przyciskiem myszy opcję **Potoki** w widoku drzewa i kliknąć opcję **Nowy potok**.
2. Zastąp kod JSON w prawym okienku następującym fragmentem kodu JSON: 

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Pamiętaj o następujących kwestiach:
   
   - W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**. Więcej informacji o działaniu kopiowania znajduje się w artykule dotyczącym [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md). W rozwiązaniach usługi Data Factory można również użyć [działań dotyczących przekształcania danych](data-factory-data-transformation-activities.md).
   - Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**. 
   - W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia. Aby uzyskać pełną listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz informacje dotyczące [obsługiwanych magazynów danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aby dowiedzieć się, jak używać określonego obsługiwanego magazynu danych jako źródła/ujścia, kliknij link w tabeli.
   - Zarówno data/godzina rozpoczęcia, jak i data/godzina zakończenia muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2016-10-14T16:32:41Z. Czas **end** jest opcjonalny, ale w tym samouczku zostanie użyty. Jeśli nie określisz wartości dla właściwości **end**, zostanie ona obliczona jako „**czas rozpoczęcia + 48 godzin**”. Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**.
     
     W powyższym przykładzie występują 24 wycinki danych, gdyż poszczególne wycinki są generowane co godzinę.

     Opisy właściwości JSON w definicji potoku znajdują się w artykule dotyczącym [tworzenia potoków](data-factory-create-pipelines.md). Opisy właściwości JSON w definicji działania kopiowania znajdują się w artykule dotyczącym [działań związanych z przenoszeniem danych](data-factory-data-movement-activities.md). Opisy właściwości JSON obsługiwanych przez BlobSource można znaleźć w [artykule dotyczącym łącznika usługi Azure Blob](data-factory-azure-blob-connector.md). Opisy właściwości JSON obsługiwanych przez SqlSink można znaleźć w artykule [dotyczącym łącznika usługi Azure SQL Database](data-factory-azure-sql-connector.md).
3. Kliknij przycisk **Wdróż** na pasku narzędzi, aby utworzyć i wdrożyć element **ADFTutorialPipeline**. Upewnij się, że potok jest wyświetlany w widoku drzewa. 
4. Teraz zamknij blok **Edytor**, klikając przycisk **X**. Kliknij ponownie przycisk **X**, aby wyświetlić stronę główną **fabryki danych** dla fabryki **ADFTutorialDataFactory**.

**Gratulacje!** Fabryka danych Azure z potokiem kopiującym dane z usługi Azure Blob Storage do bazy danych Azure SQL Database została pomyślnie utworzona. 


## <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku opisano użycie witryny Azure Portal do monitorowania tego, co dzieje się w fabryce danych Azure.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorowanie potoku przy użyciu aplikacji Monitorowanie i zarządzanie
Poniższe kroki pokazują, jak monitorować potoki w fabryce danych za pomocą aplikacji Monitorowanie i zarządzanie: 

1. Kliknij kafelek **Monitorowanie i zarządzanie** na stronie głównej fabryki danych.
   
    ![Kafelek Monitorowanie i zarządzanie](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. **Aplikacja Monitorowanie i zarządzanie** powinna zostać wyświetlona na osobnej karcie. 

    > [!NOTE]
    > Jeśli przeglądarka internetowa wyświetla cały czas komunikat „Autoryzowanie...”, wykonaj jedną z następujących czynności: wyczyść pole wyboru **Blokuj pliki cookie i dane witryn innych firm** (lub) utwórz wyjątek dla witryny **login.microsoftonline.com**, a następnie spróbuj ponownie otworzyć aplikację.

    ![Aplikacja Monitorowanie i zarządzanie](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Zmień **godzinę rozpoczęcia** i **godzinę zakończenia** na termin rozpoczęcia (2017-05-11) i zakończenia (2017-05-12) potoku, a następnie kliknij przycisk **Zastosuj**.       
3. Zostaną wyświetlone **okna działania** skojarzone z każdą godziną w zakresie od godziny rozpoczęcia do godziny zakończenia potoku na liście w środkowym okienku. 
4. Aby wyświetlić szczegóły okna działania, wyświetl to okno na liście **Okno działania**. 
    ![Szczegóły okna działania](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    W Eksploratorze okien działania po prawej stronie można zobaczyć, że wszystkie wycinki do bieżącej godziny w strefie UTC (20:12) są przetwarzane (kolor zielony). Wycinki 20–21, 21–22, 22–23, 23–12 nie zostały jeszcze przetworzone.

    Sekcja **Próby** w okienku po prawej stronie zawiera informacje o przebiegu działania dla wycinka danych. Jeśli wystąpił błąd, dostępne są szczegółowe informacje o nim. Na przykład: jeśli folder wejściowy lub kontener nie istnieje i przetwarzanie wycinka kończy się niepowodzeniem, pojawi się komunikat o błędzie zawierający informację o tym, że kontener lub folder nie istnieje.

    ![Próby uruchomienia działania](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Uruchom program **SQL Server Management Studio**, połącz się z usługą Azure SQL Database i upewnij się, że wiersze zostały wstawione do tabeli **emp** w bazie danych.
    
    ![wyniki zapytania sql](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Szczegółowe informacje dotyczące korzystania z aplikacji znajdują się w artykule [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md) (Monitorowanie potoków usługi Azure Data Factory oraz zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie).

### <a name="monitor-pipeline-using-diagram-view"></a>Monitorowanie potoku przy użyciu widoku diagramu
Można również monitorować potoki danych, korzystając z widoku diagramu.  

1. W bloku **Fabryka danych** kliknij opcję **Diagram**.
   
    ![Blok Fabryka danych — kafelek Diagram](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Powinien zostać wyświetlony diagram podobny do tego na poniższej ilustracji: 
   
    ![Widok diagramu](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. W widoku diagramu kliknij dwukrotnie pozycję **InputDataset**, aby wyświetlić wycinki dla zestawu danych.  
   
    ![Zestawy danych z wybranym zestawem InputDataset](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Kliknij link **Zobacz więcej**, aby wyświetlić wszystkie wycinki danych. Zostaną wyświetlone wycinki w zakresie 24 godzin od czasu rozpoczęcia do zakończenia potoku. 
   
    ![Wszystkie wycinki danych wejściowych](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Należy zwrócić uwagę na to, że wszystkie wycinki danych do bieżącego czasu UTC są w stanie **Gotowe**, ponieważ plik **emp.txt** znajduje się cały czas w kontenerze obiektów blob **adftutorial\input**. Wycinki dla godzin w przyszłości nie są jeszcze w stanie Gotowe. Upewnij się, że żadne wycinki nie są wyświetlane w sekcji **Ostatnie wycinki z błędami** na dole.
6. Zamknij bloki, aż zostanie wyświetlony widok diagramu (lub) przewiń w lewo, aby wyświetlić widok diagramu. Następnie kliknij dwukrotnie pozycję **OutputDataset**. 
8. Kliknij link **Zobacz więcej** w bloku **Tabela** dla **OutputDataset**, aby wyświetlić wszystkie wycinki.

    ![blok Wycinki danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Należy zwrócić uwagę na to, że wszystkie wycinki do bieżącej godziny UTC są przenoszone ze stanu **oczekiwanie na wykonanie** => do stanu **W toku** ==> **Gotowe**. Wycinki wcześniejsze (przed bieżącą godziną) są domyślnie przetwarzane od najnowszych do najstarszych. Na przykład: jeśli bieżąca godzina to 20:12 UTC, wycinek 19–20 będzie przetwarzany przed wycinkiem 18–19. Wycinek 20–21 jest domyślnie przetwarzany na końcu interwału, tj. po godzinie 21:00.  
10. Po kliknięciu dowolnego wycinka danych na liście powinien zostać wyświetlony blok **Wycinek danych**. Element danych skojarzony z oknem działania jest nazywany wycinkiem. Wycinkiem może być jeden plik lub wiele plików.  
    
     ![blok Wycinek danych](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Jeśli wycinek nie znajduje się w stanie **Gotowe**, możesz wyświetlić wycinki strumienia wychodzącego, które nie są w stanie Gotowe i blokują wykonanie bieżącego wycinka na liście **Niegotowe wycinki strumienia wychodzącego**.
11. W bloku **WYCINEK DANYCH** na liście u dołu powinny zostać wyświetlone wszystkie uruchomienia działania. Kliknij pozycję **uruchomienie działania**, aby wyświetlić blok **Szczegóły uruchamiania działania**. 
    
    ![Szczegóły uruchamiania działania](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    W tym bloku znajdują się informacje dotyczące tego, ile czasu trwała operacja kopiowania, jaka jest przepływność, ile bajtów danych zostało odczytanych i zapisanych, godzina rozpoczęcia przebiegu, godzina zakończenia przebiegu itd.  
12. Kliknij przycisk **X**, aby zamknąć wszystkie bloki przed powrotem do bloku głównego fabryki **ADFTutorialDataFactory**.
13. (Opcjonalnie) kliknij kafelek **Datasets** lub **Potoki**, aby wyświetlić bloki z poprzednich plików. 
14. Uruchom program **SQL Server Management Studio**, połącz się z usługą Azure SQL Database i upewnij się, że wiersze zostały wstawione do tabeli **emp** w bazie danych.
    
    ![wyniki zapytania sql](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Podsumowanie
W tym samouczku opisano tworzenie fabryki danych Azure w celu kopiowania danych z obiektu blob platformy Azure do bazy danych Azure SQL Database. Portal Azure został użyty do utworzenia fabryki danych, połączonych usług, zestawów danych oraz potoku. Główne kroki opisane w tym samouczku:  

1. Tworzenie **fabryki danych** Azure.
2. Tworzenie **połączonych usług**:
   1. Połączona usługa **Azure Storage** w celu połączenia z kontem usługi Azure Storage, na którym przechowywane są dane wejściowe.     
   2. Połączona usługa **Azure SQL** w celu połączenia z bazą danych Azure SQL Database, w której przechowywane są dane wyjściowe. 
3. Tworzenie **zestawów danych** opisujących dane wejściowe i wyjściowe dla potoków.
4. Tworzenie **potoku** za pomocą **działania kopiowania**, w którym źródłem jest element **BlobSource**, a ujściem element **SqlSink**.  

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku użyto magazynu obiektów blob platformy Azure jako magazynu danych źródła oraz bazy danych Azure SQL Database jako magazynu danych docelowych w operacji kopiowania. Poniższa tabela zawiera listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i lokalizacje docelowe: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Aby uzyskać informacje dotyczące kopiowania danych do/z magazynu danych, kliknij link do magazynu danych w tabeli.