---
title: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL — Azure | Dokumentacja firmy Microsoft
description: Ten samouczek pokazuje, jak użyć działania kopiowania w potoku usługi Azure Data Factory do kopiowania danych z magazynu obiektów Blob do usługi SQL database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 557228bafc00c3028a1fda520da8fe4ec8c7a6f2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678721"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Samouczek: Kopiowanie danych z magazynu obiektów Blob do usługi SQL Database przy użyciu usługi fabryka danych
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API programu .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md). 

W tym samouczku utworzysz fabrykę danych z potokiem, aby skopiować dane z magazynu obiektów Blob do usługi SQL database.

Działanie kopiowania wykonuje operację przenoszenia danych w usłudze Azure Data Factory. Jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Szczegółowe informacje dotyczące działania kopiowania znajdują się w artykule [Data Movement Activities](data-factory-data-movement-activities.md) (Działania przenoszenia danych).  

> [!NOTE]
> Aby uzyskać szczegółowe omówienie usługi Data Factory, zobacz [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) artykułu.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka
Przed rozpoczęciem tego samouczka, musisz mieć następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Zobacz [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) artykuł, aby uzyskać szczegółowe informacje.
* **Konto usługi Azure Storage**. Możesz użyć magazynu obiektów blob jako **źródła** magazynu danych w ramach tego samouczka. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).
* **Usługa Azure SQL Database**. Użyj usługi Azure SQL database jako **docelowy** magazynu danych w ramach tego samouczka. Jeśli nie masz usługi Azure SQL database, używanego w tym samouczku, zobacz [sposób tworzenia i konfigurowania usługi Azure SQL Database](../../sql-database/sql-database-get-started.md) ją utworzyć.
* **SQL Server 2012/2014 or Visual Studio 2013**. Tworzenie przykładowej bazy danych i wyświetlić dane wynikowe w bazie danych za pomocą programu SQL Server Management Studio lub Visual Studio.  

## <a name="collect-blob-storage-account-name-and-key"></a>Zbieraj klucza i nazwy konta magazynu obiektów blob
Należy nazwę konta i klucza konta z konta usługi Azure storage, aby skorzystać z tego samouczka. Zanotuj **nazwa konta** i **klucz konta** dla konta usługi Azure storage.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **wszystkich usług** na pasku menu po lewej stronie i wybierz **kont magazynu**.

    ![Przeglądanie — konta magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. W **kont magazynu** bloku wybierz **konta usługi Azure storage** , którą chcesz użyć w tym samouczku.
4. Wybierz **klucze dostępu** łącze w obszarze **ustawienia**.
5. Kliknij przycisk **kopiowania** (obraz) przycisk Dalej, aby **nazwa konta magazynu** tekst pola i Zapisz/wklej je gdzieś (na przykład: w pliku tekstowym).
6. Powtórz poprzedni krok, aby skopiować lub zanotuj **klucz1**.

    ![Klucz dostępu do magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zamknij wszystkie bloki, klikając przycisk **X**.

## <a name="collect-sql-server-database-user-names"></a>Zbieraj programu SQL server, bazy danych, nazwy użytkownika
Należy nazwy serwera Azure SQL, bazy danych i użytkownika, aby skorzystać z tego samouczka. Zanotuj nazwy **serwera**, **bazy danych**, i **użytkownika** dla usługi Azure SQL database.

1. W **witryny Azure portal**, kliknij przycisk **wszystkich usług** po lewej stronie i wybierz pozycję **baz danych SQL**.
2. W **bloku bazy danych SQL**, wybierz opcję **bazy danych** , którą chcesz użyć w tym samouczku. Zanotuj **Nazwa bazy danych**.  
3. W **bazy danych SQL** bloku kliknij **właściwości** w obszarze **ustawienia**.
4. Zanotuj wartości **nazwy serwera** i **identyfikator logowania administratora serwera**.
5. Zamknij wszystkie bloki, klikając przycisk **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Zezwalaj usługom platformy Azure na dostęp do serwera SQL
Upewnij się, że **zezwolić na dostęp do usług platformy Azure** ustawienie wyłączyć **ON** dla serwera Azure SQL, aby usługa Data Factory można uzyskiwać dostęp do serwera Azure SQL. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

1. Kliknij przycisk **wszystkich usług** Centrum po lewej stronie i kliknij przycisk **serwerów SQL**.
2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
3. W bloku **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług Azure**.
4. Zamknij wszystkie bloki, klikając przycisk **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Przygotuj usługi Blob Storage i bazy danych SQL
Teraz Przygotuj swój usługi Azure blob storage i bazy danych Azure SQL dla tego samouczka, wykonując następujące czynności:  

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go jako **emp.txt** do **C:\ADFGetStarted** folder na dysku twardym.

    ```
    John, Doe
    Jane, Doe
    ```
2. Użyj narzędzi takich jak [Eksplorator magazynu Azure](https://storageexplorer.com/) do utworzenia kontenera **adftutorial** i przekazania pliku **emp.txt** do kontenera.

3. Poniższy skrypt SQL umożliwia utworzenie tabeli **emp** w bazie danych SQL Azure.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Jeśli masz zainstalowany program SQL Server 2012/2014 na komputerze:** postępuj zgodnie z instrukcjami z [zarządzania usługi Azure SQL Database przy użyciu programu SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) Aby nawiązać połączenie z serwerem Azure SQL i uruchomić skrypt SQL. 

    Jeśli klient nie ma dostępu do serwera SQL Azure, musisz skonfigurować zaporę serwera SQL Azure tak, aby dostęp z Twojego komputera (adresu IP) był dozwolony. W [tym artykule](../../sql-database/sql-database-configure-firewall-settings.md) opisano kroki konfigurowania zapory dla serwera SQL Azure.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Wymagań wstępnych została ukończona. Można utworzyć fabryki danych przy użyciu jednej z następujących sposobów. Kliknij jedną z opcji na liście rozwijanej u góry lub poniższe linki do wykonania tego samouczka.     

* [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
* [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [Interfejs API programu .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Nie przekształca on danych wejściowych w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: tworzenie pierwszego potoku w celu przekształcania danych przy użyciu klastra Hadoop).
> 
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory). 
