---
title: Automatyczna usługa Backup SQL Server 2014 maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji automatycznego tworzenia kopii zapasowej dla programu SQL Server 2014 maszyn wirtualnych działających na platformie Azure. Ten artykuł dotyczy maszyn wirtualnych za pomocą Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 43ce94653197933a13830003dd07e5b21be2a585
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatyczne kopie zapasowe maszyn wirtualnych programu SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatyczne kopie zapasowe automatycznie konfiguruje [zarządzanej kopii zapasowej Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) dla wszystkich istniejących i nowych baz danych na maszynie Wirtualnej platformy Azure, programem SQL Server 2014 Standard lub Enterprise. Dzięki temu można skonfigurować kopie zapasowe zwykłej bazy danych, które korzystać z magazynu trwałego obiektów blob platformy Azure. Automatyczne kopie zapasowe jest zależna od [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z automatycznego tworzenia kopii zapasowej, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Wydanie wersji programu SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatyczne działa kopii zapasowej z programu SQL Server 2014. Jeśli używasz programu SQL Server 2016/2017 służy v2 automatycznego tworzenia kopii zapasowej do tworzenia kopii zapasowych baz danych. Aby uzyskać więcej informacji, zobacz [v2 automatyczna usługa Backup SQL Server 2016 maszyn wirtualnych platformy Azure](virtual-machines-windows-sql-automated-backup-v2.md).

**Baza danych konfiguracji**:

- Docelowej bazy danych muszą używać modelu odzyskiwania pełnego. Aby uzyskać więcej informacji dotyczących wpływu modelu odzyskiwania pełnego na wykonywanie kopii zapasowych, zobacz [kopii zapasowej w obszarze pełny Model odzyskiwania](https://technet.microsoft.com/library/ms190217.aspx).
- Docelowymi bazami danych musi być w domyślnym wystąpieniu programu SQL Server. Rozszerzenie IaaS serwera SQL obsługuje tylko nazwane wystąpienia.

> [!NOTE]
> Automatyczne kopie zapasowe wykorzystuje rozszerzenie agenta programu SQL Server IaaS. Bieżący obrazów Galeria maszyny wirtualnej SQL dodać to rozszerzenie domyślnie. Aby uzyskać więcej informacji, zobacz [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia

W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego tworzenia kopii zapasowej. Kroki konfiguracji rzeczywisty zależy od przy użyciu portalu Azure lub poleceń programu PowerShell systemu Windows Azure.

| Ustawienie | Zakres (ustawienie domyślne) | Opis |
| --- | --- | --- |
| **Automatyczne kopie zapasowe** | Włącza/wyłącza (wyłączone) | Włącza lub wyłącza funkcję automatycznego tworzenia kopii zapasowej dla maszyny Wirtualnej platformy Azure, programem SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** | 1 do 30 dni (30 dni) | Liczba dni przechowywania kopii zapasowej. |
| **Konto magazynu** | Konto magazynu Azure | Konto magazynu Azure do przechowywania plików automatycznego tworzenia kopii zapasowej w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji, aby zapisać wszystkie pliki kopii zapasowej. Konwencja nazewnictwa pliku kopii zapasowej obejmuje daty, godziny i nazwy komputera. |
| **Szyfrowanie** | Włącza/wyłącza (wyłączone) | Włącza lub wyłącza funkcję szyfrowania. Po włączeniu szyfrowania certyfikatów służących do przywrócenia kopii zapasowej znajdują się na koncie magazynu określonym w tym samym `automaticbackup` kontenera przy użyciu tej samej konwencji nazewnictwa. Zmiana hasła nowego certyfikatu jest generowana za pomocą tego hasła, ale pozostaje stary certyfikat do przywrócenia poprzedniego kopii zapasowych. |
| **Hasło** | Tekst hasła | Hasło dla kluczy szyfrowania. Jest to tylko wymagane, jeśli jest włączone szyfrowanie. Aby przywrócić zaszyfrowanej kopii zapasowej, musi mieć prawidłowe hasło, a powiązany certyfikat, który został użyty w momencie utworzenia kopii zapasowej. |

## <a name="configure-in-the-portal"></a>Konfigurowanie w portalu

Azure portal umożliwia konfigurowanie automatycznego tworzenia kopii zapasowej, podczas udostępniania lub dla maszyn wirtualnych istniejącego programu SQL Server 2014.

## <a name="configure-new-vms"></a>Skonfiguruj nowe maszyny wirtualne

Skonfiguruj automatyczne kopie zapasowe, podczas tworzenia nowej maszyny wirtualnej 2014 serwera SQL w modelu wdrażania usługi Resource Manager za pomocą portalu Azure.

W **ustawienia programu SQL Server** okienku wybierz **automatyczne kopie zapasowe**. Poniżej przedstawiono zrzut ekranu na portalu Azure **SQL automatyczna usługa Backup** ustawienia.

![Konfiguracja SQL automatyczna usługa Backup w portalu Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Skonfiguruj istniejące maszyny wirtualne

W przypadku istniejących maszyn wirtualnych programu SQL Server należy wybrać maszyny wirtualnej programu SQL Server. Następnie wybierz **konfigurację programu SQL Server** sekcji maszyny wirtualnej **ustawienia**.

![SQL automatyczna usługa Backup dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

W **konfigurację programu SQL Server** okienku, kliknij przycisk **Edytuj** przycisk w sekcji automatycznego tworzenia kopii zapasowej.

![Konfigurowanie kopii zapasowej SQL automatycznego dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Gdy skończysz, kliknij przycisk **OK** przycisk w dolnej części **konfigurację programu SQL Server** ustawienia, aby zapisać zmiany.

Jeśli po raz pierwszy włączasz automatyczna usługa Backup, Azure konfiguruje agenta programu SQL Server IaaS w tle. W tym czasie portalu Azure może nie informować, że skonfigurowano automatycznego tworzenia kopii zapasowej. Poczekaj kilka minut dla agenta, który ma zostać zainstalowany, skonfigurowany. Po utworzeniu tego portalu Azure będzie odzwierciedlać nowe ustawienia.

> [!NOTE]
> Istnieje również możliwość skonfigurowania automatycznego tworzenia kopii zapasowej przy użyciu szablonu. Aby uzyskać więcej informacji, zobacz [szablonu Azure szybkiego startu dla automatyczna usługa Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurowanie przy użyciu programu PowerShell

Można skonfigurować automatyczne tworzenie kopii zapasowych za pomocą programu PowerShell. Przed rozpoczęciem należy:

- [Pobierz i zainstaluj najnowsze programu Azure PowerShell](http://aka.ms/webpi-azps).
- Otwórz program Windows PowerShell i skojarzyć go z Twojego konta z **Connect-AzureRmAccount** polecenia.

### <a name="install-the-sql-iaas-extension"></a>Zainstaluj rozszerzenie IaaS SQL
Jeśli aprowizowanej maszyny wirtualnej programu SQL Server w portalu Azure, rozszerzenie IaaS serwera SQL powinno być już zainstalowane. Można określić, czy jest ona instalowana dla maszyny Wirtualnej przez wywołanie metody **Get-AzureRmVM** polecenia i sprawdzeniu **rozszerzenia** właściwości.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Jeśli zainstalowano rozszerzenia SQL Server IaaS Agent, powinien pojawić się wymienionym "SqlIaaSAgent" lub "SQLIaaSExtension". **ProvisioningState** dla rozszerzenia należy również wyświetlić "Powodzenie".

Jeśli nie jest zainstalowane lub nie można zainicjować obsługi administracyjnej, należy zainstalować go za pomocą następującego polecenia. Oprócz grupy nazwy i zasobów maszyny Wirtualnej, należy także określić region (**$region**) maszyny Wirtualnej znajdujący się w.

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Jeśli rozszerzenie nie jest już zainstalowany, instalowania rozszerzenia uruchamia ponownie usługę SQL Server.

### <a id="verifysettings"></a> Sprawdź bieżące ustawienia

Jeśli włączono automatyczne kopie zapasowe podczas inicjowania obsługi, można sprawdzić bieżącej konfiguracji za pomocą programu PowerShell. Uruchom **Get-AzureRmVMSqlServerExtension** polecenia i sprawdź, czy **AutoBackupSettings** właściwości:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Należy pobrać dane wyjściowe podobne do następujących:

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

Jeśli dane wyjściowe wskazuje, że **włączyć** ma ustawioną wartość **False**, a następnie należy włączyć automatyczne kopie zapasowe. Dobre wieści jest włączone, a następnie skonfiguruj automatyczne kopie zapasowe w taki sam sposób. W następnej sekcji dla tych informacji.

> [!NOTE] 
> Jeśli wybierzesz ustawienia natychmiast po wprowadzeniu zmian jest możliwe, że wystąpi ponownie starych wartości konfiguracji. Poczekaj kilka minut, a następnie sprawdź ustawienia ponownie, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup"></a>Skonfiguruj automatyczne kopie zapasowe
Można włączyć automatycznego tworzenia kopii zapasowej oraz aby zmodyfikować jego konfigurację i zachowanie w dowolnej chwili za pomocą programu PowerShell.

Najpierw wybierz lub Utwórz konto magazynu w przypadku plików kopii zapasowych. Poniższy skrypt wybiera konta magazynu lub jeśli nie istnieje, tworzy go.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Automatyczne kopie zapasowe nie obsługuje przechowywania kopii zapasowych w magazynie premium, ale może potrwać kopii zapasowych z dysków maszyny Wirtualnej, które używają magazyn w warstwie Premium.

Następnie użyj **AzureRmVMSqlServerAutoBackupConfig nowy** polecenie, aby włączyć i skonfigurować ustawienia automatycznego tworzenia kopii zapasowej do przechowywania kopii zapasowych w ramach konta magazynu platformy Azure. W tym przykładzie kopie zapasowe są przechowywane przez 10 dni. Drugie polecenie **AzureRmVMSqlServerExtension zestaw**, aktualizuje określonej maszyny Wirtualnej platformy Azure przy użyciu tych ustawień.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta programu SQL Server IaaS.

> [!NOTE]
> Istnieją inne ustawienia **AzureRmVMSqlServerAutoBackupConfig nowy** dotyczą tylko programu SQL Server 2016 i automatyczna usługa Backup v2. SQL Server 2014 nie obsługuje następujące ustawienia: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**,  **FullBackupStartHour**, **FullBackupWindowInHours**, i **LogBackupFrequencyInMinutes**. Jeśli do konfigurowania tych ustawień na maszynie wirtualnej programu SQL Server 2014, nie było błędu, ale nie zostać zastosowane ustawienia. Jeśli chcesz użyć tych ustawień maszyny wirtualnej programu SQL Server 2016, zobacz [v2 automatyczna usługa Backup SQL Server 2016 maszyn wirtualnych platformy Azure](virtual-machines-windows-sql-automated-backup-v2.md).

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt do przekazania **EnableEncryption** parametru wraz z hasła (bezpieczny ciąg) **CertificatePassword** parametru. Poniższy skrypt umożliwia ustawienia automatycznego tworzenia kopii zapasowej w poprzednim przykładzie i dodaje szyfrowania.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Aby potwierdzić ustawienia są stosowane, [Sprawdź konfigurację automatycznego tworzenia kopii zapasowej](#verifysettings).

### <a name="disable-automated-backup"></a>Wyłącz automatyczne kopie zapasowe

Aby wyłączyć automatyczna usługa Backup, uruchom ten sam skrypt bez **— Włącz** parametr **AzureRmVMSqlServerAutoBackupConfig nowy** polecenia. Brak **-Włącz** parametru sygnały polecenie, aby wyłączyć funkcję. Podobnie jak w przypadku instalacji, może upłynąć kilka minut, aby wyłączyć automatyczne wykonywanie kopii zapasowych.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Przykładowy skrypt

Poniższy skrypt zawiera zestaw zmiennych, które można dostosować, aby włączyć i skonfigurować automatyczna usługa Backup dla maszyny Wirtualnej. W Twoim przypadku może być konieczne dostosowanie skryptu, w zależności od wymagań. Na przykład użytkownik musi wprowadzić zmiany, aby wyłączyć tworzenie kopii zapasowej bazy danych systemu lub włączyć szyfrowanie.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorowanie

Aby monitorować automatycznego tworzenia kopii zapasowej w programie SQL Server 2014, masz dwie główne opcje. Ponieważ automatyczna usługa Backup używa funkcji zarządzanej kopii zapasowej serwera SQL, te same techniki monitorowania dotyczą zarówno.

Po pierwsze można sondować stan wywołując [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub zbadać [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) funkcji zwracającej tabelę.

> [!NOTE]
> Schemat dla zarządzanej kopii zapasowej w programie SQL Server 2014 jest **msdb.smart_admin**. W programie SQL Server 2016 to zmienić na **msdb.managed_backup**, i tematy referencyjne używają tego schematu nowsza. Dla programu SQL Server 2014, nadal należy używać, ale **smart_admin** schematu dla wszystkich obiektów zarządzanej kopii zapasowej.

Innym rozwiązaniem jest skorzystać z wbudowanych funkcji Poczta bazy danych dla powiadomień.

1. Wywołanie [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) przechowywane procedury, aby przypisać adres e-mail, aby **SSMBackup2WANotificationEmailIds** parametru. 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny Wirtualnej platformy Azure.
1. Umożliwia skonfigurowanie bazy danych poczty SMTP serwera i nazwa użytkownika. Poczta bazy danych można skonfigurować w programie SQL Server Management Studio lub za pomocą poleceń języka Transact-SQL. Aby uzyskać więcej informacji, zobacz [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurowanie agenta serwera SQL, aby korzystać z poczty bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy SMTP port jest dozwolony zarówno za pośrednictwem zapory lokalnej maszyny Wirtualnej i grupy zabezpieczeń sieci dla maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

Automatyczne kopie zapasowe konfiguruje zarządzanej kopii zapasowej na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [zapoznaj się z dokumentacją dla zarządzanej kopii zapasowej w programie SQL Server 2014](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Można znaleźć dodatkowe kopii zapasowej i przywracanie wskazówki dotyczące programu SQL Server na maszynach wirtualnych Azure w następującym artykule: [kopii zapasowej i przywracania dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

Informacje o innych zadaniach automatyzacji dostępny, zobacz [rozszerzenia agenta programu SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).
