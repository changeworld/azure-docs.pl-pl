---
title: 'Samouczek: Dodawanie Azure SQL Database elastycznej puli do grupy trybu failover | Microsoft Docs'
description: Dodaj Azure SQL Database elastyczną pulę do grupy trybu failover przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 5dd241fed757669cf8bccd96a1de948e8d73a021
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033268"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Samouczek: Dodawanie Azure SQL Database elastycznej puli do grupy trybu failover

Skonfiguruj grupę trybu failover dla Azure SQL Database elastycznej puli i przetestuj tryb failover przy użyciu Azure Portal.  Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz Azure SQL Databaseą pojedynczą bazę danych.
> - Dodaj pojedynczą bazę danych do puli elastycznej. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla puli elastycznej między dwoma logicznymi serwerami SQL.
> - Testowanie pracy w trybie failover.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.


## <a name="1---create-a-single-database"></a>1 — Tworzenie pojedynczej bazy danych 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 — Dodawanie pojedynczej bazy danych do puli elastycznej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu [Azure Portal](https://portal.azure.com).
1. Wpisz `elastic pool` w polu wyszukiwania, naciśnij klawisz ENTER, wybierz ikonę **elastycznej puli baz danych SQL** , a następnie wybierz pozycję **Utwórz**. 

    ![Wybierz pulę elastyczną z portalu Marketplace](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. Skonfiguruj pulę elastyczną przy użyciu następujących wartości:
   - **Nazwa**: Podaj unikatową nazwę puli elastycznej, na przykład `myElasticPool`. 
   - **Subskrypcja**: Wybierz subskrypcję z listy rozwijanej.
   - **Grupa zasobów**: Z `myResourceGroup` listy rozwijanej wybierz grupę zasobów utworzoną w sekcji 1. 
   - **Serwer**: Wybierz serwer utworzony w sekcji 1 z listy rozwijanej.  

       ![Utwórz nowy serwer dla puli elastycznej](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Obliczenia + magazyn**: Wybierz pozycję **Konfiguruj pulę elastyczną** , aby skonfigurować obliczenia, magazyn i dodać pojedynczą bazę danych do puli elastycznej. Na karcie **Ustawienia puli** pozostaw wartość domyślną 5 rdzeń z 2 rdzeni wirtualnych i 32 GB. 

1. Na stronie **Konfigurowanie** wybierz kartę **bazy danych** , a następnie wybierz pozycję **Dodaj bazę danych**. Wybierz bazę danych utworzoną w sekcji 1, a następnie wybierz pozycję **Zastosuj** , aby dodać ją do puli elastycznej. Wybierz pozycję **Zastosuj** ponownie, aby zastosować ustawienia puli elastycznej i zamknąć stronę **Konfiguracja** . 

    ![Dodawanie bazy danych SQL do puli elastycznej](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć ustawienia puli elastycznej, a następnie wybierz pozycję **Utwórz** , aby utworzyć pulę elastyczną. 


## <a name="3---create-the-failover-group"></a>3 — Tworzenie grupy trybu failover 
W tym kroku utworzysz [grupę trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerem Azure SQL i nowym serwerem Azure SQL w innym regionie. Następnie Dodaj pulę elastyczną do grupy trybu failover. 


1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu [Azure Portal](https://portal.azure.com). 
1. Wpisz `sql servers` w polu wyszukiwania. 
1. Obowiązkowe Wybierz ikonę gwiazdki obok pozycji serwery SQL, aby uzyskać dostęp do ulubionych **serwerów SQL** , a następnie dodaj ją do okienka nawigacji po lewej stronie. 
    
    ![Lokalizowanie serwerów SQL](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Wybierz pozycję **serwery SQL** i wybierz serwer utworzony w sekcji 1.
1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz pozycję **Dodaj grupę** , aby utworzyć nową grupę trybu failover. 

    ![Dodaj nową grupę trybu failover](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz**:
    - **Nazwa grupy trybu failover**: Wpisz unikatową nazwę grupy trybu failover, `failovergrouptutorial`na przykład. 
    - **Serwer pomocniczy**: Wybierz opcję *konfigurowania wymaganych ustawień* , a następnie wybierz opcję **utworzenia nowego serwera**. Alternatywnie można wybrać istniejący serwer jako serwer pomocniczy. Po wprowadzeniu następujących wartości dla nowego serwera pomocniczego wybierz pozycję **Wybierz**. 
        - **Nazwa serwera**: Wpisz unikatową nazwę serwera pomocniczego, `mysqlsecondary`na przykład. 
        - **Identyfikator logowania administratora serwera**: Wprowadź`azureuser`
        - **Hasło**: Wpisz złożone hasło spełniające wymagania dotyczące haseł.
        - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład Wschodnie stany USA 2. Ta lokalizacja nie może być taka sama jak lokalizacja serwera podstawowego.

       > [!NOTE]
       > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 
    
       ![Tworzenie serwera pomocniczego dla grupy trybu failover](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. Wybierz **bazy danych w grupie,** a następnie wybierz pulę elastyczną utworzoną w sekcji 2. Powinien pojawić się ostrzeżenie z monitem o utworzenie elastycznej puli na serwerze pomocniczym. Wybierz ostrzeżenie, a następnie wybierz przycisk **OK** , aby utworzyć pulę elastyczną na serwerze pomocniczym. 
        
    ![Dodawanie puli elastycznej do grupy trybu failover](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Wybierz pozycję **Wybierz** , aby zastosować ustawienia puli elastycznej do grupy trybu failover, a następnie wybierz pozycję **Utwórz** , aby utworzyć grupę trybu failover. Dodanie puli elastycznej do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej. 


## <a name="4---test-failover"></a>4 — Testowanie pracy w trybie failover 
W tym kroku nastąpi niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal. 

1. Przejdź do serwera **SQL** Server w ramach [Azure Portal](https://portal.azure.com). 
1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz grupę trybu failover utworzoną w sekcji 2. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy i który serwer jest serwerem pomocniczym. 
1. Wybierz pozycję **tryb failover** z okienka zadań, aby przełączyć grupę trybu failover w tryb pracy awaryjnej zawierającej pulę elastyczną. 
1. Na ostrzeżeniu wybierz pozycję **tak** , aby powiadomić, że sesje TDS zostaną rozłączone. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest serwerem podstawowym, który serwer jest serwerem pomocniczym. Jeśli praca awaryjna zakończyła się pomyślnie, te dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **tryb failover** , aby zakończyć pracę grupy trybu failover z powrotem do oryginalnych ustawień. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Wyczyść zasoby, usuwając grupę zasobów. 

1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Usuń grupę zasobów** , aby usunąć wszystkie zasoby w grupie, a także samą grupę zasobów. 
1. Wpisz nazwę grupy zasobów, `myResourceGroup`w polu tekstowym, a następnie wybierz pozycję **Usuń** , aby usunąć grupę zasobów.  


## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano Azure SQL Database pojedynczą bazę danych do grupy trybu failover i przetestowano tryb failover. W tym samouczku omówiono:

> [!div class="checklist"]
> - Utwórz Azure SQL Databaseą pojedynczą bazę danych.
> - Dodaj pojedynczą bazę danych do puli elastycznej. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla puli elastycznej między dwoma logicznymi serwerami SQL.
> - Testowanie pracy w trybie failover.

Przejdź do następnego samouczka dotyczącego migracji za pomocą usługi DMS.

> [!div class="nextstepaction"]
> [Samouczek: Migrowanie SQL Server do bazy danych w puli za pomocą usługi DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
