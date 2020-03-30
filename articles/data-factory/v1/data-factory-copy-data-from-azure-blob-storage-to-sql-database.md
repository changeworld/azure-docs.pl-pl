---
title: Kopiowanie danych z magazynu obiektów blob do bazy danych SQL — Azure
description: W tym samouczku pokazano, jak używać działania kopiowania w potoku usługi Azure Data Factory do kopiowania danych z magazynu obiektów Blob do bazy danych SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc2f0a513219a671dd8a75ee00af4fc9d4c6a68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979723"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Samouczek: Kopiowanie danych z magazynu obiektów blob do bazy danych SQL przy użyciu fabryki danych
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [INTERFEJS API ODPOCZYNKU](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [samouczek dotyczący działania kopiowania](../quickstart-create-data-factory-dot-net.md).

W tym samouczku utworzysz fabrykę danych z potokiem do kopiowania danych z magazynu obiektów Blob do bazy danych SQL.

Działanie kopiowania wykonuje operację przenoszenia danych w usłudze Azure Data Factory. Jest obsługiwane przez globalnie dostępną usługę, która może kopiować dane między różnymi magazynami danych w sposób bezpieczny, niezawodny i skalowalny. Szczegółowe informacje dotyczące działania kopiowania znajdują się w artykule [Data Movement Activities](data-factory-data-movement-activities.md) (Działania przenoszenia danych).  

> [!NOTE]
> Aby uzyskać szczegółowe omówienie usługi Fabryka danych, zobacz [wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md) artykułu.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Wymagania wstępne samouczka
Przed rozpoczęciem tego samouczka należy mieć następujące wymagania wstępne:

* **Subskrypcja platformy Azure**.  Jeśli nie masz subskrypcji, możesz utworzyć konto bezpłatnej wersji próbnej w zaledwie kilka minut. Szczegółowe informacje można znaleźć w artykule [Bezpłatna wersja próbna.](https://azure.microsoft.com/pricing/free-trial/)
* **Konto usługi Azure Storage**. Magazyn obiektów blob służy jako magazynu danych **źródłowych** w tym samouczku. Jeśli nie masz konta magazynu platformy Azure, zobacz [artykuł Tworzenie konta magazynu,](../../storage/common/storage-account-create.md) aby uzyskać kroki, aby je utworzyć.
* **Baza danych SQL platformy Azure**. W tym samouczku używasz bazy danych SQL platformy Azure jako **docelowego** magazynu danych. Jeśli nie masz bazy danych SQL platformy Azure, której można użyć w samouczku, zobacz [Jak utworzyć i skonfigurować bazę danych SQL platformy Azure,](../../sql-database/sql-database-get-started.md) aby ją utworzyć.
* **SQL Server 2012/2014 lub Visual Studio 2013**. Za pomocą programu SQL Server Management Studio lub Visual Studio można utworzyć przykładową bazę danych i wyświetlić dane wynikowe w bazie danych.  

## <a name="collect-blob-storage-account-name-and-key"></a>Zbieranie nazwy konta magazynu obiektów blob i klucza
Aby wykonać ten samouczek, potrzebujesz nazwy konta i klucza konta konta usługi Azure storage. Zanotuj **nazwę konta** i **klucz konta** dla konta usługi Azure storage.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. Kliknij **polecenie Wszystkie usługi** w menu po lewej stronie i wybierz pozycję Konta **magazynu**.

    ![Przeglądaj — konta magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. W **bloku Konta magazynu** wybierz **konto magazynu platformy Azure,** którego chcesz użyć w tym samouczku.
4. Wybierz **łącze Klawisze dostępu** w obszarze **USTAWIENIA**.
5. Kliknij przycisk **kopiuj** (obraz) obok pola **tekstowego Nazwa konta magazynu** i zapisz/wklej go gdzieś (na przykład: w pliku tekstowym).
6. Powtórz poprzedni krok, aby skopiować lub zanotować w dół **key1**.

    ![Klucz dostępu do magazynu](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zamknij wszystkie ostrza, **klikając**X .

## <a name="collect-sql-server-database-user-names"></a>Zbieranie serwerów SQL, baz danych, nazw użytkowników
Do wykonania tego samouczka potrzebne są nazwy serwera SQL, bazy danych i użytkownika platformy Azure. Zanotuj nazwy **serwerów,** **bazy danych**i **użytkowników** bazy danych SQL platformy Azure.

1. W **witrynie Azure portal**kliknij pozycję **Wszystkie usługi** po lewej stronie i wybierz pozycję Bazy **danych SQL**.
2. W **bloku baz danych SQL**wybierz **bazę danych,** której chcesz użyć w tym samouczku. Zanotuj **nazwę bazy danych**.  
3. W bloku **bazy danych SQL** kliknij pozycję **Właściwości** w obszarze **USTAWIENIA**.
4. Zanotuj wartości **dla NAZWY SERWERA** i LOGOWANIA **ADMINISTRATORA SERWERA**.
5. Zamknij wszystkie ostrza, **klikając**X .

## <a name="allow-azure-services-to-access-sql-server"></a>Zezwalaj usługom platformy Azure na dostęp do serwera SQL
Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** **włączone** dla serwera SQL platformy Azure, aby usługa Data Factory mogła uzyskać dostęp do serwera SQL platformy Azure. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

1. Kliknij **pozycję Centrum Wszystkie usługi** po lewej stronie i kliknij pozycję **Serwery SQL**.
2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
3. W bloku **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług Azure**.
4. Zamknij wszystkie ostrza, **klikając**X .

## <a name="prepare-blob-storage-and-sql-database"></a>Przygotowywanie magazynu obiektów blob i bazy danych SQL
Teraz przygotuj magazyn obiektów blob platformy Azure i bazę danych SQL platformy Azure dla samouczka, wykonując następujące kroki:  

1. Uruchom program Notatnik. Skopiuj następujący tekst i zapisz go jako **folder emp.txt** na dysku twardym. **C:\ADFGetStarted**

    ```
    John, Doe
    Jane, Doe
    ```
2. Użyj narzędzi takich jak [Eksplorator magazynu Azure](https://storageexplorer.com/) do utworzenia kontenera **adftutorial** i przekazania pliku **emp.txt** do kontenera.

3. Poniższy skrypt SQL umożliwia utworzenie tabeli **emp** w usłudze Azure SQL Database.  

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

    **Jeśli na komputerze jest zainstalowany program SQL Server 2012/2014:** postępuj zgodnie z instrukcjami [dotyczącymi zarządzania bazą danych SQL za pomocą](../../sql-database/sql-database-manage-azure-ssms.md) programu SQL Server Management Studio w celu nawiązania połączenia z serwerem SQL platformy Azure i uruchomienia skryptu SQL.

    Jeśli klient nie ma dostępu do serwera SQL Azure, musisz skonfigurować zaporę serwera SQL Azure tak, aby dostęp z Twojego komputera (adresu IP) był dozwolony. W [tym artykule](../../sql-database/sql-database-configure-firewall-settings.md) opisano kroki konfigurowania zapory dla serwera SQL Azure.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Wymagania wstępne zostały ukończone. Można utworzyć fabrykę danych przy użyciu jednego z następujących sposobów. Kliknij jedną z opcji na liście rozwijanej u góry lub następujące łącza, aby wykonać samouczek.     

* [Kreator kopiowania](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Szablon usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [INTERFEJS API ODPOCZYNKU](data-factory-copy-activity-tutorial-using-rest-api.md)
* [Interfejs API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Potok danych przedstawiony w tym samouczku kopiuje dane ze źródłowego do docelowego magazynu danych. Nie przekształca on danych wejściowych w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym przekształcania danych za pomocą usługi Azure Data Factory, zobacz [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Samouczek: Tworzenie pierwszego potoku przekształcającego dane przy użyciu klastra Hadoop).
>
> Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory).
