---
title: Zautomatyzowane tworzenie kopii zapasowej programu SQL Server 2014 maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono funkcja automatycznych kopii zapasowych dla programu SQL Server 2014 maszyn wirtualnych działających na platformie Azure. Ten artykuł stanowi specyficzne dla maszyn wirtualnych przy użyciu usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 99439c2b6bd4fdd271dda7a49850c5b6f44330b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66165591"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Zautomatyzowane tworzenie kopii zapasowej dla maszyn wirtualnych programu SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Zautomatyzowane tworzenie kopii zapasowej automatycznie konfiguruje [zarządzanej kopii zapasowej programu Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) dla wszystkich istniejących i nowych baz danych na Maszynie wirtualnej platformy Azure z programu SQL Server 2014 Standard lub Enterprise. Dzięki temu można skonfigurować kopie zapasowe zwykłej bazy danych, które wykorzystują trwałe usługi Azure blob storage. Zautomatyzowane tworzenie kopii zapasowej jest zależna od [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z automatycznych kopii zapasowych, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Wersją programu SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Zautomatyzowane działa kopii zapasowej z programu SQL Server 2014. Jeśli używasz programu SQL Server 2016/2017 można użyć v2 automatyczne kopie zapasowe do tworzenia kopii zapasowych baz danych. Aby uzyskać więcej informacji, zobacz [v2 automatyczne kopie zapasowe usługi SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Konfiguracja bazy danych**:

- Docelowa baza danych musi używać modelu odzyskiwania pełnego. Aby uzyskać więcej informacji dotyczących wpływu modelu odzyskiwania pełnego na tworzenie kopii zapasowych, zobacz [kopii zapasowych w ramach pełnego modelu odzyskiwania](https://technet.microsoft.com/library/ms190217.aspx).
- Docelowa baza danych musi być w domyślnym wystąpieniu programu SQL Server. Rozszerzenie IaaS programu SQL Server nie obsługuje wystąpień nazwanych.

> [!NOTE]
> Zautomatyzowane tworzenie kopii zapasowej opiera się na rozszerzenie agenta IaaS programu SQL Server. Bieżący obrazy galerii maszyn wirtualnych SQL dodać to rozszerzenie, domyślnie. Aby uzyskać więcej informacji, zobacz [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia

W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego tworzenia kopii zapasowych. Kroki rzeczywistą konfigurację się różnić w zależności od tego, czy korzystasz z witryny Azure portal lub poleceń programu Windows Azure PowerShell.

| Ustawienie | Zakres (ustawienie domyślne) | Opis |
| --- | --- | --- |
| **Automatyczne kopie zapasowe** | Włącz/Wyłącz (wyłączony) | Włącza lub wyłącza automatyczne kopie zapasowe maszyny wirtualnej platformy Azure z programu SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** | 1 – 30 dni (30 dni) | Liczba dni przechowywania kopii zapasowej. |
| **Konto magazynu** | Konto magazynu Azure | Konto magazynu platformy Azure do przechowywania automatycznych kopii zapasowych plików w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji, aby zapisać wszystkie pliki kopii zapasowej. Konwencja nazewnictwa pliku kopii zapasowej zawiera daty, godziny i nazwy komputera. |
| **Szyfrowanie** | Włącz/Wyłącz (wyłączony) | Włącza lub wyłącza funkcję szyfrowania. Gdy jest włączone szyfrowanie, certyfikaty służące do przywrócenia kopii zapasowej znajdują się w podanego konta magazynu w tej samej `automaticbackup` kontenera przy użyciu tej samej konwencji nazewnictwa. Zmiana hasła nowy certyfikat jest generowany przy użyciu tego hasła, ale stary certyfikat pozostanie do przywrócenia poprzednich kopii zapasowych. |
| **Hasło** | Tekst hasła | Hasło kluczy szyfrowania. Jest to tylko wymagane, jeśli szyfrowanie jest włączone. Aby przywrócić szyfrowanej kopii zapasowej, musisz mieć prawidłowe hasło i powiązane certyfikat, który został użyty w tym czasie, na których wykonano kopię zapasową. |

## <a name="configure-in-the-portal"></a>Konfigurowanie w portalu

Aby skonfigurować automatyczne kopie zapasowe, podczas udostępniania lub dla maszyn wirtualnych z istniejącego programu SQL Server 2014, można użyć witryny Azure portal.

## <a name="configure-new-vms"></a>Konfigurowanie nowych maszyn wirtualnych

Użyj witryny Azure portal, aby skonfigurować automatyczne kopie zapasowe, podczas tworzenia nowej maszyny wirtualnej 2014 serwera SQL w modelu wdrażania usługi Resource Manager.

W **ustawień programu SQL Server** okienku wybierz **automatyczne tworzenie kopii zapasowych**. Ilustruje poniższy zrzut ekranu na portalu Azure **automatyczne kopie zapasowe SQL** ustawienia.

![Konfiguracja SQL automatyczne kopie zapasowe w witrynie Azure portal](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Konfigurowanie istniejących maszyn wirtualnych

W przypadku istniejących maszyn wirtualnych programu SQL Server należy wybrać maszyny wirtualnej programu SQL Server. Następnie wybierz pozycję **konfiguracji programu SQL Server** sekcji maszyna wirtualna **ustawienia**.

![SQL automatyczne kopie zapasowe dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

W **konfiguracji programu SQL Server** okienku kliknij **Edytuj** przycisku w sekcji automatycznych kopii zapasowych.

![Skonfiguruj automatyczne kopie zapasowe SQL dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Po zakończeniu kliknij przycisk **OK** przycisk w dolnej części **konfiguracji programu SQL Server** ustawienia, aby zapisać zmiany.

Jeśli włączasz automatyczne kopie zapasowe po raz pierwszy, Azure konfiguruje agenta IaaS programu SQL Server w tle. W tym czasie witryny Azure portal może nie pokazać, że skonfigurowano automatyczne kopie zapasowe. Poczekaj kilka minut, zanim agent, który ma zostać zainstalowane, skonfigurowane. Po tym witryny Azure portal będzie odzwierciedlać nowych ustawień.

> [!NOTE]
> Można również skonfigurować automatyczne kopie zapasowe przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablonu szybkiego startu platformy Azure do automatycznego tworzenia kopii zapasowych](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurowanie przy użyciu programu PowerShell

Aby skonfigurować automatyczne kopie zapasowe, można użyć programu PowerShell. Przed rozpoczęciem należy:

- [Pobierz i zainstaluj najnowszą wersję programu Azure PowerShell](https://aka.ms/webpi-azps).
- Otwórz program Windows PowerShell i skojarzyć go z Twojego konta za pomocą **Connect AzAccount** polecenia.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Zainstaluj rozszerzenie SQL IaaS
Jeśli zainicjowano obsługę administracyjną maszyny wirtualnej programu SQL Server w witrynie Azure portal, rozszerzenie IaaS programu SQL Server powinny być już zainstalowane. Można określić, czy jest on zainstalowany dla maszyny Wirtualnej przez wywołanie metody **Get-AzVM** polecenia i sprawdzając **rozszerzenia** właściwości.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Jeśli zainstalowano rozszerzenie agenta IaaS programu SQL Server, powinien być widoczny czy jest ono wyświetlane jako "SqlIaaSAgent" lub "SQLIaaSExtension". **ProvisioningState** dla rozszerzenia powinien również wyświetlać "Powodzenie".

Jeśli nie jest zainstalowana lub nie może być obsługiwana, możesz zainstalować go za pomocą następującego polecenia. Oprócz maszyny Wirtualnej nazwy i grupy zasobów, należy także określić region (**$region**) maszyny Wirtualnej znajdujący się w.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest już zainstalowany, instalowania rozszerzenia powoduje ponowne uruchomienie usługi programu SQL Server.

### <a id="verifysettings"></a> Sprawdź bieżące ustawienia

Jeśli włączono automatyczne tworzenie kopii zapasowych podczas inicjowania obsługi, można użyć programu PowerShell do sprawdzania bieżącej konfiguracji. Uruchom **Get AzVMSqlServerExtension** polecenie i zbadaj **AutoBackupSettings** właściwości:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Otrzymasz dane wyjściowe podobne do następujących:

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

Jeśli dane wyjściowe wskazuje, że **Włącz** jest ustawiona na **False**, a następnie należy włączyć automatyczne tworzenie kopii zapasowych. Dobra wiadomość jest taka, można włączyć i skonfigurować automatyczne kopie zapasowe w taki sam sposób. Zobacz następną sekcję, aby uzyskać te informacje.

> [!NOTE] 
> Jeśli natychmiast po wprowadzeniu zmian możesz sprawdzić ustawienia, istnieje możliwość, że wystąpi ponownie stare wartości konfiguracji. Poczekaj kilka minut i sprawdź ustawienia ponownie, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup"></a>Skonfiguruj automatyczne kopie zapasowe
Można użyć programu PowerShell, aby umożliwić automatyczne kopie zapasowe również zmodyfikować jego konfigurację i zachowanie w dowolnym momencie.

Najpierw wybierz lub Utwórz konto magazynu dla plików kopii zapasowej. Poniższy skrypt wybiera konto magazynu lub utworzenie go, jeśli nie istnieje.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Zautomatyzowane tworzenie kopii zapasowej nie obsługuje przechowywania kopii zapasowych w usłudze premium storage, ale może potrwać kopie zapasowe z dysków maszyn wirtualnych, które używają usługi Premium Storage.

Następnie użyj **New AzVMSqlServerAutoBackupConfig** polecenie, aby włączyć i skonfigurować ustawienia automatycznych kopii zapasowych w celu przechowywania kopii zapasowych w ramach konta usługi Azure storage. W tym przykładzie kopie zapasowe są przechowywane przez 10 dni. Drugie polecenie **AzVMSqlServerExtension zestaw**, aktualizuje określoną maszynę Wirtualną platformy Azure przy użyciu tych ustawień.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta IaaS programu SQL Server.

> [!NOTE]
> Istnieją inne ustawienia **New AzVMSqlServerAutoBackupConfig** dotyczą tylko programu SQL Server 2016 i automatyczne kopie zapasowe v2. Program SQL Server 2014 obsługuje następujące ustawienia: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, i **LogBackupFrequencyInMinutes**. Jeśli spróbujesz skonfigurować te ustawienia na maszynie wirtualnej programu SQL Server 2014, nie ma błędów, ale nie zostać zastosowane ustawienia. Jeśli chcesz użyć tych ustawień na maszynie wirtualnej programu SQL Server 2016, zobacz [v2 automatyczne kopie zapasowe usługi SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Aby włączyć szyfrowanie, należy zmodyfikować poprzedni skrypt do przekazania **EnableEncryption** parametru wraz z hasła (bezpieczny ciąg) dla **CertificatePassword** parametru. Poniższy skrypt umożliwia ustawienia automatycznych kopii zapasowych w poprzednim przykładzie i dodaje szyfrowania.

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

Aby upewnić się, zostaną zastosowane ustawienia [Zweryfikuj konfigurację automatycznych kopii zapasowych](#verifysettings).

### <a name="disable-automated-backup"></a>Wyłącz automatyczne tworzenie kopii zapasowych

Aby wyłączyć automatyczne kopie zapasowe, uruchom ten sam skrypt bez **-Włącz** parametr **New AzVMSqlServerAutoBackupConfig** polecenia. Brak **-Włącz** parametru sygnalizuje polecenie, aby wyłączyć tę funkcję. Podobnie jak w przypadku instalacji, może potrwać kilka minut, aby wyłączyć automatyczne kopie zapasowe.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Przykładowy skrypt

Poniższy skrypt zawiera zbiór zmiennych, które można dostosować, aby włączyć i skonfigurować automatyczne kopie zapasowe dla maszyny Wirtualnej. W Twoim przypadku może być konieczne dostosowanie skrypt, w zależności od wymagań. Na przykład będzie trzeba wprowadzić zmiany, aby wyłączyć tworzenie kopii zapasowych baz danych, systemu lub włączyć szyfrowanie.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

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

Aby monitorować automatyczne kopie zapasowe programu SQL Server 2014, masz dwie główne opcje. Ponieważ automatyczne kopie zapasowe używa funkcji zarządzanej kopii zapasowej serwera SQL, te same techniki monitorowania mają zastosowanie do obu.

Po pierwsze można sondować stan przez wywołanie metody [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub zbadać [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) funkcja zwracająca tabelę.

> [!NOTE]
> Schemat dla zarządzanej kopii zapasowej w programie SQL Server 2014 jest **msdb.smart_admin**. W programie SQL Server 2016 to zmienić na **msdb.managed_backup**, i tematy referencyjne używają tego schematu, nowsze. Dla programu SQL Server 2014, musisz nadal korzystać, ale **smart_admin** schematu dla wszystkich obiektów na zarządzanej kopii zapasowej.

Innym rozwiązaniem jest, aby skorzystać z wbudowanych funkcji Poczta bazy danych dla powiadomień.

1. Wywołaj [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) przechowywane procedury, aby przypisać adresu e-mail, aby **SSMBackup2WANotificationEmailIds** parametru. 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny Wirtualnej platformy Azure.
1. Użyj SMTP serwera i nazwę użytkownika, aby skonfigurować pocztę bazy danych. Możesz skonfigurować pocztę bazy danych w SQL Server Management Studio lub za pomocą polecenia języka Transact-SQL. Aby uzyskać więcej informacji, zobacz [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurowanie programu SQL Server Agent Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy SMTP port może zarówno za pośrednictwem zapory lokalnej maszyny Wirtualnej i grupy zabezpieczeń sieci dla maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Zautomatyzowane tworzenie kopii zapasowej konfiguruje zarządzanej kopii zapasowej na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [zapoznaj się z dokumentacją dla zarządzanej kopii zapasowej programu SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Można znaleźć dodatkowe kopii zapasowej i przywracanie wskazówki dotyczące programu SQL Server na maszynach wirtualnych platformy Azure w następującym artykule: [Kopia zapasowa i przywracanie programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-backup-recovery.md).

Aby uzyskać informacje o innych zadaniach automatyzacji dostępności, zobacz [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).
