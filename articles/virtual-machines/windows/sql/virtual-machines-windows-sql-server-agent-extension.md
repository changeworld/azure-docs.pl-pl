---
title: Automatyzacja zadań zarządzania za pomocą rozszerzenia agenta IaaS
description: W tym artykule opisano sposób zarządzania rozszerzeniem agenta programu SQL Server IaaS, które automatyzuje określone zadania administracyjne programu SQL Server. Obejmują one automatyczne tworzenie kopii zapasowych, automatyczne instalowanie poprawek i integrację usługi Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030782"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatyzacja zadań zarządzania na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia agenta IaaS programu SQL Server
> [!div class="op_single_selector"]
> * [Menedżer zasobów](virtual-machines-windows-sql-server-agent-extension.md)
> * [Wdrożenie klasyczne](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

Rozszerzenie agenta IaaS w programie SQL Server (SqlIaasExtension) działa na maszynach wirtualnych platformy Azure w celu zautomatyzowania zadań administracyjnych. Ten artykuł zawiera omówienie usług, które obsługuje rozszerzenie. Ten artykuł zawiera również instrukcje dotyczące instalacji, stanu i usuwania rozszerzenia.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Aby wyświetlić klasyczną wersję tego artykułu, zobacz [ROZSZERZENIE AGENTA PROGRAMU SQL Server dla maszyn wirtualnych programu SQL Server (klasycznych).](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)


## <a name="supported-services"></a>Obsługiwane usługi
Rozszerzenie agenta programu SQL Server IaaS obsługuje następujące zadania administracyjne:

| Funkcja administrowania | Opis |
| --- | --- |
| **Automatyczna kopia zapasowa programu SQL Server** |Automatyzuje planowanie kopii zapasowych dla wszystkich baz danych dla wystąpienia domyślnego lub [poprawnie zainstalowane](virtual-machines-windows-sql-server-iaas-faq.md#administration) nazwane wystąpienie programu SQL Server na maszynie Wirtualnej. Aby uzyskać więcej informacji, zobacz [Automatyczna kopia zapasowa dla programu SQL Server na maszynach wirtualnych platformy Azure (Menedżer zasobów)](virtual-machines-windows-sql-automated-backup.md). |
| **Automatyczne instalowanie poprawek w programie SQL Server** |Konfiguruje okno konserwacji, w którym mogą mieć miejsce ważne aktualizacje systemu Windows na maszynie wirtualnej, dzięki czemu można uniknąć aktualizacji w godzinach szczytu dla obciążenia. Aby uzyskać więcej informacji, zobacz [Automatyczne instalowanie poprawek dla programu SQL Server na maszynach wirtualnych platformy Azure (Menedżer zasobów)](virtual-machines-windows-sql-automated-patching.md). |
| **Integracja magazynu kluczy Azure** |Umożliwia automatyczne instalowanie i konfigurowanie usługi Azure Key Vault na maszynie wirtualnej programu SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych platformy Azure (Menedżer zasobów)](virtual-machines-windows-ps-sql-keyvault.md). |

Po zainstalowaniu i uruchomieniu rozszerzenia agenta programu SQL Server Iaas udostępnia funkcje administracyjne:

* W panelu SQL Server maszyny wirtualnej w witrynie Azure portal i za pośrednictwem usługi Azure PowerShell dla obrazów programu SQL Server w portalu Azure Marketplace.
* Za pośrednictwem programu Azure PowerShell dla ręcznych instalacji rozszerzenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Oto wymagania dotyczące używania rozszerzenia agenta IaaS programu SQL Server na maszynie wirtualnej:

**System operacyjny**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Wersja programu SQL Server:**

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Program Azure PowerShell:**

* [Pobieranie i konfigurowanie najnowszych poleceń programu Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Instalacja
Rozszerzenie IaaS programu SQL Server jest instalowane podczas rejestrowania maszyny Wirtualnej programu SQL Server u [dostawcy zasobów maszyny Wirtualnej SQL.](virtual-machines-windows-sql-register-with-resource-provider.md) W razie potrzeby agent IaaS programu SQL Server można zainstalować ręcznie, używając poniższego polecenia programu PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> Instalowanie rozszerzenia uruchamia ponownie usługę SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalowanie na maszynie wirtualnej przy jednym wystąpieniu o nazwie SQL Server
Rozszerzenie IaaS programu SQL Server będzie działać z nazwanym wystąpieniem na programie SQL Server, jeśli domyślne wystąpienie zostanie odinstalowane, a rozszerzenie IaaS zostanie ponownie zainstalowane.

Aby użyć nazwanego wystąpienia programu SQL Server, wykonaj następujące kroki:
   1. Wdrażanie maszyny Wirtualnej programu SQL Server z witryny Azure Marketplace. 
   1. Odinstaluj rozszerzenie IaaS z [witryny Azure portal](https://portal.azure.com).
   1. Odinstaluj program SQL Server całkowicie w ramach maszyny Wirtualnej programu SQL Server.
   1. Zainstaluj program SQL Server z nazwanym wystąpieniem w maszynie wirtualnej programu SQL Server. 
   1. Zainstaluj rozszerzenie IaaS z witryny Azure portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Uzyskaj stan rozszerzenia IaaS programu SQL Server
Jednym ze sposobów sprawdzenia, czy rozszerzenie jest zainstalowane jest wyświetlanie stanu agenta w witrynie Azure portal. Wybierz **pozycję Wszystkie ustawienia** w oknie maszyny wirtualnej, a następnie wybierz pozycję **Rozszerzenia**. Na liście powinno zostać wyświetlone rozszerzenie **SqlIaasExtension.**

![Stan rozszerzenia agenta IaaS programu SQL Server w witrynie Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Można również użyć polecenia cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Poprzednie polecenie potwierdza, że agent jest zainstalowany i zawiera ogólne informacje o stanie. Za pomocą następujących poleceń można uzyskać określone informacje o stanie automatycznej kopii zapasowej i poprawek:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Usuwania
W witrynie Azure portal można odinstalować rozszerzenie, wybierając wielokropek w oknie **Rozszerzenia** właściwości maszyny wirtualnej. Następnie wybierz pozycję **Usuń**.

![Odinstalowywanie rozszerzenia agenta IaaS programu SQL Server w witrynie Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Można również użyć polecenia cmdlet **Remove-AzVMSqlServerExtension** PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Następne kroki
Rozpocznij korzystanie z jednej z usług, które obsługuje rozszerzenie. Aby uzyskać więcej informacji, zobacz artykuły, do których odwołuje się w sekcji [Obsługiwane usługi](#supported-services) tego artykułu.

Aby uzyskać więcej informacji na temat uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure, zobacz [Co to jest sql server na maszynach wirtualnych platformy Azure?](virtual-machines-windows-sql-server-iaas-overview.md).
