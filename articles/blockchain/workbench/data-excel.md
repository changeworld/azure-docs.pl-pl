---
title: Korzystanie z danych usługi Azure Blockchain Workbench w programie Microsoft Excel
description: Dowiedz się, jak załadować i wyświetlać dane bazy danych SQL DB usługi Azure Blockchain Workbench w programie Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 560958c2b420d3a1ec592e6bd7e0124ecad7c984
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332357"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Wyświetlanie danych usługi Azure Blockchain Workbench za pomocą programu Microsoft Excel

Za pomocą programu Microsoft Excel można wyświetlać dane z bazy danych SQL DB usługi Azure Blockchain Workbench. W tym artykule przedstawiono kroki, które należy wykonać w celu:

* Nawiązania połączenia z bazą danych usługi Blockchain Workbench z poziomu programu Microsoft Excel
* Wyświetlenia tabel i widoków bazy danych usługi Blockchain Workbench
* Załadowania danych widoku usługi Blockchain Workbench do programu Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby nawiązać połączenie z bazą danych usługi Blockchain Workbench:

1. Otwórz program Microsoft Excel.
2. Na karcie **Dane** wybierz pozycję **Pobierz dane**.
3. Wybierz pozycję **Z platformy Azure**, a następnie wybierz pozycję **Z usługi Azure SQL Database**.

   ![Łączenie z bazą danych Azure SQL Database](./media/data-excel/connect-sql-db.png)

4. W oknie dialogowym **Baza danych programu SQL Server**:

    * W polu **Serwer** wprowadź nazwę serwera usługi Blockchain Workbench.
    * W polu **Baza danych (opcjonalnie)** wprowadź nazwę bazy danych.

   ![Podawanie nazwy serwera bazy danych i bazy danych](./media/data-excel/provide-server-db.png)

5. Na pasku nawigacyjnym okna dialogowego **Baza danych programu SQL Server** wybierz pozycję **Baza danych**. Wprowadź **nazwę użytkownika** i **hasło**, a następnie wybierz pozycję **Połącz**.

    > [!NOTE]
    > Jeśli używasz poświadczeń utworzonych podczas procesu wdrażania usługi Azure Blockchain Workbench, **Nazwa użytkownika** to `dbadmin`. **Hasło** to hasło utworzone podczas wdrażania usługi Blockchain Workbench.
    
   ![Podawanie poświadczeń w celu uzyskania dostępu do bazy danych](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Przeglądanie tabel i widoków baz danych

Okno dialogowe Nawigator programu Excel zostanie otwarte po nawiązaniu połączenia z bazą danych. Nawigator umożliwia przyjrzenie się tabelom i widokom w bazie danych. Widoki są przeznaczone do celów raportowania i ich nazwy są poprzedzone prefiksem **vw**.

   ![Podgląd widoku w nawigatorze programu Excel](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Ładowanie danych widoku do skoroszytu programu Excel

W następnym przykładzie pokazano, jak załadować dane z widoku do skoroszytu programu Excel.

1. Na pasku przewijania **Nawigatora** wybierz widok **vwContractAction**. Podgląd widoku **vwContractAction** pokazuje wszystkie akcje związane z kontraktem w bazie danych usługi Blockchain Workbench.
2. Wybierz pozycję **Załaduj**, aby pobrać wszystkie dane w widoku i umieścić je w skoroszycie programu Excel.

   ![Dane załadowane z widoku](./media/data-excel/view-data.png)

Teraz, po załadowaniu danych, możesz użyć funkcji programu Excel w celu utworzenia własnych raportów przy użyciu metadanych i danych transakcji z bazy danych usługi Azure Blockchain Workbench.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)