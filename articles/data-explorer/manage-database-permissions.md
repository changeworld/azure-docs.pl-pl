---
title: Zarządzanie uprawnieniami do bazy danych w Eksploratorze danych platformy Azure
description: W tym artykule opisano formanty dostępu oparte na rolach dla baz danych i tabel w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030087"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Zarządzanie uprawnieniami bazy danych Usługi Azure Data Explorer

Usługa Azure Data Explorer umożliwia kontrolowanie dostępu do baz danych i tabel przy użyciu modelu *kontroli dostępu opartego na rolach.* W tym modelu *podmioty* (użytkownicy, grupy i aplikacje) są mapowane na *role.* Podmioty mogą uzyskiwać dostęp do zasobów zgodnie z przypisanymi rolami.

W tym artykule opisano dostępne role i sposób przypisywania podmiotów do tych ról przy użyciu poleceń zarządzania witryny Azure portal i Usługi Azure Data Explorer.

## <a name="roles-and-permissions"></a>Role i uprawnienia

Usługa Azure Data Explorer ma następujące role:

|Rola                       |Uprawnienia                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administrator bazy danych             |Można zrobić wszystko w zakresie określonej bazy danych.|
|Użytkownik bazy danych              |Może odczytywać wszystkie dane i metadane w bazie danych. Ponadto mogą tworzyć tabele (stając się administratorem tabeli dla tej tabeli) i funkcje w bazie danych.|
|Przeglądarka baz danych            |Może odczytywać wszystkie dane i metadane w bazie danych.|
|Ingestor bazy danych          |Można pozyskiwania danych do wszystkich istniejących tabel w bazie danych, ale nie kwerendy danych.|
|Monitor bazy danych           |Może wykonać '.show ...' w kontekście bazy danych i jej jednostek podrzędnych.|
|Administrator tabeli                |Może zrobić wszystko w zakresie określonej tabeli. |
|Ingestor tabeli             |Można pozyskiwania danych w zakresie określonej tabeli, ale nie kwerendy danych.|

## <a name="manage-permissions-in-the-azure-portal"></a>Zarządzanie uprawnieniami w witrynie Azure portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Przejdź do klastra usługi Azure Data Explorer.

1. W sekcji **Omówienie** wybierz bazę danych, w której chcesz zarządzać uprawnieniami.

    ![Wybieranie bazy danych](media/manage-database-permissions/select-database.png)

1. Wybierz **pozycję Uprawnienia,** a następnie **dodaj**.

    ![Uprawnienia bazy danych](media/manage-database-permissions/database-permissions.png)

1. W obszarze **Dodawanie uprawnień bazy danych**wybierz rolę, do której chcesz przypisać podmiot zabezpieczeń, a następnie wybierz polecenie Wybierz podmioty **.**

    ![Dodawanie uprawnień do bazy danych](media/manage-database-permissions/add-permission.png)

1. Wyszukaj głównego zobowiązanego, wybierz go, a następnie **wybierz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/manage-database-permissions/new-principals.png)

1. Wybierz **pozycję Zapisz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Zarządzanie uprawnieniami za pomocą poleceń zarządzania

1. Zaloguj się [https://dataexplorer.azure.com](https://dataexplorer.azure.com)do , i dodaj klaster, jeśli nie jest jeszcze dostępny.

1. W lewym okienku wybierz odpowiednią bazę danych.

1. Użyj `.add` polecenia, aby przypisać podmioty `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`do ról: . Aby dodać użytkownika do roli użytkownika bazy danych, uruchom następujące polecenie, zastępując nazwę bazy danych i użytkownika.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Dane wyjściowe polecenia pokazuje listę istniejących użytkowników i role, które są przypisane do bazy danych.
    
    Przykłady dotyczące usługi Azure Active Directory i modelu autoryzacji Kusto można znaleźć w [zasadach i dostawcach tożsamości](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Następne kroki

[Pisanie zapytań](write-queries.md)
