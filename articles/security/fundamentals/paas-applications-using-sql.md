---
title: Zabezpieczanie baz danych PaaS na platformie Azure | Dokumenty firmy Microsoft
description: 'Dowiedz się więcej o najlepszych praktykach dotyczących zabezpieczeń bazy danych SQL Database i usługi SQL Data Warehouse dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: c73f585e3102618cea378716491f9354810a6db8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125009"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Najważniejsze wskazówki dotyczące zabezpieczania baz danych PaaS na platformie Azure

W tym artykule omówimy kolekcję [azure SQL Database](../../sql-database/sql-database-technical-overview.md) i SQL Data [Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) najlepszych rozwiązań dotyczących zabezpieczania platformy jako usługi sieci web (PaaS) aplikacji sieci web i aplikacji mobilnych. Te najlepsze rozwiązania pochodzą z naszego środowiska z platformą Azure i doświadczeń klientów takich jak Ty.

Usługa Azure SQL Database i SQL Data Warehouse zapewniają relacyjne usługi bazy danych dla aplikacji internetowych. Przyjrzyjmy się usługom, które pomagają chronić aplikacje i dane podczas korzystania z bazy danych SQL Azure i magazynu danych SQL w usłudze PaaS:

- Uwierzytelnianie usługi Azure Active Directory (zamiast uwierzytelniania programu SQL Server)
- Zapora SQL platformy Azure
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Korzystanie ze scentralizowanego repozytorium tożsamości
Bazy danych SQL platformy Azure można skonfigurować tak, aby używały jednego z dwóch typów uwierzytelniania:

- **Uwierzytelnianie SQL** używa nazwy użytkownika i hasła. Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Za pomocą tych poświadczeń można uwierzytelnić do dowolnej bazy danych na tym serwerze jako właściciela bazy danych.

- **Uwierzytelnianie usługi Azure Active Directory** używa tożsamości zarządzanych przez usługę Azure Active Directory i jest obsługiwane w domenach zarządzanych i zintegrowanych. Aby korzystać z uwierzytelniania usługi Azure Active Directory, należy utworzyć innego administratora serwera o nazwie "Administrator usługi Azure AD", który może administrować użytkownikami i grupami usługi Azure AD. Ten administrator może również wykonywać wszystkie operacje, które może wykonać zwykły administrator serwera.

[Uwierzytelnianie usługi Azure Active Directory](../../active-directory/develop/authentication-scenarios.md) to mechanizm łączenia się z usługą Azure SQL Database i magazynem danych SQL przy użyciu tożsamości w usłudze Azure Active Directory (AD). Usługa Azure AD zapewnia alternatywę dla uwierzytelniania programu SQL Server, dzięki czemu można zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych. Uwierzytelnianie usługi Azure AD umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie tożsamościami oznacza jedną lokalizację do zarządzania użytkownikami bazy danych i prostsze zarządzanie uprawnieniami.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Korzyści z używania usługi Azure AD zamiast uwierzytelniania SQL
- Umożliwia obracanie hasła w jednym miejscu.
- Zarządza uprawnieniami bazy danych przy użyciu zewnętrznych grup usługi Azure AD.
- Eliminuje przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure AD.
- Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z bazą danych SQL.
- Obsługuje federację domen z usługami federacjowymi Active Directory (ADFS) lub natywnym uwierzytelnianiem użytkownika/hasła dla lokalnej usługi Azure AD bez synchronizacji domeny.
- Obsługuje połączenia z programu SQL Server Management Studio korzystające z uniwersalnego uwierzytelniania usługi Active Directory, które obejmuje [uwierzytelnianie wieloskładnikowe (MFA).](/azure/active-directory/authentication/multi-factor-authentication) Usługa MFA obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, takich jak połączenia telefoniczne, wiadomości SMS, karty inteligentne z numerem PIN lub powiadomienia przez aplikacje mobilne. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie uniwersalne z bazą danych SQL i magazynem danych SQL](../../sql-database/sql-database-ssms-mfa-authentication.md).

Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz:

- [Używanie uwierzytelniania usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL, wystąpienia zarządzanego lub magazynu danych SQL](../../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Obsługa uwierzytelniania opartego na tokenach dla usługi Azure SQL DB przy użyciu uwierzytelniania usługi Azure AD](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Aby upewnić się, że usługa Azure Active Directory jest odpowiednia dla Twojego środowiska, zobacz [Funkcje i ograniczenia usługi Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Ograniczanie dostępu na podstawie adresu IP
Można utworzyć reguły zapory określające zakresy dopuszczalnych adresów IP. Reguły te mogą być ukierunkowane na poziomie serwera i bazy danych. Zalecamy używanie reguł zapory na poziomie bazy danych, gdy tylko jest to możliwe, aby zwiększyć bezpieczeństwo i zwiększyć przenośność bazy danych. Reguły zapory na poziomie serwera są najlepiej używane dla administratorów i gdy masz wiele baz danych, które mają te same wymagania dotyczące dostępu, ale nie chcesz spędzać czasu na konfigurowaniu każdej bazy danych indywidualnie.

Ograniczenia domyślnego źródłowego adresu IP bazy danych SQL umożliwiają dostęp z dowolnego adresu platformy Azure, w tym innych subskrypcji i dzierżaw. Można ograniczyć to tylko zezwalać adresom IP na dostęp do wystąpienia. Nawet w odniesieniu do ograniczeń zapory SQL i adresów IP nadal potrzebne jest silne uwierzytelnianie. Zapoznaj się z zaleceniami zawartymi wcześniej w tym artykule.

Aby dowiedzieć się więcej o zaporze SQL platformy Azure i ograniczeniach ip, zobacz:

- [Kontrola dostępu do bazy danych SQL i usługi SQL Data Warehouse](../../sql-database/sql-database-manage-logins.md)
- [Reguły zapory usługi Azure SQL Database i SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Szyfrowanie danych magazynowanych
[Przezroczyste szyfrowanie danych (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) jest domyślnie włączone. TDE w sposób przejrzysty szyfruje pliki danych i dzienników sql server, azure SQL Database i Azure SQL Data Warehouse. TDE chroni przed naruszeniem bezpośredniego dostępu do plików lub ich kopii zapasowej. Dzięki temu można szyfrować dane w stanie spoczynku bez zmiany istniejących aplikacji. TDE powinno zawsze pozostać włączone; jednak nie spowoduje to zatrzymania osoby atakującej przy użyciu normalnej ścieżki dostępu. TDE zapewnia możliwość przestrzegania wielu przepisów ustawowych, wykonawczych i wytycznych ustanowionych w różnych branżach.

Usługa Azure SQL zarządza kluczowymi problemami związanymi z TDE. Podobnie jak w przypadku TDE, lokalnie należy zwrócić szczególną uwagę na zapewnienie możliwości odzyskania i przenoszenia baz danych. W bardziej zaawansowanych scenariuszach klucze mogą być jawnie zarządzane w usłudze Azure Key Vault za pośrednictwem rozszerzalnego zarządzania kluczami. Zobacz [Włączanie TDE na programie SQL Server przy użyciu programu EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Pozwala to również na Bring Your Own Key (BYOK) za pośrednictwem usługi Azure Key Vaults BYOK możliwości.

Usługa Azure SQL zapewnia szyfrowanie kolumn za pośrednictwem [zawsze zaszyfrowanych](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Umożliwia to tylko autoryzowanym aplikacjom dostęp do poufnych kolumn. Za pomocą tego rodzaju szyfrowania ogranicza zapytania SQL dla zaszyfrowanych kolumn do wartości opartych na równości.

Szyfrowanie na poziomie aplikacji powinno być również używane do selektywnych danych. Problemy związane z suwerennością danych można czasami złagodzić, szyfrując dane za pomocą klucza przechowywanego w odpowiednim kraju/regionie. Zapobiega to nawet przypadkowemu transferowi danych, ponieważ nie można odszyfrować danych bez klucza, przy założeniu, że używany jest silny algorytm (taki jak AES 256).

Dodatkowe środki ostrożności ułatwiają zabezpieczenie bazy danych, takie jak projektowanie bezpiecznego systemu, szyfrowanie poufnych zasobów i tworzenie zapory wokół serwerów bazy danych.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono kolekcję najlepszych rozwiązań dotyczących zabezpieczeń bazy danych SQL i usługi SQL Data Warehouse dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS. Aby dowiedzieć się więcej o zabezpieczaniu wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](paas-deployments.md)
- [Zabezpieczanie aplikacji internetowych i mobilnych PaaS przy użyciu usług Azure App Services](paas-applications-using-app-services.md)
