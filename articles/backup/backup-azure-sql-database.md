---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na platformie Azure
description: W tym artykule wyjaśniono, jak zrobić na platformie Azure kopii zapasowej programu SQL Server. W artykule objaśniono również proces odzyskiwania programu SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408764"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informacje o kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure

[Usługa Azure Backup](backup-overview.md) oferuje specjalistyczne rozwiązanie oparte na strumieniu do tworzenia kopii zapasowych programu SQL Server działającego na maszynach wirtualnych platformy Azure. To rozwiązanie jest zgodne z zaletami usługi Azure Backup wynikające z tworzenia kopii zapasowych bez infrastruktury, długoterminowego przechowywania i centralnego zarządzania. Dodatkowo zapewnia następujące zalety specjalnie dla programu SQL Server:

1. Kopie zapasowe obsługujące obciążenie, które obsługują wszystkie typy kopii zapasowych — pełne, różnicowe i dziennikowe
2. 15-min RPO (cel punktu odzyskiwania) z częstymi kopiami zapasowymi dziennika
3. Odzyskiwanie punktu w czasie do sekundy
4. Tworzenie kopii zapasowych i przywracanie na poziomie bazy danych

Aby wyświetlić scenariusze tworzenia kopii zapasowych i przywracania, które obsługujemy dzisiaj, zapoznaj się z [macierzą pomocy technicznej](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

To rozwiązanie wykorzystuje natywne interfejsy API SQL do wykonywania kopii zapasowych baz danych SQL.

* Po określeniu maszyny Wirtualnej programu SQL Server, którą chcesz chronić i kwerendy dla baz danych w `AzureBackupWindowsWorkload` niej, usługa Azure Backup zainstaluje rozszerzenie kopii zapasowej obciążenia na maszynie Wirtualnej przez rozszerzenie nazwy.
* To rozszerzenie składa się z koordynatora i wtyczki SQL. Podczas gdy koordynator jest odpowiedzialny za wyzwalanie przepływów pracy dla różnych operacji, takich jak konfigurowanie kopii zapasowej, tworzenia kopii zapasowych i przywracania, wtyczka jest odpowiedzialna za rzeczywisty przepływ danych.
* Aby móc odnajdować bazy danych na tej `NT SERVICE\AzureWLBackupPluginSvc`maszynie wirtualnej, usługa Azure Backup tworzy konto . To konto jest używane do tworzenia kopii zapasowych i przywracania i wymaga uprawnień sysadmin SQL. Konto `NT SERVICE\AzureWLBackupPluginSvc` jest [kontem usługi wirtualnej](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)i dlatego nie wymaga zarządzania hasłami. Usługa Azure Backup `NT AUTHORITY\SYSTEM` wykorzystuje konto do odnajdowania/badania bazy danych, więc to konto musi być publicznym logowaniem w języku SQL. Jeśli nie utworzono maszyny wirtualnej programu SQL Server z witryny Azure Marketplace, może wystąpić błąd **UserErrorSQLNoSysadminMembership**. W takim przypadku [wykonaj te instrukcje](#set-vm-permissions).
* Po wyzwoleniu skonfigurować ochronę w wybranych bazach danych, usługa tworzenia kopii zapasowych konfiguruje koordynatora z harmonogramów kopii zapasowych i innych szczegółów zasad, które rozszerzenie buforuje lokalnie na maszynie Wirtualnej.
* W zaplanowanym czasie koordynator komunikuje się z wtyczką i rozpoczyna przesyłanie strumieniowe danych kopii zapasowej z serwera SQL przy użyciu interfejsu VDI.  
* Wtyczka wysyła dane bezpośrednio do magazynu usług odzyskiwania, eliminując w ten sposób potrzebę lokalizacji tymczasowej. Dane są szyfrowane i przechowywane przez usługę Azure Backup na kontach magazynu.
* Po zakończeniu transferu danych koordynator potwierdza zatwierdzenie za pomocą usługi tworzenia kopii zapasowej.

  ![Architektura kopii zapasowej SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem sprawdź poniżej:

1. Upewnij się, że masz wystąpienie programu SQL Server uruchomione na platformie Azure. Możesz [szybko utworzyć wystąpienie programu SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) w witrynie Marketplace.
2. Przejrzyj [uwagę na funkcję](sql-support-matrix.md#feature-consideration-and-limitations) i obsługę [scenariuszy](sql-support-matrix.md#scenario-support).
3. [Przejrzyj często zadawane pytania](faq-backup-sql-server.md) dotyczące tego scenariusza.

## <a name="set-vm-permissions"></a>Ustawianie uprawnień maszyny wirtualnej

  Po uruchomieniu odnajdywania na serwerze SQL Server usługa Azure Backup wykonuje następujące czynności:

* Dodaje rozszerzenie AzureBackupWindowsWorkload.
* Tworzy konto NT SERVICE\AzureWLBackupPluginSvc w celu odnajdowania baz danych na maszynie wirtualnej. To konto jest używane do tworzenia kopii zapasowych i przywracania i wymaga uprawnień sysadmin SQL.
* Odnajduje bazy danych, które są uruchomione na maszynie Wirtualnej, usługa Azure Backup używa konta NT AUTHORITY\SYSTEM. To konto musi być publiczne logowanie na SQL.

Jeśli nie utworzysz maszyny Wirtualnej programu SQL Server w portalu Azure Marketplace lub jeśli korzystasz z programów SQL 2008 i 2008 R2, może zostać wyświetlony błąd **userErrorSQLNoSysadminMembership.**

Aby uzyskać uprawnienia w przypadku **SQL 2008** i **2008 R2** działającego w systemie Windows 2008 R2, zapoznaj się [z tym polem .](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)

W przypadku wszystkich innych wersji należy naprawić uprawnienia, wykonując następujące czynności:

  1. Zaloguj się do programu SQL Server Management Studio (SSMS), używając konta z uprawnieniami administratora systemu SQL Server. Jeśli nie potrzebujesz specjalnych uprawnień, uwierzytelnianie systemu Windows powinno działać.
  2. Na serwerze SQL Server otwórz folder **Zabezpieczenia/Logowania.**

      ![Otwieranie folderu Security/Logins w celu wyświetlenia kont](./media/backup-azure-sql-database/security-login-list.png)

  3. Kliknij prawym przyciskiem myszy folder **Logins** i wybierz polecenie **Nowy login**. W oknie **Nazwa logowania — nowa** wybierz pozycję **Wyszukaj**.

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
> Jeśli program SQL Server ma zainstalowane wiele wystąpień programu SQL Server, należy dodać uprawnienie sysadmin dla **konta USŁUGI NT\AzureWLBackPluginSvc** do wszystkich wystąpień SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Nadaj uprawnienia do sysadmin SQL dla programów SQL 2008 i SQL 2008 R2

Dodaj do wystąpienia programu SQL Server logowania **NT AUTHORITY\SYSTEM** i **NT Service\AzureWLBackupPluginSvc:**

1. Przejdź do wystąpienia programu SQL Server w Eksploratorze obiektów.
2. Przejdź do logowania > zabezpieczeń
3. Kliknij prawym przyciskiem myszy na loginy i kliknij *nowy login...*

    ![Nowy login za pomocą SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Przejdź do karty Ogólne i wprowadź **NT AUTHORITY\SYSTEM** jako nazwę logowania.

    ![nazwa logowania do SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Przejdź do *ról serwera* i wybierz role *publiczne* i *sysadmin.*

    ![wybieranie ról w SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Przejdź do *statusu*. *Udziel* uprawnienia do łączenia się z aparatem bazy danych i logowania jako *włączone*.

    ![Udzielanie uprawnień w ssms](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kliknij przycisk OK.
8. Powtórz tę samą sekwencję kroków (1-7 powyżej), aby dodać nt service\AzureWLBackupPluginSvc logowania do wystąpienia programu SQL Server. Jeśli logowanie już istnieje, upewnij się, że ma rolę serwera sysadmin i w obszarze Stan ma udzielić uprawnienia do łączenia się z aparatem bazy danych i zaloguj się jako włączone.
9. Po udzieleniu uprawnień ponownie **odkryj dbs** w **->** portalu: Obciążenie infrastruktury **->** kopii zapasowej w programie Azure VM:

    ![Odkryj na nowo db w witrynie Azure portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Alternatywnie można zautomatyzować nadanie uprawnień, uruchamiając następujące polecenia programu PowerShell w trybie administracyjnym. Nazwa wystąpienia jest domyślnie ustawiona na MSSQLSERVER. Zmień argument nazwy wystąpienia w skrypcie, jeśli zajdzie taka potrzeba:

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

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o](backup-sql-server-database-azure-vms.md) tworzenie kopii zapasowych baz danych programu SQL Server.
* [Dowiedz się więcej](restore-sql-database-azure-vm.md) o przywracaniu baz danych programu SQL Server z kopii zapasowych.
* [Dowiedz się więcej](manage-monitor-sql-database-backup.md) o zarządzaniu bazami danych programu SQL Server, dla których są tworzone kopie zapasowe.
