---
title: Baza danych najlepsze rozwiązania dotyczące zabezpieczeń — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące zabezpieczeń bazy danych platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9cd02172af5246c60b93a6e4696988268abed506
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258679"
---
# <a name="azure-database-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń bazy danych platformy Azure
W tym artykule opisano najlepsze rozwiązania dotyczące zabezpieczeń bazy danych.

Najlepsze rozwiązania są oparte na konsensus opinii i pracować z aktualnymi możliwościami platformy Azure, a zestawy funkcji. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule jest aktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

## <a name="secure-databases"></a>Zabezpieczanie bazy danych
Zabezpieczenia są szczególnie ważne dla zarządzania bazami danych i zawsze było priorytet [usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). Bazy danych, które mogą być ściśle chronione ułatwiające spełniają najbardziej prawnych lub wymagań dotyczących zabezpieczeń, takich jak HIPAA, ISO 27001/27002 i PCI DSS poziom 1. Bieżącą listę certyfikatów zgodności zabezpieczeń znajduje się w temacie [witryny Microsoft Trust Center](https://azure.microsoft.com/support/trust-center/services/). Można także umieścić baz danych w określonych centrów danych platformy Azure, na podstawie wymagań prawnych.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Użyj reguł zapory, aby ograniczyć dostęp do bazy danych
Microsoft Azure SQL Database udostępnia usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby zapewnić ochronę dostępu, usługa SQL Database kontroluje dostęp za pomocą:

- Reguły zapory, które ograniczają połączenia przy użyciu adresu IP.
- Mechanizmy uwierzytelniania wymagające użytkowników potwierdzić swoją tożsamość.
- Mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych.

Zapora uniemożliwia wszelki dostęp do serwera bazy danych, do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

Poniższej przedstawiono rysunku, gdzie ustawiasz zapory serwera bazy danych SQL:

![Reguły zapory](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Usługa Azure SQL Database jest dostępna wyłącznie za pośrednictwem portu TCP 1433. Dostęp do bazy danych SQL z komputera, upewnij się, że Zapora komputera klienta umożliwia wychodzący ruch TCP na porcie TCP 1433. Przy użyciu reguł zapory, należy zablokować połączenia przychodzące na porcie TCP 1433, jeśli nie potrzebujesz tych połączeń dla innych aplikacji.

W ramach procesu łączenia wszystkie połączenia połączenia z maszynami wirtualnymi platformy Azure są przekierowywane do adresu IP i portów, które są unikatowe dla każdej roli procesu roboczego. Numer portu należy do zakresu od 11000 do 11999. Aby uzyskać więcej informacji o portach TCP, zobacz [portów wyższych niż 1433 dla platformy ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Oprócz reguły IP zapory zarządza reguł sieci wirtualnej. Reguły sieci wirtualnej są oparte na punkty końcowe usługi sieci wirtualnej. Reguły sieci wirtualnej może być korzystniejsze IP reguł w niektórych przypadkach. Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej i zasady usługi Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Włącz uwierzytelnianie bazy danych
SQL Database obsługuje dwa typy uwierzytelniania, uwierzytelnianie programu SQL Server i uwierzytelniania usługi Azure AD.

### <a name="sql-server-authentication"></a>*Uwierzytelnianie programu SQL Server*

Następujące korzyści:

- Umożliwia ona bazy danych SQL do obsługi środowisk z mieszane systemy operacyjne, gdzie wszyscy użytkownicy nie są uwierzytelniane przez domenę Windows.
- Umożliwia bazy danych SQL do obsługi starszych aplikacji i dostarczone przez partnera wymagających uwierzytelniania programu SQL Server.
- Zezwala użytkownikom na łączenie się z nieznanej lub niezaufanej domeny. Przykładem jest aplikacja, gdzie ustanowionych klientom łączenie się z przypisanych logowania programu SQL Server do odbierania stan zamówienia.
- Umożliwia bazy danych SQL umożliwić obsługę aplikacji opartych na sieci web, w której użytkownikom tworzenie własnych tożsamości.
- Umożliwia deweloperom oprogramowania do rozpowszechniania aplikacji za pomocą hierarchii złożonych uprawnień opartych na nazwach logowania programu SQL Server znanych, wstępnie zdefiniowane.

> [!NOTE]
> Uwierzytelnianie programu SQL Server nie można użyć zabezpieczeń protokołu Kerberos.

Jeśli używasz uwierzytelniania programu SQL Server, musisz mieć:

- Zarządzaj silnych poświadczeń użytkownika.
- Ochrona poświadczeń w parametrach połączenia.
- (Potencjalnie) chronić poświadczenia przekazywane za pośrednictwem sieci z serwera sieci web z bazą danych. Aby uzyskać więcej informacji, zobacz [jak: Połączenia z serwerem SQL przy użyciu uwierzytelniania SQL na platformie ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Uwierzytelnianie usługi Azure Active Directory (AD)*
Uwierzytelnianie usługi Azure AD jest mechanizmem nawiązywania połączenia z usługą Azure SQL Database i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure AD. Przy użyciu uwierzytelniania usługi Azure AD możesz zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie identyfikator udostępnia jedno miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.

> [!NOTE]
> Firma Microsoft zaleca korzystanie z uwierzytelniania usługi Azure AD za korzystanie z uwierzytelniania programu SQL Server.

Następujące korzyści:

- Jego stanowi alternatywę dla uwierzytelniania programu SQL Server.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami bazy danych.
- Umożliwia ona rotacja hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnienia bazy danych przy użyciu grup zewnętrznych (Azure AD).
- Je wyeliminować zapisywania haseł, włączając zintegrowane uwierzytelnianie Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
- Użytkownicy zawartej bazy danych używa do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje ona uwierzytelnianie za pomocą tokenu dla aplikacji łączących się z bazą danych SQL.
- Obsługuje usługi AD FS (domeny federacyjnej) lub uwierzytelniania macierzystego użytkownika i hasło dla lokalnego wystąpienia usługi Azure Active Directory bez synchronizacji domeny.
- Usługa Azure AD obsługuje połączenia z SQL Server Management Studio, które używają uniwersalne uwierzytelnianie usługi Active Directory, w tym usługi Multi-Factor Authentication. Uwierzytelnianie wieloskładnikowe zapewnia silne uwierzytelnianie za pomocą szeregu opcji weryfikacji — połączenie telefoniczne, wiadomość SMS, kart inteligentnych przy użyciu numeru PIN lub powiadomienie aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [SSMS obsługuje uwierzytelnianie wieloskładnikowe systemu Azure AD przy użyciu bazy danych SQL Database i SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Kroki konfiguracji obejmują następujące procedury do konfigurowania i korzystania z uwierzytelniania usługi Azure AD:

- Utwórz i wypełnij usługi Azure AD.
- Opcjonalnie: Skojarz lub zmienić wystąpienia usługi Active Directory, który aktualnie ma skojarzone z subskrypcją platformy Azure.
- Utwórz administratora usługi Azure Active Directory dla usługi Azure SQL Database lub [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Konfigurowanie komputerów klienckich.
- Utwórz użytkowników zawartej bazy danych w bazie danych mapowany do tożsamości usługi Azure AD.
- Łączenie z bazą danych przy użyciu tożsamości usługi Azure AD.

Można znaleźć szczegółowe informacje w [uwierzytelniania użycia usługi Azure Active Directory do uwierzytelniania przy użyciu bazy danych SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Ochrona danych przy użyciu szyfrowania i zabezpieczenia na poziomie wiersza
[Usługa Azure SQL Database technologii transparent data encryption](../sql-database/transparent-data-encryption-azure-sql.md) pomaga chronić dane na dysku i zapewnia ochronę przed nieautoryzowanym dostępem do sprzętu. Wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji. Przezroczyste szyfrowanie danych szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych.

Nawet wtedy, gdy cały magazyn jest zaszyfrowany, należy również zaszyfrować sama baza danych. Jest to implementacja podejście ochronę w głębi w celu ochrony danych. Jeśli używasz usługi Azure SQL Database, aby chronić dane poufne (na przykład kart kredytowych lub numerów ubezpieczenia społecznego) umożliwia ona szyfrowanie bazy danych z trybem FIPS 140-2 zweryfikowanych 256-bitowego szyfrowania AES. To szyfrowanie spełnia wymagania wielu standardów branżowych, (na przykład, HIPAA i PCI).

Pliki związane z programem [buforu rozszerzenia puli (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nie są szyfrowane podczas szyfrowania bazy danych za pomocą technologii transparent data encryption. Należy użyć narzędzi szyfrowania na poziomie systemu plików, takich jak [funkcji BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) lub [systemu szyfrowania plików (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) dla plików związanych z BPE.

Ponieważ autoryzowanym użytkownikiem, np. administratora zabezpieczeń lub administrator bazy danych mają dostęp do danych, nawet jeśli baza danych jest szyfrowana za pomocą technologii transparent data encryption, należy również przestrzegać tych zaleceń:

- Włącz uwierzytelnianie programu SQL Server na poziomie bazy danych.
- Uwierzytelnianie usługi Azure AD przy użyciu [role RBAC](../role-based-access-control/overview.md).
- Upewnij się, że użytkownicy i aplikacje używać osobnych kont do uwierzytelniania. W ten sposób można ograniczyć uprawnienia nadane dla użytkowników i aplikacji i zmniejszyć ryzyko złośliwych działań.
- Zaimplementować zabezpieczenia na poziomie bazy danych przy użyciu ról stałej bazy danych (na przykład db_datareader i db_datawriter). Lub można utworzyć ról niestandardowych dla twojej aplikacji, aby udzielić jawnych uprawnień do obiektów wybranej bazy danych.

Inne sposoby Zabezpieczanie danych należy wziąć pod uwagę:

- [Szyfrowanie na poziomie komórki](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine), co umożliwia klientom szyfrowanie cennych danych wewnątrz aplikacji klienckich i nigdy nie ujawniania kluczy szyfrowania z aparatem bazy danych (bazy danych SQL Database lub SQL Server). W wyniku Always Encrypted umożliwia rozdzielenie tych, którzy są właścicielami danych (i mogą je wyświetlać) oraz tych, którzy zarządzania danymi (ale nie powinny mieć dostępu).
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security), które umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika, który wykonuje kwerendę. (Przykład cechy są grupy członkostwa i wykonywanie kontekstu).

Organizacje, które nie korzystają z szyfrowaniem na poziomie bazy danych może być bardziej narażony na ataki, naruszających danych znajdujących się w bazach danych SQL.

Znajdziesz więcej informacji na temat bazy danych SQL Database technologii transparent data encryption, przeczytaj artykuł [funkcji Transparent Data Encryption z usługą Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Włączanie inspekcji bazy danych
Inspekcja wystąpienie aparatu bazy danych programu SQL Server lub poszczególne bazy danych obejmuje śledzenie i rejestrowanie zdarzeń. Dla programu SQL Server można utworzyć inspekcji, które zawierają wymagania dotyczące zdarzeń na poziomie serwera i specyfikacje dotyczące zdarzeń na poziomie bazy danych. Zdarzenia inspekcji mogą być zapisywane w dziennikach zdarzeń lub plików inspekcji.

Istnieje kilka poziomów inspekcji dla programu SQL Server, w zależności od tego, czy dla instytucji rządowych lub standardy dotyczące instalacji. Inspekcja programu SQL Server udostępnia narzędzia i procesy włączania, przechowywanie i wyświetlanie inspekcji na temat różnych serwera i obiekty bazy danych.

[Funkcja inspekcji usługi Azure SQL Database](../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure.

Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i Znajdź odchylenia i anomalie, które mogą wskazywać potencjalne problemy biznesowe bądź naruszenia zabezpieczeń. Inspekcja ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności.

Aby dowiedzieć się więcej na temat inspekcji bazy danych i jak go włączyć, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](../sql-database/sql-database-auditing.md).

## <a name="enable-database-threat-detection"></a>Włącz wykrywanie zagrożeń bazy danych
Ochrona przed zagrożeniami wykracza poza wykrywania. Ochrona przed zagrożeniami dla bazy danych obejmuje:

- Odnajdowanie i klasyfikowanie najbardziej poufnych danych, dzięki czemu dane można chronić.
- Implementowanie bezpiecznej konfiguracji w bazie danych, dzięki czemu można chronić bazy danych.
- Wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, dzięki czemu można szybko reagować i korygowanie.

**Najlepsze rozwiązanie**: Odnajdywania, klasyfikowania i etykietowania poufnych danych w bazach danych.   
**Szczegóły**: Klasyfikowanie danych w bazie danych SQL przez włączenie [danych, odnajdowanie i klasyfikację](../sql-database/sql-database-data-discovery-and-classification.md) w usłudze Azure SQL Database. Można monitorować dostęp do poufnych danych na pulpicie nawigacyjnym platformy Azure lub pobrać raporty.

**Najlepsze rozwiązanie**: Śledź luk w zabezpieczeniach bazy danych, dzięki czemu można proaktywnie zwiększenia poziomu bezpieczeństwa bazy danych.   
**Szczegóły**: Użyj usługi Azure SQL Database [oceny luk w zabezpieczeniach](../sql-database/sql-vulnerability-assessment.md) usługa, która skanuje pod kątem potencjalnych luk w bazie danych. Usługa wykorzystuje wiedzy o zasady, flagę luk w zabezpieczeniach, które pokazują odchyleń od najlepszych rozwiązań, takich jak nieprawidłowe konfiguracje, nadmierne uprawnienia i niechronione poufnych danych.

Reguły są oparte na najlepszych rozwiązań firmy Microsoft i skoncentrować się na problemy z zabezpieczeniami, są one największych zagrożeń dla bazy danych i jego cennych danych. Obejmują one zarówno problemach na poziomie bazy danych, jak i problemy dotyczące zabezpieczeń na poziomie serwera, takich jak ustawieniach zapory serwera i uprawnienia na poziomie serwera. Zasady te reprezentują wiele wymagania prawne organów standardów zgodności.

**Najlepsze rozwiązanie**: Włącz wykrywanie zagrożeń.  
**Szczegóły**:  Włączanie usługi Azure SQL Database [wykrywania zagrożeń](../sql-database/sql-database-threat-detection.md) można pobrać alertów zabezpieczeń i zaleceń dotyczących zbadania i wyeliminowania zagrożenia. Możesz otrzymywać alerty o podejrzanych działaniach bazy danych, potencjalnych lukach w zabezpieczeniach oraz atakami polegającymi na iniekcji SQL, a także nietypowe bazy danych wzorce dostępu i zapytania.

[Zaawansowana ochrona przed zagrożeniami](../sql-database/sql-advanced-threat-protection.md) ujednoliconego pakiet dla zaawansowane funkcje zabezpieczeń programu SQL. Obejmuje usługi, o których wspomniano wcześniej: Odnajdywanie danych i klasyfikacji, ocena luk w zabezpieczeniach i wykrywanie zagrożeń. Zapewnia jednej lokalizacji do włączania i zarządzania tymi funkcjami.

Włączenie tych możliwości pomoże Ci:

- Spełnia wymagania zgodności z przepisami i standardami ochrony prywatności danych.
- Kontrola dostępu do baz danych i wzmocnione ich zabezpieczeń.
- Monitorowanie środowiska dynamicznej bazy danych, której zmiany są trudne do śledzenia.
- Wykrywanie i reagowanie na potencjalne zagrożenia.

Ponadto wykrywanie zagrożeń integruje alerty z usługą Azure Security Center, pełny widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure.

## <a name="enable-feature-restrictions"></a>Włącz ograniczenia funkcji

Dane znajdujące się w bazach danych może być narażony na atakujących przy użyciu wektory ataków, które błędy bazy danych i czas wykonywania zapytania. Usługa Azure SQL Database oferuje pewną liczbę funkcji ograniczeń mechanizmów ochrony bazy danych. Aby dowiedzieć się więcej, zobacz [ograniczeń funkcji bazy danych SQL](../sql-database/sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

Do dyspozycji więcej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązane usługi firmy Microsoft są następujące zasoby:
* [Blog zespołu usługi Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — aktualne instrukcje dotyczące najnowszych zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku, gdy luk w zabezpieczeniach firmy Microsoft, w tym problemów z platformą Azure, mogą zostać zgłoszone lub za pośrednictwem poczty e-mail do secure@microsoft.com
