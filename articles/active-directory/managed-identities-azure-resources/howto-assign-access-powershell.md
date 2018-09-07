---
title: Jak przypisać tożsamości usługi Zarządzanej dostępu do zasobów platformy Azure przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące przypisywania Instalatora MSI dla jednego zasobu, uzyskać dostęp do innego zasobu przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6d503526ed97dd1f61269acd83810cd44598d72f
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028353"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Przypisywanie dostępu tożsamości usługi zarządzanej (MSI) do zasobów przy użyciu programu PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu pliku MSI, można zapewnić dostęp MSI do innego zasobu, tak jak dowolnego podmiotu zabezpieczeń. W tym przykładzie pokazano, jak zapewnić maszynie wirtualnej platformy Azure w MSI dostęp do konta usługi Azure storage przy użyciu programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ponadto instalowanie [programu Azure PowerShell w wersji 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) Jeśli jeszcze go.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Przypisywanie dostępu tożsamości usługi Zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości usługi Zarządzanej w obrębie zasobu platformy Azure, [takich jak Maszynie wirtualnej platformy Azure](qs-configure-powershell-windows-vm.md):

1. Zaloguj się do platformy Azure za pomocą `Connect-AzureRmAccount` polecenia cmdlet. Użyj konta które jest skojarzone z subskrypcją platformy Azure, w którym skonfigurowano MSI:

   ```powershell
   Connect-AzureRmAccount
   ```
2. W tym przykładzie udostępniamy możliwość dostępu do maszyny Wirtualnej platformy Azure na koncie magazynu. Najpierw używamy [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) można pobrać nazwy głównej usługi dla maszyny Wirtualnej o nazwie "myVM", który został utworzony, gdy umożliwiliśmy MSI. Następnie użyjemy [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) zapewnienie maszyny Wirtualnej "Czytelnik" dostęp do konta magazynu o nazwie "myStorageAcct":

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Plik MSI dla zasobu nie jest wyświetlany na liście dostępnych tożsamości, sprawdź, czy poprawnie włączono pliku MSI. W naszym przypadku możemy przejść z powrotem do maszyny Wirtualnej platformy Azure w [witryny Azure portal](https://portal.azure.com) oraz:

- Spójrz na stronie "Konfiguracja" i upewnij się, włączoną tożsamością usługi Zarządzanej = "Yes".
- Spójrz na stronie "Rozszerzenia" i upewnij się, z rozszerzeniem MSI zostało pomyślnie wdrożone.

Jeśli jest albo nieprawidłowa, może być konieczne ponownie Wdróż ponownie MSI Twojego zasobu i rozwiązywanie problemów błędu wdrożenia.

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](overview.md).
- Aby włączyć tożsamości usługi Zarządzanej maszyny wirtualnej platformy Azure, zobacz [konfigurowania Azure VM tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell](qs-configure-powershell-windows-vm.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.

