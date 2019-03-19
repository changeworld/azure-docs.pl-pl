---
title: O programie SQL Server maszyny Wirtualnej platformy Azure za pomocą usługi Azure Backup | Dokumentacja firmy Microsoft
description: Więcej informacji na temat baz danych SQL Server na maszynie Wirtualnej platformy Azure oraz funkcji brany pod uwagę tej funkcji.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sachdevaswati
ms.openlocfilehash: a57b52b3b0cc493fdde60e9bddfb0125ff2ce3e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175968"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Narzędzie Kopia zapasowa programu SQL Server w maszynach wirtualnych platformy Azure — informacje

Bazy danych programu SQL Server to krytyczne obciążenia, które wymagają niskiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Za pomocą usługi [Azure Backup](backup-overview.md) można tworzyć kopie zapasowe baz danych programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

To rozwiązanie korzysta z natywnymi interfejsami API SQL do wykonania kopii zapasowych baz danych SQL.

* Po określeniu maszyny Wirtualnej serwera SQL, który chcesz chronić i wykonywania zapytań względem baz danych w it, usługa Azure Backup będzie Instalowanie rozszerzenia kopii zapasowej obciążeń na maszynie Wirtualnej według nazwy `AzureBackupWindowsWorkload`  rozszerzenia.
* To rozszerzenie składa się z koordynatorem i wtyczki programu SQL. Koordynator jest odpowiedzialna za wyzwolenie przepływów pracy na potrzeby różne operacje, takie jak konfigurowanie kopii zapasowej i przywracania kopii zapasowych, dodatek jest odpowiedzialny za przepływu danych rzeczywistych.
* Aby można było odnaleźć baz danych na tej maszynie Wirtualnej, usługa Azure Backup tworzy konto `NT SERVICE\AzureWLBackupPluginSvc`. To konto służy do tworzenia kopii zapasowych i przywracania i wymaga uprawnień administratora systemu SQL. Usługa Azure Backup korzysta `NT AUTHORITY\SYSTEM` konta bazy danych odnajdywania/zapytanie, więc to konto musi być publiczny logowania SQL. Jeśli nie utworzono maszynę Wirtualną programu SQL Server w witrynie Azure Marketplace, być może otrzymasz komunikat o błędzie **UserErrorSQLNoSysadminMembership**. W takim przypadku [wykonaj te instrukcje](backup-azure-sql-database.md).
* Gdy wyzwalacz można skonfigurować ochrony dla wybranych baz danych, usługi kopii zapasowej konfiguruje koordynatora za pomocą harmonogramów tworzenia kopii zapasowych i inne szczegóły zasad, których rozszerzenie zapisuje w pamięci podręcznej lokalnie na maszynie Wirtualnej 
* W zaplanowanym czasie koordynatora komunikuje się za pomocą wtyczki, a następnie uruchamia, przesyłanie strumieniowe danych kopii zapasowej z programu SQL server przy użyciu infrastruktury VDI.  
* Wtyczka wysyła dane bezpośrednio do magazynu usługi recovery services, eliminując potrzebę lokalizacji tymczasowej. Dane są szyfrowane i przechowywane w usłudze Azure Backup w ramach kont magazynu.
* Po zakończeniu transferu danych, koordynator potwierdza zatwierdzenia za pomocą usługi kopii zapasowej.

  ![Architektura kopii zapasowej SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem sprawdź następujące kwestie:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) w witrynie Marketplace.
2. Przegląd [funkcji brany pod uwagę](#feature-consideration) i [scenariusza pomocy technicznej](#scenario_support).
3. [Przejrzyj często zadawane pytania](faq-backup-sql-server.md) dotyczące tego scenariusza.


## <a name="feature-consideration-and-limitations"></a>Funkcja uwag i ograniczeń

- Maszyna wirtualna z uruchomionym programem SQL Server wymaga łączności z Internetem do uzyskiwania dostępu do publicznych adresów IP platformy Azure.
- Tworzenie kopii zapasowych [rozproszonej grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) grup pełni nie działają.
- Zawsze włączone wystąpienia klastra trybu failover w programie SQL Server nie są obsługiwane w przypadku tworzenia kopii zapasowej.
- Kopia zapasowa programu SQL Server należy skonfigurować w portalu lub za pośrednictwem programu PowerShell.
- Operacje kopii zapasowej i przywracania baz danych duplikatu infrastruktury klasyfikacji plików, migawki baz danych i baz danych nie są obsługiwane.
- Nie można chronić baz danych z dużą liczbą plików. Maksymalna liczba plików to 1000.
- Można utworzyć kopię bazy danych SQL Server do 2000 ~ w magazynie. Jeśli masz ich więcej, utwórz kolejny magazyn.
- Tworzenie kopii zapasowej dla maksymalnie 50 baz danych można skonfigurować w jednego kroku; to ograniczenie pomaga w optymalizacji obciążeń kopii zapasowych.
- Nie można chronić baz danych z więcej niż 1000 plików.
- Rozmiar zalecany bazy danych, aby zapewnić lepszą wydajność jest 2TB.
- Chroni maksymalnie 300 baz danych na serwerze, w przypadku wykonywania kopii zapasowych dziennika skonfigurowane co 15 minut. Liczba baz danych można zwiększyć, jeśli dłuższe interwały częstotliwość wykonywania kopii zapasowych. Firma Microsoft opowie o szczegółowy sposób obliczania to wkrótce.


## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia (ASE), Australia Wschodnia (AE) <br> Brazylia Południowa (BRS)<br> Kanada Środkowa (CNC), Kanada Wschodnia (CE)<br> Azja południowo-wschodnia (SEA), Azja Wschodnia (EA) <br> Wschodnie stany USA (EUS), wschodnie stany USA 2 (EUS2) zachodnie środkowe stany USA (WCUS), zachodnie stany USA (WUS); Zachodnie stany USA 2 (WUS 2) Północna środkowe stany USA (NCUS) środkowe stany USA (CUS) południowo-środkowe stany USA (SCUS) <br> Indie środkowe (INC), Indie Południowe (INS) <br> Japonia, część wschodnia (JPE), Japonia, część Zachodnia (JPW) <br> Korea Środkowa (KRC), Korea Południowa (KRS) <br> Europa Północna (NE), Europa Zachodnia <br> Republika Południowej Afryki Północna (SAN), Republika Południowej Afryki zachodnie (SAW) <br> Środkowe Zjednoczone Emiraty Arabskie (UAC), Zjednoczone Emiraty Arabskie Północna (UAN) <br> Południowe Zjednoczone Królestwo (UKS), Zjednoczone Królestwo w zachodnie (UKW)
**Obsługiwane systemy operacyjne** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2017, SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Obsługiwane wersje programu .NET** | .NET framework 4.5.2 i nowszej zainstalowany na maszynie Wirtualnej



## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o](backup-sql-server-database-azure-vms.md) tworzenia kopii zapasowych baz danych programu SQL Server.
- [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
- [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.
