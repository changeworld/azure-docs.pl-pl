---
title: Przypisywanie dostępu do tożsamości zarządzanej do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej w jednym zasobie, dostępu do innego zasobu przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547372"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Przypisywanie dostępu do tożsamości zarządzanej do zasobu przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure z tożsamością zarządzaną można przyznać tożsamości zarządzanej dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. W tym przykładzie pokazano, jak udzielić maszyny wirtualnej platformy Azure lub zestawu tożsamości zarządzanej zestawu maszyn wirtualnych do konta magazynu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, dostępne są trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) z witryny Azure portal (zobacz następną sekcję).
    - Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli) jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Przypisywanie dostępu do tożsamości zarządzanej do innego zasobu za pomocą funkcji RBAC

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, takim jak [maszyna wirtualna platformy Azure](qs-configure-cli-windows-vm.md) lub zestaw [skalowania maszyny wirtualnej platformy Azure:](qs-configure-cli-windows-vmss.md) 

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć zestaw skalowania maszyny wirtualnej lub maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. W tym przykładzie dajemy maszynie wirtualnej platformy Azure dostęp do konta magazynu. Najpierw używamy [listy zasobów AZ,](/cli/azure/resource/#az-resource-list) aby uzyskać jednostkę usługi dla maszyny wirtualnej o nazwie myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   W przypadku zestawu skalowania maszyny wirtualnej platformy Azure polecenie jest takie samo, z wyjątkiem tego miejsca, otrzymasz jednostkę usługi dla zestawu skalowania maszyny wirtualnej o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Po uzyskaniu identyfikatora jednostki usługi użyj [tworzenia przypisania roli az,](/cli/azure/role/assignment#az-role-assignment-create) aby dać maszynie wirtualnej lub zestawowi skalowania maszyny wirtualnej "Reader" dostęp do konta magazynu o nazwie "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby włączyć tożsamość zarządzaną na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](qs-configure-cli-windows-vm.md)
- Aby włączyć tożsamość zarządzaną w zestawie skalowania maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.](qs-configure-cli-windows-vmss.md)
