---
title: Użyj danych Azure Blockchain Workbench w programie Microsoft Excel
description: Dowiedz się, jak załadować i wyświetlania danych bazy danych SQL Workbench Blockchain Azure w programie Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 70297bd0af6322d0f3ac2c719d1827e4bc5898cd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Wyświetlanie danych Azure Blockchain Workbench za pomocą programu Microsoft Excel

Program Microsoft Excel służy do wyświetlania danych w bazie danych SQL Azure Blockchain Workbench. W tym artykule przedstawiono kroki, które należy:

* Połączenie z bazą danych Blockchain Workbench z programu Microsoft Excel
* Przyjrzyj się Blockchain Workbench bazy danych tabele i widoki
* Ładowanie danych widoku Blockchain Workbench do programu Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Połączenie z bazą danych Blockchain Workbench

Do połączenia z bazą danych Blockchain Workbench:

1. Otwórz program Microsoft Excel.
2. Na **danych** , wybierz pozycję **Pobierz dane**.
3. Wybierz **Azure z** , a następnie wybierz **z bazy danych SQL Azure**.

   ![Łączenie z usługą Azure SQL Database](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. W **bazy danych programu SQL Server** okno dialogowe:

    * Aby uzyskać **serwera**, wprowadź nazwę serwera Blockchain Workbench.
    * Aby uzyskać **bazy danych (opcjonalnie)**, wprowadź nazwę bazy danych.

   ![Podaj serwer bazy danych i bazy danych](media/blockchain-workbench-data-excel/provide-server-db.png)

5. W **bazy danych programu SQL Server** okna dialogowego paska nawigacyjnego, wybierz opcję **bazy danych**. Wprowadź z **Username** i **hasła**, a następnie wybierz **Connect**.

    > [!NOTE]
    > Jeśli używasz poświadczeń utworzonych podczas procesu wdrażania Azure Blockchain Workbench **nazwy użytkownika** jest `dbadmin`. **Hasło** jest tworzony jeden po wdrożeniu Blockchain Workbench.
    
   ![Podaj poświadczenia dostępu do bazy danych](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Przyjrzyj się tabele i widoki

Okno dialogowe Navigator programu Excel zostanie otwarty po nawiązaniu połączenia z bazą danych. Nawigator umożliwia przyjrzeć się tabele i widoki w bazie danych. Widoki są przeznaczone dla raportowania i ich nazwy są poprzedzane prefiksem **vw**.

   ![Nawigator programu Excel w wersji zapoznawczej widoku](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Załaduj dane widoku w skoroszycie programu Excel

W kolejnym przykładzie pokazano, jak ładowanie danych z widoku do skoroszytu programu Excel.

1. W **Nawigator** paska przewijania, wybierz opcję **vwContractAction** widoku. **VwContractAction** Podgląd pokazuje wszystkie działania z kontraktem w bazie danych Blockchain Workbench.
2. Wybierz **obciążenia** można pobrać wszystkich danych w widoku i umieszcza je w skoroszycie programu Excel.

   ![Dane załadowane z widoku](media/blockchain-workbench-data-excel/view-data.png)

Teraz, gdy masz załadować dane, można użyć funkcji programu Excel do tworzenia własnych raportów za pomocą danych transakcji i metadanych z bazy danych Azure Blockchain Workbench.

## <a name="next-steps"></a>Kolejne kroki

* [Widoki bazy danych w Azure Blockchain Workbench](blockchain-workbench-database-views.md)