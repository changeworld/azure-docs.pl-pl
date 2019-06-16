---
title: Jak skonfigurować system i zarządzanych tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i zarządzanych tożsamości przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c61313190615c2f30a7d37202bc0f9bcf14d800
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112877"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu wiersza polecenia platformy Azure, nauczysz się wykonywać następujące zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w Maszynie wirtualnej platformy Azure:

- Włączanie i wyłączanie przypisany systemowo tożsamości zarządzanej maszyny wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanych przez użytkownika na Maszynie wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnicę między przypisana przez system i przypisanych przez użytkownika tożsamości zarządzanej](overview.md#how-does-it-work)** .
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:
    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) Jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 
      
      > [!NOTE]
      > Polecenia zostały zaktualizowanie, aby odzwierciedlić najnowszą wersję [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisany systemowo tożsamości zarządzanej na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć Maszynę wirtualną platformy Azure z przypisany systemowo zarządzaną tożsamością, włączone, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć maszynę wirtualną:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupę zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az-vm-create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przypisany systemowo zarządzanych tożsamości, zgodnie z żądaniem `--assign-identity` parametru. Parametry `--admin-username` i `--admin-password` określają konto nazwy użytkownika administracyjnego i hasła na potrzeby logowania do maszyny wirtualnej. Zaktualizuj te wartości zgodnie z wymaganiami środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Włącz przypisany systemowo tożsamość zarządzaną istniejącej maszyny wirtualnej platformy Azure

Aby włączyć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną.

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać tożsamość maszyny wirtualnej az](/cli/azure/vm/identity/) z `identity assign` polecenie Włącz tożsamości przypisanych przez system do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Wyłączanie tożsamości przypisanych przez system z Maszyną wirtualną platformy Azure

Aby wyłączyć przypisany systemowo tożsamości zarządzanej maszyny wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli.  Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

Jeśli masz maszynę wirtualną, która nie wymaga tożsamości przypisanych przez system, ale nadal wymaga tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Jeśli masz maszyny wirtualnej, która nie wymaga tożsamości przypisanych przez system i ma nie tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` jest uwzględniana wielkość liter. Musi być mała. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```
> [!NOTE]
> Jeśli aprowizowaniu tożsamość zarządzaną dla zasobów platformy Azure maszyna wirtualna rozszerzenie (przestarzałe), musisz go usunąć przy użyciu [Usuń rozszerzenie maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm/). Aby uzyskać więcej informacji, zobacz [migracja z rozszerzenia maszyny Wirtualnej do IMDS platformy Azure do uwierzytelniania](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodawać i usuwać przypisane przez użytkownika tożsamości zarządzanej maszyny wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisz przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby przypisać tożsamości przypisanych przez użytkownika z maszyną wirtualną podczas jej tworzenia, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć. Tworzenie [grupy zasobów](~/articles/azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania przypisanych przez użytkownika tożsamości zarządzanej przy użyciu [Tworzenie grupy az](/cli/azure/group/#az-group-create). Upewnij się, że parametry `<RESOURCE GROUP>` i `<LOCATION>` zostały zastąpione własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz tożsamość zarządzaną przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` określa grupę zasobów, w której zostanie utworzona tożsamość zarządzana przypisana przez użytkownika, a parametr `-n` określa jej nazwę.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpowiedź zawiera szczegóły dotyczące przypisanego przez użytkownika tożsamości zarządzanej utworzone, podobny do następującego. Wartość Identyfikatora zasobu, które są przypisane do tożsamości zarządzanej przypisanych przez użytkownika jest używany w następnym kroku.

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

3. Utwórz maszynę wirtualną przy użyciu polecenia [az vm create](/cli/azure/vm/#az-vm-create). Poniższy przykład obejmuje tworzenie maszyny Wirtualnej skojarzone z nowej tożsamości przypisanych przez użytkownika określony przez `--assign-identity` parametru. Upewnij się, że parametry `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącej maszyny Wirtualnej platformy Azure

Aby przypisać tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) przypisań ról. Nie dodatkowych Azure przypisań ról katalogu usługi AD są wymagane.

1. Utwórz tożsamość przypisaną przez użytkownika za pomocą polecenia [az identity create](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupę zasobów, w której zostanie utworzony tożsamości przypisanych przez użytkownika, a `-n` parametr określa jej nazwę. Upewnij się, że parametry `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` zostały zastąpione własnymi wartościami:

    > [!IMPORTANT]
    > Tworzenie zarządzanych tożsamości przypisanych przez użytkownika przy użyciu znaków specjalnych (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Wracaj tutaj, aby zapoznać się z aktualizacjami.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpowiedź zawiera szczegóły dotyczące przypisanego przez użytkownika tożsamości zarządzanej utworzone, podobny do następującego. 

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

2. Przypisz tożsamości przypisanych przez użytkownika do maszyny Wirtualnej za pomocą [przypisać tożsamość maszyny wirtualnej az](/cli/azure/vm). Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY NAME>` Jest przypisanych do użytkowników zarządzanych tożsamości zasobu `name` właściwości utworzonemu w poprzednim kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuń przypisanych przez użytkownika tożsamości zarządzanej maszyny wirtualnej platformy Azure

Aby usunąć tożsamości przypisanych przez użytkownika do maszyny Wirtualnej, Twoje konto musi [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) przypisania roli. 

Jeśli jest to jedyny użytkownik przypisany tożsamości przypisanej do maszyny wirtualnej zarządzanej `UserAssigned` zostaną usunięte z wartości typu tożsamości.  Upewnij się, że parametry `<RESOURCE GROUP>` i `<VM NAME>` zostały zastąpione własnymi wartościami. `<USER ASSIGNED IDENTITY>` Będzie tożsamości przypisanych przez użytkownika `name` właściwość, która znajduje się w sekcji tożsamości maszyny wirtualnej przy użyciu `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Jeśli maszyna wirtualna nie ma przypisany systemowo tożsamości zarządzanej i chcesz usunąć wszystkie tożsamości przypisanych przez użytkownika z niego, użyj następującego polecenia:

> [!NOTE]
> Wartość `none` jest uwzględniana wielkość liter. Musi być mała.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Jeśli maszyna wirtualna ma zarówno przypisany systemowo i tożsamości przypisanych przez użytkownika, można usunąć wszystkich tożsamości przypisanych przez użytkownika, przełączając się używać tylko przypisana przez system. Użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Kolejne kroki
- [Zarządzanych tożsamości dla zasobów platformy Azure — omówienie](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz: 
  - [Utwórz maszynę wirtualną Windows za pomocą interfejsu wiersza polecenia](../../virtual-machines/windows/quick-create-cli.md)  
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md) 

















