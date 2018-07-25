---
title: Jak skonfigurować systemowych i użytkownika przypisane tożsamości na platformie Azure zestawu skalowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika przypisać tożsamości na platformie Azure zestawu skalowania maszyn wirtualnych, przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 36df9d00d41f3c092320fa88772b41c9a41c6d8e
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237285"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurowanie maszyny wirtualnej zestawu skalowania tożsamość usługi zarządzanej (MSI) przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej w Azure maszyn wirtualnych skalowania Ustaw (zestawu skalowania maszyn wirtualnych), przy użyciu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie systemu przypisane tożsamość zestawu skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie użytkownik, któremu przypisano tożsamość zestawu skalowania maszyn wirtualnych platformy Azure


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Aby utworzyć zestaw skalowania maszyn wirtualnych zestawu i Włącz i Usuń systemu i/lub użytkownik przypisany tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli do utworzenia tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji dowiesz się, jak włączyć i wyłączyć system tożsamości przypisanej do platformy Azure zestawu skalowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Aby utworzyć maszyny wirtualnej zestawu skalowania przy użyciu systemu przypisane włączono tożsamość:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az_login). Użyj konta które jest skojarzone z subskrypcją platformy Azure w ramach której chcesz wdrożyć zestaw skalowania maszyn wirtualnych:

   ```azurecli-interactive
   az login
   ```

2. Tworzenie [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanymi zasobami, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu [tworzenie az vmss](/cli/azure/vmss/#az_vmss_create) . Poniższy przykład obejmuje tworzenie maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* przy użyciu tożsamości przypisanej w systemie, zgodnie z żądaniem `--assign-identity` parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli potrzebujesz umożliwić tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az_login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera zestaw skalowania maszyn wirtualnych.

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać az vmss tożsamości](/cli/azure/vmss/identity/#az_vmss_identity_assign) polecenie, aby umożliwić tożsamości przypisanej w systemie, istniejącej maszyny wirtualnej:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości przypisanej w systemie z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, która nie wymaga systemu tożsamości przypisanej, ale nadal wymaga tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości przypisanej w systemie i ma żaden użytkownik nie tożsamości przypisanych przez, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` jest uwzględniana wielkość liter. Musi być mała. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Aby usunąć rozszerzenie tożsamości usługi Zarządzanej maszyny Wirtualnej, użyj [Usuń az vmss tożsamości](/cli/azure/vmss/identity/#az_vmss_remove_identity) polecenie, aby usunąć tożsamości przypisanej w systemie z zestawu skalowania maszyn wirtualnych:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak włączyć i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Przypisywanie użytkownika tożsamości przypisanej podczas tworzenia zestawu skalowania maszyn wirtualnych platformy Azure

Ta sekcja przeprowadzi Cię przez tworzenie zestawu skalowania maszyn wirtualnych i przypisanie użytkownika tożsamości przypisanej do zestawu skalowania maszyn wirtualnych. Jeśli masz już VMSS, którego chcesz użyć, Pomiń tę sekcję i przejdź do następnego.

1. Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć. Tworzenie [grupy zasobów](~/articles/azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażanie Twojej tożsamości przypisanych przez użytkownika za pomocą [Tworzenie grupy az](/cli/azure/group/#az_group_create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz użytkownika przypisane przy użyciu tożsamości [Utwórz tożsamość az](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupy zasobów, w której został utworzony tożsamości przypisanych przez użytkownika, a `-n` parametr określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpowiedź zawiera szczegóły dotyczące utworzonej, podobne do następujących tożsamości przypisanych przez użytkownika. Zasób `id` przypisane do tożsamości przypisanych przez użytkownika jest wykorzystywana w następnym kroku.

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

3. Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu [tworzenie az vmss](/cli/azure/vmss/#az-vmss-create). Poniższy przykład obejmuje tworzenie zestawu skalowania maszyn wirtualnych skojarzonych z nowej tożsamości przypisanych przez użytkownika określony przez `--assign-identity` parametru. Upewnij się, że parametry `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY ID>` zostały zastąpione własnymi wartościami. Aby uzyskać `<USER ASSIGNED IDENTITY ID>`, użyj tożsamości przypisanych przez użytkownika zasobu `id` właściwości utworzonej w poprzednim kroku: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej do istniejącej maszyny Wirtualnej platformy Azure

1. Utwórz użytkownika przypisane przy użyciu tożsamości [Utwórz tożsamość az](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupy zasobów, w której został utworzony tożsamości przypisanych przez użytkownika, a `-n` parametr określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    > [!IMPORTANT]
    > Tworzenie użytkownika przypisanego tożsamościami przy użyciu znaków specjalnych (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Wracaj tutaj, aby zapoznać się z aktualizacjami.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpowiedź zawiera szczegóły dotyczące utworzonej, podobne do następujących tożsamości przypisanych przez użytkownika. Zasób `id` przypisane do tożsamości przypisanych przez użytkownika jest wykorzystywana w następnym kroku.

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

2. Przypisz tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych za pomocą [przypisać az vmss tożsamości](/cli/azure/vmss/identity#az_vm_assign_identity). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VMSS NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY ID>` Będzie zasobów tożsamości przypisanych przez użytkownika `id` właściwości utworzonemu w poprzednim kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Usuwanie tożsamości przypisanych przez użytkownika użycia zestawu skalowania maszyn wirtualnych [Usuń az vmss tożsamości](/cli/azure/vmss/identity#az-vmss-identity-remove). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VMSS NAME>` zostały zastąpione własnymi wartościami. `<MSI NAME>` Będzie tożsamości przypisanych przez użytkownika `name` właściwości, który można znaleźć w sekcji tożsamości maszyny Wirtualnej przy użyciu przez `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <MSI NAME>
```

Jeśli chcesz usunąć wszystkich użytkowników tożsamości przypisanych przez z niego zestawu skalowania maszyny wirtualnej bez zainstalowanego systemu tożsamości przypisanej, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` jest uwzględniana wielkość liter. Musi być mała.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

Jeśli zestaw skalowania maszyn wirtualnych ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, możesz usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisane. Użyj następującego polecenia:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi zarządzanej tożsamości usługi](overview.md)
- Dla pełnej platformy Azure szybkiego startu tworzenia zestawu skalowania maszyn wirtualnych, zobacz: 

  - [Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















