---
title: Nawiązywanie połączenia z usługą Azure SQL Data Warehouse — VSTS | Microsoft Docs
description: Tworzenie zapytań względem usługi SQL Data Warehouse przy użyciu programu Visual Studio.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e6c3de1714d363eb1481bcea5f470d04125b96b9
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575316"
---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>Nawiązywanie połączenia z usługą SQL Data Warehouse przy użyciu programu Visual Studio i narzędzi SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Program Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Używając programu Visual Studio, można w kilka minut utworzyć zapytanie do usługi Azure SQL Data Warehouse. Ta metoda używa rozszerzenia SQL Server Data Tools (SSDT) w programie Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejący SQL Data Warehouse. Aby go utworzyć, zobacz artykuł [Tworzenie bazy danych w usłudze SQL Data Warehouse][Create a SQL Data Warehouse].
* Rozszerzenie SSDT dla programu Visual Studio. Jeśli masz program Visual Studio, prawdopodobnie masz też to rozszerzenie. Aby uzyskać instrukcje instalacji i informacje na temat dostępnych opcji, zobacz artykuł [Instalowanie programu Visual Studio i narzędzi SSDT][Installing Visual Studio and SSDT].
* W pełni kwalifikowana nazwa serwera SQL. Aby ją znaleźć, zobacz [Nawiązywanie połączenia z usługą SQL Data Warehouse][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Nawiązywanie połączenia z usługą SQL Data Warehouse
1. Open Visual Studio 2019.
2. Otwórz Eksplorator obiektów SQL Server. W tym celu wybierz pozycje **Widok**  >  **Eksplorator obiektów SQL Server**.
   
    ![Eksplorator obiektów SQL Server][1]
3. Kliknij **ikonę dodawania serwera SQL**.
   
    ![Dodawanie serwera SQL][2]
4. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem][3]
   
   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **Hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij przycisk **Połącz**.
5. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. Uruchamianie przykładowego zapytania
Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie][5]
3. Skopiuj to zapytanie TSQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. W tym celu kliknij zieloną strzałkę lub użyj następującego skrótu: `CTRL`+`SHIFT`+`E`.
   
    ![Uruchamianie zapytania][6]
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania][7]

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy umiesz nawiązywać połączenia i wykonywać zapytania, spróbuj wykonać [wizualizację danych przy użyciu usługi Power BI][visualizing the data with PowerBI].

Aby skonfigurować środowisko do uwierzytelniania usługi Azure Active Directory, zobacz artykuł [Uwierzytelnianie w usłudze SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
