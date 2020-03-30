---
title: Używanie tożsamości zarządzanych na maszynie Wirtualnej platformy Azure do logowania — usługa Azure AD
description: Instrukcje krok po kroku i przykłady dotyczące używania tożsamości zarządzanych przez maszynę Wirtualną platformy Azure dla jednostki usługi zasobów platformy Azure dla logowania się klienta skryptu i dostępu do zasobów.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547388"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure do logowania 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera przykłady skryptów programu PowerShell i interfejsu wiersza polecenia do logowania przy użyciu tożsamości zarządzanych dla jednostki usługi zasobów platformy Azure oraz wskazówki dotyczące ważnych tematów, takich jak obsługa błędów.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykładów interfejsu wiersza polecenia platformy Azure PowerShell lub platformy Azure w tym artykule, należy zainstalować najnowszą wersję [narzędzia Azure PowerShell](/powershell/azure/install-az-ps) lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli) 

> [!IMPORTANT]
> - Cały przykładowy skrypt w tym artykule zakłada, że klient wiersza polecenia jest uruchomiony na maszynie Wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure włączonych. Użyj funkcji maszyny Wirtualnej "Połącz" w witrynie Azure portal, aby zdalnie połączyć się z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu portalu Azure](qs-configure-portal-windows-vm.md)lub jednego z artykułów wariantu (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu SDK platformy Azure). 
> - Aby zapobiec błędom podczas dostępu do zasobów, tożsamość zarządzana maszyny Wirtualnej musi mieć co najmniej dostęp "Reader" w odpowiednim zakresie (maszyna wirtualna lub wyższa), aby zezwolić na operacje usługi Azure Resource Manager na maszynie Wirtualnej. Zobacz [Przypisywanie tożsamości zarządzanych dla zasobów platformy Azure dostępu do zasobu przy użyciu witryny Azure portal,](howto-assign-access-portal.md) aby uzyskać szczegółowe informacje.

## <a name="overview"></a>Omówienie

Tożsamości zarządzane dla zasobów platformy Azure udostępnia [obiekt jednostki usługi](../develop/developer-glossary.md#service-principal-object) , który jest [tworzony po włączeniu tożsamości zarządzanych dla zasobów platformy Azure](overview.md#how-does-the-managed-identities-for-azure-resources-work) na maszynie Wirtualnej. Podmiot usługi może mieć dostęp do zasobów platformy Azure i używany jako tożsamość przez klientów wiersza skryptu/polecenia do logowania się i dostępu do zasobów. Tradycyjnie, aby uzyskać dostęp do zabezpieczonych zasobów w ramach własnej tożsamości, klient skryptu musiałby:  

   - być zarejestrowane i zgodę na usługę Azure AD jako poufne/internetowej aplikacji klienta
   - zaloguj się pod jednostką usługi, używając poświadczeń aplikacji (które prawdopodobnie są osadzone w skrypcie)

Za pomocą tożsamości zarządzanych dla zasobów platformy Azure klient skryptu nie musi już wykonywać, ponieważ może logować się w ramach zarządzanych tożsamości dla jednostki usługi zasobów platformy Azure. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w obszarze tożsamości zarządzanej maszyny Wirtualnej dla jednostki usługi zasobów platformy Azure  
2. Wywołanie usługi Azure Resource Manager i uzyskać identyfikator jednostki usługi maszyny Wirtualnej. Cli zajmuje się zarządzaniem pozyskiwaniem/używaniem tokenów. Pamiętaj, aby zastąpić nazwę `<VM-NAME>`maszyny wirtualnej .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w obszarze tożsamości zarządzanej maszyny Wirtualnej dla jednostki usługi zasobów platformy Azure  
2. Wywołanie polecenia cmdlet usługi Azure Resource Manager, aby uzyskać informacje o maszynie Wirtualnej. Program PowerShell zajmuje się zarządzaniem użyciem tokenu automatycznie.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD,](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) aby uzyskać listę zasobów obsługujących usługę Azure AD i przetestowanych przy użyciu tożsamości zarządzanych dla zasobów platformy Azure i ich odpowiednich identyfikatorów zasobów.

## <a name="error-handling-guidance"></a>Wskazówki dotyczące obsługi błędów 

Odpowiedzi, takie jak następujące mogą wskazywać, że tożsamość zarządzana maszyny Wirtualnej dla zasobów platformy Azure nie została poprawnie skonfigurowana:

- Program PowerShell: *Invoke-WebRequest : Nie można połączyć się z serwerem zdalnym*
- CLI: *MSI: Nie można pobrać `http://localhost:50342/oauth2/token` tokenu z powodu błędu 'HTTPConnectionPool(host='localhost', port=50342)* 

Jeśli zostanie wyświetlony jeden z tych błędów, wróć do maszyny Wirtualnej platformy Azure w [witrynie Azure portal](https://portal.azure.com) i:

- Przejdź do strony **Tożsamość** i upewnij się, że **przypisany system** jest ustawiony na "Tak".
- Przejdź do strony **Rozszerzenia** i upewnij się, że zarządzane tożsamości rozszerzenia zasobów platformy Azure **(planowane do wycofania w styczniu 2019 r.)** zostały pomyślnie wdrożone.

Jeśli którakolwiek z nich jest niepoprawna, może być konieczne ponowne wdrożenie tożsamości zarządzanych zasobów platformy Azure w zasobie lub rozwiązanie problemu z niepowodzeniem wdrażania. Zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal,](qs-configure-portal-windows-vm.md) jeśli potrzebujesz pomocy w konfiguracji maszyny Wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Aby włączyć tożsamości zarządzane dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md)lub [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)






