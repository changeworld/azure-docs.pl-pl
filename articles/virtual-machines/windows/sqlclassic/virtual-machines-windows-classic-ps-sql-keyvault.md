---
title: Integracja usługi Key Vault z programem SQL Server na maszynach wirtualnych z Windows na platformie Azure (model klasyczny) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zautomatyzować proces konfiguracji szyfrowania programu SQL Server do użycia z usługą Azure Key Vault. W tym temacie opisano sposób integracji magazynu kluczy Azure za pomocą programu SQL Server, maszyn wirtualnych Utwórz w klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e20e2a094e1fd88dfc2a25b586dc6c894f92b418
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108479"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych platformy Azure (wersja klasyczna)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Omówienie
Istnieje wiele funkcji szyfrowania programu SQL Server, takich jak [technologii transparent data encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowania na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), i [szyfrowania kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te rodzaje szyfrowania wymagają przechowywania kluczy kryptograficznych używanego do szyfrowania i zarządzania nimi. Usługa Azure Key Vault (AKV) zaprojektowano w celu poprawy zabezpieczeń i zarządzania tych kluczy w bezpiecznych i wysoko dostępnych lokalizacji. [Łącznik usług SQL Server](https://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server, aby użyć tych kluczy z usługi Azure Key Vault.

> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Jeśli używasz programu SQL Server na maszynach w środowisku lokalnym, istnieją [kroki można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z komputera programu SQL Server w środowisku lokalnym](https://msdn.microsoft.com/library/dn198405.aspx). Ale programu SQL Server na maszynach wirtualnych platformy Azure, możesz zaoszczędzić czas przy użyciu *integracji magazynu kluczy Azure* funkcji. Za pomocą kilka poleceń cmdlet programu Azure PowerShell, aby włączyć tę funkcję możesz zautomatyzować konfiguracji niezbędne do maszyny Wirtualnej SQL w celu dostępu do magazynu kluczy.

Po włączeniu tej funkcji, automatycznie jest instalowana łącznik programu SQL Server, konfiguruje dostawcę EKM dostępu do usługi Azure Key Vault i tworzy poświadczeń, aby zezwolić na dostęp do magazynu. Jeśli cookie kroki opisane w dokumentacji wymienione wcześniej w środowisku lokalnym, zostanie wyświetlony, że ta funkcja automatyzuje kroki 2 i 3. Jedyną czynnością, którą należy nadal wykonać ręcznie, jest tworzenie magazynu kluczy i kluczy. W efekcie wszystkie ustawienia maszyny Wirtualnej SQL jest zautomatyzowane. Po ukończeniu tej konfiguracji tej funkcji możesz wykonać instrukcje języka T-SQL, aby rozpocząć szyfrowanie z bazy danych lub tworzenie kopii zapasowych, tak jak zwykle.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurowanie Integracja
Konfigurowanie integracji magazynu kluczy Azure przy użyciu programu PowerShell. Omówienie wymaganych parametrów, a następnie przykładowy skrypt programu PowerShell można znaleźć w poniższych sekcjach.

### <a name="install-the-sql-server-iaas-extension"></a>Zainstaluj rozszerzenie IaaS programu SQL Server
Po pierwsze, [zainstalować rozszerzenie IaaS programu SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Zrozumienie parametry wejściowe
Poniższa tabela zawiera listę parametrów wymaganych do uruchomienia skryptu programu PowerShell w następnej sekcji.

| Parametr | Opis | Przykład |
| --- | --- | --- |
| **$akvURL** |**Adres URL magazynu kluczy** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Główna nazwa usługi** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Klucz tajny jednostki usługi** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nazwa poświadczenia**: Integracja utworzenie poświadczenia w programie SQL Server, dzięki czemu maszyny Wirtualnej, aby mieć dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |"mycred1" |
| **$vmName** |**Nazwa maszyny wirtualnej**: Nazwa wcześniej utworzonej maszyny Wirtualnej SQL. |"myvmname" |
| **$serviceName** |**Nazwa usługi**: Nazwa usługi w chmurze, która jest skojarzona z maszyną Wirtualną programu SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Włącz integracja za pomocą programu PowerShell
**New AzureVMSqlServerKeyVaultCredentialConfig** polecenie cmdlet tworzy obiekt konfiguracji dla funkcji integracji magazynu kluczy Azure. **AzureVMSqlServerExtension zestaw** umożliwia skonfigurowanie tej integracji z **KeyVaultCredentialSettings** parametru. Poniższe kroki pokazują sposób użycia tych poleceń.

1. W programie Azure PowerShell najpierw skonfigurować parametrów wejściowych przy użyciu określonych wartości zgodnie z opisem w poprzednich sekcjach tego tematu. Przykładem jest poniższy skrypt.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Następnie użyj następującego skryptu, aby skonfigurować i włączyć integracja.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozszerzenie agenta IaaS programu SQL zaktualizuje maszyny Wirtualnej SQL przy użyciu tej nowej konfiguracji.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

