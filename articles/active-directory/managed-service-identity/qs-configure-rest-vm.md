---
title: Jak skonfigurować systemowych i użytkownika przypisane tożsamości na Maszynie wirtualnej platformy Azure przy użyciu usługi REST
description: Krok po kroku instrukcje dotyczące konfigurowania systemu i użytkownika tożsamości przypisanych przez na Maszynie wirtualnej platformy Azure przy użyciu programu CURL wykonywania wywołań interfejsu API REST.
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
ms.openlocfilehash: 71cca8e36a319d4e74eb68044a2ae741a747c27d
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42056758"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>Konfigurowanie tożsamości zarządzanej na Maszynie wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość zarządzana udostępnia usługi platformy Azure przy użyciu tożsamości, system automatycznie zarządzane w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wykonywać następujące operacje tożsamości zarządzanej maszyny wirtualnej platformy Azure, używając programu CURL w celu wykonywania wywołań do punktu końcowego usługi Azure Resource Manager REST:

- Włączanie i wyłączanie systemu przypisane tożsamość na Maszynie wirtualnej platformy Azure
- Dodawanie i usuwanie użytkownik, któremu przypisano tożsamość na Maszynie wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jesteś zaznajomiony z tożsamości usługi zarządzanej, zapoznaj się z [sekcji Przegląd](overview.md). **Należy przejrzeć [różnica między przypisanej w systemie i tożsamości przypisanych przez użytkownika](overview.md#how-does-it-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Do wykonywania operacji zarządzania, w tym artykule, Twoje konto musi następujących przypisań ról:
    - [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) do tworzenia maszyny Wirtualnej i Włącz oraz usuwać systemowych i/lub użytkownik przypisany tożsamości zarządzanej maszyny wirtualnej platformy Azure.
    - [Współautor tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roli do utworzenia tożsamości przypisanych przez użytkownika.
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do maszyny Wirtualnej.
- Jeśli używasz Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal.
- [Instalowanie lokalnej konsoli wiersza polecenia platformy Azure](/azure/install-azure-cli), jeśli używasz [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [dystrybucji systemu Linux, OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz lokalnej konsoli wiersza polecenia platformy Azure, zaloguj się do platformy Azure za pomocą `az login` przy użyciu konta, które jest skojarzone z platformą Azure tożsamości przypisanych subskrypcji, które chcesz zarządzać system lub użytkownik.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Tożsamości przypisanej w systemie

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisanej tożsamość na Maszynie wirtualnej platformy Azure przy użyciu programu CURL w celu wykonywania wywołań do punktu końcowego usługi Azure Resource Manager REST w systemie.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Włącz system tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć Maszynę wirtualną platformy Azure przy użyciu przypisanej włączono tożsamość w systemie, należy utworzyć Maszynę wirtualną i pobrać token dostępu, aby za pomocą programu CURL wywołanie punktu końcowego usługi Resource Manager z systemu przypisane wartości typu tożsamości.

1. Utwórz [grupę zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Tworzenie [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla maszyny Wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz Maszynę wirtualną przy użyciu programu CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu tożsamości przypisanej w systemie, jak wskazano w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włącz system przypisane tożsamości istniejącej maszyny wirtualnej platformy Azure

Aby włączyć system przypisane tożsamości istniejącej maszyny wirtualnej, należy uzyskać token dostępu, a następnie wywołać punkt końcowy REST usługi Resource Manager, można zaktualizować typu tożsamości za pomocą programu CURL.

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST w celu umożliwienia tożsamości przypisanej w systemie na maszynie Wirtualnej określonego w treści żądania przez wartość `{"identity":{"type":"SystemAssigned"}` dla maszyny Wirtualnej o nazwie *myVM*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, nie usuwaj żadnych istniejących użytkownik, któremu przypisano zarządzanych tożsamości, które są przypisane do maszyny Wirtualnej, należy wyświetlić listę tożsamości przypisanych przez użytkownika za pomocą tego polecenia CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli masz żadnych tożsamości przypisanych przez użytkownika, przypisany do maszyny Wirtualnej, jak wskazano w `identity` wartość w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości przypisanych przez użytkownika podczas włączania tożsamości przypisanej w systemie na maszynie Wirtualnej.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Aby włączyć system przypisane tożsamości na maszynie Wirtualnej przy użyciu istniejących tożsamości przypisanych przez użytkownika, należy dodać `SystemAssigned` do `type` wartości.  
   
   Na przykład, jeśli maszyna wirtualna ma tożsamości przypisanych przez użytkownika `ID1` i `ID2` do niej przypisany, i chcesz dodać tożsamości przypisanej w systemie do maszyny Wirtualnej, użyj następującego wywołania programu CURL. Zastąp `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` przy użyciu wartości odpowiednich dla danego środowiska.

   Wersja interfejsu API `2018-06-01` przechowuje tożsamości przypisanych przez użytkownika w `userAssignedIdentities` wartości w postaci słownika, w przeciwieństwie do `identityIds` wartość w formie tablicy używane w wersji interfejsu API `2017-12-01` i wcześniejszych wersji.
   
   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **Interfejs API w wersji 2017-12-01 i starszych wersji**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Wyłącz system tożsamości przypisanej w Maszynie wirtualnej platformy Azure

Aby wyłączyć tożsamości przypisanej w systemie, istniejącej maszyny wirtualnej, musisz uzyskiwanie tokenu dostępu, a następnie wywołać punkt końcowy REST usługi Resource Manager, można zaktualizować typu tożsamości, aby za pomocą programu CURL `None`.

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj maszynę Wirtualną przy użyciu programu CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST można wyłączyć tożsamości przypisanej w systemie.  Poniższy przykład wyłącza tożsamości przypisanej w systemie, jak wskazano w treści żądania przez wartość `{"identity":{"type":"None"}}` z maszyny Wirtualnej o nazwie *myVM*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, nie usuwaj żadnych istniejących użytkownik, któremu przypisano zarządzanych tożsamości, które są przypisane do maszyny Wirtualnej, należy wyświetlić listę tożsamości przypisanych przez użytkownika za pomocą tego polecenia CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli masz żadnych tożsamości przypisanych przez użytkownika, przypisany do maszyny Wirtualnej, jak wskazano w `identity` wartość w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości przypisanych przez użytkownika podczas wyłączania tożsamości przypisanej w systemie na maszynie Wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Aby usunąć przypisanej tożsamości z maszyny wirtualnej, która ma tożsamości przypisanych przez użytkownika w systemie, Usuń `SystemAssigned` z `{"identity":{"type:" "}}` wartość przy zachowaniu `UserAssigned` wartość i `userAssignedIdentities` słownika wartości, jeśli używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub wcześniej, Zachowaj `identityIds` tablicy.

## <a name="user-assigned-identity"></a>Tożsamości przypisanych przez użytkownika

W tej sekcji dowiesz się, jak dodawać i usuwać tożsamości na Maszynie wirtualnej platformy Azure przy użyciu programu CURL w celu wykonywania wywołań do punktu końcowego usługi Azure Resource Manager REST przypisanych przez użytkownika.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej podczas tworzenia maszyny Wirtualnej platformy Azure

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Tworzenie [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla maszyny Wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie tożsamości przypisanych przez użytkownika, korzystając z instrukcji w tym miejscu: [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Utwórz Maszynę wirtualną przy użyciu programu CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST. Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* w grupie zasobów *myResourceGroup* przy użyciu tożsamości przypisanych przez użytkownika `ID1`, jak wskazano w treści żądania przez wartość `"identity":{"type":"UserAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
 
   
   **INTERFEJS API W WERSJI 2018-06-01**
    
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 

   **Interfejs API w wersji 2017-12-01 i starszych wersji**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Przypisywanie użytkownika tożsamości przypisanej do istniejącej maszyny Wirtualnej platformy Azure

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Tworzenie tożsamości przypisanych przez użytkownika, korzystając z instrukcji w tym miejscu [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Aby upewnić się, nie usuwaj istniejącego użytkownika lub systemu przypisany zarządzanych tożsamości, które są przypisane do maszyny Wirtualnej, należy wyświetlić listę typów tożsamości przypisane do maszyny Wirtualnej przy użyciu następującego polecenia CURL. Jeśli masz zarządzanych tożsamości przypisane do zestawu skalowania maszyn wirtualnych, są one wyświetlane w obszarze w `identity` wartość.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Jeśli masz jakiekolwiek użytkownika lub tożsamości przypisanej w systemie przypisane do maszyny Wirtualnej, jak wskazano w `identity` wartość w odpowiedzi, przejdź do kroku 5, który pokazuje, jak zachować tożsamości przypisanej w systemie t podczas dodawania tożsamości przypisanych przez użytkownika na maszynie Wirtualnej.

4. Jeśli nie masz każdy użytkownik przypisany do maszyny Wirtualnej tożsamości przypisanych przez należy użyć następującego polecenia CURL wywołać punkt końcowy usługi Azure Resource Manager REST można przypisać pierwszego użytkownika tożsamości przypisanej do maszyny Wirtualnej.

   Poniższe przykłady przypisuje tożsamości przypisanych przez użytkownika, `ID1` do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   **INTERFEJS API W WERSJI 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **Interfejs API w wersji 2017-12-01 i starszych wersji**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Jeśli masz istniejącą przypisany użytkownik lub system przypisane tożsamości przypisanych do maszyny Wirtualnej:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodawanie tożsamości przypisanych przez użytkownika do `userAssignedIdentities` wartość słownika.
    
   Na przykład jeśli masz system przypisać tożsamości i tożsamości przypisanych przez użytkownika `ID1` aktualnie przypisane do maszyny Wirtualnej i chcesz dodać tożsamość użytkownika `ID2` do niego:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **Interfejs API w wersji 2017-12-01 i starszych wersji**

   Zachowaj tożsamości przypisanych przez użytkownika, czy chcesz zachować w `identityIds` tablica wartości podczas dodawania nowej tożsamości przypisanych przez użytkownika.

   Na przykład jeśli masz system przypisać tożsamości i tożsamości przypisanych przez użytkownika `ID1` aktualnie przypisane do maszyny Wirtualnej i chcesz dodać tożsamość użytkownika `ID2` do niego: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Usuń użytkownika z tożsamości przypisanej w Maszynie wirtualnej platformy Azure

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę Wirtualną z systemem zarządzanych tożsamości przypisanej.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usuwaj żadnych istniejących użytkownik, któremu przypisano zarządzanych tożsamości, które chcesz zachować przypisanej do maszyny Wirtualnej lub usuń system tożsamości przypisanej, musisz listy zarządzanych tożsamości za pomocą następującego polecenia CURL: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Jeśli masz zarządzane tożsamości przypisanych do maszyny Wirtualnej, są one wymienione w odpowiedzi `identity` wartość.

   Na przykład, jeśli masz tożsamości przypisanych przez użytkownika `ID1` i `ID2` przypisane do maszyny Wirtualnej i chcesz zachować `ID1` przypisane i zachowywanie tożsamości przypisanej w systemie:
   
   **INTERFEJS API W WERSJI 2018-06-01**

   Dodaj `null` tożsamości przypisanych przez użytkownika, czy chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **Interfejs API w wersji 2017-12-01 i starszych wersji**

   Zachowaj tylko przypisane do użytkownika identity(s) chcesz przechowywać w `identityIds` tablicy:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Jeśli maszyna wirtualna ma przypisanej w systemie i tożsamości przypisanych przez użytkownika, należy usunąć wszystkich użytkowników tożsamości przypisanych przez przełączenie na system tylko przypisać przy użyciu następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Jeśli chcesz usunąć wszystkie maszyna wirtualna ma tylko jednego tożsamości przypisanych przez użytkownika, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat sposobu tworzenia, listy lub usunąć użytkownika przypisane przy użyciu interfejsu REST, zobacz:

- [Tworzenie listy i usuwanie tożsamości przypisanych przez użytkownika, przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)