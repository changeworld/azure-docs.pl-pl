---
title: Jak używać zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure dotyczące logowania
description: Krok po kroku instrukcje i przykłady dotyczące korzystania z maszyny Wirtualnej platformy Azure zarządzanych tożsamości dla jednostki usługi zasobów platformy Azure dla skryptu klienta logowania i dostępu do zasobów.
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
ms.openlocfilehash: 43aa0859fa67cc6b2f5c5974f072e7b6d4b29527
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442129"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Jak używać zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure dotyczące logowania 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera przykłady skryptów programu PowerShell i interfejsu wiersza polecenia Zaloguj się przy użyciu zarządzanych tożsamości dla jednostki usługi zasobów platformy Azure oraz wskazówki dotyczące ważnych tematów, takich jak obsługa błędów.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykłady programu Azure PowerShell lub wiersza polecenia platformy Azure, w tym artykule, należy zainstalować najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-az-ps) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Wszystkie przykładowy skrypt w tym artykule przyjęto założenie, że klient wiersza polecenia jest uruchomiona na maszynie Wirtualnej z zarządzanych tożsamości dla zasobów platformy Azure są włączone. Funkcja VM "Połącz" w witrynie Azure portal na połączenie zdalne z maszyną wirtualną. Aby uzyskać więcej informacji na temat włączania zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md), lub jeden z tych artykułów variant (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub platformy Azure ZESTAW SDK). 
> - Aby uniknąć błędów podczas uzyskiwania dostępu do zasobów, tożsamości zarządzanej maszyny Wirtualnej należy podać co najmniej "Czytelnik" na uzyskiwanie dostępu do wybranego zakresu (maszyna wirtualna lub nowszej) umożliwia operacje usługi Azure Resource Manager na maszynie Wirtualnej. Zobacz [przypisywanie zarządzanych tożsamości dla zasobów platformy Azure dostęp do zasobów przy użyciu witryny Azure portal](howto-assign-access-portal.md) Aby uzyskać szczegółowe informacje.

## <a name="overview"></a>Przegląd

Zarządzanych tożsamości dla zasobów platformy Azure zapewnia [obiektu jednostki usługi](../develop/developer-glossary.md#service-principal-object) , czyli [utworzone podczas włączania zarządzanych tożsamości dla zasobów platformy Azure](overview.md#how-does-it-work) na maszynie Wirtualnej. Nazwy głównej usługi można nadać dostęp do zasobów platformy Azure i używany jako tożsamość przez skrypt/wiersza polecenia klientów do logowania i dostępu do zasobów. Tradycyjnie Aby uzyskać dostęp do zabezpieczonych zasobów w ramach własnej tożsamości, klient skryptu musi:  

   - być zarejestrowane i wyrażeniu zgody z usługą Azure AD jako aplikację kliencką poufne/sieci web
   - Zaloguj się w ramach jego nazwy głównej usługi, przy użyciu poświadczeń aplikacji, (które prawdopodobnie osadzony w skrypcie)

Z zarządzanych tożsamości dla zasobów platformy Azure klient skrypt nie musi już wykonaj jedną z nich, zgodnie z jego może zalogować się w ramach zarządzanych tożsamości dla jednostki usługi zasobów platformy Azure. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w ramach tożsamości zarządzanej maszyny Wirtualnej dla jednostki usługi zasobów platformy Azure  
2. Wywoływanie usługi Azure Resource Manager i Pobierz identyfikator jednostki usługi maszyny Wirtualnej Interfejs wiersza polecenia zajmuje się zarządzaniem tokenu nabycia lub używać dla Ciebie automatycznie. Pamiętaj zastąpić nazwę maszyny wirtualnej dla `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w ramach tożsamości zarządzanej maszyny Wirtualnej dla jednostki usługi zasobów platformy Azure  
2. Wywołaj polecenie cmdlet usługi Azure Resource Manager, aby uzyskać informacje na temat maszyny Wirtualnej. Program PowerShell zajmuje się zarządzaniem użycia tokenu dla Ciebie automatycznie.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) listę zasobów, które obsługują usługę Azure AD i zostały przetestowane z zarządzanych tożsamości dla zasobów platformy Azure oraz ich odpowiednich identyfikatorów zasobów.

## <a name="error-handling-guidance"></a>Wskazówki dotyczące obsługi błędów 

Odpowiedzi na następujące może wskazywać, że tożsamości zarządzanej maszyny Wirtualnej dla zasobów platformy Azure nie został prawidłowo skonfigurowany:

- Program PowerShell: *Invoke-WebRequest: Nie można nawiązać połączenia z serwerem zdalnym*
- CLI: *MSI: Nie można pobrać tokenu z `http://localhost:50342/oauth2/token` przy błędzie "HTTPConnectionPool (host ="localhost", port = 50342)* 

Jeśli zostanie wyświetlony jeden z tych błędów, wróć do maszyny Wirtualnej platformy Azure w [witryny Azure portal](https://portal.azure.com) oraz:

- Przejdź do **tożsamości** strony i upewnij się, **przypisanej w systemie** jest ustawiona na "Yes".
- Przejdź do **rozszerzenia** strony i upewnij się, zarządzanych tożsamości dla zasobów platformy Azure rozszerzenia **(zaplanowane do wycofania z użycia w styczniu 2019)** zostało pomyślnie wdrożone.

Jeśli jest albo nieprawidłowa, może być konieczne ponowne wdrażanie zarządzanych tożsamości dla zasobów platformy Azure w swoim zasobie, ponownie lub Rozwiązywanie problemów z błędu wdrożenia. Zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md) Jeśli potrzebujesz pomocy przy użyciu konfiguracji maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md), lub [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)






