---
title: Jak skonfigurować systemowych i użytkownika przypisane tożsamości na platformie Azure zestawu skalowania maszyn wirtualnych przy użyciu usługi REST
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika tożsamości przypisanych przez na zestawu skalowania maszyn wirtualnych platformy Azure przy użyciu programu CURL wykonywania wywołań interfejsu API REST.
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: c4ccd75f679e58dd42f136bf1199eda88dc3f087
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431719"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurowanie tożsamości zarządzanej na zestawie skalowania maszyn wirtualnych przy użyciu wywołań interfejsu API REST

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość zarządzana udostępnia usługi platformy Azure przy użyciu tożsamości, system automatycznie zarządzane w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości zarządzanej na zestaw skalowania maszyn wirtualnych przy użyciu programu CURL w celu wykonywania wywołań do punktu końcowego usługi Azure Resource Manager REST:

- Włączanie i wyłączanie tożsamości przypisanej w systemie w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości przypisanych przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Aby utworzyć zestaw skalowania maszyn wirtualnych zestawu i Włącz i Usuń systemu i/lub użytkownik przypisany tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli do utworzenia tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Jeśli używasz Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal.
- [Instalowanie lokalnej konsoli wiersza polecenia platformy Azure](/azure/install-azure-cli), jeśli używasz [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [dystrybucji systemu Linux, OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz lokalnej konsoli wiersza polecenia platformy Azure, zaloguj się do platformy Azure za pomocą `az login` przy użyciu konta, które jest skojarzone z platformą Azure tożsamości przypisanych subskrypcji, które chcesz zarządzać system lub użytkownik.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisanej tożsamość zestawu, używając programu CURL w celu wykonywania wywołań do punktu końcowego usługi Azure Resource Manager REST skalowania maszyn wirtualnych w systemie.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie podczas tworzenia zestawu skalowania maszyn wirtualnych

Do utworzenia maszyny wirtualnej zestawu skalowania przy użyciu przypisanej włączono tożsamość w systemie, należy utworzyć zestaw skalowania maszyn wirtualnych i pobrać token dostępu, aby za pomocą programu CURL wywołanie punktu końcowego usługi Resource Manager z systemu przypisane wartości typu tożsamości.

1. Tworzenie [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanymi zasobami, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Tworzenie [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) skalowania maszyny wirtualnej można ustawić:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST. Poniższy przykład obejmuje tworzenie maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* w *myResourceGroup* przy użyciu tożsamości przypisanej w systemie, jak wskazano w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Włączanie tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

Aby włączyć system przypisane tożsamości na podstawie istniejącego zestawu skalowania maszyn wirtualnych, należy uzyskać token dostępu, a następnie wywołać punkt końcowy REST usługi Resource Manager, można zaktualizować typu tożsamości za pomocą programu CURL.

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST, do obsługi tożsamości przypisanej w systemie w zestawie skalowania maszyn wirtualnych ustawienia określone w treści żądania przez wartość `{"identity":{"type":"SystemAssigned"}` dla maszyny wirtualnej zestawu skalowania o nazwie  *myVMSS*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, nie usuwaj żadnych istniejących użytkownik, któremu przypisano zarządzanych tożsamości, które są przypisane do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę tożsamości przypisanych przez użytkownika za pomocą tego polecenia CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli masz żadnych tożsamości przypisanych przez użytkownika, przypisane do zestawu, jak wskazano w skalowania maszyn wirtualnych `identity` wartość w odpowiedzi, przejdź do kroku 3 pokazuje, jak zachować tożsamości przypisanych przez użytkownika podczas włączania tożsamości przypisanej w systemie w sieci wirtualnej zestaw skalowania maszyn.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Aby włączyć system przypisane tożsamość maszyny wirtualnej zestawu skalowania przy użyciu istniejących tożsamości przypisanych przez użytkownika, należy dodać `SystemAssigned` do `type` wartości.  
   
   Na przykład jeśli zestaw skalowania maszyn wirtualnych ma tożsamości przypisanych przez użytkownika `ID1` i `ID2` do niej przypisany, i chcesz dodać tożsamości przypisanej w systemie do zestawu skalowania maszyn wirtualnych, użyj następującego wywołania programu CURL. Zastąp `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` przy użyciu wartości odpowiednich dla danego środowiska.
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości przypisanej w systemie z zestawu skalowania maszyn wirtualnych platformy Azure

Aby wyłączyć tożsamości przypisanej w systemie, na podstawie istniejącego zestawu skalowania maszyn wirtualnych, musisz uzyskiwanie tokenu dostępu, a następnie wywołać punkt końcowy REST usługi Resource Manager, można zaktualizować typu tożsamości, aby za pomocą programu CURL `None`.

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizowanie maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST można wyłączyć tożsamości przypisanej w systemie.  Poniższy przykład wyłącza tożsamości przypisanej w systemie, jak wskazano w treści żądania przez wartość `{"identity":{"type":"None"}}` z maszyny wirtualnej zestawu skalowania o nazwie *myVMSS*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, nie usuwaj żadnych istniejących użytkownik, któremu przypisano zarządzanych tożsamości, które są przypisane do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę tożsamości przypisanych przez użytkownika za pomocą tego polecenia CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli żaden użytkownik przypisany tożsamości przypisane do zestawu skalowania maszyn wirtualnych, przejdź do kroku 3, który pokazuje, jak zachować użytkownika tożsamości przypisanych przez podczas usuwania systemu tożsamości przypisanej w zestawie skalowania maszyn wirtualnych.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Aby usunąć przypisanej tożsamości z zestawu skalowania maszyn wirtualnych, które ma tożsamości przypisanych przez użytkownika w systemie, Usuń `SystemAssigned` z `{"identity":{"type:" "}}` wartość przy zachowaniu `UserAssigned` wartość i `identityIds` tablicy określa jakie tożsamości przypisanych przez użytkownika są przypisywane do zestawu skalowania maszyn wirtualnych.

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak dodawać i usuwać tożsamości na maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do nawiązywania połączeń z punktem końcowym usługi Azure Resource Manager REST przypisanych przez użytkownika.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisz tożsamości przypisanych przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Tworzenie [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) skalowania maszyny wirtualnej można ustawić:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie tożsamości przypisanych przez użytkownika, korzystając z instrukcji w tym miejscu: [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST. Poniższy przykład obejmuje tworzenie maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* w grupie zasobów *myResourceGroup* przy użyciu tożsamości przypisanych przez użytkownika `ID1`, jak wskazano w treści żądania przez wartość `"identity":{"type":"UserAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisz tożsamości przypisanych przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Tworzenie tożsamości przypisanych przez użytkownika, korzystając z instrukcji w tym miejscu [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby upewnić się, że nie usuwaj istniejącego użytkownika lub systemu przypisaną zarządzanych tożsamości, które są przypisane do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę tożsamości ustawionych typów przypisanych do skalowania maszyn wirtualnych przy użyciu następującego polecenia CURL. Jeśli masz zarządzanych tożsamości przypisane do zestawu skalowania maszyn wirtualnych, są one wyświetlane w obszarze w `identity` wartość.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Jeśli nie masz, wszyscy użytkownicy lub przypisane do zestawu skalowania maszyn wirtualnych tożsamości przypisanych przez system, umożliwia następujące polecenie CURL wywołuj punkty końcowe usługi Azure Resource Manager REST, aby przypisać pierwszy użytkownik tożsamości przypisanej do zestawu skalowania maszyn wirtualnych.  Jeśli użytkownik lub system przypisane identity(s) przypisane do zestawu skalowania maszyn wirtualnych, przejdź do kroku 5, który pokazuje, jak dodać wiele tożsamości przypisanych przez użytkownika do skali maszyny wirtualnej Ustaw zachowując tożsamości przypisanej w systemie.

   Poniższy przykład przypisuje tożsamości przypisanych przez użytkownika, `ID1` do maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* w grupie zasobów *myResourceGroup*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Jeśli masz użytkownika lub tożsamości przypisanej w systemie przypisane do zestawu skalowania maszyn wirtualnych, należy dodać nowej tożsamości przypisanych przez użytkownika w celu `identityIDs` tablicy przy zachowaniu użytkownika i tożsamości przypisanej w systemie, które są obecnie przypisane do wirtualnego zestaw skalowania maszyn.

   Na przykład jeśli masz system przypisać tożsamości i tożsamości przypisanych przez użytkownika `ID1` aktualnie przypisane do zestawu skalowania maszyny wirtualnej i chcesz dodać tożsamość użytkownika `ID2` , użyj następującego polecenia CURL. Zastąp `<ACCESS TOKEN>` wartością otrzymany w krokach zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości przypisanych przez użytkownika z zestawu skalowania maszyn wirtualnych

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych w systemie przypisane tożsamość zarządzaną.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usuwaj żadnych istniejących użytkownik, któremu przypisano zarządzanych tożsamości, które chcesz zachować przypisane do zestawu skalowania maszyn wirtualnych lub usuń system tożsamości przypisanej, musisz listy zarządzanych tożsamości za pomocą następującego polecenia CURL: 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Jeśli masz zarządzane tożsamości przypisanych do maszyny Wirtualnej, są one wymienione w odpowiedzi `identity` wartość. 
    
   Na przykład, jeśli masz tożsamości przypisanych przez użytkownika `ID1` i `ID2` przypisane do zestawu skalowania maszyn wirtualnych i tylko chcesz zachować `ID1` przypisane tożsamości przypisanej w systemie, należy użyć tego samego polecenia CURL, jako Przypisanie użytkownika zarządzanych tożsamości przypisanej do skalowania maszyn wirtualnych Ustaw tylko utrzymywanie `ID1` wartość i zachować `SystemAssigned` wartość. Spowoduje to usunięcie `ID2` tożsamości przypisanych przez użytkownika z skalowania maszyn wirtualnych przy zachowaniu tożsamości przypisanej w systemie.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Jeśli zestaw skalowania maszyn wirtualnych ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, należy usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisać przy użyciu następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Jeśli chcesz usunąć wszystkie zestawu skalowania maszyn wirtualnych jest jedynym użytkownikiem tożsamości przypisanych przez, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat sposobu tworzenia, listy lub usunąć użytkownika przypisane przy użyciu interfejsu REST, zobacz:

- [Tworzenie listy i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)