---
title: Zarządzanie bazami danych z pojedynczych i puli po migracji — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzanie bazą danych po migracji do usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: a83bc6518409add8a0732e5a0b17ab46c36564af
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358416"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Nowe DBA w chmurze — zarządzanie pojedynczymi i puli baz danych w usłudze Azure SQL Database

Przenoszenie z tradycyjnych zarządza własnym, własnym kontrolowanym środowisku w środowisku PaaS może wydawać się nieco trudne na początku. Jako Deweloper aplikacji lub administratorem baz danych należy zawsze znasz podstawowe możliwości platformy, które może pomóc w prace nad aplikacją, które są dostępne, wydajne, bezpieczne i odporne na błędy —. Ten artykuł ma na celu dokładnie. Artykuł krótkiej formie organizuje zasoby i zawiera wskazówki dotyczące jak najlepiej wykorzystać kluczowe funkcje usługi SQL Database za pomocą jednego i puli baz danych do zarządzania i prace nad aplikacją, wydajne działanie i uzyskania optymalnych wyników w chmurze. Typowe odbiorców, w tym artykule byłoby tych, którzy:

- Ocenia migrację swoich aplikacjach, do usługi Azure SQL Database — Modernizujesz swoje aplikacje.
- Trwa migrowanie swoich aplikacjach — scenariusz migracji w toku.
- Niedawno ukończone migracji do usługi Azure SQL DB — nowe DBA w chmurze.

W tym artykule omówiono niektóre z podstawowych cech usługi Azure SQL Database jako platformy, które mogą oni podczas pracy z pojedynczymi bazami danych i baz danych w puli w elastycznej puli. Są to następujące czynności:

- Business ciągłości działania i odzyskiwania po awarii (BCDR)
- Zabezpieczenia i zgodność z przepisami
- Inteligentna baza danych monitorowania i konserwacji
- Przenoszenie danych

> [!NOTE]
> Ten artykuł dotyczy następujących opcji wdrażania w usłudze Azure SQL Database: pojedyncze bazy danych i pul elastycznych. Nie ma zastosowania do opcji wdrożenia wystąpienia zarządzanego w bazie danych SQL.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Business ciągłości działania i odzyskiwania po awarii (BCDR)

Możliwości odzyskiwania ciągłość działalności biznesowej i odzyskiwanie po awarii firm umożliwiają kontynuować swoją firmę w zwykły sposób, w razie awarii. Po awarii może być zdarzeń na poziomie bazy danych (na przykład ktoś przez pomyłkę spada kluczowym tabeli) lub centrum danych zdarzenia poziomu (katastrofy regionalnych, na przykład tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak utworzyć i zarządzanie kopiami zapasowymi w bazie danych SQL

Nie twórz kopie zapasowe w usłudze Azure SQL DB i to, ponieważ nie masz. SQL Database automatycznie tworzy kopie zapasowe baz danych, dzięki czemu możesz już nie muszą przejmować planowania, wykonywania i zarządzanie kopiami zapasowymi. Platforma przyjmuje pełna kopia zapasowa co tydzień, różnicowej kopii zapasowej co kilka godzin i dziennika kopii zapasowej co 5 minut, aby upewnić się, że odzyskiwanie po awarii jest wydajny i minimalne utrata danych. Pierwsza pełna kopia zapasowa odbywa się, jak utworzyć bazę danych. Te kopie zapasowe są udostępniane użytkownikowi przez pewien okres o nazwie "Okres przechowywania" i zależy od warstwy usługi, którą wybierzesz. SQL Database udostępnia możliwość przywracania do dowolnego punktu w czasie w ramach tego przechowywania okresu przy użyciu [punktu w czasie odzyskiwania (Odzyskiwanie)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Warstwa usług|Czas przechowywania w dniach|
|---|:---:|
|Podstawowa|7|
|Standardowa (Standard)|35|
|Premium|35|
|||

Ponadto [długoterminowe przechowywanie (od lewej do prawej)](sql-database-long-term-retention.md) funkcja służy do przechowywania na pliki kopii zapasowych znacznie dłuższy okres w szczególności dla maksymalnie 10 lat i przywrócić dane z tych kopii zapasowych w dowolnym momencie, w tym okresie. Ponadto kopie zapasowe bazy danych są przechowywane w georeplikowanym magazynie, aby zapewnić odporność na błędy z regionalnym klęskami. Można także przywrócić te kopie zapasowe w dowolnym regionie systemu Azure w dowolnym momencie w okresie przechowywania. Zobacz [omówienie ciągłości działania](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak zapewnić ciągłość biznesową na wypadek poziomie centrum danych po awarii lub regionalnych katastrofy

Ponieważ kopie zapasowe bazy danych są przechowywane w georeplikowanym magazynie, aby upewnić się, że w przypadku regionalnej awarii, można przywrócić kopii zapasowej do innego regionu platformy Azure. Jest to funkcja przywracania geograficznego. RPO (cel punktu odzyskiwania) w tym jest zwykle < 1 godziny i ERT (szacowane godzina odzyskiwania — kilka minut, godzin).

W przypadku baz danych o kluczowym znaczeniu w bazy danych SQL Azure oferuje, aktywnej replikacji geograficznej. To zasadniczo działania jest, że tworzy ono replikowanej geograficznie pomocniczej kopii oryginalnej bazy danych w innym regionie. Na przykład, jeśli baza danych początkowo znajduje się w regionie platformy Azure zachodnie stany USA i chcesz regionalnych odporności. W regionie zachodnie stany USA, wschodnie stany USA powiedzieć, należy utworzyć repliki bazy danych dla aktywnej replikacji geograficznej. Gdy calamity awaria w regionie zachodnie stany USA, możesz przełączać awaryjnie do regionu wschodnie stany USA. Konfigurując je w grupie automatyczny tryb failover jest jeszcze lepsza, ponieważ gwarantuje to, że baza danych automatycznie przełącza do regionu pomocniczego w regionie wschodnie stany USA, w razie awarii. Cel punktu odzyskiwania dla tego jest < 5 sekund i ERT < 30 sekund.

Jeśli nie skonfigurowano grupę automatyczny tryb failover, aplikacja musi aktywnie monitorować pod kątem awarii i zainicjuj tryb failover do regionu pomocniczego. Możesz utworzyć maksymalnie 4 takich active geo replik w różnych regionach platformy Azure. Pobiera on jeszcze lepsze. Można również uzyskać dostęp do tych active geo pomocnicza dostępu tylko do odczytu. To jest dostępna w bardzo przydatna do zmniejszenia opóźnienia dla scenariusza aplikacji rozproszonej geograficznie.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak Mój plan odzyskiwania po awarii zmienia się ze środowiska lokalnego do usługi SQL Database

Podsumowując, tradycyjnych lokalnych programu SQL Server wymagane ustawienia, aktywnie zarządzać poziomu dostępności za pomocą funkcji, takich jak klaster pracy awaryjnej, dublowanie bazy danych, replikacji transakcji lub wysyłania dzienników oraz obsługa i zarządzanie kopiami zapasowymi, aby upewnić się Ciągłość prowadzenia działalności biznesowej. Usługa SQL Database platformy zarządza to dla Ciebie, dzięki czemu można skupić się na opracowywaniu i optymalizowania aplikacji bazy danych i nie martw się o zarządzanie po awarii, jaka. Może mieć kopię zapasową i skonfigurować plany odzyskiwania po awarii i Praca z zaledwie kilku kliknięć w witrynie Azure portal (lub kilka poleceń, za pomocą interfejsów API programu PowerShell).

Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz: [Odzyskiwanie po awarii bazy danych Azure SQL 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpieczenia i zgodność z przepisami

Bazy danych SQL trwa bardzo poważnie zabezpieczeń i prywatności. Zabezpieczenia w bazie danych SQL jest dostępna na poziomie bazy danych, jak i na poziomie platformy i najlepiej jest zrozumiałe, gdy dzieli na kilku warstw. W każdej warstwie uzyskujesz do kontroli i zapewnia optymalną bezpieczeństwa aplikacji. Dostępne są następujące warstwy:

- Tożsamość i uwierzytelnianie ([uwierzytelnianie Windows/SQL i uwierzytelnianie usługi Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitorowanie aktywności ([inspekcji](sql-database-auditing.md) i [wykrywanie zagrożeń](sql-database-threat-detection.md)).
- Ochrona danych rzeczywistych ([funkcji Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) i [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Kontrolowanie dostępu do danych poufnych i uprzywilejowanego ([zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) i [dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)).

[Usługa Azure Security Center](https://azure.microsoft.com/services/security-center/) oferuje Zarządzanie scentralizowanych zabezpieczeń na potrzeby różnych obciążeń uruchomionych na platformie Azure, lokalnie i w innych chmurach. Można wyświetlić, czy jest to istotne ochrony bazy danych SQL, takie jak [inspekcji](sql-database-auditing.md) i [funkcji Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) są skonfigurowane dla wszystkich zasobów i utworzyć zasady oparte na własnych wymagań.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jakie metody uwierzytelniania użytkownika są oferowane w bazie danych SQL

Istnieją [dwóch metod uwierzytelniania](sql-database-control-access.md#authentication) oferowanych w usłudze SQL Database:

- [Uwierzytelnianie usługi Azure Active Directory](sql-database-aad-authentication.md)
- Uwierzytelnianie SQL

Uwierzytelnianie tradycyjnego systemu windows nie jest obsługiwana. Usługa Azure Active Directory (AD) to scentralizowany Usługa zarządzania tożsamościami i dostępem. Dzięki temu można bardzo łatwo udostępnić pojedynczego logowania jednokrotnego dostępu rejestracji jednokrotnej (SSO) wszystkich pracowników w Twojej organizacji. Oznacza to, że poświadczenia są współużytkowane przez wszystkich usług platformy Azure do uwierzytelniania prostsze. Obsługuje usługi AAD [uwierzytelniania Wieloskładnikowego (uwierzytelniania współczynnik Multi)](sql-database-ssms-mfa-authentication.md) i [kilkoma kliknięciami](../active-directory/hybrid/how-to-connect-install-express.md) AAD można zintegrować z usługą Active Directory systemu Windows Server. Uwierzytelnianie SQL działa dokładnie tak, jak używasz go w przeszłości. Podaj nazwę użytkownika/hasło, i mogą uwierzytelniać użytkowników do dowolnej bazy danych na danym serwerze bazy danych SQL. Umożliwia także bazy danych SQL Database i SQL Data Warehouse, która oferuje uwierzytelnianie wieloskładnikowe kont gości i użytkowników w domenie usługi Azure AD. Jeśli masz już usługi Active Directory w środowisku lokalnym, może tworzyć federacje katalogu z usługą Azure Active Directory do rozszerzenia katalogu usługi Azure.

|**Jeśli użytkownik...**|**Baza danych SQL / SQL Data Warehouse**|
|---|---|
|Nie chcesz używać usługi Azure Active Directory (AD) na platformie Azure|Użyj [uwierzytelnianie SQL](sql-database-security-overview.md)|
|AD używane w programie SQL Server w środowisku lokalnym|[Sfederuj AD z usługą Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)i korzystać z uwierzytelniania usługi Azure AD. Dzięki temu można użyć logowania jednokrotnego.|
|Trzeba wymusić uwierzytelnianie wieloskładnikowe (MFA)|Wymagać uwierzytelniania Wieloskładnikowego jako zasady za pomocą [dostęp warunkowy Microsoft](sql-database-conditional-access.md)i użyj [uwierzytelniania usługi Azure AD Universal z obsługą uwierzytelniania Wieloskładnikowego](sql-database-ssms-mfa-authentication.md).|
|Mają konta gościa z konta Microsoft (live.com, outlook.com) lub z innych domen (gmail.com)|Użyj [uwierzytelniania usługi Azure AD Universal](sql-database-ssms-mfa-authentication.md) w usłudze SQL Database/Data Warehouse, która korzysta z [współpracy B2B usługi Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Zalogować się do Windows przy użyciu poświadczeń usługi Azure AD z domeny federacyjnej|Użyj [zintegrowane uwierzytelnianie usługi Azure AD](sql-database-aad-authentication-configure.md).|
|Zalogować się do Windows przy użyciu poświadczeń z domeny nie obejmuje Federacja z platformą Azure|Użyj [zintegrowane uwierzytelnianie usługi Azure AD](sql-database-aad-authentication-configure.md).|
|Usługi warstwy środkowej, które muszą połączyć się z bazą danych SQL lub SQL Data Warehouse|Użyj [zintegrowane uwierzytelnianie usługi Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak ograniczyć lub kontrolować połączenia dostępu do bazy danych

Istnieje kilka technik do dyspozycji, którego można użyć do osiągnięcia organizacji optymalną łączność aplikacji.

- Reguły zapory
- Punkty końcowe usługi sieci wirtualnej
- Zastrzeżone adresy IP

#### <a name="firewall"></a>Zapora

Zapora uniemożliwia dostęp do serwera z zewnętrznej jednostki, zezwalając tylko określonych jednostek dostęp do serwera usługi SQL Database. Domyślnie wszystkie połączenia i bazami danych serwera SQL Database są niedozwolone, z wyjątkiem połączeń z innymi usługami platformy Azure. Za pomocą reguły zapory można otworzyć dostępu do serwera tylko dla jednostek (na przykład maszyny dewelopera), które należy zatwierdzić, zezwalając na adres IP tego komputera przez zaporę. W tym obszarze pozwala także określić zakres adresów IP, który chcesz zezwolić na dostęp do serwera bazy danych SQL. Na przykład adresy IP maszyn dla deweloperów w Twojej organizacji można dodać tylko raz, określając zakres na stronie ustawień zapory.

Można utworzyć reguły zapory na poziomie serwera lub na poziomie bazy danych. Reguły zapory IP poziomu serwera albo mogą być tworzone przy użyciu usługi Azure portal lub za pomocą programu SSMS. Aby uzyskać więcej informacji na temat sposobu ustawiania reguły zapory poziomu serwera i na poziomie bazy danych, zobacz: [Tworzenie reguły zapory IP w usłudze SQL Database](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Punkty końcowe usługi

Domyślnie usługi SQL database jest skonfigurowany do "Zezwalaj na usługi platformy Azure na dostęp do serwera" — oznacza to, dowolnej maszyny wirtualnej na platformie Azure może próbować połączyć się z bazą danych. Te próby nadal jest konieczne może zostać uwierzytelniony. Jednakże jeśli nie chcesz bazę danych, aby były dostępne dla żadnych adresów IP usługi Azure, możesz wyłączyć "Zezwalaj na usługi platformy Azure na dostęp do serwera". Ponadto można skonfigurować [punkty końcowe usługi sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md).

Punkty końcowe usługi (SE) umożliwiają udostępnianie krytycznych zasobów platformy Azure tylko do własnej prywatnej sieci wirtualnej na platformie Azure. W ten sposób można zasadniczo wyeliminować publicznego dostępu do zasobów. Ruch między siecią wirtualną na platformie Azure pozostaje w sieci szkieletowej platformy Azure. Bez SE uzyskasz tunelowania wymuszanego routing pakietów. Sieć wirtualna wymusza ruch internetowy do organizacji i zapoznać się z tej samej trasie ruch usługi platformy Azure. Dzięki punktom końcowym usług można zoptymalizować to od przepływu pakietów bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Azure.

![Punkty końcowe usługi sieci wirtualnej](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Zastrzeżone adresy IP

Innym rozwiązaniem jest aprowizowanie [zastrzeżone adresy IP](../virtual-network/virtual-networks-reserved-public-ip.md) dla maszyn wirtualnych i dozwolonych tych określonych adresów IP maszyn wirtualnych na serwerze ustawienia zapory. Przypisując zastrzeżone adresy IP, możesz zapisać pracy związanej z konieczności zaktualizować reguły zapory przy użyciu zmiany adresów IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Port nawiązać połączenie z bazą danych SQL na

Port 1433. Baza danych SQL komunikuje się za pośrednictwem tego portu. Aby nawiązać połączenie z sieci firmowej, trzeba dodać regułę dla ruchu wychodzącego w ustawieniach zapory Twojej organizacji. Program narzędziowy należy unikać uwidaczniania portu 1433 poza granicami platformy Azure. Można uruchomić program SSMS w systemie Azure za pomocą [usługi Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Nie wymaga to na otwieranie połączeń wychodzących do portu 1433, adres IP jest statyczny, dzięki czemu bazy danych może być otwarty, aby tylko połączenia programów RemoteApp i obsługuje ona usługi Multi Factor Authentication (MFA).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak monitorować i regulują działania na mój serwer i bazę danych w bazie danych SQL

#### <a name="sql-database-auditing"></a>Inspekcja bazy danych SQL

W usłudze SQL Database można włączać inspekcji śledzić zdarzenia bazy danych. [SQL Database Auditing](sql-database-auditing.md) zapisuje zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji na koncie magazynu platformy Azure. Inspekcja jest szczególnie przydatne, jeśli użytkownik zamierza uzyskać wgląd w potencjalne naruszenia zabezpieczeń i zasad, zachować zgodność z przepisami itp. Umożliwia definiowanie i konfigurowanie niektórych rodzajów zdarzeń, które Twoim zdaniem należy inspekcji i na podstawie uzyskasz wstępnie skonfigurowane raporty i pulpit nawigacyjny, aby uzyskać przegląd zdarzeń występujących w bazie danych. Można zastosować te zasady inspekcji na poziomie bazy danych lub na poziomie serwera. Zobacz Przewodnik po jak włączyć funkcję inspekcji serwera/bazy danych: [Włączanie bazy danych SQL inspekcji](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Wykrywanie zagrożeń

Za pomocą [wykrywanie zagrożeń](sql-database-threat-detection.md), otrzymasz możliwość działania na naruszenia zabezpieczeń lub zasad odnalezione przez bardzo łatwo inspekcji. Nie musisz być ekspertem w celu rozwiązania potencjalnych zagrożeń i naruszeń w systemie zabezpieczeń. Wykrywanie zagrożeń ma również niektóre wbudowane funkcje, takie jak wykrywanie wstrzyknięcie kodu SQL. Iniekcja SQL to próba zmiany lub naruszyć bezpieczeństwo danych i dość często sposób ogólnie rzecz biorąc atakiem aplikacji bazy danych. Wykrywanie zagrożeń działa wiele zestawów algorytmów potrafiących Wykrywanie potencjalnych luk w zabezpieczeniach oraz atakami polegającymi na iniekcji SQL, a także wzorce dostępu i nietypowych bazy danych (takich jak dostęp z nietypowej lokalizacji lub nieznanego podmiotu zabezpieczeń). Specjaliści ds. bezpieczeństwa lub innymi wyznaczeni administratorzy będą otrzymywać powiadomienia e-mail w przypadku wykrycia zagrożeń w bazie danych. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zalecenia dotyczące dalszego zbadania i wyeliminowania zagrożenia. Aby dowiedzieć się, jak włączyć funkcję wykrywania zagrożeń, zobacz: [Włącz wykrywanie zagrożeń](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak chronić dane na ogół w bazie danych SQL

Szyfrowanie zapewnia silne mechanizm Chroń i Zabezpieczaj dane poufne przed intruzami. Zaszyfrowane dane są nie użytkowania intruz bez klucz odszyfrowujący. W związku z tym dodaje dodatkową warstwę ochrony na podstawie istniejących wbudowanym warstwom zabezpieczeń w usłudze SQL Database. Istnieją dwa aspekty ochrony danych w bazie danych SQL:

- Dane, który jest przechowywanych w plikach danych i dziennika
- Twoje dane, które są aktywne

W bazie danych SQL, domyślnie dane magazynowane na platformie plików danych i dziennika w podsystemie magazynowania jest całkowicie i zawsze szyfrowane za pomocą [funkcji Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Kopie zapasowe są również są szyfrowane. Za pomocą funkcji TDE nie uległy zmianie wymagane po swojej stronie aplikacji, który uzyskuje dostęp do tych danych. Szyfrowanie i odszyfrowywanie się tak zdarzyć w sposób przezroczysty; Dlatego nazwa.
Na potrzeby ochrony danych poufnych śledząc i magazynowanych, SQL Database oferuje funkcję o nazwie [zawsze zaszyfrowane (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE jest formą szyfrowania po stronie klienta, który szyfruje poufnych kolumn w bazie danych (dzięki czemu będzie szyfrowany, administratorzy baz danych i nieautoryzowanym użytkownikom). Serwer odebrał zaszyfrowane dane, rozpoczynać się. Klucz dla funkcji Always Encrypted także są przechowywane po stronie klienta, aby tylko autoryzowani klienci może odszyfrować poufnych kolumn. Serwer i administratorów danych nie może, zobacz dane poufne, ponieważ klucze szyfrowania są przechowywane na komputerze klienckim. AE szyfruje poufnych kolumn w tabeli typu end to end, od nieautoryzowanych klientów na dysku fizycznym. Porównywanie równości jest w AE obsługuje obecnie, dzięki czemu przetwarzający mogą w dalszym ciągu zapytania zaszyfrowanych kolumn jako część ich poleceń SQL. Zawsze zaszyfrowane mogą być używane z różnych opcji magazynu kluczy, takie jak [usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), Magazyn certyfikatów Windows i lokalne sprzętowych modułów zabezpieczeń.

|**Właściwości**|**Zawsze szyfrowane**|**Niewidoczne szyfrowanie danych**|
|---|---|---|
|**Zakres szyfrowania**|End-to-end|Dane w spoczynku|
|**Serwer bazy danych mają dostęp do poufnych danych**|Nie|Tak, ponieważ szyfrowanie danych magazynowanych|
|**Dozwolone operacje języka T-SQL**|Porównanie równości|Wszystkie powierzchni języka T-SQL jest dostępna|
|**Zmiany aplikacji wymagane jest użycie funkcji**|Minimalny|Minimalny|
|**Poziom szczegółowości szyfrowania**|Na poziomie kolumny|Poziom bazy danych|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak ograniczyć dostęp do poufnych danych w bazie danych

Każda aplikacja ma nieco poufnych danych w bazie danych, które muszą być chronione, jest widoczny dla wszystkich użytkowników. Pewność, że pracownicy z organizacji konieczne do wyświetlenia tych danych, jednak inne osoby nie powinien mieć możliwości wyświetlania tych danych. Przykładem jest płace pracownika. Menedżer musi jednak dostęp do informacji o płacach jego bezpośrednich podwładnych, poszczególnych członków zespołu nie powinny mieć dostęp do informacji o płacach z jego elementów równorzędnych. Inny scenariusz polega na dane i deweloperów mogą wchodzenie w interakcje z danymi poufnymi w fazie rozwoju lub testowania, na przykład PESEL klientów. Te informacje ponownie nie musi być udostępniana dla dewelopera. W takich przypadkach poufne dane albo musi być zamaskowana lub nie będą widoczne na wszystkich. Usługa SQL Database oferuje dwa takiego podejścia do zapobiegania nieautoryzowanemu dostępowi do danych poufnych:

[Dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md) to funkcja maskowania danych, która umożliwia ograniczania ujawniania poufnych danych przez ich maskowanie dla nieuprzywilejowanych użytkowników w warstwie aplikacji. Należy zdefiniować regułę maskowania, która może utworzyć wzorzec maskowania (na przykład, aby przedstawiać tylko cztery ostatnie cyfry national SSN Identyfikatora: XXX-xx-0000 i oznacz go jako Xs większość) i być zidentyfikowanie użytkowników do wykluczenia z reguł maskowania. Maskowanie miały na bieżąco, a są dostępne dla różnych kategorii danych różnych funkcji maskowania. Dynamiczne maskowanie danych pozwala do automatycznego wykrywania poufnych danych w bazie danych i dotyczą maskowania.

[Zabezpieczeń na poziomie wiersza](/sql/relational-databases/security/row-level-security) pozwala na kontrolowanie dostępu na poziomie wiersza. To znaczy, niektórych wierszy w tabeli bazy danych na podstawie użytkownika wykonującego zapytanie (grupy członkostwa lub kontekstu wykonania) są ukryte. Ograniczenie dostępu odbywa się w warstwie bazy danych, zamiast w warstwie aplikacji, aby uprościć logikę aplikacji. Możesz rozpocząć od utworzenia predykat filtru, filtrowanie wierszy, które nie są widoczne i zabezpieczeń zasad dalej Definiowanie kto ma dostęp do tych wierszy. Na koniec użytkownika końcowego wykonuje zapytanie ich i, w zależności od uprawnień użytkownika, wyświetlanie tych wierszy, ograniczony lub nie możesz zobaczyć je na wszystkich.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak zarządzać kluczami szyfrowania w chmurze

Dostępne są opcje zarządzania kluczami Always Encrypted (szyfrowanie po stronie klienta) i technologia Transparent Data Encryption (szyfrowanie danych magazynowanych). Zaleca się regularnie Obróć klucze szyfrowania. Częstotliwość obrotu należy wyrównać z organizacji wewnętrznej przepisów i zgodności z wymaganiami.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

W funkcji TDE jest hierarchii klucz dwóch — dane w każdej bazie danych użytkownika są szyfrowane przy symetrycznego AES-256 unikatowy bazy danych klucza szyfrowania bazy danych (klucz szyfrowania danych), który z kolei jest szyfrowany przez serwer Unikatowy klucza asymetrycznego wzorzec szyfrowania RSA 2048. Klucz główny może być zarządzany, albo:

- Automatycznie przez platformę — SQL Database.
- Lub przez użytkownika za pomocą [usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako magazynu kluczy.

Domyślnie klucz główny dla funkcji Transparent Data Encryption jest zarządzane przez usługę SQL Database dla wygody. Jeśli Twoja organizacja chce kontrolę nad klucz główny, istnieje możliwość użycia Vault](sql-database-always-encrypted-azure-key-vault.md) klucza platformy Azure jako magazynu kluczy. Za pomocą usługi Azure Key Vault, Twoja organizacja przyjęto założenie, kontrolę nad formanty klawisza inicjowania obsługi administracyjnej, obrotu i uprawnień. [Obrót lub zmianą typu klucza głównego TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) jest szybkie, jak go ponownie szyfruje klucz szyfrowania danych. Dla organizacji mających separacji ról między zabezpieczeń i zarządzania danymi Administrator zabezpieczeń może aprowizować materiału klucza dla funkcji TDE klucza głównego w usłudze Azure Key Vault i podaj identyfikator klucza usługi Azure Key Vault do administratora bazy danych na potrzeby szyfrowanie danych magazynowanych na serwerze. Key Vault zaprojektowano w taki sposób, że firma Microsoft nie jest w stanie zobaczyć ani wyodrębnić jakiekolwiek klucze szyfrowania. Możesz także uzyskać scentralizowanego zarządzania kluczami w swojej organizacji.

#### <a name="always-encrypted"></a>Zawsze szyfrowane

Istnieje również [hierarchii klucz dwóch](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) w funkcji zawsze zaszyfrowane — kolumny poufne dane są szyfrowane za klucza szyfrowania kolumny 256 AES (CEK), który z kolei jest szyfrowany przez klucz główny kolumny (CMK). Sterowniki klienta podana dla funkcji Always Encrypted mają żadne ograniczenia dotyczące długości CMKs. Zaszyfrowana wartość CEK są przechowywane w bazie danych, a CMK znajduje się w zaufanej magazynu kluczy, takie jak Windows certyfikatu Store, Azure Key Vault lub sprzętowego modułu zabezpieczeń.

- Zarówno [CEK i CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) można obracać.
- Obrót CEK jest rozmiar operacji na danych i może być czasochłonnymi, w zależności od rozmiaru tabel zawierających zaszyfrowanych kolumn. Dlatego rozsądne jest właściwie CEK wymiany.
- Obrót CMK, jednak nie kolidują z wydajnością bazy danych i może odbywać się przy użyciu ról rozdzielonych.

Na poniższym diagramie przedstawiono opcje magazynu kluczy dla kluczy głównych kolumny w Always Encrypted

![Zawsze szyfrowane CMK przechowywania dostawców](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak zoptymalizować i bezpieczny ruch między organizacji i bazy danych SQL

Ruch sieciowy między organizacji i bazy danych SQL może uzyskać zazwyczaj kierowane za pośrednictwem publicznej sieci. Jednak jeśli wybierzesz zoptymalizować tę ścieżkę i bezpieczniejsze, warto zajrzeć do Express Route. Expressroute umożliwia zasadniczo rozszerzanie sieci firmowej na platformie Azure za pośrednictwem połączenia prywatnego. W ten sposób nie omijają publiczny Internet. Możesz także uzyskać wyższy poziom zabezpieczeń, niezawodności i optymalizacji routingu, który przekłada się na mniejsze opóźnienia sieci i szybciej, wyższe szybkości niż w przypadku normalnie doświadczenie przesyłane za pośrednictwem publicznej sieci internet. Jeśli planowane jest na transferowanie znaczące fragmentów danych między organizacji i Azure, za pomocą Express Route może przynieść korzyści finansowe. Można wybierać spośród trzech modeli innego połączenia dla połączenia z Twojej organizacji na platformie Azure:

- [Chmura Exchange wspólnej lokalizacji](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Dowolna dowolna](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Punkt-punkt](../expressroute/expressroute-connectivity-models.md#Ethernet)

Expressroute umożliwia również serii maksymalnie 2 x limit przepustowości, zakupionych dla bez dodatkowych opłat. Jest również możliwość skonfigurowania wielu łączności regionie przy użyciu Express route. Aby wyświetlić listę dostawców łączności ER, zobacz: [Express Route partnerzy i lokalizacje komunikacji równorzędnej](../expressroute/expressroute-locations.md). Szczegółowo w następujących artykułach opisano Express Route:

- [Wprowadzenie Express Route](../expressroute/expressroute-introduction.md)
- [Wymagania wstępne](../expressroute/expressroute-prerequisites.md)
- [Przepływy](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Jest bazą danych SQL spełniają wszelkie wymagania prawne i jak, pomaga przy użyciu własnego organizacji zgodności

Baza danych SQL jest zgodna z szeroką gamę zachowania zgodności z przepisami prawnymi. Aby wyświetlić najnowszy zestaw zachowania zgodności prawnymi, które zostały spełnione przez usługę SQL Database, odwiedź stronę [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i przechodzenie do zachowania zgodności prawnymi, które są istotne dla Twojej organizacji, aby zobaczyć, jeśli baza danych SQL znajduje się w obszarze zgodne Usługi platformy Azure. Należy pamiętać, że mimo że bazy danych SQL może być zakwalifikowany jako zgodnej usługi, go pomaga zgodności organizacji usługi, ale nie automatycznie gwarantuje on jest.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentna baza danych monitorowania i konserwacji po migracji

Gdy zakończysz migrację bazy danych do usługi SQL Database, czy chcecie monitorowanie bazy danych (na przykład, jak to wykorzystania zasobów takich jak sprawdzanie lub DBCC kontrole) i wykonywać regularnej konserwacji (na przykład ponownie utworzyć lub zreorganizować indeksy i statystyki itp.). Na szczęście usługa SQL Database to Intelligent w tym sensie, że ułatwiają Proaktywne monitorowanie i konserwacja bazy danych, aby aplikacja działa optymalnie zawsze używa trendów historycznych i zarejestrowanych metryk i statystyki. W niektórych przypadkach bazy danych SQL Azure może automatycznie wykonać zadania konserwacji, w zależności od ustawień konfiguracji. Istnieją trzy aspekty monitorowania bazy danych w bazie danych SQL:

- Monitorowanie wydajności i optymalizacji.
- Optymalizacja zabezpieczeń.
- Optymalizację kosztów.

### <a name="performance-monitoring-and-optimization"></a>Monitorowanie wydajności i optymalizacja

Za pomocą szczegółowe informacje o wydajności zapytań można uzyskać zaleceń dostosowanych do potrzeb dla obciążenia bazy danych, aby kontynuować uruchamianie aplikacji pracę na optymalnym poziomie — zawsze. Możesz również skonfigurować ją tak, aby te zalecenia pozwalają uzyskać stosowane automatycznie, a nie trzeba odblokowane wykonywanie zadań konserwacji. Jeśli korzystasz z Index Advisor można automatycznie wdrożyć zalecenia dotyczące indeksu na podstawie własnego obciążenia — jest to nazywane automatycznego dostrajania. Zalecenia ewoluować wraz ze wzrostem zmiany obciążenia aplikacji, aby udostępniać najistotniejsze sugestie. Możesz także uzyskać opcję, aby ręcznie przejrzeć te zalecenia i zastosować je według uznania.  

### <a name="security-optimization"></a>Optymalizacja zabezpieczeń

SQL Database udostępnia zalecenia dotyczące zabezpieczeń informacje z możliwością działania, aby zabezpieczyć swoje dane i wykrywanie zagrożeń dla identyfikacji i badania podejrzanych działaniach bazy danych, które mogą stanowić potencjalne wątku do bazy danych. [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) jest bazą danych skanowania i raportowania usługi, która umożliwia monitorowanie stanu zabezpieczeń wszystkich baz danych na dużą skalę i Identyfikuj zagrożenia bezpieczeństwa oraz odstają od linii bazowej zabezpieczeń zdefiniowane przez użytkownika. Po każdym skanowaniu niestandardowej listy kroki z możliwością działania i skrypty korygujące zostanie podany, a także raport z oceny, który może służyć w celu spełnienia wymagań dotyczących zgodności.

Usługa Azure Security Center Identyfikuj zalecenia dotyczące zabezpieczeń na tablicy i zastosowanie ich przy użyciu jednego kliknięcia.

### <a name="cost-optimization"></a>Optymalizacji kosztów

Platforma Azure SQL analizuje historii wykorzystania w bazach danych na serwerze, aby ocenić i zaleca się opcjami optymalizację kosztów. Ta analiza jest zwykle dwóch tygodni, analizowanie i tworząc praktyczne zalecenia. Pula elastyczna jest jedną z opcji. Zalecenie jest wyświetlana w portalu jako transparent:

![zalecenia dotyczące puli elastycznej](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Ta analiza można również wyświetlić w sekcji "Doradca":

![zalecenia dotyczące puli elastycznej — usługi advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak monitorować wydajność i wykorzystanie zasobów w usłudze SQL Database

W usłudze SQL Database można skorzystać z inteligentnego szczegółowe informacje dotyczące platformy w celu monitorowania wydajności i dostosowywania odpowiednio. Możesz monitorować wydajność i wykorzystanie zasobów w usłudze SQL Database przy użyciu następujących metod:

#### <a name="azure-portal"></a>Azure Portal

Witryny Azure portal pokazuje wykorzystanie bazy danych, wybierając bazę danych, a następnie klikając na wykresie w okienku przeglądu. Można zmodyfikować wykres, aby wyświetlić wiele metryk, takich jak procent użycia procesora CPU, procentowa wartość jednostki DTU, procent we/wy danych, procent sesji i jako procent rozmiaru bazy danych.

![Wykres monitorowania](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Monitorowanie — wykres 2](./media/sql-database-manage-after-migration/chart.png)

Na podstawie tego wykresu można skonfigurować alerty przez zasób. Te alerty umożliwiają reagowanie na ilością zasobów za pomocą adresu e-mail, zapis do punktu końcowego HTTPS/HTTP lub wykonania akcji. Aby uzyskać więcej informacji, zobacz [tworzyć alerty](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Dynamicznych widoków zarządzania

Można tworzyć zapytania [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamicznego widoku zarządzania do zwrócenia Historia statystyk użycia zasobów z ostatniej godziny i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) widoku wykazu systemu do zwrócenia Historia w ciągu ostatnich 14 dni.

#### <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) pozwala wyświetlać historię, najpopularniejsze zapytania korzystające z zasobów i długotrwałe zapytania dla konkretnej bazy danych. Można szybko zidentyfikować NAJPOPULARNIEJSZE zapytania, wykorzystanie zasobów, czasu trwania i częstotliwości wykonywania. Można śledzić zapytań i wykrywać regresji. Ta funkcja wymaga [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) były włączone i aktywnej bazy danych.

![Szczegółowe informacje o wydajności zapytań](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Dzienniki usługi Azure SQL Analytics (wersja zapoznawcza) w usłudze Azure Monitor

[Dzienniki platformy Azure Monitor](../azure-monitor/insights/azure-sql.md) umożliwia gromadzenie i wizualizowanie kluczowe metryki wydajności usługi Azure SQL Azure obsługuje maksymalnie 150 000 baz danych SQL i 5000 pule elastyczne SQL danego obszaru roboczego. Służy do monitorowania i otrzymywać powiadomienia. Można monitorować bazy danych SQL i metryki elastycznej puli w wielu subskrypcji platformy Azure i pule elastyczne i może służyć do identyfikowania problemów w każdej warstwie stosu aplikacji.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jestem zwracają uwagę problemy z wydajnością: Czym różni się Moje metodologię rozwiązywania problemów z bazy danych SQL z programu SQL Server

Główna część techniki rozwiązywania problemów należy użyć do diagnozowania zapytania i problemy z wydajnością bazy danych pozostają bez zmian. Po tym samym serwerze SQL aparatu obsługuje chmury. Jednak platforma — bazy danych SQL Azure ma wbudowane "analizy". Może pomóc Ci rozwiązać i jeszcze łatwiej diagnozować problemy z wydajnością. Można go również wykonywać niektóre z tych akcji naprawczych w Twoim imieniu i w niektórych przypadkach, proaktywnie — automatycznie rozwiązać.

Znacznie zyskują swoje podejście do rozwiązywania problemów z wydajnością za pomocą inteligentnych funkcji, takich jak [Insight(QPI) wydajności zapytań](sql-database-query-performance.md) i [funkcji Database Advisor](sql-database-advisor.md) w połączeniu i więc różnicę w metodologii różni się w tym względzie — nie jest już konieczne ręcznych stopień essential szczegółowe informacje, które mogą pomóc w rozwiązaniu problemu pod ręką. Platforma wykonuje trudną pracę. Przykładem tego jest qpi, włączono funkcję. Za pomocą QPI można przejść do aż do poziomu zapytania i przyjrzyj się trendów historycznych i zorientować się, kiedy uwzględniona dokładnie zapytania. Funkcji Database Advisor zawiera zalecenia dotyczące rzeczy, które mogą pomóc poprawić wydajność ogólną ogólnie rzecz biorąc, takich jak - brakujące indeksy, usuwanie indeksów, parametryzacji zapytań itp.

Za pomocą Rozwiązywanie problemów z wydajnością, jest ważne, aby ustalić, czy jest tylko aplikacja lub tworzenie jej kopii bazy danych, która ma wpływ na wydajność aplikacji. Często znajduje się problem z wydajnością w warstwie aplikacji. Może to być architektury lub wzorzec dostępu do danych. Rozważmy na przykład korzystać z aplikacji z dużą liczbą, która jest wrażliwa na opóźnienie sieci. W takim przypadku aplikacja spada, ponieważ będzie istniało wiele żądań krótki, przechodząc do i z powrotem dodać te natężenie ruchu ("dużą liczbą") między aplikacją a serwera a na obciążonej sieci, szybko. Aby zwiększyć wydajność w takiej sytuacji, można użyć [wsadowe](sql-database-performance-guidance.md#batch-queries). Za pomocą partie pomaga znacznie ponieważ teraz przetworzenie żądania w partii; w związku z tym pomaga zmniejszyć opóźnienia w obie strony i poprawić wydajność aplikacji.

Ponadto w przypadku zauważenia spadku ogólnej wydajności bazy danych, można monitorować [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamicznego zarządzania widoków w celu Dowiedz się, użycie procesora CPU, we/wy i pamięci. Wydajność może być wpływ, ponieważ baza danych jest przetrzymywany zasobów. Może to być może należy zmienić rozmiar obliczeń i/lub warstwę, w oparciu o stałym wzbogacaniu i zmniejszanie obciążenia usługi.

Aby uzyskać kompleksowy zestaw zaleceń dotyczących dostrajania problemy z wydajnością zobacz: [Dostosowywanie bazy danych](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Jak zapewnić I używam warstwy odpowiednią usługę i obliczenia rozmiaru

Usługa SQL Database oferuje różne warstwy usług podstawowa, standardowa i Premium. Każda warstwa usługi, możesz uzyskać gwarantowane przewidywalnej wydajności związany z danej warstwie usługi. W zależności od obciążenia może być wzrosty działania gdzie wykorzystanie zasobów może napotkać Zaokrąglenie w górę bieżący rozmiar obliczeń, które znajdują się w. W takich przypadkach warto najpierw rozpocząć poprzez ocenę czy wszelkie dostrajania może pomóc (na przykład dodawanie lub zmienianie indeksu itp.). Jeśli nadal napotykasz problemy limit, rozważ przeniesienie na wyższą warstwę usługi lub obliczenia rozmiaru.

|**Warstwa usług**|**Typowe scenariusze przypadków użycia**|
|---|---|
|**Podstawowa**|Aplikacje korzystające z grupy użytkowników i bazy danych, która nie ma wysokie wymagania dotyczące współbieżności, skalowalność i wydajność. |
|**Standardowa (Standard)**|Aplikacje za pomocą znaczne wymagania współbieżności, skalowalność i wydajność dzięki połączeniu z usługami małej do średniej żądania We/Wy. |
|**Premium**|Aplikacje z dużą liczbą równoczesnych użytkowników, wysoka Procesora/pamięci i wysoki żądań We/Wy. Wysoka współbieżności, wysokiej przepływności i opóźnienia w przypadku poufnych aplikacji można wykorzystać na poziomie Premium. |
|||

Upewnienie się, że jesteś na rozmiar obliczeń w prawo, aby uzyskać można monitorować swoje użycie zasobów zapytań i bazy danych za pomocą jednego z wymienionych powyżej metod "Jak mogę monitorować wydajności i użycia zasobów w usłudze SQL Database". Należy znaleźć zapytania/bazy danych działają spójnie gorąca na Procesora/pamięci itp., należy rozważyć skalowanie w górę do większy rozmiar obliczeń. Podobnie jeśli użytkownik należy pamiętać, że nawet w swojej godzinach szczytu, prawdopodobnie nie korzystać z zasobów, jaka; należy rozważyć skalowanie w dół od bieżącego rozmiaru obliczeń.

Jeśli masz wzorca aplikacji SaaS lub scenariusza konsolidacji bazy danych, należy rozważyć użycie puli elastycznej dla optymalizację kosztów. Pula elastyczna jest doskonałym sposobem osiągnięcia konsolidacji bazy danych oraz optymalizację kosztów. Aby dowiedzieć się więcej o zarządzaniu wieloma bazami danych przy użyciu elastycznej puli, zobacz: [Zarządzanie pul i baz danych](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak często należy przeprowadzić sprawdzania integralności bazy danych dla bazy danych

SQL Database przy użyciu kilka technik inteligentne, które zezwolić na obsługę niektórych klas uszkodzenia danych i automatycznie bez utraty danych. Metody te są wbudowane w usługę i są wykorzystywane przez usługę gdy muszą pojawia się. Na bieżąco kopie zapasowe bazy danych w usłudze są badane przywracanie ich, a następnie uruchamiając polecenie DBCC CHECKDB na nim. Jeśli występują problemy, bazy danych SQL aktywnie je usuwa. [Strona automatycznej naprawy](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) jest używany na potrzeby rozwiązywania stron, które są uszkodzone lub masz problemy z integralnością danych. Strony bazy danych zawsze są weryfikowane z domyślnym ustawieniem sumę KONTROLNĄ, która weryfikuje integralność strony. Bazy danych SQL aktywnie monitoruje i monitoruje integralność danych, bazy danych i, jeśli wystąpią problemy, usuwa je z najwyższym priorytetem. Oprócz wspomnianych można opcjonalnie Uruchom własne sprawdzeń integralności na użytkownika spowoduje.  Aby uzyskać więcej informacji, zobacz [integralność danych w bazie danych SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Przenoszenie danych po migracji

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Jak eksportować i importować dane jako pliki BACPAC z bazy danych SQL

- **Eksportuj**: Usługi Azure SQL database można wyeksportować jako plik BACPAC w witrynie Azure portal

   ![Eksportowanie bazy danych](./media/sql-database-export/database-export1.png)

- **Importuj**: Możesz zaimportować dane jako plik BACPAC do bazy danych przy użyciu witryny Azure portal.

   ![Importowanie bazy danych](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Jak zsynchronizować dane między bazą danych SQL i programu SQL Server

Istnieje kilka sposobów osiągnięcia tego:

- **[Synchronizacja danych](sql-database-sync-data.md)**  — ta funkcja pomaga w synchronizacji danych dwukierunkowo między wieloma bazami danych SQL Server w środowisku lokalnym i bazy danych SQL. Aby zsynchronizować z bazami danych programu SQL Server w środowisku lokalnym, musisz zainstalować i skonfigurować agenta synchronizacji na komputerze lokalnym i otwarciu wychodzącego portu TCP 1433.
- **[Replikacji transakcji](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  — za pomocą replikacji transakcji można synchronizować dane z lokalnej bazy danych SQL Azure z lokalnego wydawcy i bazy danych SQL Azure, subskrybent jest. Obecnie jest obsługiwana tylko w tej konfiguracji. Aby uzyskać więcej informacji na temat sposobu migrować dane ze środowiska lokalnego do usług SQL Azure przy minimalnych przestojach zobacz: [Użycie replikacji transakcji](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [bazy danych SQL](sql-database-technical-overview.md).
