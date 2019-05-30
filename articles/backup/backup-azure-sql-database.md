---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure | Microsoft Docs
description: W tym samouczku opisano sposób tworzenia kopii zapasowych programu SQL Server na platformie Azure. W artykule objaśniono również proces odzyskiwania programu SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: raynew
ms.openlocfilehash: 2a6319565aa05f34ce31a14c5fc57e591248f4ee
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399689"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informacje o kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure

Bazy danych programu SQL Server to krytyczne obciążenia, które wymagają niskiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową baz danych programu SQL Server uruchomiony na maszynach wirtualnych platformy Azure przy użyciu [kopia zapasowa Azure](backup-overview.md).

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

To rozwiązanie korzysta z natywnymi interfejsami API SQL do wykonania kopii zapasowych baz danych SQL.

* Po określeniu maszynę Wirtualną SQL Server, którą chcesz chronić i wykonywania zapytań względem baz danych w niej, usługa Azure Backup zostanie zainstalowany rozszerzenia do tworzenia kopii zapasowych obciążeń na maszynie Wirtualnej według nazwy `AzureBackupWindowsWorkload`  rozszerzenia.
* To rozszerzenie składa się z koordynatorem i wtyczki programu SQL. Koordynator jest odpowiedzialna za wyzwolenie przepływów pracy na potrzeby różne operacje, takie jak konfigurowanie kopii zapasowej i przywracania kopii zapasowych, dodatek jest odpowiedzialny za przepływu danych rzeczywistych.
* Aby można było odnaleźć baz danych na tej maszynie Wirtualnej, usługa Azure Backup tworzy konto `NT SERVICE\AzureWLBackupPluginSvc`. To konto służy do tworzenia kopii zapasowych i przywracania i wymaga uprawnień administratora systemu SQL. Usługa Azure Backup korzysta `NT AUTHORITY\SYSTEM` konta bazy danych odnajdywania/zapytanie, więc to konto musi być publiczny logowania SQL. Jeśli nie utworzono maszynę Wirtualną programu SQL Server w witrynie Azure Marketplace, być może otrzymasz komunikat o błędzie **UserErrorSQLNoSysadminMembership**. W takim przypadku [wykonaj te instrukcje](backup-azure-sql-database.md).
* Gdy wyzwalacz można skonfigurować ochrony dla wybranych baz danych, usługi kopii zapasowej konfiguruje koordynatora za pomocą harmonogramów tworzenia kopii zapasowych i inne szczegóły zasad, których rozszerzenie zapisuje w pamięci podręcznej lokalnie na maszynie Wirtualnej 
* W zaplanowanym czasie koordynatora komunikuje się za pomocą wtyczki, a następnie uruchamia, przesyłanie strumieniowe danych kopii zapasowej z programu SQL server przy użyciu infrastruktury VDI.  
* Wtyczka wysyła dane bezpośrednio do magazynu usługi recovery services, eliminując potrzebę lokalizacji tymczasowej. Dane są szyfrowane i przechowywane w usłudze Azure Backup w ramach kont magazynu.
* Po zakończeniu transferu danych, koordynator potwierdza zatwierdzenia za pomocą usługi kopii zapasowej.

  ![Architektura kopii zapasowej SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem należy sprawdzić poniżej:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) w witrynie Marketplace.
2. Przegląd [funkcji brany pod uwagę](#feature-consideration-and-limitations) i [scenariusza pomocy technicznej](#scenario-support).
3. [Przejrzyj często zadawane pytania](faq-backup-sql-server.md) dotyczące tego scenariusza.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia (ASE), Australia Wschodnia (AE) <br> Brazylia Południowa (BRS)<br> Kanada Środkowa (CNC), Kanada Wschodnia (CE)<br> Azja południowo-wschodnia (SEA), Azja Wschodnia (EA) <br> Wschodnie stany USA (EUS), wschodnie stany USA 2 (EUS2) zachodnie środkowe stany USA (WCUS), zachodnie stany USA (WUS); Zachodnie stany USA 2 (WUS 2) Północna środkowe stany USA (NCUS) środkowe stany USA (CUS) południowo-środkowe stany USA (SCUS) <br> Indie środkowe (INC), Indie Południowe (INS) <br> Japonia, część wschodnia (JPE), Japonia, część Zachodnia (JPW) <br> Korea Środkowa (KRC), Korea Południowa (KRS) <br> Europa Północna (NE), Europa Zachodnia <br> Południowe Zjednoczone Królestwo (UKS), Zjednoczone Królestwo w zachodnie (UKW)
**Obsługiwane systemy operacyjne** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Obsługiwane wersje programu .NET** | .NET framework 4.5.2 i nowszej zainstalowany na maszynie Wirtualnej

## <a name="feature-consideration-and-limitations"></a>Funkcja uwag i ograniczeń

- Kopia zapasowa programu SQL Server można skonfigurować w witrynie Azure portal lub **PowerShell**. Interfejs wiersza polecenia nie jest obsługiwana.
- To rozwiązanie jest obsługiwane w obu rodzajów z [wdrożeń](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) — maszyny wirtualne platformy Azure Resource Manager i klasycznych maszyn wirtualnych.
- Maszynę Wirtualną z uruchomionym programem SQL Server wymaga łączności z Internetem na dostęp do platformy Azure z publicznymi adresami IP.
- Program SQL Server **wystąpienia klastra trybu Failover (FCI)** i SQL Server-zawsze w wystąpieniu klastra trybu Failover nie są obsługiwane.
- Tworzenie kopii zapasowych i operacje przywracania dla migawki bazy danych i dublowania bazy danych nie są obsługiwane.
- Przy użyciu więcej niż jednego rozwiązania tworzenia kopii zapasowych do utworzenia kopii zapasowej z autonomicznego programu SQL Server lub wystąpienia programu SQL zawsze włączona grupa dostępności może prowadzić do niepowodzenia wykonywania kopii zapasowej; Unikaj umieszczania w ten sposób.
- Tworzenie kopii zapasowej dwa węzły grupy dostępności za pomocą tych samych lub różnych rozwiązań, również może prowadzić do niepowodzenia wykonywania kopii zapasowej.
- Usługa Azure Backup obsługuje tylko pełne i typy tylko do kopiowania pełnej kopii zapasowej **tylko do odczytu** baz danych
- Nie można chronić baz danych z dużą liczbą plików. Maksymalna liczba plików, który jest obsługiwany jest **~ 1000**.  
- Można utworzyć kopię do **OK. 2000** baz danych programu SQL Server w magazynie. Można utworzyć wiele magazynów, w przypadku, gdy masz większą liczbę baz danych.
- Kopie zapasowe można skonfigurować maksymalnie **50** Przejdź baz danych w jednym; temu ograniczeniu można zoptymalizować obciążeń kopii zapasowych.
- Firma Microsoft obsługuje bazy danych do **2TB** rozmiar; o rozmiarze większym niż ta, wydajność może pojawiania się nowych problemów.
- Aby poznać dotyczące liczby baz danych mogą być chronione na serwerze, należy wziąć pod uwagę czynniki, takie jak przepustowość, rozmiar maszyny Wirtualnej, częstotliwość wykonywania kopii zapasowych, rozmiar bazy danych itp. Pracujemy nad planner, które będą pomocne podczas obliczania te liczby na zostanie właścicielami. Firma Microsoft będzie publikować dane w jej wkrótce.
- W przypadku grup dostępności kopie zapasowe są pobierane z różnych węzłach, na podstawie kilku czynników. Poniżej podsumowano zachowanie tworzenia kopii zapasowej dla grupy dostępności.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Tworzenie kopii zapasowej zachowanie w przypadku zawsze włączonych grup dostępności

Zaleca się, że kopia zapasowa jest skonfigurowana na tylko jeden węzeł grupy dostępności. Kopia zapasowa zawsze powinna być skonfigurowana w tym samym regionie co węzła głównego. Innymi słowy należy zawsze węzła podstawowego znajdować się w regionie, w której chcesz skonfigurować kopii zapasowej. Jeśli wszystkie węzły grupy dostępności w tym samym regionie, w którym skonfigurowano kopię zapasową, nie ma jakiekolwiek wątpliwości.

**Dla grupy dostępności między regionami**
- Niezależnie od preferencji tworzenia kopii zapasowej kopii zapasowych nie nastąpi z węzłów, które nie znajdują się w tym samym regionie, w której skonfigurowano kopii zapasowej. Jest to spowodowane między regionami kopie zapasowe nie są obsługiwane. Jeśli masz tylko 2 węzły i pomocniczy węzeł znajduje się w innym regionie; w tym przypadku kopie zapasowe będą nadal mieć miejsce z węzła podstawowego (chyba że preferencje kopii zapasowej "tylko pomocnicza").
- W przypadku trybu failover do regionu innego niż ten, w którym skonfigurowano kopii zapasowej, kopii zapasowych zakończy się niepowodzeniem na węzłach w regionie trybie failed-over.

W zależności od preferencji kopii zapasowych i typy kopii zapasowych (pełnej/różnicowej/log/tylko do kopiowania pełną) kopie zapasowe są pobierane z określonego węzła (podstawowy/dodatkowy).

- **Preferencję tworzenia kopii zapasowych: Podstawowy**

**Typ kopii zapasowej** | **Node**
    --- | ---
    Pełne | Podstawowy
    Różnicowa | Podstawowy
    Log |  Podstawowy
    Pełne copy-Only |  Podstawowy

- **Preferencję tworzenia kopii zapasowych: Tylko pomocnicza**

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowa | Podstawowy
Log |  Pomocniczy
Pełne copy-Only |  Pomocniczy

- **Preferencję tworzenia kopii zapasowych: Pomocniczy**

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowa | Podstawowy
Log |  Pomocniczy
Pełne copy-Only |  Pomocniczy

- **Brak preferencji kopii zapasowej**

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicowa | Podstawowy
Log |  Pomocniczy
Pełne copy-Only |  Pomocniczy

## <a name="fix-sql-sysadmin-permissions"></a>Naprawianie uprawnień administratora systemu SQL

  Jeśli trzeba usunąć uprawnienia z powodu **UserErrorSQLNoSysadminMembership** błąd, wykonaj następujące czynności:

  1. Zaloguj się do programu SQL Server Management Studio (SSMS), używając konta z uprawnieniami administratora systemu SQL Server. Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelnianie systemu Windows powinno działać.
  2. W programie SQL Server otwórz folder **Security/Logins**.

      ![Otwieranie folderu Security/Logins w celu wyświetlenia kont](./media/backup-azure-sql-database/security-login-list.png)

  3. Kliknij prawym przyciskiem myszy folder **Logins**, a następnie wybierz pozycję **Nowa nazwa logowania**. W oknie **Nazwa logowania — nowa** wybierz pozycję **Wyszukaj**.

      ![Wybieranie pozycji Wyszukaj w oknie dialogowym Nazwa logowania — nowa](./media/backup-azure-sql-database/new-login-search.png)

  4. Konto usługi wirtualnej systemu Windows **NT SERVICE\AzureWLBackupPluginSvc** zostało utworzone podczas rejestracji maszyny wirtualnej i fazy odnajdywania SQL. Wprowadź nazwę konta, jak pokazano w polu **Wprowadź nazwę obiektu do wybrania**. Wybierz pozycję **Sprawdź nazwy** w celu rozpoznania nazwy. Kliknij przycisk **OK**.

      ![Wybieranie pozycji Sprawdź nazwy w celu rozpoznania nieznanej nazwy](./media/backup-azure-sql-database/check-name.png)

  5. W obszarze **Role serwera** sprawdź, czy wybrano rolę **sysadmin**. Kliknij przycisk **OK**. Wymagane uprawnienia powinny teraz istnieć.

      ![Sprawdzanie, czy wybrano rolę sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Skojarz bazę danych z magazynem usługi Recovery Services. W witrynie Azure Portal na liście **Serwery chronione** kliknij prawym przyciskiem myszy serwer w stanie błędu i wybierz pozycję **Ponownie znajdź bazy danych**.

      ![Sprawdzanie, czy serwer ma odpowiednie uprawnienia](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Sprawdź postęp w obszarze **Powiadomienia**. Po znalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

      ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Jeśli program SQL Server ma wiele wystąpień programu SQL Server zainstalowana, a następnie należy dodać uprawnienia administratora systemu dla **NT Service\AzureWLBackupPluginSvc** konta do wszystkich wystąpień programu SQL.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o](backup-sql-server-database-azure-vms.md) tworzenia kopii zapasowych baz danych programu SQL Server.
- [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
- [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.
