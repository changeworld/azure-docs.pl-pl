---
title: Authentication
description: Dowiedz się, jak uwierzytelniać się w Azure SQL Data Warehouse przy użyciu usługi Azure Active Directory (AAD) lub SQL Server uwierzytelniania.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 706c68cb7a139a5c4f6def5aed7ad67e49090ede
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545147"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Uwierzytelnianie do Azure SQL Data Warehouse
Dowiedz się, jak uwierzytelniać się w Azure SQL Data Warehouse przy użyciu usługi Azure Active Directory (AAD) lub SQL Server uwierzytelniania.

Aby nawiązać połączenie z SQL Data Warehouse, musisz przekazać poświadczenia zabezpieczeń w celu uwierzytelnienia. Podczas ustanawiania połączenia niektóre ustawienia połączeń są konfigurowane w ramach ustanawiania sesji zapytań.  

Aby uzyskać więcej informacji na temat zabezpieczeń i sposobu włączania połączeń z magazynem danych, zobacz temat [Zabezpieczanie bazy danych w SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Uwierzytelnianie SQL
Aby nawiązać połączenie z SQL Data Warehouse, należy podać następujące informacje:

* W pełni kwalifikowana ServerName
* Określanie uwierzytelniania SQL
* Nazwa użytkownika
* Hasło
* Domyślna baza danych (opcjonalnie)

Domyślnie połączenie jest nawiązywane z bazą danych *Master* , a nie z bazą danych użytkownika. Aby nawiązać połączenie z bazą danych użytkownika, możesz wykonać jedną z dwóch czynności:

* Określ domyślną bazę danych podczas rejestrowania serwera przy użyciu Eksplorator obiektów SQL Server w SSDT, SSMS lub w parametrach połączenia aplikacji. Na przykład Dołącz parametr InitialCatalog dla połączenia ODBC.
* Wyróżnij bazę danych użytkownika przed utworzeniem sesji w programie SSDT.

> [!NOTE]
> Instrukcja języka Transact-SQL **Użyj elementu webdatabase;** nie jest obsługiwana w przypadku zmiany bazy danych dla połączenia. Aby uzyskać wskazówki dotyczące łączenia się z SQL Data Warehouse za pomocą SSDT, zobacz [zapytanie z programem Visual Studio](sql-data-warehouse-query-visual-studio.md) .
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Uwierzytelnianie Azure Active Directory (AAD)
[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) uwierzytelnianiem jest mechanizm łączenia się z Microsoft Azure SQL Data Warehouse przy użyciu tożsamości w Azure Active Directory (Azure AD). Przy użyciu uwierzytelniania Azure Active Directory można centralnie zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie IDENTYFIKATORami umożliwia zarządzanie SQL Data Warehouse użytkownikami i upraszcza zarządzanie uprawnieniami. 

### <a name="benefits"></a>Korzyści
Korzyści Azure Active Directory obejmują:

* Stanowi alternatywę dla SQL Server uwierzytelniania.
* Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach baz danych.
* Umożliwia rotację haseł w jednym miejscu
* Zarządzanie uprawnieniami bazy danych przy użyciu grup zewnętrznych (AAD).
* Eliminuje przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez Azure Active Directory.
* Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
* Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z SQL Data Warehouse.
* Obsługuje uwierzytelnianie wieloskładnikowe za Active Directory uniwersalnego uwierzytelniania dla różnych narzędzi, w tym [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) i [SQL Server narzędzi](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)do obsługi danych.

> [!NOTE]
> Azure Active Directory nadal jest stosunkowo nowy i ma pewne ograniczenia. Aby upewnić się, że Azure Active Directory jest dobrze dopasowane do danego środowiska, zobacz [funkcje i ograniczenia usługi Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), w tym dodatkowe zagadnienia.
> 
> 

### <a name="configuration-steps"></a>Kroki konfiguracji
Wykonaj następujące kroki, aby skonfigurować uwierzytelnianie Azure Active Directory.

1. Tworzenie i wypełnianie Azure Active Directory
2. Opcjonalnie: Skojarz lub Zmień usługę Active Directory, która jest aktualnie skojarzona z subskrypcją platformy Azure
3. Utwórz Azure Active Directory administratora dla Azure SQL Data Warehouse.
4. Konfigurowanie komputerów klienckich
5. Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD
6. Nawiązywanie połączenia z magazynem danych przy użyciu tożsamości usługi Azure AD

Obecnie Azure Active Directory użytkownicy nie są wyświetlani w Eksplorator obiektów SSDT. Aby obejść ten element, Wyświetl użytkowników w obszarze [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Znajdź szczegóły
* Kroki konfigurowania i używania uwierzytelniania Azure Active Directory są niemal identyczne w przypadku Azure SQL Database i Azure SQL Data Warehouse. Postępuj zgodnie ze szczegółowymi instrukcjami w temacie [nawiązywanie połączenia z usługą SQL Database lub SQL Data Warehouse przy użyciu uwierzytelniania Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Utwórz niestandardowe role bazy danych i Dodaj użytkowników do ról. Następnie przyznaj uprawnienia szczegółowe do ról. Aby uzyskać więcej informacji, zobacz [wprowadzenie z uprawnieniami aparatu bazy danych](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć tworzenie zapytań względem magazynu danych przy użyciu programu Visual Studio i innych aplikacji, zobacz artykuł [Query with Visual Studio](sql-data-warehouse-query-visual-studio.md) (Wykonywanie zapytań przy użyciu programu Visual Studio).
