---
title: Pobierz szczegóły bazy danych usługi Azure łańcucha bloków Workbench w wersji zapoznawczej
description: Dowiedz się, jak uzyskać informacje dotyczące bazy danych i serwera bazy danych Azure łańcucha bloków Workbench w wersji zapoznawczej.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845075"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Uzyskaj informacje na temat bazy danych usługi Azure łańcucha bloków Workbench w wersji zapoznawczej

W tym artykule pokazano, jak uzyskać szczegółowe informacje o bazie danych usługi Azure łańcucha bloków Workbench w wersji zapoznawczej.

## <a name="overview"></a>Omówienie

Informacje o aplikacjach, przepływach pracy i wykonywaniu kontraktów inteligentnych są podane przy użyciu widoków bazy danych w bazie danych SQL usługi Blockchain Workbench. Deweloperzy mogą używać tych informacji w przypadku korzystania z takich narzędzi, jak Microsoft Excel, Power BI, Visual Studio i SQL Server Management Studio.

Aby deweloperzy mogli łączyć się z bazą danych, potrzebują następujących elementów:

* Dostęp klienta zewnętrznego dozwolony w zaporze bazy danych. W tym artykule na temat konfigurowania zapory bazy danych wyjaśniono, jak zezwolić na dostęp.
* Nazwa serwera bazy danych i nazwa bazy danych.

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby połączyć się z bazą danych:

1. Zaloguj się do Azure Portal przy użyciu konta z uprawnieniami **właściciela** dla zasobów usługi Azure łańcucha bloków Workbench.
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