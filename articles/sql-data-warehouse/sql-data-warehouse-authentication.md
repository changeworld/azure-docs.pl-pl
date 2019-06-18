---
title: Uwierzytelnianie w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak do uwierzytelniania usługi Azure SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory (AAD) lub SQL Server.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a3bed9df5b62ce7f2f3df7046357dc4f2458575c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475035"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Uwierzytelnianie w usłudze Azure SQL Data Warehouse
Dowiedz się, jak do uwierzytelniania usługi Azure SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory (AAD) lub SQL Server.

Aby połączyć z usługą SQL Data Warehouse, należy przekazać w poświadczeniach zabezpieczeń na potrzeby uwierzytelniania. Po ustanowieniu połączenia, niektóre ustawienia połączenia są skonfigurowane jako część ustanawianie sesji kwerendy.  

Aby uzyskać więcej informacji na temat zabezpieczeń oraz jak włączyć połączenia z magazynem danych, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Uwierzytelnianie SQL
Aby połączyć z usługą SQL Data Warehouse, należy podać następujące informacje:

* W pełni kwalifikowaną servername
* Określ uwierzytelnianie programu SQL
* Nazwa użytkownika
* Hasło
* Domyślna baza danych (opcjonalnie)

Domyślnie połączenie łączy się z *wzorca* bazy danych, a nie bazy danych użytkowników. Aby połączyć się z bazą danych użytkownika, można wykonać jedną z następujących czynności:

* Podczas rejestrowania serwera w Eksploratorze obiektów SQL Server w programie SSDT SSMS, lub w ciągu połączenia aplikacji, należy określić domyślną bazę danych. Na przykład można dołączyć parametru InitialCatalog dla połączenia ODBC.
* Wyróżnij bazy danych użytkowników przed utworzeniem sesji w programie SSDT.

> [!NOTE]
> Instrukcji języka Transact-SQL **mojabazadanych UŻYJ;** nie jest obsługiwana dla zmienianie bazy danych dla połączenia. Wskazówki dotyczące nawiązywania połączenia z SQL Data Warehouse przy użyciu programu SSDT, można znaleźć [Query with Visual Studio] [ Query with Visual Studio] artykułu.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Uwierzytelnianie usługi Azure Active Directory (AAD)
[Usługa Azure Active Directory] [ What is Azure Active Directory] uwierzytelniania jest mechanizmem nawiązywania połączenia z usługą Microsoft Azure SQL Data Warehouse przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Przy użyciu uwierzytelniania usługi Azure Active Directory można centralnie zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami SQL Data Warehouse i upraszcza zarządzanie uprawnieniami. 

### <a name="benefits"></a>Korzyści
Usługa Azure Active Directory korzyści:

* Stanowi alternatywę dla uwierzytelniania programu SQL Server.
* Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami bazy danych.
* Umożliwia rotacja hasła w jednym miejscu
* Zarządzaj uprawnieniami bazy danych przy użyciu grup zewnętrznych (AAD).
* Eliminuje zapisywania haseł przez włączenie zintegrowane uwierzytelnianie Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
* Zastosowań zawartych użytkowników bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
* Obsługuje uwierzytelnianie oparte na tokenie na potrzeby aplikacji łączenie z usługą SQL Data Warehouse.
* Obsługuje uwierzytelnianie wieloskładnikowe przy użyciu uwierzytelniania usługi Active Directory Universal dla różnych narzędzi, takich jak [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) i [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Usługa Azure Active Directory jest nadal stosunkowo nowe i ma pewne ograniczenia. Aby upewnić się, że usługi Azure Active Directory jest odpowiednia dla danego środowiska, zobacz [funkcji usługi Azure AD i ograniczenia][Azure AD features and limitations], w szczególności dodatkowe zagadnienia.
> 
> 

### <a name="configuration-steps"></a>Kroki konfiguracji
Wykonaj następujące kroki, aby skonfigurować uwierzytelnianie usługi Azure Active Directory.

1. Utworzyć i wypełnić usługi Azure Active Directory
2. Opcjonalnie: Skojarz lub zmienić usługi active directory, która jest aktualnie skojarzona z subskrypcją platformy Azure
3. Utwórz administratora usługi Azure Active Directory dla usługi Azure SQL Data Warehouse.
4. Konfigurowanie komputerów klienckich
5. Tworzenie użytkowników zawartej bazy danych w bazie danych mapowany do tożsamości usługi Azure AD
6. Łączenie z magazynem danych za pomocą tożsamości usługi Azure AD

Obecnie użytkownicy usługi Azure Active Directory nie są wyświetlane w Eksploratorze obiektów programu SSDT. Jako obejście, Wyświetl użytkowników w [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Szczegółowe informacje
* Kroki konfigurowania i korzystania z uwierzytelniania usługi Azure Active Directory są niemal identyczne dla usługi Azure SQL Database i Azure SQL Data Warehouse. Wykonaj szczegółowe instrukcje zostały podane w temacie [nawiązywania połączenia z bazą danych SQL lub SQL danych magazynu przez przy użyciu usługi Azure Active Directory Authentication](../sql-database/sql-database-aad-authentication.md).
* Tworzenie ról niestandardowych bazy danych i dodawanie użytkowników do ról. Następnie przyznania szczegółowych uprawnień do tych ról. Aby uzyskać więcej informacji, zobacz [rozpoczęcie korzystania z bazy danych aparatu uprawnień](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Kolejne kroki
Aby uruchomić zapytania magazynu danych przy użyciu programu Visual Studio i innych aplikacji, zobacz [Query with Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
