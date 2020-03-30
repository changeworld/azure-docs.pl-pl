---
title: Integracja usługi Key Vault z klasyczną maszyną wirtualną programu Azure SQL Server
description: Dowiedz się, jak zautomatyzować konfigurację szyfrowania programu SQL Server do użytku z usługą Azure Key Vault. W tym temacie wyjaśniono, jak używać integracji usługi Azure Key Vault z maszynami wirtualnymi programu SQL Server utworzonych w klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f878c6f7a59328e2f68ffbaee066bba4a5b6c898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978129"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych platformy Azure (klasyczny)
> [!div class="op_single_selector"]
> * [Menedżer zasobów](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Omówienie
Istnieje wiele funkcji szyfrowania programu SQL Server, takich jak [przezroczyste szyfrowanie danych (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [szyfrowanie na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)i [szyfrowanie kopii zapasowych.](https://msdn.microsoft.com/library/dn449489.aspx) Te formy szyfrowania wymagają zarządzania kluczami kryptograficznymi używanymi do szyfrowania i przechowywania ich. Usługa Azure Key Vault (AKV) została zaprojektowana w celu zwiększenia bezpieczeństwa i zarządzania tymi kluczami w bezpiecznej i wysoce dostępnej lokalizacji. Łącznik [programu SQL Server umożliwia](https://www.microsoft.com/download/details.aspx?id=45344) programowi SQL Server używanie tych kluczy z usługi Azure Key Vault.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i Klasyczny](../../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano przy użyciu modelu wdrażania klasycznego. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Jeśli korzystasz z programu SQL Server z komputerami lokalnymi, [należy wykonać kroki, aby uzyskać dostęp do usługi Azure Key Vault z lokalnego komputera programu SQL Server.](https://msdn.microsoft.com/library/dn198405.aspx) Jednak w przypadku programu SQL Server na maszynach wirtualnych platformy Azure można zaoszczędzić czas przy użyciu funkcji *integracji usługi Azure Key Vault.* Za pomocą kilku poleceń cmdlet programu Azure PowerShell, aby włączyć tę funkcję, można zautomatyzować konfigurację niezbędną do uzyskania dostępu do magazynu kluczy.

Gdy ta funkcja jest włączona, automatycznie instaluje łącznik programu SQL Server, konfiguruje dostawcę EKM, aby uzyskać dostęp do usługi Azure Key Vault i tworzy poświadczenia, aby umożliwić dostęp do magazynu. Jeśli spojrzeno na kroki opisane wcześniej w lokalnej dokumentacji, widać, że ta funkcja automatyzuje kroki 2 i 3. Jedyną rzeczą, którą nadal musisz zrobić ręcznie, jest utworzenie magazynu kluczy i kluczy. Stamtąd cała konfiguracja maszyny Wirtualnej SQL jest zautomatyzowana. Po zakończeniu tej konfiguracji tej funkcji można wykonać instrukcje T-SQL, aby rozpocząć szyfrowanie baz danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurowanie integracji AKV
Konfigurowanie integracji usługi Azure Key Vault za pomocą programu PowerShell. Poniższe sekcje zawierają omówienie wymaganych parametrów, a następnie przykładowy skrypt programu PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instalowanie rozszerzenia IaaS programu SQL Server
Najpierw [zainstaluj rozszerzenie IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Opis parametrów wejściowych
W poniższej tabeli wymieniono parametry wymagane do uruchomienia skryptu programu PowerShell w następnej sekcji.

| Parametr | Opis | Przykład |
| --- | --- | --- |
| **$akvURL** |**Adres URL magazynu kluczy** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Nazwa jednostki głównej usługi** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Tajemnica główna usługi** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nazwa poświadczenia**: integracja usługi Azure Key Vault powoduje utworzenie poświadczenia w programie SQL Server, co umożliwia maszynie wirtualnej dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |"mycred1" |
| **$vmName** |**Nazwa maszyny wirtualnej**: Nazwa wcześniej utworzonej maszyny wirtualnej SQL. |"nazwa myvmname" |
| **$serviceName** |**Nazwa usługi:** Nazwa usługi w chmurze skojarzona z maszyną wirtualną SQL. |"nazwa obsługi mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Włącz integrację AKV z programem PowerShell
Polecenie cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** tworzy obiekt konfiguracji dla funkcji integracji usługi Azure Key Vault. **Set-AzureVMSqlServerExtension** konfiguruje tę integrację z **parametrem KeyVaultCredentialSettings.** Poniższe kroki pokazują, jak używać tych poleceń.

1. W programie Azure PowerShell najpierw skonfiguruj parametry wejściowe z określonymi wartościami, zgodnie z opisem w poprzednich sekcjach tego tematu. Poniższy skrypt jest przykładem.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Następnie użyj następującego skryptu, aby skonfigurować i włączyć integrację AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozszerzenie agenta SQL IaaS zaktualizuje maszynę wirtualną SQL za pomocą tej nowej konfiguracji.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

