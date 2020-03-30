---
title: Zarządzanie pojedynczymi i buforowanymi bazami danych po migracji
description: Dowiedz się, jak zarządzać bazą danych po migracji do usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: ebb512fee0186bed3cc7f49f0525dac43e57da3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256187"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Nowa dba w chmurze — zarządzanie pojedynczymi i łączonych baz danych w bazie danych SQL Azure

Przejście od tradycyjnego środowiska zarządzanego samodzielnie, samokontroli do środowiska PaaS może wydawać się nieco przytłaczające na początku. Jako deweloper aplikacji lub DBA chcesz poznać podstawowe możliwości platformy, które pomogą Ci zachować aplikację dostępną, wydajną, bezpieczną i odporną — zawsze. Ten artykuł ma na celu zrobić dokładnie to. W artykule zwięźle organizuje zasoby i zawiera pewne wskazówki dotyczące jak najlepiej korzystać z kluczowych możliwości bazy danych SQL z pojedynczych i puli baz danych do zarządzania i utrzymania aplikacji działa wydajnie i osiągnąć optymalne wyniki w chmurze. Typową odbiorcą tego artykułu będą ci, którzy:

- Oceniają migrację ich aplikacji do usługi Azure SQL Database — modernizując aplikacje.
- Są w trakcie migracji ich aplikacji — w toku scenariusz migracji.
- Niedawno zakończono migrację do usługi Azure SQL DB — new DBA w chmurze.

W tym artykule omówiono niektóre z podstawowych cech usługi Azure SQL Database jako platformy, które można łatwo wykorzystać podczas pracy z pojedynczych baz danych i puli baz danych w pulach elastycznych. Są one następujące:

- Monitorowanie bazy danych za pomocą portalu Azure
- Ciągłość biznesowa i odzyskiwanie po awarii (BCDR)
- Bezpieczeństwo i zgodność
- Inteligentne monitorowanie i konserwacja baz danych
- Przenoszenie danych

> [!NOTE]
> Ten artykuł dotyczy następujących opcji wdrażania w usłudze Azure SQL Database: pojedyncze bazy danych i pule elastyczne. Nie ma zastosowania do opcji wdrażania wystąpienia zarządzanego w bazie danych SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal

W [witrynie Azure portal](https://portal.azure.com/)można monitorować wykorzystanie poszczególnych baz danych, wybierając bazę danych i klikając wykres **monitorowania.** Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

- Procent użycia procesora CPU
- Procent użycia jednostek DTU
- Procent użycia operacji we/wy na danych
- Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk można nadal wyświetlać je na wykresie **monitorowania,** aby uzyskać więcej informacji w oknie **metryki.** Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Aby uzyskać więcej informacji na temat warstw usług, zobacz oparte [na usłudze DTU modele zakupów](sql-database-service-tiers-dtu.md) i artykuły na [modelach zakupów opartych na platformach](sql-database-service-tiers-vcore.md) wirtualnych.  

![Monitorowanie warstw usług pod względem wydajności bazy danych.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Możesz również skonfigurować alerty dotyczące metryk wydajności. Kliknij przycisk **Dodaj alert** w oknie **Metryka**. Użyj kreatora, aby skonfigurować alert. Istnieje możliwość ustawienia alertu, który będzie wysyłany, gdy metryki przekroczą określony próg lub spadną poniżej określonego progu.

Jeśli na przykład spodziewasz się, że obciążenie bazy danych wzrośnie, możesz skonfigurować alert polegający na wysłaniu wiadomości e-mail, gdy dowolna z metryk wydajności osiągnie 80%. Można użyć tego jako wczesnego ostrzeżenia, aby dowiedzieć się, kiedy może być trzeba przełączyć się do następnego najwyższego rozmiaru obliczeń.

Metryki wydajności może również pomóc w określeniu, czy można obniżyć do niższego rozmiaru obliczeń. Załóżmy, że używasz bazy danych, której warstwa i poziom to Standardowa S2, a wszystkie metryki wydajności pokazują, że baza danych wykorzystuje średnio nie więcej niż 10% w określonym czasie. Istnieje prawdopodobieństwo, że baza danych będzie działać równie dobrze na poziomie Standardowa S1. Należy jednak pamiętać o obciążeniach, które skok lub wahania przed podjęciem decyzji, aby przejść do niższego rozmiaru obliczeń.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Ciągłość biznesowa i odzyskiwanie po awarii (BCDR)

Ciągłość działania i możliwości odzyskiwania po awarii umożliwiają kontynuowanie działalności, jak zwykle, w przypadku awarii. Katastrofą może być zdarzenie na poziomie bazy danych (na przykład ktoś omyłkowo upuszcza kluczową tabelę) lub zdarzenie na poziomie centrum danych (katastrofa regionalna, na przykład tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak tworzyć kopie zapasowe i zarządzać nimi w bazie danych SQL

Nie należy tworzyć kopii zapasowych na platformie Azure SQL DB, a to dlatego, że nie trzeba. Baza danych SQL automatycznie kopiuje kopie zapasowe baz danych, dzięki czemu nie musisz już martwić się o planowanie, wykonywanie i zarządzanie kopiami zapasowymi. Platforma wykonuje pełną kopię zapasową co tydzień, różnicową kopię zapasową co kilka godzin i kopię zapasową dziennika co 5 minut, aby zapewnić wydajność odzyskiwania po awarii, a utrata danych minimalna. Pierwsza pełna kopia zapasowa odbywa się natychmiast po utworzeniu bazy danych. Te kopie zapasowe są dostępne dla danego okresu o nazwie "Okres przechowywania" i różni się w zależności od warstwy usług, którą wybierzesz. Baza danych SQL umożliwia przywrócenie do dowolnego punktu w czasie w tym okresie przechowywania przy użyciu [funkcji wykańczania czasu (PITR).](sql-database-recovery-using-backups.md#point-in-time-restore)

|Warstwa usług|Okres przechowywania w dniach|
|---|:---:|
|Podstawowa (Basic)|7|
|Standardowa|35|
|Premium|35|
|||

Ponadto funkcja [długoterminowego przechowywania (LTR)](sql-database-long-term-retention.md) umożliwia przechowywanie plików kopii zapasowych przez znacznie dłuższy okres, w szczególności przez okres do 10 lat, i przywracanie danych z tych kopii zapasowych w dowolnym momencie w tym okresie. Ponadto kopie zapasowe bazy danych są przechowywane w magazynach replikowanych geograficznie, aby zapewnić odporność na katastrofy regionalne. Można również przywrócić te kopie zapasowe w dowolnym regionie platformy Azure w dowolnym momencie w okresie przechowywania. Zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak zapewnić ciągłość działania w przypadku katastrofy na poziomie centrum danych lub katastrofy regionalnej

Ponieważ kopie zapasowe bazy danych są przechowywane w magazynie replikowanym geograficznie, aby upewnić się, że w przypadku awarii regionalnej można przywrócić kopię zapasową do innego regionu platformy Azure. Jest to nazywane przywracanie geograficzne. Cel punktu odzyskiwania (Punkt odzyskiwania) jest zazwyczaj < 1 godzinę i ERT (Szacowany czas odzyskiwania – od kilku minut do godzin).

W przypadku baz danych o znaczeniu krytycznym usługa Azure SQL DB oferuje aktywną replikację geograficzną. Zasadniczo powoduje to, że tworzy replikowaną geograficznie pomocniczą kopię oryginalnej bazy danych w innym regionie. Na przykład jeśli baza danych jest początkowo hostowana w regionie zachodnie stany USA platformy Azure i chcesz regionalnej odporności na awarie. Należy utworzyć aktywną replikę geograficzną bazy danych w zachodnie stany USA, aby powiedzieć wschodnie stany USA. Kiedy klęska uderza w zachodnie stany USA, możesz awaryjnie przejść do regionu Wschodnich Stanów Zjednoczonych. Konfigurowanie ich w grupie automatycznego trybu failover jest jeszcze lepsze, ponieważ zapewnia to, że baza danych automatycznie przechodzi w tryb failover do pomocniczego w wschodnich stanach USA w przypadku awarii. RPO jest < 5 sekund, a ERT < 30 sekund.

Jeśli grupa automatycznego trybu failover nie jest skonfigurowana, aplikacja musi aktywnie monitorować awarię i inicjować od pracy awaryjnej do pomocniczego. Można utworzyć maksymalnie 4 takie aktywne repliki geograficzne w różnych regionach platformy Azure. Jest jeszcze lepiej. Można również uzyskać dostęp do tych pomocniczych aktywnych replik geograficznych w celu uzyskania dostępu tylko do odczytu. Jest to bardzo przydatne, aby zmniejszyć opóźnienia dla scenariusza aplikacji rozproszonej geograficznie.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak mój plan odzyskiwania po awarii zmienia się z lokalnego na bazę danych SQL

Podsumowując, tradycyjna lokalna konfiguracja programu SQL Server wymagała aktywnego zarządzania dostępnością przy użyciu takich funkcji, jak klastrowanie trybu failover, dublowanie baz danych, replikacja transakcji lub wysyłka dziennika oraz obsługa kopii zapasowych i zarządzanie nimi w celu zapewnienia Ciągłości. Dzięki bazie danych SQL platforma zarządza nimi za Ciebie, dzięki czemu możesz skupić się na opracowywaniu i optymalizowaniu aplikacji bazy danych i nie martwić się o zarządzanie klęskami żywiołowymi. Możesz mieć skonfigurowane plany tworzenia kopii zapasowych i odzyskiwania po awarii i pracy z zaledwie kilku kliknięć w witrynie Azure portal (lub kilka poleceń przy użyciu interfejsów API programu PowerShell).

Aby dowiedzieć się więcej o odzyskiwaniu po awarii, zobacz: [Odzyskiwanie po awarii usługi Azure SQL Db 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Bezpieczeństwo i zgodność

Baza danych SQL traktuje bezpieczeństwo i prywatność bardzo poważnie. Zabezpieczenia w bazie danych SQL jest dostępny na poziomie bazy danych i na poziomie platformy i jest najlepiej zrozumiałe, gdy podzielone na kilka warstw. W każdej warstwie można uzyskać kontrolę i zapewnić optymalne bezpieczeństwo dla aplikacji. Warstwy są następujące:

- Uwierzytelnianie & tożsamości[(uwierzytelnianie SQL i uwierzytelnianie usługi Azure Active Directory [AAD].](sql-database-manage-logins.md)
- Monitorowanie aktywności[(Inspekcja](sql-database-auditing.md) i [wykrywanie zagrożeń).](sql-database-threat-detection.md)
- Ochrona rzeczywistych danych[(przezroczyste szyfrowanie danych [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) i [zawsze szyfrowane [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Kontrolowanie dostępu do poufnych i uprzywilejowanych danych[(zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) i [dynamiczne maskowanie danych).](/sql/relational-databases/security/dynamic-data-masking)

[Usługa Azure Security Center](https://azure.microsoft.com/services/security-center/) oferuje scentralizowane zarządzanie zabezpieczeniami między obciążeniami działającymi na platformie Azure, lokalnie i w innych chmurach. Można wyświetlić, czy podstawowa ochrona bazy danych SQL, takich jak [inspekcja](sql-database-auditing.md) i [przezroczyste szyfrowanie danych [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) są skonfigurowane na wszystkich zasobach i tworzyć zasady na podstawie własnych wymagań.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jakie metody uwierzytelniania użytkowników są oferowane w bazie danych SQL

Istnieją dwie metody uwierzytelniania oferowane w bazie danych SQL:

- [Uwierzytelnianie usługi Azure Active Directory](sql-database-aad-authentication.md)
- [Uwierzytelnianie SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Tradycyjne uwierzytelnianie systemu Windows nie jest obsługiwane. Usługa Azure Active Directory (AD) to scentralizowana usługa zarządzania tożsamościami i dostępem. Dzięki temu można bardzo wygodnie zapewnić dostęp do logowania jednokrotnego (SSO) dla wszystkich pracowników w organizacji. Oznacza to, że poświadczenia są współużytkowane przez wszystkie usługi platformy Azure w celu prostszego uwierzytelniania. Usługa AAD obsługuje usługę [MFA (uwierzytelnianie wieloskładnikowe)](sql-database-ssms-mfa-authentication.md) i za [pomocą kilku kliknięć](../active-directory/hybrid/how-to-connect-install-express.md) AAD można zintegrować z usługą Windows Server Active Directory. Uwierzytelnianie SQL działa dokładnie tak, jak używasz go w przeszłości. Podajesz nazwę użytkownika/hasło i można uwierzytelniać użytkowników do dowolnej bazy danych na danym serwerze bazy danych SQL. Dzięki temu baza danych SQL i usługa SQL Data Warehouse mogą oferować uwierzytelnianie wieloskładnikowe i konta użytkowników-gości w domenie usługi Azure AD. Jeśli masz już usługę Active Directory lokalnie, możesz sfedować katalog za pomocą usługi Azure Active Directory, aby rozszerzyć katalog na platformę Azure.

|**Jeśli...**|**Baza danych SQL / magazyn danych SQL**|
|---|---|
|Wolisz nie używać usługi Azure Active Directory (AD) na platformie Azure|Korzystanie z [uwierzytelniania SQL](sql-database-security-overview.md)|
|Używana usługa AD w środowisku lokalnym programu SQL Server|[Federuj usługę AD za pomocą usługi Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)i korzystaj z uwierzytelniania usługi Azure AD. Dzięki temu można użyć logowania jednokrotnego.|
|Konieczność wymuszania uwierzytelniania wieloskładnikowego (MFA)|Wymagaj usługi MFA jako zasad za pośrednictwem [programu Microsoft Conditional Access](sql-database-conditional-access.md)i używaj [uniwersalnego uwierzytelniania usługi Azure AD z obsługą usługi MFA.](sql-database-ssms-mfa-authentication.md)|
|Mieć konta gości z kont Microsoft (live.com, outlook.com) lub innych domen (gmail.com)|Użyj [uniwersalnego uwierzytelniania usługi Azure AD](sql-database-ssms-mfa-authentication.md) w bazie danych/hurtowni danych SQL, która wykorzystuje [współpracę usługi Azure AD B2B.](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)|
|Są zalogowani w systemie Windows przy użyciu poświadczeń usługi Azure AD z domeny federacyjnej|Użyj [zintegrowanego uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md).|
|Są zalogowani w systemie Windows przy użyciu poświadczeń z domeny niesfederowanej z platformą Azure|Użyj [zintegrowanego uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md).|
|Mieć usługi warstwy środkowej, które muszą łączyć się z bazą danych SQL lub magazynem danych SQL|Użyj [zintegrowanego uwierzytelniania usługi Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak ograniczyć lub kontrolować dostęp do łączności do bazy danych

Istnieje wiele technik do państwa dyspozycji, które można użyć do osiągnięcia optymalnej organizacji łączności dla aplikacji.

- Reguły zapory
- Punkty końcowe usługi sieci wirtualnej
- Zastrzeżone adresy IP

#### <a name="firewall"></a>Zapora

Zapora uniemożliwia dostęp do serwera z jednostki zewnętrznej, zezwalając tylko określonym jednostkom na dostęp do serwera bazy danych SQL. Domyślnie wszystkie połączenia i bazy danych wewnątrz serwera bazy danych SQL są niedozwolone, z wyjątkiem połączeń pochodzących z innych usług platformy Azure. Za pomocą reguły zapory można otworzyć dostęp do serwera tylko do jednostek (na przykład komputera dewelopera), które zatwierdzasz, zezwalając na adres IP tego komputera za pośrednictwem zapory. Umożliwia również określenie zakresu adresów IP, które mają umożliwić dostęp do serwera bazy danych SQL. Na przykład adresy IP komputera dewelopera w organizacji można dodać jednocześnie, określając zakres na stronie Ustawienia zapory.

Reguły zapory można tworzyć na poziomie serwera lub na poziomie bazy danych. Reguły zapory IP na poziomie serwera można tworzyć za pomocą witryny Azure portal lub za pomocą usługi SSMS. Aby dowiedzieć się więcej o ustawianiu reguły zapory na poziomie serwera i bazy danych, zobacz: [Tworzenie reguł zapory IP w bazie danych SQL](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Punkty końcowe usługi

Domyślnie baza danych SQL jest skonfigurowana jako "Zezwalaj usługom platformy Azure na dostęp do serwera" — co oznacza, że każda maszyna wirtualna na platformie Azure może próbować połączyć się z bazą danych. Te próby nadal muszą zostać uwierzytelnione. Jeśli jednak baza danych nie ma być dostępna dla dowolnej platformy IP platformy Azure, można wyłączyć opcję "Zezwalaj na dostęp do serwera usług platformy Azure". Ponadto można skonfigurować [punkty końcowe usługi sieci wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md).

Punkty końcowe usługi (SE) umożliwiają udostępnianie krytycznych zasobów platformy Azure tylko do własnej prywatnej sieci wirtualnej na platformie Azure. W ten sposób zasadniczo wyeliminować publiczny dostęp do zasobów. Ruch między siecią wirtualną do platformy Azure pozostaje w sieci szkieletowej platformy Azure. Bez SE otrzymasz wymuszone tunelowanie routingu pakietów. Twoja sieć wirtualna wymusza ruch internetowy do organizacji i ruchu usługi Azure, aby przejść przez tę samą trasę. Dzięki punktom końcowym usługi można to zoptymalizować, ponieważ pakiety przepływają bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Azure.

![Punkty końcowe usługi sieci wirtualnej](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Zastrzeżone adresy IP

Inną opcją jest [aprowizgorować zarezerwowane adresy IP](../virtual-network/virtual-networks-reserved-public-ip.md) dla maszyn wirtualnych i dodać te określone adresy IP maszyny wirtualnej w ustawieniach zapory serwera. Przypisując zastrzeżone adresy IP, można zapisać problem konieczności aktualizacji reguł zapory ze zmieniającymi się adresami IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Na jakim porcie mogę połączyć się z bazą danych SQL

Port 1433. Baza danych SQL komunikuje się za pośrednictwem tego portu. Aby połączyć się z sieci firmowej, należy dodać regułę ruchu wychodzącego w ustawieniach zapory w organizacji. Jako wytyczne należy unikać ujawniania portu 1433 poza granicami platformy Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak mogę monitorować i regulować aktywność na moim serwerze i w bazie danych w bazie danych SQL

#### <a name="sql-database-auditing"></a>Inspekcja usługi SQL Database

Za pomocą bazy danych SQL można włączyć inspekcję, aby śledzić zdarzenia bazy danych. [Inspekcja bazy danych SQL](sql-database-auditing.md) rejestruje zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji na koncie usługi Azure Storage. Inspekcja jest szczególnie przydatna, jeśli zamierzasz uzyskać wgląd w potencjalne naruszenia zabezpieczeń i zasad, zachować zgodność z przepisami itp. Umożliwia definiowanie i konfigurowanie niektórych kategorii zdarzeń, które twoim zdaniem wymagają inspekcji i na podstawie tego można uzyskać wstępnie skonfigurowane raporty i pulpit nawigacyjny, aby uzyskać przegląd zdarzeń występujących w bazie danych. Te zasady inspekcji można zastosować na poziomie bazy danych lub na poziomie serwera. Przewodnik dotyczący włączania inspekcji serwera/bazy danych, zobacz: [Włączanie inspekcji baz danych SQL](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Wykrywanie zagrożeń

Dzięki [wykrywaniu zagrożeń](sql-database-threat-detection.md)otrzymujesz możliwość działania w przypadku naruszeń zabezpieczeń lub zasad wykrytych przez inspekcję bardzo łatwo. Nie musisz być ekspertem w dziedzinie bezpieczeństwa, aby zaradzić potencjalnym zagrożeniom lub naruszeniom w systemie. Wykrywanie zagrożeń ma również pewne wbudowane funkcje, takie jak wykrywanie iniekcji SQL. Sql Injection jest próbą zmiany lub naruszenia danych i dość powszechny sposób atakowania aplikacji bazy danych w ogóle. Wykrywanie zagrożeń uruchamia wiele zestawów algorytmów, które wykrywają potencjalne luki w zabezpieczeniach i ataki iniekcji SQL, a także nietypowe wzorce dostępu do bazy danych (takie jak dostęp z nietypowej lokalizacji lub nieznanego podmiotu). Funkcjonariusze ochrony lub inni wyznaczeni administratorzy otrzymują powiadomienie e-mail, jeśli w bazie danych zostanie wykryte zagrożenie. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zalecenia dotyczące dalszego badania i łagodzenia zagrożenia. Aby dowiedzieć się, jak włączyć wykrywanie zagrożeń, zobacz: [Włączanie wykrywania zagrożeń](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak ogólnie chronić swoje dane w bazie danych SQL

Szyfrowanie zapewnia silny mechanizm ochrony i zabezpieczania poufnych danych przed intruzami. Zaszyfrowane dane nie są używane dla intruza bez klucza deszyfrującego. W związku z tym dodaje dodatkową warstwę ochrony na wierzchu istniejących warstw zabezpieczeń wbudowanych w bazie danych SQL. Istnieją dwa aspekty ochrony danych w bazie danych SQL:

- Twoje dane, które znajdują się w spoczynku w plikach danych i dzienników
- Twoje dane, które są w locie

W bazie danych SQL domyślnie dane przechowywane w plikach danych i plików dziennika w podsystemie magazynu są całkowicie i zawsze szyfrowane za pomocą [przezroczystego szyfrowania danych [TDE].](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) Kopie zapasowe są również szyfrowane. Z TDE nie są wymagane żadne zmiany po stronie aplikacji, która uzyskuje dostęp do tych danych. Szyfrowanie i odszyfrowywanie odbywa się w sposób przejrzysty; stąd nazwa.
W celu ochrony poufnych danych podczas lotu i w spoczynku baza danych SQL database udostępnia funkcję o nazwie [Zawsze szyfrowane (AE).](/sql/relational-databases/security/encryption/always-encrypted-database-engine) AE jest formą szyfrowania po stronie klienta, który szyfruje poufne kolumny w bazie danych (więc są one w szyfru do administratorów bazy danych i nieautoryzowanych użytkowników). Serwer odbiera zaszyfrowane dane na początek. Klucz zawsze zaszyfrowany jest również przechowywany po stronie klienta, więc tylko autoryzowani klienci mogą odszyfrować poufne kolumny. Administratorzy serwerów i danych nie widzą poufnych danych, ponieważ klucze szyfrowania są przechowywane na kliencie. AE szyfruje poufne kolumny w tabeli od końca do końca, od nieautoryzowanych klientów do dysku fizycznego. AE obsługuje porównania równości dzisiaj, więc administratorzy bazy danych można kontynuować kwerendy zaszyfrowanych kolumn w ramach ich poleceń SQL. Zawsze szyfrowane mogą być używane z różnymi opcjami magazynu kluczy, takimi jak [Usługa Azure Key Vault,](sql-database-always-encrypted-azure-key-vault.md)magazyn certyfikatów systemu Windows i lokalne moduły zabezpieczeń sprzętu.

|**Właściwości**|**Zawsze szyfrowane**|**Niewidoczne szyfrowanie danych**|
|---|---|---|
|**Zakres szyfrowania**|End-to-end|Dane w spoczynku|
|**Serwer bazy danych może uzyskiwać dostęp do poufnych danych**|Nie|Tak, ponieważ szyfrowanie jest dla danych w spoczynku|
|**Dozwolone operacje T-SQL**|Porównanie równości|Dostępna jest cała powierzchnia T-SQL|
|**Zmiany w aplikacji wymagane do korzystania z tej funkcji**|Minimalny|Bardzo minimalne|
|**Szczegółowość szyfrowania**|Poziom kolumny|Poziom bazy danych|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak mogę ograniczyć dostęp do poufnych danych w mojej bazie danych

Każda aplikacja ma pewien bit poufnych danych w bazie danych, które muszą być chronione przed widoczne dla wszystkich. Niektórzy pracownicy w organizacji muszą wyświetlać te dane, jednak inni nie powinni mieć możliwości wyświetlania tych danych. Jednym z przykładów są płace pracowników. Menedżer musiałby mieć dostęp do informacji o wynagrodzeniach do swoich bezpośrednich raportów, jednak poszczególni członkowie zespołu nie powinni mieć dostępu do informacji o wynagrodzeniach swoich rówieśników. Innym scenariuszem są deweloperzy danych, którzy mogą wchodzić w interakcje z poufnymi danymi na etapach osiągnięć programisty lub testowania, na przykład sieci SSN klientów. Te informacje ponownie nie muszą być narażone na dewelopera. W takich przypadkach dane poufne muszą być maskowane lub w ogóle nie mogą być narażone. Baza danych SQL oferuje dwa takie podejścia, aby zapobiec nieautoryzowanym użytkownikom jest w stanie wyświetlić poufne dane:

[Dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md) to funkcja maskowania danych, która umożliwia ograniczenie ekspozycji poufnych danych przez maskowanie ich użytkownikom nieuprzywilejowanym w warstwie aplikacji. Definiujesz regułę maskowania, która może utworzyć wzorzec maskowania (na przykład, aby wyświetlić tylko cztery ostatnie cyfry krajowego numeru SSN identyfikatora XXX-XX-0000 i oznaczyć większość z nich jako Xs) i określić, którzy użytkownicy mają zostać wykluczeni z reguły maskowania. Maskowanie odbywa się w locie i istnieją różne funkcje maskowania dostępne dla różnych kategorii danych. Dynamiczne maskowanie danych umożliwia automatyczne wykrywanie poufnych danych w bazie danych i stosowanie do niej maskowania.

[Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) umożliwiają kontrolowanie dostępu na poziomie wiersza. Co oznacza, że niektóre wiersze w tabeli bazy danych na podstawie użytkownika wykonującego kwerendę (członkostwo w grupie lub kontekst wykonywania) są ukryte. Ograniczenie dostępu odbywa się w warstwie bazy danych, a nie w warstwie aplikacji, aby uprościć logikę aplikacji. Można rozpocząć od utworzenia predykatu filtru, odfiltrowywania wierszy, które nie są widoczne i zasad zabezpieczeń obok definiowania, kto ma dostęp do tych wierszy. Na koniec użytkownik końcowy uruchamia kwerendę i, w zależności od uprawnień użytkownika, albo wyświetla te wiersze z ograniczeniami, albo w ogóle nie może ich zobaczyć.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak zarządzać kluczami szyfrowania w chmurze

Dostępne są opcje zarządzania kluczami zarówno dla zawsze zaszyfrowane (szyfrowanie po stronie klienta) i transparentne szyfrowanie danych (szyfrowanie w spoczynku). Zaleca się regularne obracanie kluczy szyfrowania. Częstotliwość obrotu powinna być zgodna zarówno z wewnętrznymi przepisami organizacyjnymi, jak i wymaganiami dotyczącymi zgodności.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

W TDE istnieje dwukluczowa hierarchia — dane w każdej bazie danych użytkownika są szyfrowane przez symetryczny klucz szyfrowania bazy danych AES-256 (DEK), który z kolei jest szyfrowany przez unikatowy dla serwera asymetryczny klucz główny RSA 2048. Kluczem głównym można zarządzać:

- Automatycznie przez platformę - baza danych SQL.
- Lub przy użyciu [usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako magazynu kluczy.

Domyślnie klucz główny szyfrowania danych przezroczystych jest zarządzany przez usługę bazy danych SQL dla wygody. Jeśli twoja organizacja chce kontrolować klucz główny, istnieje opcja użycia usługi Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) jako magazynu kluczy. Korzystając z usługi Azure Key Vault, organizacja przejmuje kontrolę nad formantami inicjowania obsługi administracyjnej kluczy, rotacji i uprawnień. [Obrót lub przełączanie typu klucza głównego TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) jest szybkie, ponieważ tylko ponownie szyfruje DEK. W przypadku organizacji z rozdzieleniem ról między zabezpieczeniami a zarządzaniem danymi administrator zabezpieczeń może udostępnić materiał klucza głównego TDE w usłudze Azure Key Vault i udostępnić administratorowi bazy danych identyfikator klucza usługi Azure Key Vault. szyfrowania w spoczynku na serwerze. Usługa Key Vault została zaprojektowana w taki sposób, że firma Microsoft nie widzi ani nie wyodrębnia żadnych kluczy szyfrowania. Otrzymasz również scentralizowane zarządzanie kluczami dla swojej organizacji.

#### <a name="always-encrypted"></a>Zawsze szyfrowane

Istnieje również [hierarchia dwóch kluczy](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) w zawsze zaszyfrowane - kolumna poufnych danych jest szyfrowana przez klucz szyfrowania AES 256-kolumnowy (CEK), który z kolei jest szyfrowany przez klucz główny kolumny (CMK). Sterowniki klienta przewidziane dla zawsze zaszyfrowane nie mają ograniczeń co do długości cmks. Zaszyfrowana wartość CEK jest przechowywana w bazie danych, a cmk jest przechowywany w magazynie kluczy zaufanych, takich jak Windows Certificate Store, Azure Key Vault lub sprzętowy moduł zabezpieczeń.

- Zarówno [CEK,](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) jak i CMK można obracać.
- Obrót CEK jest rozmiar operacji danych i może być czasochłonne w zależności od rozmiaru tabel zawierających zaszyfrowane kolumny. W związku z tym rozsądne jest odpowiednie planowanie rotacji CEK.
- Rotacja cmk, jednak nie koliduje z wydajnością bazy danych i można zrobić z oddzielnych ról.

Na poniższym diagramie przedstawiono opcje magazynu kluczy dla kluczy głównych kolumn w obszarze Zawsze zaszyfrowane

![Zawsze szyfrowani dostawcy sklepu CMK](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak zoptymalizować i zabezpieczyć ruch między moją organizacją a bazą danych SQL

Ruch sieciowy między organizacją a bazą danych SQL zazwyczaj jest kierowany przez sieć publiczną. Jeśli jednak zdecydujesz się zoptymalizować tę ścieżkę i uczynić ją bezpieczniejszą, możesz zajrzeć do trasy ekspresowej. Trasa ekspresowa zasadniczo umożliwia rozszerzenie sieci firmowej na platformę Azure za pośrednictwem połączenia prywatnego. W ten sposób nie przechodzisz przez publiczny Internet. Można również uzyskać wyższe bezpieczeństwo, niezawodność i routing optymalizacji, co przekłada się na niższe opóźnienia sieci i znacznie szybsze prędkości niż zwykle doświadczenie będzie przez publiczny Internet. Jeśli planujesz przeniesienie znacznej części danych między organizacją a platformą Azure, użycie trasy ekspresowej może przynieść korzyści ekonomiczne. Można wybrać jeden z trzech różnych modeli łączności dla połączenia z organizacji do platformy Azure:

- [Kolokcja programu Cloud Exchange](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Każdy dowolny](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Express Route umożliwia również rozerwanie do 2x limit przepustowości zakupionej bez dodatkowych opłat. Możliwe jest również skonfigurowanie łączności międzyregionowej przy użyciu trasy ekspresowej. Aby wyświetlić listę dostawców łączności ER, zobacz: [Ekspresowi partnerzy trasy i lokalizacje komunikacji równorzędnej](../expressroute/expressroute-locations.md). W poniższych artykułach opisano bardziej szczegółowo trasę ekspresową:

- [Wprowadzenie na trasie ekspresowej](../expressroute/expressroute-introduction.md)
- [Wymagania wstępne](../expressroute/expressroute-prerequisites.md)
- [Przepływy](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Czy baza danych SQL jest zgodna z wszelkimi wymogami regulacyjnymi i w jaki sposób pomaga to w zgodności z przepisami mojej organizacji

Baza danych SQL jest zgodna z szeregiem zgodności regulacyjnych. Aby wyświetlić najnowszy zestaw zgodności, które zostały spełnione przez bazę danych SQL, odwiedź [Centrum zaufania firmy Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i przejdź do szczegółów dotyczących zgodności, które są ważne dla twojej organizacji, aby sprawdzić, czy baza danych SQL jest uwzględniona w zgodnych usługach platformy Azure. Należy pamiętać, że chociaż baza danych SQL może być certyfikowana jako usługa zgodna, pomaga w zgodności z usługą organizacji, ale nie gwarantuje jej automatycznie.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentne monitorowanie i konserwacja baz danych po migracji

Po migracji bazy danych do bazy danych SQL, będziesz chciał monitorować bazę danych (na przykład, sprawdź, jak jest używane zasobów lub dbcc sprawdza) i wykonać regularną konserwację (na przykład odbudować lub reorganizować indeksy, statystyki itp.). Na szczęście baza danych SQL jest inteligentna w tym sensie, że używa historycznych trendów i zarejestrowanych metryk i statystyk, aby aktywnie monitorować i utrzymywać bazę danych, dzięki czemu aplikacja działa optymalnie zawsze. W niektórych przypadkach usługa Azure SQL DB może automatycznie wykonywać zadania konserwacji w zależności od konfiguracji konfiguracji. Istnieją trzy aspekty do monitorowania bazy danych w bazie danych SQL:

- Monitorowanie wydajności i optymalizacja.
- Optymalizacja zabezpieczeń.
- Optymalizacja kosztów.

### <a name="performance-monitoring-and-optimization"></a>Monitorowanie i optymalizacja wydajności

Dzięki usługowiu Query Performance Insights można uzyskać dostosowane zalecenia dotyczące obciążenia bazy danych, dzięki czemu aplikacje mogą działać na optymalnym poziomie — zawsze. Można również skonfigurować go tak, aby te zalecenia były stosowane automatycznie i nie trzeba przejmować się wykonywaniem zadań konserwacji. Za pomocą klasyfikatora indeksu można automatycznie implementować zalecenia dotyczące indeksu na podstawie obciążenia — nazywa się to automatycznym dostrajaniem. Zalecenia ewoluują wraz ze zmianami obciążenia aplikacji, aby zapewnić najbardziej odpowiednie sugestie. Możesz również uzyskać możliwość ręcznego zapoznania się z tymi zaleceniami i zastosowania ich według własnego uznania.  

### <a name="security-optimization"></a>Optymalizacja zabezpieczeń

Baza danych SQL zawiera zalecenia dotyczące zabezpieczeń, które umożliwiają zasłanianie działania, aby ułatwić zabezpieczanie danych i wykrywanie zagrożeń w celu identyfikowania i badania podejrzanych działań bazy danych, które mogą stanowić potencjalny wątek do bazy danych. [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to usługa skanowania i raportowania baz danych, która umożliwia monitorowanie stanu zabezpieczeń baz danych na dużą skalę oraz identyfikowanie zagrożeń bezpieczeństwa i odejście od zdefiniowanej przez użytkownika linii bazowej zabezpieczeń. Po każdym skanowaniu udostępniana jest dostosowana lista czynności i skryptów korygowania, a także raport z oceny, który może służyć do spełnienia wymagań dotyczących zgodności.

Za pomocą usługi Azure Security Center można zidentyfikować zalecenia dotyczące zabezpieczeń we wszystkich dziedzinach i zastosować je za pomocą jednego kliknięcia.

### <a name="cost-optimization"></a>Optymalizacja kosztów

Platforma SQL platformy Azure analizuje historię wykorzystania w bazach danych na serwerze, aby ocenić i zalecić opcje optymalizacji kosztów dla Ciebie. Analiza ta trwa zwykle dwa tygodnie do analizy i budowania zasuwają zalecenia. Elastyczna pula jest jedną z takich opcji. Zalecenie pojawia się na portalu jako baner:

![zalecenia dotyczące elastycznego basenu](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Tę analizę można również wyświetlić w sekcji "Doradca":

![elastyczne rekomendacje basen-doradca](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak monitorować wydajność i wykorzystanie zasobów w bazie danych SQL

W bazie danych SQL można wykorzystać inteligentne spostrzeżenia platformy do monitorowania wydajności i dostroić odpowiednio. Wydajność i wykorzystanie zasobów można monitorować w bazie danych SQL przy użyciu następujących metod:

#### <a name="azure-portal"></a>Portal Azure

Portal Azure zawiera wykorzystanie bazy danych, wybierając bazę danych i klikając wykres w okienku Przegląd. Można zmodyfikować wykres, aby wyświetlić wiele metryk, w tym procent procesora CPU, procent jednostki DTU, procent we/wy danych, procent sesji i procent rozmiaru bazy danych.

![Wykres monitorowania](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Wykres monitorowania2](./media/sql-database-manage-after-migration/chart.png)

Z tego wykresu można również skonfigurować alerty według zasobów. Te alerty umożliwiają reagowanie na warunki zasobów za pomocą wiadomości e-mail, zapisywanie do punktu końcowego HTTPS/HTTP lub wykonywanie akcji. Aby uzyskać więcej informacji, zobacz [Tworzenie alertów](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Dynamiczne widoki zarządzania

Można zbadać widok zarządzania dynamicznego [sys.dm_db_resource_stats,](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) aby zwrócić historię statystyk zużycia zasobów z ostatniej godziny i widok wykazu systemu [sys.resource_stats,](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) aby zwrócić historię z ostatnich 14 dni.

#### <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Query Performance Insight](sql-database-query-performance.md) umożliwia wyświetlenie historii najlepszych zapytań zużywających zasoby i długotrwałych zapytań dla określonej bazy danych. Można szybko zidentyfikować TOP zapytania według wykorzystania zasobów, czas trwania i częstotliwość wykonywania. Można śledzić kwerendy i wykrywać regresję. Ta funkcja wymaga, aby [magazyn zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) był włączony i aktywny dla bazy danych.

![Szczegółowe informacje o wydajności zapytań](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Usługa Azure SQL Analytics (wersja zapoznawcza) w dziennikach usługi Azure Monitor

[Dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-sql.md) umożliwiają zbieranie i wizualizowanie kluczowych metryk wydajności bazy danych SQL platformy Azure, obsługujących do 150 000 baz danych SQL i pul elastycznych SQL na obszar roboczy. Można go używać do monitorowania i odbierania powiadomień. Można monitorować bazę danych SQL i metryki puli elastycznej w wielu subskrypcjach platformy Azure i pulach elastycznych i może służyć do identyfikowania problemów w każdej warstwie stosu aplikacji.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Widzę problemy z wydajnością: Czym różni się metodologia rozwiązywania problemów z bazą danych SQL od programu SQL Server

Duża część technik rozwiązywania problemów, które można użyć do diagnozowania problemów z wydajnością kwerend i bazy danych pozostają takie same. Przecież ten sam aparat PROGRAMU SQL Server zasila chmurę. Jednak platforma — usługa Azure SQL DB została wbudowana w "analizy". Może jeszcze łatwiej rozwiązywać i diagnozować problemy z wydajnością. Może również wykonywać niektóre z tych działań naprawczych w Twoim imieniu, a w niektórych przypadkach proaktywnie je naprawiać - automatycznie.

Podejście do rozwiązywania problemów z wydajnością może znacznie przynieść korzyści przy użyciu inteligentnych funkcji, takich jak [Query Performance Insight (QPI)](sql-database-query-performance.md) i [Database Advisor](sql-database-advisor.md) w połączeniu, a więc różnica w metodologii różni się pod tym względem — nie trzeba już wykonywać ręcznej pracy szlifowania podstawowych szczegółów, które mogą pomóc w rozwiązaniu problemu. Platforma wykonuje ciężką pracę za Ciebie. Jednym z przykładów jest QPI. Za pomocą interfejsu QPI można drążyć aż do poziomu zapytania i przyjrzeć się trendom historycznym i dowiedzieć się, kiedy dokładnie kwerenda cofnęła się. Doradca bazy danych zawiera zalecenia dotyczące rzeczy, które mogą pomóc poprawić ogólną wydajność w ogóle, takich jak - brak indeksów, upuszczając indeksy, parametryzowanie zapytań itp.

W rozwiązywaniu problemów z wydajnością ważne jest, aby określić, czy jest to tylko aplikacja lub baza danych, która ją wspiera, co ma wpływ na wydajność aplikacji. Często problem z wydajnością leży w warstwie aplikacji. Może to być architektura lub wzorzec dostępu do danych. Na przykład należy wziąć pod uwagę, że masz chatty aplikacji, która jest wrażliwa na opóźnienie sieci. W takim przypadku aplikacja cierpi, ponieważ nie byłoby wiele krótkich żądań będzie tam iz powrotem ("chatty") między aplikacją a serwerem i w zatłoczonej sieci, te objazdy sumują się szybko. Aby zwiększyć wydajność w tym przypadku, można użyć [kwerend wsadowych](sql-database-performance-guidance.md#batch-queries). Korzystanie z partii pomaga ogromnie, ponieważ teraz twoje żądania są przetwarzane w partii; w ten sposób, pomaga zmniejszyć opóźnienie w obie strony i poprawić wydajność aplikacji.

Ponadto jeśli zauważysz spadek ogólnej wydajności bazy danych, można monitorować [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats dynamiczne](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) widoki zarządzania w celu zrozumienia użycia procesora CPU, we/wy i pamięci. Wydajność może mieć wpływ, ponieważ baza danych jest pozbawiony zasobów. Może to być konieczne, aby zmienić rozmiar obliczeń i/lub warstwy usług w oparciu o rosnące i kurczące się wymagania obciążenia.

Aby uzyskać kompleksowy zestaw zaleceń dotyczących problemów z wydajnością dostrajania, zobacz: [Dostrajanie bazy danych](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Jak upewnić się, że używam odpowiedniej warstwy usług i rozmiaru obliczeń

Baza danych SQL oferuje różne warstwy usług Basic, Standard i Premium. Każda warstwa usług otrzymasz gwarantowaną przewidywalną wydajność powiązaną z tą warstwą usług. W zależności od obciążenia może być seria aktywności, gdzie wykorzystanie zasobów może trafić pułap bieżącego rozmiaru obliczeń, w którym się znajdujesz. W takich przypadkach warto najpierw rozpocząć od oceny, czy dostrajanie może pomóc (na przykład dodanie lub zmiana indeksu itp.). Jeśli nadal występują problemy z limitem, rozważ przejście do wyższej warstwy usług lub rozmiaru obliczeń.

|**Warstwa usług**|**Typowe scenariusze przypadków użycia**|
|---|---|
|**Podstawowa (Basic)**|Aplikacje z kilku użytkowników i bazy danych, która nie ma wysokiej współbieżności, skala i wymagania dotyczące wydajności. |
|**Standardowa**|Aplikacje o znacznej współbieżności, skali i wydajności w połączeniu z niskimi i średnimi wymaganiami we/wy. |
|**Premium**|Aplikacje z dużą ilością równoczesnych użytkowników, wysokim procesorem/pamięcią i wysokimi wymaganiami we/wy. Aplikacje o wysokiej współbieżności, wysokiej przepływności i opóźnieniach mogą korzystać z poziomu premium. |
|||

Aby upewnić się, że masz odpowiedni rozmiar obliczeń, możesz monitorować zużycie zasobów zapytania i bazy danych za pomocą jednego z wyżej wymienionych sposobów w "Jak monitorować wydajność i wykorzystanie zasobów w bazie danych SQL". Jeśli okaże się, że zapytania/bazy danych są stale działa gorąco na procesorze CPU/pamięci itp., można rozważyć skalowanie do większego rozmiaru obliczeń. Podobnie, jeśli zauważysz, że nawet w godzinach szczytu, nie wydaje się korzystać z zasobów tyle; należy rozważyć skalowanie w dół od bieżącego rozmiaru obliczeń.

Jeśli masz wzorzec aplikacji SaaS lub scenariusz konsolidacji bazy danych, należy rozważyć użycie puli elastycznej do optymalizacji kosztów. Pula elastyczna to świetny sposób na konsolidację bazy danych i optymalizację kosztów. Aby dowiedzieć się więcej o zarządzaniu wieloma bazami danych przy użyciu puli elastycznej, zobacz: [Zarządzanie pulami i bazami danych](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak często muszę uruchamiać sprawdzanie integralności bazy danych dla mojej bazy danych

Baza danych SQL używa niektórych inteligentnych technik, które umożliwiają obsługę niektórych klas uszkodzenia danych automatycznie i bez utraty danych. Techniki te są wbudowane w usługę i są wykorzystywane przez usługę, gdy zajdzie taka potrzeba. Regularnie kopie zapasowe bazy danych w całej usłudze są testowane przez ich przywracanie i uruchamianie bazy danych DBCC CHECKDB na nim. Jeśli występują problemy, baza danych SQL proaktywnie rozwiązuje je. [Automatyczna naprawa stron](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) jest wykorzystywana do naprawiania stron, które są uszkodzone lub mają problemy z integralnością danych. Strony bazy danych są zawsze weryfikowane przy domyślnym ustawieniu SUMA KONTROLNA, która weryfikuje integralność strony. Baza danych SQL aktywnie monitoruje i przegląda integralność danych bazy danych i, jeśli wystąpią problemy, rozwiązuje je z najwyższym priorytetem. Oprócz nich możesz opcjonalnie uruchomić własne kontrole integralności według własnego woli.  Aby uzyskać więcej informacji, zobacz [Integralność danych w bazie danych SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Przenoszenie danych po migracji

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Jak eksportować i importować dane jako pliki BACPAC z bazy danych SQL

- **Eksport:** Możesz wyeksportować bazę danych SQL platformy Azure jako plik BACPAC z witryny Azure portal

   ![eksport bazy danych](./media/sql-database-export/database-export1.png)

- **Import:** Można również importować dane jako plik BACPAC do bazy danych za pomocą witryny Azure portal.

   ![import bazy danych](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Jak zsynchronizować dane między bazą danych SQL a programem SQL Server

Masz kilka sposobów, aby to osiągnąć:

- **[Synchronizacja danych](sql-database-sync-data.md)** — ta funkcja ułatwia synchronizowanie danych dwukierunkowo między wieloma lokalnymi bazami danych programu SQL Server i bazą danych SQL Database. Aby synchronizować z lokalnymi bazami danych programu SQL Server, należy zainstalować i skonfigurować agenta synchronizacji na komputerze lokalnym i otworzyć wychodzący port TCP 1433.
- **[Replikacja transakcji](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** — dzięki replikacji transakcji można synchronizować dane z lokalnego do usługi Azure SQL DB z lokalnym wydawcą i usługą Azure SQL DB będącą subskrybentem. Na razie tylko ta konfiguracja jest obsługiwana. Aby uzyskać więcej informacji na temat migracji danych z lokalnego programu Azure SQL przy minimalnym przestoju, zobacz: [Użyj replikacji transakcji](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [bazie danych SQL](sql-database-technical-overview.md).
