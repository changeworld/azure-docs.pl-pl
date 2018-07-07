---
title: Jak używać platformy Azure maszyna wirtualna tożsamości usługi zarządzanej dla logowania
description: Krok po kroku instrukcje i przykłady, za pomocą nazwy głównej usługi Zarządzanej maszyny Wirtualnej platformy Azure dla skryptu klienta Zaloguj się, zasobami i ich dostęp.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 4a811c5354a9ff2aaa48a300d9b2655f91fdab23
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901096"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Jak używać usługi Azure VM tożsamość usługi zarządzanej (MSI) dla logowania 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera przykłady skryptów programu PowerShell i interfejsu wiersza polecenia Zaloguj się przy użyciu nazwy głównej usługi MSI i wskazówki na ważne tematy, takie jak obsługa błędów.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Jeśli planujesz użyć przykłady programu Azure PowerShell lub wiersza polecenia platformy Azure, w tym artykule, należy zainstalować najnowszą wersję [programu Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) lub [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Wszystkie przykładowy skrypt w tym artykule przyjęto założenie, że klient wiersza polecenia jest uruchomiona na maszynie wirtualnej włączona MSI. Funkcja VM "Połącz" w witrynie Azure portal na połączenie zdalne z maszyną wirtualną. Aby uzyskać więcej informacji na temat włączania MSI na maszynie Wirtualnej, zobacz [Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md), lub jeden z tych artykułów variant (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu SDK platformy Azure). 
> - Aby uniknąć błędów podczas uzyskiwania dostępu do zasobów, tożsamości usługi Zarządzanej maszyny Wirtualnej należy podać co najmniej "Czytelnik" na uzyskiwanie dostępu do wybranego zakresu (maszyna wirtualna lub nowszej) umożliwia operacje usługi Azure Resource Manager na maszynie Wirtualnej. Zobacz [przypisywanie dostępu tożsamości usługi zarządzanej (MSI) do zasobów przy użyciu witryny Azure portal](howto-assign-access-portal.md) Aby uzyskać szczegółowe informacje.

## <a name="overview"></a>Przegląd

Instalator MSI zapewnia [obiektu jednostki usługi](../develop/active-directory-dev-glossary.md#service-principal-object) , czyli [utworzone podczas włączania MSI](overview.md#how-does-it-work) na maszynie Wirtualnej. Nazwy głównej usługi można nadać dostęp do zasobów platformy Azure i używany jako tożsamość przez skrypt/wiersza polecenia klientów do logowania i dostępu do zasobów. Tradycyjnie Aby uzyskać dostęp do zabezpieczonych zasobów w ramach własnej tożsamości, klient skryptu musi:  

   - być zarejestrowane i wyrażeniu zgody z usługą Azure AD jako aplikację kliencką poufne/sieci web
   - Zaloguj się w ramach jego nazwy głównej usługi, przy użyciu poświadczeń aplikacji, (które prawdopodobnie osadzony w skrypcie)

Za pomocą pliku MSI klient skrypt nie musi już wykonaj jedną z nich, zgodnie z jego może zalogować się w obszarze nazwy głównej usługi MSI. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w ramach jednostki usługi tożsamości usługi Zarządzanej maszyny Wirtualnej  
2. Wywoływanie usługi Azure Resource Manager i Pobierz identyfikator jednostki usługi maszyny Wirtualnej Interfejs wiersza polecenia zajmuje się zarządzaniem tokenu nabycia lub używać dla Ciebie automatycznie. Pamiętaj zastąpić nazwę maszyny wirtualnej dla `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt pokazuje, jak:

1. Zaloguj się do usługi Azure AD w ramach jednostki usługi tożsamości usługi Zarządzanej maszyny Wirtualnej  
2. Wywołaj polecenie cmdlet usługi Azure Resource Manager, aby uzyskać informacje na temat maszyny Wirtualnej. Program PowerShell zajmuje się zarządzaniem użycia tokenu dla Ciebie automatycznie.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Identyfikatory zasobów dla usług platformy Azure

Zobacz [usługi systemu Azure to uwierzytelnianie pomocy technicznej usługi Azure AD](services-support-msi.md#azure-services-that-support-azure-ad-authentication) listę zasobów, które obsługują usługę Azure AD i zostały przetestowane za pomocą pliku MSI i ich odpowiednich identyfikatorów zasobów.

## <a name="error-handling-guidance"></a>Wskazówki dotyczące obsługi błędów 

Odpowiedzi, takie jak następujące może wskazywać, że tożsamości usługi Zarządzanej maszyny Wirtualnej nie został prawidłowo skonfigurowany:

- Program PowerShell: *Invoke-WebRequest: nie można nawiązać połączenia z serwerem zdalnym*
- Interfejs wiersza polecenia: *MSI: nie można pobrać tokenu z "http://localhost:50342/oauth2/token" z powodu błędu programu "HTTPConnectionPool (host ="localhost", port = 50342)* 

Jeśli zostanie wyświetlony jeden z tych błędów, wróć do maszyny Wirtualnej platformy Azure w [witryny Azure portal](https://portal.azure.com) oraz:

- Przejdź do **konfiguracji** strony i upewnij się, "Tożsamość usługi zarządzanej" jest ustawiona na "Yes".
- Przejdź do **rozszerzenia** strony i upewnij się, z rozszerzeniem MSI zostało pomyślnie wdrożone.

Jeśli jest albo nieprawidłowa, może być konieczne ponownie Wdróż ponownie MSI Twojego zasobu i rozwiązywanie problemów błędu wdrożenia. Zobacz [Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal](qs-configure-portal-windows-vm.md) Jeśli potrzebujesz pomocy przy użyciu konfiguracji maszyny Wirtualnej.

## <a name="related-content"></a>Powiązana zawartość

- Aby włączyć tożsamości usługi Zarządzanej maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md), lub [Konfigurowanie maszyny Wirtualnej tożsamość usługi zarządzanej (MSI) przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md)

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.








