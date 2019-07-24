---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure | Microsoft Docs
description: W tym samouczku opisano sposób tworzenia kopii zapasowych programu SQL Server na platformie Azure. W artykule objaśniono również proces odzyskiwania programu SQL Server.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 42a99c1be348a24a9325173dc275d0c416b975a2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465423"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informacje o kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure

Bazy danych programu SQL Server to krytyczne obciążenia, które wymagają niskiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową SQL Server baz danych uruchomionych na maszynach wirtualnych platformy Azure przy użyciu [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

To rozwiązanie wykorzystuje natywne interfejsy API SQL do wykonywania kopii zapasowych baz danych SQL.

* Po określeniu maszyny wirtualnej SQL Server, która ma być chroniona, i zapytania dotyczącej baz danych, usługa Azure Backup Service zainstaluje rozszerzenie kopii zapasowej obciążenia na maszynie wirtualnej `AzureBackupWindowsWorkload`o rozszerzeniu nazwy  .
* To rozszerzenie składa się z koordynatora i wtyczki SQL. Chociaż koordynator jest odpowiedzialny za wyzwalanie przepływów pracy dla różnych operacji, takich jak konfigurowanie kopii zapasowej, tworzenie kopii zapasowej i przywracanie, wtyczka jest odpowiedzialna za rzeczywisty przepływ danych.
* Aby móc odnajdywać bazy danych na tej maszynie wirtualnej, Azure Backup tworzy konto `NT SERVICE\AzureWLBackupPluginSvc`. To konto jest używane na potrzeby tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL. Azure Backup korzysta z `NT AUTHORITY\SYSTEM` konta do odnajdywania i wyszukiwania bazy danych, więc to konto musi być publicznym logowaniem na serwerze SQL. Jeśli maszyna wirtualna SQL Server nie została utworzona z poziomu portalu Azure Marketplace, może zostać wyświetlony komunikat o błędzie **UserErrorSQLNoSysadminMembership**. W takim przypadku [postępuj zgodnie z tymi instrukcjami](backup-azure-sql-database.md).
* Po zainicjowaniu konfigurowania ochrony dla wybranych baz danych usługa tworzenia kopii zapasowych konfiguruje koordynatora przy użyciu harmonogramów tworzenia kopii zapasowych i innych szczegółów zasad, które są buforowane lokalnie na maszynie wirtualnej. 
* W zaplanowanym czasie koordynator komunikuje się z wtyczką i zaczyna przesyłać strumieniowo dane kopii zapasowej z programu SQL Server przy użyciu infrastruktury VDI.  
* Wtyczka wysyła dane bezpośrednio do magazynu usługi Recovery Services, co eliminuje konieczność lokalizacji tymczasowej. Dane są szyfrowane i przechowywane przez usługę Azure Backup na kontach magazynu.
* Po zakończeniu transferu danych koordynator potwierdza zatwierdzenie w usłudze kopii zapasowej.

  ![Architektura kopii zapasowych SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem Sprawdź, czy:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) w witrynie Marketplace.
2. Zapoznaj się z zagadnieniami dotyczącymi [funkcji](#feature-consideration-and-limitations) i [scenariuszem](#scenario-support).
3. [Przejrzyj często zadawane pytania](faq-backup-sql-server.md) dotyczące tego scenariusza.

## <a name="scenario-support"></a>Obsługa scenariuszy

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Obsługiwane wdrożenia** | Obsługiwane są maszyny wirtualne SQL Marketplace Azure i maszyny wirtualne spoza witryny Marketplace (z ręcznie instalowanym programem SQL Server).
**Obsługiwane obszary geograficzne** | Australia Południowo-Wschodnia (ASE), Australia Wschodnia (AE) <br> Brazylia Południowa (BRS)<br> Kanada Środkowa (CNC), Kanada Wschodnia (CE)<br> Azja Wschodnia Południowe (SEA), Azja Wschodnia (EA) <br> Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), zachodnio-środkowe stany USA (WCUS), zachodnie stany USA (WUS); Zachodnie stany USA 2 (WUS 2) Północno-środkowe stany USA (NCUS) środkowe stany USA (CUS) Południowo-środkowe stany USA (SCUS) <br> Indie Środkowe (INC.), Indie Południowe (INS) <br> Japonia Wschodnia (JPE), Japonia Zachodnia (JPW) <br> Korea Środkowa (KRC), Korea Południowa (KRS) <br> Europa Północna (NE), Europa Zachodnia <br> Południowe Zjednoczone Królestwo (UKS), Zachodnie Zjednoczone Królestwo (UKW)
**Obsługiwane systemy operacyjne** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> System Linux nie jest obecnie obsługiwany.
**Obsługiwane wersje programu SQL Server** | SQL Server 2017, zgodnie z opisem [poniżej](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 i SPS w sposób opisany [tutaj](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Obsługiwane wersje platformy .NET** | .NET Framework 4.5.2 i nowsze wersje zainstalowane na maszynie wirtualnej

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Obsługa SQL Server 2008 i SQL Server 2008 R2

Azure Backup ostatnio ogłosiła wsparcie dla [EOS SQL Servers](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) -SQL Server 2008 i SQL Server 2008 R2. Rozwiązanie jest obecnie dostępne w wersji zapoznawczej dla usługi EOS SQL Server i obsługuje następującą konfigurację:

1. SQL Server 2008 i SQL Server 2008 R2 uruchomione w systemie Windows 2008 R2 z dodatkiem SP1
2. .NET Framework 4.5.2 i nowsze muszą być zainstalowane na maszynie wirtualnej
3. Tworzenie kopii zapasowej dla FCI i dublowanych baz danych nie jest obsługiwane

Użytkownicy nie będą obciążani opłatami za tę funkcję aż do momentu, w którym jest ogólnie dostępna. Wszystkie inne zagadnienia [i ograniczenia dotyczące funkcji](#feature-consideration-and-limitations) są również stosowane do tych wersji. Należy zapoznać się z [wymaganiami wstępnymi](backup-sql-server-database-azure-vms.md#prerequisites) przed skonfigurowaniem ochrony na serwerach SQL 2008 i 2008 R2, które obejmują ustawienie [klucza rejestru](backup-sql-server-database-azure-vms.md#add-registry-key-to-enable-registration) (ten krok nie jest wymagany, gdy funkcja jest ogólnie dostępna).


## <a name="feature-consideration-and-limitations"></a>Zagadnienia i ograniczenia dotyczące funkcji

- SQL Server kopii zapasowej można skonfigurować w Azure Portal lub **PowerShell**. Interfejs wiersza polecenia nie jest obsługiwany.
- Rozwiązanie jest obsługiwane w przypadku obu rodzajów [wdrożeń](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) — Azure Resource Manager maszyn wirtualnych i klasycznych maszyn wirtualnych.
- Maszyna wirtualna z systemem SQL Server wymaga łączności z Internetem, aby uzyskać dostęp do publicznych adresów IP platformy Azure.
- SQL Server **wystąpienia klastra trybu failover (FCI)** i SQL Server zawsze włączone wystąpienie klastra trybu failover nie są obsługiwane.
- Operacje tworzenia kopii zapasowych i przywracania dla duplikatów baz danych i migawek baz danych nie są obsługiwane.
- Użycie więcej niż jednego rozwiązania do tworzenia kopii zapasowych w celu utworzenia kopii zapasowej autonomicznego wystąpienia SQL Server lub zawsze włączona Grupa dostępności programu SQL Server może prowadzić do awarii kopii zapasowej; nie należy tego robić.
- Tworzenie kopii zapasowej dwóch węzłów grupy dostępności indywidualnie z tymi samymi lub różnymi rozwiązaniami może również prowadzić do niepowodzenia kopii zapasowych.
- Azure Backup obsługuje tylko pełne i pełne kopie zapasowe tylko dla baz danych **tylko do odczytu**
- Nie można chronić baz danych z dużą liczbą plików. Maksymalna liczba obsługiwanych plików to **~ 1000**.  
- Można utworzyć kopię zapasową do **~ 2000** SQL Server baz danych w magazynie. Możesz utworzyć wiele magazynów, jeśli masz większą liczbę baz danych.
- Można skonfigurować kopie zapasowe dla maksymalnie **50** baz danych w jednym z nich. to ograniczenie ułatwia optymalizację obciążeń kopii zapasowych.
- Obsługiwane są bazy danych o rozmiarze do **2 TB** . w przypadku większych rozmiarów mogą wystąpić problemy z wydajnością.
- Aby określić, jak wiele baz danych może być chronionych na serwer, należy wziąć pod uwagę takie czynniki jak przepustowość, rozmiar maszyny wirtualnej, częstotliwość tworzenia kopii zapasowych, rozmiar bazy danych itp. [Pobierz](http://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) planistę zasobów, który zapewnia przybliżoną liczbę baz danych, które mogą być dostępne dla każdego serwera, na podstawie zasobów maszyny wirtualnej i zasad tworzenia kopii zapasowych.
- W przypadku grup dostępności kopie zapasowe są pobierane z różnych węzłów w oparciu o kilka czynników. Zachowanie tworzenia kopii zapasowej dla grupy dostępności znajduje się poniżej.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Zachowanie tworzenia kopii zapasowej w przypadku zawsze włączonych grup dostępności

Zaleca się, aby kopie zapasowe zostały skonfigurowane tylko na jednym węźle w AG. Kopia zapasowa powinna być zawsze konfigurowana w tym samym regionie co węzeł podstawowy. Innymi słowy, w regionie, w którym konfigurujesz kopię zapasową, jest zawsze potrzebny węzeł podstawowy. Jeśli wszystkie węzły w AG znajdują się w tym samym regionie, w którym jest skonfigurowana kopia zapasowa, nie ma żadnego problemu.

**W przypadku międzyregionowej AG**
- Niezależnie od preferencji tworzenia kopii zapasowej kopie zapasowe nie będą wykonywane z węzłów, które nie znajdują się w tym samym regionie, w którym jest skonfigurowana kopia zapasowa. Wynika to z faktu, że kopie zapasowe między regionami nie są obsługiwane. Jeśli masz tylko 2 węzły, a węzeł pomocniczy znajduje się w innym regionie; w takim przypadku kopie zapasowe będą nadal wykonywane z węzła podstawowego (chyba że preferencja kopii zapasowej jest tylko pomocnicza).
- Jeśli przechodzenie w tryb failover następuje w regionie innym niż ten, w którym skonfigurowano tworzenie kopii zapasowej, kopie zapasowe będą kończyć się niepowodzeniem w węzłach w regionie przełączenia w tryb failover.

W zależności od preferencji tworzenia kopii zapasowych i typów kopii zapasowych (pełna/różnicowa/log/Copy-Only) kopie zapasowe są pobierane z określonego węzła (podstawowy/pomocniczy).

- **Preferencja kopii zapasowej: Głównym**

**Typ kopii zapasowej** | **Node**
    --- | ---
    Pełne | Podstawowy
    Różnicy | Podstawowy
    Log |  Podstawowy
    Tylko kopiowanie pełne |  Podstawowy

- **Preferencja kopii zapasowej: Tylko pomocnicza**

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicy | Podstawowy
Log |  Pomocnicza
Tylko kopiowanie pełne |  Pomocnicza

- **Preferencja kopii zapasowej: Dodatkowych**

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicy | Podstawowy
Log |  Pomocnicza
Tylko kopiowanie pełne |  Pomocnicza

- **Brak preferencji dotyczących kopii zapasowych**

**Typ kopii zapasowej** | **Node**
--- | ---
Pełne | Podstawowy
Różnicy | Podstawowy
Log |  Pomocnicza
Tylko kopiowanie pełne |  Pomocnicza

## <a name="set-vm-permissions"></a>Ustawianie uprawnień maszyny wirtualnej

  Po uruchomieniu odnajdywania na SQL Server, Azure Backup wykonuje następujące czynności:

* Dodaje rozszerzenie AzureBackupWindowsWorkload.
* Tworzy konto NT SERVICE\AzureWLBackupPluginSvc do odnajdywania baz danych na maszynie wirtualnej. To konto jest używane do tworzenia kopii zapasowych i przywracania oraz wymaga uprawnień administratora systemu SQL.
* Odnajduje bazy danych, które są uruchomione na maszynie wirtualnej, Azure Backup korzysta z konta NT NT\SYSTEM. To konto musi być publicznym logowaniem na serwerze SQL.

Jeśli maszyna wirtualna SQL Server nie została utworzona w portalu Azure Marketplace lub korzystasz z usług SQL 2008 i 2008 R2, może wystąpić błąd **UserErrorSQLNoSysadminMembership** .

W przypadku nadawania uprawnień w przypadku **programów SQL 2008** i **2008 R2** uruchomionych w systemie Windows 2008 R2 zapoznaj się [tutaj](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

W przypadku wszystkich innych wersji należy rozwiązać uprawnienia, wykonując następujące czynności:

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
> Jeśli SQL Server ma zainstalowanych wiele wystąpień SQL Server, należy dodać uprawnienia sysadmin dla konta **NT Service\AzureWLBackupPluginSvc** do wszystkich wystąpień programu SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Nadaj uprawnienia administratora systemu SQL dla programu SQL 2008 i programu SQL 2008 R2

Dodaj logowania **NT NT\SYSTEM** i **NT Service\AzureWLBackupPluginSvc** do wystąpienia SQL Server:

1. Przejdź do wystąpienia SQL Server w Eksploratorze obiektów.
2. Przejdź do > logowania do zabezpieczeń
3. Kliknij prawym przyciskiem myszy nazwę logowania i kliknij pozycję *nowe logowanie...*

    ![Nowa nazwa logowania przy użyciu programu SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Przejdź do karty Ogólne i wprowadź **NT NT\SYSTEM** jako nazwę logowania.

    ![Nazwa logowania dla programu SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Przejdź do *ról serwera* i wybierz role *publiczne* i sysadmin.

    ![Wybieranie ról w programie SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Przejdź do pozycji *stan*. *Przyznaj* uprawnienie do nawiązywania połączenia z aparatem bazy danych i logowanie jako *włączone*.

    ![Przyznawanie uprawnień w programie SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kliknij przycisk OK.
8. Powtórz tę samą sekwencję kroków (1-7 powyżej), aby dodać logowanie NT Service\AzureWLBackupPluginSvc do wystąpienia SQL Server. Jeśli logowanie już istnieje, upewnij się, że ma ona rolę serwera sysadmin i w obszarze stan, przyznaje uprawnienia do nawiązywania połączenia z aparatem bazy danych i logowanie jako włączone.
9. Po udzieleniu uprawnień **ponownie odkryj baz danych** w portalu: Obciążenie **->** **infrastruktury->** kopii zapasowej magazynu na maszynie wirtualnej platformy Azure:

    ![Odnajdź baz danych w Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternatywnie można zautomatyzować nadawanie uprawnień, uruchamiając następujące polecenia programu PowerShell w trybie administratora. Nazwa wystąpienia jest domyślnie ustawiona na MSSQLSERVER. W razie potrzeby zmień argument Nazwa wystąpienia w skrypcie:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat](backup-sql-server-database-azure-vms.md) tworzenia kopii zapasowych baz danych SQL Server.
* [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
* [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.
