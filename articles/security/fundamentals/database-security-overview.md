---
title: Omówienie zabezpieczeń usługi Azure Database | Microsoft Docs
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń usługi Azure Database.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: dc4c36735ade8a094acbf897ee040a239a1bed48
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727412"
---
# <a name="azure-database-security-overview"></a>Omówienie zabezpieczeń usługi Azure Database

Zabezpieczenia to najważniejsze zagadnienia związane z zarządzaniem bazami danych i zawsze ma priorytet dla Azure SQL Database. Azure SQL Database obsługuje zabezpieczenia połączeń z regułami zapory i szyfrowaniem połączeń. Obsługuje ona uwierzytelnianie przy użyciu nazwy użytkownika i hasła oraz uwierzytelnianie Azure Active Directory (Azure AD), które używa tożsamości zarządzanych przez Azure Active Directory. Autoryzacja korzysta z kontroli dostępu opartej na rolach.

Azure SQL Database obsługuje szyfrowanie przez wykonywanie szyfrowania w czasie rzeczywistym oraz odszyfrowywanie baz danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

Firma Microsoft oferuje dodatkowe metody szyfrowania danych przedsiębiorstwa:

-   Szyfrowanie na poziomie komórki jest dostępne do szyfrowania określonych kolumn lub nawet komórek danych przy użyciu różnych kluczy szyfrowania.
-   Jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania, rozważ użycie Azure Key Vault z SQL Server na maszynie wirtualnej platformy Azure.
-   Always Encrypted (obecnie w wersji zapoznawczej) sprawia, że szyfrowanie jest przezroczyste dla aplikacji. Umożliwia ona również klientom szyfrowanie poufnych danych wewnątrz aplikacji klienckich bez udostępniania kluczy szyfrowania za pomocą SQL Database.

Inspekcja Azure SQL Database umożliwia przedsiębiorstwom rejestrowanie zdarzeń w dzienniku inspekcji w usłudze Azure Storage. Usługa SQL Database Auditing jest również zintegrowana z usługą Microsoft Power BI, co ułatwia wykonywanie szczegółowych raportów i analiz.

Bazy danych Azure SQL Database mogą być ściśle zabezpieczone, aby spełniały wymagania związane z przepisami lub zabezpieczeniami, w tym HIPAA, ISO 27001/27002 PCI DSS i poziom 1. Aktualna lista certyfikatów zgodności zabezpieczeń jest dostępna w [witrynie Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/services/).

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania Microsoft Azure baz danych SQL na potrzeby danych strukturalnych, tabelarycznych i relacyjnych. W szczególności opisano rozpoczęcie pracy z zasobami na potrzeby ochrony danych, kontrolowania dostępu i aktywnego monitorowania.

## <a name="protection-of-data"></a>Ochrona danych

SQL Database pomaga zabezpieczyć dane, zapewniając szyfrowanie:

- W przypadku danych w ruchu przez [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- W przypadku danych przechowywanych przy użyciu [przezroczystego szyfrowania danych](https://go.microsoft.com/fwlink/?LinkId=526242).
- Do danych używanych przez [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Można również rozważyć inne sposoby szyfrowania danych:

-   [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   [Azure Key Vault z SQL Server na maszynie wirtualnej platformy Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania.

### <a name="encryption-in-motion"></a>Szyfrowanie w ruchu

Typowy problem dotyczący wszystkich aplikacji klienta/serwera jest konieczny do zachowania poufności informacji, gdy dane są przemieszczane za pośrednictwem sieci publicznych i prywatnych. Jeśli dane przenoszone przez sieć nie są szyfrowane, istnieje szansa, że można ją przechwycić i skradzionie przez nieautoryzowanych użytkowników. W przypadku korzystania z usług bazy danych upewnij się, że dane są szyfrowane między klientem i serwerem bazy danych. Upewnij się również, że dane są szyfrowane między serwerami baz danych, które komunikują się ze sobą i z aplikacjami warstwy środkowej.

Jednym z problemów podczas administrowania siecią jest zabezpieczanie danych przesyłanych między aplikacjami za pośrednictwem niezaufanej sieci. Za pomocą [protokołu TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) można uwierzytelniać serwery i klientów, a następnie używać ich do szyfrowania komunikatów przesyłanych między uwierzytelnionymi stronami.

W procesie uwierzytelniania klient TLS/SSL wysyła komunikat do serwera TLS/SSL. Serwer reaguje na informacje, które serwer musi uwierzytelnić się. Klient i serwer programu wykonują dodatkową wymianę kluczy sesji i zostanie wyświetlone okno dialogowe uwierzytelniania. Po zakończeniu uwierzytelniania komunikacja zabezpieczona przy użyciu protokołu SSL może zaczynać się między serwerem a klientem za pośrednictwem kluczy szyfrowania symetrycznego, które są ustanawiane podczas procesu uwierzytelniania.

Wszystkie połączenia do Azure SQL Database wymagają szyfrowania (TLS/SSL) przez cały czas, gdy dane są przesyłane do i z bazy danych. SQL Database używa protokołu TLS/SSL do uwierzytelniania serwerów i klientów, a następnie do szyfrowania komunikatów między uwierzytelnionymi stronami. 

W parametrach połączenia aplikacji należy określić parametry szyfrowania połączenia, a nie ufać certyfikatowi serwera. (Jest to wykonywane w przypadku kopiowania parametrów połączenia z Azure Portal). W przeciwnym razie połączenie nie zweryfikuje tożsamości serwera i będzie podatne na ataki typu "man-in-the-middle". Na przykład dla sterownika ADO.NET parametry połączenia to `Encrypt=True` i. `TrustServerCertificate=False`

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Aby zabezpieczyć bazę danych, można wykonać kilka środków zaradczych. Na przykład Zaprojektuj bezpieczny system, Szyfruj poufne zasoby i Utwórz zaporę wokół serwerów baz danych. Jednak w przypadku kradzieży nośnika fizycznego (takiego jak dyski lub taśmy kopii zapasowych) złośliwa strona może po prostu przywrócić lub dołączyć bazę danych oraz przeglądać dane.

Jednym z rozwiązań jest zaszyfrowanie poufnych danych w bazie danych programu i ochrona kluczy używanych do szyfrowania danych za pomocą certyfikatu. To rozwiązanie uniemożliwia nikomu bez kluczy używanie danych, ale należy zaplanować ten rodzaj ochrony.

Aby rozwiązać ten problem, SQL Server i SQL Database obsługują [przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Szyfrowanie danych przezroczystych szyfruje SQL Server i SQL Database pliki danych, znane jako dane szyfrowania przechowywane w spoczynku.

Przezroczyste szyfrowanie danych pomaga chronić przed zagrożeniami złośliwych działań. Wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w czasie rzeczywistym bez konieczności wprowadzania zmian w aplikacji.  

Przezroczyste szyfrowanie danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego zwanego kluczem szyfrowania bazy danych. W SQL Database klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera SQL Database.

Jeśli baza danych znajduje się w relacji typu Geo-DR, jest ona chroniona przez inny klucz na każdym serwerze. Jeśli dwie bazy danych są połączone z tym samym serwerem, współużytkują one ten sam certyfikat wbudowany. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. 

Aby uzyskać więcej informacji, zobacz [przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Szyfrowanie w użyciu (klient)

Większość naruszeń danych obejmuje kradzież najważniejszych danych, takich jak numery kart kredytowych lub informacje umożliwiające identyfikację użytkownika. Bazy danych mogą być Skarbnik troves informacji poufnych. Mogą zawierać dane osobowe klientów (na przykład krajowe numery identyfikacyjne), poufne informacje o konkurencji i własność intelektualną. Zgubione lub skradzione dane, zwłaszcza dane klientów, mogą skutkować uszkodzeniem marki, konkurencją konkurencyjną i poważnymi grzywnami.

![Funkcja Always Encrypted zilustrowana jako blokada i klucz](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) to funkcja przeznaczona do ochrony poufnych danych przechowywanych w bazach danych Azure SQL Database lub SQL Server. Always Encrypted pozwala klientom szyfrować poufne dane wewnątrz aplikacji klienckich i nigdy nie ujawniać kluczy szyfrowania do aparatu bazy danych (SQL Database lub SQL Server).

Always Encrypted zapewnia rozdzielenie między osobami, które są właścicielami danych (i mogą je wyświetlać) oraz osobami, które zarządzają danymi (ale nie powinni mieć dostępu). Pomaga zapewnić, że lokalni administratorzy baz danych, operatorzy bazy danych w chmurze lub inni z wysokim poziomem uprawnień, ale nieautoryzowani użytkownicy nie mogą uzyskać dostępu do zaszyfrowanych danych.

Ponadto Always Encrypted sprawia, że szyfrowanie jest przezroczyste dla aplikacji. Sterownik z włączonym Always Encrypted jest instalowany na komputerze klienckim, dzięki czemu może automatycznie szyfrować i odszyfrowywać poufne dane w aplikacji klienckiej. Sterownik szyfruje dane w poufnych kolumnach przed przekazaniem danych do aparatu bazy danych. Sterownik automatycznie ponownie zapisuje zapytania, aby umożliwić zachowanie semantyki do aplikacji. Podobnie, sterownik w sposób przezroczysty odszyfrowuje dane przechowywane w kolumnach zaszyfrowanej bazy danych zawarte w wynikach zapytania.

## <a name="access-control"></a>Kontrola dostępu

Aby zapewnić bezpieczeństwo, SQL Database kontroluje dostęp przy użyciu:

- Reguły zapory, które ograniczają łączność przy użyciu adresu IP.
- Mechanizmy uwierzytelniania, które wymagają, aby użytkownicy udowadniali swoją tożsamość.
- Mechanizmy autoryzacji, które ograniczają użytkowników do określonych działań i danych.

### <a name="database-access"></a>Dostęp do bazy danych

Ochrona danych rozpoczyna się od kontrolowania dostępu do danych. Centrum danych, które hostuje dane, zarządza dostępem fizycznym. Zaporę można skonfigurować do zarządzania zabezpieczeniami w warstwie sieciowej. Można również kontrolować dostęp przez skonfigurowanie logowań do uwierzytelniania i definiowanie uprawnień dla ról serwera i bazy danych.

#### <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) zapewnia usługi relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory usługi Azure SQL Database](/azure/sql-database/sql-database-firewall-configure).

Usługa Azure SQL Database jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do bazy danych SQL z komputera, należy się upewnić, że Zapora komputera klienckiego zezwala na wychodzącą komunikację TCP na porcie TCP 1433. Jeśli połączenia przychodzące nie są potrzebne dla innych aplikacji, Zablokuj je na porcie TCP 1433.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:

-   **Uwierzytelnianie SQL Server**: Konto jednej nazwy logowania jest tworzone podczas tworzenia logicznego wystąpienia SQL o nazwie konto subskrybenta usługi SQL Database. To konto nawiązuje połączenie przy użyciu [uwierzytelniania SQL Server](/azure/sql-database/sql-database-security-overview) (nazwa użytkownika i hasło). To konto jest administratorem wystąpienia serwera logicznego i wszystkich baz danych użytkownika dołączonych do tego wystąpienia. Uprawnienia konta subskrybenta nie mogą być ograniczone. Może istnieć tylko jedno z tych kont.
-   **Uwierzytelnianie Azure Active Directory**: [Uwierzytelnianie w usłudze Azure AD](/azure/sql-database/sql-database-aad-authentication) to mechanizm łączenia się z Azure SQL Database i Azure SQL Data Warehouse przy użyciu tożsamości w usłudze Azure AD. Za jego pomocą można centralnie zarządzać tożsamościami użytkowników bazy danych.

![Uwierzytelnianie usługi Azure AD za pomocą SQL Database](./media/database-security-overview/azure-database-fig2.png)

 Zalety uwierzytelniania usługi Azure AD:
  - Stanowi alternatywę dla SQL Server uwierzytelniania.
  - Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach baz danych i umożliwia rotację haseł w jednym miejscu.
  - Uprawnieniami do bazy danych można zarządzać przy użyciu zewnętrznych grup (Azure AD).
  - Może on wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure AD.

#### <a name="authorization"></a>Authorization

[Autoryzacja](/azure/sql-database/sql-database-manage-logins) odnosi się do tego, co użytkownik może zrobić w usłudze Azure SQL Database. Jest ona kontrolowana przez [członkostwo w roli](https://msdn.microsoft.com/library/ms189121) bazy danych konta użytkownika i [uprawnienia na poziomie obiektów](https://msdn.microsoft.com/library/ms191291.aspx). Autoryzacja to proces określania, które zasoby, do których ma dostęp podmiot zabezpieczeń, i które operacje są dozwolone dla tych zasobów.

### <a name="application-access"></a>Dostęp do aplikacji

#### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

Przedstawiciel usługi w centrum telefonicznym może identyfikować wywoływania według kilku cyfr numerów ubezpieczenia społecznego lub numerów kart kredytowych. Jednak te elementy danych nie powinny być w pełni ujawnione dla przedstawiciela usługi.

Można zdefiniować regułę maskowania, która będzie maskować wszystkie, ale cztery ostatnie cyfry numeru ubezpieczenia społecznego lub numeru karty kredytowej w zestawie wyników dowolnych zapytań.

![Maskowanie w przypadku liczby wysyłanej między bazą danych SQL i aplikacjami biznesowymi](./media/database-security-overview/azure-database-fig3.png)

Innym przykładem może być zdefiniowanie odpowiedniej maski danych w celu ochrony informacji umożliwiających identyfikację użytkownika. Deweloper może następnie wysyłać zapytania dotyczące środowisk produkcyjnych w celu rozwiązywania problemów bez naruszania przepisów dotyczących zgodności.

Funkcja [dynamicznego maskowania danych w bazie danych SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dynamiczne maskowanie danych jest obsługiwane w przypadku wersji V12 Azure SQL Database.

[Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ułatwia Zapobieganie nieautoryzowanemu dostępowi do poufnych danych przez umożliwienie wyznaczenia ilości poufnych danych do ujawnienia przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

> [!Note]
> Dynamiczne maskowanie danych można skonfigurować za pomocą ról Administrator usługi Azure Database, administrator serwera lub oficer zabezpieczeń.

#### <a name="row-level-security"></a>Zabezpieczenia na poziomie wierszy

Inne typowe wymagania dotyczące zabezpieczeń dla wielodostępnych baz danych to [zabezpieczenia na poziomie wiersza](https://msdn.microsoft.com/library/dn765131.aspx). Za pomocą tej funkcji można kontrolować dostęp do wierszy w tabeli bazy danych w oparciu o charakterystykę użytkownika, który wykonuje zapytanie. (Przykładowe cechy są przynależność do grupy i kontekst wykonywania).

![Zabezpieczenia na poziomie wiersza umożliwiające użytkownikowi dostęp do wierszy w tabeli za pomocą aplikacji klienckiej](./media/database-security-overview/azure-database-fig4.png)

Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie na danych w innej warstwie aplikacji. System bazy danych stosuje ograniczenia dostępu za każdym razem, gdy próbuje się uzyskać dostęp do danych z dowolnej warstwy. Dzięki temu system zabezpieczeń jest bardziej niezawodny i niezawodny poprzez zmniejszenie powierzchni systemu zabezpieczeń.

Zabezpieczenia na poziomie wiersza wprowadzają kontrolę dostępu opartą na predykatach. Zawiera ona elastyczną, scentralizowaną ocenę, która może uwzględniać metadane lub inne kryteria, które administrator określi odpowiednio. Predykat jest używany jako kryterium, aby określić, czy użytkownik ma odpowiedni dostęp do danych na podstawie atrybutów użytkownika. Kontrolę dostępu opartą na etykietach można zaimplementować przy użyciu kontroli dostępu opartej na predykatach.

## <a name="proactive-monitoring"></a>Aktywne monitorowanie

SQL Database pomaga zabezpieczyć dane przez zapewnienie możliwości *inspekcji* i *wykrywania zagrożeń* .

### <a name="auditing"></a>Inspekcja

[Inspekcja Azure SQL Database](/azure/sql-database/sql-database-auditing-get-started) zwiększa możliwości uzyskiwania wglądu w zdarzenia i zmiany, które wystąpiły w bazie danych. Przykłady to aktualizacje i zapytania dotyczące danych.

Inspekcja SQL Database śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, zrozumieć aktywność bazy danych oraz uzyskać wgląd w niezgodności i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń. Inspekcja umożliwia i ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje zgodności.

Inspekcji SQL Database można użyć do:

- **Zachowaj** dziennik inspekcji dla wybranych zdarzeń. Można definiować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** dotyczący działania bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem działań i zdarzeń.
- **Analizuj** raporty. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

Istnieją dwie metody inspekcji:

-   **Inspekcja obiektów BLOB**: Dzienniki są zapisywane w magazynie Azure Blob. Jest to nowsza Metoda inspekcji. Zapewnia wyższą wydajność, obsługuje wyższe inspekcje na poziomie obiektów i jest tańsze.
-   **Inspekcja tabeli**: Dzienniki są zapisywane w usłudze Azure Table Storage.

### <a name="threat-detection"></a>Wykrywanie zagrożeń

[Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) wykrywa podejrzane działania wskazujące na potencjalne zagrożenia bezpieczeństwa. Możesz użyć wykrywania zagrożeń, aby odpowiedzieć na podejrzane zdarzenia w bazie danych, takie jak iniekcje SQL, gdy wystąpią. Zapewnia ona alerty i umożliwia korzystanie z inspekcji Azure SQL Database do eksplorowania podejrzanych zdarzeń.

![Wykrywanie zagrożeń dla SQL Database i aplikacji sieci Web przy użyciu zewnętrznego ataku i złośliwego testera](./media/database-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) oferuje zestaw zaawansowanych funkcji zabezpieczeń SQL, takich jak funkcja odnajdywania danych & klasyfikacji, oceny luk w zabezpieczeniach i wykrywania zagrożeń. 

- [Klasyfikacja & odnajdywania danych](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Ocena luk w zabezpieczeniach](/azure/sql-database/sql-vulnerability-assessment)  
- [Wykrywanie zagrożeń](/azure/sql-database/sql-database-threat-detection)

[Azure Database for PostgreSQL zaawansowaną ochronę przed zagrożeniami](/azure/postgresql/concepts-data-access-and-security-threat-protection) zapewnia nową warstwę zabezpieczeń, która umożliwia wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań. Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych oraz potencjalne luki w zabezpieczeniach, a także nietypowego dostępu do bazy danych i wzorców zapytań. Zaawansowana ochrona przed zagrożeniami dla Azure Database for PostgreSQL integruje alerty z Azure Security Center. Typy alertów obejmują:

- Dostęp z nietypowej lokalizacji
- Dostęp z nietypowego centrum danych platformy Azure 
- Dostęp z nieznanego podmiotu zabezpieczeń 
- Dostęp z potencjalnie szkodliwej aplikacji 
- Poświadczenia usługi Azure Database for PostgreSQL 

[Azure Database for MySQL zaawansowaną ochronę](/azure/mysql/concepts-data-access-and-security-threat-protection) przed zagrożeniami zapewnia ochronę podobną do PostgreSQL Advanced Protection.  

## <a name="centralized-security-management"></a>Scentralizowane zarządzanie zabezpieczeniami

Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby być niezauważalne, i współpracuje z szeroką ekosystemem rozwiązań w zakresie zabezpieczeń.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomaga chronić dane w SQL Database, zapewniając wgląd w zabezpieczenia wszystkich serwerów i baz danych. Za pomocą Security Center można:

- Zdefiniuj zasady dla SQL Database szyfrowania i inspekcji.
- Monitoruj zabezpieczenia SQL Database zasobów we wszystkich Twoich subskrypcjach.
- Szybkie identyfikowanie i rozwiązywanie problemów z zabezpieczeniami.
- Integruj alerty z [wykrywania Azure SQL Database zagrożeń](/azure/sql-database/sql-database-threat-detection).

Security Center obsługuje dostęp oparty na rolach.

## <a name="sql-information-protection"></a>Information Protection SQL

Funkcja [SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) automatycznie odnajduje i klasyfikuje potencjalnie poufne dane, zapewnia mechanizm etykietowania do trwałego tagowania poufnych danych przy użyciu atrybutów klasyfikacji i udostępnia szczegółowy pulpit nawigacyjny pokazujący stan klasyfikacji bazy danych.  

Ponadto oblicza on czułość zestawu wyników zapytań SQL, tak aby zapytania, które wyodrębniają poufne dane, mogły być jawnie poddane inspekcji, a dane mogą być chronione. Aby uzyskać więcej informacji na temat Information Protection SQL, zobacz Azure SQL Database odnajdywania i klasyfikowania danych.

[Zasady Information Protection SQL](/azure/security-center/security-center-info-protection-policy) można skonfigurować w programie Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług w trybie online, który umożliwia początkującym i niezależnym dostawcom oprogramowania oferowanie swoich rozwiązań klientom platformy Azure na całym świecie.
Portal Azure Marketplace łączy ekosystemy partnerów Microsoft Azure na ujednoliconą platformę w celu lepszego obsłużynia klientów i partnerów. Możesz [uruchomić wyszukiwanie](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) , aby wyświetlić produkty zabezpieczeń bazy danych dostępne w portalu Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

- [Zabezpieczanie bazy danych Azure SQL Database](/azure/sql-database/sql-database-security-tutorial)
- [Usługa Azure Security Center i Azure SQL Database](/azure/security-center/security-center-sql-database)
- [SQL Database wykrywanie zagrożeń](/azure/sql-database/sql-database-threat-detection)
- [Poprawianie wydajności bazy danych SQL](/azure/sql-database/sql-database-performance-tutorial)
