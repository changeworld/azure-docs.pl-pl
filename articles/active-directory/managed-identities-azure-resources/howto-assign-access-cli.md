---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure — Azure AD
description: Krok po kroku instrukcje dotyczące przypisywania tożsamość zarządzaną w jeden zasób, uzyskać dostęp do innego zasobu przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547372"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Przypisywanie dostępu tożsamości zarządzanej do zasobów przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej, można zapewnić dostęp tożsamość zarządzaną, do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym przykładzie pokazano, jak przyznać maszynie wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych tożsamość zarządzaną dostęp do konta usługi Azure storage przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal (zobacz następną sekcję).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) , jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, na przykład w przypadku [maszyny wirtualnej platformy Azure](qs-configure-cli-windows-vm.md) lub [zestawu skalowania maszyn wirtualnych platformy Azure](qs-configure-cli-windows-vmss.md): 

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta które jest skojarzone z subskrypcją platformy Azure w ramach której chcesz wdrożyć zestaw skalowania maszyny Wirtualnej lub maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. W tym przykładzie udostępniamy możliwość dostępu do maszyny wirtualnej platformy Azure na koncie magazynu. Najpierw użyj [AZ Resource list](/cli/azure/resource/#az-resource-list) , aby uzyskać nazwę główną usługi dla maszyny wirtualnej o nazwie myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Dla zestawu skalowania maszyn wirtualnych platformy Azure polecenie jest taka sama, z wyjątkiem tego miejsca, możesz uzyskać nazwy głównej usługi dla zestawu skalowania maszyn wirtualnych o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Gdy masz identyfikator jednostki usługi, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby nadać maszynie wirtualnej lub zestaw skalowania maszyn wirtualnych "czytnik" dostęp do konta magazynu o nazwie "ciąg mystorageacct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md).
- Aby włączyć zarządzaną tożsamość w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](qs-configure-cli-windows-vmss.md).
