---
title: Jak skonfigurować systemu i użytkownika przypisane tożsamości na maszynie Wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika przypisuje tożsamości na maszynie Wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 0f167fef2303ff3b552b9924db3eae9a07c1cb69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Konfigurowanie zarządzanych tożsamości usługi (MSI) na maszynie Wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak wykonać następujące operacje zarządzane tożsamości usługi na maszynie Wirtualnej platformy Azure, przy użyciu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie systemu przypisane tożsamości na maszynie Wirtualnej platformy Azure
- Dodawanie i usuwanie użytkownika przypisanego tożsamości na maszynie Wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

    - Użyj [powłoki chmury Azure](../../cloud-shell/overview.md) z portalu Azure (zobacz następną sekcję).
    - Użyć osadzonego powłoki chmury Azure za pośrednictwem "Spróbuj on" przycisk, znajdujący się w prawym górnym rogu każdej blok kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>System przypisane tożsamości

W tej sekcji możesz informacje o sposobie włączania i wyłączania systemu przypisane tożsamości na maszynie Wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Włącz system przypisane tożsamości podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć maszyny Wirtualnej platformy Azure w systemie przypisane włączone tożsamości:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz wdrożyć maszyny Wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania maszyny Wirtualnej i powiązanych zasobów, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Ten krok można pominąć, jeśli masz już grupę zasobów, które chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz maszynę Wirtualną przy użyciu [tworzenia maszyny wirtualnej az](/cli/azure/vm/#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* o tożsamości systemu przypisane, zgodnie z żądaniem `--assign-identity` parametru. `--admin-username` i `--admin-password` parametry Określ konto użytkownika administracyjnego nazwę i hasło do logowania w maszynie wirtualnej. Zaktualizować te wartości jako odpowiednie dla danego środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włącz system przypisane tożsamości na istniejącej maszynie Wirtualnej Azure

Jeśli musisz włączyć tożsamość systemu przypisane na istniejącej maszyny Wirtualnej:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, który zawiera maszyny Wirtualnej. Upewnij się, Twoje konto należy do roli, która umożliwia również uprawnienia do zapisu na maszynie Wirtualnej, takie jak "Współautora maszyny wirtualnej":

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać tożsamość maszyny wirtualnej az](/cli/azure/vm/identity/#az_vm_identity_assign) z `identity assign` polecenia Włącz tożsamości systemu przypisanych do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system przypisany tożsamości z maszyny Wirtualnej platformy Azure

> [!NOTE]
> Wyłączanie zarządzane tożsamość usługi z maszyny wirtualnej nie jest obecnie obsługiwane. Tymczasem można przełączać się między przypisane systemu i przypisane tożsamości użytkowników.

Jeśli masz maszyny wirtualnej, która nie będzie już potrzebował systemu przypisane tożsamości, ale nadal należy przypisać tożsamości użytkownika, użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Aby usunąć rozszerzenia maszyny Wirtualnej MSI użytkownika `-n ManagedIdentityExtensionForWindows` lub `-n ManagedIdentityExtensionForLinux` przełącznik (w zależności od typu maszyny Wirtualnej) z [Usuwanie rozszerzenia maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Tożsamość użytkownika przypisane

W tej sekcji dowiesz się, jak dodać i usunąć użytkownika przypisany tożsamości z maszyny Wirtualnej platformy Azure, przy użyciu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Przypisać użytkownicy przypisani tożsamości podczas tworzenia maszyny Wirtualnej platformy Azure

W tej sekcji przedstawiono tworzenie maszyny wirtualnej za pomocą przypisania przypisane tożsamości użytkownika. Jeśli masz już maszyny Wirtualnej, którego chcesz użyć, Pomiń tę sekcję i przejdź do następnej.

1. Ten krok można pominąć, jeśli masz już grupę zasobów, które chcesz użyć. Utwórz [grupy zasobów](~/articles/azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania programu MSI przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<LOCATION>` wartości parametrów z własne wartości. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz użytkownika przypisane przy użyciu tożsamości [utworzenia tożsamości az](/cli/azure/identity#az_identity_create).  `-g` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości i `-n` parametr określa jego nazwę.
    
    > [!IMPORTANT]
    > Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g myResourceGroup -n myUserAssignedIdentity
    ```
Odpowiedź zawiera szczegóły dotyczące tożsamości użytkownika z przypisanym utworzone, podobny do następującego. Wartość identyfikatora zasobu, które są przypisane do użytkownika z przypisanym tożsamości jest używana w następnym kroku.

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

3. Utwórz maszynę Wirtualną przy użyciu [tworzenia maszyny wirtualnej az](/cli/azure/vm/#az_vm_create). Poniższy przykład tworzy skojarzone z nową tożsamość użytkownika z przypisanym określoną przez maszynę Wirtualną `--assign-identity` parametru. Pamiętaj zastąpić `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, i `<MSI ID>` wartości parametrów z własne wartości. Aby uzyskać `<MSI ID>`, użyj zasobu tożsamość użytkownika z przypisanym `id` właściwości utworzonej w poprzednim kroku: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Przypisać użytkownicy przypisani tożsamości do istniejącej maszyny Wirtualnej Azure

1. Utwórz użytkownika przypisane przy użyciu tożsamości [utworzenia tożsamości az](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości i `-n` parametr określa jego nazwę. Pamiętaj zastąpić `<RESOURCE GROUP>` i `<MSI NAME>` wartości parametrów z własne wartości:

    > [!IMPORTANT]
    > Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Odpowiedź zawiera szczegóły dotyczące użytkownika z przypisanym MSI utworzone, podobny do następującego. Zasób `id` wartość przypisana do tożsamości użytkownika przypisany jest używana w następnym kroku.

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

2. Przypisz tożsamości użytkownika przypisanego do przy użyciu maszyny Wirtualnej [przypisać tożsamość maszyny wirtualnej az](/cli/azure/vm#az-vm-identity-assign). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów z własne wartości. `<MSI ID>` Będzie zasobów tożsamość użytkownika z przypisanym `id` właściwości, utworzonym w poprzednim kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Usuń użytkownika przypisanego tożsamości z maszyny Wirtualnej platformy Azure

> [!NOTE]
> Usuwanie wszystkich tożsamości przypisane przez użytkownika z maszyny wirtualnej obecnie nie jest obsługiwane, chyba że masz przypisane tożsamości systemu.

Jeśli maszyna wirtualna ma wiele tożsamości przypisane przez użytkownika, możesz usunąć wszystkie z wyjątkiem ostatniego go przy użyciu [Usuń tożsamość maszyny wirtualnej az](/cli/azure/vm#az-vm-identity-remove). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów z własne wartości. `<MSI NAME>` Będzie tożsamość użytkownika z przypisanym `name` właściwości, które można znaleźć w sekcji tożsamości maszynę Wirtualną przy użyciu przez `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Maszyna wirtualna ma przypisany systemu i przypisać tożsamości użytkownika, po usunięciu wszystkich użytkowników przypisanych tożsamości przełączając na system tylko przypisane. Użyj następującego polecenia:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Zawartość pokrewna
- [Omówienie tożsamość usługi zarządzanej](overview.md)
- Pełna tworzenia maszyny Wirtualnej platformy Azure — przewodniki szybkiego startu dla: 
  - [Utwórz maszynę wirtualną systemu Windows z interfejsu wiersza polecenia](../../virtual-machines/windows/quick-create-cli.md)  
  - [Utwórz maszynę wirtualną systemu Linux z interfejsu wiersza polecenia](../../virtual-machines/linux/quick-create-cli.md) 

















