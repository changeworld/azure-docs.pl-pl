---
title: Konfigurowanie tożsamości zarządzanych w zestawie skalowania maszyny wirtualnej — interfejsu wiersza polecenia platformy Azure — usługa Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez użytkownika w zestawie skalowania maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2832a8c584c0fbe707f22501809d772c6ffb970b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430087"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie tożsamości zarządzanej przypisanej przez system w zestawie skalowania maszyny wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyny wirtualnej platformy Azure


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, twoje konto wymaga następujących przypisań kontroli dostępu opartych na rolach platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej,](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aby utworzyć zestaw skalowania maszyny wirtualnej i włączyć i usunąć system i/lub przypisaną przez użytkownika tożsamość zarządzaną z zestawu skalowania maszyny wirtualnej.
    - [Rola współautora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) w celu utworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - [Rola zarządzanego operatora tożsamości](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyny wirtualnej.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, dostępne są trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) z witryny Azure portal (zobacz następną sekcję).
    - Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu.
    - Jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia, należy [zainstalować najnowszą wersję interfejsu wiersza](https://docs.microsoft.com/cli/azure/install-azure-cli) polecenia platformy Azure (2.0.13 lub nowszym). 
      
      > [!NOTE]
      > Polecenia zostały zaktualizowane w celu odzwierciedlenia najnowszej wersji [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamości zarządzanej przypisane do systemu dla zestawu skalowania maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia zestawu skalowania maszyny wirtualnej platformy Azure

Aby utworzyć zestaw skalowania maszyny wirtualnej z włączoną tożsamością zarządzaną przypisaną przez system:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć zestaw skalowania maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) do zamknięcia i wdrożenia zestawu skalowania maszyny wirtualnej i powiązanych z nią zasobów przy użyciu [grupy az create](/cli/azure/group/#az-group-create). Możesz pominąć ten krok, jeśli masz już grupę zasobów, której chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz zestaw skalowania maszyny wirtualnej przy użyciu [programu az vmss create](/cli/azure/vmss/#az-vmss-create) . Poniższy przykład tworzy zestaw skalowania maszyny wirtualnej o nazwie *myVMSS* z `--assign-identity` tożsamością zarządzaną przypisaną przez system, zgodnie z żądaniem parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu w istniejącym zestawie skalowania maszyny wirtualnej platformy Azure

Jeśli chcesz włączyć tożsamość zarządzaną przypisaną do systemu w istniejącym zestawie skalowania maszyny wirtualnej platformy Azure:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą zestaw skalowania maszyny wirtualnej.

   ```azurecli-interactive
   az login
   ```

2. Użyj polecenia [przypisywania tożsamości az vmss,](/cli/azure/vmss/identity/#az-vmss-identity-assign) aby włączyć przypisaną do systemu tożsamość zarządzaną istniejącej maszynie wirtualnej:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skalowania maszyny wirtualnej, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu, ale nadal wymaga tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości zarządzanej przypisanej do systemu i nie ma tożsamości zarządzanych przypisanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W `none` wartości jest rozróżniana wielkość liter. Musi być małe litery. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak włączyć i usunąć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyny wirtualnej

W tej sekcji można przejść przez tworzenie zestawu skalowania maszyny wirtualnej i przypisanie tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyny wirtualnej. Jeśli masz już zestaw skalowania maszyny wirtualnej, którego chcesz użyć, pomiń tę sekcję i przejdź do następnego.

1. Możesz pominąć ten krok, jeśli masz już grupę zasobów, której chcesz użyć. Utwórz [grupę zasobów](~/articles/azure-resource-manager/management/overview.md#terminology) do przechowywania i wdrażania tożsamości zarządzanej przypisanej przez użytkownika, używając [tworzenia grupy az](/cli/azure/group/#az-group-create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpowiedź zawiera szczegółowe informacje dotyczące utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. Wartość `id` zasobu przypisana do tożsamości zarządzanej przypisanej przez użytkownika jest używana w następnym kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Utwórz zestaw skalowania maszyny wirtualnej przy użyciu [programu az vmss create](/cli/azure/vmss/#az-vmss-create). Poniższy przykład tworzy zestaw skalowania maszyny wirtualnej skojarzony z nową tożsamością zarządzaną przypisaną przez użytkownika, zgodnie z parametrem. `--assign-identity` Upewnij się, że parametry `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyny wirtualnej

1. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegółowe informacje dotyczące utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Przypisz tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyny wirtualnej przy użyciu [przypisania tożsamości az vmss](/cli/azure/vmss/identity). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. Jest `<USER ASSIGNED IDENTITY>` to właściwość zasobu `name` tożsamości przypisanej przez użytkownika, utworzona w poprzednim kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyny wirtualnej platformy Azure

Aby usunąć tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyny wirtualnej, należy użyć [pliku az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Jeśli jest to jedyna przypisana przez użytkownika tożsamość zarządzana `UserAssigned` przypisana do zestawu skalowania maszyny wirtualnej, zostanie usunięta z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. Będzie `<USER ASSIGNED IDENTITY>` to właściwość tożsamości zarządzanej `name` przypisana przez użytkownika, którą można znaleźć w sekcji `az vmss identity show`tożsamości zestawu skalowania maszyny wirtualnej przy użyciu:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli zestaw skalowania maszyny wirtualnej nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć z niej wszystkie tożsamości zarządzane przypisane przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W `none` wartości jest rozróżniana wielkość liter. Musi być małe litery.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli zestaw skalowania maszyny wirtualnej ma zarówno tożsamości zarządzane przypisane przez system, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanej przypisanej do systemu. Użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełny zestaw skalowania maszyny wirtualnej platformy Azure, zobacz: 

  - [Tworzenie zestawu skalowania maszyny wirtualnej za pomocą interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















