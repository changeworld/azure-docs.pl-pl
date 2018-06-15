---
title: Automatyczna usługa Backup dla maszyn wirtualnych serwera SQL (klasyczne) | Dokumentacja firmy Microsoft
description: 'Zawiera opis funkcji automatycznego tworzenia kopii zapasowej dla programu SQL Server uruchomionego w maszynach wirtualnych platformy Azure za pomocą Menedżera zasobów. '
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: jroth
ms.openlocfilehash: 3bca1c6c357527a32de499ac9207b1bb734dad7b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187127"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatyczne kopie zapasowe programu SQL Server na maszynach wirtualnych platformy Azure (klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-backup.md)
> 
> 

Automatyczne kopie zapasowe automatycznie konfiguruje [zarządzanej kopii zapasowej Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) dla wszystkich istniejących i nowych baz danych na maszynie Wirtualnej platformy Azure, programem SQL Server 2014 Standard lub Enterprise. Dzięki temu można skonfigurować kopie zapasowe zwykłej bazy danych, które korzystać z magazynu trwałego obiektów blob platformy Azure. Automatyczne kopie zapasowe jest zależna od [rozszerzenia agenta programu SQL Server IaaS](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję usługi Resource Manager w tym artykule, zobacz [automatyczna usługa Backup dla programu SQL Server w Menedżerze zasobów maszyn wirtualnych Azure](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z automatycznego tworzenia kopii zapasowej, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wydanie wersji programu SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatyczne kopie zapasowe programu SQL Server 2016 jest obsługiwana na maszynach wirtualnych Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [v2 automatyczna usługa Backup SQL Server 2016 maszyn wirtualnych platformy Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Baza danych konfiguracji**:

* Docelowej bazy danych muszą używać modelu odzyskiwania pełnego.

**Program Azure PowerShell**:

* [Zainstaluj najnowsze poleceń programu PowerShell Azure](/powershell/azure/overview).

**Rozszerzenia programu SQL Server IaaS**:

* [Zainstaluj rozszerzenie IaaS serwera SQL](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego tworzenia kopii zapasowej. Klasycznych maszyn wirtualnych należy skonfigurować te ustawienia za pomocą programu PowerShell.

| Ustawienie | Zakres (ustawienie domyślne) | Opis |
| --- | --- | --- |
| **Automatyczne kopie zapasowe** |Włącza/wyłącza (wyłączone) |Włącza lub wyłącza funkcję automatycznego tworzenia kopii zapasowej dla maszyny Wirtualnej platformy Azure, programem SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** |1 do 30 dni (30 dni) |Liczba dni przechowywania kopii zapasowej. |
| **Konto magazynu** |Konto magazynu Azure (na koncie magazynu utworzonym dla określonej maszyny Wirtualnej) |Konto magazynu Azure do przechowywania plików automatycznego tworzenia kopii zapasowej w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji, aby zapisać wszystkie pliki kopii zapasowej. Konwencja nazewnictwa pliku kopii zapasowej obejmuje daty, godziny i nazwy komputera. |
| **Szyfrowanie** |Włącza/wyłącza (wyłączone) |Włącza lub wyłącza funkcję szyfrowania. Po włączeniu szyfrowania certyfikatów służących do przywrócenia kopii zapasowej znajdują się na koncie magazynu określonym w tym samym kontenerze automaticbackup przy użyciu tej samej konwencji nazewnictwa. Zmiana hasła nowego certyfikatu jest generowana za pomocą tego hasła, ale pozostaje stary certyfikat do przywrócenia poprzedniego kopii zapasowych. |
| **Hasło** |Tekst hasła, (Brak) |Hasło dla kluczy szyfrowania. Jest to tylko wymagane, jeśli jest włączone szyfrowanie. Aby przywrócić zaszyfrowanej kopii zapasowej, musi mieć prawidłowe hasło, a powiązany certyfikat, który został użyty w momencie utworzenia kopii zapasowej. | **Kopia zapasowa systemowych baz danych** | Włącza/wyłącza (wyłączone) | Korzystać z pełnej kopii zapasowych Master, Model i MSDB |
| **Skonfiguruj harmonogram tworzenia kopii zapasowych** | Ręczne/automatycznego (automatycznego) | Wybierz **automatycznego** automatycznie wykonać pełne i kopii zapasowych w oparciu o wzrost dziennika dziennika. Wybierz **ręcznego** można określić harmonogramu dla pełnej i kopii zapasowych dziennika. |

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
W poniższym przykładzie programu PowerShell automatycznego tworzenia kopii zapasowej jest skonfigurowany dla istniejącej maszyny Wirtualnej 2014 r. dla serwera SQL. **AzureVMSqlServerAutoBackupConfig nowy** polecenie konfiguruje ustawienia automatycznego tworzenia kopii zapasowej do przechowywania kopii zapasowych w ramach określonej przez zmienną $storageaccount konta magazynu platformy Azure. Te kopie zapasowe będą przechowywane w ciągu 10 dni. **AzureVMSqlServerExtension zestaw** polecenia aktualizacji określonej maszyny Wirtualnej platformy Azure przy użyciu tych ustawień.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta programu SQL Server IaaS.

Aby włączyć szyfrowanie, zmodyfikuj poprzedni skrypt do przekazania parametrów EnableEncryption wraz z hasłem (bezpieczny ciąg) dla parametru CertificatePassword. Poniższy skrypt umożliwia ustawienia automatycznego tworzenia kopii zapasowej w poprzednim przykładzie i dodaje szyfrowania.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Aby wyłączyć automatyczne wykonywanie kopii zapasowej, należy uruchomić skrypt tego samego bez **-Włącz** parametr **AzureVMSqlServerAutoBackupConfig nowy**. Podobnie jak w przypadku instalacji, może upłynąć kilka minut, aby wyłączyć automatyczne wykonywanie kopii zapasowych.

> [!NOTE]
> Wyłączanie i odinstalowywania programu SQL Server IaaS Agent nie powoduje usunięcia wcześniej skonfigurowanych ustawień zarządzanej kopii zapasowej. Automatyczna usługa Backup należy wyłączyć przed wyłączeniem lub odinstalowanie agenta programu SQL Server IaaS.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Automatyczne kopie zapasowe konfiguruje zarządzanej kopii zapasowej na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [zapoznaj się z dokumentacją dla zarządzanej kopii zapasowej](https://msdn.microsoft.com/library/dn449496.aspx) zrozumienie zachowania i skutki.

Można znaleźć dodatkowe kopii zapasowej i przywracanie wskazówki dotyczące programu SQL Server na maszynach wirtualnych Azure w następującym temacie: [kopii zapasowej i przywracania dla programu SQL Server w usłudze Azure Virtual Machines](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Informacje o innych zadaniach automatyzacji dostępny, zobacz [rozszerzenia agenta programu SQL Server IaaS](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

