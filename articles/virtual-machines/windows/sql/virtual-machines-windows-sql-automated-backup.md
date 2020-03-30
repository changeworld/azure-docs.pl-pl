---
title: Automatyczna kopia zapasowa dla maszyn wirtualnych platformy Azure programu SQL Server 2014
description: W tym artykule opisano funkcję automatycznej kopii zapasowej dla maszyn wirtualnych programu SQL Server 2014 uruchomionych na platformie Azure. Ten artykuł jest specyficzny dla maszyn wirtualnych przy użyciu Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c7dea85d8de17a0f65e6e73b5b5fbe619d464d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650344"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatyczna kopia zapasowa dla maszyn wirtualnych programu SQL Server 2014 (Menedżer zasobów)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [Program SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatyczna kopia zapasowa automatycznie konfiguruje [zarządzaną kopię zapasową](https://msdn.microsoft.com/library/dn449496.aspx) na platformie Microsoft Azure dla wszystkich istniejących i nowych baz danych na maszynie Wirtualnej Platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. Dzięki temu można skonfigurować regularne kopie zapasowe bazy danych, które wykorzystują trwałe magazynu obiektów blob platformy Azure. Automatyczna kopia zapasowa zależy od [rozszerzenia agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć automatycznej kopii zapasowej, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Wersja/wersja programu SQL Server:**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatyczna kopia zapasowa współpracuje z programem SQL Server 2014. Jeśli używasz programu SQL Server 2016/2017, możesz użyć automatycznej kopii zapasowej w wersji 2 do utworzenia kopii zapasowej baz danych. Aby uzyskać więcej informacji, zobacz [Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure sql server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

**Konfiguracja bazy danych**:

- Docelowe bazy danych muszą używać pełnego modelu odzyskiwania. Aby uzyskać więcej informacji na temat wpływu pełnego modelu odzyskiwania na kopie zapasowe, zobacz [Tworzenie kopii zapasowych w modelu pełnego odzyskiwania](https://technet.microsoft.com/library/ms190217.aspx).
- Docelowe bazy danych muszą znajdować się w domyślnym wystąpieniu programu SQL Server. Rozszerzenie IaaS programu SQL Server nie obsługuje nazwanych wystąpień.

> [!NOTE]
> Automatyczna kopia zapasowa opiera się na rozszerzeniu agenta IaaS programu SQL Server. Bieżące obrazy galerii maszyn wirtualnych SQL domyślnie dodają to rozszerzenie. Aby uzyskać więcej informacji, zobacz [ROZSZERZENIE AGENTA PROGRAMU SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia

W poniższej tabeli opisano opcje, które można skonfigurować dla automatycznej kopii zapasowej. Kroki konfiguracji rzeczywistej różnią się w zależności od tego, czy używasz poleceń azure portal lub programu Azure Windows PowerShell.

| Ustawienie | Zakres (domyślnie) | Opis |
| --- | --- | --- |
| **Automatyczna kopia zapasowa** | Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza automatyczną kopię zapasową dla maszyny Wirtualnej platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** | 1-30 dni (30 dni) | Liczba dni do zachowania kopii zapasowej. |
| **Konto magazynu** | Konto magazynu Azure | Konto magazynu platformy Azure do przechowywania plików automatycznej kopii zapasowej w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji do przechowywania wszystkich plików kopii zapasowych. Konwencja nazewnictwa plików kopii zapasowej zawiera datę, godzinę i nazwę komputera. |
| **Szyfrowania** | Włącz/Wyłącz (wyłączone) | Włącza lub wyłącza szyfrowanie. Gdy szyfrowanie jest włączone, certyfikaty używane do przywracania kopii zapasowej `automaticbackup` znajdują się na określonym koncie magazynu w tym samym kontenerze przy użyciu tej samej konwencji nazewnictwa. Jeśli hasło ulegnie zmianie, zostanie wygenerowany nowy certyfikat przy tym haśle, ale stary certyfikat pozostaje do przywrócenia wcześniejszych kopii zapasowych. |
| **Hasło** | Tekst hasła | Hasło kluczy szyfrowania. Jest to wymagane tylko wtedy, gdy szyfrowanie jest włączone. Aby przywrócić zaszyfrowaną kopię zapasową, musisz mieć poprawne hasło i powiązany certyfikat, który był używany w momencie wykonywania kopii zapasowej. |


## <a name="configure-new-vms"></a>Konfigurowanie nowych maszyn wirtualnych

Użyj portalu Azure, aby skonfigurować automatyczną kopię zapasową podczas tworzenia nowej maszyny wirtualnej programu SQL Server 2014 w modelu wdrażania Menedżera zasobów.

Na karcie **Ustawienia programu SQL Server** przewiń w dół do pozycji **Automatyczna kopia zapasowa** i wybierz pozycję **Włącz**. Poniższy zrzut ekranu portalu platformy Azure przedstawia ustawienia **automatycznej kopii zapasowej SQL.**

![Konfiguracja zautomatyzowanej kopii zapasowej SQL w witrynie Azure portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurowanie istniejących maszyn wirtualnych

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

W przypadku istniejących maszyn wirtualnych programu SQL Server można włączyć i wyłączyć automatyczne kopie zapasowe, zmienić okres przechowywania, określić konto magazynu i włączyć szyfrowanie z witryny Azure portal. 

Przejdź do [zasobu maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) dla maszyny wirtualnej programu SQL Server 2014, a następnie wybierz pozycję **Kopie zapasowe**. 

![Automatyczna kopia zapasowa SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Po zakończeniu wybierz przycisk **Zastosuj** u dołu strony **Kopie zapasowe,** aby zapisać zmiany.

Jeśli po raz pierwszy włączysz automatyczną kopię zapasową, platforma Azure konfiguruje agenta IaaS programu SQL Server w tle. W tym czasie witryna Azure portal może nie wykazywać, że skonfigurowano automatyczną kopię zapasową. Poczekaj kilka minut na zainstalowanie i skonfigurowanie agenta. Następnie witryna Azure portal będzie odzwierciedlać nowe ustawienia.

> [!NOTE]
> Można również skonfigurować automatyczną kopię zapasową przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [Szablon szybki start platformy Azure dla automatycznej kopii zapasowej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurowanie za pomocą programu PowerShell

Za pomocą programu PowerShell można skonfigurować automatyczną kopię zapasową. Przed rozpoczęciem należy:

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

Jeśli nie jest zainstalowany lub nie można aprowizować, można zainstalować go za pomocą następującego polecenia. Oprócz nazwy maszyny Wirtualnej i grupy zasobów należy również określić region (**$region**), w których znajduje się maszyna wirtualna. Określ typ licencji dla maszyny Wirtualnej programu SQL Server, wybierając między płatnością zgodnie z rzeczywistym i wyjazdem lub przynoś własną licencję za pośrednictwem [usługi Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/) Aby uzyskać więcej informacji na temat licencjonowania, zobacz [model licencjonowania](virtual-machines-windows-sql-ahb.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest jeszcze zainstalowane, zainstalowanie rozszerzenia uruchamia ponownie usługę PROGRAMU SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Weryfikowanie bieżących ustawień

Jeśli włączono automatyczną kopię zapasową podczas inicjowania obsługi administracyjnej, można użyć programu PowerShell, aby sprawdzić bieżącą konfigurację. Uruchom polecenie **Get-AzVMSqlServerExtension** i sprawdź właściwość **AutoBackupSettings:**

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Powinieneś uzyskać dane wyjściowe podobne do następujących:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Jeśli dane wyjściowe pokazuje, że **Włącz** jest **ustawiona**na False , a następnie należy włączyć automatyczną kopię zapasową. Dobrą wiadomością jest to, że można włączyć i skonfigurować automatyczną kopię zapasową w ten sam sposób. Zobacz następną sekcję, aby uzyskać te informacje.

> [!NOTE] 
> Jeśli sprawdzisz ustawienia natychmiast po dokonaniu zmiany, możliwe jest, że wrócisz stare wartości konfiguracji. Zaczekaj kilka minut i ponownie sprawdź ustawienia, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup"></a>Konfigurowanie automatycznej kopii zapasowej
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

Następnie użyj polecenia **New-AzVMSqlServerAutoBackupConfig,** aby włączyć i skonfigurować ustawienia automatycznej kopii zapasowej do przechowywania kopii zapasowych na koncie magazynu platformy Azure. W tym przykładzie kopie zapasowe są zachowywane przez 10 dni. Drugie polecenie **Set-AzVMMSqlServerExtension**aktualizuje określoną maszynę wirtualną platformy Azure za pomocą tych ustawień.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Zainstalowanie i skonfigurowanie agenta IaaS programu SQL Server może potrwać kilka minut.

> [!NOTE]
> Istnieją inne ustawienia **dla New-AzVMSqlServerAutoBackupConfig,** które mają zastosowanie tylko do programu SQL Server 2016 i automatycznej kopii zapasowej w wersji 2. Program SQL Server 2014 nie obsługuje następujących ustawień: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**i **LogBackupFrequencyInMinutes**. Jeśli spróbujesz skonfigurować te ustawienia na maszynie wirtualnej programu SQL Server 2014, nie występuje żaden błąd, ale ustawienia nie zostaną zastosowane. Jeśli chcesz użyć tych ustawień na maszynie wirtualnej programu SQL Server 2016, zobacz [Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md).

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt, aby przekazać parametr **EnableEncryption** wraz z hasłem (bezpiecznym ciągiem) dla **parametru CertificatePassword.** Poniższy skrypt włącza ustawienia automatycznej kopii zapasowej w poprzednim przykładzie i dodaje szyfrowanie.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorowanie

Aby monitorować automatyczną kopię zapasową w programie SQL Server 2014, dostępne są dwie główne opcje. Ponieważ automatyczna kopia zapasowa korzysta z funkcji zarządzanej kopii zapasowej programu SQL Server, te same techniki monitorowania dotyczą obu.

Najpierw możesz sondować stan, dzwoniąc do [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub kwerendy [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tabela wartość funkcji.

> [!NOTE]
> Schemat zarządzanej kopii zapasowej w programie SQL Server 2014 to **msdb.smart_admin**. W programie SQL Server 2016 zmieniono to na **msdb.managed_backup**, a tematy referencyjne używają tego nowszego schematu. Jednak w przypadku programu SQL Server 2014 należy nadal używać schematu **smart_admin** dla wszystkich obiektów zarządzanej kopii zapasowej.

Inną opcją jest skorzystanie z wbudowanej funkcji Poczty bazy danych dla powiadomień.

1. Wywołanie procedury składowanej [msdb.smart_admin.sp_set_parameter,](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) aby przypisać adres e-mail do parametru **SSMBackup2WANotificationEmailIds.** 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny Wirtualnej platformy Azure.
1. Konfigurowanie poczty bazy danych za pomocą serwera SMTP i nazwy użytkownika. Poczcie bazy danych można skonfigurować w programie SQL Server Management Studio lub za pomocą poleceń Transact-SQL. Aby uzyskać więcej informacji, zobacz [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Skonfiguruj program SQL Server Agent do używania poczty bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy port SMTP jest dozwolony zarówno za pośrednictwem lokalnej zapory maszyny Wirtualnej, jak i sieciowej grupy zabezpieczeń maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

Automatyczna kopia zapasowa konfiguruje zarządzaną kopię zapasową na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [przejrzeć dokumentację managed backup na sql server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Dodatkowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania programu SQL Server można znaleźć w następującym artykule: [Kopia zapasowa i przywracanie dla programu SQL Server w programie Azure Virtual Machines.](virtual-machines-windows-sql-backup-recovery.md)

Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [Rozszerzenie agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](virtual-machines-windows-sql-server-iaas-overview.md)
