---
title: Nawiązywanie połączenia z programem SSMS
description: Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie z usługą i Azure SQL Data Warehouse zapytań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d5c903a24ea47cb152555330688dd0bc515c625b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692585"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Nawiązywanie połączenia z SQL Data Warehouse przy użyciu SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Program Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie z usługą i Azure SQL Data Warehouse zapytań. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejący SQL Data Warehouse. Aby go utworzyć, zobacz artykuł [Tworzenie bazy danych w usłudze SQL Data Warehouse][Create a SQL Data Warehouse].
* SQL Server Management Studio (SSMS). Bezpłatnie [Zainstaluj program SSMS][Install SSMS] , jeśli go jeszcze nie masz.
* W pełni kwalifikowana nazwa serwera SQL. Aby ją znaleźć, zobacz [Nawiązywanie połączenia z usługą SQL Data Warehouse][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Połącz się z SQL Data Warehouse
1. Otwórz program SSMS.
2. Otwórz Eksplorator obiektów. W tym celu wybierz pozycję **plik** > **Połącz Eksplorator obiektów**.
   
    ![Eksplorator obiektów SQL Server][1]
3. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem][2]
   
   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **Hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij przycisk **Połącz**.
4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2. Uruchom przykładowe zapytanie
Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie][4]
3. Skopiuj to zapytanie TSQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. Aby to zrobić, kliknij przycisk `Execute` lub użyj następującego skrótu: `F5`.
   
    ![Uruchamianie zapytania][5]
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania][6]

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy umiesz nawiązywać połączenia i wykonywać zapytania, spróbuj wykonać [wizualizację danych przy użyciu usługi Power BI][visualizing the data with PowerBI].

Aby skonfigurować środowisko do uwierzytelniania usługi Azure Active Directory, zobacz artykuł [Uwierzytelnianie w usłudze SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
