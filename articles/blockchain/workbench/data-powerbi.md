---
title: Korzystanie z danych usługi Azure Blockchain Workbench w usłudze Microsoft Power BI
description: Dowiedz się, jak załadować i wyświetlać dane bazy danych SQL DB usługi Azure Blockchain Workbench w usłudze Microsoft Power BI.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6e1f160c3563a280548c74ebe84f30bf08945c3f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324792"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Korzystanie z danych usługi Azure Blockchain Workbench w usłudze Microsoft Power BI

Usługa Microsoft Power BI umożliwia łatwe generowanie zaawansowanych raportów z baz danych SQL DB przy użyciu usługi Power BI Desktop, a następnie publikowanie ich w witrynie [https://www.powerbi.com](https://www.powerbi.com).

Ten artykuł zawiera przewodnik krok po kroku, w którym przedstawiono instrukcje dotyczące połączenia z bazą danych SQL Database usługi Azure Blockchain Workbench z poziomu programu Power BI Desktop, tworzenia raportu i wdrażania go w witrynie powerbi.com.

## <a name="prerequisites"></a>Wymagania wstępne

* Download [Power BI Desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Connecting Power BI to data in Azure Blockchain Workbench

1.  Otwórz program Power BI Desktop.
2.  Wybierz pozycję **Pobieranie danych**.

    ![Pobieranie danych](./media/data-powerbi/get-data.png)
3.  Wybierz pozycję **SQL Server** z listy typów źródeł danych.

4.  Podaj nazwę serwera i bazy danych w oknie dialogowym. Określ, czy chcesz importować dane lub wykonać zapytanie w trybie **DirectQuery**. Kliknij przycisk **OK**.

    ![Wybieranie pozycji SQL Server](./media/data-powerbi/select-sql.png)

5.  Podaj poświadczenia bazy danych, aby uzyskać dostęp do usługi Azure Blockchain Workbench. Wybierz pozycję **Baza danych** i wprowadź swoje poświadczenia.

    Jeśli używasz poświadczeń utworzonych przez proces wdrażania usługi Azure Blockchain Workbench, nazwa użytkownika to **dbadmin**, a hasło to hasło podane podczas wdrażania.

    ![Ustawienia usługi SQL DB](./media/data-powerbi/db-settings.png)

6.  Po połączeniu z bazą danych w oknie dialogowym **Nawigator** są wyświetlane tabele i widoki dostępne w bazie danych. Widoki są przeznaczone do celów raportowania i wszystkie mają prefiks **vw**.

    ![Nawigator](./media/data-powerbi/navigator.png)

7.  Wybierz widoki, które mają zostać uwzględnione. For demonstration purposes, we include **vwContractAction**, which provides details on the actions that have taken place on a contract.

    ![Wybieranie widoków](./media/data-powerbi/select-views.png)

Teraz możesz tworzyć i publikować raporty, tak jak zwykle przy użyciu usługi Power BI.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)