---
title: Użyj połączenia Open Database Connectivity (ODBC) z usługą Azure Eksplorator danych, aby wizualizować dane za pomocą Tableau
description: W tym artykule dowiesz się, jak używać połączenia Open Database Connectivity (ODBC) z usługą Azure Eksplorator danych Connection do wizualizacji danych za pomocą Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4dd8fbd761a3442536919e17bae5465adf6b945f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023858"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Wizualizuj dane z usługi Azure Eksplorator danych w Tableau

 [Tableau](https://www.tableau.com/) to platforma analityczna do analizy biznesowej. Aby nawiązać połączenie z usługą Azure Eksplorator danych z Tableau i wprowadzić dane z przykładowego klastra, użyj sterownika SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące elementy:

* [Połącz się z usługą azure Eksplorator danych za pomocą ODBC](connect-odbc.md) przy użyciu sterownika SQL Server ODBC, aby nawiązać połączenie z platformą Azure Eksplorator danych z Tableau. 

* Tableau Desktop, pełna lub wersja [próbna](https://www.tableau.com/products/desktop/download) .

* Klaster zawierający przykładowe dane StormEvents. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra Eksplorator danych i bazy danych platformy Azure](create-cluster-database-portal.md) oraz pobieranie [przykładowych danych do usługi Azure Eksplorator danych](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Wizualizowanie danych w Tableau 

Po zakończeniu konfigurowania ODBC można przenieść przykładowe dane do Tableau.

1. Na pulpicie Tableau, w menu po lewej stronie wybierz kolejno pozycje **inne bazy danych (ODBC)** .

    ![Łączenie z bazą danych ODBC](media/tableau/connect-odbc.png)

1. W polu **DSN**wybierz źródło danych utworzone dla ODBC, a następnie wybierz pozycję **Zaloguj się**.

    ![Logowanie za pośrednictwem ODBC](media/tableau/odbc-sign-in.png)

1. W polu **baza danych**wybierz bazę danych w przykładowym klastrze, na przykład *TestDatabase*. W obszarze **schemat**wybierz pozycję *dbo*, a dla opcji **tabela**wybierz tabelę przykładową *StormEvents* .

    ![Wybieranie bazy danych i tabeli](media/tableau/select-database-table.png)

1. Tableau teraz pokazuje schemat dla przykładowych danych. Wybierz pozycję **Aktualizuj teraz** , aby przenieść dane do Tableau.

    ![Aktualizowanie danych](media/tableau/update-data.png)

    Po zaimportowaniu danych Tableau wyświetla wiersze danych podobne do poniższej ilustracji.

    ![Zestaw wyników](media/tableau/result-set.png)

1. Teraz można tworzyć wizualizacje w programie Tableau w oparciu o dane wprowadzone w ramach platformy Azure Eksplorator danych. Aby uzyskać więcej informacji, zobacz [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)