---
title: Jak przypisać z tożsamości zarządzanej dostępu do zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 2fb16b1762f1e5330cf058c37a6b7e0f008eb447
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60290982"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Przypisywanie dostępu tożsamości zarządzanej do zasobów przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure za pomocą tożsamości zarządzanej, można zapewnić dostęp tożsamość zarządzaną, do innego zasobu, podobnie jak podmiot zabezpieczeń. W tym przykładzie pokazano, jak przyznać maszynie wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych tożsamość zarządzaną dostęp do konta usługi Azure storage przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) Jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu tożsamości zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości zarządzanej w obrębie zasobu platformy Azure, takich jak [maszyny wirtualnej platformy Azure](qs-configure-cli-windows-vm.md) lub [zestawu skalowania maszyn wirtualnych platformy Azure](qs-configure-cli-windows-vmss.md): 

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta które jest skojarzone z subskrypcją platformy Azure w ramach której chcesz wdrożyć zestaw skalowania maszyny Wirtualnej lub maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. W tym przykładzie udostępniamy możliwość dostępu do maszyny wirtualnej platformy Azure na koncie magazynu. Najpierw używamy [az resource list](/cli/azure/resource/#az-resource-list) można pobrać nazwy głównej usługi dla maszyny wirtualnej o nazwie myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Dla zestawu skalowania maszyn wirtualnych platformy Azure polecenie jest taka sama, z wyjątkiem tego miejsca, możesz uzyskać nazwy głównej usługi dla zestawu skalowania maszyn wirtualnych o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Po utworzeniu identyfikator jednostki usługi, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create) nadać maszyny wirtualnej lub skalowania maszyn wirtualnych należy ustawić na konto magazynu o nazwie "myStorageAcct" access "Czytelnik":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć tożsamość zarządzana na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md).
- Aby włączyć tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vmss.md).
