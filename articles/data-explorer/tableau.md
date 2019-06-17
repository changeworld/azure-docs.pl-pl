---
title: Użyj połączenia Open Database Connectivity (ODBC) do Eksploratora danych platformy Azure, aby Wizualizacja danych przy użyciu Tableau
description: W tym artykule dowiesz się, jak Wizualizacja danych przy użyciu Tableau za pomocą połączenia Open Database Connectivity (ODBC) do połączenia Eksploratora danych usługi Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499087"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Wizualizuj dane z Eksploratora danych platformy Azure w Tableau

 [TABLEAU](https://www.tableau.com/) to platforma analizę graficzną do analizy biznesowej. Aby połączyć do Eksploratora danych usługi Azure z Tableau i wyświetlić dane z klastra z próbki, należy użyć sterownik programu SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące polecenie, aby zakończyć w tym artykule:

* [Łączenie do Eksploratora danych platformy Azure z ODBC](connect-odbc.md) nawiązać połączenia z Eksploratora danych platformy Azure z Tableau za pomocą sterownika SQL Server ODBC. 

* Desktop TABLEAU pełnego, lub [wersji próbnej](https://www.tableau.com/products/desktop/download) wersji.

* Klastra, który zawiera StormEvents przykładowych danych. Aby uzyskać więcej informacji, zobacz [tworzenia klastra Azure Eksploratora danych i bazy danych](create-cluster-database-portal.md) i [pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Wizualizowanie danych w Tableau 

Po zakończeniu konfigurowania ODBC przykładowych danych może zapewnić Tableau.

1. Na pulpicie Tableau, w menu po lewej stronie wybierz **inne bazy danych (ODBC)** .

    ![Łączenie z bazą danych ODBC](media/tableau/connect-odbc.png)

1. Aby uzyskać **DSN**, wybierz źródło danych utworzone dla ODBC, a następnie wybierz **Sign In**.

    ![Logowanie w ODBC](media/tableau/odbc-sign-in.png)

1. Aby uzyskać **bazy danych**, wybierz bazę danych w klastrze przykładowe, takie jak *TestDatabase*. Dla **schematu**, wybierz opcję *dbo*oraz **tabeli**, wybierz opcję *StormEvents* przykładowej tabeli.

    ![Wybierz bazę danych i tabelę](media/tableau/select-database-table.png)

1. TABLEAU są teraz wyświetlane schematu dla przykładowych danych. Wybierz **Aktualizuj teraz** do przenoszenia danych do firmy Tableau.

    ![Aktualizowanie danych](media/tableau/update-data.png)

    Po zaimportowaniu danych Tableau zawiera wiersze danych podobny do poniższej ilustracji.

    ![Zestaw wyników](media/tableau/result-set.png)

1. Teraz możesz utworzyć wizualizacje w Tableau, w oparciu o dane pochodzące z Eksploratora danych usługi Azure. Aby uzyskać więcej informacji, zobacz [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Kolejne kroki

* [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)