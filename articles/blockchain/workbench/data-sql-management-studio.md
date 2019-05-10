---
title: Korzystanie z danych usługi Azure Blockchain Workbench w programie SQL Server Management Studio
description: Dowiedz się, jak nawiązać połączenie z bazą danych SQL usługi Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: db43dd04da601650b3592dc74cdd804fae394c00
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510909"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Korzystanie z danych usługi Azure Blockchain Workbench w programie SQL Server Management Studio

Microsoft SQL Server Management Studio umożliwia szybkie pisanie i testowanie zapytań względem bazy danych SQL Azure Blockchain Workbench. Ta sekcja zawiera przewodnik krok po kroku dotyczący sposobu nawiązywania połączenia z bazą danych SQL DB usługi Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Łączenie programu SQL Server Management Studio z danymi w usłudze Azure Blockchain Workbench

1. Otwórz program SQL Server Management Studio i wybierz pozycję **Połącz**.
2. Wybierz pozycję **Aparat bazy danych**.

    ![Aparat bazy danych](./media/data-sql-management-studio/database-engine.png)

3. W oknie dialogowym **Łączenie z serwerem** wprowadź nazwę serwera i poświadczenia bazy danych.

    Jeśli używasz poświadczeń utworzonych przez proces wdrażania usługi Azure Blockchain Workbench, nazwa użytkownika to **dbadmin**, a hasło to hasło podane podczas wdrażania.

    ![Wprowadzanie poświadczeń usługi SQL](./media/data-sql-management-studio/sql-creds.png)

   1. Program SQL Server Management Studio wyświetla listę baz danych, widoków bazy danych i procedur składowanych w bazie danych usługi Azure Blockchain Workbench.

      ![Lista baz danych](./media/data-sql-management-studio/db-list.png)

5. Aby wyświetlić dane skojarzone z dowolnymi widokami bazy danych, możesz automatycznie wygenerować instrukcję select, wykonując następujące kroki.
6. Kliknij prawym przyciskiem myszy jeden z widoków bazy danych w Eksploratorze obiektów.
7. Wybierz pozycję **Skrypt widoku jako**.
8. Wybierz pozycję **SELECT to**.
9. Wybierz pozycję **Nowe okno edytora zapytań**.
10. Nowe zapytanie można utworzyć, wybierając pozycję **Nowe zapytanie**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)
