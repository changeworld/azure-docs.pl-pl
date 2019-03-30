---
title: Omówienie zabezpieczeń usługi Azure SQL Database | Microsoft Docs
description: Więcej informacji na temat zabezpieczeń usługi Azure SQL Database i programu SQL Server, włącznie z różnicami między chmurą i programu SQL Server w środowisku lokalnym.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 03/29/2019
ms.openlocfilehash: 7fc69c3d0b6820db2227b241d63e4304152d99bf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664679"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Omówienie funkcji zabezpieczeń usługi Azure SQL Database

W tym artykule przedstawiono podstawowe informacje o zabezpieczeniach warstwy danych aplikacji przy użyciu usługi Azure SQL Database. Strategii zabezpieczeń opisane poniżej warstwowego podejścia ochronę w głębi, jak pokazano na ilustracji poniżej, a są przenoszone z zewnątrz w:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Bezpieczeństwo sieci

Microsoft Azure SQL Database udostępnia usługę relacyjnej bazy danych na potrzeby chmury i przeznaczonymi dla przedsiębiorstw. Aby lepiej chronić dane klientów, Zapora uniemożliwia dostęp do sieci z serwerem bazy danych, do momentu dostępu ma jawnie przyznane uprawnienia na podstawie adresu IP lub Azure Virtual źródła ruchu sieciowego.

### <a name="ip-firewall-rules"></a>Reguły zapory IP

Reguły zapory IP udzielić dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure SQL Database i SQL Data Warehouse reguły zapory](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Reguły zapory sieci wirtualnej

[Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) rozszerzanie łączność z siecią wirtualną za pośrednictwem sieci szkieletowej platformy Azure i włączyć usługi Azure SQL Database zidentyfikować podsieci sieci wirtualnej, z którego pochodzi ruch. Aby zezwolić na ruch do usługi Azure SQL Database, należy użyć SQL [tagów usług](../virtual-network/security-overview.md) zezwalająca na ruch wychodzący za pomocą sieciowych grup zabezpieczeń.

[Reguły sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) włączyć usługi Azure SQL Database, aby akceptowane były tylko łączności, które są wysyłane z wybranej podsieci w sieci wirtualnej.

> [!NOTE]
> Kontrola dostępu za pomocą reguł zapory jest *nie* dotyczą **wystąpienia zarządzanego**. Aby dowiedzieć się więcej o konfiguracji sieci potrzebne zobacz [nawiązywania połączenia z wystąpienia zarządzanego](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Zarządzanie dostępem

> [!IMPORTANT]
> Zarządzanie bazami danych i serwery baz danych w obrębie platformy Azure jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [kontroli dostępu opartej na rolach w witrynie Azure portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Uwierzytelnianie jest to proces potwierdzania użytkownika, który podaje. Usługa Azure SQL Database obsługuje dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**:

    Uwierzytelnianie bazy danych SQL odwołuje się do uwierzytelniania użytkownika podczas łączenia się z [usługi Azure SQL Database](sql-database-technical-overview.md) przy użyciu nazwy użytkownika i hasła. Podczas tworzenia serwera bazy danych dla bazy danych można określić identyfikatora logowania "Server admin" przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń, "server admin" może uwierzytelniać się do dowolnej bazy danych na tym serwerze bazy danych jako właściciel bazy danych. Po tym dodatkowych kont logowania SQL i użytkownicy mogą być tworzone przez administratora serwera, które umożliwiają użytkownikom nawiązywanie połączeń za pomocą nazwy użytkownika i hasła.

- **Uwierzytelnianie usługi Azure Active Directory**:

    Uwierzytelnianie usługi Azure Active Directory jest mechanizmem nawiązywania połączenia z usługą [usługi Azure SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Uwierzytelnianie usługi Azure AD umożliwia administratorom centralne zarządzanie tożsamościami i uprawnieniami użytkowników bazy danych wraz z innymi usługami firmy Microsoft w jednej centralnej lokalizacji. Obejmuje minimalizacji zapisywania haseł i umożliwia zasady dotyczące haseł scentralizowane obrotu.

     Administrator serwera o nazwie **administratora usługi Active Directory** muszą zostać utworzone do użycia uwierzytelnianie usługi Azure AD z usługą SQL Database. Aby uzyskać więcej informacji, zobacz [nawiązywania połączenia z SQL bazy danych przez przy użyciu usługi Azure Active Directory Authentication](sql-database-aad-authentication.md). Uwierzytelnianie usługi Azure AD obsługuje zarówno zarządzanych, jak i federacyjnego konta. Kont federacyjnych obsługuje Windows użytkowników i grup w domenie klienta Sfederowane za pomocą usługi Azure AD.

    Dodatkowe są dostępne opcje uwierzytelniania usługi Azure AD [uniwersalne uwierzytelnianie usługi Active Directory dla programu SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) połączeń w tym [uwierzytelnianie wieloskładnikowe](../active-directory/authentication/concept-mfa-howitworks.md) i [ Dostęp warunkowy](sql-database-conditional-access.md).

> [!IMPORTANT]
> Zarządzanie bazami danych i serwerów w ramach platformy Azure jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji na temat tego artykułu, zobacz [kontroli dostępu opartej na rolach w witrynie Azure portal](../role-based-access-control/overview.md). Kontrola dostępu za pomocą reguł zapory jest *nie* dotyczą **wystąpienia zarządzanego**. Zobacz następujący artykuł w [nawiązywania połączenia z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md) uzyskać więcej informacji o konfiguracji sieci niezbędnej.

Autoryzacja odwołuje się do uprawnienia przypisane do użytkownika w ramach usługi Azure SQL Database i określa, jakie użytkownik może wykonywać. Uprawnienia są kontrolowane przez dodawanie kont użytkowników do [ról bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles) definiują uprawnienia na poziomie bazy danych lub przyznawanie użytkownikowi niektórych [uprawnienia na poziomie obiektu](/sql/relational-databases/security/permissions-database-engine). Aby uzyskać więcej informacji, zobacz [identyfikatory logowania i użytkowników](sql-database-manage-logins.md)

Najlepszym rozwiązaniem należy dodać użytkowników do roli z co najmniej uprawnienia wymagane do ich funkcję zadania. Konto administratora serwera jest członkiem roli db_owner, która ma szersze uprawnienia i może być przyznany użytkownikom ostrożnie. Podczas korzystania z aplikacji z usługą Azure SQL Database, używać [ról aplikacji](/sql/relational-databases/security/authentication-access/application-roles) z ograniczonymi uprawnieniami. Dzięki temu aplikacja z bazą danych ma minimalnych uprawnień wymaganych przez aplikację.

### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza

Zabezpieczenia na poziomie wiersza umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (na przykład grupy członkostwa lub kontekstu wykonania). Aby uzyskać więcej informacji, zobacz [Zabezpieczenia na poziomie wierszy](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła. 

Aby uzyskać omówienie uprawnień w usłudze Azure SQL Database, zobacz [identyfikatory logowania i użytkowników](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>Ochrona przed zagrożeniami

Usługa SQL Database zabezpiecza dane klientów, zapewniając możliwości wykrywania zagrożeń i inspekcji.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Inspekcja SQL w dzienniki usługi Azure Monitor i usługi Event Hubs

Inspekcji usługi SQL Database śledzi działania bazy danych i pomaga zachować zgodność ze standardami zabezpieczeń, rejestrując zdarzenia bazy danych inspekcji logowania na koncie magazynu platformy Azure należącej do klienta. Inspekcja umożliwia monitorowanie trwających działań bazy danych, a także analizowanie i badanie działań historycznych w celu zidentyfikowania potencjalnych zagrożeń, podejrzanych nadużyć i naruszeń zabezpieczeń. Aby uzyskać więcej informacji, zobacz wprowadzenie do [inspekcji usługi SQL Database](sql-database-auditing.md).  

### <a name="threat-detection"></a>Wykrywanie zagrożeń

Ochronę przed zagrożeniami wykrywania rozszerza możliwości inspekcji, analizując dzienniki inspekcji dla nietypowe zachowanie i potencjalnie szkodliwe próby dostępu lub wykorzystania baz danych. Alerty są tworzone dla podejrzanych działań lub wzorce nietypowego dostępu, takich jak ataki przez wstrzyknięcie kodu SQL, potencjalne przeniknięciu danych i siłowego złamania hasła. Alerty wykrywania zagrożeń są wyświetlane z [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/), gdzie znajdują się szczegółowe informacje o podejrzanych działaniach i zalecenia dotyczące dalsze badanie podane wraz z akcji ograniczenia zagrożenia. Koszt wykrywania zagrożeń to 15 USD/serwera/miesiąc. Jest to bezpłatne przez pierwsze 60 dni. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą SQL Database Threat detection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Information protection i szyfrowania

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security, TLS (szyfrowanie podczas przesyłania)

Usługa SQL Database zabezpiecza dane klienta dzięki szyfrowaniu danych w ruchu przy użyciu [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server wymusza szyfrowania (SSL/TLS) na wszystkie godziny dla wszystkich połączeń. Dzięki temu wszystkie dane są szyfrowane "przesyłanych" między klientem i serwerem, niezależnie od ustawienia **Szyfruj** lub **TrustServerCertificate** w parametrach połączenia.

Najlepszym rozwiązaniem, zalecamy w połączeniu aplikacji ciągu możesz określić szyfrowanego połączenia i _**nie**_ ufać certyfikatowi serwera. Zmusza to aplikację, aby zweryfikować certyfikat serwera i uniemożliwia aplikacji jest narażony na ataki typu man w atakach typu środkowej.

Na przykład używając sterownika ADO.NET jest to realizowane za pośrednictwem **Encrypt = True** i **TrustServerCertificate = False**. Parametry połączenia można uzyskać w witrynie Azure portal, mają prawidłowe ustawienia.

> [!IMPORTANT]
> Należy pamiętać, że niektóre sterowniki firmy Microsoft może domyślnie używają protokołu TLS lub nie polegają na starszą wersję protokołu TLS (< 2.0) aby funkcjonować. W takim przypadku programu SQL Server nadal umożliwia nawiązanie połączenia z bazą danych. Jednak zaleca się dokonanie oceny zagrożeń bezpieczeństwa umożliwienia takiego sterowników i aplikacji do łączenia z bazą danych SQL, zwłaszcza, jeśli przechowujesz ważne dane. 
>
> Aby uzyskać więcej informacji na temat protokołów TLS i łączność, zobacz [zagadnienia dotyczące protokołu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (szyfrowanie w spoczynku)

[Przezroczystego szyfrowania danych (TDE) usługi Azure SQL Database](transparent-data-encryption-azure-sql.md) warstwę zabezpieczeń, aby chronić dane magazynowane przed dostępem nieautoryzowanych lub w trybie offline pliki raw lub tworzenie kopii zapasowych. Typowe scenariusze obejmują kradzieży centrum danych lub niezabezpieczony likwidacji sprzętu lub nośników, takich jak stacje dysków i taśm kopii zapasowych. Funkcja TDE szyfruje całą bazę danych przy użyciu algorytmu szyfrowania AES, które nie wymagają deweloperów aplikacji wprowadzić zmiany, aby migrować istniejące aplikacje.

Na platformie Azure domyślnie szyfrowane są wszystkie nowo utworzone bazy danych SQL i klucza szyfrowania bazy danych jest chroniona za pomocą wbudowanego serwera certyfikatu.  Certyfikat utrzymania i rotacji są zarządzane przez usługę i nie wymaga wprowadzania danych przez użytkownika. Klienci, którzy wolą, aby przejąć kontrolę nad kluczami szyfrowania mogą zarządzać kluczami w [usługi Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Zarządzanie kluczami za pomocą usługi Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK), obsługa [funkcji Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) pozwala klientom na przejęcie na własność zarządzania kluczami i korzystania z rotacji [usługi Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), Azure w chmurze system zewnętrzny zarządzania kluczami. Po odebraniu dostępu bazy danych do magazynu kluczy bazy danych nie można odszyfrować i odczytu do pamięci. Usługa Azure Key Vault zapewnia platformę centralne zarządzanie kluczami, wykorzystuje ściśle monitorowanych sprzętowych modułach zabezpieczeń (HSM) i umożliwia rozdzielenie obowiązków między danych i zarządzanie kluczami w celu spełnienia wymagań dotyczących zgodności zabezpieczeń.

### <a name="always-encrypted-encryption-in-use"></a>Zawsze szyfrowane (szyfrowanie w użyciu)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) jest funkcją zaprojektowanych w celu ochrony danych poufnych przechowywanych w kolumnach konkretnej bazy danych z programu access (na przykład numerów kart kredytowych, narodowe numery identyfikacyjne lub dane na _trzeba znać_ podstawy). W tym administratorzy baz danych lub innych uprzywilejowanych użytkowników, którzy są upoważnione do dostępu do bazy danych, aby wykonywać zadania zarządzania, jednak musi nie działalność wymaga dostępu do danych określonego w zaszyfrowanych kolumn. Dane są zawsze szyfrowane, co oznacza, że zaszyfrowane dane zostaną odszyfrowane tylko w przypadku przetwarzania przez aplikacje klienckie przy użyciu dostępu do klucza szyfrowania.  Klucz szyfrowania nigdy nie jest ujawniany do bazy danych SQL i mogą być przechowywane w [Store certyfikatu Windows](sql-database-always-encrypted.md) lub [usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>Maskowania

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

Baza danych SQL dynamiczne maskowanie danych ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dane dynamiczne maskowanie automatycznie wykrywa potencjalnie poufne dane w bazie danych SQL Azure i zapewnia wiarygodne zalecenia z tych pól, z minimalnym wpływem na warstwę aplikacji. Jego działanie polega na zaciemnianiu poufnych danych w wyznaczonych polach bazy danych w zestawie wyników zapytania, przy czym dane w bazie danych pozostają bez zmian. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą SQL Database dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Maskowanie danych statycznych

[Maskowanie danych statycznych](/sql/relational-databases/security/static-data-masking) to narzędzie klienta dostępne w [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18,0 wersji zapoznawczej 5 lub nowszy.  Maskowanie danych statycznych umożliwia użytkownikom tworzenie kopię bazy danych, których dane w wybranych kolumnach zostało trwale maskowane. Dostępne funkcje maskowania: o wartości NULL maskowania, maskowanie pojedynczej wartości, losowa i shuffle grupy maskowania oraz ciągu złożonego maskowania. Maskowane kopią bazy danych organizacje mogą oddzielić produkcji i środowisk testowych, udostępniając maskowanego kopiowania. Danych poufnych jest dostatecznie chroniony i utrzymują się inne właściwości bazy danych. Maskowanie baz danych jest zalecane, gdzie firm dostęp do bazy danych jest wymagany.

## <a name="security-management"></a>Zarządzanie zabezpieczeniami

### <a name="vulnerability-assessment"></a>Ocena luk w zabezpieczeniach

[Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) można łatwo skonfigurować usługę, która może odnajdywać, śledzenie i ułatwić korygowanie potencjalnych luk w zabezpieczeniach bazy danych z celem aktywnie poprawy ogólnego stanu zabezpieczeń bazy danych. Ocena luk w zabezpieczeniach (oceny luk w zabezpieczeniach) jest częścią oferty zabezpieczeń (ADS) zaawansowanych danych, która jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń programu SQL. Ocena luk w zabezpieczeniach można uzyskać dostępu do i zarządzane za pośrednictwem portalu centralnej REKLAM SQL.

### <a name="data-discovery--classification"></a>Odnajdowanie i klasyfikacja danych

Odnajdywanie i klasyfikacja (obecnie w wersji zapoznawczej) danych zapewnia zaawansowane możliwości wbudowane w usłudze Azure SQL Database na potrzeby odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w bazach danych. Odnajdywanie i klasyfikacja priorytetowe poufne dane (biznesowe i finansowe, opieki zdrowotnej, dane osobowe, itp.), można odtwarzać zasadniczą rolę w Twojej organizacji stature ochrony informacji. Może służyć jako infrastruktury:

- Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując nietypowego dostępu do poufnych danych.
- Kontrolowanie dostępu do i wzmacniania ochrony bezpieczeństwa, baz danych zawierających poufne dane.
- Pomaga zaspokoić potrzeby dotyczące standardów ochrony prywatności danych i wymagania dotyczące zgodności z przepisami.

Aby uzyskać więcej informacji, zobacz [wprowadzenie odnajdywanie i klasyfikacja danych](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Zgodność

Oprócz powyższych funkcji i funkcjonalności, które mogą pomóc aplikacji spełnić różne wymagania dotyczące zabezpieczeń, usługa Azure SQL Database również uczestniczy w regularnych inspekcjach i posiada certyfikaty na liczbie standardów zgodności. Aby uzyskać więcej informacji, zobacz witrynę [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/), w której można znaleźć aktualną listę [certyfikatów zgodności usługi SQL Database](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Kolejne kroki

- Omówienie korzystania z funkcji kontroli dostępu w usłudze SQL Database zawiera artykuł dotyczący [kontroli dostępu](sql-database-control-access.md).
- Omówienie bazy danych inspekcji, zobacz [inspekcji usługi SQL Database](sql-database-auditing.md).
- Omówienie wykrywania zagrożeń, zobacz [wykrywania zagrożeń bazy danych SQL](sql-database-threat-detection.md).
