---
title: Uzyskiwanie szczegółów bazy danych usługi Azure Blockchain Workbench
description: Dowiedz się, jak uzyskać informacje o bazie danych usługi Azure Blockchain Workbench i o serwerze bazy danych.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0d806491c58c4b1881adc2fd830de7c7b9f0859d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331932"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Pobieranie informacji o bazie danych usługi Azure Blockchain Workbench

W tym artykule pokazano, jak uzyskać szczegółowe informacje o bazie danych usługi Azure Blockchain Workbench.

## <a name="overview"></a>Przegląd

Informacje o aplikacjach, przepływach pracy i wykonywaniu kontraktów inteligentnych są podane przy użyciu widoków bazy danych w bazie danych SQL usługi Blockchain Workbench. Deweloperzy mogą używać tych informacji w przypadku korzystania z narzędzi, takich jak program Microsoft Excel, Power BI, programu Visual Studio i SQL Server Management Studio.

Aby deweloperzy mogli łączyć się z bazą danych, potrzebują następujących elementów:

* Dostęp klienta zewnętrznego dozwolony w zaporze bazy danych. W tym artykule na temat konfigurowania zapory bazy danych wyjaśniono, jak zezwolić na dostęp.
* Nazwa serwera bazy danych i nazwa bazy danych.

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby połączyć się z bazą danych:

1. Zaloguj się do witryny Azure portal za pomocą konta mającego **właściciela** uprawnienia do zasobów aplikacji Azure Blockchain Workbench.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia usługi Blockchain Workbench.
4. Wybierz **Typ**, aby posortować listę zasobów, a następnie wybierz **program SQL Server**. Posortowana lista na następnym zrzucie ekranu pokazuje dwie bazy danych SQL, „master” i drugą, która używa ciągu „lhgn” jako **prefiksu zasobów**.

   ![Posortowana lista zasobów usługi Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Aby wyświetlić szczegółowe informacje o bazie danych usługi Blockchain Workbench, wybierz link bazy danych z **prefiksem zasobów** podanym podczas wdrażania usługi Blockchain Workbench.

   ![Szczegóły bazy danych](./media/getdb-details/workbench-db-details.png)

Nazwa serwera bazy danych i nazwa bazy danych pozwalają nawiązać połączenie z bazą danych usługi Blockchain Workbench przy użyciu narzędzia do programowania lub raportowania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)