---
title: Jak skonfigurować systemowych i użytkownika przypisane tożsamości na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika tożsamości przypisanych przez na maszynie Wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: db32f56c55f189001e51a727ca6b5703e82dafe4
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904001"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Konfigurowanie tożsamości usługi zarządzanej (MSI) na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej udostępnia usługi platformy Azure przy użyciu automatycznie zarządzanych tożsamości w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości usługi zarządzanej na Maszynie wirtualnej platformy Azure, przy użyciu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie systemu przypisane tożsamość na Maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie użytkownik, któremu przypisano tożsamość na Maszynie wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

    - Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
    - Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji dowiesz się, jak włączyć i wyłączyć system przypisane tożsamość na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć Maszynę wirtualną platformy Azure z systemem przypisane włączono tożsamość:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure za pomocą [az login](/cli/azure/reference-index#az_login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure w ramach której chcesz wdrożyć maszynę Wirtualną:

   ```azurecli-interactive
   az login
   ```

2. Tworzenie [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania maszyny Wirtualnej i jej powiązanymi zasobami, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz maszynę Wirtualną przy użyciu [tworzenie az vm](/cli/azure/vm/#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu tożsamości przypisanej w systemie, zgodnie z żądaniem `--assign-identity` parametru. `--admin-username` i `--admin-password` parametry określają konta użytkownika administracyjnego nazwy i hasła do logowania w maszynie wirtualnej. Zaktualizuj te wartości jako odpowiednie dla danego środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włącz system przypisane tożsamości istniejącej maszyny wirtualnej platformy Azure

Jeśli potrzebujesz umożliwić tożsamości przypisanej w systemie istniejącej maszyny wirtualnej:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure za pomocą [az login](/cli/azure/reference-index#az_login). Użyj konta, który jest skojarzony z subskrypcją platformy Azure, który zawiera maszynę Wirtualną. Upewnij się, Twoje konto należy do roli, który zapewnia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautor maszyny wirtualnej":

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać tożsamość maszyny wirtualnej az](/cli/azure/vm/identity/#az_vm_identity_assign) z `identity assign` polecenie Włącz tożsamości przypisanej w systemie do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system tożsamości przypisanej w Maszynie wirtualnej platformy Azure

> [!NOTE]
> Wyłączanie tożsamości usługi zarządzanej z maszyny wirtualnej nie jest obecnie obsługiwane. W międzyczasie można przełączać się między przypisana przez System i tożsamości przypisanych użytkowników.

Jeśli masz maszyny wirtualnej, która nie wymaga systemu tożsamości przypisanej, ale nadal wymaga tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Aby usunąć rozszerzenie tożsamości usługi Zarządzanej maszyny Wirtualnej użytkownika `-n ManagedIdentityExtensionForWindows` lub `-n ManagedIdentityExtensionForLinux` przełącznik (w zależności od typu maszyny Wirtualnej) z [Usuń rozszerzenie maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Tożsamość przypisany użytkownik

W tej sekcji dowiesz się, jak dodać i usunąć użytkownika z tożsamości przypisanej w Maszynie wirtualnej platformy Azure, przy użyciu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure

W tej sekcji opisano proces tworzenia maszyny wirtualnej z przypisaniem tożsamości przypisanych przez użytkownika. Jeśli masz już maszynę Wirtualną, której chcesz użyć, Pomiń tę sekcję i przejdź do następnego.

1. Można pominąć ten krok, jeśli masz już grupę zasobów, których chcesz użyć. Tworzenie [grupy zasobów](~/articles/azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania usługi MSI przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Koniecznie Zastąp `<RESOURCE GROUP>` i `<LOCATION>` wartości parametrów własnymi wartościami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz użytkownika przypisane przy użyciu tożsamości [Utwórz tożsamość az](/cli/azure/identity#az_identity_create).  `-g` Parametr określa grupy zasobów, w której został utworzony tożsamości przypisanych przez użytkownika, a `-n` parametr określa jej nazwę.    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
Odpowiedź zawiera szczegóły dotyczące utworzonej, podobne do następujących tożsamości przypisanych przez użytkownika. Wartość identyfikatora zasobu, które są przypisane do tożsamości przypisanych przez użytkownika jest używany w następnym kroku.

```json
{
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
}
```

3. Utwórz maszynę Wirtualną przy użyciu [tworzenie az vm](/cli/azure/vm/#az_vm_create). Poniższy przykład obejmuje tworzenie maszyny Wirtualnej skojarzone z nowej tożsamości przypisanych przez użytkownika określony przez `--assign-identity` parametru. Koniecznie Zastąp `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, i `<MSI ID>` wartości parametrów własnymi wartościami. Aby uzyskać `<MSI ID>`, użyj tożsamości przypisanych przez użytkownika zasobu `id` właściwości utworzonej w poprzednim kroku: 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej do istniejącej maszyny Wirtualnej platformy Azure

1. Utwórz użytkownika przypisane przy użyciu tożsamości [Utwórz tożsamość az](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupy zasobów, w której został utworzony tożsamości przypisanych przez użytkownika, a `-n` parametr określa jej nazwę. Koniecznie Zastąp `<RESOURCE GROUP>` i `<MSI NAME>` wartości parametrów własnymi wartościami:

    > [!IMPORTANT]
    > Tworzenie użytkownika przypisanego tożsamościami przy użyciu znaków specjalnych (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdź aktualizacje.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Odpowiedź zawiera szczegóły dotyczące użytkownika z przypisanym utworzone, podobny do następującego pliku MSI. Zasób `id` przypisane do tożsamości przypisanych przez użytkownika jest wykorzystywana w następnym kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Przypisz tożsamości przypisanych przez użytkownika do maszyny Wirtualnej za pomocą [przypisać tożsamość maszyny wirtualnej az](/cli/azure/vm#az-vm-identity-assign). Koniecznie Zastąp `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów własnymi wartościami. `<MSI ID>` Będzie zasobów tożsamości przypisanych przez użytkownika `id` właściwości utworzonemu w poprzednim kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Usuń użytkownika z tożsamości przypisanej w Maszynie wirtualnej platformy Azure

> [!NOTE]
> Usuwanie wszystkich tożsamości przypisanych przez użytkownika z maszyny wirtualnej aktualnie nie jest obsługiwane, chyba że używany jest system tożsamości przypisanej.

Jeśli maszyna wirtualna ma wiele tożsamości przypisanych przez użytkownika, możesz usunąć wszystkie oprócz ostatnim miejscu przy użyciu [Usuń tożsamość maszyny wirtualnej az](/cli/azure/vm#az-vm-identity-remove). Koniecznie Zastąp `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów własnymi wartościami. `<MSI NAME>` Będzie tożsamości przypisanych przez użytkownika `name` właściwości, który można znaleźć w sekcji tożsamości maszyny Wirtualnej przy użyciu przez `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Jeśli maszyna wirtualna ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, możesz usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisane. Użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Powiązana zawartość
- [Omówienie usługi zarządzanej tożsamości usługi](overview.md)
- Pełne tworzenie maszyny Wirtualnej platformy Azure przewodników Szybki Start zobacz: 
  - [Utwórz maszynę wirtualną Windows za pomocą interfejsu wiersza polecenia](../../virtual-machines/windows/quick-create-cli.md)  
  - [Utwórz maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md) 

















