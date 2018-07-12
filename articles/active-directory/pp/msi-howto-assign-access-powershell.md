---
title: Jak przypisać tożsamości usługi Zarządzanej dostępu do zasobów platformy Azure przy użyciu programu PowerShell
description: Krok po kroku instrukcje dotyczące przypisywania Instalatora MSI dla jednego zasobu, uzyskać dostęp do innego zasobu przy użyciu programu PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac8cca1e80defca33a879db5d4c160362314931a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610966"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Przypisywanie dostępu tożsamości usługi zarządzanej (MSI) do zasobów przy użyciu programu PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu pliku MSI, można zapewnić dostęp MSI do innego zasobu, tak jak dowolnego podmiotu zabezpieczeń. W tym przykładzie pokazano, jak zapewnić maszynie wirtualnej platformy Azure w MSI dostęp do konta usługi Azure storage przy użyciu programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Ponadto instalowanie [programu Azure PowerShell w wersji 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) Jeśli jeszcze go.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Przypisywanie dostępu tożsamości usługi Zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości usługi Zarządzanej w obrębie zasobu platformy Azure, [takich jak Maszynie wirtualnej platformy Azure](msi-qs-configure-powershell-windows-vm.md):

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

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](msi-overview.md).
- Aby włączyć tożsamości usługi Zarządzanej maszyny wirtualnej platformy Azure, zobacz [konfigurowania Azure VM tożsamość usługi zarządzanej (MSI) przy użyciu programu PowerShell](msi-qs-configure-powershell-windows-vm.md).

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.

