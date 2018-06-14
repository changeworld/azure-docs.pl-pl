---
title: Integracja magazynu kluczy z programem SQL Server na maszynach wirtualnych systemu Windows na platformie Azure (klasyczne) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zautomatyzować konfigurację programu SQL Server szyfrowania do użycia z usługą Azure Key Vault. W tym temacie opisano sposób korzystania z integracji magazynu kluczy Azure z programem SQL Server, tworzenia maszyn wirtualnych w klasycznym modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
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
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fd0fb1f8ac9bb0132c64c195d4cc9c86ef8edd0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399733"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurowanie integracji magazynu kluczy Azure dla programu SQL Server na maszynach wirtualnych Azure (klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Wdrożenie klasyczne](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Przegląd
Istnieje wiele funkcji szyfrowania programu SQL Server, takich jak [przezroczystego szyfrowania danych (funkcji TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [szyfrowania na poziomie kolumny (CLE)](https://msdn.microsoft.com/library/ms173744.aspx), i [szyfrowania kopii zapasowych](https://msdn.microsoft.com/library/dn449489.aspx). Te formy szyfrowania wymagają przechowywania kluczy kryptograficznych używanego do szyfrowania i zarządzania nimi. Usługa Azure Key Vault (AKV) zaprojektowano w celu poprawy zabezpieczeń i zarządzania tych kluczy w lokalizacji bezpieczne i wysokiej dostępności. [Łącznik usług SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) umożliwia SQL Server do użycia tych kluczy z usługi Azure Key Vault.

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Jeśli korzystasz z programu SQL Server z lokalnymi maszynami, istnieją [kroki można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z komputera lokalnego programu SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Jednak dla programu SQL Server na maszynach wirtualnych Azure, można oszczędzić czas za pomocą *integracji magazynu kluczy Azure* funkcji. Kilka polecenia cmdlet programu Azure PowerShell do włączenia tej funkcji można zautomatyzować konfigurowanie niezbędne dla maszyny Wirtualnej SQL uzyskać dostępu do magazynu kluczy.

Gdy ta funkcja jest włączona, automatycznie jest instalowana łącznika programu SQL Server, konfiguruje dostawcy EKM. Aby uzyskać dostępu do usługi Azure Key Vault i tworzy poświadczenia, aby umożliwić użytkownikowi dostęp do magazynu. Jeśli przeglądał kroki opisane w dokumentacji lokalnego opisane powyżej, zobaczysz, że ta funkcja pozwala zautomatyzować kroki 2 i 3. Jedyną operacją, której nadal trzeba wykonać ręcznie polega na utworzeniu magazynu kluczy i kluczy. Z tego miejsca wszystkie ustawienia maszyny Wirtualnej SQL jest zautomatyzowany. Po ukończeniu tej konfiguracji tej funkcji można wykonywać instrukcje T-SQL, aby rozpocząć szyfrowania z bazy danych lub kopii zapasowych w zwykły sposób.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Skonfiguruj Integracja
Konfigurowanie integracji magazynu kluczy Azure za pomocą programu PowerShell. Poniższe sekcje zawierają omówienie wymagane parametry, a następnie przykładowy skrypt programu PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Zainstaluj rozszerzenie IaaS serwera SQL
Najpierw [zainstalować rozszerzenie IaaS serwera SQL](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Zrozumienie parametrów wejściowych
Poniższa tabela zawiera listę parametrów wymaganych do uruchomienia skryptu programu PowerShell w następnej sekcji.

| Parametr | Opis | Przykład |
| --- | --- | --- |
| **$akvURL** |**Adres URL magazynu kluczy** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Główna nazwa usługi** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Klucz tajny nazwy głównej usługi** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nazwa poświadczenia**: integracja usługi Azure Key Vault powoduje utworzenie poświadczenia w programie SQL Server, co umożliwia maszynie wirtualnej dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |"mycred1" |
| **$vmName** |**Nazwa maszyny wirtualnej**: Nazwa maszyny wirtualnej utworzonej wcześniej SQL. |"myvmname" |
| **$serviceName** |**Nazwa usługi**: Nazwa usługi w chmurze, która jest skojarzona z maszyną Wirtualną programu SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Włącz integracja przy użyciu programu PowerShell
**AzureVMSqlServerKeyVaultCredentialConfig nowy** polecenie cmdlet tworzy obiekt konfiguracji dla funkcji integracji magazynu kluczy Azure. **AzureVMSqlServerExtension zestaw** konfiguruje Integracja z **KeyVaultCredentialSettings** parametru. Poniższe kroki pokazują, jak używać tych poleceń.

1. W programie Azure PowerShell Skonfiguruj parametry wejściowe o określonej wartości zgodnie z opisem w poprzednich sekcjach tego tematu. Poniższy skrypt to przykład.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Następnie użyj następującego skryptu, aby skonfigurować i włączyć integracja.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Rozszerzenie agenta SQL IaaS spowoduje zaktualizowanie maszyny Wirtualnej SQL przy użyciu tej nowej konfiguracji.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

