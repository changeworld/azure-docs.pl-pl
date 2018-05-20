---
title: Korzystanie z danych usługi Azure Blockchain Workbench w programie SQL Server Management Studio
description: Dowiedz się, jak nawiązać połączenie z bazą danych SQL usługi Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b640aec322d6dc184a428e67d9576cf162c8f394
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Korzystanie z danych usługi Azure Blockchain Workbench w programie SQL Server Management Studio

Program Microsoft SQL Server Management Studio zapewnia możliwość szybkiego pisania i testowania zapytań dotyczących bazy danych SQL DB usługi Azure Blockhain Workbench. Ta sekcja zawiera przewodnik krok po kroku dotyczący sposobu nawiązywania połączenia z bazą danych SQL DB usługi Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Łączenie programu SQL Server Management Studio z danymi w usłudze Azure Blockchain Workbench

1. Otwórz program SQL Server Management Studio i wybierz pozycję **Połącz**.
2. Wybierz pozycję **Aparat bazy danych**.

    ![Aparat bazy danych](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. W oknie dialogowym **Łączenie z serwerem** wprowadź nazwę serwera i poświadczenia bazy danych.

    Jeśli używasz poświadczeń utworzonych przez proces wdrażania usługi Azure Blockchain Workbench, nazwa użytkownika to **dbadmin**, a hasło to hasło podane podczas wdrażania.

    ![Wprowadzanie poświadczeń usługi SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. Program SQL Server Management Studio wyświetla listę baz danych, widoków bazy danych i procedur składowanych w bazie danych usługi Azure Blockchain Workbench.

    ![Lista baz danych](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Aby wyświetlić dane skojarzone z dowolnymi widokami bazy danych, możesz automatycznie wygenerować instrukcję select, wykonując następujące kroki.
6. Kliknij prawym przyciskiem myszy jeden z widoków bazy danych w Eksploratorze obiektów.
7. Wybierz pozycję **Skrypt widoku jako**.
8. Wybierz pozycję **SELECT to**.
9. Wybierz pozycję **Nowe okno edytora zapytań**.
10. Nowe zapytanie można utworzyć, wybierając pozycję **Nowe zapytanie**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](blockchain-workbench-database-views.md)