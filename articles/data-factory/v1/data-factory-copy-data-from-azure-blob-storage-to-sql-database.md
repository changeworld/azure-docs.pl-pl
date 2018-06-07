---
title: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL — Azure | Dokumentacja firmy Microsoft
description: Ten samouczek przedstawia sposób użycia działanie kopiowania w potoku fabryki danych Azure, aby skopiować dane z magazynu obiektów Blob do bazy danych SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6575a68423df97d7727401b79ded44545a43afca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621109"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Samouczek: Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL przy użyciu fabryki danych
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
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi Data Factory, która jest w wersji zapoznawczej, zapoznaj się z [dokumentacją samouczka działania kopiowania w wersji 2](../quickstart-create-data-factory-dot-net.md). 

W tym samouczku utworzysz fabryki danych z potoku, aby skopiować dane z magazynu obiektów Blob do bazy danych SQL.

Działanie kopiowania wykonuje operację przenoszenia danych w usłudze Azure Data Factory. Jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Szczegółowe informacje dotyczące działania kopiowania znajdują się w artykule [Data Movement Activities](data-factory-data-movement-activities.md) (Działania przenoszenia danych).  

> [!NOTE]
> Szczegółowe omówienie usługi fabryka danych, zobacz [wprowadzenie do fabryki danych Azure](data-factory-introduction.md) artykułu.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne dotyczące samouczka
Przed rozpoczęciem tego samouczka wymagane są następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Zobacz [bezpłatnej wersji próbnej](http://azure.microsoft.com/pricing/free-trial/) artykułu, aby uzyskać szczegółowe informacje.
* **Konto magazynu Azure**. Użyj magazynu obiektów blob jako **źródła** magazynu danych, w tym samouczku. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Usługa Azure SQL Database**. Użyj bazy danych Azure SQL jako **docelowego** magazynu danych, w tym samouczku. Jeśli nie masz bazy danych Azure SQL można użyć w instrukcji, zobacz [sposobu tworzenia i konfigurowania bazy danych SQL Azure](../../sql-database/sql-database-get-started.md) go utworzyć.
* **2014 programu SQL Server 2012 lub Visual Studio 2013**. Użyj programu SQL Server Management Studio lub Visual Studio do tworzenia przykładowej bazy danych i wyświetlać dane wynikowe w bazie danych.  

## <a name="collect-blob-storage-account-name-and-key"></a>Zbierać nazwy konta magazynu obiektów blob i kluczy
Należy nazwę konta i klucz konta magazynu Azure w celu tego samouczka. Należy zanotować **nazwa konta** i **klucz konta** dla konta magazynu Azure.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij przycisk **wszystkie usługi** w menu po lewej stronie i wybierz **kont magazynu**.

    ![Przeglądaj - kont magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. W **kont magazynu** bloku, wybierz opcję **konto magazynu Azure** , które mają być używane w tym samouczku.
4. Wybierz **klucze dostępu** łącze w obszarze **ustawienia**.
5. Kliknij przycisk **kopiowania** (obraz) przycisk Dalej, aby **nazwy konta magazynu** tekst pola i Zapisz/wklej go innym (na przykład: w pliku tekstowym).
6. Powtórz poprzedni krok, aby skopiować lub notowanie niezbędnych **klucz1**.

    ![Klucz dostępu do magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zamknij wszystkie bloki, klikając **X**.

## <a name="collect-sql-server-database-user-names"></a>Zbieraj programu SQL server, bazy danych, nazwy użytkowników
Należy nazwy serwera Azure SQL, bazy danych i wykonywać w tym samouczku. Zanotuj nazwy **serwera**, **bazy danych**, i **użytkownika** bazy danych Azure SQL.

1. W **portalu Azure**, kliknij przycisk **wszystkie usługi** po lewej i wybierz **baz danych SQL**.
2. W **bloku bazy danych SQL**, wybierz pozycję **bazy danych** , które mają być używane w tym samouczku. Należy zanotować **Nazwa bazy danych**.  
3. W **bazy danych SQL** bloku, kliknij przycisk **właściwości** w obszarze **ustawienia**.
4. Zanotuj wartości **nazwy serwera** i **identyfikator logowania administratora serwera**.
5. Zamknij wszystkie bloki, klikając **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Zezwalaj usługom platformy Azure na dostęp do serwera SQL
Upewnij się, że **zezwolić na dostęp do usług platformy Azure** ustawienie włączenia **ON** serwera Azure SQL, aby usługi fabryka danych można uzyskać dostępu do serwera Azure SQL. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

1. Kliknij przycisk **wszystkie usługi** koncentratora po lewej i kliknij przycisk **serwerów SQL**.
2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
3. W bloku **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług Azure**.
4. Zamknij wszystkie bloki, klikając **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Przygotowanie magazynu obiektów Blob i bazy danych SQL
Teraz Przygotuj magazyn obiektów blob platformy Azure i bazy danych Azure SQL dla tego samouczka, wykonując następujące czynności:  

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go jako **emp.txt** do **C:\ADFGetStarted** folderu na dysku twardym.

    ```
    John, Doe
    Jane, Doe
    ```
2. Użyj narzędzi takich jak [Eksplorator magazynu Azure](http://storageexplorer.com/) do utworzenia kontenera **adftutorial** i przekazania pliku **emp.txt** do kontenera.

    ![Eksplorator usługi Storage platformy Azure. Kopiowanie danych z magazynu obiektów Blob do bazy danych SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
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

    **Jeśli masz programu SQL Server 2012/2014 zainstalowanej na komputerze:** postępuj zgodnie z instrukcjami [Zarządzanie bazą danych SQL Azure przy użyciu programu SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) do połączenia się z serwerem Azure SQL, a następnie uruchom skrypt SQL. 

    Jeśli klient nie ma dostępu do serwera SQL Azure, musisz skonfigurować zaporę serwera SQL Azure tak, aby dostęp z Twojego komputera (adresu IP) był dozwolony. W [tym artykule](../../sql-database/sql-database-configure-firewall-settings.md) opisano kroki konfigurowania zapory dla serwera SQL Azure.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Sprawdzanie wymagań wstępnych została ukończona. Można utworzyć fabryki danych przy użyciu jednej z następujących sposobów. Kliknij jedną z opcji na liście rozwijanej na górze lub poniższe linki do wykonania w samouczku.     

* [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
* [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Program Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Program PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Interfejs API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [Interfejs API programu .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Nie przekształca on danych wejściowych w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie pierwszego potoku przekształcającego dane przy użyciu klastra Hadoop).
> 
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory). 
