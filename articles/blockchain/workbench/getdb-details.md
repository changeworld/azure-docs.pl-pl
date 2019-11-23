---
title: Uzyskiwanie szczegółów bazy danych usługi Azure Blockchain Workbench
description: Learn how to get Azure Blockchain Workbench Preview database and database server information.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324690"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Pobieranie informacji o bazie danych usługi Azure Blockchain Workbench

This article shows how to get detailed information about your Azure Blockchain Workbench Preview database.

## <a name="overview"></a>Przegląd

Informacje o aplikacjach, przepływach pracy i wykonywaniu kontraktów inteligentnych są podane przy użyciu widoków bazy danych w bazie danych SQL usługi Blockchain Workbench. Developers can use this information when using tools such as Microsoft Excel, Power BI, Visual Studio, and SQL Server Management Studio.

Aby deweloperzy mogli łączyć się z bazą danych, potrzebują następujących elementów:

* Dostęp klienta zewnętrznego dozwolony w zaporze bazy danych. W tym artykule na temat konfigurowania zapory bazy danych wyjaśniono, jak zezwolić na dostęp.
* Nazwa serwera bazy danych i nazwa bazy danych.

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby połączyć się z bazą danych:

1. Sign in to the Azure portal with an account that has **Owner** permissions for the Azure Blockchain Workbench resources.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia usługi Blockchain Workbench.
4. Wybierz **Typ**, aby posortować listę zasobów, a następnie wybierz **program SQL Server**. Posortowana lista na następnym zrzucie ekranu pokazuje dwie bazy danych SQL, „master” i drugą, która używa ciągu „lhgn” jako **prefiksu zasobów**.

   ![Posortowana lista zasobów usługi Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Aby wyświetlić szczegółowe informacje o bazie danych usługi Blockchain Workbench, wybierz link bazy danych z **prefiksem zasobów** podanym podczas wdrażania usługi Blockchain Workbench.

   ![Szczegóły bazy danych](./media/getdb-details/workbench-db-details.png)

Nazwa serwera bazy danych i nazwa bazy danych pozwalają nawiązać połączenie z bazą danych usługi Blockchain Workbench przy użyciu narzędzia do programowania lub raportowania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)