---
title: Nawiązywanie połączenia z programem SSMS
description: Program SQL Server Management Studio (SSMS) umożliwia łączenie się z usługą Azure Synapse Analytics i wykonywanie zapytań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 12f8240d254b2d393734604928a809a2d9f1e14e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351645"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Połącz się z usługą Azure Synapse Analytics za pomocą programu SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Uczenie maszynowe platformy Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [Ssms](sql-data-warehouse-query-ssms.md)
> 
> 

Program SQL Server Management Studio (SSMS) umożliwia łączenie się z magazynem danych w ramach usługi Azure Synapse i wykonywanie zapytań. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejąca pula SQL. Aby go utworzyć, zobacz [Tworzenie puli SQL](create-data-warehouse-portal.md).
* Sql Server Management Studio (SSMS) zainstalowany. [Zainstaluj SSMS](https://msdn.microsoft.com/library/hh213248.aspx) za darmo, jeśli jeszcze go nie masz.
* W pełni kwalifikowana nazwa serwera SQL. Aby znaleźć te informacje, zobacz [Łączenie z pulą SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Połącz się z pulą SQL
1. Otwórz program SSMS.
2. Otwórz Eksploratora obiektów, wybierając**Eksploratora obiektów łączenia** **plików** > .
   
    ![Eksplorator obiektów SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **Hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij pozycję **Połącz**.
4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Uruchamianie przykładowej kwerendy
Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Skopiuj do okna kwerendy następującą kwerendę T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom kwerendę, `Execute` klikając lub użyj `F5`następującego skrótu: .
   
    ![Uruchamianie zapytania](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy możesz się połączyć i zapytać, spróbuj [zwizualizować dane za pomocą usługi Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md ).
Aby skonfigurować środowisko uwierzytelniania usługi Azure Active Directory, zobacz [Uwierzytelnianie w puli SQL](sql-data-warehouse-authentication.md).
