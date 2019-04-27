---
title: Omówienie zabezpieczeń usługi Azure database | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie usługi Azure database funkcje zabezpieczeń.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587654"
---
# <a name="azure-database-security-overview"></a>Omówienie zabezpieczeń usługi Azure database

Zabezpieczenia są szczególnie ważne dla zarządzania bazami danych i zawsze było priorytet dla usługi Azure SQL Database. Usługa Azure SQL Database obsługuje zabezpieczenia połączeń przy użyciu reguł zapory i szyfrowania połączenia. Obsługuje ona uwierzytelnianie za pomocą nazwy użytkownika i hasła i uwierzytelniania usługi Azure Active Directory (Azure AD), która korzysta z tożsamości zarządzanej przez usługę Azure Active Directory. Autoryzacja używa kontroli dostępu opartej na rolach.

Usługa Azure SQL Database obsługuje szyfrowanie, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji.

Firma Microsoft udostępnia dodatkowe sposoby szyfrowania danych przedsiębiorstwa:

-   Szyfrowanie na poziomie komórki jest dostępny do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   Jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania, należy wziąć pod uwagę przy użyciu usługi Azure Key Vault z programem SQL Server na maszynie wirtualnej (VM) platformy Azure.
-   Zawsze zaszyfrowane (obecnie w wersji zapoznawczej) sprawia, że szyfrowanie przezroczyste dla aplikacji. Umożliwia klientom szyfrowanie cennych danych wewnątrz aplikacji klienckich bez udostępniania kluczy szyfrowania w usłudze SQL Database.

Usługa Azure SQL Database Auditing umożliwia przedsiębiorstwom rekord, który inspekcji w dzienniku w usłudze Azure Storage. Usługa SQL Database Auditing jest również zintegrowana z usługą Microsoft Power BI, co ułatwia wykonywanie szczegółowych raportów i analiz.

Baz danych SQL Azure, które mogą być ściśle chronione w spełniają najbardziej prawnych lub wymagań dotyczących zabezpieczeń, takich jak HIPAA, ISO 27001/27002 i PCI DSS poziom 1. Bieżącą listę certyfikatów zgodności zabezpieczeń znajduje się w temacie [w witrynie Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/services/).

W tym artykule przedstawiono podstawowe informacje o zabezpieczeniach baz danych Microsoft Azure SQL dla danych ze strukturą, tabelaryczne i relacyjnych. W szczególności opisano rozpoczęcie pracy z zasobami na potrzeby ochrony danych, kontrolowania dostępu i aktywnego monitorowania.

## <a name="protection-of-data"></a>Ochrona danych

Bazy danych SQL Database pomaga w zabezpieczaniu danych, zapewniając szyfrowania:

- W przypadku danych w ruchu za pośrednictwem [zabezpieczeń TLS (Transport Layer)](https://support.microsoft.com/kb/3135244).
- Dla danych magazynowanych za pomocą [technologii transparent data encryption](https://go.microsoft.com/fwlink/?LinkId=526242).
- Do danych za pośrednictwem [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Można również rozważyć inne sposoby szyfrowania danych:

-   [Szyfrowanie na poziomie komórki](https://msdn.microsoft.com/library/ms179331.aspx) służące do szyfrowania określonej kolumny lub nawet poszczególnych komórek danych przy użyciu różnych kluczy szyfrowania.
-   [Usługa Azure Key Vault z programem SQL Server w Maszynie wirtualnej platformy Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), jeśli potrzebujesz sprzętowego modułu zabezpieczeń lub centralnego zarządzania hierarchią kluczy szyfrowania.

### <a name="encryption-in-motion"></a>Szyfrowanie w ruchu

Typowy problem dla wszystkich aplikacji typu klient/serwer jest konieczność zachowania, jak dane są przenoszone w sieciach publicznych i prywatnych. Jeśli danych przenoszonych przez sieć nie jest zaszyfrowany, istnieje prawdopodobieństwo, że mogą być przechwytywane i kradzieży przez nieautoryzowanych użytkowników. Gdy jesteś zajmujących się usługi bazy danych, upewnij się, że dane są szyfrowane od bazy danych klienta i serwera. Również upewnić się, że dane są szyfrowane, między serwerami bazy danych, które komunikują się ze sobą oraz z aplikacjami warstwy środkowej.

Jednym z problemów podczas administrowania siecią jest zabezpieczanie danych przesyłanych między aplikacjami przez niezaufaną sieć. Możesz użyć [protokołów TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) do uwierzytelnienia serwerów i klientów, a następnie użyć go do szyfrowania wiadomości między uwierzytelnionymi stronami.

W procesie uwierzytelniania klient TLS/SSL wysyła wiadomość do serwera TLS/SSL. W odpowiedzi serwer podaje informacje, które serwer musi zostać uwierzytelniony. Klient i serwer dodatkowo wymieniają się kluczami sesji, a dialog uwierzytelniania kończy się. Po zakończeniu uwierzytelniania między serwerem a klientem może rozpocząć komunikacja zabezpieczona protokołem SSL przy użyciu kluczy szyfrowania symetrycznego, które są ustalane w procesie uwierzytelniania.

Wszystkie połączenia z usługą Azure SQL Database wymagają szyfrowania (TLS/SSL) w cały czas danych "w trakcie przesyłania" do i z bazy danych. SQL Database przy użyciu protokołu TLS/SSL do uwierzytelnienia serwerów i klientów, a następnie użyć go do szyfrowania wiadomości między uwierzytelnionymi stronami. 

W parametrach połączenia aplikacji należy określić parametry, aby szyfrować połączenia, a nie ufać certyfikatowi serwera. (To jest wykonywane po skopiowaniu parametrów połączenia z witryny Azure portal.) W przeciwnym razie połączenie nie zweryfikuje tożsamości serwera i będzie podatne na ataki "man-in--middle". W przypadku sterownika ADO.NET, na przykład te parametry połączenia są `Encrypt=True` i `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Możesz wykonać kilka kroków, aby ułatwić zabezpieczanie bazy danych. Na przykład projektowanie bezpieczny system szyfrowania poufnych zasobów i tworzenie zapory wokół serwerów bazy danych. Jednak w scenariuszu, gdzie kradzieży nośnik fizyczny (np. dysków lub taśm kopii zapasowych), złośliwa strona może po prostu przywrócić lub dołączyć bazę danych i przeglądać dane.

Jest jedno z rozwiązań do szyfrowania poufnych danych w bazie danych i chronić klucze, które są używane do szyfrowania danych przy użyciu certyfikatu. To rozwiązanie zapobiega każdy bez kluczy przy użyciu danych, ale muszą być planowane tego rodzaju ochrony.

Aby rozwiązać ten problem, SQL Server i obsługi bazy danych SQL [technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Przezroczyste szyfrowanie danych szyfruje programu SQL Server i SQL Database plików danych, znane jako szyfrowanie danych magazynowanych.

Przezroczyste szyfrowanie danych pomaga zapewnić ochronę przed złośliwymi działaniami. Wykonuje w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian do aplikacji.  

Przezroczyste szyfrowanie danych szyfruje magazyn całą bazę danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych. W bazie danych SQL klucz szyfrowania bazy danych jest chroniony za pomocą wbudowanego serwera certyfikatu. Certyfikat wbudowanego serwera jest unikatowy dla każdego serwera bazy danych SQL.

Jeśli baza danych jest w relacji Geo-DR, jest chroniony przez inny klucz na każdym serwerze. Jeśli dwie bazy danych są podłączone do tego samego serwera, współużytkują one ten sam certyfikat wbudowanych. Microsoft automatycznie przełącza tych certyfikatów, co 90 dni. 

Aby uzyskać więcej informacji, zobacz [technologii Transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Szyfrowanie w użyciu (klient)

Większości naruszeń danych obejmują kradzież krytyczne dane, takie jak numery kart kredytowych lub identyfikowalnych danych osobowych. Bazy danych może być troves macguffina poufnych informacji. Zawierają one dane osobowe (np. narodowe numery identyfikacyjne), informacje poufne konkurencyjne i praw własności intelektualnej klientów. Zgubione lub skradzione, dane szczególnie klienta, może spowodować uszkodzenie marki, konkurencji i poważne kar — nawet pozwy.

![Funkcja Always Encrypted, przedstawione jako blokady i klucza](./media/azure-databse-security-overview/azure-database-fig1.png)

[Zawsze szyfrowane](https://msdn.microsoft.com/library/mt163865.aspx) jest funkcją zaprojektowanych w celu ochrony danych poufnych przechowywanych w bazach danych Azure SQL Database lub SQL Server. Funkcja Always Encrypted umożliwia klientom szyfrowanie cennych danych wewnątrz aplikacji klienckich i nigdy nie ujawniania kluczy szyfrowania z aparatem bazy danych (bazy danych SQL Database lub SQL Server).

Zawsze zaszyfrowane zapewnia oddzielenie osób, które są właścicielami danych (i mogą je wyświetlać) i osób, które zarządzać danymi (ale nie powinny mieć dostępu). Pomaga upewnić się, czy lokalnej bazy danych, Administratorzy, operatory bazy danych w chmurze lub innych wysokim poziomem uprawnień, ale nieautoryzowanym użytkownikom nie może uzyskać dostępu do zaszyfrowanych danych.

Ponadto Always Encrypted sprawia, że szyfrowanie przezroczyste dla aplikacji. Z włączoną funkcją Always Encrypted sterownik jest zainstalowany na komputerze klienckim, tak, aby go automatycznie szyfrowania i odszyfrowywania poufnych danych w aplikacji klienckiej. Sterownik szyfruje dane w kolumnach poufnych przed przekazaniem ich z aparatem bazy danych. Sterownik automatycznie ponownie zapisuje zapytania, tak aby zostały zachowane semantyki do aplikacji. Podobnie sterownika przezroczyste odszyfrowuje danych przechowywanych w kolumnach szyfrowanej bazy danych, zawarte w wynikach kwerendy.

## <a name="access-control"></a>Kontrola dostępu

Aby zapewnić ochronę, SQL Database kontroluje dostęp za pomocą:

- Reguły zapory, które ograniczają połączenia przy użyciu adresu IP.
- Mechanizmy uwierzytelniania wymagające użytkowników potwierdzić swoją tożsamość.
- Mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych.

### <a name="database-access"></a>Dostęp do bazy danych

Ochrona danych rozpoczyna się od kontrolowanie dostępu do danych. Centrum danych, które hostuje dane zarządza fizyczny dostęp. Można skonfigurować zapory w celu zarządzania zabezpieczeniami w warstwie sieciowej. Można także kontrolować dostęp przez skonfigurowanie identyfikatorów logowania dla uwierzytelniania oraz Ustawianie uprawnień dla ról serwera i bazy danych.

#### <a name="firewall-and-firewall-rules"></a>Zapora i reguły zapory

[Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/) udostępnia usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania. Aby uzyskać więcej informacji, zobacz [Omówienie reguł zapory usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Usługa Azure SQL Database jest dostępna wyłącznie za pośrednictwem portu TCP 1433. Dostęp do bazy danych SQL z komputera, upewnij się, że Zapora komputera klienta umożliwia wychodzący ruch TCP na porcie TCP 1433. W przypadku połączeń przychodzących, nie są wymagane przez inne aplikacje, należy je zablokować na porcie TCP 1433.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:

-   **Uwierzytelnianie programu SQL Server**: Konto jednej nazwy logowania jest tworzone podczas tworzenia logicznego wystąpienia SQL o nazwie konto subskrybenta usługi SQL Database. To konto łączy się za pomocą [uwierzytelniania programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nazwa użytkownika i hasło). To konto jest administratorem wystąpienia serwera logicznego i wszystkich baz danych użytkownika dołączonych do tego wystąpienia. Uprawnień konta subskrybenta nie może być ograniczony. Może istnieć tylko jedno z tych kont.
-   **Uwierzytelnianie usługi Azure Active Directory**: [Uwierzytelnianie usługi Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) jest mechanizmem nawiązywania połączenia z usługą Azure SQL Database i Azure SQL Data Warehouse przy użyciu tożsamości w usłudze Azure AD. Służy on do centralne zarządzanie tożsamościami użytkowników bazy danych.

![Uwierzytelnianie usługi Azure AD z usługą SQL Database](./media/azure-databse-security-overview/azure-database-fig2.png)

 Korzyści wynikające z uwierzytelniania usługi Azure AD obejmują:
  - Jego stanowi alternatywę dla uwierzytelniania programu SQL Server.
  - On pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami bazy danych i umożliwia rotacja hasła w jednym miejscu.
  - Uprawnienia bazy danych można zarządzać za pomocą grup zewnętrznych (Azure AD).
  - Je wyeliminować zapisywania haseł, włączając zintegrowane uwierzytelnianie Windows i innych metod uwierzytelniania, który obsługuje usługi Azure AD.

#### <a name="authorization"></a>Autoryzacja

[Autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) odwołuje się do co użytkownik może zrobić w ramach usługi Azure SQL database. Jest to kontrolowane przez konto użytkownika bazy danych [członkostwa w roli](https://msdn.microsoft.com/library/ms189121) i [uprawnienia na poziomie obiektu](https://msdn.microsoft.com/library/ms191291.aspx). Autoryzacja to proces określania, że zasoby zabezpieczanego podmiot zabezpieczeń mogą uzyskiwać dostęp do i jakie operacje są dozwolone dla tych zasobów.

### <a name="application-access"></a>Dostęp do aplikacji

#### <a name="dynamic-data-masking"></a>Dynamiczne maskowanie danych

Z przedstawicielem w Centrum połączenia może identyfikować obiekty wywołujące przez kilka cyfry numeru karty kredytowej lub numer ubezpieczenia społecznego. Jednak te elementy danych nie należy całkowicie uwidaczniać do przedstawiciela biura obsługi.

Można zdefiniować regułę maskowania maskuje wszystkie z wyjątkiem cztery ostatnie cyfry numeru karty kredytowej w zestawie wyników dowolnego zapytania lub numer ubezpieczenia społecznego.

![Maskowanie na wielu wysyłanych między aplikacjami SQL database i biznesowych](./media/azure-databse-security-overview/azure-database-fig3.png)

Inny przykład można zdefiniować maskę odpowiednie dane do ochrony danych osobowych. Deweloper może następnie utworzyć zapytanie względem środowisk produkcyjnych na potrzeby rozwiązywania problemów bez naruszania kryteria zgodności z przepisami.

Funkcja [dynamicznego maskowania danych w bazie danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ujawnianie poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników. Dynamiczne maskowanie danych jest obsługiwana dla usługi Azure SQL Database w wersji V12.

[Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ułatwia zapobieganie nieautoryzowanemu dostępowi do danych poufnych, umożliwiając wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

> [!Note]
> Dynamiczne maskowanie danych można skonfigurować przez administratora bazy danych Azure, administrator serwera lub ról ds. zabezpieczeń.

#### <a name="row-level-security"></a>Zabezpieczenia na poziomie wierszy

Innym często spotykanym wymaganiem zabezpieczeń dla wielodostępnych baz danych jest [zabezpieczenia na poziomie wiersza](https://msdn.microsoft.com/library/dn765131.aspx). Ta funkcja służy do kontrolowania dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika, który wykonuje kwerendę. (Przykład cechy są grupy członkostwa i wykonywanie kontekstu).

![Umożliwianie użytkownikowi dostępu do wierszy w tabeli za pomocą aplikacji klienckiej zabezpieczenia na poziomie wiersza](./media/azure-databse-security-overview/azure-database-fig4.png)

Logika ograniczeń dostępu jest znajduje się w warstwie bazy danych, a nie od danych w innej warstwie aplikacji. Za każdym razem, gdy podejmowana jest próba tego dostępu do danych z dowolnej warstwy system bazy danych mają zastosowanie ograniczenia dostępu. Dzięki temu system zabezpieczeń bardziej niezawodne i niezawodne, zmniejszając prawdopodobieństwo systemu zabezpieczeń.

Zabezpieczenia na poziomie wiersza wprowadza kontroli dostępu na podstawie predykatu. Zawiera funkcje elastyczne, scentralizowane wersję próbną, która może wziąć pod uwagę metadanych lub inne kryteria, które administrator określa odpowiednio. Predykat jest używany jako kryterium do określenia, czy użytkownik ma odpowiedni dostęp do danych na podstawie atrybutów użytkownika. Kontrola dostępu oparta na etykietę można zaimplementować przy użyciu kontroli dostępu na podstawie predykatu.

## <a name="proactive-monitoring"></a>Aktywne monitorowanie

Bazy danych SQL Database pomaga w zabezpieczaniu danych, zapewniając *inspekcji* i *wykrywanie zagrożeń* możliwości.

### <a name="auditing"></a>Inspekcja

[Funkcja inspekcji usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) zwiększa możliwości, aby uzyskać wgląd w zdarzenia i zmiany, które występują w bazie danych. Przykładami są aktualizacje i zapytania dotyczące danych.

SQL Database auditing śledzi zdarzenia bazy danych i zapisuje je do inspekcji logowania na koncie magazynu platformy Azure. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych i uzyskać wgląd w odchylenia i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń. Inspekcja umożliwia i ułatwia zgodności ze standardami zgodności, ale nie gwarantuje zgodności.

Możesz użyć inspekcji do bazy danych SQL:

- **Zachowaj** dziennik inspekcji wybranych zdarzeń. Można zdefiniować kategorie działań bazy danych powinien być poddany inspekcji.
- **Raport** aktywność bazy danych. Wstępnie skonfigurowane raporty i pulpit nawigacyjny umożliwia szybkie rozpoczynanie pracy z działaniem i raportowanie zdarzeń.
- **Analizowanie** raportów. Możesz znaleźć podejrzanych zdarzeń, nietypowej aktywności i trendów.

Istnieją dwie metody inspekcji:

-   **Inspekcja obiektów blob**: Dzienniki są zapisywane w magazynie Azure Blob. Jest nowsza metoda inspekcji. Go zapewnia wyższą wydajność, obsługuje wyższy poziom szczegółowości na poziomie obiektu inspekcji i jest bardziej opłacalna.
-   **Inspekcja tabeli**: Dzienniki są zapisywane w usłudze Azure Table storage.

### <a name="threat-detection"></a>Wykrywanie zagrożeń

[Zaawansowana ochrona przed zagrożeniami dla bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) wykrywa podejrzane działania, które wskazują potencjalne zagrożenia bezpieczeństwa. Wykrywanie zagrożeń służy do reagowania na podejrzane zdarzenia w bazie danych, takie jak wstrzyknięcia kodu SQL w momencie ich wystąpienia. Zapewnia alerty i umożliwia korzystanie z usługi Azure SQL Database inspekcji eksplorować podejrzane zdarzenia.

![Wykrywanie zagrożeń dla bazy danych SQL Database i aplikację internetową, zewnętrzna osoba atakująca i złośliwe oprogramowanie](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL zaawansowane Threat Protection (ATP) zawiera zestaw zaawansowanych funkcji zabezpieczeń programu SQL, w tym odnajdywanie danych i klasyfikacji, ocena luk w zabezpieczeniach i wykrywanie zagrożeń. 

- [Odnajdywanie i klasyfikacja danych](../sql-database/sql-database-data-discovery-and-classification.md)
- [Ocena luk w zabezpieczeniach](../sql-database/sql-vulnerability-assessment.md)  
- [Wykrywanie zagrożeń](../sql-database/sql-database-threat-detection.md)

[Usługa Azure Database for postgresql w warstwie zaawansowanej ochrony przed zagrożeniami](../postgresql/concepts-data-access-and-security-threat-protection.md) stanowi nową warstwę zabezpieczeń, która umożliwia wykrywanie oraz reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku wystąpienia nietypowych działań. Użytkownicy otrzymują alert po podejrzanych działaniach bazy danych oraz potencjalnych luk w zabezpieczeniach, a także wzorce dostępu i zapytania nietypowego bazy danych. Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for PostgreSQL integruje alerty z usługą Azure Security Center. Typów alertów, obejmują:

- Dostęp z nietypowej lokalizacji
- Dostęp do centrum danych platformy Azure 
- Dostęp z nieznanego podmiotu zabezpieczeń 
- Dostęp z potencjalnie szkodliwej aplikacji 
- Atak siłowy usługa Azure database for postgresql — poświadczeń 

[Usługa Azure Database for MySQL — Zaawansowana ochrona przed zagrożeniami](../mysql/concepts-data-access-and-security-threat-protection.md) zapewnia ochronę, podobnie jak PostgreSQL zaawansowanej ochrony.  

## <a name="centralized-security-management"></a>Zarządzanie zabezpieczeniami scentralizowane

Usługa [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomaga w zapobieganiu zagrożeniom, wykrywaniu ich i reagowaniu na nie. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Usługa Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) pomaga chronić dane w bazie danych SQL, zapewniając wgląd w zabezpieczenia serwerów i baz danych. Usługa Security Center możesz wykonywać następujące czynności:

- Definiuj zasady szyfrowania bazy danych SQL i inspekcji.
- Monitorowanie zabezpieczeń zasobów bazy danych SQL dla wszystkich swoich subskrypcji.
- Szybkie identyfikowanie i rozwiązywanie problemów dotyczących bezpieczeństwa.
- Integruj alerty z [wykrywania zagrożeń usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Usługa Security Center obsługuje dostępu opartej na rolach.

## <a name="sql-information-protection"></a>Usługa SQL Information Protection

[Usługa SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) automatycznie odnajduje i klasyfikuje potencjalnie poufnych danych, udostępnia mechanizm etykietowania trwałe tagowanie poufnych danych za pomocą atrybutów klasyfikacji i zapewnia wyświetlanie szczegółowego pulpitu nawigacyjnego Stan klasyfikacji bazy danych.  

Ponadto oblicza wynik ustawić czułość zapytań SQL, dzięki czemu mogą jawnie inspekcji zapytań, które umożliwiają wyodrębnianie danych poufnych, a dane mogą być chronione. Aby uzyskać szczegółowe informacje na temat SQL Information Protection Zobacz odnajdywanie danych bazy danych SQL Azure i klasyfikacji.

Można skonfigurować [zasad SQL Information Protection](../security-center/security-center-info-protection-policy.md) w usłudze Azure Security Center.

## <a name="azure-marketplace"></a>Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług w trybie online, który umożliwia początkującym i niezależnym dostawcom oprogramowania oferowanie swoich rozwiązań klientom platformy Azure na całym świecie.
W portalu Azure Marketplace łączy ekosystemy partnerów Microsoft Azure w to ujednolicona platforma, aby lepiej spełniała klientów i partnerów. Możesz [uruchomić przeszukiwanie](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) do wyświetlania produktów zabezpieczeń bazy danych jest dostępna w witrynie Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

- [Zabezpieczanie usługi Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Usługa Azure Security Center i Azure SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Wykrywanie zagrożeń bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Zwiększ wydajność bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
