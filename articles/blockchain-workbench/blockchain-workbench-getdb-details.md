---
title: Uzyskiwanie szczegółów bazy danych usługi Azure Blockchain Workbench
description: Dowiedz się, jak uzyskać informacje o bazie danych usługi Azure Blockchain Workbench i o serwerze bazy danych.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 63b718bcb8722c5fd501891d162eadfae9fb8ec2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Pobieranie informacji o bazie danych usługi Azure Blockchain Workbench

W tym artykule pokazano, jak uzyskać szczegółowe informacje o bazie danych usługi Azure Blockchain Workbench.

## <a name="overview"></a>Omówienie

Informacje o aplikacjach, przepływach pracy i wykonywaniu kontraktów inteligentnych są podane przy użyciu widoków bazy danych w bazie danych SQL usługi Blockchain Workbench. Deweloperzy mogą korzystać z tych informacji podczas używania narzędzi takich jak program Microsoft Excel, usługa Power BI, program Visual Studio i program SQL Server Management Studio.

Aby deweloperzy mogli łączyć się z bazą danych, potrzebują następujących elementów:

* Dostęp klienta zewnętrznego dozwolony w zaporze bazy danych. W tym artykule na temat konfigurowania zapory bazy danych wyjaśniono, jak zezwolić na dostęp.
* Nazwa serwera bazy danych i nazwa bazy danych.

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby połączyć się z bazą danych:

1. Zaloguj się do witryny Azure Portal za pomocą konta z uprawnieniami **Właściciel** do zasobów usługi Azure Blockchain Workbench.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia usługi Blockchain Workbench.
4. Wybierz **Typ**, aby posortować listę zasobów, a następnie wybierz **program SQL Server**. Posortowana lista na następnym zrzucie ekranu pokazuje dwie bazy danych SQL, „master” i drugą, która używa ciągu „lhgn” jako **prefiksu zasobów**.

   ![Posortowana lista zasobów usługi Blockchain Workbench](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Aby wyświetlić szczegółowe informacje o bazie danych usługi Blockchain Workbench, wybierz link bazy danych z **prefiksem zasobów** podanym podczas wdrażania usługi Blockchain Workbench.

   ![Szczegóły bazy danych](media/blockchain-workbench-getdb-details/workbench-db-details.png)

Nazwa serwera bazy danych i nazwa bazy danych pozwalają nawiązać połączenie z bazą danych usługi Blockchain Workbench przy użyciu narzędzia do programowania lub raportowania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](blockchain-workbench-database-views.md)