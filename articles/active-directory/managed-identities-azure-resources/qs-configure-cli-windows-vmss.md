---
title: Jak skonfigurować system i zarządzanych tożsamości przypisanych przez użytkownika na skalę maszyny wirtualnej platformy Azure można ustawić przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i zarządzanych tożsamości przypisanych przez użytkownika na skalę maszyny wirtualnej platformy Azure zestawu, przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 02/15/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04a3c9eba1f6498796a7e617b400649963c996d1
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444471"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie przypisany systemowo tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanych przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujące przypisania kontroli dostępu opartej na rolach platformy Azure:

    > [!NOTE]
    > Nie dodatkowych Azure przypisań ról katalogu usługi AD wymagane.

    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) do tworzenia zestawu skalowania maszyn wirtualnych i włączyć i usuwać systemowych i/lub przypisanych przez użytkownika z tożsamości zarządzanej z zestawu skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) tożsamości zarządzanej roli do utworzenia przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania, użytkownik przypisany tożsamości zarządzanej, od i do zestawu skalowania maszyn wirtualnych.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 
      
      > [!NOTE]
      > Polecenia zostały zaktualizowanie, aby odzwierciedlić najnowszą wersję [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisany systemowo tożsamości zarządzanych dla maszyn wirtualnych zestawu skalowania przy użyciu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć maszyny wirtualnej zestawu skalowania z przypisany systemowo tożsamość zarządzaną włączone:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta które jest skojarzone z subskrypcją platformy Azure w ramach której chcesz wdrożyć zestaw skalowania maszyn wirtualnych:

   ```azurecli-interactive
   az login
   ```

2. Tworzenie [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanymi zasobami, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az-group-create). Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu [tworzenie az vmss](/cli/azure/vmss/#az-vmss-create) . Poniższy przykład obejmuje tworzenie maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* przypisany systemowo zarządzanych tożsamości, zgodnie z żądaniem `--assign-identity` parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamość zarządzana na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli chcesz włączyć przypisany systemowo tożsamości zarządzanego na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać az vmss tożsamości](/cli/azure/vmss/identity/#az-vmss-identity-assign) polecenie, aby włączyć system przypisane zarządzanych tożsamości do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, które nie wymaga tożsamości zarządzanej przypisana przez system, ale nadal wymaga tożsamości zarządzanej przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości zarządzanej przypisana przez system, i przedstawiono w nim nie przypisanych do użytkowników zarządzanych tożsamości, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` jest uwzględniana wielkość liter. Musi być mała. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

> [!NOTE]
> Jeśli aprowizowaniu tożsamość zarządzaną dla zasobów platformy Azure maszyna wirtualna rozszerzenie (przestarzałe), musisz go usunąć przy użyciu [Usuń rozszerzenie zestawu skalowania maszyn wirtualnych az](https://docs.microsoft.com/cli/azure/vm/). Aby uzyskać więcej informacji, zobacz [migracja z rozszerzenia maszyny Wirtualnej do IMDS platformy Azure do uwierzytelniania](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Przypisane przez użytkownika z tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i Usuń przypisanych przez użytkownika tożsamości zarządzanej przy użyciu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisz przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych

Ta sekcja przeprowadzi Cię przez tworzenie zestawu skalowania maszyn wirtualnych i przypisywanie przypisanych przez użytkownika tożsamości zarządzanych do zestawu skalowania maszyn wirtualnych. Jeśli masz już zestaw skalowania maszyn wirtualnych, które chcesz użyć, Pomiń tę sekcję i przejdź do następnego.

1. Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć. Tworzenie [grupy zasobów](~/articles/azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania przypisanych przez użytkownika tożsamości zarządzanej przy użyciu [Tworzenie grupy az](/cli/azure/group/#az-group-create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpowiedź zawiera szczegóły dotyczące przypisanego przez użytkownika tożsamości zarządzanej utworzone, podobny do następującego. Zasób `id` wartość przypisana do zarządzanego tożsamości przypisanych przez użytkownika jest używany w następnym kroku.

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

3. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu [tworzenie az vmss](/cli/azure/vmss/#az-vmss-create). Poniższy przykład tworzy zestaw skalowania maszyn wirtualnych skojarzonych z nowy użytkownik przypisany zarządzaną tożsamością, określony przez `--assign-identity` parametru. Upewnij się, że parametry `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych

1. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegóły dotyczące przypisanego przez użytkownika tożsamości zarządzanej utworzone, podobny do następującego.

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

2. Przypisz przypisanych przez użytkownika tożsamości zarządzanej usługi skalowania maszyn wirtualnych można ustawić przy użyciu [przypisać az vmss tożsamości](/cli/azure/vmss/identity). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY>` Zasób tożsamości przypisanych przez użytkownika jest `name` właściwości utworzonemu w poprzednim kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika zarządzanej z zestawu skalowania maszyn wirtualnych platformy Azure

Usuwanie tożsamości przypisanych przez użytkownika zarządzanej użycia zestawu skalowania maszyn wirtualnych [Usuń az vmss tożsamości](/cli/azure/vmss/identity#az-vmss-identity-remove). Jeśli jest to jedyny użytkownik przypisany tożsamości przypisane do zestawu skalowania maszyn wirtualnych, zarządzanej `UserAssigned` zostaną usunięte z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VIRTUAL MACHINE SCALE SET NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY>` Zostaną przypisane do użytkowników zarządzanych tożsamości `name` właściwość, która znajduje się w sekcji tożsamości maszyny wirtualnej zestawu skalowania przy użyciu `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli zestaw skalowania maszyn wirtualnych nie ma przypisany systemowo zarządzanych tożsamości i chcesz usunąć wszystkich przypisanych do użytkowników zarządzanych tożsamości z niego, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` jest uwzględniana wielkość liter. Musi być mała.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli w zestawie skalowania maszyn wirtualnych znajdują się oba przypisana przez system i tożsamości przypisanych przez użytkownika zarządzanego, można usunąć wszystkich tożsamości przypisanych przez użytkownika, przełączając się używać tylko przypisany systemowo zarządzanych tożsamości. Użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Kolejne kroki

- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Dla pełnej platformy Azure szybkiego startu tworzenia zestawu skalowania maszyn wirtualnych, zobacz: 

  - [Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















