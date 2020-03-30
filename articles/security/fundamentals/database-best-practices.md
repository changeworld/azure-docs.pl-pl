---
title: Najważniejsze wskazówki dotyczące zabezpieczeń bazy danych — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń bazy danych platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0f2e0257c5bf855b0d9be61c43b68b4e30b3d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125104"
---
# <a name="azure-database-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń bazy danych na platformie Azure
W tym artykule opisano najważniejsze wskazówki dotyczące zabezpieczeń bazy danych.

Najlepsze rozwiązania są oparte na konsensusie opinii i działają z bieżącymi możliwościami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się w czasie, a ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="secure-databases"></a>Bezpieczne bazy danych
Bezpieczeństwo jest głównym problemem w zarządzaniu bazami danych i zawsze było priorytetem dla [usługi Azure SQL Database.](../../sql-database/index.yml) Bazy danych mogą być ściśle zabezpieczone, aby spełnić większość wymagań regulacyjnych lub bezpieczeństwa, w tym HIPAA, ISO 27001/27002 i PCI DSS Level 1. Aktualna lista certyfikatów zgodności zabezpieczeń jest dostępna w [witrynie Centrum zaufania firmy Microsoft](https://azure.microsoft.com/support/trust-center/services/). Można również umieścić bazy danych w określonych centrach danych platformy Azure na podstawie wymagań regulacyjnych.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Ograniczanie dostępu do bazy danych za pomocą reguł zapory
Usługa Microsoft Azure SQL Database zapewnia relacyjne usługi bazy danych dla platformy Azure i innych aplikacji internetowych. Aby zapewnić bezpieczeństwo dostępu, baza danych SQL kontroluje dostęp za pomocą:

- Reguły zapory, które ograniczają łączność według adresu IP.
- Mechanizmy uwierzytelniania, które wymagają od użytkowników udowodnienia swojej tożsamości.
- Mechanizmy autoryzacji, które ograniczają użytkowników do określonych akcji i danych.

Zapory uniemożliwiają dostęp do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

Na poniższym rysunku pokazano, gdzie ustawiono zaporę serwera w bazie danych SQL:

![Reguły zapory](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Usługa Azure SQL Database jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do bazy danych SQL z komputera, upewnij się, że zapora komputera klienckiego umożliwia wychodzącą komunikację TCP na porcie TCP 1433. Blokuj połączenia przychodzące w porcie TCP 1433 przy użyciu reguł zapory, jeśli nie są potrzebne te połączenia dla innych aplikacji.

W ramach procesu połączenia połączenia z maszyn wirtualnych platformy Azure są przekierowywane do adresu IP i portu, które są unikatowe dla każdej roli procesu roboczego. Numer portu należy do zakresu od 11000 do 11999. Aby uzyskać więcej informacji na temat portów TCP, zobacz [Porty poza 1433 dla ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Oprócz reguł IP zapora zarządza regułami sieci wirtualnej. Reguły sieci wirtualnej są oparte na punktach końcowych usługi sieci wirtualnej. Reguły sieci wirtualnej mogą być w niektórych przypadkach preferowane w przypadku reguł ip. Aby dowiedzieć się więcej, zobacz [Punkty końcowe usługi sieci wirtualnej i reguły dla usługi Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Włączanie uwierzytelniania bazy danych
Baza danych SQL obsługuje dwa typy uwierzytelniania, uwierzytelniania programu SQL Server i uwierzytelniania usługi Azure AD.

### <a name="sql-server-authentication"></a>*Uwierzytelnianie programu SQL Server*

Oto przykładowe korzyści:

- Umożliwia sql database do obsługi środowisk z mieszanych systemów operacyjnych, gdzie wszyscy użytkownicy nie są uwierzytelniane przez domenę systemu Windows.
- Umożliwia bazie danych SQL do obsługi starszych aplikacji i aplikacji dostarczonych przez partnera, które wymagają uwierzytelniania programu SQL Server.
- Umożliwia użytkownikom łączenie się z nieznanych lub niezaufanych domen. Przykładem jest aplikacja, w której ustalone klienci łączą się z przypisanymi identyfikatorami logowania programu SQL Server, aby otrzymać stan swoich zamówień.
- Umożliwia bazie danych SQL do obsługi aplikacji opartych na sieci Web, w których użytkownicy tworzą własne tożsamości.
- Umożliwia deweloperom oprogramowania rozpowszechnianie swoich aplikacji przy użyciu złożonej hierarchii uprawnień na podstawie znanych, wstępnie ustawionych danych logowania programu SQL Server.

> [!NOTE]
> Uwierzytelnianie programu SQL Server nie może używać protokołu zabezpieczeń Protokołu Kerberos.

Jeśli używasz uwierzytelniania programu SQL Server, należy:

- Samodzielnie zarządzaj silnymi poświadczeniami.
- Chroń poświadczenia w ciągu połączenia.
- (Potencjalnie) chronić poświadczenia przekazywane przez sieć z serwera sieci web do bazy danych. Aby uzyskać więcej informacji, zobacz [Jak: Łączenie się z programem SQL Server przy użyciu uwierzytelniania SQL w ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Uwierzytelnianie usługi Azure Active Directory (AD)*
Uwierzytelnianie usługi Azure AD to mechanizm łączenia się z usługą Azure SQL Database i [magazynem danych SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure AD. Za pomocą uwierzytelniania usługi Azure AD można zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie tożsamościami oznacza jedną lokalizację do zarządzania użytkownikami bazy danych i prostsze zarządzanie uprawnieniami.

> [!NOTE]
> Zaleca się użycie uwierzytelniania usługi Azure AD za pomocą uwierzytelniania programu SQL Server.

Oto przykładowe korzyści:

- Zapewnia alternatywę dla uwierzytelniania programu SQL Server.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych.
- Umożliwia obracanie hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Można wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
- Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji, które łączą się z bazą danych SQL.
- Obsługuje usługi AD FS (federacja domen) lub natywne uwierzytelnianie użytkownika/hasła dla lokalnego wystąpienia usługi Azure Active Directory bez synchronizacji domeny.
- Usługa Azure AD obsługuje połączenia z programu SQL Server Management Studio korzystające z uwierzytelniania uniwersalnego usługi Active Directory, które obejmuje uwierzytelnianie wieloskładnikowe. Uwierzytelnianie wieloskładnikowe zapewnia silne uwierzytelnianie dzięki szerokiej gamie opcji weryfikacji — połączenia telefonicznemu, wiadomości sms, kartom inteligentnym z kodem PIN lub powiadomieniu o aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [Obsługa usługi SSMS dla uwierzytelniania wieloskładnikowego usługi Azure AD z bazą danych SQL i magazynem danych SQL](../../sql-database/sql-database-ssms-mfa-authentication.md).

Kroki konfiguracji obejmują następujące procedury konfigurowania i używania uwierzytelniania usługi Azure AD:

- Tworzenie i wypełnianie usługi Azure AD.
- Opcjonalnie: skojarzyć lub zmienić wystąpienie usługi Active Directory, które jest obecnie skojarzone z subskrypcją platformy Azure.
- Utwórz administratora usługi Azure Active Directory dla usługi Azure SQL Database lub [usługi Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Skonfiguruj komputery klienckie.
- Tworzenie użytkowników zawartej bazy danych w bazie danych mapowane do tożsamości usługi Azure AD.
- Połącz się z bazą danych przy użyciu tożsamości usługi Azure AD.

Szczegółowe informacje można znaleźć w obszarze [Korzystanie z uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą bazy danych SQL, wystąpienia zarządzanego lub magazynu danych SQL](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Ochrona danych przy użyciu szyfrowania i zabezpieczeń na poziomie wiersza
[Szyfrowanie danych przezroczystych usługi Azure SQL Database](../../sql-database/transparent-data-encryption-azure-sql.md) pomaga chronić dane na dysku i chroni przed nieautoryzowanym dostępem do sprzętu. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności wprowadzania jakichkolwiek zmian w aplikacji. Przezroczyste szyfrowanie danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucz szyfrowania bazy danych.

Nawet wtedy, gdy cały magazyn jest szyfrowany, ważne jest również, aby również zaszyfrować samą bazę danych. Jest to implementacja obrony w szczegółowe podejście do ochrony danych. Jeśli używasz usługi Azure SQL Database i chcesz chronić poufne dane (takie jak numery kart kredytowych lub numerów ubezpieczenia społecznego), możesz szyfrować bazy danych za pomocą zweryfikowanego 256-bitowego szyfrowania AES fips 140-2. Szyfrowanie to spełnia wymagania wielu standardów branżowych (na przykład HIPAA i PCI).

Pliki związane z [rozszerzeniem puli buforów (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nie są szyfrowane podczas szyfrowania bazy danych przy użyciu przezroczystego szyfrowania danych. W przypadku plików związanych z bpe należy używać narzędzi szyfrowania na poziomie systemu plików, takich jak [funkcja BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) lub system szyfrowania plików [(EFS).](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10))

Ponieważ autoryzowany użytkownik, taki jak administrator zabezpieczeń lub administrator bazy danych, może uzyskać dostęp do danych, nawet jeśli baza danych jest szyfrowana za pomocą przezroczystego szyfrowania danych, należy również postępować zgodnie z tymi zaleceniami:

- Włącz uwierzytelnianie programu SQL Server na poziomie bazy danych.
- Użyj uwierzytelniania usługi Azure AD przy użyciu [ról RBAC](/azure/role-based-access-control/overview).
- Upewnij się, że użytkownicy i aplikacje używają oddzielnych kont do uwierzytelniania. W ten sposób można ograniczyć uprawnienia przyznane użytkownikom i aplikacjom oraz zmniejszyć ryzyko złośliwej aktywności.
- Zaimplementuj zabezpieczenia na poziomie bazy danych przy użyciu ról stałej bazy danych (takich jak db_datareader lub db_datawriter). Można też utworzyć role niestandardowe dla aplikacji, aby udzielić jawnych uprawnień do wybranych obiektów bazy danych.

Aby uzyskać inne sposoby zabezpieczania danych, należy wziąć pod uwagę:

- [Szyfrowanie na poziomie komórki](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine), co pozwala klientom na szyfrowanie poufnych danych wewnątrz aplikacji klienckich i nigdy nie ujawniają kluczy szyfrowania do aparatu bazy danych (bazy danych SQL lub programu SQL Server). W rezultacie Always Encrypted zapewnia separację między tymi, którzy są właścicielami danych (i mogą je wyświetlać) a tymi, którzy zarządzają danymi (ale nie powinni mieć dostępu).
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security), który umożliwia klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie cech użytkownika, który wykonuje kwerendę. (Przykładowe cechy to kontekst członkostwa w grupie i wykonywania).

Organizacje, które nie używają szyfrowania na poziomie bazy danych, mogą być bardziej podatne na ataki, które naruszają dane znajdujące się w bazach danych SQL.

Więcej informacji na temat szyfrowania danych przezroczystych bazy danych SQL można dowiedzieć się, czytając artykuł [Przezroczyste szyfrowanie danych za pomocą usługi Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Włączanie inspekcji bazy danych
Inspekcja wystąpienia aparatu baz danych programu SQL Server lub pojedynczej bazy danych obejmuje śledzenie i rejestrowanie zdarzeń. W przypadku programu SQL Server można utworzyć inspekcje zawierające specyfikacje zdarzeń na poziomie serwera i specyfikacje zdarzeń na poziomie bazy danych. Zdarzenia inspekcji mogą być zapisywane w dziennikach zdarzeń lub w plikach inspekcji.

Istnieje kilka poziomów inspekcji dla programu SQL Server, w zależności od wymagań rządowych lub standardów dla instalacji. Inspekcja programu SQL Server udostępnia narzędzia i procesy umożliwiające włączanie, przechowywanie i wyświetlanie inspekcji na różnych obiektach serwera i bazy danych.

[Inspekcja bazy danych SQL usługi Azure](/azure/sql-database/sql-database-auditing) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure.

Inspekcja może pomóc w zachowaniu zgodności z przepisami, zrozumieniu aktywności bazy danych oraz znalezieniu rozbieżności i anomalii, które mogą wskazywać na problemy biznesowe lub naruszenia zabezpieczeń. Inspekcja ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje zgodności.

Aby dowiedzieć się więcej o inspekcji baz danych i włączaniu jej, zobacz Wprowadzenie do [inspekcji baz danych SQL](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Włączanie wykrywania zagrożeń w bazie danych
Ochrona przed zagrożeniami wykracza poza wykrywanie. Ochrona przed zagrożeniami w bazie danych obejmuje:

- Odnajdowanie i klasyfikowanie najbardziej poufnych danych, dzięki czemu można chronić swoje dane.
- Implementowanie bezpiecznych konfiguracji w bazie danych, dzięki czemu można chronić bazę danych.
- Wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich pojawiania się, dzięki czemu można szybko reagować i korygować.

**Najlepsze rozwiązanie:** Odnajdowanie, klasyfikowanie i oznaczanie poufnych danych w bazach danych.   
**Szczegóły:** klasyfikowanie danych w bazie danych SQL, włączając [odnajdowanie i klasyfikację danych](/azure/sql-database/sql-database-data-discovery-and-classification) w bazie danych SQL azure. Możesz monitorować dostęp do poufnych danych na pulpicie nawigacyjnym platformy Azure lub pobierać raporty.

**Najlepsze rozwiązanie:** Śledzenie luk w zabezpieczeniach bazy danych, dzięki czemu można proaktywnie poprawić bezpieczeństwo bazy danych.   
**Szczegóły:** Użyj usługi oceny [luk w zabezpieczeniach](/azure/sql-database/sql-vulnerability-assessment) bazy danych SQL SQL, która skanuje w poszukiwaniu potencjalnych luk w zabezpieczeniach bazy danych. Usługa wykorzystuje bazę wiedzy reguł, które flagi luk w zabezpieczeniach i pokazać odstępstwa od najlepszych rozwiązań, takich jak błędy konfiguracji, nadmierne uprawnienia i niechronione dane poufne.

Reguły są oparte na najlepszych praktykach firmy Microsoft i koncentrują się na problemach z zabezpieczeniami, które stanowią największe zagrożenia dla bazy danych i jej cennych danych. Obejmują one zarówno problemy na poziomie bazy danych, jak i problemy z zabezpieczeniami na poziomie serwera, takie jak ustawienia zapory serwera i uprawnienia na poziomie serwera. Zasady te reprezentują również wiele wymogów organów regulacyjnych w celu spełnienia ich standardów zgodności.

**Najlepsze rozwiązanie:** Włącz wykrywanie zagrożeń.  
**Szczegóły:** Włącz [wykrywanie zagrożeń](/azure/sql-database/sql-database-threat-detection) bazy danych SQL platformy Azure, aby uzyskać alerty zabezpieczeń i zalecenia dotyczące sposobu badania i ograniczania zagrożeń. Pojawiają się alerty o podejrzanych działaniach bazy danych, potencjalnych lukach i atakach iniekcji SQL, a także nietypowych wzorcach dostępu do bazy danych i zapytań.

[Advanced Threat Protection](/azure/sql-database/sql-advanced-threat-protection) to ujednolicony pakiet dla zaawansowanych funkcji zabezpieczeń SQL. Obejmuje usługi wymienione wcześniej: odnajdowanie i klasyfikacja danych, ocena luk w zabezpieczeniach i wykrywanie zagrożeń. Zapewnia jedną lokalizację do włączania i zarządzania tymi możliwościami.

Włączenie tych funkcji pomaga:

- Spełniaj standardy ochrony danych i wymagania dotyczące zgodności z przepisami.
- Kontroluj dostęp do baz danych i wzmacniaj ich bezpieczeństwo.
- Monitoruj dynamiczne środowisko bazy danych, w którym zmiany są trudne do śledzenia.
- Wykrywanie potencjalnych zagrożeń i reagowanie na nie.

Ponadto wykrywanie zagrożeń integruje alerty z usługą Azure Security Center, aby uzyskać centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure.

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

Dostępne są następujące zasoby, aby zapewnić bardziej ogólne informacje na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — aby uzyskać aktualne informacje na temat najnowszych informacji w usłudze Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — gdzie można zgłaszać luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, lubsecure@microsoft.com
