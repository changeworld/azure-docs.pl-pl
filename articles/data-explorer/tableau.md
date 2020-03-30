---
title: Wizualizacja danych Tableau za pomocą łącznika ODBC usługi Azure Data Explorer
description: W tym artykule dowiesz się, jak używać połączenia ODBC (Open Database Connectivity) do połączenia Usługi Azure Data Explorer w celu wizualizacji danych za pomocą tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562449"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Wizualizuj dane z Eksploratora danych platformy Azure w Tableau

 [Tableau](https://www.tableau.com/) to platforma do analizy wizualnej dla biznesu. Aby połączyć się z Eksploratorem danych platformy Azure z Tableau i przynieść dane z przykładowego klastra, użyj sterownika SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebujesz następujących czynności:

* [Połącz się z Eksploratorem danych platformy Azure za](connect-odbc.md) pomocą usługi ODBC przy użyciu sterownika ODBC programu SQL Server, aby połączyć się z Eksploratorem danych platformy Azure z Tableau. 

* Tableau Desktop, wersja pełna lub [wersja próbna.](https://www.tableau.com/products/desktop/download)

* Klaster zawierający przykładowe dane StormEvents. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md) oraz pozyskiwania [przykładowych danych do Eksploratora danych platformy Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Wizualizuj dane w Tableau 

Po zakończeniu konfigurowania ODBC, można przynieść przykładowe dane do Tableau.

1. W tableau pulpitu, w lewym menu, wybierz **inne bazy danych (ODBC)**.

    ![Łączenie z bazą danych ODBC](media/tableau/connect-odbc.png)

1. W przypadku **dsn**wybierz źródło danych utworzone dla odbc, a następnie wybierz pozycję **Zaloguj**się .

    ![Logowanie ODBC](media/tableau/odbc-sign-in.png)

1. W obszarze **Baza danych**wybierz bazę danych w przykładowym klastrze, na przykład *TestDatabase*. W przypadku **schematu**wybierz *dbo*i **tabelę**wybierz przykładową tabelę *StormEvents.*

    ![Wybieranie bazy danych i tabeli](media/tableau/select-database-table.png)

1. Tableau pokazuje teraz schemat dla przykładowych danych. Wybierz **opcję Aktualizuj teraz,** aby przenieść dane do Tableau.

    ![Aktualizowanie danych](media/tableau/update-data.png)

    Podczas importowania danych Tableau pokazuje wiersze danych podobne do poniższej ilustracji.

    ![Zestaw wyników](media/tableau/result-set.png)

1. Teraz możesz tworzyć wizualizacje w Tableau na podstawie danych, które zostały wprowadzone z Usługi Azure Data Explorer. Aby uzyskać więcej informacji, zobacz [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla Eksploratora danych platformy Azure](write-queries.md)