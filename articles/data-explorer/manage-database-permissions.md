---
title: Zarządzanie uprawnieniami bazy danych w Eksploratorze danych platformy Azure
description: W tym artykule opisano kontroli dostępu opartej na rolach dla bazy danych i tabele w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 36e1bb77be1e825e42f0e5d25457214a8b5f882d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045672"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Zarządzanie uprawnieniami bazy danych Azure Eksplorator danych

Eksplorator danych usługi Azure pozwala na kontrolowanie dostępu do baz danych i tabel, za pomocą *kontroli dostępu opartej na rolach* modelu. W tym modelu *podmiotów* (użytkownikom, grupom i aplikacjom), które są mapowane na *role*. Podmiotów zabezpieczeń można uzyskać dostęp do zasobów zgodnie z rolami, które są przydzielone.

W tym artykule opisano dostępne role i jak można przypisać jednostki do tych ról przy użyciu witryny Azure portal i poleceń zarządzania Eksploratora danych usługi Azure.

## <a name="roles-and-permissions"></a>Role i uprawnienia

Eksplorator usługi Azure Data ma następujące role:

|Rola                       |Uprawnienia                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administrator bazy danych             |Można to zrobić w zakresie konkretnej bazy danych.|
|Użytkownik bazy danych              |Może odczytywać wszystkie dane i metadane w bazie danych. Ponadto mogą utworzyć tabele (staje się administratorem tabeli dla tej tabeli) i funkcje w bazie danych.|
|Podgląd bazy danych            |Może odczytywać wszystkie dane i metadane w bazie danych.|
|Dużych możliwościach skalowania bazy danych          |Pozyskiwanie danych do wszystkich istniejących tabel w bazie danych, ale nie wykonywania zapytań o dane.|
|Monitor bazy danych           |Można wykonać polecenia ".show..." w kontekście bazy danych i jego obiektów podrzędnych.|
|Administrator tabeli                |Można to zrobić w zakresie konkretnej tabeli. |
|Tabela dużych możliwościach skalowania             |Pozyskiwanie danych w zakresie konkretnej tabeli, ale nie wykonywania zapytań o dane.|

## <a name="manage-permissions-in-the-azure-portal"></a>Zarządzanie uprawnieniami w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do klastra usługi Azure Eksploratora danych.

1. W **Przegląd** sekcji, wybierz bazę danych, w którym chcesz zarządzać uprawnieniami.

    ![Wybierz bazę danych](media/manage-database-permissions/select-database.png)

1. Wybierz **uprawnienia** następnie **Dodaj**.

    ![Uprawnienia bazy danych](media/manage-database-permissions/database-permissions.png)

1. W obszarze **Dodaj uprawnienia bazy danych**, wybierz rolę, którą chcesz przypisać podmiot zabezpieczeń, aby następnie **wybierz jednostki**.

    ![Dodaj uprawnienia bazy danych](media/manage-database-permissions/add-permission.png)

1. Wyszukaj podmiot zabezpieczeń, wybierz go, a następnie **wybierz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/manage-database-permissions/new-principals.png)

1. Wybierz pozycję **Zapisz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Zarządzanie uprawnieniami za pomocą polecenia zarządzania

1. Zaloguj się do [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com)i Dodaj klaster, jeśli nie jest jeszcze dostępna.

1. W okienku po lewej stronie wybierz odpowiednią bazą danych.

1. Użyj `.add` polecenie, aby przypisać jednostki do ról: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Aby dodać użytkownika do roli bazy danych, uruchom następujące polecenie, zastępując swoją nazwę bazy danych i użytkownika.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Dane wyjściowe polecenia pokazuje listę istniejących użytkowników i ról które są przydzielone w bazie danych.

## <a name="next-steps"></a>Kolejne kroki

[Pisanie zapytań](write-queries.md)
