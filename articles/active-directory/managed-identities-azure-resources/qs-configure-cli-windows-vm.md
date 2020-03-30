---
title: Konfigurowanie tożsamości zarządzanych na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — usługi Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez użytkownika na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244149"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule przy użyciu interfejsu wiersza polecenia platformy Azure, dowiesz się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie Wirtualnej platformy Azure:

- Włączanie i wyłączanie tożsamości zarządzanej przypisanej przez system na maszynie Wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie Wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, dostępne są trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) z witryny Azure portal (zobacz następną sekcję).
    - Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli) jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 
      
      > [!NOTE]
      > Polecenia zostały zaktualizowane w celu odzwierciedlenia najnowszej wersji [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamości zarządzanej przypisanej przez system na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną do systemu, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć maszynę wirtualną:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az-vm-create). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z tożsamością `--assign-identity` zarządzaną przypisaną do systemu, zgodnie z żądaniem parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej do systemu na istniejącej maszynie wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure zawierającą maszynę wirtualną.

   ```azurecli-interactive
   az login
   ```

2. Użyj przypisania tożsamości maszyny `identity assign` [wirtualnej az](/cli/azure/vm/identity/) za pomocą polecenia, włącz tożsamość przypisaną systemowi do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Wyłączanie tożsamości przypisanej systemowi z maszyny Wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości przypisanej do systemu, ale nadal potrzebuje tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszynę wirtualną, która nie potrzebuje już tożsamości przypisanej do systemu i nie ma tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W `none` wartości jest rozróżniana wielkość liter. Musi być małe litery. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika z maszyny Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny Wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej podczas jej tworzenia, konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Możesz pominąć ten krok, jeśli masz już grupę zasobów, której chcesz użyć. Utwórz [grupę zasobów](~/articles/azure-resource-manager/management/overview.md#terminology) do przechowywania i wdrażania tożsamości zarządzanej przypisanej przez użytkownika, używając [tworzenia grupy az](/cli/azure/group/#az-group-create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpowiedź zawiera szczegółowe informacje dotyczące utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. Wartość identyfikatora zasobu przypisana do tożsamości zarządzanej przypisanej przez użytkownika jest używana w następnym kroku.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az-vm-create). Poniższy przykład tworzy maszynę wirtualną skojarzoną z nową tożsamością `--assign-identity` przypisaną przez użytkownika, zgodnie z parametrem. Upewnij się, że parametry `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny Wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Utwórz tożsamość przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której tworzona jest `-n` tożsamość przypisana przez użytkownika, a parametr określa jego nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    > [!IMPORTANT]
    > Tworzenie tożsamości zarządzanych przypisanych przez użytkownika ze znakami specjalnymi (tj. podkreśleniem) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumeryczne. Wracaj tutaj, aby zapoznać się z aktualizacjami.  Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegółowe informacje dotyczące utworzonej tożsamości zarządzanej przypisanej przez użytkownika, podobne do następujących. 

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

2. Przypisz tożsamość przypisaną przez użytkownika do maszyny Wirtualnej przy użyciu [przypisania tożsamości az vm](/cli/azure/vm). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. Jest `<USER ASSIGNED IDENTITY NAME>` to właściwość zasobu tożsamości `name` zarządzanej przypisanej przez użytkownika, utworzona w poprzednim kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny Wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 

Jeśli jest to jedyna przypisana przez użytkownika tożsamość `UserAssigned` zarządzana przypisana do maszyny wirtualnej, zostanie usunięta z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. Będzie `<USER ASSIGNED IDENTITY>` to właściwość tożsamości przypisanej `name` przez użytkownika, którą można znaleźć w sekcji `az vm identity show`tożsamości maszyny wirtualnej przy użyciu:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli maszyna wirtualna nie ma tożsamości zarządzanej przypisanej do systemu i chcesz usunąć z niej wszystkie tożsamości przypisane przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> W `none` wartości jest rozróżniana wielkość liter. Musi być małe litery.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli maszyna wirtualna ma zarówno tożsamości przypisane do systemu, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości przypisane przez użytkownika, przełączając się na użycie tylko przypisanych do systemu. Użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Następne kroki
- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure](overview.md)
- Aby uzyskać pełną platformę Szybkiego startu tworzenia maszyn wirtualnych platformy Azure, zobacz: 
  - [Tworzenie maszyny wirtualnej systemu Windows za pomocą interfejsu wiersza polecenia](../../virtual-machines/windows/quick-create-cli.md)  
  - [Tworzenie maszyny wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md) 

















