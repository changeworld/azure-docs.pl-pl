---
title: Omówienie zabezpieczeń bazy danych platformy Azure| Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie funkcji zabezpieczeń bazy danych platformy Azure.
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
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906058"
---
# <a name="azure-database-security-overview"></a>Omówienie zabezpieczeń bazy danych platformy Azure

Zabezpieczenia jest głównym problemem dla zarządzania bazami danych i zawsze był priorytetem dla usługi Azure SQL Database. Usługa Azure SQL Database obsługuje zabezpieczenia połączeń z regułami zapory i szyfrowaniem połączeń. Obsługuje uwierzytelnianie przy użyciu nazwy użytkownika i hasła oraz uwierzytelniania usługi Azure Active Directory (Azure AD), które używa tożsamości zarządzanych przez usługę Azure Active Directory. Autoryzacja używa kontroli dostępu opartej na rolach.

Usługa Azure SQL Database obsługuje szyfrowanie, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie baz danych, skojarzonych kopii zapasowych i plików dziennika transakcji w spoczynku bez konieczności zmiany w aplikacji.

Firma Microsoft udostępnia dodatkowe sposoby szyfrowania danych przedsiębiorstwa:

-   Szyfrowanie na poziomie komórki jest dostępne do szyfrowania określonych kolumn, a nawet komórek danych za pomocą różnych kluczy szyfrowania.
-   Jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania, rozważ użycie usługi Azure Key Vault z programem SQL Server na maszynie wirtualnej platformy Azure(VM).
-   Zawsze szyfrowane (obecnie w wersji zapoznawczej) sprawia, że szyfrowanie jest przezroczyste dla aplikacji. Umożliwia również klientom szyfrowanie poufnych danych wewnątrz aplikacji klienckich bez udostępniania kluczy szyfrowania z bazą danych SQL.

Inspekcja bazy danych SQL platformy Azure umożliwia przedsiębiorstwom rejestrowanie zdarzeń w dzienniku inspekcji w usłudze Azure Storage. Usługa SQL Database Auditing jest również zintegrowana z usługą Microsoft Power BI, co ułatwia wykonywanie szczegółowych raportów i analiz.

Bazy danych SQL platformy Azure mogą być ściśle zabezpieczone, aby spełnić większość wymagań regulacyjnych lub zabezpieczeń, w tym HIPAA, ISO 27001/27002 i PCI DSS Level 1. Aktualna lista certyfikatów zgodności zabezpieczeń jest dostępna w [witrynie Centrum zaufania platformy Microsoft Azure](https://azure.microsoft.com/support/trust-center/services/).

W tym artykule oszukuje się podstawy zabezpieczania baz danych SQL platformy Microsoft Azure dla danych strukturalnych, tabelaryczne i relacyjnych. W szczególności opisano rozpoczęcie pracy z zasobami na potrzeby ochrony danych, kontrolowania dostępu i aktywnego monitorowania.

## <a name="protection-of-data"></a>Ochrona danych

Baza danych SQL pomaga zabezpieczyć dane, zapewniając szyfrowanie:

- W przypadku danych w ruchu za pośrednictwem [protokołu TLS (Transport Layer Security).](https://support.microsoft.com/kb/3135244)
- Dla danych w stanie spoczynku poprzez [przezroczyste szyfrowanie danych](https://go.microsoft.com/fwlink/?LinkId=526242).
- Dla danych używanych za pośrednictwem [zawsze zaszyfrowane](https://msdn.microsoft.com/library/mt163865.aspx).

Można również rozważyć inne sposoby szyfrowania danych:

-   [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   [Usługa Azure Key Vault z programem SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)— jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania.

### <a name="encryption-in-motion"></a>Szyfrowanie w ruchu

Częstym problemem dla wszystkich aplikacji klienta/serwera jest potrzeba zachowania prywatności w miarę przenoszenia danych przez sieci publiczne i prywatne. Jeśli dane przesyłane przez sieć nie są szyfrowane, istnieje prawdopodobieństwo, że mogą zostać przechwycone i skradzione przez nieautoryzowanych użytkowników. Gdy masz do czynienia z usługami bazy danych, upewnij się, że dane są szyfrowane między klientem bazy danych a serwerem. Upewnij się również, że dane są szyfrowane między serwerami bazy danych, które komunikują się ze sobą i z aplikacjami warstwy środkowej.

Jednym z problemów podczas administrowania siecią jest zabezpieczanie danych wysyłanych między aplikacjami przez niezaufaną sieć. Za pomocą [protokołu TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) można uwierzytelniać serwery i klientów, a następnie używać go do szyfrowania wiadomości między uwierzytelnionymi stronami.

W procesie uwierzytelniania klient protokołu TLS/SSL wysyła wiadomość do serwera TLS/SSL. Serwer odpowiada na informacje, które serwer musi się uwierzytelnić. Klient i serwer dodatkowo wymieniają się kluczami sesji, po czym dialog uwierzytelniania kończy się. Po zakończeniu uwierzytelniania komunikacja zabezpieczona protokołem SSL może rozpocząć się między serwerem a klientem za pośrednictwem symetrycznych kluczy szyfrowania, które są ustanawiane podczas procesu uwierzytelniania.

Wszystkie połączenia z usługą Azure SQL Database wymagają szyfrowania (TLS/SSL) przez cały czas, gdy dane są "przesyłane" do i z bazy danych. Baza danych SQL używa protokołu TLS/SSL do uwierzytelniania serwerów i klientów, a następnie do szyfrowania wiadomości między uwierzytelnionymi stronami. 

W ciągu połączenia aplikacji należy określić parametry do szyfrowania połączenia, a nie ufać certyfikat serwera. (Jest to wykonywane dla Ciebie, jeśli kopiujesz ciąg połączenia z witryny Azure portal.) W przeciwnym razie połączenie nie zweryfikuje tożsamości serwera i będzie podatne na ataki typu "man-in-the-middle". Na przykład dla ADO.NET sterownika te parametry ciągu `Encrypt=True` `TrustServerCertificate=False`połączenia są i .

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Można podjąć kilka środków ostrożności, aby pomóc zabezpieczyć bazę danych. Na przykład zaprojektuj bezpieczny system, zaszyfruj poufne zasoby i zbuduj zaporę wokół serwerów bazy danych. Ale w scenariuszu, w którym nośniki fizyczne (takie jak dyski lub taśmy kopii zapasowej) są skradzione, złośliwa strona może po prostu przywrócić lub dołączyć bazę danych i przeglądać dane.

Jednym z rozwiązań jest szyfrowanie poufnych danych w bazie danych i chronienie kluczy, które są używane do szyfrowania danych za pomocą certyfikatu. To rozwiązanie uniemożliwia nikomu bez kluczy korzystanie z danych, ale tego rodzaju ochrony należy zaplanować.

Aby rozwiązać ten problem, SQL Server i baza danych SQL obsługują [przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Przezroczyste szyfrowanie danych szyfruje pliki danych SQL Server i SQL Database, znane jako dane szyfrowania w spoczynku.

Przezroczyste szyfrowanie danych pomaga chronić przed zagrożeniem złośliwą aktywnością. Ta technologia w czasie rzeczywistym szyfruje i odszyfrowuje magazynowaną bazę danych, skojarzone kopie zapasowe i pliki dzienników transakcji bez konieczności wprowadzania jakichkolwiek zmian w aplikacji.  

Przezroczyste szyfrowanie danych szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucz szyfrowania bazy danych. W bazie danych SQL klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera bazy danych SQL.

Jeśli baza danych znajduje się w relacji Geo-DR, jest chroniona przez inny klucz na każdym serwerze. Jeśli dwie bazy danych są połączone z tym samym serwerem, współużytkują ten sam wbudowany certyfikat. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. 

Aby uzyskać więcej informacji, zobacz [Przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Szyfrowanie w użyciu (klient)

Większość naruszeń danych wiąże się z kradzieżą krytycznych danych, takich jak numery kart kredytowych lub dane osobowe. Bazy danych mogą być skarbnicami poufnych informacji. Mogą one zawierać dane osobowe klientów (takie jak krajowe numery identyfikacyjne), poufne informacje o konkurencji oraz własność intelektualną. Zagubione lub skradzione dane, zwłaszcza dane klientów, mogą spowodować uszkodzenie marki, niekorzystną sytuację konkurencyjną i poważne grzywny - nawet pozwy sądowe.

![Funkcja Zawsze szyfrowana zilustrowana jako blokada i klawisz](./media/database-security-overview/azure-database-fig1.png)

[Zawsze szyfrowane](https://msdn.microsoft.com/library/mt163865.aspx) to funkcja przeznaczona do ochrony poufnych danych przechowywanych w bazach danych azure SQL Database lub SQL Server. Zawsze szyfrowane umożliwia klientom szyfrowanie poufnych danych wewnątrz aplikacji klienckich i nigdy nie ujawniają kluczy szyfrowania do aparatu bazy danych (bazy danych SQL lub PROGRAMU SQL Server).

Zawsze zaszyfrowane zapewnia separację między osobami, które są właścicielami danych (i mogą je wyświetlać) a osobami, które zarządzają danymi (ale nie powinny mieć dostępu). Pomaga zapewnić, że administratorzy lokalnej bazy danych, operatorzy chmurowej bazy danych lub inni użytkownicy o wysokich uprawnieniach, ale nieautoryzowani, nie mogą uzyskać dostępu do zaszyfrowanych danych.

Ponadto zawsze szyfrowane sprawia, że szyfrowanie jest przezroczyste dla aplikacji. Sterownik z włączoną funkcją Zawsze szyfrowana jest zainstalowany na komputerze klienckim, dzięki czemu może automatycznie szyfrować i odszyfrowywać poufne dane w aplikacji klienckiej. Sterownik szyfruje dane w poufnych kolumnach przed przekazaniem danych do aparatu bazy danych. Sterownik automatycznie przepisuje kwerendy, tak aby semantyka do aplikacji są zachowywane. Podobnie sterownik w sposób przejrzysty odszyfrowuje dane, przechowywane w zaszyfrowanych kolumnach bazy danych, zawarte w wynikach kwerendy.

## <a name="access-control"></a>Kontrola dostępu

Aby zapewnić bezpieczeństwo, baza danych SQL kontroluje dostęp przy użyciu:

- Reguły zapory, które ograniczają łączność według adresu IP.
- Mechanizmy uwierzytelniania, które wymagają od użytkowników udowodnienia swojej tożsamości.
- Mechanizmy autoryzacji, które ograniczają użytkowników do określonych akcji i danych.

### <a name="database-access"></a>Dostęp do bazy danych

Ochrona danych rozpoczyna się od kontrolowania dostępu do twoich danych. Centrum danych, w którym hostuje się dane, zarządza dostępem fizycznym. Zaporę można skonfigurować do zarządzania zabezpieczeniami w warstwie sieciowej. Można również kontrolować dostęp, konfigurując logowania do uwierzytelniania i definiowanie uprawnień dla ról serwera i bazy danych.

#### <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

[Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/) zapewnia relacyjne usługi bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory usługi Azure SQL Database](/azure/sql-database/sql-database-firewall-configure).

Usługa Azure SQL Database jest dostępna tylko za pośrednictwem portu TCP 1433. Aby uzyskać dostęp do bazy danych SQL z komputera, upewnij się, że zapora komputera klienckiego umożliwia wychodzącą komunikację TCP na porcie TCP 1433. Jeśli połączenia przychodzące nie są potrzebne dla innych aplikacji, zablokuj je w porcie TCP 1433.

#### <a name="authentication"></a>Uwierzytelnianie

Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:

-   **Uwierzytelnianie programu SQL Server:** Podczas tworzenia logicznego wystąpienia SQL tworzone jest pojedyncze konto logowania, nazywane kontem subskrybenta bazy danych SQL. To konto łączy się przy użyciu [uwierzytelniania programu SQL Server](/azure/sql-database/sql-database-security-overview) (nazwa użytkownika i hasło). To konto jest administratorem wystąpienia serwera logicznego i wszystkich baz danych użytkownika dołączonych do tego wystąpienia. Uprawnienia konta subskrybenta nie mogą być ograniczone. Może istnieć tylko jedno z tych kont.
-   **Uwierzytelnianie usługi Azure Active Directory:** [Uwierzytelnianie usługi Azure AD](/azure/sql-database/sql-database-aad-authentication) to mechanizm łączenia się z usługą Azure SQL Database i usługi Azure SQL Data Warehouse przy użyciu tożsamości w usłudze Azure AD. Można go używać do centralnego zarządzania tożsamościami użytkowników bazy danych.

![Uwierzytelnianie usługi Azure AD z bazą danych SQL](./media/database-security-overview/azure-database-fig2.png)

 Zalety uwierzytelniania usługi Azure AD obejmują:
  - Zapewnia alternatywę dla uwierzytelniania programu SQL Server.
  - Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych i umożliwia obracanie hasła w jednym miejscu.
  - Uprawnienia bazy danych można zarządzać przy użyciu zewnętrznych grup (Azure AD).
  - Można wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania, które obsługuje usługa Azure AD.

#### <a name="authorization"></a>Autoryzacja

[Autoryzacja](/azure/sql-database/sql-database-manage-logins) odnosi się do tego, co użytkownik może zrobić w bazie danych SQL platformy Azure. Jest kontrolowana przez członkostwo w [roli](https://msdn.microsoft.com/library/ms189121) bazy danych konta użytkownika i [uprawnienia na poziomie obiektu.](https://msdn.microsoft.com/library/ms191291.aspx) Autoryzacja jest procesem określania, które zabezpieczalne zasoby podmiotu zabezpieczeń mogą uzyskać dostęp i które operacje są dozwolone dla tych zasobów.

### <a name="application-access"></a>Dostęp do aplikacji

#### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

Przedstawiciel działu obsługi w centrum obsługi telefonicznej może identyfikować osoby dzwoniące za pomocą kilku cyfr numeru ubezpieczenia społecznego lub numeru karty kredytowej. Jednak te elementy danych nie powinny być w pełni narażone na przedstawiciela usługi.

W zestawie wyników można zdefiniować regułę maskowania, która maskuje wszystkie z tych ostatnich czterech cyfr numeru ubezpieczenia społecznego lub numeru karty kredytowej.

![Maskowanie numeru wysyłanego między bazą danych SQL a aplikacjami biznesowymi](./media/database-security-overview/azure-database-fig3.png)

Innym przykładem można zdefiniować odpowiednią maskę danych w celu ochrony informacji umożliwiających identyfikację użytkownika. Deweloper może następnie wysyłać zapytania do środowisk produkcyjnych w celu rozwiązywania problemów bez naruszania przepisów dotyczących zgodności.

Funkcja [dynamicznego maskowania danych w bazie danych SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dynamiczne maskowanie danych jest obsługiwane dla wersji V12 usługi Azure SQL Database.

[Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) pomaga zapobiegać nieautoryzowanemu dostępowi do poufnych danych, umożliwiając określenie, ile poufnych danych ma zostać ujawnionych przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

> [!Note]
> Dynamiczne maskowanie danych może być skonfigurowane przez role administratora usługi Azure Database, administratora serwera lub oficera zabezpieczeń.

#### <a name="row-level-security"></a>Zabezpieczenia na poziomie wiersza

Innym typowym wymogiem zabezpieczeń dla wielodostępnych baz danych jest [zabezpieczona na poziomie wiersza](https://msdn.microsoft.com/library/dn765131.aspx). Za pomocą tej funkcji można kontrolować dostęp do wierszy w tabeli bazy danych na podstawie cech użytkownika, który wykonuje kwerendę. (Przykładowe cechy to kontekst członkostwa w grupie i wykonywania).

![Zabezpieczenia na poziomie wiersza umożliwiające użytkownikowi dostęp do wierszy w tabeli za pośrednictwem aplikacji klienckiej](./media/database-security-overview/azure-database-fig4.png)

Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie z dala od danych w innej warstwie aplikacji. System bazy danych stosuje ograniczenia dostępu za każdym razem, gdy próbowano uzyskać dostęp do danych z dowolnej warstwy. Dzięki temu system zabezpieczeń jest bardziej niezawodny i niezawodny dzięki zmniejszeniu powierzchni systemu zabezpieczeń.

Zabezpieczenia na poziomie wiersza wprowadzają kontrolę dostępu opartą na predykacie. Posiada elastyczną, scentralizowaną ocenę, która może uwzględniać metadane lub inne kryteria, które administrator określa odpowiednio. Predykat jest używany jako kryterium, aby określić, czy użytkownik ma odpowiedni dostęp do danych na podstawie atrybutów użytkownika. Można zaimplementować kontrolę dostępu opartą na etykietach przy użyciu kontroli dostępu opartej na predykacie.

## <a name="proactive-monitoring"></a>Aktywne monitorowanie

Baza danych SQL pomaga zabezpieczyć dane, zapewniając możliwości *inspekcji* i *wykrywania zagrożeń.*

### <a name="auditing"></a>Inspekcja

[Inspekcja usługi Azure SQL Database](/azure/sql-database/sql-database-auditing-get-started) zwiększa możliwość uzyskania wglądu w zdarzenia i zmiany, które występują w bazie danych. Przykładami są aktualizacje i zapytania dotyczące danych.

Inspekcja bazy danych SQL śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure. Inspekcja może pomóc w zachowaniu zgodności z przepisami, zrozumieniu aktywności w bazie danych i uzyskaniu wglądu w rozbieżności i anomalie, które mogą wskazywać na obawy biznesowe lub podejrzewane naruszenia zabezpieczeń. Inspekcja umożliwia i ułatwia przestrzeganie standardów zgodności, ale nie gwarantuje zgodności.

Inspekcja bazy danych SQL umożliwia:

- **Zachowaj** ścieżkę inspekcji wybranych zdarzeń. Możesz zdefiniować kategorie akcji bazy danych, które mają być poddane inspekcji.
- **Raport** dotyczący aktywności bazy danych. Możesz użyć wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby szybko rozpocząć pracę z raportowaniem aktywności i zdarzeń.
- **Analizuj** raporty. Można znaleźć podejrzane zdarzenia, nietypową aktywność i trendy.

Istnieją dwie metody inspekcji:

-   **Inspekcja obiektów blob:** Dzienniki są zapisywane w magazynie obiektów Blob platformy Azure. Jest to nowsza metoda inspekcji. Zapewnia wyższą wydajność, obsługuje inspekcję na poziomie obiektu o wyższej szczegółowości i jest bardziej opłacalna.
-   **Inspekcja tabel:** Dzienniki są zapisywane w magazynie tabel platformy Azure.

### <a name="threat-detection"></a>Wykrywanie zagrożeń

[Zaawansowana ochrona przed zagrożeniami dla usługi Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) wykrywa podejrzane działania, które wskazują potencjalne zagrożenia bezpieczeństwa. Wykrywanie zagrożeń można użyć do reagowania na podejrzane zdarzenia w bazie danych, takie jak zastrzyki SQL, gdy występują. Zapewnia alerty i umożliwia korzystanie z inspekcji usługi Azure SQL Database do eksplorowania podejrzanych zdarzeń.

![Wykrywanie zagrożeń dla bazy danych SQL i aplikacji sieci web z zewnętrznym atakiem i złośliwym niejawnym testerem](./media/database-security-overview/azure-database-fig5.jpg)

Usługa SQL Advanced Threat Protection (ATP) zapewnia zestaw zaawansowanych funkcji zabezpieczeń SQL, w tym odnajdowanie danych & klasyfikacji, ocenie luk w zabezpieczeniach i wykrywaniu zagrożeń. 

- [Odnajdowanie danych & klasyfikacji](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Ocena luk w zabezpieczeniach](/azure/sql-database/sql-vulnerability-assessment)  
- [Wykrywanie zagrożeń](/azure/sql-database/sql-database-threat-detection)

[Usługa Azure Database for PostgreSQL Advanced Threat Protection](/azure/postgresql/concepts-data-access-and-security-threat-protection) zapewnia nową warstwę zabezpieczeń, która umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania przez dostarczanie alertów zabezpieczeń dotyczących nietypowych działań. Użytkownicy otrzymują alert o podejrzanych działaniach bazy danych i potencjalnych lukach w zabezpieczeniach, a także nietypowych wzorcach dostępu do bazy danych i zapytań. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for PostgreSQL integruje alerty z usługą Azure Security Center. Typ alertów obejmuje:

- Dostęp z nietypowej lokalizacji
- Dostęp z nietypowego centrum danych platformy Azure 
- Dostęp od nieznanego głównego zobowiązanego 
- Dostęp z potencjalnie szkodliwej aplikacji 
- Brute wymuś bazę danych platformy Azure dla poświadczeń PostgreSQL 

[Usługa Azure Database for MySQL Advanced Threat Protection](/azure/mysql/concepts-data-access-and-security-threat-protection) zapewnia ochronę podobną do ochrony zaawansowanej PostgreSQL.  

## <a name="centralized-security-management"></a>Scentralizowane zarządzanie zabezpieczeniami

Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Usługa Security Center](../../security-center/security-center-alerts-data-services.md) pomaga chronić dane w bazie danych SQL, zapewniając wgląd w zabezpieczenia wszystkich serwerów i baz danych. Dzięki Centrum zabezpieczeń możesz:

- Definiowanie zasad szyfrowania i inspekcji bazy danych SQL.
- Monitoruj bezpieczeństwo zasobów bazy danych SQL we wszystkich subskrypcjach.
- Szybko identyfikuj i remediaj problemy z zabezpieczeniami.
- Integruj alerty z [wykrywania zagrożeń bazy danych SQL platformy Azure](/azure/sql-database/sql-database-threat-detection).

Usługa Security Center obsługuje dostęp oparty na rolach.

## <a name="sql-information-protection"></a>Ochrona informacji SQL

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) automatycznie odnajduje i klasyfikuje potencjalnie poufne dane, zapewnia mechanizm etykietowania dla trwałego tagowania poufnych danych za pomocą atrybutów klasyfikacji i zapewnia szczegółowy pulpit nawigacyjny przedstawiający stan klasyfikacji bazy danych.  

Ponadto oblicza czułość zestawu wyników zapytań SQL, dzięki czemu kwerendy, które wyodrębniają poufne dane mogą być jawnie kontrolowane, a dane mogą być chronione. Aby uzyskać więcej informacji na temat ochrony informacji SQL, zobacz odnajdowanie i klasyfikacja danych bazy danych SQL azure.

Zasady ochrony [informacji SQL](/azure/security-center/security-center-info-protection-policy) można skonfigurować w usłudze Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług online, który umożliwia start-upom i niezależnym dostawcom oprogramowania oferowanie swoich rozwiązań klientom platformy Azure na całym świecie.
Portal Azure Marketplace łączy ekosystemy partnerów platformy Microsoft Azure w zunifikowaną platformę, aby lepiej obsługiwać klientów i partnerów. Można [uruchomić wyszukiwanie,](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) aby wyświetlić produkty zabezpieczeń bazy danych dostępne w portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

- [Zabezpiecz swoją bazę danych SQL platformy Azure](/azure/sql-database/sql-database-security-tutorial)
- [Usługa Azure Security Center i usługa Azure SQL Database](/azure/security-center/security-center-sql-database)
- [Wykrywanie zagrożeń bazy danych SQL](/azure/sql-database/sql-database-threat-detection)
- [Zwiększanie wydajności bazy danych SQL](/azure/sql-database/sql-database-performance-tutorial)
