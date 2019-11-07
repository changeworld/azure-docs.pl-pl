---
title: Kopiowanie danych za pomocą narzędzia Kopiowanie danych platformy Azure
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z usługi Azure Blob Storage do usługi SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: jingwang
ms.openlocfilehash: 4646d7429dc4b3286f6af8861eaf7f1e6e27a760
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683608"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopiowanie danych z usługi Azure Blob Storage do usługi SQL Database przy użyciu narzędzia do kopiowania danych
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](tutorial-copy-data-tool.md)

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie użyj narzędzia Kopiowanie danych, aby utworzyć potok, który kopiuje dane z usługi Azure Blob Storage do bazy danych SQL.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:
> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: użyj usługi Blob Storage jako _źródłowego_ magazynu danych. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).
* **Azure SQL Database**: jako magazynu danych będącego _ujściem_ użyj bazy danych SQL. Jeśli nie masz usługi SQL Database, zobacz instrukcje w temacie [Tworzenie bazy danych SQL](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Przygotuj usługi Blob Storage i SQL Database pod kątem tego samouczka, wykonując następujące kroki.

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom program **Notatnik**. Skopiuj poniższy tekst i zapisz go na dysku w pliku o nazwie **inputEmp.txt**:

    ```
    John|Doe
    Jane|Doe
    ```

1. Utwórz kontener o nazwie **adfv2tutorial** i przekaż plik inputEmp.txt do kontenera. Aby wykonać te zadania, można użyć Azure Portal lub różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) .

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

2. Zezwól usługom platformy Azure na dostęp do programu SQL Server. Sprawdź, czy ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone na serwerze, na którym działa usługa SQL Database. To ustawienie umożliwia usłudze Data Factory zapisywanie danych w danym wystąpieniu bazy danych. Aby sprawdzić i włączyć to ustawienie, przejdź do pozycji Azure SQL Server > Overview > Ustaw zaporę serwera > Ustaw opcję **Zezwalaj na dostęp do usług platformy Azure** na wartość **włączone**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób** > **Analytics** > **Data Factory**:
    
    ![Tworzenie nowej fabryki danych](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

    Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:
    
    ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

    Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_ **ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
1. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych.
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
    
    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.
    
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).

1. W obszarze **Wersja** wybierz wersję **V2**.
1. W obszarze **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
    
    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
1. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych.

    ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)
1. Na stronie **Właściwości** w obszarze **Nazwa zadania**, wprowadź wartość **CopyFromBlobToSqlPipeline**. Następnie wybierz przycisk **Dalej**. Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania.

1. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz pozycję **Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    d. Na stronie **Nowa połączona usługa** wybierz konto magazynu z listy **Nazwa konta magazynu**, a następnie wybierz przycisk **Zakończ**.

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi źródłowej](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:
    
    a. Kliknij pozycję **Przeglądaj**, aby przejść do folderu **adfv2tutorial/input**, wybierz plik **inputEmp.txt**, a następnie kliknij przycisk **Wybierz**.

    b. Kliknij przycisk **Dalej**, aby przejść do następnego kroku.

1. Na stronie **Ustawienia formatu pliku** zwróć uwagę, że narzędzie automatycznie wykrywa ograniczniki kolumn i wierszy. Wybierz opcję **Dalej**. Na tej stronie możesz także wyświetlić podgląd danych i wyświetlić schemat danych wejściowych.

    ![Ustawienia formatu pliku](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Na stronie **Docelowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz **Azure SQL Database** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    d. Na stronie **Nowa połączona usługa** wybierz nazwę serwera i nazwę bazy danych z listy rozwijanej, wprowadź nazwę użytkownika i hasło, a następnie wybierz przycisk **Zakończ**.

    ![Konfigurowanie bazy danych Azure SQL](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Wybierz nowo utworzoną połączoną usługę jako ujście, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi ujścia](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Na stronie **Mapowanie tabeli** wybierz tabelę **[dbo].[emp]** , a następnie kliknij przycisk **Dalej**.

1. Na stronie **Mapowanie schematu** zwróć uwagę, że pierwsza i druga kolumna w pliku wejściowym są mapowane na kolumny **FirstName** i **LastName** tabeli **emp**. Wybierz opcję **Dalej**.

    ![Strona Mapowanie schematu](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Na stronie **Ustawienia** wybierz przycisk **Dalej**.
1. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.
1. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).
1. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę.

1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Aby powrócić do widoku uruchomienia potoków, wybierz link **uruchomienia potoku** u góry. Aby odświeżyć widok, wybierz pozycję **Odśwież**.

    ![Monitorowanie uruchomień działania](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Sprawdź, czy dane są wstawiane do tabeli **emp** w usłudze SQL Database.


1. Wybierz kartę **Autor** po lewej stronie, aby przełączyć się w tryb edytora. Za pomocą edytora można zaktualizować usługi połączone, zestawy danych i potoki utworzone przez narzędzie. Aby uzyskać szczegółowe informacje dotyczące edytowania tych jednostek w interfejsie użytkownika usługi Data Factory, zobacz [wersję witryny Azure Portal używaną w tym samouczku](tutorial-copy-data-portal.md).

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z usługi Blob Storage do usługi SQL Database. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

Aby dowiedzieć się, jak kopiować dane ze środowiska lokalnego do chmury, przejdź do następującego samouczka:

>[!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-data-tool.md)
