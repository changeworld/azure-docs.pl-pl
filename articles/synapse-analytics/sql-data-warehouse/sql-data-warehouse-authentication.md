---
title: Authentication
description: Dowiedz się, jak uwierzytelnić się w usłudze Azure Synapse Analytics przy użyciu usługi Azure Active Directory (Azure AD) lub uwierzytelniania programu SQL Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ccc5db828a03c37d3fc4f49b13883ac3eeda2368
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584230"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Uwierzytelnij się w usłudze Azure Synapse Analytics

Dowiedz się, jak uwierzytelnić się w poool programu Synapse SQL w usłudze Azure Synapse przy użyciu usługi Azure Active Directory (Azure AD) lub uwierzytelniania programu SQL Server.

Aby połączyć się z pulą SQL, należy przekazać poświadczenia zabezpieczeń do celów uwierzytelniania. Po ustanowieniu połączenia niektóre ustawienia połączenia są konfigurowane jako część ustanawiania sesji kwerendy.  

Aby uzyskać więcej informacji na temat zabezpieczeń i włączania połączeń z magazynem danych, zobacz [zabezpieczanie dokumentacji bazy danych](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Uwierzytelnianie SQL

Aby połączyć się z pulą SQL, należy podać następujące informacje:

* W pełni kwalifikowana nazwa serwera
* Określanie uwierzytelniania SQL
* Nazwa użytkownika
* Hasło
* Domyślna baza danych (opcjonalnie)

Domyślnie połączenie łączy się z *główną* bazą danych, a nie z bazą danych użytkowników. Aby połączyć się z bazą danych użytkowników, można wykonać jedną z dwóch czynności:

* Określ domyślną bazę danych podczas rejestrowania serwera za pomocą Eksploratora obiektów programu SQL Server w programie SSDT, SSMS lub w ciągu połączenia aplikacji. Na przykład dołącz parametr InitialCatalog dla połączenia ODBC.
* Wyróżnij bazę danych użytkowników przed utworzeniem sesji w SSDT.

> [!NOTE]
> Instrukcja Transact-SQL **UŻYJ Bazy Danych MyDatabase;** nie jest obsługiwana do zmiany bazy danych dla połączenia. Aby uzyskać wskazówki dotyczące łączenia się z pulą SQL z dyskiem SSDT, zapoznaj się [z artykułem Kwerenda z programem Visual Studio.](sql-data-warehouse-query-visual-studio.md)
> 
> 

## <a name="azure-active-directory-azure-ad-authentication"></a>Uwierzytelnianie usługi Azure Active Directory (Azure AD)

Uwierzytelnianie [usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) to mechanizm łączenia się z pulą SQL przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Za pomocą uwierzytelniania usługi Azure Active Directory można centralnie zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikatorami zapewnia jedno miejsce do zarządzania użytkownikami usługi Azure Synapse i upraszcza zarządzanie uprawnieniami. 

### <a name="benefits"></a>Korzyści

Korzyści z usługi Azure Active Directory obejmują:

* Stanowi alternatywę dla uwierzytelniania programu SQL Server.
* Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych.
* Umożliwia rotację haseł w jednym miejscu
* Zarządzanie uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
* Eliminuje przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
* Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
* Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z pulą SQL.
* Obsługuje uwierzytelnianie wieloskładnikowe za pomocą uniwersalnego uwierzytelniania usługi Active Directory dla różnych narzędzi, w tym [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md) i SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Usługa Azure Active Directory jest nadal stosunkowo nowa i ma pewne ograniczenia. Aby upewnić się, że usługa Azure Active Directory jest odpowiedni dla twojego środowiska, zobacz [funkcje usługi Azure AD i ograniczenia](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), w szczególności dodatkowe zagadnienia.
> 
> 

### <a name="configuration-steps"></a>Kroki konfiguracji

Wykonaj następujące kroki, aby skonfigurować uwierzytelnianie usługi Azure Active Directory.

1. Tworzenie i wypełnianie usługi Azure Active Directory
2. Opcjonalnie: skojarz lub zmień usługę active directory, która jest obecnie skojarzona z subskrypcją platformy Azure
3. Tworzenie administratora usługi Azure Active Directory dla usługi Azure Synapse
4. Konfigurowanie komputerów klienckich
5. Tworzenie użytkowników zawartej bazy danych w bazie danych mapowanych na tożsamości usługi Azure AD
6. Łączenie się z pulą SQL przy użyciu tożsamości usługi Azure AD

Obecnie użytkownicy usługi Azure Active Directory nie są wyświetlane w Eksploratorze obiektów SSDT. Aby obejść ten problem, wyświetl użytkowników w [pliku sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Znajdź szczegóły

* Kroki konfigurowania i używania uwierzytelniania usługi Azure Active Directory są prawie identyczne dla usługi Azure SQL Database i puli SQL Synapse w usłudze Azure Synapse. Postępuj zgodnie ze szczegółowymi instrukcjami w temacie [Łączenie się z bazą danych SQL lub pulą SQL przy użyciu uwierzytelniania usługi Azure Active Directory](../../sql-database/sql-database-aad-authentication.md).
* Tworzenie ról niestandardowych baz danych i dodawanie użytkowników do ról. Następnie przyznaj szczegółowe uprawnienia do ról. Aby uzyskać więcej informacji, zobacz [Wprowadzenie z uprawnieniami aparatu bazy danych](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć wykonywanie zapytań za pomocą programu Visual Studio i innych aplikacji, zobacz [Kwerenda w programie Visual Studio](sql-data-warehouse-query-visual-studio.md).
