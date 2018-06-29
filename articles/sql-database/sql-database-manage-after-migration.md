---
title: Zarządzanie po migracji — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania bazą danych po zakończeniu migracji do usługi Azure SQL Database.
services: sql-database
author: joesackmsft
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: josack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: d97e0bf94b911936cacf04f7f26c172b9975c56f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061648"
---
# <a name="new-dba-in-the-cloud--managing-your-database-in-azure-sql-database"></a>Nowy model DBA w chmurze — zarządzania bazą danych w bazie danych SQL Azure

Przenoszenie z tradycyjnego zarządzać własnym, własnym kontrolowanego środowiska do środowiska PaaS może wydawać się nieco utrudnione na początku. Jako Deweloper aplikacji lub administratorem baz danych należy znać możliwości podstawowej platformy, który umożliwia zachowanie aplikacji dostępne, wydajność, bezpieczne i elastyczne — zawsze. Ten artykuł ma na celu dokładnie. Artykuł krótkiej formie organizuje zasoby i daje wskazówek na temat sposobu najlepiej użyć kluczowych możliwości bazy danych SQL do zarządzania i Zachowaj wydajne działanie aplikacji i uzyskać optymalne wyniki w chmurze. Typowy odbiorców w tym artykule będzie te które: 
- Ocenia migracji swoich aplikacjach do bazy danych SQL Azure — modernizacji Twojej aplikacji.
- Trwa migrowanie swoich aplikacjach — scenariusz migracji w toku.
- Niedawnym zakończeniu migracji do usługi Azure SQL DB — nowy DBA w chmurze.

W tym artykule omówiono niektóre właściwości podstawowej bazy danych SQL Azure to platforma, która można łatwo wykorzystać. Są następujące:- 
- Biznesowe ciągłości i odzyskiwanie po awarii (BCDR)
- Zabezpieczenia i zgodność z przepisami
- Inteligentnego bazy danych monitorowania i konserwacji
- Przenoszenie danych

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Biznesowe ciągłości i odzyskiwanie po awarii (BCDR)
Możliwości odzyskiwania ciągłości i odzyskiwaniem po awarii firm umożliwiają kontynuować firmy, w zwykły sposób, w przypadku awarii. Po awarii może być zdarzeniu poziomu bazy danych (na przykład ktoś przez pomyłkę porzuca kluczowe tabeli) lub centrum danych zdarzenia poziomu (katastrofy regionalnych, na przykład tsunami). 

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak tworzyć i zarządzać kopiami zapasowymi w bazie danych SQL
Nie twórz kopie zapasowe dla bazy danych SQL Azure i to, ponieważ nie masz. Baza danych SQL automatycznie kopie zapasowe baz danych, więc możesz już musi martwić planowania, pobieranie i zarządzanie kopiami zapasowymi. Platforma trwa pełna kopia zapasowa co tydzień, różnicowej kopii zapasowej co kilka godzin i dziennika kopii zapasowej co 5 minut, aby upewnić się, że odzyskiwania po awarii jest wydajne i minimalnej utracie danych. Pierwsza pełna kopia zapasowa odbywa się, jak utworzyć bazę danych. Te kopie zapasowe są dostępne na pewien okres o nazwie "Okres przechowywania" i zależy od wybranej warstwy wydajności.  Baza danych SQL udostępnia możliwość przywracania do dowolnego punktu w czasie, w tym przechowywania okresu przy użyciu [punktu w czasie odzyskiwania (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Warstwę wydajności|Okres przechowywania w dniach|
|---|:---:|
|Podstawowa|7|
|Standardowa (Standard)|35|
|Premium|35|
|||

Ponadto [długoterminowe przechowywanie STYLEM](sql-database-long-term-retention.md) funkcja służy do przechowywania na plików kopii zapasowych znacznie dłuższy okres, w szczególności maksymalnie 10 lat i przywrócić dane z tych kopii zapasowych w dowolnym momencie, w tym okresie. Ponadto kopie zapasowe bazy danych są przechowywane w pamięci masowej replikacją geograficzną zapewniające odporność z regionalnych katastrofy. Można też przywrócić te kopie zapasowe w dowolnym regionie Azure, w dowolnym momencie czasu w okresie przechowywania. Zobacz [omówienie ciągłości działalności biznesowej](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak zapewnić ciągłość prowadzenia działalności biznesowej w przypadku awarii centrum danych na poziomie lub regionalnych katastrofy?
Ponieważ kopie zapasowe bazy danych są przechowywane w podsystemie magazynu z replikacją geograficzną, aby upewnić się, że w przypadku regionalnej awarii, można przywrócić kopii zapasowej do innego regionu systemu Azure. Jest to przywracaniem geograficznym. RPO (cel punktu odzyskiwania) w tym jest to zwykle < 1 godziny i Wstaw (Szacowany odzyskiwania czas — godzin po kilku minutach).

Krytycznym baz danych bazy danych SQL Azure oferuje, aktywna replikacja geograficzna. To przede wszystkim do czego jest tworzy replikacją geograficzną dodatkowej kopię oryginalnej bazy danych, w innym regionie. Na przykład, jeśli baza danych początkowo znajduje się w regionie Azure zachodnie nam i ma odporności regionalnych po awarii. Należy utworzyć repliki bazy danych dla aktywnej replikacji geograficznej w zachodnie stany USA znaczy wschodnie stany USA. Gdy calamity, lecz na zachodnie stany USA można trybu failover w regionie wschodnie stany USA. Konfigurując je w grupie pracy awaryjnej automatycznie jest jeszcze bardziej poprawić jakość, ponieważ gwarantuje to, że bazy danych automatycznie przełącza na serwerze pomocniczym w wschodnie stany USA w przypadku awarii. Cel punktu odzyskiwania dla tego jest < 5 sekund i Wstaw < 30 sekund.

Jeśli grupa automatycznej pracy w trybie failover nie jest skonfigurowana, aplikacja musi aktywnie monitorowania do awarii i zainicjuj tryb failover na serwerze pomocniczym. Możesz utworzyć maksymalnie 4 takie active-geograficznych w różnych regionach platformy Azure. Pobiera on jeszcze bardziej poprawić jakość. Można również uzyskać dostępu do tych dodatkowej active-geograficznych dla dostęp tylko do odczytu. To jest dostarczany bardzo przydatne w celu zmniejszenia opóźnień w scenariuszu aplikacji rozproszonej geograficznie. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak planu odzyskiwania po awarii zmienia z lokalną bazą danych SQL?
Podsumowując tradycyjnych, lokalnie Instalatora programu SQL Server wymagane aktywnie zarządzać jego dostępność za pomocą funkcji, takich jak klaster pracy awaryjnej, dublowania bazy danych, replikacji transakcji, etc. wysyłania dziennika i obsługa i zarządzanie kopiami zapasowymi, aby upewnić się Ciągłość prowadzenia działalności biznesowej. Z bazy danych SQL platformy zarządza są dla Ciebie, dzięki czemu można skupić się na tworzenie i optymalizowanie aplikacji bazy danych i nie martw się o zarządzaniu po awarii, jaka. Masz kopii zapasowej i skonfigurować plany odzyskiwania po awarii i działa za pomocą kilku kliknięć w portalu Azure (lub kilku poleceń, za pomocą interfejsów API środowiska PowerShell). 

Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz: [Azure SQL bazy danych po awarii odzyskiwania 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpieczenia i zgodność z przepisami
Baza danych SQL trwa bardzo poważnie zabezpieczeń i prywatności. Zabezpieczenia w ramach bazy danych SQL jest dostępna na poziomie bazy danych i na poziomie platformy i jest rozpoznawany najlepiej, gdy przydzielone do kilku warstw. W każdej warstwie Pobierz kontrolowanie i zapewnienia optymalnych zabezpieczeń aplikacji. Są warstwy:
- Usługi tożsamości i uwierzytelniania ([uwierzytelniania systemu Windows/SQL i uwierzytelniania usługi Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitorowanie aktywności ([inspekcji](sql-database-auditing.md) i [wykrywanie zagrożeń](sql-database-threat-detection.md)).
- Ochrona danych rzeczywistych ([przezroczystego szyfrowania danych [funkcji TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) i [zawsze zaszyfrowane [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)). 
- Kontrolowanie dostępu do danych poufnych i uprzywilejowane ([zabezpieczeń na poziomie wiersza](/sql/relational-databases/security/row-level-security) i [dynamicznego maskowania danych](/sql/relational-databases/security/dynamic-data-masking)).

[Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) oferuje zarządzania scentralizowanych zabezpieczeń między obciążenia działające na platformie Azure i lokalnego, a w innych chmur. Można wyświetlić, czy jest to istotne ochrony bazy danych SQL, takich jak [inspekcji](sql-database-auditing.md) i [przezroczystego szyfrowania danych [funkcji TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) są skonfigurowane na wszystkie zasoby, a następnie utwórz zasady na podstawie własnych wymagań.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Metody uwierzytelniania użytkownika, które są oferowane w bazie danych SQL?
Brak [dwóch metod uwierzytelniania](sql-database-control-access.md#authentication) oferowanych w bazie danych SQL: 
- [Uwierzytelnianie usługi Azure Active Directory](sql-database-aad-authentication.md)
- Uwierzytelnianie SQL. 

Uwierzytelnianie tradycyjnego systemu windows nie jest obsługiwane. Azure Active Directory (AD) jest scentralizowane usługi zarządzania tożsamościami i dostępem. Z tym można bardzo łatwo Podaj pojedynczego logowania jednokrotnego dostępu (SSO) do wszystkich pracowników w Twojej organizacji. Co to jest, że poświadczenia są współużytkowane przez wszystkich usług platformy Azure dla uwierzytelniania prostsze. Obsługuje AAD [MFA (Multi Factor Authentication)](sql-database-ssms-mfa-authentication.md) i z [kilku kliknięć](../active-directory/connect/active-directory-aadconnect-get-started-express.md) AAD można zintegrować z usługą Active Directory systemu Windows Server. Uwierzytelnianie SQL działa dokładnie tak, w obecnie używasz go w przeszłości. Podaj nazwę użytkownika/hasło, a można uwierzytelniać użytkowników do dowolnej bazy danych na danym serwerze logicznym. Umożliwia także bazy danych SQL i magazyn danych SQL, zapewniają uwierzytelnianie wieloskładnikowe i konta użytkownika gościa w ramach domeny usługi Azure AD. Jeśli masz już usługi Active Directory — lokalny, można było wykonać Federację katalogu w usłudze Azure Active Directory, aby rozszerzyć katalog na platformie Azure.

|**Jeśli użytkownik...**|**Baza danych SQL / magazyn danych SQL**|
|---|---|
|Nie chce używać usługi Azure Active Directory (AD) na platformie Azure|Użyj [uwierzytelnianie SQL](sql-database-security-overview.md)|
|Lokalne AD używane na serwerze SQL|[Federację AD z usługą Azure AD](../active-directory/connect/active-directory-aadconnect.md)i korzystać z uwierzytelniania usługi Azure AD. Dzięki temu można użyć rejestracji jednokrotnej.|
|Musisz wymusić uwierzytelnianie wieloskładnikowe (MFA)|Uwierzytelniania MFA można wymagać jako zasady za pomocą [dostępu warunkowego Microsoft](sql-database-conditional-access.md)i użyj [uwierzytelniania usługi Azure AD uniwersalnych z obsługą uwierzytelniania Wieloskładnikowego](sql-database-ssms-mfa-authentication.md).|
|Mają konta gościa z konta Microsoft (live.com, outlook.com) lub z innych domen (gmail.com)|Użyj [uwierzytelniania usługi Azure AD uniwersalnych](sql-database-ssms-mfa-authentication.md) w magazynie danych/bazy danych SQL, która wykorzystuje [współpracy B2B usługi Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Zalogować się do systemu Windows z domeny federacyjnej przy użyciu poświadczeń usługi Azure AD|Użyj [zintegrowane uwierzytelnianie usługi Azure AD](sql-database-aad-authentication-configure.md).|
|Zalogować się do systemu Windows przy użyciu poświadczeń z domeny nie jest Sfederowane przy użyciu usługi Azure|Użyj [zintegrowane uwierzytelnianie usługi Azure AD](sql-database-aad-authentication-configure.md).|
|Usługi warstwy środkowej, które mogą połączyć się z bazą danych SQL lub SQL Data Warehouse|Użyj [zintegrowane uwierzytelnianie usługi Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak ograniczyć lub kontrolować łączności dostępu do bazy danych?
Istnieje wiele technik dyspozycji, którego można użyć, aby osiągnąć optymalną łączność organizacji dla aplikacji. 
- Reguły zapory
- Punkty końcowe usługi sieci Wirtualnej
- Zastrzeżone adresy IP

#### <a name="firewall"></a>Zapora
Zapora uniemożliwia dostęp do serwera z zewnętrznej jednostki, zezwalając tylko określonym jednostek dostęp do serwera sieci logicznej. Domyślnie wszystkie połączenia i bazy danych wewnątrz serwera logicznego jest niedozwolone, z wyjątkiem połączeń przychodzących z innymi usługami Azure. Z reguły zapory można otworzyć dostęp do serwera tylko dla jednostek (na przykład komputer developer), które zatwierdzenia, zezwalając na adres IP tego komputera przez zaporę. Można też określić zakres adresów IP, który chcesz zezwolić na dostęp do serwera logicznego. Na przykład adresy IP maszyn deweloperów w Twojej organizacji można dodać na raz, określając zakres na stronie ustawień zapory. 

Można tworzyć reguły zapory na poziomie serwera lub na poziomie bazy danych. Reguły zapory poziomu serwera można utworzony za pośrednictwem portalu lub przy użyciu narzędzia SSMS. Aby uzyskać więcej informacji na temat ustawiania serwera i reguły zapory poziomu bazy danych, zobacz: [tworzenie reguł zapory w bazie danych SQL](sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal).

#### <a name="service-endpoints"></a>Punkty końcowe usługi
Domyślnie Twoja baza danych SQL jest skonfigurowane i umożliwiają "wszystkich usług platformy Azure" — co oznacza, że wszystkie maszyny wirtualnej platformy Azure może próbować połączyć się z bazą danych. Te próby nadal jest konieczne uwierzytelniane. Jednak jeśli nie chcesz bazy danych jako dostępny przez wszystkie adresy IP platformy Azure, można wyłączyć "Zezwalaj na wszystkich usług platformy Azure". Ponadto można skonfigurować [punktów końcowych usługi sieci Wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md).

Punkty końcowe usługi (Szwecja) umożliwiają ujawnia krytyczne zasobów na platformie Azure tylko do własnej prywatnej sieci wirtualnej na platformie Azure. W ten sposób zasadniczo wyeliminować publiczny dostęp do zasobów. Ruch między sieci wirtualnej Azure pozostaje w sieci Azure sieci szkieletowej. Bez SE otrzymasz tunelowanie wymuszone routing pakietów. Sieci wirtualne wymusza ruch internetowy do organizacji i ruchu usługi Azure, aby zapoznać się z tej samej ścieżki. Z punktów końcowych usługi Aby zoptymalizować to od przepływu pakietów bezpośrednio z sieci wirtualnej do usługi w sieci Azure sieci szkieletowej.

![Punkty końcowe usługi sieci Wirtualnej](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

#### <a name="reserved-ips"></a>Zastrzeżone adresy IP
Innym rozwiązaniem jest udostępniania [zastrzeżone adresy IP](../virtual-network/virtual-networks-reserved-public-ip.md) dla maszyn wirtualnych i dozwolonych tych określonych adresów IP maszyny Wirtualnej na serwerze ustawienia zapory. Przypisując zastrzeżonych adresów IP, należy zapisać problemy o można zaktualizować reguł zapory ze zmianą adresów IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Jakie portu połączyć z bazą danych SQL na?

Port 1433. Baza danych SQL komunikuje się za pośrednictwem tego portu. Aby połączyć się z sieci firmowej, należy dodać regułę ruchu wychodzącego w ustawieniach zapory w Twojej organizacji. Program nie narażać portu 1433 granicą Azure. Można uruchomić narzędzia SSMS Azure przy użyciu [usługi Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Ta sytuacja nie wymaga otwarcia połączenia wychodzące do portu 1433, adres IP jest statyczny, dzięki czemu bazy danych może być otwarty tylko programów RemoteApp i obsługuje wiele Multi-Factor Authentication (MFA).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak monitorować i uregulowanie działania na mój serwer i bazę danych w bazie danych SQL?
#### <a name="sql-database-auditing"></a>Inspekcja bazy danych SQL
Baza danych SQL można włączyć ON inspekcji do śledzenia zdarzeń bazy danych. [SQL Database Auditing](sql-database-auditing.md) rejestruje zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji na koncie magazynu Azure. Inspekcja jest szczególnie przydatne, jeśli chcesz uzyskać wgląd w potencjalnych naruszeń zabezpieczeń i zasad, zachować zgodność z przepisami itp. Dzięki temu można zdefiniować i skonfigurować niektóre kategorie zdarzeń, które prawdopodobnie potrzebna przeprowadzania inspekcji i na podstawie uzyskasz wstępnie skonfigurowane raporty i pulpit nawigacyjny, aby uzyskać przegląd zdarzeń występujących w bazie danych. Można stosować te zasady inspekcji na poziomie bazy danych lub na poziomie serwera. Przewodnik na temat Włączanie inspekcji serwera/bazy danych, zobacz: [Włączanie inspekcji bazy danych SQL](sql-database-security-tutorial.md#enable-sql-database-auditing-if-necessary).

#### <a name="threat-detection"></a>Wykrywanie zagrożeń
Z [wykrywanie zagrożeń](sql-database-threat-detection.md), możesz uzyskać możliwość operować na naruszenia zabezpieczeń lub zasad odnalezione przez bardzo łatwo inspekcji. Nie trzeba być ekspertów reagowania na potencjalne zagrożenia lub naruszeń w systemie zabezpieczeń. Wykrywanie zagrożeń ma także niektóre wbudowane funkcje, takie jak wykrywanie iniekcja kodu SQL. Iniekcja kodu SQL jest próba zmiany lub naruszyć bezpieczeństwo danych i dość często sposób ogólnie zaatakowania aplikacji bazy danych. Wykrywanie zagrożeń bazy danych SQL działa wiele zestawów algorytmy, które Wykrywanie potencjalnych luk w zabezpieczeniach i ataki, a także wzorce dostępu nietypowych bazy danych (takich jak dostęp z nietypowych lokalizacji lub przez nieznane podmiot zabezpieczeń). Funkcjonariusze lub innych administratorów wyznaczonych otrzymywać wiadomość e-mail z powiadomieniem po wykryciu zagrożenia w bazie danych. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zalecenia dotyczące dalszego zbadania i ograniczyć zagrożenie. Aby dowiedzieć się, jak włączyć wykrywanie zagrożeń, zobacz: [włączyć wykrywanie zagrożeń bazy danych SQL](sql-database-security-tutorial.md#enable-sql-database-threat-detection). 
### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak chronić dane na ogół w bazie danych SQL?
Szyfrowanie zapewnia mechanizm silnej ochrony i zabezpieczyć poufne dane przed intruzami. Zaszyfrowanych danych jest używana do postaci bez generowania klucza odszyfrowującego. W związku z tym dodaje dodatkową warstwę ochrony na istniejące warstwy zabezpieczeń w bazie danych SQL. Istnieją dwa aspekty do ochrony danych w bazie danych SQL: 
- Danych, która jest w pozostałych plików danych i dziennika 
- Dane, który jest aktywny. 

W bazie danych SQL, domyślnie dane przechowywane w plikach dziennika i danych w podsystemie magazynowania jest całkowicie i zawsze szyfrowane za pomocą [przezroczystego szyfrowania danych [funkcji TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Tworzenie kopii zapasowych również są szyfrowane. Z funkcji TDE nie wprowadzono żadnych zmian wymaganych po stronie użytkownika aplikacji, który uzyskuje dostęp do tych danych. Szyfrowanie i odszyfrowywanie się tak zdarzyć w sposób przezroczysty; Dlatego nazwa. Do ochrony danych poufnych locie i magazynowane SQL Database zapewnia funkcję [zawsze zaszyfrowane (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE jest formą szyfrowania po stronie klienta, który szyfruje poufnych kolumny w bazie danych (tak, aby były w tekstu szyfrowanego dla administratorów bazy danych i nieautoryzowanym użytkownikom). Serwer odbiera rozpoczynać się od znaku zaszyfrowanych danych. Klucz dla zawsze zaszyfrowane jest przechowywana na po stronie klienta, aby tylko autoryzowani klienci można odszyfrować poufnych kolumn. Serwera i administratorów danych nie widzi poufnych danych, ponieważ klucze szyfrowania są przechowywane na kliencie. AE szyfruje poufnych kolumn w tabeli kompleksowe od nieautoryzowanych klientów na dysku fizycznym. AE dzisiaj, obsługuje porównywanie równości, więc DBAs mogą w dalszym ciągu zapytania zaszyfrowanych kolumn jako część ich poleceń SQL. Zawsze zaszyfrowane mogą być używane z różnych opcji magazynu kluczy, takich jak [usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), magazynu certyfikatów systemu Windows i lokalnej sprzętowych modułów zabezpieczeń.

|**Właściwości**|**Zawsze zaszyfrowane**|**Przezroczystego szyfrowania danych**|
|---|---|---|
|**Zakres szyfrowania**|End-to-end|Dane na rest|
|**Serwer bazy danych można uzyskać dostępu do poufnych danych**|Nie|Tak, ponieważ szyfrowanie danych magazynowanych|
|**Dozwolonych operacji T-SQL**|Porównania równości|Wszystkie powierzchni T-SQL jest dostępna|
|**Zmiany aplikacji wymagane jest użycie funkcji**|Minimalny|Minimalne|
|**Poziom szczegółowości szyfrowania**|Na poziomie kolumny|Baza danych ma poziom|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak ograniczyć dostęp do poufnych danych w bazie danych
Każda aplikacja ma nieco poufnych danych w bazie danych, które wymagają ochrony jest widoczny dla wszystkich użytkowników. Niektóre personelu w organizacji należy do wyświetlania tych danych, jednak inne nie powinien mieć możliwość wyświetlania tych danych. Przykładem jest płace pracownika. Menedżer jednak potrzebny dostęp do informacji płac jego bezpośrednich podwładnych, poszczególnych członków zespołu nie powinien mieć dostęp do informacji płac ich elementów równorzędnych. Inny scenariusz jest danych deweloperów, którzy mogą interakcji z danymi poufnymi etapach rozwoju lub testowania, na przykład numerów PESEL klientów. Te informacje ponownie nie musi zostać uwidoczniona dla deweloperów. W takich przypadkach poufne dane albo musi być przesłonięte lub nie będą widoczne na wszystkich. Baza danych SQL oferuje dwa takiego podejścia do uniemożliwić nieautoryzowanym użytkownikom na wyświetlanie danych poufnych:

[Dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md) to funkcja maskowania danych, która umożliwia ograniczenie ujawnienie danych poufnych przez maskowania go użytkownikom bez uprawnień na warstwie aplikacji. Zdefiniuj reguły maskowania, którą można utworzyć wzorzec maskowania (na przykład do pokazania tylko cztery ostatnie cyfry national SSN identyfikator: XXX-XX-0000 i oznacz ją jako Xs większość) i zidentyfikować użytkowników, którzy są mają być wykluczone z reguł maskowania. Maskowanie wykonywany na bieżąco i są dostępne dla różnych kategorii danych różnych funkcji maskowania. Maskowanie danych dynamicznych służy do automatycznego wykrywania poufnych danych w bazie danych i dotyczą maskowania.

[Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) umożliwia kontrolowanie dostępu na poziomie wiersza. To znaczy, niektóre wiersze w tabeli bazy danych na podstawie użytkownika wykonywania zapytania (grupy członkostwa lub wykonywania kontekst) są ukryte. Ograniczenie dostępu odbywa się na warstwie bazy danych, zamiast w warstwie aplikacji, aby uprościć logiki aplikacji. Rozpoczyna się od utworzenia predykatu filtru, filtrowanie wierszy, które nie są widoczne i zabezpieczeń zasad dalej Definiowanie kto ma dostęp do tych wierszy. Ponadto użytkownik końcowy uruchamia ich zapytania i, w zależności od uprawnień użytkownika, wyświetlić te wiersze ograniczony lub nie można znaleźć je na wszystkich.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak zarządzać kluczami szyfrowania w chmurze?

Dostępne są opcje zarządzania kluczami zawsze zaszyfrowane (szyfrowanie po stronie klienta) i przezroczystego szyfrowania danych (szyfrowanie w stanie spoczynku). Zaleca się regularne zmienić klucze szyfrowania. Częstotliwość obrotu powinno odpowiadać z przepisami wewnętrznej organizacji i wymagań dotyczących zgodności.

**Funkcji przezroczystego szyfrowania danych (TDE)**: Brak hierarchii dwa klucza w funkcji TDE — dane w każdej bazie danych użytkownika są szyfrowane przez symetrycznego AES 256 unikatowy bazy danych klucza szyfrowania bazy danych (jeśli Istnieje), który z kolei są szyfrowane za pomocą serwera unikatowy RSA asymetryczne 2048 klucza głównego. Klucz główny musi być zarządzany, albo:
- Automatycznie przez platformę — bazy danych SQL.
- Lub przez użytkownika za pomocą [usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako magazynu kluczy.

Domyślnie klucza głównego przezroczystego szyfrowania danych jest zarządzane przez usługę bazy danych SQL dla wygody. Jeśli Twoja organizacja będzie kontrolę nad klucza głównego, brak opcję do użycia jako magazynu kluczy Azure Vault](sql-database-always-encrypted-azure-key-vault.md) klucza. Za pomocą usługi Azure Key Vault, organizacji zakłada kontrolę nad formanty klawisza inicjowania obsługi administracyjnej, obracanie i uprawnienia. [Obracanie lub przełączenie typ klucza głównego funkcji TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) jest szybkie, jak tylko ponownie zaszyfrowanego klucza szyfrowania danych. Dla organizacji mających separacji ról między zabezpieczeń i zarządzanie danymi administratora zabezpieczeń można udostępnić materiału klucza dla klucza głównego funkcji TDE w usłudze Azure Key Vault i podaj identyfikator klucza usługi Azure Key Vault administratora bazy danych do użycia dla szyfrowanie przechowywanych na serwerze. Key Vault jest zaprojektowana tak, aby firma Microsoft nie jest w stanie Zobacz i Wyodrębnij klucze szyfrowania. Otrzymasz również scentralizowane zarządzanie kluczami dla Twojej organizacji. 

**Zawsze zaszyfrowane**: dostępne są także [hierarchii dwa klucza](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) w zawsze zaszyfrowane - kolumnę poufne dane są szyfrowane za AES 256 klucza szyfrowania kolumny (CEK), który jest natomiast szyfrowany za pomocą klucza głównego kolumny (CMK). Sterowniki klientów przewidzianych zawsze zaszyfrowane ma żadne ograniczenia dotyczące długości CMKs. Zaszyfrowaną wartość CEK są przechowywane w bazie danych, a CMK jest przechowywany w magazynie kluczy zaufanych, takich jak magazynu certyfikatów systemu Windows, usługi Azure Key Vault lub sprzętowego modułu zabezpieczeń. 
- Zarówno [CEK i CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) można obracać. 
- Obracanie CEK ma rozmiar operacji danych i mogą być intensywnie czas w zależności od rozmiaru tabel zawierających zaszyfrowanych kolumn. Dlatego rozsądne jest odpowiednio zaplanować CEK obrotów. 
- Obracanie CMK, jednak nie zakłóca wydajność bazy danych i może odbywać się z rolami rozdzielonych.
Na poniższym diagramie przedstawiono opcje magazynu kluczy dla kluczy głównych kolumn w zawsze zaszyfrowane

![Zawsze zaszyfrowane CMK przechowywania dostawców](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak zoptymalizować i ochronę ruchu między mojej organizacji i bazy danych SQL?
Ruch sieciowy między organizacji i bazy danych SQL czy pobrać zazwyczaj kierowane za pośrednictwem sieci publicznej. Jednak jeśli chcesz zoptymalizować tę ścieżkę i bezpieczniejsze, mogą zobaczyć w Express Route. Usługi Express route umożliwia zasadniczo rozszerzona platformy Azure z siecią firmową za pośrednictwem połączenia prywatnego. W ten sposób nie przejść za pośrednictwem publicznej sieci Internet. Możesz również uzyskać wyższy poziom zabezpieczeń, niezawodności i optymalizacji routingu, który tłumaczy do dolnej opóźnienia sieci i prędkości szybciej, niż można zwykle doświadczenie przez publicznego Internetu. Jeśli planujesz przesyłania znaczących fragmentów danych między organizacji i Azure, przy użyciu Express Route mogą spowodować oszczędności. Są dostępne trzy modele innego połączenia dla połączenia z Twojej organizacji do platformy Azure: 
- [Kolokacja chmury programu Exchange](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Any-to-any](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Usługi Express Route umożliwia również serii maksymalnie 2 x limit przepustowości, zakupu bez dodatkowych opłat. Istnieje również możliwość konfigurowania cross łączności region przy użyciu Express route. Aby wyświetlić listę dostawców łączności ER, zobacz: [Express partnerów trasy i lokalizacje równorzędna](../expressroute/expressroute-locations.md). Poniższe artykuły zawierają Express Route bardziej szczegółowo:
- [Wprowadzenie na Express Route](../expressroute/expressroute-introduction.md)
- [Wymagania wstępne](../expressroute/expressroute-prerequisites.md)
- [Przepływy pracy](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Jest zgodna z wymagań regulacyjnych bazy danych SQL, i w jaki sposób który pomaga z własnej organizacji zgodności?
Baza danych SQL jest zgodne z przepisami compliances zakresem. Aby wyświetlić najnowsze zbiór compliances, które zostały spełnione, odwiedź stronę [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/compliance/complianceofferings) oraz rozwijanie compliances, które są ważne dla organizacji, aby zobaczyć, czy baza danych SQL jest objęte zgodny z usług Azure. Należy pamiętać, że chociaż baza danych SQL mogą mieć certyfikat potwierdzający usługi zgodne, pomaga w zgodności organizacji usługi ale nie automatycznie gwarantuje on.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentnego bazy danych monitorowania oraz konserwacji po migracji

Po migracji bazy danych do bazy danych SQL, są, przechodząc do chcesz monitorować bazy danych (na przykład Sprawdź, czy sposób wykorzystania zasobów takich jak lub DBCC sprawdza) oraz wykonywania konserwacji (na przykład odbudować lub zreorganizować indeksy i statystyki itp.). Na szczęście bazy danych SQL jest Intelligent w tym sensie, że do aktywnego ułatwić monitorowanie i konserwacja bazy danych, dzięki czemu aplikacja działa optymalnie zawsze używa historyczne trendy i zarejestrowane metryki i statystyki. W niektórych przypadkach bazy danych SQL Azure automatycznie można wykonać zadania konserwacji, w zależności od ustawień konfiguracji. Istnieją trzy aspekty monitorowania bazy danych w bazie danych SQL:
- Monitorowanie wydajności i optymalizacji.
- Optymalizacja zabezpieczeń.
- Optymalizacja kosztów.

**Monitorowanie wydajności i optymalizacji**: Z zapytania szczegółowe informacje o wydajności, możesz też uzyskać zalecenia dopasowane obciążenie bazy danych, dzięki czemu aplikacje mogą działanie na optymalną — zawsze. Można również skonfigurowaniu go tak, aby automatycznie zastosowane te zalecenia i jest konieczne wykonywanie zadań konserwacji odblokowane. Z Doradcę w zakresie indeksowania można automatycznie zaimplementować zalecenia dotyczące indeksu na obciążenie — ta metoda jest wywoływana automatycznego dostrajania. Zalecenia rozwijać, zmiana obciążenia aplikacji dostarczają najbardziej odpowiednie sugestie. Otrzymasz również opcję, aby ręcznie sprawdzić te zalecenia i zastosować je według uznania.  

**Optymalizacja zabezpieczeń**: baza danych SQL zawiera zalecenia dotyczące zabezpieczeń można wykonać, aby zabezpieczyć dane i wykrywanie zagrożeń identyfikacji i bazy danych podejrzanych działań, które mogą stanowić potencjalne wątku w celu badania Baza danych. [Oceny luk w zabezpieczeniach SQL](sql-vulnerability-assessment.md) jest bazą danych skanowania i raportowania usługi, która umożliwia monitorowanie stanu zabezpieczeń baz danych na dużą skalę i identyfikować zagrożenia bezpieczeństwa oraz rozchodzenia z planu bazowego zabezpieczeń zdefiniowane przez użytkownika. Po każdym skanowaniu listę niestandardowych można wykonać kroki i skrypty korygowania podano oraz raport oceny, który może służyć za pomoc w celu zapewnienia zgodności.

W Centrum zabezpieczeń Azure możesz zidentyfikować zalecenia dotyczące zabezpieczeń w tablicy i zastosować je za pomocą jednego kliknięcia. 

**Koszt optymalizacji**: platformy Azure SQL analizuje historii wykorzystania dla baz danych na serwerze do oceny i zaleca opcjami optymalizację kosztów. Analiza potrzebuje zazwyczaj dwóch tygodni do analizowania i tworzenie praktyczne zalecenia. Pula elastyczna jest jedną z opcji. Zalecane jest wyświetlana w portalu jako transparent:

![zalecenia dotyczące puli elastycznej](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png) 

Analiza można również znaleźć w sekcji "Doradcy":

![zalecenia dotyczące puli elastycznej-advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak monitorować wydajność i użycie zasobów w bazie danych SQL

W bazie danych SQL można wykorzystać inteligentnego szczegółowych danych platformy do monitorowania wydajności i odpowiednio dostosować. Można monitorować wydajność i użycie zasobów w bazie danych SQL przy użyciu następujących metod:
- **Azure portal**: Azure portal zawiera wykorzystanie pojedynczej bazy danych wybraniu bazy danych, a następnie klikając na wykresie w okienku przeglądu. Można zmodyfikować na wykresie, aby wyświetlić wiele metryk, takich jak procent użycia procesora CPU, procent użycia jednostek DTU, dane we/wy wartość procentowa i sesje oraz jako procent rozmiaru bazy danych.

   ![Wykres monitorowania](./media/sql-database-manage-after-migration/monitoring-chart.png)

   ![Monitorowanie — wykres 2](./media/sql-database-manage-after-migration/chart.png)

Z tego wykresu można również skonfigurować alerty przez zasób. Te alerty pozwalają na odpowiadanie na warunkach zasobów z wiadomości e-mail, zapisać punktu końcowego HTTPS/HTTP lub wykonania akcji. Zobacz [monitorowanie wydajności bazy danych w bazie danych SQL](sql-database-single-database-monitor.md) szczegółowe informacje.

- **Dynamicznych widoków zarządzania**: można zbadać [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamiczny widok zarządzania do zwrócenia historii statystyk zużycia zasobów z ostatniej godziny i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) widoku wykazu systemu do zwrócenia historii przez ostatnie 14 dni.
- **Szczegółowe informacje o wydajności zapytań**: [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) umożliwia wyświetlenie historii najważniejszych zapytań korzystanie z zasobów i długotrwałe zapytania dotyczące określonej bazy danych. Umożliwi szybkie identyfikowanie najważniejszych zapytań przez wykorzystanie zasobów, czas trwania i częstotliwość wykonywania. Można śledzić zapytania i wykrywania regresji. Ta funkcja wymaga [magazyn zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) być włączone i aktywnej bazy danych.

   ![Szczegółowe informacje o wydajności zapytań](./media/sql-database-manage-after-migration/query-performance-insight.png)

- **Azure Analytics SQL (wersja zapoznawcza) w analizy dzienników**: [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) służy do zbierania i wizualizacji kluczowych miar wydajności usługi Azure SQL Azure, Obsługa maksymalnie 150 000 baz danych i pul elastycznych 5000 SQL na obszar roboczy. Służy on do monitorowania i otrzymywać powiadomienia. Można monitorować bazy danych SQL i metryki elastycznej puli w wielu subskrypcji platformy Azure i pule elastyczne i mogą służyć do identyfikowania problemów w każdej warstwie stosu aplikacji.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jestem I powiadamiania problemy z wydajnością: jak Moje metodologia rozwiązywania problemów z bazy danych SQL różni się od programu SQL Server?
Główna część techniki rozwiązywania problemów, można skorzystać do diagnozowania zapytania i problemy z wydajnością bazy danych pozostają takie same. Po tym samym serwerze SQL aparatu uprawnień chmury. Jednak platforma - bazy danych SQL Azure ma wbudowany "analizy". Go ułatwiają rozwiązywanie problemów i diagnozowanie problemów z wydajnością nawet łatwiej. On również wykonać niektóre z tych działań korygujących w Twoim imieniu, a w niektórych przypadkach, aktywne naprawione - automatycznie. 

Użytkownika podejście do rozwiązywania problemów z wydajnością może znacząco skorzystać za pomocą funkcji inteligentnego, takich jak [Insight(QPI) wydajności kwerendy](sql-database-query-performance.md) i [doradcy bazy danych](sql-database-advisor.md) w połączeniu i Dzięki różnica w metodologii różni się w tym zakresie — nie należy wykonać ręcznych stopnia podstawowych szczegółowych informacji, które mogą ułatwić rozwiązania problemu z wykonywanego. Platforma wykonuje praca. Przykładem tego jest QPI. QPI można przejść do szczegółów aż do poziomu zapytania i ich przyjrzeć się historyczne trendy i zorientować się, kiedy uległa pogorszeniu w wersji dokładnie zapytania. Doradcy bazy danych zapewnia zalecenia na rzeczy, które mogą pomóc w usprawnieniu na ogólną wydajność ogólnie takie jak - brakujące indeksy porzucenie indeksów, parametryzacja zapytań itp. 

Wydajność rozwiązywania problemów, należy ustalić, czy jest tylko aplikacja lub jego kopię bazy danych, która jest wpływające na wydajność aplikacji. Często problem z wydajnością znajduje się w warstwie aplikacji. Może to być architektura lub wzorca dostępu do danych. Rozważmy na przykład, że masz chatty aplikacji, która jest wrażliwy na opóźnienia sieci. W takim przypadku aplikacji wystąpi, ponieważ będzie wiele żądań krótkich przechodzi do i z powrotem ("chatty") pomiędzy aplikacją i serwera i sieci przeciążona, Dodaj te dwukierunkowe przesyłanie danych fast. Aby zwiększyć wydajność w tym przypadku, można użyć [partii zapytań](sql-database-performance-guidance.md#batch-queries). Przy użyciu partie pomaga znacznie ponieważ teraz przetworzenie swoje żądania w partii; w związku z tym pomaga skrócić czas oczekiwania obie strony i poprawić wydajność aplikacji. 

Ponadto w przypadku zauważenia spadku ogólnej wydajności bazy danych można monitorować [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamicznego zarządzania widoków w celu Dowiedz się, użycie procesora CPU, we/wy i pamięci. Wydajność może być w pełni funkcjonalne, ponieważ baza danych jest zagłodzone zasobów. Może to być należy zmienić poziom wydajności i/lub warstwę, na podstawie powiększania i zmniejszanie obciążenia żądania usługi. 

Aby rozbudowany zestaw zaleceń dotyczących dostrajania problemy z wydajnością, zobacz: [dostrajania bazy danych](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Jak zapewnić, że używam odpowiednią warstwę i poziom wydajności usługi?
Baza danych SQL oferuje różnych warstwach usług podstawowa, standardowa i Premium. Każdej warstwy usług, należy pobrać gwarantowane przewidywalną wydajność związana z poziomu tej usługi. W zależności od obciążenia może być seria działań gdzie z wykorzystania zasobów może trafień limitu bieżący poziom wydajności, które są w. W takich przypadkach warto pierwszego uruchomienia wyniku obliczenia czy żadnych dostrajanie może pomóc (na przykład, dodawanie lub zmienianie indeksu itp.). Jeśli nadal występują problemy limit, należy wziąć pod uwagę przeniesienie na wyższy poziom wydajności i poziomu usług. 

|**Poziom usług**|**Typowe scenariusze przypadków użycia**|
|---|---|
|**Podstawowa**|Aplikacje z kilku użytkowników i bazy danych, które nie mają wysokie wymagania współbieżności, skalowalność i wydajność. |
|**Standardowa**|Aplikacje z znaczne wymagania współbieżności, skalowalność i wydajność połączone z niskiego na Średnia zapotrzebowanie we/wy. |
|**Premium**|Aplikacje z dużą liczbą jednoczesnych użytkowników, Procesora/pamięci wysokiej i wysoki żądań We/Wy. Poziom Premium mogą korzystać z wysokiej współbieżności, wysokiej przepływności i opóźnienia poufnych aplikacji. |
|||

Upewnić się, że jesteś na poziomie wydajności prawo, można monitorować zapytań i bazy danych użycia zasobów za pomocą jednego z wyżej wymienionych metod "Jak monitorować wydajności i użycia zasobów w bazie danych SQL". Użytkownik stwierdzi, że zapytania lub bazy danych działają spójnie gorących na Procesora/pamięci itp., należy rozważyć skalowaniu na wyższy poziom wydajności. Podobnie jeśli użytkownik należy pamiętać, że nawet w Twojej godzinach szczytu, prawdopodobnie nie korzystać z zasobów tyle; należy rozważyć skalowania z bieżący poziom wydajności. 

Jeśli wzorzec aplikacji SaaS lub scenariusza konsolidacji bazy danych, rozważ użycie puli elastycznej dla optymalizację kosztów. Pula elastyczna jest doskonałym sposobem uzyskania konsolidacji bazy danych i optymalizację kosztów. Aby uzyskać więcej informacji na temat zarządzania wieloma bazami danych przy użyciu puli elastycznej, zobacz: [Zarządzanie pul i baz danych](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases). 

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak często konieczne, uruchom sprawdzanie integralności bazy danych dla bazy danych?
Baza danych SQL używa niektóre techniki inteligentne, umożliwiające obsługę określonych klas uszkodzenie danych i automatycznie bez utraty danych. Te techniki wbudowanych w usługę i są wykorzystywane przez usługę podczas muszą powstaje. Regularnie sprawdzane są kopie zapasowe bazy danych przez usługę Przywracanie ich, a na nim uruchomione polecenie DBCC CHECKDB. Jeśli występują problemy, baza danych SQL aktywne adresy je. [Naprawy strony](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) jest wykorzystywana do ustalenia stron, które nie są uszkodzone lub mają problemy z integralnością danych. Strony bazy danych zawsze są weryfikowane z domyślnym ustawieniem sumę KONTROLNĄ, która weryfikuje integralność strony. Baza danych SQL aktywnie monitoruje i monitoruje integralność bazy danych oraz, jeśli wystąpią problemy, adresów je o najwyższym priorytecie. Oprócz tych można opcjonalnie uruchamiaj własne sprawdzanie integralności momencie użytkownika.  Aby uzyskać więcej informacji, zobacz [integralność danych w bazie danych SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Przenoszenie danych po migracji

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Jak wyeksportować i zaimportować dane jako pliki pliku BACPAC z bazy danych SQL

- **Eksportuj**: baza danych Azure SQL można wyeksportować w formacie pliku BACPAC z portalu Azure

   ![Eksportowanie bazy danych](./media/sql-database-export/database-export.png)

- **Importuj**: można również zaimportować dane w formacie pliku BACPAC, do bazy danych przy użyciu portalu Azure.

   ![Importowanie bazy danych](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Sposób synchronizacji danych między bazą danych SQL i programu SQL Server?
Istnieje kilka sposobów osiągnięcia tego: 
- **[Synchronizacja danych](sql-database-sync-data.md)**  — ta funkcja pomaga w synchronizacji danych dwukierunkowo między wieloma lokalnych baz danych programu SQL Server i bazy danych SQL. Synchronizacja lokalnych baz danych programu SQL Server, należy zainstalować i skonfigurować synchronizację agenta na komputerze lokalnym i otwórz wychodzący port TCP 1433.
- **[Transakcja replikacji](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  — w przypadku replikacji transakcji można synchronizować dane z lokalnej bazy danych SQL Azure z lokalnego wydawcy i subskrybencie są bazy danych SQL Azure. Obecnie jest obsługiwany tylko w tej instalacji. Aby uzyskać więcej informacji na temat migracji danych z lokalnego do usługi Azure SQL z minimalnym czasem przestojów, zobacz: [użycie transakcji replikacji](sql-database-cloud-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [bazy danych SQL](sql-database-technical-overview.md).

