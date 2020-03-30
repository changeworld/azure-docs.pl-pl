---
title: Podręcznik dotyczący spełniania wspólnych wymagań dotyczących bezpieczeństwa | Dokumenty firmy Microsoft
titleSuffix: Azure SQL Database
description: Ten artykuł zawiera typowe wymagania dotyczące zabezpieczeń i najlepsze rozwiązania w usłudze Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: c18e1b1a1feba5c528a692b7d63287b3751b62cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77506216"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Podręcznik do rozwiązywania typowych wymagań dotyczących zabezpieczeń za pomocą usługi Azure SQL Database

> [!NOTE]
> Ten dokument zawiera najlepsze rozwiązania dotyczące rozwiązywania typowych wymagań dotyczących zabezpieczeń. Nie wszystkie wymagania mają zastosowanie do wszystkich środowisk i należy skonsultować się z zespołem bazy danych i zabezpieczeń, na których funkcje do zaimplementowania.

## <a name="solving-common-security-requirements"></a>Rozwiązywanie wspólnych wymagań dotyczących zabezpieczeń

Ten dokument zawiera wskazówki dotyczące rozwiązywania typowych wymagań dotyczących zabezpieczeń dla nowych lub istniejących aplikacji przy użyciu usługi Azure SQL Database. Jest zorganizowany przez obszary bezpieczeństwa wysokiego poziomu. Aby rozwiązać określone zagrożenia, zapoznaj się z sekcją [Typowe zagrożenia bezpieczeństwa i potencjalne środki zaradcze.](#common-security-threats-and-potential-mitigations) Chociaż niektóre z przedstawionych zaleceń mają zastosowanie podczas migracji aplikacji z lokalnego na platformę Azure, scenariusze migracji nie są przedmiotem tego dokumentu.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Oferty wdrażania bazy danych SQL usługi Azure omówione w tym przewodniku

- [Bazy danych SQL:](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) [pojedyncze bazy danych](sql-database-single-database.md) i [pule elastyczne](sql-database-elastic-pool.md) na [serwerach bazy danych SQL Azure](sql-database-servers.md)
- [Wystąpienia zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Oferty wdrażania SQL, które nie zostały omówione w tym przewodniku

- Azure SQL Data Warehouse
- Maszyny wirtualne sql platformy Azure (IaaS)
- Sql Server lokalnie

### <a name="audience"></a>Grupy odbiorców

Odbiorcami przeznaczonymi dla tego przewodnika są klienci stojący pytania dotyczące sposobu zabezpieczania usługi Azure SQL Database. Role zainteresowane tym artykułem najlepszych praktyk obejmują między innymi:

- Architekci zabezpieczeń
- Menedżerowie zabezpieczeń
- Inspektorzy ds. zgodności
- Inspektorzy ochrony prywatności
- Inżynierowie bezpieczeństwa

### <a name="using-this-guide"></a><a id="using"></a>Korzystanie z tego przewodnika

Ten dokument jest przeznaczony jako towarzysz naszej istniejącej dokumentacji [zabezpieczeń usługi Azure SQL Database.](sql-database-security-overview.md)

O ile nie określono inaczej, zalecamy przestrzeganie wszystkich najlepszych praktyk wymienionych w każdej sekcji, aby osiągnąć odpowiedni cel lub wymóg. Aby spełnić określone standardy zgodności z przepisami lub najlepsze praktyki w zakresie zgodności z przepisami, ważne mechanizmy kontroli zgodności z przepisami są wymienione w sekcji Wymagania lub cele w stosownych przypadkach. Są to normy bezpieczeństwa i przepisy, o których mowa w niniejszym dokumencie:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Kontrola dostępu, kryptografia
- [Microsoft Operational Security Assurance (OSA) praktyki:](https://www.microsoft.com/en-us/securityengineering/osa/practices)Praktyka #1-6 i #9
- [Publikacja specjalna NIST 800-53 Środki kontroli bezpieczeństwa:](https://nvd.nist.gov/800-53)AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Opinia

Planujemy nadal aktualizować zalecenia i najlepsze rozwiązania wymienione tutaj. Podaj dane wejściowe lub wszelkie poprawki dla tego dokumentu za pomocą łącza **Opinie** u dołu tego artykułu.

## <a name="authentication"></a>Uwierzytelnianie

Uwierzytelnianie jest procesem udowodnienia, że użytkownik jest tym, za kogo się podają. Usługa Azure SQL Database obsługuje dwa typy uwierzytelniania:

- Uwierzytelnianie SQL
- Uwierzytelnianie za pomocą usługi Azure Active Directory

> [!NOTE]
> Uwierzytelnianie usługi Azure Active Directory może nie być obsługiwane dla wszystkich narzędzi i aplikacji innych firm.

### <a name="central-management-for-identities"></a>Centralne zarządzanie tożsamościami

Centralne zarządzanie tożsamością oferuje następujące korzyści:

- Zarządzaj kontami grup i kontroluj uprawnienia użytkowników bez duplikowania loginów na serwerach i bazach danych usługi Azure SQL Database.
- Uproszczone i elastyczne zarządzanie uprawnieniami.
- Zarządzanie aplikacjami na dużą skalę.

**Jak wdrożyć**:

- Użyj uwierzytelniania usługi Azure Active Directory (Azure AD) do zarządzania scentralizowanymi tożsamościami.

**Najważniejsze wskazówki:**

- Utwórz dzierżawę usługi Azure AD i [utwórz użytkowników](../active-directory/fundamentals/add-users-azure-active-directory.md) do reprezentowania użytkowników [ludzkich](../active-directory/develop/app-objects-and-service-principals.md) i tworzenia podmiotów zabezpieczeń usług do reprezentowania aplikacji, usług i narzędzi automatyzacji. Jednostki usługi są równoważne kont usług w systemach Windows i Linux. 

- Przypisywanie praw dostępu do zasobów do podmiotów zabezpieczeń usługi Azure AD za pomocą przypisania grupy: Tworzenie grup usługi Azure AD, udzielanie dostępu do grup i dodawanie poszczególnych członków do grup. W bazie danych utwórz użytkowników zawartej bazy danych, którzy mapują grupy usługi Azure AD. Aby przypisać uprawnienia wewnątrz bazy danych, umieść użytkowników w rolach bazy danych z odpowiednimi uprawnieniami.
  - Zobacz artykuły, [Konfigurowanie uwierzytelniania usługi Azure Active Directory i zarządzanie nim za pomocą języka SQL](sql-database-aad-authentication-configure.md) i używanie usługi Azure AD do [uwierzytelniania za pomocą programu SQL.](sql-database-aad-authentication.md)
  > [!NOTE]
  > W wystąpieniu zarządzanym można również utworzyć logowania, które mapują podmioty usługi Azure AD w głównej bazie danych. Zobacz [TWORZENIE LOGOWANIA (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Korzystanie z grup usługi Azure AD upraszcza zarządzanie uprawnieniami i zarówno właściciel grupy, jak i właściciel zasobu można dodać/usunąć członków do/z grupy. 

- Utwórz oddzielną grupę dla administratora usługi Azure AD dla serwerów bazy danych SQL.

  - Zobacz artykuł [Aprowizuj administratora usługi Azure Active Directory dla serwera bazy danych SQL platformy Azure](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Monitorowanie zmian członkostwa w grupach usługi Azure AD przy użyciu raportów aktywności inspekcji usługi Azure AD. 

- W przypadku wystąpienia zarządzanego do utworzenia administratora usługi Azure AD wymagany jest osobny krok. 
  - Zobacz artykuł [Aprowizuj administratora usługi Azure Active Directory dla wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Uwierzytelnianie usługi Azure AD jest rejestrowane w dziennikach inspekcji SQL platformy Azure, ale nie w dziennikach logowania usługi Azure AD.
> - Uprawnienia RBAC przyznane na platformie Azure nie mają zastosowania do uprawnień usługi Azure SQL DB. Takie uprawnienia muszą być tworzone/mapowane ręcznie w bazy danych SQL przy użyciu istniejących uprawnień SQL.
> - Po stronie klienta uwierzytelnianie usługi Azure AD wymaga dostępu do Internetu lub za pośrednictwem trasy zdefiniowanej przez użytkownika (UDR) do sieci wirtualnej.
> - Token dostępu usługi Azure AD jest buforowany po stronie klienta, a jego okres istnienia zależy od konfiguracji tokenu. Zobacz artykuł [Konfigurowalne okresy istnienia tokenów w usłudze Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Aby uzyskać wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem usługi Azure AD, zobacz następujący blog:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Usługa Multi-Factor Authentication (MFA)

> Wymienione w: OSA Practice #2, ISO Access Control (AC)

Uwierzytelnianie wieloskładnikowe usługi Azure (MFA) pomaga zapewnia dodatkowe zabezpieczenia, wymagając więcej niż jednej formy uwierzytelniania.

**Jak wdrożyć**:

- [Włącz usługę MFA](../active-directory/authentication/concept-mfa-howitworks.md) w usłudze Azure AD przy użyciu dostępu warunkowego i użyj uwierzytelniania interaktywnego. 

- Alternatywą jest włączenie usługi MFA dla całej domeny usługi Azure AD lub AD.

**Najważniejsze wskazówki:**

- Aktywuj dostęp warunkowy w usłudze Azure AD (wymaga subskrypcji Premium). 
  - Zobacz artykuł [Dostęp warunkowy w usłudze Azure AD](../active-directory/conditional-access/overview.md).  

- Utwórz grupy usług Azure AD i włącz zasady usługi MFA dla wybranych grup przy użyciu dostępu warunkowego usługi Azure AD. 
  - Zobacz artykuł [Planowanie wdrożenia dostępu warunkowego](../active-directory/conditional-access/plan-conditional-access.md). 

- Usługa MFA może być włączona dla całej usługi Azure AD lub dla całej usługi Active Directory federowanej z usługą Azure AD. 

- Użyj trybu uwierzytelniania usługi Azure AD Interactive dla bazy danych SQL, gdzie hasło jest wymagane interaktywnie, a następnie uwierzytelnianie usługi MFA:      
  - Użyj uwierzytelniania uniwersalnego w ssms. Zobacz artykuł [Przy użyciu wieloczynnikowego uwierzytelniania AAD za pomocą usługi Azure SQL Database i usługi Azure SQL Data Warehouse (obsługa usługi SSMS dla usługi MFA).](sql-database-ssms-mfa-authentication.md)
  - Użyj uwierzytelniania interaktywnego obsługiwanego w narzędziach SQL Server Data Tools (SSDT). Zobacz ten artykuł, [obsługa usługi Azure Active Directory w narzędziach SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Użyj innych narzędzi SQL obsługujących usługi MFA. 
    - Obsługa kreatora SSMS dla eksportu/wyodrębniania/wdrażania bazy danych  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): opcja '/ua' 
    - [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility): opcja -G (interaktywna)
    - [bcp Utility](https://docs.microsoft.com/sql/tools/bcp-utility): opcja -G (interaktywna) 

- Zaimplementuj aplikacje, aby połączyć się z usługą Azure SQL Database przy użyciu uwierzytelniania interaktywnego z obsługą usługi MFA. 
  - Zobacz artykuł [Połącz się z usługą Azure SQL Database za pomocą uwierzytelniania wieloskładnikowego platformy Azure](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Ten tryb uwierzytelniania wymaga tożsamości opartych na użytkownikach. W przypadkach, gdy używany jest model tożsamości zaufanej, który pomija indywidualne uwierzytelnianie użytkowników usługi Azure AD (np. przy użyciu tożsamości zarządzanej dla zasobów platformy Azure), usługa MFA nie ma zastosowania.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimalizowanie korzystania z uwierzytelniania opartego na hasłach dla użytkowników

> Wspomniano w: OSA Practice #4, ISO Access Control (AC)

Metody uwierzytelniania oparte na hasłach są słabszą formą uwierzytelniania. Poświadczenia mogą zostać naruszone lub omyłkowo rozdane.

**Jak wdrożyć**:

- Użyj zintegrowanego uwierzytelniania usługi Azure AD, które eliminuje użycie haseł.

**Najważniejsze wskazówki:**

- Użyj uwierzytelniania logowania jednokrotnego przy użyciu poświadczeń systemu Windows. Sedno lokalnej domeny usługi AD za pomocą usługi Azure AD i użyj zintegrowanego uwierzytelniania systemu Windows (dla komputerów przyłączonych do domeny z usługą Azure AD).
  - Zobacz artykuł Obsługa [SSMS dla zintegrowanego uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimalizowanie korzystania z uwierzytelniania opartego na hasłach w aplikacjach 

> Wspomniano w: OSA Practice #4, ISO Access Control (AC)

**Jak wdrożyć**:

- Włącz tożsamość zarządzana platformy Azure. Można również użyć uwierzytelniania zintegrowanego lub opartego na certyfikatach. 

**Najważniejsze wskazówki:**

- Użyj [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).
  - [Tożsamość zarządzana przypisana do systemu](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Tożsamość zarządzana przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Użyj usługi Azure SQL Database z usługi app service z tożsamością zarządzaną (bez zmian kodu)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Użyj uwierzytelniania opartego na cert dla aplikacji. 
  - Zobacz ten [przykładowy kod](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Użyj uwierzytelniania usługi Azure AD dla zintegrowanej domeny federacyjnej i komputera przyłączonego do domeny (zobacz sekcję powyżej).
  - Zobacz [przykładową aplikację do zintegrowanego uwierzytelniania](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Ochrona haseł i wpisów tajnych

W przypadkach, gdy haseł nie da się uniknąć, upewnij się, że są zabezpieczone.

**Jak wdrożyć**:

- Usługa Azure Key Vault służy do przechowywania haseł i wpisów tajnych. W stosownych przypadkach należy użyć usługi MFA dla usługi Azure SQL Database z użytkownikami usługi Azure AD.

**Najważniejsze wskazówki:**

- Jeśli unikanie haseł lub wpisów tajnych nie jest możliwe, przechowuj hasła użytkowników i wpisy tajne aplikacji w usłudze Azure Key Vault i zarządzaj dostępem za pomocą zasad dostępu usługi Key Vault. 

- Różne struktury tworzenia aplikacji może również oferować mechanizmy specyficzne dla struktury do ochrony wpisów tajnych w aplikacji. Na przykład: [ASP.NET podstawową aplikację](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Używanie uwierzytelniania SQL dla starszych aplikacji 

Uwierzytelnianie SQL odnosi się do uwierzytelniania użytkownika podczas łączenia się z usługą Azure SQL Database przy użyciu nazwy użytkownika i hasła. Należy utworzyć identyfikator logowania na każdym serwerze bazy danych SQL lub wystąpieniu zarządzanym, a użytkownik został utworzony w każdej bazie danych.

**Jak wdrożyć**:

- Użyj uwierzytelniania SQL.

**Najważniejsze wskazówki:**

- Jako administrator serwera twórz loginy i użytkowników. O ile nie używa się użytkowników zawartej bazy danych z hasłami, wszystkie hasła są przechowywane w głównej bazie danych.
  - Zobacz artykuł [Kontrolowanie i udzielanie dostępu do bazy danych SQL database i usługi SQL Data Warehouse](sql-database-manage-logins.md).

## <a name="access-management"></a>Zarządzanie dostępem

Zarządzanie dostępem to proces kontrolowania dostępu autoryzowanych użytkowników i zarządzania nimi oraz ich uprawnień do bazy danych SQL.

### <a name="implement-principle-of-least-privilege"></a>Wdrażanie zasady najmniejszych uprawnień

> Wspomniano w: FedRamp kontroluje AC-06, NIST: AC-6, OSA Practice #3

Zasada najmniejszych uprawnień stanowi, że użytkownicy nie powinni mieć więcej uprawnień niż jest to potrzebne do wykonywania swoich zadań. Aby uzyskać więcej informacji, zobacz artykuł [Wystarczy administracja](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Jak wdrożyć**:

Przypisz tylko [uprawnienia](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) niezbędne do wykonania wymaganych zadań:

- W płaszczyźnie danych SQL: 
    - Użyj szczegółowych uprawnień i ról bazy danych zdefiniowanych przez użytkownika (lub ról serwera w MI): 
        1. Tworzenie wymaganych ról
            - [TWORZENIE ROLI](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [TWORZENIE ROLI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Tworzenie wymaganych użytkowników
            - [TWORZENIE UŻYTKOWNIKA](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Dodawanie użytkowników jako członków do ról 
            - [ZMIEŃ ROLĘ](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ZMIANA ROLI SERWERA](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Następnie przypisz uprawnienia do ról. 
            - [Udzielić](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Upewnij się, że nie przypisują użytkowników do niepotrzebnych ról.

- W usłudze Azure Resource Manager:
  - Użyj wbudowanych ról, jeśli są dostępne lub niestandardowe role RBAC i przypisz niezbędne uprawnienia.
    - [Wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md) 
    - [Niestandardowe role dla zasobów platformy Azure](../role-based-access-control/custom-roles.md)

**Najważniejsze wskazówki:**

Następujące najlepsze rozwiązania są opcjonalne, ale skutkują lepszą możliwością zarządzania i wsparciem strategii zabezpieczeń: 

- Jeśli to możliwe, zacznij od możliwie najmniejszego zestawu uprawnień i zacznij dodawać uprawnienia jeden po drugim, jeśli istnieje rzeczywista konieczność (i uzasadnienie) — w przeciwieństwie do podejścia przeciwnego: od podejmowania uprawnień krok po kroku. 

- Powstrzymaj się od przypisywania uprawnień poszczególnym użytkownikom. Zamiast tego należy konsekwentnie używać ról (ról bazy danych lub serwera). Role znacznie pomagają w raportowaniu i rozwiązywaniu problemów z uprawnieniami. (Usługa Azure RBAC obsługuje przypisywanie uprawnień tylko za pośrednictwem ról). 

- Tworzenie i używanie ról niestandardowych z dokładnymi wymaganymi uprawnieniami. Typowe role, które są używane w praktyce: 
  - Wdrażanie zabezpieczeń 
  - Administrator 
  - Developer 
  - Personel pomocniczy 
  - Audytor 
  - Zautomatyzowane procesy 
  - Użytkownik końcowy 
  
- Użyj wbudowanych ról tylko wtedy, gdy uprawnienia ról odpowiadają dokładnie wymaganych uprawnień dla użytkownika. Można przypisać użytkowników do wielu ról. 

- Należy pamiętać, że uprawnienia w programie SQL Server Database Engine mogą być stosowane w następujących zakresach. Im mniejszy zakres, tym mniejszy wpływ przyznanych uprawnień: 
  - Serwer bazy danych SQL azure (role specjalne w głównej bazie danych) 
  - baza danych 
  - Schemat
      - Najlepszym rozwiązaniem jest użycie schematów do udzielania uprawnień wewnątrz bazy danych. (zobacz także: [Projekt schematu dla programu SQL Server: zalecenia dotyczące projektowania schematu z myślą o zabezpieczeniach)](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)
  - Obiekt (tabela, widok, procedura itp.) 
  > [!NOTE]
  > Nie zaleca się stosowania uprawnień na poziomie obiektu, ponieważ ten poziom dodaje niepotrzebną złożoność do ogólnej implementacji. Jeśli zdecydujesz się użyć uprawnień na poziomie obiektu, powinny być wyraźnie udokumentowane. To samo dotyczy uprawnień na poziomie kolumny, które są jeszcze mniej godne polecenia z tych samych powodów. Należy również pamiętać, że domyślnie [deny](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) na poziomie tabeli nie zastępuje grant na poziomie kolumny. Wymagałoby to aktywowania [konfiguracji serwera zgodności z typowymi kryteriami.](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)

- Przeprowadzaj regularne kontrole za pomocą [oceny luk w zabezpieczeniach (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) w celu przetestowania zbyt wielu uprawnień.

### <a name="implement-separation-of-duties"></a>Wdrożenie rozdzielenia obowiązków

> Wymienione w: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Separacja obowiązków, zwana również segregacją obowiązków, opisuje wymóg podziału poufnych zadań na wiele zadań, które są przypisane do różnych użytkowników. Rozdzielenie obowiązków pomaga zapobiegać naruszeniom ochrony danych.

**Jak wdrożyć**:

- Określ wymagany poziom rozdzielenia obowiązków. Przykłady: 
  - Między środowiskami rozwoju/testowania i produkcji 
  - Zadania na poziomie zarządzania a administratorem bazy danych (DBA) a zadania deweloperskie. 
    - Przykłady: Audytor, tworzenie zasad zabezpieczeń dla zabezpieczeń na poziomie roli (RLS), implementowanie obiektów bazy danych SQL z uprawnieniami DDL.

- Zidentyfikuj kompleksową hierarchię użytkowników (i zautomatyzowanych procesów), którzy uzyskują dostęp do systemu.

- Tworzenie ról zgodnie z wymaganymi grupami użytkowników i przypisywanie uprawnień do ról. 
  - W przypadku zadań na poziomie zarządzania w witrynie Azure portal lub za pośrednictwem automatyzacji programu PowerShell należy użyć ról RBAC. Znajdź wbudowaną rolę odpowiadającą wymaganiu lub utwórz niestandardową rolę RBAC przy użyciu dostępnych uprawnień 
  - Tworzenie ról serwera dla zadań całego serwera (tworzenie nowych loginów, baz danych) w wystąpieniu zarządzanym. 
  - Tworzenie ról bazy danych dla zadań na poziomie bazy danych.

- W przypadku niektórych poufnych zadań należy rozważyć utworzenie specjalnych procedur przechowywanych podpisanych certyfikatem w celu wykonania zadań w imieniu użytkowników. 
  - Przykład: [Samouczek: Podpisywanie procedur przechowywanych za pomocą certyfikatu](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Zaimplementuj przezroczyste szyfrowanie danych (TDE) z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault, aby włączyć rozdzielenie obowiązków między właścicielem danych a właścicielem zabezpieczeń. 
  - Zobacz artykuł [Konfigurowanie kluczy zarządzanych przez klienta dla szyfrowania usługi Azure Storage z witryny Azure Portal](../storage/common/storage-encryption-keys-portal.md). 

- Aby upewnić się, że DBA nie widzi danych, które są uważane za bardzo wrażliwe i nadal można wykonywać zadania DBA, można użyć zawsze zaszyfrowane z separacji ról. 
  - Zobacz artykuły, [Omówienie zarządzania kluczami dla zawsze zaszyfrowanych,](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [Inicjowanie obsługi administracyjnej kluczy z separacją ról](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)i [rotacja klucza głównego kolumny z separacją ról](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- W przypadkach, gdy nie jest to możliwe, przynajmniej nie bez większych kosztów i wysiłków, które mogą sprawić, że system stanie się prawie bezużyteczny, można dokonać i złagodzić je poprzez zastosowanie form kompensacyjnych, takich jak: 
  - Interwencja człowieka w procesy. 
  - Ścieżki inspekcji — aby uzyskać więcej informacji na temat inspekcji, zobacz [Inspekcja krytycznych zdarzeń zabezpieczeń](#audit-critical-security-events).

**Najważniejsze wskazówki:**

- Upewnij się, że różne konta są używane w środowiskach deweloperskich/testowych i produkcyjnych. Różne konta pomagają w przestrzeganiu rozdzielenia systemów testowych & produkcyjnych.

- Powstrzymaj się od przypisywania uprawnień poszczególnym użytkownikom. Zamiast tego należy konsekwentnie używać ról (ról bazy danych lub serwera). Posiadanie ról znacznie pomaga w raportowaniu i rozwiązywaniu problemów z uprawnieniami.

- Użyj wbudowanych ról, gdy uprawnienia są zgodne dokładnie z wymaganymi uprawnieniami — jeśli połączenie wszystkich uprawnień z wielu wbudowanych ról prowadzi do dopasowania 100%, można przypisać wiele ról jednocześnie. 

- Tworzenie i używanie ról niestandardowych, gdy wbudowane role przyznają zbyt wiele uprawnień lub niewystarczające uprawnienia. 

- Przypisania ról można również wykonać tymczasowo, znany również jako dynamiczne rozdzielenie obowiązków (DSD), albo w ramach sql agent zadania kroki w T-SQL lub przy użyciu usługi Azure PIM dla ról RBAC. 

- Upewnij się, że administratorzy dbA nie mają dostępu do kluczy szyfrowania lub magazynów kluczy, a administratorzy zabezpieczeń z dostępem do kluczy nie mają dostępu do bazy danych po kolei. 

- Zawsze upewnij się, że masz dziennik inspekcji dla działań związanych z zabezpieczeniami. 

- Można pobrać definicję wbudowanych ról RBAC, aby wyświetlić używane uprawnienia i utworzyć rolę niestandardową na podstawie fragmentów i kumulacji tych za pośrednictwem programu PowerShell.

- Ponieważ każdy członek roli db_owner bazy danych może zmienić ustawienia zabezpieczeń, takie jak przezroczyste szyfrowanie danych (TDE), lub zmienić SLO, członkostwo to powinno być przyznawane z ostrożnością. Istnieje jednak wiele zadań, które wymagają db_owner uprawnień. Zadanie takie jak zmiana dowolnego ustawienia bazy danych, takiego jak zmiana opcji bazy danych. Inspekcja odgrywa kluczową rolę w każdym rozwiązaniu.

- Nie można ograniczyć uprawnień db_owner i w związku z tym uniemożliwić kontu administracyjnemu wyświetlanie danych użytkownika. Jeśli w bazie danych znajdują się bardzo poufne dane, zawsze zaszyfrowane może służyć do bezpiecznego zapobiegania db_owners lub innych DBA z wyświetlaniem go.

> [!NOTE]
> Osiągnięcie separacji obowiązków (SoD) jest wyzwaniem dla zadań związanych z zabezpieczeniami lub rozwiązywania problemów. Inne obszary, takie jak rozwój i role użytkowników końcowych są łatwiejsze do segregować. Większość kontroli związanych z zgodnością zezwala na korzystanie z alternatywnych funkcji sterowania, takich jak Inspekcja, gdy inne rozwiązania nie są praktyczne.

Dla czytelników, którzy chcą zagłębić się w SoD, zalecamy następujące zasoby: 

- Dla bazy danych SQL platformy Azure:  
  - [Kontrolowanie i udzielanie dostępu do bazy danych SQL i magazynu danych SQL](sql-database-manage-logins.md)
  - [Separacja obowiązków silnika dla dewelopera aplikacji](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Rozdzielenie obowiązków w programie SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Rejestrowanie procedur składowanych w programie SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- W przypadku zarządzania zasobami platformy Azure:
  - [Wbudowane role platformy Azure](../role-based-access-control/built-in-roles.md) 
  - [Niestandardowe role dla zasobów platformy Azure](../role-based-access-control/custom-roles.md)
  - [Korzystanie z uprzywilejowanego zarządzania tożsamościami usługi Azure AD w celu uzyskania podwyższonego poziomu dostępu](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Regularne przeglądy kodu

> Wymienione w: PCI: 6.3.2, SOC: SDL-3 

Separacja obowiązków nie jest ograniczona do danych w bazie danych, ale zawiera kod aplikacji. Złośliwy kod może potencjalnie obejść mechanizmy kontroli zabezpieczeń. Przed wdrożeniem kodu niestandardowego w procesach produkcyjnych należy przejrzeć, co jest wdrażane.

**Jak wdrożyć**:

- Użyj narzędzia bazy danych, takiego jak usługa Azure Data Studio, które obsługuje kontrolę źródła. 

- Zaimplementuj proces wdrażania kodu segregowanego.

- Przed zatwierdzeniem do głównej gałęzi, osoba (inna niż autor samego kodu) musi sprawdzić kod pod kątem potencjalnego podniesienia poziomu uprawnień ryzyka, jak również złośliwych modyfikacji danych w celu ochrony przed oszustwami i nieautoryzowanym dostępem. Można to zrobić za pomocą mechanizmów kontroli źródła.

**Najważniejsze wskazówki:**

- Standaryzacja: Pomaga zaimplementować standardową procedurę, która ma być przestrzegana dla wszelkich aktualizacji kodu. 

- Ocena luk w zabezpieczeniach zawiera reguły, które sprawdzają nadmierne uprawnienia, użycie starych algorytmów szyfrowania i inne problemy z zabezpieczeniami w schemacie bazy danych. 

- Dalsze kontrole można wykonać w środowisku kontroli jakości lub testowej przy użyciu zaawansowanej ochrony przed zagrożeniami, która skanuje w poszukiwaniu kodu, który jest podatny na iniekcję SQL.

- Przykłady, na które należy zwrócić uwagę: 
  - Tworzenie użytkownika lub zmienianie ustawień zabezpieczeń z poziomu automatycznego wdrożenia aktualizacji kodu SQL. 
  - Procedura składowana, która, w zależności od dostarczonych parametrów, aktualizuje wartość pieniężną w komórce w sposób niezgodny. 

- Upewnij się, że osoba przeprowadzająca przegląd jest osobą inną niż autor kodu źródłowego i posiadającą wiedzę na temat przeglądów kodu i bezpiecznego kodowania.

- Pamiętaj, aby znać wszystkie źródła zmian kodu. Kod może być w skryptach T-SQL. Mogą to być polecenia ad hoc, które mają być wykonywane lub wdrażane w formach widoków, funkcji, wyzwalaczy i procedur przechowywanych. Może być częścią definicji zadania agenta SQL (kroki). Może być również wykonywany z poziomu pakietów SSIS, usługi Azure Data Factory i innych usług.

## <a name="data-protection"></a>Ochrona danych

Ochrona danych to zestaw możliwości ochrony ważnych informacji przed naruszeniem zabezpieczeń przez szyfrowanie lub zaciemnienie.

> [!NOTE]
> Firma Microsoft potwierdza usługę Azure SQL Database jako zgodność z normą FIPS 140-2 Level 1. Odbywa się to po sprawdzeniu ścisłego użycia algorytmów akceptowalnych poziomu 1 fips 1 i fips 140-2 Poziom 1 zweryfikowanych wystąpień tych algorytmów, w tym spójności z wymaganymi długościami kluczy, zarządzanie kluczami, generowanie kluczy i przechowywanie kluczy. To zaświadczenie ma na celu umożliwienie naszym klientom reagowania na potrzebę lub wymóg stosowania zatwierdzonych wystąpień FIPS 140-2 Poziomu 1 w przetwarzaniu danych lub dostarczaniu systemów lub aplikacji. Definiujemy terminy "zgodność z poziomem 1 FIPS 1" i "zgodność z poziomem 1 FIPS 140-2" użyte w powyższym oświadczeniu w celu wykazania zamierzonego zastosowania do rządu USA i Kanady innego terminu "FIPS 140-2 Level 1 validated". 


### <a name="encrypt-data-in-transit"></a>Szyfrowanie danych podczas przesyłania

> Wspomniano w: OSA Practice #6, ISO Control Family: Kryptografia

Chroni dane podczas przenoszenia danych między klientem a serwerem. Zobacz: [Zabezpieczenia sieciowe](#network-security).

### <a name="encrypt-data-at-rest"></a>Szyfrowanie danych magazynowanych

> Wspomniano w: OSA Practice #6, ISO Control Family: Kryptografia

Szyfrowanie w spoczynku jest kryptograficzną ochroną danych, gdy są utrwalone w plikach bazy danych, dziennika i kopii zapasowej.

**Jak wdrożyć**:

- [Szyfrowanie przezroczystej bazy danych (TDE)](transparent-data-encryption-azure-sql.md) z kluczami zarządzanymi usługami są domyślnie włączone dla wszystkich baz danych utworzonych po 2017 r. w usłudze Azure SQL Database.
- W wystąpieniu zarządzanym, jeśli baza danych jest tworzona na podstawie operacji przywracania przy użyciu serwera lokalnego, ustawienie TDE oryginalnej bazy danych zostanie honorowane. Jeśli oryginalna baza danych nie ma włączonego TDE, zaleca się ręczne włączenie TDE dla wystąpienia zarządzanego.

**Najważniejsze wskazówki:**

- Nie przechowuj danych, które wymagają szyfrowania w spoczynku w głównej bazie danych. Główna baza danych nie może być szyfrowana za pomocą TDE.

- Użyj kluczy zarządzanych przez klienta w usłudze Azure Key Vault, jeśli potrzebujesz większej przezroczystości i szczegółowej kontroli nad ochroną TDE. Usługa Azure Key Vault umożliwia możliwość odwołania uprawnień w dowolnym momencie, aby uniemożliwić dostęp do bazy danych. Możesz centralnie zarządzać ochraniaczami TDE wraz z innymi kluczami lub obracać ochronę TDE zgodnie z własnym harmonogramem przy użyciu usługi Azure Key Vault.

- Jeśli używasz kluczy zarządzanych przez klienta w usłudze Azure Key Vault, postępuj zgodnie z artykułami, [Wskazówki dotyczące konfigurowania TDE za pomocą usługi Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) i jak [skonfigurować geo-DR za pomocą usługi Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Ochrona poufnych danych w użyciu przed zaawansowanymi, nieautoryzowanymi użytkownikami

Dane w użyciu to dane przechowywane w pamięci systemu bazy danych podczas wykonywania zapytań SQL. Jeśli baza danych przechowuje poufne dane, organizacja może być wymagana, aby upewnić się, że użytkownicy o wysokich uprawnieniach nie mogą wyświetlać poufnych danych w bazie danych. Użytkownicy o wysokich uprawnieniach, takich jak operatorzy firmy Microsoft lub dbA w organizacji powinny być w stanie zarządzać bazą danych, ale uniemożliwić wyświetlanie i potencjalnie exfiltracji poufnych danych z pamięci procesu programu SQL Server lub przez kwerendy bazy danych.

Zasady, które określają, które dane są poufne i czy poufne dane muszą być szyfrowane w pamięci i nie dostępne dla administratorów w postaci zwykłego tekstu, są specyficzne dla organizacji i przepisów dotyczących zgodności, których należy przestrzegać. Zobacz powiązane wymagania: [Identyfikowanie i oznaczanie poufnych danych](#identify-and-tag-sensitive-data).

**Jak wdrożyć**:

- Użyj [zawsze zaszyfrowane,](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) aby upewnić się, że poufne dane nie są widoczne w postaci zwykłego tekstu w usłudze Azure SQL Database, nawet w pamięci/w użyciu. Zawsze szyfrowane chroni dane przed administratorami baz danych (DBA) i administratorami chmury (lub złych aktorów, którzy mogą podszywać się pod użytkowników o wysokich uprawnieniach, ale nieautoryzowanych) i daje większą kontrolę nad tym, kto może uzyskać dostęp do danych.

**Najważniejsze wskazówki:**

- Zawsze zaszyfrowane nie zastępuje szyfrowania danych w spoczynku (TDE) lub podczas przesyłania (SSL/TLS). Zawsze zaszyfrowane nie powinny być używane dla danych niechuwawionych, aby zminimalizować wpływ na wydajność i funkcjonalność. Używanie funkcji Zawsze szyfrowane w połączeniu z TDE i transport layer security (TLS) jest zalecane do kompleksowej ochrony danych w spoczynku, podczas przesyłania i użytkowania. 

- Przed wdrożeniem zawsze zaszyfrowanej w produkcyjnej bazie danych należy ocenić wpływ szyfrowania zidentyfikowanych kolumn poufnych danych. Ogólnie rzecz biorąc, zawsze szyfrowane zmniejsza funkcjonalność zapytań na zaszyfrowanych kolumnach i ma inne ograniczenia, wymienione w [Zawsze zaszyfrowane - Szczegóły funkcji](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). W związku z tym może być konieczne rearchitect aplikacji do ponownego zaimplementowania funkcji, kwerenda nie obsługuje, po stronie klienta i refaktoryzacji schematu bazy danych, w tym definicje procedur składowanych, funkcje, widoki i wyzwalacze. Istniejące aplikacje mogą nie działać z zaszyfrowanymi kolumnami, jeśli nie są zgodne z ograniczeniami i ograniczeniami zawsze zaszyfrowanych. Podczas gdy ekosystem narzędzi, produktów i usług firmy Microsoft obsługujących zawsze szyfrowane rośnie, wiele z nich nie działa z zaszyfrowanymi kolumnami. Szyfrowanie kolumny może również mieć wpływ na wydajność kwerendy, w zależności od charakterystyki obciążenia. 

- Zarządzaj zawsze zaszyfrowanymi kluczami z separacją ról, jeśli używasz zawsze zaszyfrowanych do ochrony danych przed złośliwymi administratorami baz danych. W separacji ról administrator zabezpieczeń tworzy klucze fizyczne. Dba tworzy klucz główny kolumny i kolumny szyfrowania klucz obiektów metadanych, opisujące klucze fizyczne, w bazie danych. Podczas tego procesu administrator zabezpieczeń nie potrzebuje dostępu do bazy danych, a dba nie potrzebuje dostępu do kluczy fizycznych w postaci zwykłego tekstu. 
  - Zobacz artykuł [Zarządzanie kluczami z separacji ról, aby](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) uzyskać szczegółowe informacje. 

- Przechowuj klucze główne kolumn w usłudze Azure Key Vault, aby ułatwić zarządzanie. Unikaj używania Magazynu certyfikatów systemu Windows (i ogólnie rozwiązań magazynu kluczy rozproszonych, w przeciwieństwie do rozwiązań do zarządzania kluczami centralnymi), które utrudniają zarządzanie kluczami. 

- Zastanów się dokładnie nad kompromisami przy użyciu wielu kluczy (klucz główny kolumny lub klucze szyfrowania kolumn). Zachowaj małą liczbę kluczy, aby zmniejszyć koszty zarządzania kluczami. Jeden klucz główny kolumny i jeden klucz szyfrowania kolumny na bazę danych jest zazwyczaj wystarczające w środowiskach w stanie stacjonarnym (nie w środku rotacji klucza). Dodatkowe klucze mogą być potrzebne, jeśli masz różne grupy użytkowników, z których każda używa różnych kluczy i uzyskuje dostęp do różnych danych.  

- Obracanie kluczy głównych kolumn zgodnie z wymaganiami dotyczącymi zgodności. Jeśli konieczne jest również obrócenie kluczy szyfrowania kolumn, należy rozważyć użycie szyfrowania online w celu zminimalizowania przestojów aplikacji. 
  - Zobacz artykuł, [Zagadnienia dotyczące wydajności i dostępności](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Użyj szyfrowania deterministycznego, jeśli obliczenia (równość) danych muszą być obsługiwane. W przeciwnym razie należy użyć szyfrowania losowego. Należy unikać szyfrowania deterministycznego dla zestawów danych o niskiej entropii lub zestawów danych z publicznie znaną dystrybucją. 

- Jeśli obawiasz się legalnego uzyskiwania dostępu do danych przez inne firmy bez twojej zgody, upewnij się, że wszystkie aplikacje i narzędzia, które mają dostęp do kluczy i danych w postaci zwykłego tekstu, działają poza usługą Microsoft Azure Cloud. Bez dostępu do kluczy, strona trzecia nie będzie miała możliwości odszyfrowania danych, chyba że ominie szyfrowanie.

- Zawsze zaszyfrowane nie obsługuje łatwe udzielanie tymczasowego dostępu do kluczy (i chronionych danych). Na przykład, jeśli trzeba udostępnić klucze z DBA, aby umożliwić DBA zrobić kilka operacji oczyszczania na poufnych i zaszyfrowanych danych. Jedynym sposobem na niezawodność odwołać dostęp do danych z DBA będzie obrócić zarówno klucze szyfrowania kolumny i klucze główne kolumny ochrony danych, co jest kosztowną operacją. 

- Aby uzyskać dostęp do wartości zwykłego tekstu w zaszyfrowanych kolumnach, użytkownik musi mieć dostęp do cmk, który chroni kolumny, który jest skonfigurowany w magazynie kluczy gospodarstwa CMK. Użytkownik musi również mieć **wyświetl dowolną definicję klucza głównego kolumny** i wyświetlić wszystkie uprawnienia do bazy danych **definicji klucza szyfrowania kolumny.**

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Kontroluj dostęp użytkowników aplikacji do poufnych danych za pomocą szyfrowania

Szyfrowanie może służyć jako sposób, aby upewnić się, że tylko określone użytkownicy aplikacji, którzy mają dostęp do kluczy kryptograficznych mogą wyświetlać lub aktualizować dane.

**Jak wdrożyć**:

- Użyj szyfrowania na poziomie komórki (CLE). Zobacz [artykuł, Szyfruj kolumnę danych, aby](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) uzyskać szczegółowe informacje. 
- Użyj zawsze zaszyfrowane, ale należy pamiętać o jego ograniczenia. Ograniczenia są wymienione poniżej.

**Najważniejsze wskazówki**

W przypadku korzystania z CLE:

- Kontroluj dostęp do kluczy za pomocą uprawnień i ról SQL. 

- Do szyfrowania danych należy używać AES (zalecane 256 AES). Algorytmy, takie jak RC4, DES i TripleDES, są przestarzałe i nie powinny być używane ze względu na znane luki w zabezpieczeniach. 

- Chroń klucze symetryczne za pomocą asymetrycznych kluczy/certyfikatów (nie haseł), aby uniknąć używania 3DES. 

- Należy zachować ostrożność podczas migracji bazy danych przy użyciu szyfrowania na poziomie komórki za pomocą eksportu/importu (plików bacpac). 
  - Zobacz artykuł Zalecenia [dotyczące używania szyfrowania na poziomie komórki w usłudze Azure SQL Database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) dotyczące zapobiegania utracie kluczy podczas migracji danych oraz innych wskazówek dotyczących najlepszych rozwiązań.

Należy pamiętać, że always encrypted jest przeznaczony przede wszystkim do ochrony poufnych danych używanych przez użytkowników o wysokich uprawnieniach usługi Azure SQL Database (operatorów chmury, dbA) - zobacz [Ochrona poufnych danych w użyciu przed wysokiej uprzywilejowanych, nieautoryzowanych użytkowników](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Należy pamiętać o następujących wyzwaniach podczas korzystania zawsze szyfrowane do ochrony danych przed użytkownikami aplikacji:

- Domyślnie wszystkie sterowniki klienckie firmy Microsoft obsługujące zawsze szyfrowane utrzymują globalną (po jednym na aplikację) pamięć podręczną kluczy szyfrowania kolumn. Gdy sterownik klienta uzyskuje klucz szyfrowania kolumny w postaci zwykłego tekstu, kontaktując się z magazynem kluczy zawierającym klucz główny kolumny, klucz szyfrowania kolumny zwykłego tekstu jest buforowany. To sprawia, że izolowanie danych od użytkowników aplikacji dla wielu użytkowników jest trudne. Jeśli aplikacja podszywa się pod użytkowników końcowych podczas interakcji z magazynem kluczy (np. usługa Azure Key Vault), po tym jak zapytanie użytkownika wypełnia pamięć podręczną kluczem szyfrowania kolumny, kolejne zapytanie, które wymaga tego samego klucza, ale jest wyzwalane przez innego użytkownika, użyje buforowanego klucza. Sterownik nie zadzwoni do magazynu kluczy i nie sprawdzi, czy drugi użytkownik ma uprawnienia dostępu do klucza szyfrowania kolumny. W rezultacie użytkownik będzie mógł zobaczyć zaszyfrowane dane, nawet jeśli użytkownik nie ma dostępu do kluczy. Aby osiągnąć izolację użytkowników w aplikacji dla wielu użytkowników, można wyłączyć buforowanie kluczy szyfrowania kolumn. Wyłączenie buforowania spowoduje dodatkowe koszty ogólne wydajności, ponieważ sterownik będzie musiał skontaktować się z magazynem kluczy dla każdej operacji szyfrowania lub odszyfrowywania danych.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Ochrona danych przed nieautoryzowanym przeglądaniem przez użytkowników aplikacji przy jednoczesnym zachowaniu formatu danych
Inną techniką zapobiegania nieautoryzowanym użytkownikom przeglądania danych jest zaciemnianie lub maskowanie danych przy jednoczesnym zachowaniu typów i formatów danych, aby zapewnić, że aplikacje użytkownika mogą nadal obsługiwać i wyświetlać dane.

**Jak wdrożyć**:

- Użyj [dynamicznego maskowania danych,](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) aby zaciemnić kolumny tabeli.

> [!NOTE]
> Zawsze szyfrowane nie działa z dynamicznego maskowania danych. Nie jest możliwe szyfrowanie i maskowanie tej samej kolumny, co oznacza, że należy nadać priorytet ochronie danych w użyciu w porównaniu z maskowanie danych dla użytkowników aplikacji za pomocą dynamicznego maskowania danych.

**Najważniejsze wskazówki:**

> [!NOTE]
> Dynamiczne maskowanie danych nie może służyć do ochrony danych przed użytkownikami o wysokich uprawnieniach. Zasady maskowania nie mają zastosowania do użytkowników z dostępem administracyjnym, takimi jak db_owner.

- Nie zezwalaj użytkownikom aplikacji na uruchamianie zapytań ad hoc (ponieważ mogą one być w stanie obejść dynamiczne maskowanie danych).  
  - Zobacz artykuł [Pomijając maskowanie przy użyciu wnioskowania lub technik brute-force,](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) aby uzyskać szczegółowe informacje.  

- Użyj zasad kontroli dostępu (za pośrednictwem uprawnień SQL, ról, RLS), aby ograniczyć uprawnienia użytkownika do wprowadzania aktualizacji w zamaskowanych kolumnach. Utworzenie maski w kolumnie nie uniemożliwia aktualizacji tej kolumny. Użytkownicy, którzy otrzymują zamaskowane dane podczas wykonywania zapytań o zamaskowane kolumny, mogą zaktualizować dane, jeśli mają uprawnienia do zapisu.    

-  Dynamiczne maskowanie danych nie zachowuje właściwości statystycznych zamaskowanych wartości. Może to mieć wpływ na wyniki kwerendy (na przykład kwerendy zawierające filtrowanie predykatów lub sprzężenia na zamaskowanych danych).

## <a name="network-security"></a>Bezpieczeństwo sieci
Zabezpieczenia sieciowe odnosi się do kontroli dostępu i najlepszych rozwiązań w celu zabezpieczenia danych podczas przesyłania do usługi Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Konfigurowanie klienta do bezpiecznego łączenia się z bazą danych SQL usługi Azure 
Najlepsze rozwiązania dotyczące zapobiegania łączeniu się z usługą Azure SQL Database na komputerach klienckich i aplikacjach ze znanymi lukami w zabezpieczeniach (na przykład przy użyciu starszych protokołów TLS i zestawów szyfrowania).

**Jak wdrożyć**:

- Upewnij się, że maszyny klienckie łączące się z usługą Azure SQL Database korzystają z [usługi Transport Layer Security (TLS).](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)

**Najważniejsze wskazówki:**

- Konfigurowanie wszystkich aplikacji i narzędzi do łączenia się z bazą danych SQL z włączoną obsługą szyfrowania 
  - Encrypt = On, TrustServerCertificate = Off (lub równoważne ze sterownikami innych firm). 

- Jeśli aplikacja używa sterownika, który nie obsługuje protokołu TLS lub obsługuje starszą wersję protokołu TLS, w miarę możliwości zastąp go. Jeśli nie jest to możliwe, należy dokładnie ocenić zagrożenia bezpieczeństwa. 

- Zmniejsz liczbę wektorów ataków dzięki lukom w zabezpieczeniach w ustawieniach rejestru usługi SSL 2.0, SSL 3.0, TLS 1.0 i TLS 1.1, wyłączając je na komputerach klienckich łączących się z usługą Azure SQL Database na [ustawienia rejestru TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10) 

- Sprawdź pakiety szyfrowania dostępne na [kliencie: Pakiety szyfrowania w TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). W szczególności wyłącz 3DES na [konfigurowanie kolejności pakietu szyfrowania TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- W przypadku usługi Azure SQL Database szyfrowanie jest wymuszane dla typów połączeń proxy i przekierowania. Jeśli używasz wystąpienia zarządzanego, użyj typu połączenia **serwera proxy** (domyślnie), ponieważ wymusza to szyfrowanie od strony serwera. Typ połączenia **przekierowania** obecnie nie obsługuje wymuszania szyfrowania i jest dostępny tylko w prywatnych połączeniach IP. 

- Aby uzyskać więcej informacji, zobacz [Architektura łączności SQL platformy Azure — zasady połączenia](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>Minimalizowanie powierzchni ataku
Zminimalizuj liczbę funkcji, które mogą zostać zaatakowane przez złośliwego użytkownika. Implementowanie kontroli dostępu do sieci dla usługi Azure SQL Database.

> Wspomniano w: OSA Practice #5

**Jak wdrożyć**:

Na serwerze usługi Azure SQL Database (zawierającym pojedynczą bazę danych lub pule elastyczne):
- Ustaw zezwalaj na dostęp do usług platformy Azure na OFF.

- Użyj punktów końcowych usługi sieci wirtualnej i reguł zapory sieci wirtualnej.

- Użyj łącza prywatnego (wersja zapoznawcza).

W wystąpieniu zarządzanym:
- Postępuj zgodnie z wytycznymi zawartymi w [wymaganiach sieciowych](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Najważniejsze wskazówki:**

- Ograniczanie dostępu do usługi Azure SQL Database przez połączenie z prywatnym punktem końcowym (na przykład przy użyciu ścieżki danych prywatnych): 
  - Wystąpienie zarządzane może być izolowane wewnątrz sieci wirtualnej, aby zapobiec dostęp zewnętrzny. Aplikacje i narzędzia, które znajdują się w tej samej lub równorzędnej sieci wirtualnej w tym samym regionie może uzyskać do niej bezpośredni dostęp. Aplikacje i narzędzia, które znajdują się w różnych regionach, mogą używać połączenia sieci wirtualnej do sieci wirtualnej lub komunikacji równorzędnej obwodu usługi ExpressRoute w celu nawiązania połączenia. Klient powinien używać sieciowych grup zabezpieczeń (NSG) do ograniczania dostępu przez port 1433 tylko do zasobów, które wymagają dostępu do wystąpienia zarządzanego 
  - W przypadku serwera bazy danych SQL (zawierającego pojedyncze bazy danych lub pule elastyczne) użyj funkcji [Łącze prywatne,](sql-database-private-endpoint-overview.md) która udostępnia dedykowany prywatny adres IP dla serwera bazy danych SQL wewnątrz sieci wirtualnej. Można również użyć [punktów końcowych usługi sieci wirtualnej z regułami zapory sieci wirtualnej,](sql-database-vnet-service-endpoint-rule-overview.md) aby ograniczyć dostęp do serwerów bazy danych SQL.
  - Użytkownicy mobilni powinni używać połączeń sieci VPN typu punkt-lokacja do łączenia się za pośrednictwem ścieżki danych.
  - Użytkownicy połączeni z siecią lokalną powinni używać połączenia sieci VPN lokacja lokacja lokacja lub usługi ExpressRoute do łączenia się za pośrednictwem ścieżki danych.

- Dostęp do usługi Azure SQL Database można uzyskać, łącząc się z publicznym punktem końcowym (na przykład przy użyciu ścieżki danych publicznych). Należy wziąć pod uwagę następujące najlepsze praktyki: 
  - W przypadku serwera bazy danych SQL należy ograniczyć dostęp do tylko autoryzowanych adresów IP za pomocą [reguł zapory IP.](sql-database-firewall-configure.md)
  - W przypadku wystąpienia zarządzanego użyj sieciowych grup zabezpieczeń (NSG), aby ograniczyć dostęp przez port 3342 tylko do wymaganych zasobów. Aby uzyskać więcej informacji, zobacz [Bezpieczne korzystanie z wystąpienia zarządzanego usługi Azure SQL Database z publicznymi punktami końcowymi.](sql-database-managed-instance-public-endpoint-securely.md) 

> [!NOTE]
> Publiczny punkt końcowy wystąpienia zarządzanego nie jest domyślnie włączony i musi być jawnie włączony. Jeśli zasady firmy nie zezwalają na korzystanie z publicznych punktów końcowych, użyj [zasady platformy Azure,](../governance/policy/overview.md) aby zapobiec włączaniu publicznych punktów końcowych w pierwszej kolejności.

- Konfigurowanie składników sieci platformy Azure: 
  - Postępuj zgodnie z [najlepszymi rozwiązaniami platformy Azure w zakresie zabezpieczeń sieci](../security/fundamentals/network-best-practices.md).
  - Planowanie konfiguracji sieci wirtualnej (VNet) zgodnie z najlepszymi rozwiązaniami opisanymi w [usłudze Azure Virtual Network często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)](../virtual-network/virtual-networks-faq.md) i planowanie. 
  - Segment sieci wirtualnej do wielu podsieci i przypisać zasoby dla podobnej roli do tej samej podsieci (na przykład front-end vs zasobów zaplecza).
  - Użyj [sieciowych grup zabezpieczeń (NSG),](../virtual-network/security-overview.md) aby kontrolować ruch między podsieciami wewnątrz granicy sieci wirtualnej platformy Azure.
  - Włącz [usługę Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) dla subskrypcji, aby monitorować przychodzący i wychodzący ruch sieciowy.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Konfigurowanie usługi Power BI dla bezpiecznych połączeń z bazą danych SQL usługi Azure

**Najważniejsze wskazówki:**

- W programie Power BI Desktop używaj prywatnej ścieżki danych, gdy tylko jest to możliwe. 

- Upewnij się, że program Power BI Desktop łączy się przy użyciu protokołu TLS1.2, ustawiając klucz rejestru na komputerze klienckim zgodnie z ustawieniami rejestru [TLS (Transport Layer Security).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Ograniczanie dostępu do danych dla określonych użytkowników za pośrednictwem [zabezpieczeń na poziomie wiersza (RLS) w usłudze Power BI](https://docs.microsoft.com/power-bi/service-admin-rls). 

- W przypadku usługi Power BI service należy użyć [lokalnej bramy danych,](https://docs.microsoft.com/power-bi/service-gateway-onprem)mając na uwadze [ograniczenia i uwagi.](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Konfigurowanie usługi App Service dla bezpiecznych połączeń z bazą danych SQL usługi Azure

**Najważniejsze wskazówki:**

- W przypadku prostej aplikacji sieci Web łączenie się za pośrednictwem publicznego punktu końcowego wymaga ustawienia **Zezwalaj na usługi platformy Azure** na WŁĄCZONE. 

- [Zintegruj aplikację z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md) dla łączności ścieżki danych prywatnych z wystąpieniem zarządzanym. Opcjonalnie można również wdrożyć aplikację sieci Web ze [środowiskami usługi App Service (ASE).](../app-service/environment/intro.md) 

- W przypadku aplikacji sieci Web ze zintegrowaną aplikacją sieci Web ASE lub sieci wirtualnej łączącą się z bazą danych na serwerze bazy danych SQL można użyć [punktów końcowych usługi sieci wirtualnej i reguł zapory sieci wirtualnej,](sql-database-vnet-service-endpoint-rule-overview.md) aby ograniczyć dostęp z określonej sieci wirtualnej i podsieci. Następnie ustaw **zezwalaj na wyłowianie usług platformy Azure.** Można również połączyć ASE z wystąpieniem zarządzanym za pomocą prywatnej ścieżki danych.  

- Upewnij się, że aplikacja sieci Web jest skonfigurowana zgodnie z artykułem, [Najważniejsze wskazówki dotyczące zabezpieczania aplikacji sieci Web paaS i aplikacji mobilnych przy użyciu usługi Azure App Service](../security/security-paas-applications-using-app-services.md). 

- Zainstaluj [Zaporę aplikacji sieci Web (WAF),](../application-gateway/waf-overview.md) aby chronić aplikację internetową przed typowymi exploitami i lukami w zabezpieczeniach.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Konfigurowanie hostingu maszyn wirtualnych platformy Azure w celu zapewnienia bezpiecznych połączeń z bazą danych SQL platformy Azure

**Najważniejsze wskazówki:**

- Użyj kombinacji zezwalaj i odmów reguły na sieciowej grupy wirtualnej platformy Azure, aby kontrolować, które regiony mogą być dostępne z maszyny Wirtualnej.

- Upewnij się, że maszyna wirtualna jest skonfigurowana zgodnie z artykułem, [Najważniejsze wskazówki dotyczące zabezpieczeń dla obciążeń IaaS na platformie Azure.](../security/azure-security-iaas.md)

- Upewnij się, że wszystkie maszyny wirtualne są skojarzone z określoną siecią wirtualną i podsiecią. 

- Oceń, czy potrzebujesz domyślnej trasy 0.0.0.0/Internet zgodnie z wytycznymi dotyczącymi [wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Jeśli tak – na przykład podsieć front-end — zachowaj trasę domyślną.
  - Jeśli nie — na przykład warstwa środkowa lub podsieć zaplecza — włącz tunelowanie siłowe, aby żaden ruch nie przechodził przez Internet, aby dotrzeć do lokalnego (aka cross-premises). 

- Zaimplementuj [opcjonalne trasy domyślne,](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) jeśli używasz komunikacji równorzędnej lub łączenia się z lokalnymi. 

- Zaimplementuj [trasy zdefiniowane przez użytkownika,](../virtual-network/virtual-networks-udr-overview.md#user-defined) jeśli chcesz wysłać cały ruch w sieci wirtualnej do wirtualnego urządzenia sieciowego w celu inspekcji pakietów. 

- Użyj [punktów końcowych usługi sieci wirtualnej,](sql-database-vnet-service-endpoint-rule-overview.md) aby zapewnić bezpieczny dostęp do usług PaaS, takich jak usługa Azure Storage, za pośrednictwem sieci szkieletowej platformy Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Ochrona przed atakami DDoS (Distributed Denial of Service)
Ataki typu "DDoS) typu "distributed Denial of Service" to próby wysłania przez złośliwego użytkownika zalewu ruchu sieciowego do bazy danych SQL usługi Azure w celu przytłaczania infrastruktury platformy Azure i spowodowania odrzucenia prawidłowych loginów i obciążenia.

> Wspomniano w: OSA Practice #9

**Jak wdrożyć**:

Ochrona DDoS jest automatycznie włączana jako część platformy Azure. Obejmuje ono zawsze włączone monitorowanie ruchu i łagodzenie ataków na poziomie sieci na publiczne punkty końcowe. 

- Usługa [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) służy do monitorowania publicznych adresów IP skojarzonych z zasobami wdrożonymi w sieciach wirtualnych.

- Za pomocą [zaawansowanej ochrony przed zagrożeniami dla usługi Azure SQL Database](sql-database-threat-detection-overview.md) do wykrywania ataków typu "odmowa usługi" (DoS) na bazy danych.

**Najważniejsze wskazówki:**

- Postępuj zgodnie z praktykami opisanymi w [minimalizowanie powierzchni ataku](#minimize-attack-surface) pomaga zminimalizować zagrożenia atakami DDoS. 

- Alert o **poświadczeniach zaawansowaną ochronę przed zagrożeniami Brute wymusza poświadczeń SQL** pomaga wykryć ataki siłowe. W niektórych przypadkach alert może nawet odróżnić obciążenia testów penetracyjnych. 

- W przypadku aplikacji hostingowych platformy Azure obsługujących aplikacje łączące się z bazą danych SQL: 
  - Postępuj zgodnie z zaleceniami, aby ograniczyć dostęp za pośrednictwem punktów końcowych z dostępem do Internetu w usłudze Azure Security Center. 
  - Użyj zestawów skalowania maszyny wirtualnej, aby uruchomić wiele wystąpień aplikacji na maszynach wirtualnych platformy Azure. 
  - Wyłącz RDP i SSH z Internetu, aby zapobiec brutalnemu atakowi sił. 

## <a name="monitoring-logging-and-auditing"></a>Monitorowanie, rejestrowanie i inspekcja  
Ta sekcja odnosi się do funkcji ułatwiających wykrywanie nietypowych działań wskazujących na nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Opisano w nim również najlepsze rozwiązania dotyczące konfigurowania inspekcji bazy danych w celu śledzenia i przechwytywania zdarzeń bazy danych.

### <a name="protect-databases-against-attacks"></a>Ochrona baz danych przed atakami 
Zaawansowana ochrona przed zagrożeniami umożliwia wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń dotyczące nietypowych działań.

**Jak wdrożyć**:

- Za pomocą [zaawansowanej ochrony przed zagrożeniami dla języka SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) można wykryć nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania, w tym:
  - Atak iniekcji SQL.
  - Kradzież/wyciek poświadczeń.
  - Nadużycie przywilejów.
  - Eksfiltracja danych.

**Najważniejsze wskazówki:**

- Skonfiguruj [zaawansowane zabezpieczenia danych (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) dla usługi Azure SQL Database dla określonego serwera bazy danych SQL lub wystąpienia zarządzanego. Można również skonfigurować usługi ADS dla wszystkich serwerów bazy danych SQL i wystąpień zarządzanych w ramach subskrypcji, przełączając się na [warstwę Azure Security Center Standard](../security-center/security-center-pricing.md). 

- Aby uzyskać pełne środowisko dochodzenia, zaleca się włączenie [inspekcji bazy danych SQL](sql-database-auditing.md). Dzięki inspekcji można śledzić zdarzenia bazy danych i zapisywać je w dzienniku inspekcji w obszarze roboczym usługi Azure Storage lub usługi Azure Log Analytics. 

### <a name="audit-critical-security-events"></a>Inspekcja krytycznych zdarzeń zabezpieczeń
Śledzenie zdarzeń bazy danych pomaga zrozumieć aktywność bazy danych. Możesz uzyskać wgląd w rozbieżności i anomalie, które mogą wskazywać na obawy biznesowe lub podejrzewane naruszenia zabezpieczeń. Umożliwia również i ułatwia przestrzeganie standardów zgodności. 

**Jak wdrożyć**:

- Włącz [inspekcję bazy danych SQL,](sql-database-auditing.md) aby śledzić zdarzenia bazy danych i zapisywać je w dzienniku inspekcji na koncie usługi Azure Storage, obszarze roboczym usługi Log Analytics (wersja zapoznawcza) lub centrum zdarzeń (wersja zapoznawcza). 

- Dzienniki inspekcji można zapisać na koncie usługi Azure Storage, w obszarze roboczym usługi Log Analytics do użycia przez dzienniki usługi Azure Monitor lub w centrum zdarzeń do użycia przy użyciu centrum zdarzeń. Można skonfigurować dowolną kombinację tych opcji, a dzienniki inspekcji zostaną zapisane w każdej z nich. 

**Najważniejsze wskazówki:**

- Konfigurując [inspekcję bazy danych SQL](sql-database-auditing.md) na serwerze bazy danych w celu inspekcji zdarzeń, wszystkie istniejące i nowo utworzone bazy danych na tym serwerze zostaną poddane inspekcji.
- Domyślnie zasady inspekcji obejmują wszystkie akcje (zapytania, procedury przechowywane oraz pomyślne i nieudane logowania) w bazach danych, co może spowodować dużą liczbę dzienników inspekcji. Zaleca się, aby klienci [konfigurują inspekcję dla różnych typów akcji i grup akcji przy użyciu programu PowerShell](sql-database-auditing.md#subheading-7). Skonfigurowanie to pomoże kontrolować liczbę skontrolowanych akcji i zminimalizować ryzyko utraty zdarzeń. Konfiguracja inspekcji niestandardowej umożliwia klientom przechwytywanie tylko potrzebnych danych inspekcji.
- Dzienniki inspekcji mogą być używane bezpośrednio w [witrynie Azure portal](https://portal.azure.com/)lub z lokalizacji magazynu, który został skonfigurowany. 


> [!NOTE]
> Włączenie inspekcji usługi Log Analytics spowoduje poniesienie kosztów na podstawie stawek pozyskiwania. Należy pamiętać o skojarzonych kosztów przy użyciu tej [opcji](https://azure.microsoft.com/pricing/details/monitor/)lub należy rozważyć przechowywanie dzienników inspekcji na koncie magazynu platformy Azure. 

**Dalsze zasoby**:

- [Inspekcja bazy danych SQL](sql-database-auditing.md)
- [Inspekcja programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Bezpieczne dzienniki inspekcji
Ogranicz dostęp do konta magazynu, aby obsługiwać separację obowiązków i oddzielać dba od audytorów. 

**Jak wdrożyć**:

- Podczas zapisywania dzienników inspekcji w usłudze Azure Storage upewnij się, że dostęp do konta magazynu jest ograniczony do minimalnych zasad zabezpieczeń. Kontroluj, kto ma dostęp do konta magazynu.
    - Aby uzyskać więcej informacji, zobacz [Autoryzowanie dostępu do usługi Azure Storage](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Najważniejsze wskazówki:**

- Kontrolowanie dostępu do celu inspekcji jest kluczową koncepcją w oddzielaniu DBA od audytorów. 

- Podczas inspekcji dostępu do poufnych danych należy rozważyć zabezpieczenie danych za pomocą szyfrowania danych, aby uniknąć wycieku informacji do audytora. Aby uzyskać więcej informacji, zobacz sekcję [Ochrona poufnych danych używanych przed użytkownikami o wysokich uprawnieniach i nieautoryzowanych](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

W tej sekcji opisano różne aspekty i najlepsze rozwiązania dotyczące zarządzania postawą zabezpieczeń baz danych. Zawiera najlepsze rozwiązania dotyczące zapewnienia, że bazy danych są skonfigurowane tak, aby spełniały standardy zabezpieczeń, odnajdywania oraz klasyfikowania i śledzenia dostępu do potencjalnie poufnych danych w bazach danych. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Upewnij się, że bazy danych są skonfigurowane tak, aby spełniały najlepsze rozwiązania w zakresie zabezpieczeń 

Proaktywnie zwiększ bezpieczeństwo bazy danych, wykrywając i korygując potencjalne luki w zabezpieczeniach bazy danych.

**Jak wdrożyć**:

- Włącz [program SQL Vulnerability Assessment](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) w celu skanowania bazy danych w poszukiwaniu problemów z zabezpieczeniami i automatycznego uruchamiania okresowo w bazach danych.

**Najważniejsze wskazówki:**

- Początkowo uruchom usługi VA w bazach danych i iteracji przez korygowanie kontroli w przypadku awarii, które sprzeciwiają się najlepsze rozwiązania dotyczące zabezpieczeń. Skonfiguruj linie bazowe dla akceptowalnych konfiguracji, dopóki skanowanie nie zostanie _czyste_lub wszystkie kontrole nie przeszły.  

- Skonfiguruj okresowe skanowanie cykliczne tak, aby były uruchamiane raz w tygodniu, i skonfiguruj odpowiednią osobę do otrzymywania zbiorczych wiadomości e-mail. 

- Przejrzyj podsumowanie VA po każdym cotygodniowym skanowaniu. W przypadku wykrycia luk w zabezpieczeniach należy ocenić dryf z poprzedniego wyniku skanowania i określić, czy należy rozwiązać tę kontrolę. Sprawdź, czy istnieje uzasadniony powód zmiany konfiguracji.   

- W stosownych przypadkach rozwiązuj kontrole i aktualizuj linie bazowe. Utwórz elementy biletu do rozwiązywania akcji i śledź je, dopóki nie zostaną rozwiązane. 

**Dalsze zasoby**:

- [Ocena luk w zabezpieczeniach SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Usługa oceny luk w zabezpieczeniach SQL pomaga zidentyfikować luki w zabezpieczeniach bazy danych](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identyfikowanie i oznaczanie poufnych danych 

Odnajduj kolumny, które potencjalnie zawierają poufne dane. To, co jest uważane za poufne dane, w dużej mierze zależy od klienta, regulacji zgodności itp., i musi zostać ocenione przez użytkowników odpowiedzialnych za te dane. Klasyfikuj kolumny, aby używały zaawansowanych scenariuszy inspekcji i ochrony opartych na czułości. 

**Jak wdrożyć**:

- [Odnajdowanie i klasyfikacja danych SQL umożliwia](sql-database-data-discovery-and-classification.md) odnajdowanie, klasyfikowanie, etykietowanie i ochronę poufnych danych w bazach danych. 
  - Służy do wyświetlania zaleceń dotyczących klasyfikacji utworzonych przez automatyczne odnajdowanie na pulpicie nawigacyjnym odnajdowania i klasyfikacji danych SQL. Zaakceptuj odpowiednie klasyfikacje, tak aby poufne dane były trwale oznaczane etykietami klasyfikacji. 
  - Ręcznie dodaj klasyfikacje dla wszelkich dodatkowych pól danych poufnych, które nie zostały wykryte przez zautomatyzowany mechanizm. 
- Aby uzyskać więcej informacji, zobacz [Odnajdywanie danych SQL & Klasyfikacja](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Najważniejsze wskazówki:**

- Regularnie monitoruj pulpit nawigacyjny klasyfikacji, aby uzyskać dokładną ocenę stanu klasyfikacji bazy danych. Raport o stanie klasyfikacji bazy danych można wyeksportować lub wydrukować w celu udostępnienia do celów zgodności i inspekcji.

- Ciągłe monitorowanie stanu zalecanych poufnych danych w ocenie luk w zabezpieczeniach SQL. Śledź regułę odnajdowania poufnych danych i identyfikuj wszelkie dryf w zalecanych kolumnach do klasyfikacji.  

- Użyj klasyfikacji w sposób dostosowany do konkretnych potrzeb organizacji. Dostosuj zasady ochrony informacji (etykiety wrażliwości, typy informacji, logika odnajdywania) w [zasadach ochrony informacji SQL](../security-center/security-center-info-protection-policy.md) w usłudze Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Śledź dostęp do poufnych danych 
Monitoruj, kto uzyskuje dostęp do poufnych danych i przechwytuj zapytania dotyczące poufnych danych w dziennikach inspekcji.

**Jak wdrożyć**:

- Użyj inspekcji SQL i klasyfikacji danych w połączeniu. 
  - W dzienniku [inspekcji bazy danych SQL](sql-database-auditing.md) można śledzić dostęp specjalnie do poufnych danych. Można również wyświetlić informacje, takie jak dane, które zostały uzyskiczone, a także jego etykiety czułości. Aby uzyskać więcej informacji, zobacz [Inspekcja dostępu do poufnych danych](sql-database-data-discovery-and-classification.md#subheading-3). 

**Najważniejsze wskazówki:**

- Zobacz najważniejsze wskazówki dotyczące sekcji Inspekcja i klasyfikacja danych: 
  - [Inspekcja krytycznych zdarzeń zabezpieczeń](#audit-critical-security-events) 
  - [Identyfikowanie i oznaczanie poufnych danych](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Wizualizuj stan zabezpieczeń i zgodności 

Użyj ujednoliconego systemu zarządzania zabezpieczeniami infrastruktury, który wzmacnia poziom zabezpieczeń centrów danych (w tym baz danych SQL). Wyświetl listę zaleceń dotyczących bezpieczeństwa baz danych i stanu zgodności.

**Jak wdrożyć**:

- Monitoruj zalecenia dotyczące zabezpieczeń związane z sql i aktywne zagrożenia w [usłudze Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Wspólne zagrożenia dla bezpieczeństwa i potencjalne środki zaradcze

W tej sekcji pomaga znaleźć środki bezpieczeństwa w celu ochrony przed niektórymi wektorami ataku. Oczekuje się, że większość środków zaradczych można osiągnąć, wykonując jedną lub więcej wytycznych dotyczących zabezpieczeń powyżej.

### <a name="security-threat-data-exfiltration"></a>Zagrożenie bezpieczeństwa: eksfiltracja danych

Eksfiltracja danych to nieautoryzowane kopiowanie, przesyłanie lub pobieranie danych z komputera lub serwera. Zobacz definicję [eksfiltracji danych](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipedii.

Łączenie się z serwerem usługi Azure SQL Database za pośrednictwem publicznego punktu końcowego stwarza ryzyko eksfiltracji danych, ponieważ wymaga od klientów otwierania zapór dla publicznych adresów IP.  

**Scenariusz 1:** Aplikacja na maszynie Wirtualnej platformy Azure łączy się z bazą danych na serwerze bazy danych SQL Azure. Nieuczciwych aktor dostaje dostęp do maszyny Wirtualnej i naraża ją na szwank. W tym scenariuszu eksfiltracji danych oznacza, że zewnętrzna jednostka przy użyciu nieuczciwych maszyn wirtualnych łączy się z bazą danych, kopiuje dane osobowe i przechowuje je w magazynie obiektów blob lub innej bazy danych SQL w innej subskrypcji.

**Scenariusz 2**: A Rouge DBA. Ten scenariusz jest często wywoływany przez klientów wrażliwych na bezpieczeństwo z branż regulowanych. W tym scenariuszu użytkownik o wysokich uprawnieniach może skopiować dane z usługi Azure SQL Database do innej subskrypcji nie jest kontrolowana przez właściciela danych.

**Potencjalne środki zaradcze:**

Obecnie usługa Azure SQL Database oferuje następujące techniki ograniczania zagrożeń związanych z eksfiltracją danych: 

- Użyj kombinacji zezwalaj i odmów reguły na sieciowej grupy wirtualnej platformy Azure, aby kontrolować, które regiony mogą być dostępne z maszyny Wirtualnej. 
- Jeśli używasz serwera usługi Azure SQL Database (zawierającego bazy danych singleton lub pule elastyczne), ustaw następujące opcje:
  - Zezwalaj na wyłowianie usług platformy Azure.
  - Zezwalaj tylko na ruch z podsieci zawierającej maszynę wirtualną platformy Azure, konfigurując regułę zapory sieci wirtualnej.
  - Korzystanie z [łącza prywatnego](sql-database-private-endpoint-overview.md)
- W przypadku wystąpienia zarządzanego przy użyciu prywatnego dostępu do adresu IP pierwszy problem eksfiltracji danych nieuczciwych maszyny Wirtualnej. Włącz funkcję delegowania podsieci w podsieci, aby automatycznie ustawić najbardziej restrykcyjne zasady w podsieci wystąpienia zarządzanego.
- Rogue DBA problem jest bardziej narażone z wystąpienia zarządzanego, ponieważ ma większą powierzchnię i wymagania sieciowe są widoczne dla klientów. Najlepszym środkiem zaradczym jest zastosowanie wszystkich praktyk w tym przewodniku zabezpieczeń, aby zapobiec scenariuszowi Rogue DBA w pierwszej kolejności (nie tylko w przypadku eksfiltracji danych). Zawsze zaszyfrowane jest jedną z metod ochrony poufnych danych przez szyfrowanie go i utrzymanie klucz niedostępny dla DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspekty bezpieczeństwa ciągłości działania i dostępności

Większość standardów zabezpieczeń adres dostępności danych pod względem ciągłości operacyjnej, osiągnięte przez wdrożenie nadmiarowości i możliwości awaryjnej, aby uniknąć pojedynczych punktów awarii. W przypadku scenariuszy awarii jest powszechną praktyką, aby zachować kopie zapasowe plików danych i dziennika.Poniższa sekcja zawiera omówienie wysokiego poziomu możliwości, które są wbudowane w platformie Azure. Zapewnia również dodatkowe opcje, które można skonfigurować w celu zaspokojenia określonych potrzeb: 

- Platforma Azure oferuje wbudowaną wysoką dostępność: [wysoką dostępność i bazę danych SQL platformy Azure](sql-database-high-availability.md) 

- Warstwa Krytyczna dla firm obejmuje grupy trybu failover, strefy wielu dostępności, pełne i różnicowe kopie zapasowe dziennika oraz domyślnie włączone kopie zapasowe w czasie przywracania:  
  - [Wysoka dostępność i usługa Azure SQL Database — nadmiarowa konfiguracja strefy](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatyczne kopie zapasowe](sql-database-automated-backups.md)
  - [Odzyskiwanie bazy danych SQL usługi Azure przy użyciu automatycznych kopii zapasowych bazy danych — przywracanie w czasie](sql-database-recovery-using-backups.md#point-in-time-restore)

- Dodatkowe funkcje ciągłości biznesowej, takie jak grupy automatycznego trybu failover w różnych lokalizacjach geograficznych platformy Azure, można skonfigurować w sposób opisany poniżej: [Omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Następne kroki

- Zobacz [omówienie funkcji zabezpieczeń usługi Azure SQL Database](sql-database-security-overview.md)
