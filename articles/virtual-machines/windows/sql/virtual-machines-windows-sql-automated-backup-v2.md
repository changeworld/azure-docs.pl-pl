---
title: Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure z programu SQL Server 2016/2017 | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcję automatycznej kopii zapasowej dla maszyn wirtualnych programu SQL Server 2016/2017 uruchomionych na platformie Azure. Ten artykuł jest specyficzny dla maszyn wirtualnych przy użyciu Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651439"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure (Menedżer zasobów)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [Program SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatyczna kopia zapasowa w wersji 2 automatycznie konfiguruje [zarządzaną kopię zapasową](https://msdn.microsoft.com/library/dn449496.aspx) na platformie Microsoft Azure dla wszystkich istniejących i nowych baz danych na maszynie Wirtualnej platformy Azure z systemem SQL Server 2016/2017 w wersjach standard, enterprise lub developer. Dzięki temu można skonfigurować regularne kopie zapasowe bazy danych, które wykorzystują trwałe magazynu obiektów blob platformy Azure. Automatyczna kopia zapasowa w wersji 2 zależy od [rozszerzenia agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć automatycznej kopii zapasowej w wersji 2, przejrzyj następujące wymagania wstępne:

**System operacyjny**:

- Windows Server 2012 R2
- Windows Server 2016

**Wersja/wersja programu SQL Server:**

- SQL Server 2016: Deweloper, Standard lub Enterprise
- SQL Server 2017: Deweloper, Standard lub Enterprise

> [!IMPORTANT]
> Automatyczna kopia zapasowa w wersji 2 współpracuje z programem SQL Server 2016 lub nowszym. Jeśli używasz programu SQL Server 2014, możesz użyć automatycznej kopii zapasowej w wersji 1 do tworzenia kopii zapasowych baz danych. Aby uzyskać więcej informacji, zobacz [Automatyczna kopia zapasowa dla maszyn wirtualnych platformy Azure sql server 2014](virtual-machines-windows-sql-automated-backup.md).

**Konfiguracja bazy danych**:

- Docelowe bazy danych muszą używać pełnego modelu odzyskiwania. Aby uzyskać więcej informacji na temat wpływu pełnego modelu odzyskiwania na kopie zapasowe, zobacz [Tworzenie kopii zapasowych w modelu pełnego odzyskiwania](https://technet.microsoft.com/library/ms190217.aspx).
- Systemowe bazy danych nie muszą używać pełnego modelu odzyskiwania. Jeśli jednak wymagane są kopie zapasowe dziennika, które mają być pobierane dla modelu lub usługi MSDB, należy użyć pełnego modelu odzyskiwania.
- Docelowe bazy danych muszą znajdować się w domyślnym wystąpieniu programu SQL Server lub [poprawnie zainstalowanym](virtual-machines-windows-sql-server-iaas-faq.md#administration) wystąpieniu o nazwie. 

> [!NOTE]
> Automatyczna kopia zapasowa opiera się na **rozszerzeniu agenta IaaS programu SQL Server**. Bieżące obrazy galerii maszyn wirtualnych SQL domyślnie dodają to rozszerzenie. Aby uzyskać więcej informacji, zobacz [ROZSZERZENIE AGENTA PROGRAMU SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla automatycznej kopii zapasowej w wersji 2. Kroki konfiguracji rzeczywistej różnią się w zależności od tego, czy używasz poleceń azure portal lub programu Azure Windows PowerShell.

### <a name="basic-settings"></a>Ustawienia podstawowe

| Ustawienie | Zakres (domyślnie) | Opis |
| --- | --- | --- |
| **Automatyczna kopia zapasowa** | Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza automatyczną kopię zapasową dla maszyny Wirtualnej platformy Azure z systemem SQL Server 2016/2017 Developer, Standard lub Enterprise. |
| **Okres przechowywania** | 1-30 dni (30 dni) | Liczba dni przechowywania kopii zapasowych. |
| **Konto magazynu** | Konto magazynu Azure | Konto magazynu platformy Azure do przechowywania plików automatycznej kopii zapasowej w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji do przechowywania wszystkich plików kopii zapasowych. Konwencja nazewnictwa plików kopii zapasowych zawiera identyfikator GUID daty, godziny i bazy danych. |
| **Szyfrowania** |Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza szyfrowanie. Gdy szyfrowanie jest włączone, certyfikaty używane do przywracania kopii zapasowej znajdują się na określonym koncie magazynu. Używa tego samego **kontenera automatycznej kopii zapasowej** z tej samej konwencji nazewnictwa. Jeśli hasło ulegnie zmianie, zostanie wygenerowany nowy certyfikat przy tym haśle, ale stary certyfikat pozostaje do przywrócenia wcześniejszych kopii zapasowych. |
| **Hasło** |Tekst hasła | Hasło kluczy szyfrowania. To hasło jest wymagane tylko wtedy, gdy szyfrowanie jest włączone. Aby przywrócić zaszyfrowaną kopię zapasową, musisz mieć poprawne hasło i powiązany certyfikat, który był używany w momencie wykonywania kopii zapasowej. |

### <a name="advanced-settings"></a>Ustawienia zaawansowane

| Ustawienie | Zakres (domyślnie) | Opis |
| --- | --- | --- |
| **Tworzenie kopii zapasowych bazy danych systemu** | Włącz/Wyłącz (wyłączone) | Po włączeniu ta funkcja również kopii zapasowej systemowych baz danych: Master, MSDB i Model. W przypadku baz danych MSDB i Model sprawdź, czy są one w trybie pełnego odzyskiwania, jeśli chcesz wykonać kopie zapasowe dziennika. Kopie zapasowe dziennika nigdy nie są pobierane dla wzorca. I nie kopie zapasowe są podejmowane dla TempDB. |
| **Harmonogram tworzenia kopii zapasowych** | Ręczny/zautomatyzowany (zautomatyzowany) | Domyślnie harmonogram tworzenia kopii zapasowych jest automatycznie określany na podstawie wzrostu dziennika. Ręczny harmonogram tworzenia kopii zapasowych umożliwia użytkownikowi określenie przedziału czasu tworzenia kopii zapasowych. W takim przypadku kopie zapasowe odbywają się tylko z określoną częstotliwością i w określonym przedziale czasu danego dnia. |
| **Pełna częstotliwość tworzenia kopii zapasowych** | Codziennie/co tydzień | Częstotliwość pełnych kopii zapasowych. W obu przypadkach pełne kopie zapasowe rozpoczynają się w następnym zaplanowanym oknie czasu. Gdy wybrano opcję co tydzień, kopie zapasowe mogą obejmować wiele dni, dopóki wszystkie bazy danych nie zostaną pomyślnie utworzone. |
| **Pełny czas rozpoczęcia tworzenia kopii zapasowej** | 00:00 – 23:00 (01:00) | Godzina rozpoczęcia danego dnia, podczas którego mogą odbywać się pełne kopie zapasowe. |
| **Okno pełnego czasu tworzenia kopii zapasowych** | 1 – 23 godziny (1 godzina) | Czas trwania przedziału czasu danego dnia, w którym mogą odbywać się pełne kopie zapasowe. |
| **Log częstotliwość tworzenia kopii zapasowych** | 5 – 60 minut (60 minut) | Częstotliwość tworzenia kopii zapasowych dziennika. |

## <a name="understanding-full-backup-frequency"></a>Opis pełnej częstotliwości tworzenia kopii zapasowych
Ważne jest, aby zrozumieć różnicę między dziennymi i cotygodniowymi pełnymi kopiami zapasowymi. Rozważmy następujące dwa przykładowe scenariusze.

### <a name="scenario-1-weekly-backups"></a>Scenariusz 1: Cotygodniowe kopie zapasowe
Masz maszynę wirtualną programu SQL Server, która zawiera wiele dużych baz danych.

W poniedziałek włączysz automatyczną kopię zapasową w wersji 2 z następującymi ustawieniami:

- Harmonogram tworzenia kopii zapasowych: **Ręczny**
- Pełna częstotliwość tworzenia kopii zapasowych: **Co tydzień**
- Pełny czas rozpoczęcia tworzenia kopii zapasowej: **01:00**
- Pełny czas tworzenia kopii zapasowych: **1 godzina**

Oznacza to, że następne dostępne okno kopii zapasowej jest wtorek o 1 w nocy na 1 godzinę. W tym czasie automatyczna kopia zapasowa rozpoczyna wykonywanie kopii zapasowych baz danych po jednym na raz. W tym scenariuszu bazy danych są wystarczająco duże, że pełne kopie zapasowe są kompletne dla pierwszych baz danych para. Jednak po godzinie nie wszystkie bazy danych zostały utworzone.

W takim przypadku automatyczna kopia zapasowa rozpoczyna wykonywanie kopii zapasowej pozostałych baz danych następnego dnia, w środę o godzinie 1:00 przez jedną godzinę. Jeśli nie wszystkie bazy danych zostały utworzone w tym czasie, próbuje ponownie następnego dnia w tym samym czasie. Jest to kontynuowane, dopóki nie wszystkie bazy danych zostały pomyślnie utworzone.

Po osiągnięciu wtorek ponownie, Automated Backup rozpoczyna tworzenie kopii zapasowych wszystkich baz danych ponownie.

W tym scenariuszu pokazano, że automatyczna kopia zapasowa działa tylko w określonym przedziale czasu, a każda baza danych jest archiwizuje się raz w tygodniu. Pokazuje to również, że jest możliwe dla kopii zapasowych obejmować wiele dni w przypadku, gdy nie jest możliwe, aby wykonać wszystkie kopie zapasowe w ciągu jednego dnia.

### <a name="scenario-2-daily-backups"></a>Scenariusz 2: Codzienne kopie zapasowe
Masz maszynę wirtualną programu SQL Server, która zawiera wiele dużych baz danych.

W poniedziałek włączysz automatyczną kopię zapasową w wersji 2 z następującymi ustawieniami:

- Harmonogram tworzenia kopii zapasowych: Ręczny
- Pełna częstotliwość tworzenia kopii zapasowych: Codziennie
- Pełny czas rozpoczęcia tworzenia kopii zapasowej: 22:00
- Pełny czas tworzenia kopii zapasowych: 6 godzin

Oznacza to, że następne dostępne okno kopii zapasowej to poniedziałek o godzinie 22:00 przez 6 godzin. W tym czasie automatyczna kopia zapasowa rozpoczyna wykonywanie kopii zapasowych baz danych po jednym na raz.

Następnie we wtorek o godzinie 10 przez 6 godzin pełne kopie zapasowe wszystkich baz danych zaczynają się od nowa.

> [!IMPORTANT]
> Podczas planowania codziennych kopii zapasowych zaleca się zaplanowanie szerokiego przedziału czasowego, aby upewnić się, że w tym czasie można utworzyć kopię zapasową wszystkich baz danych. Jest to szczególnie ważne w przypadku, gdy masz dużą ilość danych do utworzenia kopii zapasowej.

## <a name="configure-new-vms"></a>Konfigurowanie nowych maszyn wirtualnych

Użyj witryny Azure Portal, aby skonfigurować automatyczną kopię zapasową w wersji 2 podczas tworzenia nowej maszyny wirtualnej programu SQL Server 2016 lub 2017 w modelu wdrażania Menedżera zasobów.

Na karcie **Ustawienia programu SQL Server** wybierz pozycję **Włącz** w obszarze **Automatyczna kopia zapasowa**. Poniższy zrzut ekranu portalu platformy Azure przedstawia ustawienia **automatycznej kopii zapasowej SQL.**

![Konfiguracja zautomatyzowanej kopii zapasowej SQL w witrynie Azure portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatyczna kopia zapasowa w wersji 2 jest domyślnie wyłączona.

## <a name="configure-existing-vms"></a>Konfigurowanie istniejących maszyn wirtualnych

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych programu SQL Server przejdź do [zasobu maszyn wirtualnych SQL,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) a następnie wybierz pozycję **Kopie zapasowe,** aby skonfigurować automatyczne kopie zapasowe.

![Automatyczna kopia zapasowa SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Po zakończeniu kliknij przycisk **Zastosuj** u dołu strony Ustawienia **kopii zapasowych,** aby zapisać zmiany.

Jeśli po raz pierwszy włączysz automatyczną kopię zapasową, platforma Azure konfiguruje agenta IaaS programu SQL Server w tle. W tym czasie witryna Azure portal może nie wykazywać, że skonfigurowano automatyczną kopię zapasową. Poczekaj kilka minut na zainstalowanie agenta, skonfigurowane. Następnie witryna Azure portal będzie odzwierciedlać nowe ustawienia.

## <a name="configure-with-powershell"></a>Konfigurowanie za pomocą programu PowerShell

Za pomocą programu PowerShell można skonfigurować automatyczną kopię zapasową w wersji 2. Przed rozpoczęciem należy:

- [Pobierz i zainstaluj najnowszą usługę Azure PowerShell](https://aka.ms/webpi-azps).
- Otwórz program Windows PowerShell i skojarz go ze swoim kontem za pomocą polecenia **Connect-AzAccount.**

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Instalowanie rozszerzenia IaaS sql
Jeśli aprowizowana maszyna wirtualna programu SQL Server została udostępniona z portalu Azure, rozszerzenie IaaS programu SQL Server powinno być już zainstalowane. Można określić, czy jest zainstalowany dla maszyny Wirtualnej, wywołując polecenie **Get-AzVM** i sprawdzając **Extensions** właściwości.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Jeśli jest zainstalowane rozszerzenie programu SQL Server IaaS Agent, powinien on zostać wyświetlony jako "SqlIaaSAgent" lub "SQLIaaSExtension". **ProvisioningState** dla rozszerzenia powinien również pokazać "Powiodło się". 

Jeśli nie jest zainstalowany lub nie można aprowizować, można zainstalować go za pomocą następującego polecenia. Oprócz nazwy maszyny Wirtualnej i grupy zasobów należy również określić region (**$region**), w których znajduje się maszyna wirtualna.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Weryfikowanie bieżących ustawień
Jeśli włączono automatyczną kopię zapasową podczas inicjowania obsługi administracyjnej, można użyć programu PowerShell, aby sprawdzić bieżącą konfigurację. Uruchom polecenie **Get-AzVMSqlServerExtension** i sprawdź właściwość **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Powinieneś uzyskać dane wyjściowe podobne do następujących:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Jeśli dane wyjściowe pokazuje, że **Włącz** jest **ustawiona**na False , a następnie należy włączyć automatyczną kopię zapasową. Dobrą wiadomością jest to, że można włączyć i skonfigurować automatyczną kopię zapasową w ten sam sposób. Zobacz następną sekcję, aby uzyskać te informacje.

> [!NOTE] 
> Jeśli sprawdzisz ustawienia natychmiast po dokonaniu zmiany, możliwe jest, że wrócisz stare wartości konfiguracji. Zaczekaj kilka minut i ponownie sprawdź ustawienia, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup-v2"></a>Konfigurowanie automatycznej kopii zapasowej w wersji 2
Program PowerShell umożliwia włączenie automatycznej kopii zapasowej, a także zmodyfikowanie jej konfiguracji i zachowania w dowolnym momencie. 

Najpierw wybierz lub utwórz konto magazynu dla plików kopii zapasowej. Poniższy skrypt wybiera konto magazynu lub tworzy je, jeśli nie istnieje.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatyczna kopia zapasowa nie obsługuje przechowywania kopii zapasowych w magazynie w wersji premium, ale może wykonać kopie zapasowe z dysków maszyn wirtualnych korzystających z magazynu w wersji Premium.

Następnie użyj polecenia **New-AzVMMSqlServerAutoBackupConfig,** aby włączyć i skonfigurować ustawienia automatycznej kopii zapasowej w wersji 2 do przechowywania kopii zapasowych na koncie magazynu platformy Azure. W tym przykładzie kopie zapasowe są ustawione do przechowywania przez 10 dni. Kopie zapasowe bazy danych systemu są włączone. Pełne kopie zapasowe są planowane co tydzień, a okno czasowe zaczyna się od 20:00 przez dwie godziny. Kopie zapasowe dziennika są planowane co 30 minut. Drugie polecenie **Set-AzVMMSqlServerExtension**aktualizuje określoną maszynę wirtualną platformy Azure za pomocą tych ustawień.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Zainstalowanie i skonfigurowanie agenta IaaS programu SQL Server może potrwać kilka minut. 

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt, aby przekazać parametr **EnableEncryption** wraz z hasłem (bezpiecznym ciągiem) dla **parametru CertificatePassword.** Poniższy skrypt włącza ustawienia automatycznej kopii zapasowej w poprzednim przykładzie i dodaje szyfrowanie.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Aby potwierdzić zastosowanie ustawień, [sprawdź konfigurację automatycznej kopii zapasowej](#verifysettings).

### <a name="disable-automated-backup"></a>Wyłącz automatyczną kopię zapasową
Aby wyłączyć automatyczną kopię zapasową, uruchom ten sam skrypt bez parametru **-Enable** do polecenia **New-AzVMSqlServerAutoBackupConfig.** Brak parametru **-Enable** sygnalizuje polecenie wyłączenia funkcji. Podobnie jak w przypadku instalacji, wyłączenie automatycznej kopii zapasowej może potrwać kilka minut.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Przykładowy skrypt
Poniższy skrypt zawiera zestaw zmiennych, które można dostosować, aby włączyć i skonfigurować automatyczną kopię zapasową dla maszyny Wirtualnej. W takim przypadku może być konieczne dostosowanie skryptu na podstawie wymagań. Na przykład należy wprowadzić zmiany, jeśli chcesz wyłączyć kopię zapasową systemowych baz danych lub włączyć szyfrowanie.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorowanie

Aby monitorować automatyczną kopię zapasową w programie SQL Server 2016/2017, dostępne są dwie główne opcje. Ponieważ automatyczna kopia zapasowa korzysta z funkcji zarządzanej kopii zapasowej programu SQL Server, te same techniki monitorowania dotyczą obu.

Najpierw możesz sondować stan, dzwoniąc do [pliku msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub kwerendy [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabeli wartości funkcji.

Inną opcją jest skorzystanie z wbudowanej funkcji Poczty bazy danych dla powiadomień.

1. Wywołanie procedury składowanej [msdb.managed_backup.sp_set_parameter,](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) aby przypisać adres e-mail do parametru **SSMBackup2WANotificationEmailIds.** 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny Wirtualnej platformy Azure.
1. Konfigurowanie poczty bazy danych za pomocą serwera SMTP i nazwy użytkownika. Poczcie bazy danych można skonfigurować w programie SQL Server Management Studio lub za pomocą poleceń Transact-SQL. Aby uzyskać więcej informacji, zobacz [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Skonfiguruj program SQL Server Agent do używania poczty bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy port SMTP jest dozwolony zarówno za pośrednictwem lokalnej zapory maszyny Wirtualnej, jak i sieciowej grupy zabezpieczeń maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki
Automatyczna kopia zapasowa w wersji 2 konfiguruje zarządzaną kopię zapasową na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [przejrzeć dokumentację managed backup,](https://msdn.microsoft.com/library/dn449496.aspx) aby zrozumieć zachowanie i implikacje.

Dodatkowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania programu SQL Server można znaleźć w następującym artykule: [Kopia zapasowa i przywracanie dla programu SQL Server w programie Azure Virtual Machines.](virtual-machines-windows-sql-backup-recovery.md)

Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [Rozszerzenie agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)

