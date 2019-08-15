---
title: Najlepsze rozwiązania w zakresie zabezpieczeń bazy danych — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń usługi Azure Database.
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
ms.openlocfilehash: 72b15d77baedae318d4503f2d481b08202730459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928001"
---
# <a name="azure-database-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń bazy danych na platformie Azure
W tym artykule opisano najlepsze rozwiązania dotyczące zabezpieczeń bazy danych.

Najlepsze rozwiązania są oparte na konsensusie opinii i współpracują z bieżącymi funkcjami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się wraz z upływem czasu, a ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="secure-databases"></a>Zabezpieczanie baz danych
Zabezpieczenia to najważniejsze zagadnienia związane z zarządzaniem bazami danych i zawsze ma priorytet dla [Azure SQL Database](../../sql-database/index.yml). Bazy danych mogą być ściśle zabezpieczone, aby pomóc spełnić większość wymagań prawnych lub bezpieczeństwa, w tym HIPAA, ISO 27001/27002 PCI DSS i poziom 1. Bieżąca lista certyfikatów zgodności z zabezpieczeniami jest dostępna w [witrynie Centrum zaufania firmy Microsoft](https://azure.microsoft.com/support/trust-center/services/). Możesz również wybrać opcję umieszczenia baz danych w określonych centrach Datacenter w oparciu o wymagania prawne.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Używanie reguł zapory do ograniczania dostępu do bazy danych
Microsoft Azure SQL Database zapewnia usługi relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby zapewnić bezpieczeństwo dostępu, SQL Database kontroluje dostęp z:

- Reguły zapory, które ograniczają łączność przy użyciu adresu IP.
- Mechanizmy uwierzytelniania, które wymagają, aby użytkownicy udowadniali swoją tożsamość.
- Mechanizmy autoryzacji, które ograniczają użytkowników do określonych działań i danych.

Zapory uniemożliwiają dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

Na poniższej ilustracji przedstawiono, w jaki sposób ustawić zaporę serwera w SQL Database:

![Reguły zapory](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Usługa Azure SQL Database jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do bazy danych SQL z komputera, należy się upewnić, że Zapora komputera klienckiego zezwala na wychodzącą komunikację TCP na porcie TCP 1433. Blokuj połączenia przychodzące na porcie TCP 1433 przy użyciu reguł zapory, jeśli nie są potrzebne te połączenia dla innych aplikacji.

W ramach procesu połączenia połączenia z maszyn wirtualnych platformy Azure są przekierowywane na adres IP i port, które są unikatowe dla każdej roli procesu roboczego. Numer portu należy do zakresu od 11000 do 11999. Aby uzyskać więcej informacji na temat portów TCP, zobacz [porty powyżej 1433 dla ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Oprócz reguł adresów IP Zapora zarządza regułami sieci wirtualnej. Reguły sieci wirtualnej są oparte na punktach końcowych usługi sieci wirtualnej. Reguły sieci wirtualnej mogą być preferowane z regułami adresów IP w niektórych przypadkach. Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej i reguły dla Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Włącz uwierzytelnianie bazy danych
SQL Database obsługuje dwa typy uwierzytelniania, uwierzytelnianie SQL Server i uwierzytelnianie usługi Azure AD.

### <a name="sql-server-authentication"></a>*Uwierzytelnianie SQL Server*

Oto przykładowe korzyści:

- Umożliwia SQL Database do obsługi środowisk z mieszanymi systemami operacyjnymi, w przypadku których wszyscy użytkownicy nie są uwierzytelniani przez domenę systemu Windows.
- Umożliwia SQL Database obsługi starszych aplikacji i aplikacji dostarczonych przez partnerów, które wymagają uwierzytelniania SQL Server.
- Umożliwia użytkownikom łączenie się z nieznanych lub niezaufanych domen. Przykładem jest aplikacja, w której ustanowioni klienci nawiązują połączenie z przypisanymi SQL Server logowaniami, aby otrzymywać stan ich zamówień.
- Umożliwia SQL Database obsługi aplikacji opartych na sieci Web, w których użytkownicy tworzą własne tożsamości.
- Umożliwia deweloperom oprogramowania dystrybuowanie swoich aplikacji przy użyciu złożonej hierarchii uprawnień na podstawie znanych, wstępnie ustawionych SQL Server nazw logowania.

> [!NOTE]
> Uwierzytelnianie SQL Server nie może korzystać z protokołu zabezpieczeń protokołu Kerberos.

Jeśli używasz uwierzytelniania SQL Server, musisz:

- Samodzielne zarządzanie silnymi poświadczeniami.
- Zabezpiecz poświadczenia w parametrach połączenia.
- (Potencjalnie) Chroń poświadczenia przesyłane przez sieć z serwera sieci Web do bazy danych programu. Aby uzyskać więcej informacji, zobacz [jak: Połącz się z SQL Server przy użyciu uwierzytelniania SQL w](/previous-versions/msp-n-p/ff648340(v=pandp.10))ASP.NET 2,0.

### <a name="azure-active-directory-ad-authentication"></a>*Uwierzytelnianie Azure Active Directory (AD)*
Uwierzytelnianie w usłudze Azure AD to mechanizm łączenia się z Azure SQL Database i [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure AD. Uwierzytelnianie za pomocą usługi Azure AD umożliwia zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie IDENTYFIKATORami zapewnia pojedyncze miejsce do zarządzania użytkownikami bazy danych i upraszcza zarządzanie uprawnieniami.

> [!NOTE]
> Zalecamy używanie uwierzytelniania usługi Azure AD w ramach uwierzytelniania SQL Server.

Oto przykładowe korzyści:

- Stanowi alternatywę dla SQL Server uwierzytelniania.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach baz danych.
- Umożliwia obrót hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Może on wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez Azure Active Directory.
- Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z SQL Database.
- Obsługuje ona AD FS (Federacja domen) lub natywne uwierzytelnianie użytkownika/hasła dla lokalnego wystąpienia Azure Active Directory bez synchronizacji domeny.
- Usługa Azure AD obsługuje połączenia z SQL Server Management Studio korzystających z Active Directory uwierzytelniania uniwersalnego, które obejmuje uwierzytelnianie wieloskładnikowe. Uwierzytelnianie wieloskładnikowe zapewnia silne uwierzytelnianie z zakresu opcji weryfikacji — połączenia telefonicznego, wiadomości tekstowej, kart inteligentnych z numerem PIN lub powiadomień aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz temat [Obsługa uwierzytelniania wieloskładnikowego w usłudze Azure AD przy użyciu SQL Database i SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Czynności konfiguracyjne obejmują następujące procedury dotyczące konfigurowania i używania uwierzytelniania usługi Azure AD:

- Utwórz i wypełnij usługę Azure AD.
- Opcjonalnie: Skojarz lub Zmień wystąpienie Active Directory, które jest aktualnie skojarzone z subskrypcją platformy Azure.
- Utwórz Azure Active Directory administratora Azure SQL Database lub [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Skonfiguruj komputery klienckie.
- Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD.
- Nawiąż połączenie z bazą danych za pomocą tożsamości usługi Azure AD.

Szczegółowe informacje można znaleźć w temacie [używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu SQL Database, wystąpienia zarządzanego lub SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Ochrona danych przy użyciu szyfrowania i zabezpieczeń na poziomie wierszy
[Azure SQL Database przezroczyste szyfrowanie danych](../../sql-database/transparent-data-encryption-azure-sql.md) pomaga chronić dane na dysku i chroni przed nieautoryzowanym dostępem do sprzętu. Wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w czasie rzeczywistym bez konieczności wprowadzania zmian w aplikacji. Przezroczyste szyfrowanie danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych.

Nawet jeśli cały magazyn jest szyfrowany, ważne jest również szyfrowanie samej bazy danych. Jest to implementacja podejścia do kompleksowej ochrony danych. Jeśli używasz Azure SQL Database i chcesz chronić poufne dane (np. karty kredytowe lub numery ubezpieczenia społecznego), możesz szyfrować bazy danych z zweryfikowaną wersją standardu FIPS 140-2 256-bitowym szyfrowaniem AES. To szyfrowanie spełnia wymagania wielu standardów branżowych (np. HIPAA i PCI).

Pliki powiązane z [rozszerzeniem puli buforów (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) nie są szyfrowane podczas szyfrowania bazy danych przy użyciu funkcji przezroczystego szyfrowania danych. Należy użyć narzędzi do szyfrowania na poziomie systemu plików, takich jak [Funkcja BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) lub [System szyfrowania plików (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) dla plików związanych z BPE.

Ponieważ autoryzowany użytkownik, taki jak administrator zabezpieczeń lub administrator bazy danych, może uzyskać dostęp do danych nawet wtedy, gdy baza danych jest zaszyfrowana przy użyciu funkcji przezroczystego szyfrowania danych, należy również wykonać następujące zalecenia:

- Włącz uwierzytelnianie SQL Server na poziomie bazy danych.
- Użyj uwierzytelniania usługi Azure AD przy użyciu [ról RBAC](/azure/role-based-access-control/overview).
- Upewnij się, że użytkownicy i aplikacje używają osobnych kont do uwierzytelniania. W ten sposób można ograniczyć uprawnienia przyznawane użytkownikom i aplikacjom oraz zmniejszyć ryzyko złośliwych działań.
- Implementowanie zabezpieczeń na poziomie bazy danych przy użyciu stałych ról bazy danych (takich jak db_datareader lub db_datawriter). Możesz też utworzyć niestandardowe role dla aplikacji, aby udzielić jawnych uprawnień do wybranych obiektów bazy danych.

Aby uzyskać inne sposoby zabezpieczania danych, należy rozważyć następujące kwestie:

- [Szyfrowanie na poziomie komórki](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), dzięki czemu klienci mogą szyfrować poufne dane wewnątrz aplikacji klienckich i nigdy nie ujawniać kluczy szyfrowania do aparatu bazy danych (SQL Database lub SQL Server). W związku z tym Always Encrypted zapewnia rozdzielenie między osobami, które są właścicielami danych (i mogą je wyświetlać) oraz tymi, które zarządzają danymi (ale nie powinny mieć dostępu).
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security), które umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika, który wykonuje zapytanie. (Przykładowe cechy są przynależność do grupy i kontekst wykonywania).

Organizacje, które nie używają szyfrowania na poziomie bazy danych, mogą być bardziej podatne na ataki, które naruszają dane znajdujące się w bazach danych SQL.

Więcej informacji na temat SQL Database przezroczystego szyfrowania danych można znaleźć w artykule [transparent Data Encryption z Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Włącz inspekcję bazy danych
Inspekcja wystąpienia aparatu bazy danych SQL Server lub pojedynczej bazy danych obejmuje śledzenie i rejestrowanie zdarzeń. W przypadku SQL Server można utworzyć inspekcje zawierające specyfikacje dla zdarzeń na poziomie serwera i specyfikacji dla zdarzeń na poziomie bazy danych. Zdarzenia inspekcji można zapisywać w dziennikach zdarzeń lub w celu inspekcji plików.

Istnieje kilka poziomów inspekcji dla SQL Server, w zależności od wymagań dla instytucji rządowych lub standardowych związanych z instalacją. Inspekcja SQL Server udostępnia narzędzia i procesy umożliwiające Włączanie, przechowywanie i wyświetlanie inspekcji dla różnych obiektów serwera i bazy danych.

[Inspekcja Azure SQL Database](/azure/sql-database/sql-database-auditing) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.

Inspekcja pomaga zachować zgodność z przepisami, zrozumieć aktywność bazy danych i znaleźć niezgodności i anomalie, które mogą wskazywać na problemy biznesowe lub naruszenia zabezpieczeń. Inspekcja ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje zgodności.

Aby dowiedzieć się więcej o inspekcji bazy danych i sposobie jej włączania, zobacz [Rozpoczynanie pracy z inspekcją bazy danych SQL](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Włącz wykrywanie zagrożeń bazy danych
Ochrona przed zagrożeniami wykracza poza wykrywanie. Ochrona bazy danych przed zagrożeniami obejmuje:

- Odnajdywanie i klasyfikowanie najbardziej poufnych danych, dzięki czemu możesz chronić dane.
- Implementowanie bezpiecznych konfiguracji w bazie danych, dzięki czemu możesz chronić swoją bazę danych.
- Wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, aby można było szybko reagować i skorygować.

**Najlepsze rozwiązanie**: Odkryj, klasyfikuj i Oznacz poufne dane w bazach danych.   
**Szczegóły**: Klasyfikowanie danych w bazie danych SQL przez włączenie [odnajdowania i klasyfikacji danych](/azure/sql-database/sql-database-data-discovery-and-classification) w Azure SQL Database. Możesz monitorować dostęp do poufnych danych na pulpicie nawigacyjnym platformy Azure lub pobierać raporty.

**Najlepsze rozwiązanie**: Śledź luki w zabezpieczeniach bazy danych, aby umożliwić proaktywne ulepszanie zabezpieczeń bazy danych.   
**Szczegóły**: Użyj usługi [oceny luk](/azure/sql-database/sql-vulnerability-assessment) w zabezpieczeniach Azure SQL Database, która skanuje potencjalne luki w zabezpieczeniach bazy danych. Usługa korzysta z bazy wiedzy o regułach, które flagą luki w zabezpieczeniach i pokazują odchylenia od najlepszych rozwiązań, takich jak niepotrzebne konfiguracje, nadmierne uprawnienia i niechronione dane poufne.

Zasady są oparte na najlepszych rozwiązaniach firmy Microsoft i koncentrują się na problemach z bezpieczeństwem, które stanowią największe ryzyko dla bazy danych i jej cennych danych. Obejmują one zarówno problemy na poziomie bazy danych, jak i problemy z zabezpieczeniami na poziomie serwera, takie jak ustawienia zapory serwera i uprawnienia na poziomie serwera. Te reguły reprezentują także wiele wymagań od organów prawnych do spełnienia ich standardów zgodności.

**Najlepsze rozwiązanie**: Włącz wykrywanie zagrożeń.  
**Szczegóły**:  Włącz [wykrywanie zagrożeń](/azure/sql-database/sql-database-threat-detection) Azure SQL Database, aby uzyskać alerty zabezpieczeń i zalecenia dotyczące sposobu badania i łagodzenia zagrożeń. Otrzymujesz alerty dotyczące podejrzanych działań bazy danych, potencjalnych luk w zabezpieczeniach i ataków wstrzykiwania kodu SQL, a także nietypowego dostępu do bazy danych i wzorców zapytań.

[Zaawansowana ochrona przed zagrożeniami](/azure/sql-database/sql-advanced-threat-protection) to ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Obejmuje ona wymienione wcześniej usługi: Odnajdywanie i Klasyfikacja danych, Ocena luk w zabezpieczeniach i wykrywanie zagrożeń. Zapewnia ona jedną lokalizację do włączania i zarządzania tymi funkcjami.

Włączenie tych funkcji ułatwia:

- Poznaj standardy prywatności danych i wymagania dotyczące zgodności z przepisami.
- Kontroluj dostęp do baz danych i ograniczaj ich bezpieczeństwo.
- Monitoruj dynamiczne środowisko bazy danych, w którym zmiany są trudne do śledzenia.
- Wykrywanie potencjalnych zagrożeń i reagowanie na nie.

Ponadto wykrywanie zagrożeń integruje alerty z Azure Security Center, aby uzyskać centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure.

## <a name="enable-feature-restrictions"></a>Włącz ograniczenia funkcji

Dane zawarte w bazach danych mogą być narażone osobom atakującym przy użyciu wektorów ataków, które wykorzystują błędy bazy danych i czasy wykonywania zapytań. Azure SQL Database udostępnia wiele mechanizmów ograniczeń funkcji chroniących bazę danych. Aby dowiedzieć się więcej, zobacz [SQL Database ograniczenia dotyczące funkcji](/azure/sql-database/sql-database-feature-restrictions).

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce dotyczące zabezpieczeń platformy Azure](best-practices-and-patterns.md) , aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, które są używane podczas projektowania i wdrażania rozwiązań w chmurze oraz zarządzania nimi przy użyciu platformy Azure.

Dostępne są następujące zasoby umożliwiające dostarczenie bardziej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu ds. zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — na bieżąco z najnowszymi informacjami na temat zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku których luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, mogą być zgłaszane lub wysyłane pocztą e-mailsecure@microsoft.com
