---
title: Zabezpieczanie bazy danych PaaS na platformie Azure | Dokumentacja firmy Microsoft
description: " Więcej informacji na temat zabezpieczeń bazy danych SQL Azure i SQL Data Warehouse najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 054a3987cfd67fbd558fe9d4b482aac3d9b467fd
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114681"
---
# <a name="securing-paas-databases-in-azure"></a>Zabezpieczanie PaaS baz danych na platformie Azure

W tym artykule omówiono Kolekcja [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/) i [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z platformy Azure i doświadczenia klientów, takie jak samodzielnie.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Baza danych Azure SQL i SQL Data Warehouse
[Baza danych SQL Azure](../sql-database/sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) świadczenia usługi relacyjnej bazy danych dla aplikacji internetowych. Oto usługi pomagające w ochronie danych i aplikacji, podczas korzystania z bazy danych SQL Azure i SQL Data Warehouse w ramach wdrożenia PaaS:

- Uwierzytelnianie usługi Active Directory platformy Azure (zamiast uwierzytelniania programu SQL Server)
- Zapory SQL Azure
- Przezroczystego szyfrowania danych (funkcji TDE)

## <a name="best-practices"></a>Najlepsze praktyki

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Korzystania z repozytorium scentralizowane tożsamości do uwierzytelniania i autoryzacji

Aby użyć jednej z dwóch typów uwierzytelniania można skonfigurować bazy danych SQL Azure:

- **Uwierzytelnianie SQL** używa nazwy użytkownika i hasła. Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń, można uwierzytelniać na dowolnym bazy danych na tym serwerze jako właściciela bazy danych.

- **Uwierzytelniania usługi Azure Active Directory** używa tożsamości zarządzanych przez usługę Azure Active Directory i jest obsługiwana w przypadku domen zarządzanych i zintegrowanego. Aby użyć uwierzytelniania usługi Azure Active Directory, należy utworzyć inny administrator serwera o nazwie "administratora usługi Azure AD," co jest dozwolone do administrowania usługą Azure AD użytkownicy i grupy. Ten administrator może również wykonywać wszystkie operacje, które może wykonać zwykły administrator serwera.

[Usługa Azure Active Directory authentication](../active-directory/develop/active-directory-authentication-scenarios.md) mechanizm nawiązywać połączenia z bazą danych SQL Azure i SQL Data Warehouse przy użyciu tożsamości w usłudze Azure Active Directory (AD). Usługa Azure AD zapewnia zamiast uwierzytelniania programu SQL Server, na serwerach bazy danych można zatrzymać rozprzestrzenianie tożsamości użytkowników. Usługa Azure AD authentication umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.  

Za pomocą usługi Azure AD authentication zamiast uwierzytelniania SQL zalety:

- Umożliwia obrotu hasła w jednym miejscu.
- Zarządza uprawnień do bazy danych za pomocą zewnętrznej usługi Azure AD grup.
- Eliminuje zapisywania haseł przez włączenie zintegrowanego uwierzytelniania systemu Windows i innych metod uwierzytelniania obsługiwanych przez usługę Azure AD.
- Używa zawarte bazy danych użytkowników do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje uwierzytelnianie na podstawie tokenu dla aplikacji łączenia z bazą danych SQL.
- Obsługuje uwierzytelnianie macierzysty użytkownika/hasło lub usług AD FS (Federacja domen) dla lokalnej usługi Azure AD bez synchronizacji domeny.
- Obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnych uwierzytelnianie usługi Active Directory, która obejmuje [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). Silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe obejmuje MFA — połączenie telefoniczne, wiadomość tekstowa, karty inteligentne z numeru pin lub powiadomienie aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [SSMS obsługę usługi Azure AD MFA z bazy danych SQL i usługi SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Aby dowiedzieć się więcej na temat uwierzytelniania usługi Azure AD, zobacz:

- [Połączenie z bazą danych SQL lub SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Authentication to Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md) (Uwierzytelnianie w usłudze Azure SQL Data Warehouse)
- [Obsługę uwierzytelniania opartego na tokenie dla bazy danych SQL Azure przy użyciu uwierzytelniania usługi Azure AD](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> Aby upewnić się, że usługi Azure Active Directory jest odpowiedni dla danego środowiska, zobacz [funkcje usługi Azure AD i ograniczenia](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), w szczególności dodatkowe zagadnienia.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Ograniczanie dostępu na podstawie adresu IP
Można utworzyć reguły zapory, które określ zakresy adresów IP akceptowane. Te reguły można zastosować na poziomach serwera i bazy danych. Zaleca się przy użyciu reguł zapory na poziomie bazy danych, jeśli to możliwe, aby zwiększyć bezpieczeństwo i wprowadzać przenośną bazy danych. Reguły zapory poziomu serwera najlepiej są używane dla administratorów i jeśli masz wiele baz danych, które mają te same wymagania dostępu, ale nie chcesz poświęcenie czasu na skonfigurowanie każdej bazy danych oddzielnie.

Ograniczenia adresu IP źródłowego domyślne SQL Database zezwolić na dostęp z dowolnego adresu Azure (w tym inne subskrypcje i dzierżawców). Można ograniczyć, aby umożliwić tylko adresy IP do dostępu do wystąpienia. Nawet w przypadku zapory SQL i ograniczenia adresu IP są nadal potrzebne silnego uwierzytelniania. Zobacz zalecenia w tym artykule.

Aby dowiedzieć się więcej na temat ograniczeń zapory SQL Azure i IP, zobacz:

- [Kontrola dostępu do bazy danych SQL Azure](../sql-database/sql-database-control-access.md)
- [Konfigurowanie reguł zapory bazy danych SQL Azure — omówienie](../sql-database/sql-database-firewall-configure.md)
- [Skonfiguruj regułę zapory poziomu serwera bazy danych SQL Azure przy użyciu portalu Azure](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych
[Funkcji przezroczystego szyfrowania danych (TDE)](https://msdn.microsoft.com/library/azure/bb934049) jest domyślnie włączona. Funkcji TDE szyfruje niewidocznie plików danych i dziennika programu SQL Server, bazy danych SQL Azure i usługi Azure SQL Data Warehouse. Funkcji TDE chroni przed naruszeniem bezpośredniego dostępu do plików lub ich kopii zapasowej. Umożliwia szyfrowanie danych magazynowanych bez zmieniania istniejących aplikacji. Funkcji TDE powinien zawsze włączone; jednak nie spowoduje to zatrzymanie atakujący, przy użyciu ścieżki normalny. Funkcji TDE zapewnia możliwość wykonania wielu ustawowych, wykonawczych i wskazówkami w różnych branżach.

Azure SQL zarządza klucza problemy związane z funkcji TDE. Z funkcji TDE, lokalnymi specjalne należy uważać, aby zapewnić możliwość odzyskania danych, gdy przenoszenia baz danych. W bardziej zaawansowanych scenariuszy klucze jawnie odbywa się w usłudze Azure Key Vault za pośrednictwem rozszerzonego zarządzania kluczami (zobacz [włączenia funkcji TDE na SQL Server przy użyciu EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). Umożliwia to także dla Przenieś swój własny klucza (BYOK) za pośrednictwem funkcji BYOK magazynów kluczy Azure.

Azure SQL zapewnia szyfrowanie kolumn za pomocą [zawsze zaszyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Dzięki temu tylko autoryzowanych aplikacji dostęp do poufnych kolumn. Za pomocą tego rodzaju szyfrowania ogranicza kwerendy SQL dla zaszyfrowanych kolumn na podstawie równości wartości.

Szyfrowanie na poziomie aplikacji, powinien także służyć do wybranych danych. Czasami można zminimalizować dotyczy suwerenności danych przez szyfrowanie danych przy użyciu klucza, który jest przechowywany w prawidłowy kraj. Transfer danych nawet przypadkowemu zapobiega przyczyną problemu, ponieważ nie jest możliwe do odszyfrowania danych bez klucza, przy założeniu, że algorytm silne jest używana (na przykład AES 256).

Dodatkowe środki ostrożności służy do zabezpieczania bazy danych, takich jak projektowania bezpieczny system, szyfrowania poufnych zasobów i konfigurowania zapory wokół serwerów baz danych.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wprowadzone do kolekcji z bazy danych SQL i usługi SQL Data Warehouse najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. Aby dowiedzieć się więcej na temat zabezpieczania wdrożeń typu PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](security-paas-deployments.md)
- [Zabezpieczanie PaaS w sieci web i aplikacji dla urządzeń przenośnych za pomocą usługi aplikacji Azure](security-paas-applications-using-app-services.md)
