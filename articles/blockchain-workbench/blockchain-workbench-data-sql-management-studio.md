---
title: Azure Blockchain Workbench danych za pomocą programu SQL Server Management Studio
description: Dowiedz się, jak nawiązać połączenie bazy danych SQL Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1c9ee1e94ba1195db027c3edfab2e2a96f181ca3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Przy użyciu danych Azure Blockchain Workbench za pomocą programu SQL Server Management Studio

Microsoft SQL Server Management Studio zapewnia możliwość szybkiego zapisu i testowanie zapytań dotyczących bazy danych SQL Azure Blockhain Workbench. Ta sekcja zawiera przewodnik krok po kroku do nawiązania połączenia bazy danych SQL Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz [programu SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio o łączeniu z danymi w Azure Blockchain Workbench

1. Otwórz program SQL Server Management Studio i wybierz **Connect**.
2. Wybierz **bazy danych aparatu**.

    ![Aparat bazy danych](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. W **Połącz z serwerem** okna dialogowego, wprowadź nazwę serwera i poświadczenia bazy danych.

    Jeśli używane są poświadczenia utworzone przez proces wdrażania Azure Blockchain Workbench, jego nazwa zostanie **dbadmin** i hasło będzie podana podczas wdrażania.

    ![Wprowadź poświadczenia SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio Wyświetla listę baz danych, bazy danych, widoków i procedur przechowywanych w bazie danych Azure Blockchain Workbench.

    ![Listy baz danych](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Aby wyświetlić dane skojarzone z żadnym widoku bazy danych, można automatycznie wygenerować instrukcji select, wykonując następujące kroki.
6. Kliknij prawym przyciskiem myszy jeden z widoków bazy danych w Eksploratorze obiektów.
7. Wybierz **skryptu widoku w postaci**.
8. Wybierz **umożliwia**.
9. Wybierz **nowe okno edytora zapytań**.
10. Można tworzyć nowe zapytanie, wybierając **nowe zapytanie**.

## <a name="next-steps"></a>Kolejne kroki

* [Widoki bazy danych w Azure Blockchain Workbench](blockchain-workbench-database-views.md)