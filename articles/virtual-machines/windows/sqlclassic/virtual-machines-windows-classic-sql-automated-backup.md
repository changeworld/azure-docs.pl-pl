---
title: Automatyczna kopia zapasowa dla maszyn wirtualnych programu SQL Server (klasyczny) | Dokumenty firmy Microsoft
description: 'W tym artykule wyjaśniono funkcję automatycznej kopii zapasowej dla programu SQL Server działającej w usłudze Azure Virtual Machines przy użyciu Menedżera zasobów. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978095"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczna kopia zapasowa dla programu SQL Server w maszynach wirtualnych platformy Azure (klasyczny)
> [!div class="op_single_selector"]
> * [Menedżer zasobów](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-backup.md)
> 
> 

Automatyczna kopia zapasowa automatycznie konfiguruje [zarządzaną kopię zapasową](https://msdn.microsoft.com/library/dn449496.aspx) na platformie Microsoft Azure dla wszystkich istniejących i nowych baz danych na maszynie Wirtualnej Platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. Dzięki temu można skonfigurować regularne kopie zapasowe bazy danych, które wykorzystują trwałe magazynu obiektów blob platformy Azure. Automatyczna kopia zapasowa zależy od [rozszerzenia agenta IaaS programu SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję Menedżera zasobów tego artykułu, zobacz [Automatyczna kopia zapasowa dla programu SQL Server w Menedżerze zasobów maszyn wirtualnych platformy Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć automatycznej kopii zapasowej, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersja/wersja programu SQL Server:**

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatyczna kopia zapasowa dla programu SQL Server 2016 jest obsługiwana przez maszyny wirtualne Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [Automatyczna kopia zapasowa w wersji 2 dla maszyn wirtualnych platformy Azure SQL Server 2016 (Menedżer zasobów)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfiguracja bazy danych**:

* Docelowe bazy danych muszą używać pełnego modelu odzyskiwania.

**Program Azure PowerShell:**

* [Zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).

**Rozszerzenie IaaS programu SQL Server:**

* [Zainstaluj rozszerzenie IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować dla automatycznej kopii zapasowej. W przypadku klasycznych maszyn wirtualnych do skonfigurowania tych ustawień należy użyć programu PowerShell.

| Ustawienie | Zakres (domyślnie) | Opis |
| --- | --- | --- |
| **Automatyczna kopia zapasowa** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza automatyczną kopię zapasową dla maszyny Wirtualnej platformy Azure z systemem SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** |1-30 dni (30 dni) |Liczba dni do zachowania kopii zapasowej. |
| **Konto magazynu** |Konto magazynu platformy Azure (konto magazynu utworzone dla określonej maszyny Wirtualnej) |Konto magazynu platformy Azure do przechowywania plików automatycznej kopii zapasowej w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji do przechowywania wszystkich plików kopii zapasowych. Konwencja nazewnictwa plików kopii zapasowej zawiera datę, godzinę i nazwę komputera. |
| **Szyfrowania** |Włącz/Wyłącz (wyłączone) |Włącza lub wyłącza szyfrowanie. Gdy szyfrowanie jest włączone, certyfikaty używane do przywracania kopii zapasowej znajdują się na określonym koncie magazynu w tym samym kontenerze automaticbackup przy użyciu tej samej konwencji nazewnictwa. Jeśli hasło ulegnie zmianie, zostanie wygenerowany nowy certyfikat przy tym haśle, ale stary certyfikat pozostaje do przywrócenia wcześniejszych kopii zapasowych. |
| **Hasło** |Tekst hasła (brak) |Hasło kluczy szyfrowania. Jest to wymagane tylko wtedy, gdy szyfrowanie jest włączone. Aby przywrócić zaszyfrowaną kopię zapasową, musisz mieć poprawne hasło i powiązany certyfikat, który był używany w momencie wykonywania kopii zapasowej. |
| **Bazy danych systemu tworzenia kopii zapasowych** | Włącz/Wyłącz (wyłączone) | Wykonywanie pełnych kopii zapasowych wzorców, modeli i usług MSDB |
| **Konfigurowanie harmonogramu tworzenia kopii zapasowych** | Ręczny/zautomatyzowany (zautomatyzowany) | Wybierz **opcję Automatyczne,** aby automatycznie wykonać pełne i dziennikowe kopie zapasowe na podstawie wzrostu liczby dzienników. Wybierz **opcję Ręcznie,** aby określić harmonogram pełnych i rejestrowanych kopii zapasowych. |

## <a name="configuration-with-powershell"></a>Konfiguracja z programem PowerShell
W poniższym przykładzie programu PowerShell automatyczna kopia zapasowa jest skonfigurowana dla istniejącej maszyny wirtualnej programu SQL Server 2014. Polecenie **New-AzureVMSqlServerAutoBackupConfig** konfiguruje ustawienia automatycznej kopii zapasowej do przechowywania kopii zapasowych na koncie magazynu platformy Azure określonym przez zmienną $storageaccount. Te kopie zapasowe będą przechowywane przez 10 dni. Polecenie **Set-AzureVMSqlServerExtension** aktualizuje określoną maszynę wirtualną platformy Azure przy tych ustawieniach.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Zainstalowanie i skonfigurowanie agenta IaaS programu SQL Server może potrwać kilka minut.

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt, aby przekazać parametr EnableEncryption wraz z hasłem (bezpiecznym ciągiem) dla parametru CertificatePassword. Poniższy skrypt włącza ustawienia automatycznej kopii zapasowej w poprzednim przykładzie i dodaje szyfrowanie.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Aby wyłączyć automatyczną kopię zapasową, uruchom ten sam skrypt bez parametru **-Enable** do **pliku New-AzureVMSqlServerAutoBackupConfig**. Podobnie jak w przypadku instalacji, wyłączenie automatycznej kopii zapasowej może potrwać kilka minut.

> [!NOTE]
> Wyłączenie i odinstalowanie programu SQL Server IaaS Agent nie powoduje usunięcia wcześniej skonfigurowanych ustawień zarządzanej kopii zapasowej. Przed wyłączeniem lub odinstalowaniem agenta IaaS programu SQL Server należy wyłączyć automatyczną kopię zapasową.
> 
> 

## <a name="next-steps"></a>Następne kroki
Automatyczna kopia zapasowa konfiguruje zarządzaną kopię zapasową na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [przejrzeć dokumentację managed backup,](https://msdn.microsoft.com/library/dn449496.aspx) aby zrozumieć zachowanie i implikacje.

Dodatkowe wskazówki dotyczące tworzenia kopii zapasowych i przywracania programu SQL Server można znaleźć w następującym temacie: [Kopia zapasowa i przywracanie dla programu SQL Server w programie Azure Virtual Machines.](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)

Aby uzyskać informacje o innych dostępnych zadaniach automatyzacji, zobacz [Rozszerzenie agenta programu SQL Server IaaS](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [omówienie programu SQL Server na maszynach wirtualnych platformy Azure.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

