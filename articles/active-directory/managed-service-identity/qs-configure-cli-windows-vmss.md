---
title: Jak skonfigurować systemu i użytkownika przypisane tożsamości na VMSS Azure przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika przypisuje tożsamości na VMSS Azure przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 4237371073669bd46c0da8f75998157aa54ffbf1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurowanie maszyny wirtualnej zestawu skalowania zarządzane usługi tożsamości (MSI) przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule Dowiedz się jak wykonać następujące operacje zarządzane tożsamości usługi na Azure maszyny wirtualnej skali Ustaw (VMSS), przy użyciu wiersza polecenia platformy Azure:
- Włączanie i wyłączanie tożsamość na VMSS Azure przypisanego przez system
- Dodawanie i usuwanie użytkownika przypisanego tożsamość na VMSS Azure


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli znasz zarządzane tożsamość usługi, zapoznaj się [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między systemu przypisane i przypisać tożsamość użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

- Użyj [powłoki chmury Azure](../../cloud-shell/overview.md) z portalu Azure (zobacz następną sekcję).
- Użyć osadzonego powłoki chmury Azure za pośrednictwem "Spróbuj on" przycisk, znajdujący się w prawym górnym rogu każdej blok kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>System przypisane tożsamości

W tej sekcji możesz informacje o sposobie włączania i wyłączania systemu przypisane tożsamości dla VMSS Azure przy użyciu wiersza polecenia platformy Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz tożsamość systemu przypisany podczas tworzenia zestawu skali maszyny wirtualnej platformy Azure

Aby utworzyć zestaw w systemie przypisane tożsamości włączone skalowania maszyny wirtualnej:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz wdrożyć zestaw skali maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania zestawu skali maszyny wirtualnej i powiązanych zasobów, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Ten krok można pominąć, jeśli masz już grupę zasobów, które chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz skali maszyny wirtualnej ustawić za pomocą [az vmss utworzyć](/cli/azure/vmss/#az_vmss_create) . Poniższy przykład tworzy skalowania maszyny wirtualnej, ustaw nazwane *myVMSS* o tożsamości systemu przypisane, zgodnie z żądaniem `--assign-identity` parametru. `--admin-username` i `--admin-password` parametry Określ konto użytkownika administracyjnego nazwę i hasło do logowania w maszynie wirtualnej. Zaktualizować te wartości jako odpowiednie dla danego środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz system przypisane tożsamości na podstawie istniejącego zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli musisz włączyć tożsamości systemu przypisane na podstawie istniejącego zestawu skalowania maszyny wirtualnej platformy Azure:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać tożsamość vmss az](/cli/azure/vmss/identity/#az_vmss_identity_assign) polecenie, aby włączyć tożsamością systemu przypisanych do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłącz system przypisane tożsamości z zestawu skalowania maszyny wirtualnej platformy Azure

> [!NOTE]
> Wyłączanie zarządzane tożsamość usługi z zestawu skalowania maszyn wirtualnych nie jest obecnie obsługiwane. Tymczasem można przełączać się między przypisane systemu i przypisane tożsamości użytkowników. Sprawdzanie dostępności aktualizacji.

Jeśli masz zestaw skali maszyny wirtualnej, który nie będzie już potrzebował system przypisane tożsamości, ale nadal należy przypisać tożsamości użytkownika, wykonaj następujące polecenie:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Aby usunąć rozszerzenia maszyny Wirtualnej MSI, użyj [usunąć tożsamości vmss az](/cli/azure/vmss/identity/#az_vmss_remove_identity) polecenie, aby usunąć z VMSS tożsamość systemu przypisane:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Użytkownik, któremu przypisano tożsamości

W tej sekcji dowiesz sposobu włączania i usuwanie tożsamości przypisane przez użytkownika, przy użyciu wiersza polecenia platformy Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Przypisać użytkownicy przypisani tożsamości podczas tworzenia VMSS Azure

W tej sekcji przedstawiono tworzenie VMSS i przypisanie przypisane do VMSS tożsamości użytkownika. Jeśli masz już VMSS, którego chcesz użyć, Pomiń tę sekcję i przejdź do następnej.

1. Ten krok można pominąć, jeśli masz już grupę zasobów, które chcesz użyć. Utwórz [grupy zasobów](~/articles/azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania Twoją tożsamość użytkownika z przypisanym przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<LOCATION>` wartości parametrów z własne wartości. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Utwórz użytkownika przypisane przy użyciu tożsamości [utworzenia tożsamości az](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości i `-n` parametr określa jego nazwę. Pamiętaj zastąpić `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów z własne wartości:

    > [!IMPORTANT]
    > Tworzenie tożsamości użytkowników przypisanych obsługuje tylko alfanumeryczne i łączniki (0-9 lub a-z lub A-Z lub -) znaków. Ponadto nazwa powinna być ograniczona do 24 znaków do przypisania do maszyny Wirtualnej/VMSS działała poprawnie. Sprawdzanie dostępności aktualizacji. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpowiedź zawiera szczegóły dotyczące tożsamości użytkownika z przypisanym utworzone, podobny do następującego. Zasób `id` wartość przypisana do tożsamości użytkownika przypisany jest używana w następnym kroku.

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

3. Tworzenie przy użyciu VMSS [az vmss utworzyć](/cli/azure/vmss/#az-vmss-create). Poniższy przykład tworzy VMSS, skojarzone z nową tożsamość użytkownika z przypisanym określony przez `--assign-identity` parametru. Pamiętaj zastąpić `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>`, i `<USER ASSIGNED IDENTITY ID>` wartości parametrów z własne wartości. Aby uzyskać `<USER ASSIGNED IDENTITY ID>`, użyj zasobu tożsamość użytkownika z przypisanym `id` właściwości utworzonej w poprzednim kroku: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Przypisać użytkownicy przypisani tożsamości do istniejącej maszyny Wirtualnej Azure

1. Utwórz użytkownika przypisane przy użyciu tożsamości [utworzenia tożsamości az](/cli/azure/identity#az-identity-create).  `-g` Parametr określa grupę zasobów, w której utworzono użytkownika z przypisanym tożsamości i `-n` parametr określa jego nazwę. Pamiętaj zastąpić `<RESOURCE GROUP>` i `<USER ASSIGNED IDENTITY NAME>` wartości parametrów z własne wartości:

    > [!IMPORTANT]
    > Tworzenie użytkownika z przypisanym tożsamości z znaki specjalne (np. podkreślenie) w nazwie nie jest obecnie obsługiwane. Użyj znaków alfanumerycznych. Sprawdzanie dostępności aktualizacji.  Aby uzyskać więcej informacji, zobacz [— często zadawane pytania i znane problemy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpowiedź zawiera szczegóły dotyczące tożsamości użytkownika z przypisanym utworzone, podobny do następującego. Zasób `id` wartość przypisana do tożsamości użytkownika przypisany jest używana w następnym kroku.

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

2. Przypisz tożsamości użytkownika przypisanego do przy użyciu VMSS [przypisać tożsamość vmss az](/cli/azure/vmss/identity#az_vm_assign_identity). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów z własne wartości. `<USER ASSIGNED IDENTITY ID>` Będzie zasobów tożsamość użytkownika z przypisanym `id` właściwości, utworzonym w poprzednim kroku:

    ```azurecli-interactive
    az vmss identity-assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Usuń użytkownika przypisanego tożsamości z VMSS Azure

> [!NOTE]
>  Usuwanie wszystkich tożsamości przypisane przez użytkownika z zestawu skalowania maszyn wirtualnych obecnie nie jest obsługiwane, chyba że masz przypisane tożsamości systemu. 

Jeśli Twoje VMSS ma wiele tożsamości przypisane przez użytkownika, możesz usunąć wszystkie z wyjątkiem ostatniego go przy użyciu [usunąć tożsamości vmss az](/cli/azure/vmss/identity#az-vmss-identity-remove). Pamiętaj zastąpić `<RESOURCE GROUP>` i `<VM NAME>` wartości parametrów z własne wartości. `<MSI NAME>` Jest tożsamość użytkownika z przypisanym właściwości name, który można znaleźć w sekcji tożsamości maszynę Wirtualną przy użyciu przez `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Twoje VMSS ma przypisany systemu i przypisać tożsamości użytkownika, po usunięciu wszystkich użytkowników przypisanych tożsamości przełączając na system tylko przypisane. Użyj następującego polecenia: 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie tożsamość usługi zarządzanej](overview.md)
- Pełna Azure Tworzenie szybkiego startu zestawu skalowania maszyny wirtualnej, zobacz: 

  - [Utwórz zestaw skali maszyny wirtualnej z interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















