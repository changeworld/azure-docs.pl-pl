---
title: Zabezpieczanie baz danych PaaS, na platformie Azure | Dokumentacja firmy Microsoft
description: 'Informacje o zabezpieczeniach usługi Azure SQL Database i SQL Data Warehouse najlepsze rozwiązania dotyczące zabezpieczania usługi PaaS w sieci web i aplikacji mobilnych. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 057ec69db300c3ec95cbc63bd5056a78c24d5660
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601663"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Najlepsze rozwiązania dotyczące zabezpieczania PaaS baz danych, na platformie Azure

W tym artykule omawiane jest kolekcją [usługi Azure SQL Database](../sql-database/sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) najlepsze rozwiązania dotyczące zabezpieczania platform-as-a-service (PaaS) w sieci web i aplikacji mobilnych. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeń z platformą Azure i procesy, przez klientów, takich jak samodzielnie.

Usługa Azure SQL Database i SQL Data Warehouse zapewnia usługa relacyjnej bazy danych, dla aplikacji internetowych. Przyjrzyjmy się usług, które pomagają w ochronie aplikacji i danych, korzystając z usługi Azure SQL Database i SQL Data Warehouse w to wdrożenie PaaS:

- Uwierzytelnianie usługi Active Directory systemu Azure (zamiast uwierzytelniania programu SQL Server)
- Zapora usługi Azure SQL
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Korzystać z tożsamości scentralizowane repozytorium
Aby użyć jednego z dwóch typów uwierzytelniania można skonfigurować baz danych SQL Azure:

- **Uwierzytelnianie SQL** używa nazwy użytkownika i hasła. Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń, można wybrać metodę uwierzytelniania dowolnej bazy danych na tym serwerze jako właściciel bazy danych.

- **Uwierzytelnianie usługi Azure Active Directory** korzysta z tożsamości zarządzanej przez usługę Azure Active Directory i jest obsługiwany w przypadku zarządzanych i zintegrowanych domen. Aby użyć uwierzytelniania usługi Azure Active Directory, należy utworzyć innego administratora serwera o nazwie "Azure AD admin", co umożliwi administrowanie Azure AD użytkownicy i grupy. Ten administrator może również wykonywać wszystkie operacje, które może wykonać zwykły administrator serwera.

[Uwierzytelnianie usługi Azure Active Directory](../active-directory/develop/authentication-scenarios.md) jest mechanizmem nawiązywania połączenia z usługą Azure SQL Database i SQL Data Warehouse przy użyciu tożsamości w usłudze Azure Active Directory (AD). Usługa Azure AD zapewnia alternatywę dla uwierzytelniania programu SQL Server, dzięki czemu możesz zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami bazy danych. Uwierzytelnianie usługi Azure AD umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Korzyści z używania usługi Azure AD zamiast uwierzytelniania SQL
- Umożliwia rotacja hasła w jednym miejscu.
- Zarządza uprawnienia bazy danych za pomocą zewnętrznego Azure grup usługi Active Directory.
- Eliminuje zapisywania haseł przez włączenie zintegrowane uwierzytelnianie Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure AD.
- Zastosowań zawartych użytkowników bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje uwierzytelnianie oparte na tokenach aplikacji nawiązywania połączenia z bazą danych SQL.
- Obsługuje domeny federacyjnej przy użyciu Active Directory Federation Services (ADFS) lub macierzystym użytkownika i hasło uwierzytelniania dla lokalnej usługi Azure AD bez synchronizacji domeny.
- Obsługuje połączenia z SQL Server Management Studio, które używają uniwersalne uwierzytelnianie usługi Active Directory, która zawiera [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). Uwierzytelnianie wieloskładnikowe obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji — połączenie telefoniczne, wiadomość SMS, kart inteligentnych przy użyciu numeru pin lub powiadomienie aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [uniwersalne uwierzytelnianie przy użyciu bazy danych SQL Database i SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Aby dowiedzieć się więcej na temat uwierzytelniania usługi Azure AD, zobacz:

- [Użyj uwierzytelniania usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md) (Uwierzytelnianie w usłudze Azure SQL Data Warehouse)
- [Obsługa uwierzytelniania opartego na tokenach dla bazy danych SQL Azure przy użyciu uwierzytelniania usługi Azure AD](../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Aby upewnić się, że usługi Azure Active Directory jest odpowiednia dla danego środowiska, zobacz [funkcji usługi Azure AD i ograniczenia](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Ograniczanie dostępu na podstawie adresu IP
Można utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Te zasady można zastosować na poziomach serwera i bazy danych. Zaleca się przy użyciu reguł zapory na poziomie bazy danych w miarę możliwości, aby zwiększyć poziom bezpieczeństwa i uczynić bazę danych bardziej przenośny. Reguły zapory na poziomie serwera, najlepiej sprawdzają się dla administratorów i gdy wiele baz danych, które mają takie same wymagania dotyczące dostępu, ale nie chcesz poświęcać czasu na oddzielne Konfigurowanie każdej bazy danych.

Ograniczenia adresów IP źródła domyślnej bazy danych SQL Database zezwala na dostęp z dowolnego adresu platformy Azure, łącznie z innych subskrypcji i dzierżaw. Można ograniczyć tylko zezwolenia na dostęp do wystąpienia adresy IP. Nawet w przypadku zapory SQL i ograniczenia adresów IP są nadal potrzebne silnego uwierzytelniania. Zobacz zalecenia przedstawione w tym artykule.

Aby dowiedzieć się więcej na temat ograniczeń zapory SQL Azure i adres IP, zobacz:

- [Kontrola dostępu do usługi Azure SQL Database i SQL Data Warehouse](../sql-database/sql-database-control-access.md)
- [Reguły zapory usługi Azure SQL Database i SQL Data Warehouse](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Szyfrowanie danych magazynowanych
[Przezroczyste szyfrowanie danych (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) jest domyślnie włączona. TDE w sposób niewidoczny dla użytkownika szyfruje plików danych i dziennika programu SQL Server, usługi Azure SQL Database i Azure SQL Data Warehouse. Funkcja TDE chroni przed naruszeniem bezpośredni dostęp do plików lub ich kopii zapasowej. Umożliwia szyfrowanie danych magazynowanych bez wprowadzania zmian w istniejących aplikacji. Funkcja TDE zawsze powinna pozostać włączone; jednak nie spowoduje to zatrzymanie atakujący, przy użyciu ścieżki normalny dostęp. Funkcja TDE oferuje możliwość wykonania wielu prawem, regulacjami i wskazówkami w różnych branżach.

Usługi Azure SQL zarządza kluczowych problemów powiązanych dla funkcji TDE. Za pomocą funkcji TDE, lokalnych specjalne należy uważać, aby zapewnić możliwość odzyskania danych, gdy przenoszenie baz danych. W bardziej zaawansowanych scenariuszach kluczy można jawnie zarządzać w usłudze Azure Key Vault przy użyciu rozszerzonego zarządzania kluczami. Zobacz [włączyć funkcję TDE w programie SQL Server przy użyciu EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Dzięki temu również dla Bring Your Own Key (BYOK) za pomocą możliwości funkcji BYOK usługi Azure Key magazynów.

Usługi Azure SQL zawiera szyfrowania dla kolumn za pomocą [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Dzięki temu tylko autoryzowane aplikacje dostępu do poufnych kolumn. Za pomocą tego rodzaju szyfrowania ogranicza zapytania SQL dla zaszyfrowanych kolumn na podstawie równości wartości.

Szyfrowanie na poziomie aplikacji powinien również dla wybranych danych. Dotyczy niezależność danych czasami może być ograniczona przez szyfrowanie danych za pomocą klucza, który jest przechowywany w poprawny kraj/region. Transfer danych nawet przypadkowym zapobiega przyczyną problemu, ponieważ nie jest możliwe do odszyfrowania danych bez klucza, przy założeniu, że używany jest algorytm silne (na przykład AES 256).

Dodatkowe środki ostrożności służy do zabezpieczania bazy danych, takich jak projektowanie bezpieczny system szyfrowania poufnych zasobów i tworzenie zapory wokół serwerów bazy danych.

## <a name="next-steps"></a>Kolejne kroki
Ten artykuł zawiera wprowadzenie do kolekcji bazy danych SQL Database i SQL Data Warehouse najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji mobilnych. Aby dowiedzieć się więcej na temat zabezpieczanie wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](security-paas-deployments.md)
- [Zabezpieczanie PaaS w sieci web i mobilnych przy użyciu usługi Azure App Services](security-paas-applications-using-app-services.md)
