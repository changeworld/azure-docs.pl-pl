---
title: Użyj danych Azure Blockchain Workbench w usłudze Microsoft Power BI
description: Dowiedz się, jak załadować i wyświetlania danych bazy danych SQL Workbench Blockchain Azure w usłudze Microsoft Power BI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 2b909c0a8441010b87c913e5937d25c8127058f1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Przy użyciu danych Azure Blockchain Workbench w usłudze Microsoft Power BI

Microsoft Power BI umożliwia łatwe generowanie zaawansowanych raportów z bazy danych SQL baz danych przy użyciu Power BI Desktop, a następnie opublikuj je, aby [ https://www.powerbi.com ](http://www.powerbi.com).

Ten artykuł zawiera przewodnik krok po kroku do połączenia z bazy danych SQL Azure Blockchain Workbench z poziomu usługi Power BI desktop, tworzenie raportu i wdrożyć raport w witrynie powerbi.com.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz [PowerBI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-powerbi-to-data-in-azure-blockchain-workbench"></a>Łączenie z usługą Power BI z danymi w Azure Blockchain Workbench

1.  Otwórz program Power BI Desktop.
2.  Wybierz **Pobierz dane**.

    ![Pobieranie danych](media/blockchain-workbench-data-powerbi/get-data.png)
3.  Wybierz **programu SQL Server** z listy typy źródeł danych.

4.  Podaj nazwę serwera i bazy danych w oknie dialogowym. Określ, czy do importowania danych lub wykonywania **DirectQuery**. Kliknij przycisk **OK**.

    ![Wybieranie pozycji SQL Server](media/blockchain-workbench-data-powerbi/select-sql.png)

5.  Podaj poświadczenia bazy danych do Azure Blockchain Workbench. Wybierz **bazy danych** i wprowadź swoje poświadczenia.

    Jeśli używane są poświadczenia utworzone przez proces wdrażania Azure Blockchain Workbench, nazwa użytkownika jest **dbadmin** i hasło jest podana podczas wdrażania.

    ![Ustawienia bazy danych SQL.](media/blockchain-workbench-data-powerbi/db-settings.png)

6.  Po połączeniu z bazą danych, **Nawigator** Wyświetla okno dialogowe, tabele i widoki, które są dostępne w bazie danych. Widoki są przeznaczone do celów raportowania oraz czy jest poprzedzony **vw**.

    ![Nawigator](media/blockchain-workbench-data-powerbi/navigator.png)

7.  Wybierz widoki, które mają zostać uwzględnione. Dla celów demonstracyjnych, przeprowadzamy **vwContractAction**, który zawiera szczegółowe informacje na wszystkich działań, które miały miejsce na kontrakt.

    ![Wybierz widoki](media/blockchain-workbench-data-powerbi/select-views.png)

Teraz można tworzyć i publikować raporty, jak zwykle przy użyciu usługi Power BI.

## <a name="next-steps"></a>Kolejne kroki

* [Widoki bazy danych w Azure Blockchain Workbench](blockchain-workbench-database-views.md)