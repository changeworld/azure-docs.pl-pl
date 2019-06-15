---
title: Automatycznych kopii zapasowych w wersji 2 dla maszyn wirtualnych programu SQL Server 2016/2017 platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono funkcja automatycznych kopii zapasowych dla działającej na platformie Azure maszyn wirtualnych programu SQL Server 2016/2017. Ten artykuł stanowi specyficzne dla maszyn wirtualnych przy użyciu usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d03d4bd86367aa29bbf93062f7cc03f57f4cad83
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075922"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatyczne v2 kopii zapasowych maszyn wirtualnych platformy Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatycznie konfiguruje automatycznych kopii zapasowych v2 [zarządzanej kopii zapasowej programu Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) dla wszystkich istniejących i nowych baz danych na maszynie Wirtualnej platformy Azure, w obsługiwanych wersjach programu SQL Server 2016/2017 Standard, Enterprise lub dewelopera. Dzięki temu można skonfigurować kopie zapasowe zwykłej bazy danych, które wykorzystują trwałe usługi Azure blob storage. Zautomatyzowanych kopii zapasowych v2 jest zależna od [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z automatycznych kopii zapasowych w wersji 2, sprawdź następujące wymagania wstępne:

**System operacyjny**:

- Windows Server 2012 R2
- Windows Server 2016

**Wersją programu SQL Server**:

- SQL Server 2016: Developer, Standard lub Enterprise
- SQL Server 2017: Developer, Standard lub Enterprise

> [!IMPORTANT]
> Zautomatyzowanych kopii zapasowych v2 współpracuje z programem SQL Server 2016 lub nowszego. Jeśli używasz programu SQL Server 2014, można użyć v1 automatyczne kopie zapasowe do tworzenia kopii zapasowych baz danych. Aby uzyskać więcej informacji, zobacz [automatyczne kopie zapasowe usługi SQL Server 2014 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

**Konfiguracja bazy danych**:

- Docelowa baza danych musi używać modelu odzyskiwania pełnego. Aby uzyskać więcej informacji dotyczących wpływu modelu odzyskiwania pełnego na tworzenie kopii zapasowych, zobacz [kopii zapasowych w ramach pełnego modelu odzyskiwania](https://technet.microsoft.com/library/ms190217.aspx).
- Systemowe bazy danych nie trzeba używać modelu odzyskiwania pełnego. Jednak jeśli potrzebujesz kopii zapasowych dziennika mają być pobrane do modelu lub w bazie danych MSDB, należy użyć modelu odzyskiwania pełnego.
- Docelowa baza danych musi znajdować się na obu domyślne wystąpienie programu SQL Server, lub [prawidłowo zainstalowane](virtual-machines-windows-sql-server-iaas-faq.md#administration) nazwanego wystąpienia. 

> [!NOTE]
> Zautomatyzowane tworzenie kopii zapasowej opiera się na **rozszerzenie agenta IaaS programu SQL Server**. Bieżący obrazy galerii maszyn wirtualnych SQL dodać to rozszerzenie, domyślnie. Aby uzyskać więcej informacji, zobacz [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować automatyczne kopie zapasowe w wersji 2. Kroki rzeczywistą konfigurację się różnić w zależności od tego, czy korzystasz z witryny Azure portal lub poleceń programu Windows Azure PowerShell.

### <a name="basic-settings"></a>Ustawienia podstawowe

| Ustawienie | Zakres (ustawienie domyślne) | Opis |
| --- | --- | --- |
| **Automatyczne kopie zapasowe** | Włącz/Wyłącz (wyłączony) | Włącza lub wyłącza automatyczne kopie zapasowe maszyny wirtualnej platformy Azure, uruchamiania programu SQL Server 2016/2017 Developer, Standard lub Enterprise. |
| **Okres przechowywania** | 1 – 30 dni (30 dni) | Liczba dni przechowywania kopii zapasowych. |
| **Konto magazynu** | Konto magazynu Azure | Konto magazynu platformy Azure do przechowywania automatycznych kopii zapasowych plików w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji, aby zapisać wszystkie pliki kopii zapasowej. Konwencja nazewnictwa pliku kopii zapasowej zawiera daty, godziny i identyfikator GUID bazy danych. |
| **Szyfrowanie** |Włącz/Wyłącz (wyłączony) | Włącza lub wyłącza funkcję szyfrowania. Gdy jest włączone szyfrowanie, certyfikaty służące do przywrócenia kopii zapasowej znajdują się w podanego konta magazynu. Używa ona taka sama **automatyczne wykonywanie kopii zapasowej** kontener o tej samej konwencji nazewnictwa. Zmiana hasła nowy certyfikat jest generowany przy użyciu tego hasła, ale stary certyfikat pozostanie do przywrócenia poprzednich kopii zapasowych. |
| **Hasło** |Tekst hasła | Hasło kluczy szyfrowania. To hasło jest tylko wymagane, jeśli szyfrowanie jest włączone. Aby przywrócić szyfrowanej kopii zapasowej, musisz mieć prawidłowe hasło i powiązane certyfikat, który został użyty w tym czasie, na których wykonano kopię zapasową. |

### <a name="advanced-settings"></a>Ustawienia zaawansowane

| Ustawienie | Zakres (ustawienie domyślne) | Opis |
| --- | --- | --- |
| **Kopie zapasowe bazy danych systemu** | Włącz/Wyłącz (wyłączony) | Po włączeniu tej funkcji tworzy kopie zapasowe baz danych systemu: Master, MSDB i modelu. W przypadku baz danych MSDB i Model potwierdzić, że jest w trybie odzyskiwania pełnego Jeśli chcesz, aby kopie zapasowe dziennika, należy podjąć. Kopie zapasowe dziennika nigdy nie są pobierane dla serwera głównego. I żadne kopie zapasowe są wykonywane dla bazy danych TempDB. |
| **Harmonogram tworzenia kopii zapasowych** | Ręczne/automatyczne (automatyczne) | Domyślnie harmonogram tworzenia kopii zapasowych jest ustalany automatycznie na podstawie wzrostu dzienników. Ręczne harmonogram tworzenia kopii zapasowych umożliwia użytkownikowi określenie przedziału czasu dla kopii zapasowych. W tym przypadku kopii zapasowych miejsce wyłącznie z określoną częstotliwością i przedziale czasu określonym danego dnia. |
| **Częstotliwość tworzenia pełnych kopii zapasowych** | Codziennie/co tydzień | Częstotliwość pełnych kopii zapasowych. W obu przypadkach pełne kopie zapasowe rozpocząć podczas następnego okna zaplanowanej godzinie. Po wybraniu cotygodniowych kopii zapasowych może obejmować wiele dni, dopóki wszystkie bazy danych pomyślnie wykonano kopię zapasową. |
| **Czas rozpoczęcia tworzenia pełnych kopii zapasowych** | 00:00 – 23:00 (01:00) | Rozpocznij czas w danym dniu, w którym pełne kopie zapasowe mogą być wykonywane. |
| **Okno czasowe tworzenia pełnych kopii zapasowych** | 1 – 23 godzin (1 godzina) | Czas trwania okna czas w danym dniu, w którym pełne kopie zapasowe mogą być wykonywane. |
| **Częstotliwość wykonywania kopii zapasowych dziennika** | 5 – 60 minut (60 minut) | Częstotliwość wykonywania kopii zapasowych dziennika. |

## <a name="understanding-full-backup-frequency"></a>Informacje o częstotliwości tworzenia pełnej kopii zapasowej
Należy zrozumieć różnicę między dzienne i tygodniowe pełne kopie zapasowe. Należy wziąć pod uwagę następujące dwa przykładowe scenariusze.

### <a name="scenario-1-weekly-backups"></a>Scenariusz 1: Cotygodniowe kopie zapasowe
Masz maszynę Wirtualną serwera SQL, która zawiera szereg dużych baz danych.

W poniedziałek należy włączyć automatyczne kopie zapasowe w wersji 2 z następującymi ustawieniami:

- Harmonogram tworzenia kopii zapasowych: **Ręcznie**
- Częstotliwość tworzenia pełnych kopii zapasowych: **Co tydzień**
- Godzina rozpoczęcia tworzenia pełnych kopii zapasowych: **01:00**
- Okno czasowe tworzenia pełnych kopii zapasowych: **1 godzina**

Oznacza to, że następnym dostępnym oknie kopii zapasowej jest wtorek o 1: 00 1 godziny. W tym czasie automatyczne kopie zapasowe rozpoczyna wykonywanie kopii zapasowych baz danych jednego naraz. W tym scenariuszu baz danych są wystarczająco duże, że pełne kopie zapasowe ukończenia pierwsze kilka baz danych. Jednak po godzinie nie wszystkie bazy danych utworzone kopie zapasowe.

W takim przypadku automatyczne kopie zapasowe rozpoczyna wykonywanie kopii zapasowych pozostałe bazy danych następnego dnia, środę o godzinie 1: 00, opłata za godzinę. Jeśli nie wszystkie bazy danych utworzone kopie zapasowe w tym czasie, próbuje ponownie następnego dnia, w tym samym czasie. Ten proces jest kontynuowany, dopóki wszystkie bazy danych ma zostać pomyślnie utworzyła kopię zapasową.

Po jego osiągnięciu wtorek rozpoczyna się automatyczne kopie zapasowe, ponownie tworzenie kopii zapasowych wszystkich baz danych.

W tym scenariuszu pokazano, że automatyczne kopie zapasowe działa tylko w określonym przedziale czasu, a każda baza danych jest kopia zapasowa jest tworzona raz w tygodniu. To pokazuje, że istnieje możliwość tworzenia kopii zapasowych na kilka dni w przypadku, gdy nie jest możliwe do wykonania wszystkich kopii zapasowych w jednym dniu.

### <a name="scenario-2-daily-backups"></a>Scenariusz 2: Codzienne wykonywanie kopii zapasowych
Masz maszynę Wirtualną serwera SQL, która zawiera szereg dużych baz danych.

W poniedziałek należy włączyć automatyczne kopie zapasowe w wersji 2 z następującymi ustawieniami:

- Harmonogram tworzenia kopii zapasowych: Ręcznie
- Częstotliwość tworzenia pełnych kopii zapasowych: Codziennie
- Godzina rozpoczęcia tworzenia pełnych kopii zapasowych: 22:00
- Okno czasowe tworzenia pełnych kopii zapasowych: 6 godz.

Oznacza to, że następnym dostępnym oknie kopii zapasowej jest poniedziałek o 22: 00 przez 6 godzin. W tym czasie automatyczne kopie zapasowe rozpoczyna wykonywanie kopii zapasowych baz danych jednego naraz.

Następnie we wtorek od 10 do 6 godzin, pełne kopie zapasowe wszystkich baz danych uruchom ponownie.

> [!IMPORTANT]
> Podczas planowania codzienne kopie zapasowe, zaleca się zaplanowanie przedział czasu szerokie, aby upewnić się, że wszystkie bazy danych można kopie zapasowe w tej chwili. Jest to szczególnie ważne w przypadku, gdy masz dużą ilość danych, aby utworzyć kopię zapasową.

## <a name="configure-in-the-portal"></a>Konfigurowanie w portalu

Za pomocą witryny Azure portal skonfigurować automatyczne kopie zapasowe w wersji 2, podczas udostępniania lub dla istniejących maszyn wirtualnych programu SQL Server 2016/2017.

## <a name="configure-for-new-vms"></a>Konfigurowanie dla nowych maszyn wirtualnych

Użyj witryny Azure portal, aby skonfigurować automatyczne kopie zapasowe w wersji 2, podczas tworzenia nowego programu SQL Server 2016 lub 2017 maszyny wirtualnej w modelu wdrażania usługi Resource Manager.

W **ustawień programu SQL Server** zaznacz **Włącz** w obszarze **automatyczne tworzenie kopii zapasowych**. Ilustruje poniższy zrzut ekranu na portalu Azure **automatyczne kopie zapasowe SQL** ustawienia.

![Konfiguracja SQL automatyczne kopie zapasowe w witrynie Azure portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Zautomatyzowanych kopii zapasowych v2 jest domyślnie wyłączona.

## <a name="configure-existing-vms"></a>Konfigurowanie istniejących maszyn wirtualnych

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Dla istniejących maszyn wirtualnych programu SQL Server, przejdź do [zasobów maszyn wirtualnych SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) , a następnie wybierz **kopie zapasowe** do konfigurowania zautomatyzowanych kopii zapasowych.

![SQL automatyczne kopie zapasowe dla istniejących maszyn wirtualnych](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Po zakończeniu kliknij przycisk **Zastosuj** przycisk w dolnej części **kopie zapasowe** strony ustawień, aby zapisać zmiany.

Jeśli włączasz automatyczne kopie zapasowe po raz pierwszy, Azure konfiguruje agenta IaaS programu SQL Server w tle. W tym czasie witryny Azure portal może nie pokazać, że skonfigurowano automatyczne kopie zapasowe. Poczekaj kilka minut, zanim agent, który ma zostać zainstalowane, skonfigurowane. Po tym witryny Azure portal będzie odzwierciedlać nowych ustawień.

## <a name="configure-with-powershell"></a>Konfigurowanie przy użyciu programu PowerShell

Aby skonfigurować automatyczne kopie zapasowe w wersji 2, można użyć programu PowerShell. Przed rozpoczęciem należy:

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

Jeśli nie jest zainstalowana lub nie może być obsługiwana, możesz zainstalować go za pomocą następującego polecenia. Oprócz maszyny Wirtualnej nazwy i grupy zasobów, należy także określić region ( **$region**) maszyny Wirtualnej znajdujący się w.

```powershell
$region = “EASTUS2”
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Sprawdź bieżące ustawienia
Jeśli włączono automatyczne tworzenie kopii zapasowych podczas inicjowania obsługi, można użyć programu PowerShell do sprawdzania bieżącej konfiguracji. Uruchom **Get AzVMSqlServerExtension** polecenie i zbadaj **AutoBackupSettings** właściwości:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Otrzymasz dane wyjściowe podobne do następujących:

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

Jeśli dane wyjściowe wskazuje, że **Włącz** jest ustawiona na **False**, a następnie należy włączyć automatyczne tworzenie kopii zapasowych. Dobra wiadomość jest taka, można włączyć i skonfigurować automatyczne kopie zapasowe w taki sam sposób. Zobacz następną sekcję, aby uzyskać te informacje.

> [!NOTE] 
> Jeśli natychmiast po wprowadzeniu zmian możesz sprawdzić ustawienia, istnieje możliwość, że wystąpi ponownie stare wartości konfiguracji. Poczekaj kilka minut i sprawdź ustawienia ponownie, aby upewnić się, że zmiany zostały zastosowane.

### <a name="configure-automated-backup-v2"></a>Konfigurowanie automatycznych kopii zapasowych v2
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

Następnie użyj **New AzVMSqlServerAutoBackupConfig** polecenie, aby włączyć i skonfigurować ustawienia automatycznych kopii zapasowych w wersji 2, do przechowywania kopii zapasowych w ramach konta usługi Azure storage. W tym przykładzie kopie zapasowe są ustawiane będą przechowywane w ciągu 10 dni. Kopie zapasowe bazy danych systemu są włączone. Pełne kopie zapasowe są planowane dla cotygodniowych z przedział czasu, zaczynając od 20:00, przez dwie godziny. Kopie zapasowe dziennika zaplanowano co 30 minut. Drugie polecenie **AzVMSqlServerExtension zestaw**, aktualizuje określoną maszynę Wirtualną platformy Azure przy użyciu tych ustawień.

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

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta IaaS programu SQL Server. 

Aby włączyć szyfrowanie, należy zmodyfikować poprzedni skrypt do przekazania **EnableEncryption** parametru wraz z hasła (bezpieczny ciąg) dla **CertificatePassword** parametru. Poniższy skrypt umożliwia ustawienia automatycznych kopii zapasowych w poprzednim przykładzie i dodaje szyfrowania.

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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitorowanie

Aby monitorować automatyczne kopie zapasowe w programie SQL Server 2016/2017, masz dwie główne opcje. Ponieważ automatyczne kopie zapasowe używa funkcji zarządzanej kopii zapasowej serwera SQL, te same techniki monitorowania mają zastosowanie do obu.

Po pierwsze można sondować stan przez wywołanie metody [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Lub zbadać [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) funkcji zwracającej tabelę.

Innym rozwiązaniem jest, aby skorzystać z wbudowanych funkcji Poczta bazy danych dla powiadomień.

1. Wywołaj [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) przechowywane procedury, aby przypisać adresu e-mail, aby **SSMBackup2WANotificationEmailIds** parametru. 
1. Włącz [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) do wysyłania wiadomości e-mail z maszyny Wirtualnej platformy Azure.
1. Użyj SMTP serwera i nazwę użytkownika, aby skonfigurować pocztę bazy danych. Możesz skonfigurować pocztę bazy danych w SQL Server Management Studio lub za pomocą polecenia języka Transact-SQL. Aby uzyskać więcej informacji, zobacz [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurowanie programu SQL Server Agent Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Sprawdź, czy SMTP port może zarówno za pośrednictwem zapory lokalnej maszyny Wirtualnej i grupy zabezpieczeń sieci dla maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Zautomatyzowanych kopii zapasowych v2 konfiguruje zarządzanej kopii zapasowej na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [zapoznaj się z dokumentacją dla zarządzanej kopii zapasowej](https://msdn.microsoft.com/library/dn449496.aspx) , aby zrozumieć zachowania i skutków.

Można znaleźć dodatkowe kopii zapasowej i przywracanie wskazówki dotyczące programu SQL Server na maszynach wirtualnych platformy Azure w następującym artykule: [Kopia zapasowa i przywracanie programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-backup-recovery.md).

Aby uzyskać informacje o innych zadaniach automatyzacji dostępności, zobacz [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md).

