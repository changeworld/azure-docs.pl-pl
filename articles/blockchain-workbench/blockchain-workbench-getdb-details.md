---
title: Uzyskiwanie szczegółowych informacji z bazy danych Azure Blockchain Workbench
description: Dowiedz się, jak uzyskać Azure Blockchain Workbench bazy danych i informacji o serwerze bazy danych.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: bf7cc85e823e6630dbd3278bc91fba85f404059f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Pobierz informacje o bazie danych Azure Blockchain Workbench

W tym artykule pokazano, jak uzyskać szczegółowe informacje o bazie danych Azure Blockchain Workbench.

## <a name="overview"></a>Przegląd

Informacje o aplikacji, przepływy pracy i wykonywania inteligentne kontraktu podano korzystanie z widoków bazy danych w bazie danych SQL Workbench Blockchain. Deweloperzy mogą używać tych informacji, korzystając z narzędzi, takich jak program Microsoft Excel, usługa Power BI Visual Studio i SQL Server Management Studio.

Aby deweloperzy mogą łączyć się z bazą danych, potrzebują:

* Dostęp klienta zewnętrznego dozwolone w zaporze bazy danych. W tym artykule o konfigurowaniu artykułu zapory bazy danych wyjaśniono, jak zezwolić na dostęp.
* Nazwa serwera bazy danych i nazwę bazy danych.

## <a name="connect-to-the-blockchain-workbench-database"></a>Połączenie z bazą danych Blockchain Workbench

Do połączenia z bazą danych:

1. Zaloguj się do portalu Azure za pomocą konta mającego **właściciela** uprawnienia do zasobów Azure Blockchain Workbench.
2. W okienku nawigacji po lewej stronie wybierz **grup zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia Blockchain Workbench.
4. Wybierz **typu** sortowanie listy zasobów, a następnie wybierz pozycję z **programu SQL server**. Posortowanej listy w następnym Przechwytywanie ekranu znajdują się dwie bazy danych SQL, "główny" i korzystającą "lhgn" jako **prefiks zasobów**.

   ![Posortowaną listę zasobów Blockchain Workbench](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Aby wyświetlić szczegółowe informacje o bazie danych Blockchain Workbench, wybierz łącze bazy danych o **prefiks zasobów** podane Blockchain Workbench do wdrażania.

   ![Szczegóły bazy danych](media/blockchain-workbench-getdb-details/workbench-db-details.png)

Nazwa serwera bazy danych i nazwę bazy danych pozwalają połączyć z bazą danych Blockchain Workbench przy użyciu programowania lub narzędzia raportowania.

## <a name="next-steps"></a>Kolejne kroki

* [Widoki bazy danych w Azure Blockchain Workbench](blockchain-workbench-database-views.md)