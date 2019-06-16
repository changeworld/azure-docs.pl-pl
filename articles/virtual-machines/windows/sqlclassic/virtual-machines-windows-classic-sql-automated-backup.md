---
title: Zautomatyzowane tworzenie kopii zapasowej dla maszyn wirtualnych programu SQL Server (klasyczny) | Dokumentacja firmy Microsoft
description: 'W tym artykule wyjaśniono funkcja automatycznych kopii zapasowych programu SQL Server uruchomionego na maszynach wirtualnych platformy Azure przy użyciu usługi Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607697"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Zautomatyzowane tworzenie kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Wdrożenie klasyczne](../classic/sql-automated-backup.md)
> 
> 

Zautomatyzowane tworzenie kopii zapasowej automatycznie konfiguruje [zarządzanej kopii zapasowej programu Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) dla wszystkich istniejących i nowych baz danych na Maszynie wirtualnej platformy Azure z programu SQL Server 2014 Standard lub Enterprise. Dzięki temu można skonfigurować kopie zapasowe zwykłej bazy danych, które wykorzystują trwałe usługi Azure blob storage. Zautomatyzowane tworzenie kopii zapasowej jest zależna od [rozszerzenie agenta IaaS programu SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby wyświetlić wersję tego artykułu w zakresie usługi Resource Manager, zobacz [automatyczne kopie zapasowe dla programu SQL Server w usłudze Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z automatycznych kopii zapasowych, należy wziąć pod uwagę następujące wymagania wstępne:

**System operacyjny**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Wersją programu SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Zautomatyzowane tworzenie kopii zapasowej dla programu SQL Server 2016 jest obsługiwana na maszynach wirtualnych usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [automatyczne kopie zapasowe w wersji 2 dla programu SQL Server 2016 platformy Azure maszyn wirtualnych (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Konfiguracja bazy danych**:

* Docelowa baza danych musi używać modelu odzyskiwania pełnego.

**Azure PowerShell**:

* [Zainstaluj najnowsze polecenia programu Azure PowerShell](/powershell/azure/overview).

**Rozszerzenie programu SQL Server IaaS**:

* [Zainstaluj rozszerzenie IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Ustawienia
W poniższej tabeli opisano opcje, które można skonfigurować do automatycznego tworzenia kopii zapasowych. Dla klasycznych maszyn wirtualnych należy skonfigurować te ustawienia za pomocą programu PowerShell.

| Ustawienie | Zakres (ustawienie domyślne) | Opis |
| --- | --- | --- |
| **Automatyczne kopie zapasowe** |Włącz/Wyłącz (wyłączony) |Włącza lub wyłącza automatyczne kopie zapasowe maszyny wirtualnej platformy Azure z programu SQL Server 2014 Standard lub Enterprise. |
| **Okres przechowywania** |1 – 30 dni (30 dni) |Liczba dni przechowywania kopii zapasowej. |
| **Konto magazynu** |Konto usługi Azure storage (konto magazynu utworzone dla określonej maszyny Wirtualnej) |Konto magazynu platformy Azure do przechowywania automatycznych kopii zapasowych plików w magazynie obiektów blob. Kontener jest tworzony w tej lokalizacji, aby zapisać wszystkie pliki kopii zapasowej. Konwencja nazewnictwa pliku kopii zapasowej zawiera daty, godziny i nazwy komputera. |
| **Szyfrowanie** |Włącz/Wyłącz (wyłączony) |Włącza lub wyłącza funkcję szyfrowania. Gdy jest włączone szyfrowanie, certyfikaty służące do przywrócenia kopii zapasowej znajdują się w podanego konta magazynu w tym samym kontenerze automaticbackup przy użyciu tej samej konwencji nazewnictwa. Zmiana hasła nowy certyfikat jest generowany przy użyciu tego hasła, ale stary certyfikat pozostanie do przywrócenia poprzednich kopii zapasowych. |
| **Hasło** |Tekst hasła, (Brak) |Hasło kluczy szyfrowania. Jest to tylko wymagane, jeśli szyfrowanie jest włączone. Aby przywrócić szyfrowanej kopii zapasowej, musisz mieć prawidłowe hasło i powiązane certyfikat, który został użyty w tym czasie, na których wykonano kopię zapasową. |
| **Bazy danych systemu tworzenia kopii zapasowych** | Włącz/Wyłącz (wyłączony) | Twórz pełne kopie zapasowe Master, Model i MSDB |
| **Konfigurowanie harmonogramu tworzenia kopii zapasowych** | Ręczne/automatyczne (automatyczne) | Wybierz **automatyczne** do automatycznie twórz pełne kopie zapasowe w oparciu o wzrost dziennika dzienników. Wybierz **ręczne** Aby określić harmonogram dla pełnej i kopie zapasowe dzienników. |

## <a name="configuration-with-powershell"></a>Konfiguracja przy użyciu programu PowerShell
W poniższym przykładzie programu PowerShell automatyczne kopie zapasowe jest skonfigurowany dla istniejącej maszyny Wirtualnej 2014 r. dla serwera SQL. **New AzureVMSqlServerAutoBackupConfig** polecenie konfiguruje ustawienia automatycznych kopii zapasowych w celu przechowywania kopii zapasowych w ramach konta usługi Azure storage określone przez zmienną $storageaccount. Te kopie zapasowe będą przechowywane przez 10 dni. **AzureVMSqlServerExtension zestaw** polecenie aktualizuje określoną maszynę Wirtualną platformy Azure przy użyciu tych ustawień.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Go może potrwać kilka minut, aby zainstalować i skonfigurować agenta IaaS programu SQL Server.

Aby włączyć szyfrowanie, zmodyfikować poprzedni skrypt do przekazania parametrów EnableEncryption wraz z hasłem (bezpieczny ciąg) dla parametru CertificatePassword. Poniższy skrypt umożliwia ustawienia automatycznych kopii zapasowych w poprzednim przykładzie i dodaje szyfrowania.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Aby wyłączyć automatyczne wykonywanie kopii zapasowej, uruchom ten sam skrypt bez **-Włącz** parametr **New AzureVMSqlServerAutoBackupConfig**. Podobnie jak w przypadku instalacji, może potrwać kilka minut, aby wyłączyć automatyczne kopie zapasowe.

> [!NOTE]
> Wyłączanie i odinstalowywanie agenta IaaS programu SQL Server nie powoduje usunięcia wcześniej skonfigurowanych ustawień zarządzanych kopii zapasowych. Przed przystąpieniem do wyłączenia lub odinstalowania agenta IaaS programu SQL Server, należy wyłączyć automatyczne kopie zapasowe.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Zautomatyzowane tworzenie kopii zapasowej konfiguruje zarządzanej kopii zapasowej na maszynach wirtualnych platformy Azure. Dlatego ważne jest, aby [zapoznaj się z dokumentacją dla zarządzanej kopii zapasowej](https://msdn.microsoft.com/library/dn449496.aspx) , aby zrozumieć zachowania i skutków.

Można znaleźć dodatkowe kopii zapasowej i przywracanie wskazówki dotyczące programu SQL Server na maszynach wirtualnych platformy Azure w następującym temacie: [Kopia zapasowa i przywracanie programu SQL Server na maszynach wirtualnych platformy Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Aby uzyskać informacje o innych zadaniach automatyzacji dostępności, zobacz [rozszerzenie agenta IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

