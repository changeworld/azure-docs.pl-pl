---
title: Zarządzanie uprawnieniami bazy danych w usłudze Azure Eksplorator danych
description: W tym artykule opisano kontrolę dostępu opartą na rolach dla baz danych i tabel w usłudze Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030087"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Zarządzanie uprawnieniami usługi Azure Eksplorator danych Database

Usługa Azure Eksplorator danych umożliwia kontrolowanie dostępu do baz danych i tabel przy użyciu modelu *kontroli dostępu opartej na rolach* . W tym modelu *podmioty zabezpieczeń* (Użytkownicy, grupy i aplikacje) są mapowane na *role*. Podmioty zabezpieczeń mogą uzyskać dostęp do zasobów zgodnie z przypisanymi rolami.

W tym artykule opisano dostępne role i sposób przypisywania podmiotów zabezpieczeń do tych ról przy użyciu poleceń Azure Portal i zarządzania usługą Azure Eksplorator danych.

## <a name="roles-and-permissions"></a>Role i uprawnienia

Usługa Azure Eksplorator danych ma następujące role:

|Rola                       |Uprawnienia                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administrator bazy danych             |Można coś zrobić w zakresie konkretnej bazy danych.|
|Użytkownik bazy danych              |Może odczytywać wszystkie dane i metadane w bazie danych. Ponadto mogą tworzyć tabele (stać się administratorem tabeli dla tej tabeli) i funkcje w bazie danych.|
|Przeglądarka bazy danych            |Może odczytywać wszystkie dane i metadane w bazie danych.|
|Pozyskiwanie bazy danych          |Może pozyskać dane do wszystkich istniejących tabel w bazie danych, ale nie zapytania dotyczące danych.|
|Monitor bazy danych           |Można wykonać polecenie ". show..." polecenia w kontekście bazy danych i jej jednostek podrzędnych.|
|Administrator tabeli                |Może zrobić coś w zakresie konkretnej tabeli. |
|Pozyskiwanie tabel             |Może pozyskać dane z zakresu konkretnej tabeli, ale nie wykonuje zapytania dotyczącego danych.|

## <a name="manage-permissions-in-the-azure-portal"></a>Zarządzanie uprawnieniami w Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

1. Przejdź do klastra usługi Azure Eksplorator danych.

1. W sekcji **Przegląd** wybierz bazę danych, w której chcesz zarządzać uprawnieniami.

    ![Wybieranie bazy danych](media/manage-database-permissions/select-database.png)

1. Wybierz pozycję **uprawnienia** , a następnie **Dodaj**.

    ![Uprawnienia bazy danych](media/manage-database-permissions/database-permissions.png)

1. W obszarze **Dodawanie uprawnień do bazy danych**wybierz rolę, do której chcesz przypisać podmiot zabezpieczeń, a następnie **Wybierz pozycję podmioty zabezpieczeń**.

    ![Dodawanie uprawnień do bazy danych](media/manage-database-permissions/add-permission.png)

1. Wyszukaj podmiot zabezpieczeń, wybierz go, a następnie **Wybierz**.

    ![Zarządzanie uprawnieniami w Azure Portal](media/manage-database-permissions/new-principals.png)

1. Wybierz pozycję **Zapisz**.

    ![Zarządzanie uprawnieniami w Azure Portal](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Zarządzanie uprawnieniami za pomocą poleceń zarządzania

1. Zaloguj się do [https://dataexplorer.azure.com](https://dataexplorer.azure.com)i Dodaj swój klaster, jeśli nie jest jeszcze dostępny.

1. W lewym okienku wybierz odpowiednią bazę danych.

1. Użyj `.add` polecenie, aby przypisać podmioty zabezpieczeń do ról: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Aby dodać użytkownika do roli użytkownika bazy danych, uruchom następujące polecenie, zastępując nazwę bazy danych i użytkownika.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    Dane wyjściowe polecenia przedstawia listę istniejących użytkowników i role, do których są przypisane w bazie danych.
    
    Przykłady dotyczące Azure Active Directory i modelu autoryzacji Kusto można znaleźć w temacie [zasady i dostawcy tożsamości](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Następne kroki

[Pisanie zapytań](write-queries.md)
