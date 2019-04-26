---
title: Jak skonfigurować system i zarządzanych tożsamości przypisanych przez użytkownika na platformie Azure zestawu skalowania maszyn wirtualnych przy użyciu usługi REST
description: Wywołuje krok po kroku instrukcje dotyczące konfigurowania systemu i zarządzanych tożsamości przypisanych przez użytkownika na platformie Azure zestawu skalowania maszyn wirtualnych przy użyciu programu CURL w celu uzyskania interfejsu API REST.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cafb3c97befd64cc6413a2eefa5e5baa9e01bf93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308284"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej zestawu skalowania przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzanych tożsamości dla zasobów platformy Azure udostępnia usługi platformy Azure przy użyciu tożsamości, system automatycznie zarządzane w usłudze Azure Active Directory. Można użyć tej tożsamości do uwierzytelniania na dowolne usługi obsługujące uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule przy użyciu programu CURL w celu wykonywania wywołań do punktu końcowego usługi Azure Resource Manager REST dowiesz się, jak wykonywać następujące zarządzanych tożsamości dla operacji tworzenia zasobów platformy Azure w zestawie skalowania maszyn wirtualnych:

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
    - [Operator tożsamości zarządzanych](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli przypisywania i usuwania tożsamości przypisanych przez użytkownika z i do zestawu skalowania maszyn wirtualnych.
- Jeśli używasz Windows, zainstaluj [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal.
- [Instalowanie lokalnej konsoli wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), jeśli używasz [podsystem Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub [dystrybucji systemu Linux, OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz lokalnej konsoli wiersza polecenia platformy Azure, zaloguj się do platformy Azure za pomocą `az login` przy użyciu konta, który jest skojarzony z subskrypcją platformy Azure chcesz zarządzać system lub zarządzanych tożsamości przypisanych przez użytkownika.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Przypisana przez system tożsamości zarządzanej

W tej sekcji dowiesz się, jak włączyć i wyłączyć przypisany systemowo tożsamość zarządzaną w skali maszyny wirtualnej można ustawić przy użyciu programu CURL do nawiązywania połączeń z punktem końcowym usługi Azure Resource Manager REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych

Do utworzenia maszyny wirtualnej zestawu skalowania z przypisany systemowo tożsamości zarządzanej, włączone, należy utworzyć zestaw skalowania maszyn wirtualnych i pobrania tokenu dostępu, aby za pomocą programu CURL wywołanie punktu końcowego Menedżera zasobów z wartością typu przypisany systemowo tożsamość zarządzaną.

1. Tworzenie [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania zestawu skalowania maszyn wirtualnych i powiązanymi zasobami, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Tworzenie [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) skalowania maszyny wirtualnej można ustawić:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST. Poniższy przykład obejmuje tworzenie maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* w *myResourceGroup* przypisany systemowo zarządzanych tożsamości, jak wskazano w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Włącz przypisany systemowo tożsamość zarządzana na podstawie istniejącego zestawu skalowania maszyn wirtualnych

Aby włączyć tożsamość zarządzaną przypisana przez system, na podstawie istniejącego zestawu skalowania maszyn wirtualnych, musisz uzyskiwanie tokenu dostępu, a następnie wywołać punkt końcowy REST usługi Resource Manager, można zaktualizować typu tożsamości za pomocą programu CURL.

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST umożliwiające przypisany systemowo tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych ustawienia określone w treści żądania przez wartość `{"identity":{"type":"SystemAssigned"}` dla maszyny wirtualnej zestawu skalowania o nazwie *myVMSS*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, nie usuwaj żadnych istniejących tożsamości przypisanych przez użytkownika zarządzanego, które są przypisane do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika za pomocą tego polecenia programu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli masz jakiekolwiek przypisanych do użytkowników zarządzanych tożsamości przypisane do zestawu, jak wskazano w skalowania maszyn wirtualnych `identity` wartość w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować zarządzanych tożsamości przypisanych przez użytkownika podczas włączania przypisana przez system tożsamość zarządzaną w zestawie skalowania maszyn wirtualnych.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisana przez system na maszynie wirtualnej zestawu skalowania przy użyciu istniejących tożsamości zarządzanej przypisanych przez użytkownika, należy dodać `SystemAssigned` do `type` wartości.  
   
   Na przykład, jeśli w zestawie skalowania maszyny wirtualnej ma przypisany użytkownik zarządzane tożsamości `ID1` i `ID2` do niej przypisany, i chcesz dodać tożsamość zarządzaną przypisana przez system do zestawu skalowania maszyn wirtualnych, użyj następującego wywołania programu CURL. Zastąp `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` przy użyciu wartości odpowiednich dla danego środowiska.

   Wersja interfejsu API `2018-06-01` przechowuje tożsamości zarządzanych przypisanych przez użytkownika w `userAssignedIdentities` wartości w postaci słownika, w przeciwieństwie do `identityIds` wartość w formie tablicy używane w wersji interfejsu API `2017-12-01`.
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. |
 
   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```
   
   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Wyłącz przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych

Aby wyłączyć tożsamości przypisanych przez system na podstawie istniejącego zestawu skalowania maszyn wirtualnych, musisz uzyskiwanie tokenu dostępu, a następnie wywołać punkt końcowy REST usługi Resource Manager, można zaktualizować typu tożsamości, aby za pomocą programu CURL `None`.

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizowanie maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST można wyłączyć tożsamości zarządzanej przypisana przez system.  Poniższy przykład wyłącza przypisany systemowo tożsamości zarządzanej, jak wskazano w treści żądania przez wartość `{"identity":{"type":"None"}}` z maszyny wirtualnej zestawu skalowania o nazwie *myVMSS*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, nie usuwaj żadnych istniejących tożsamości przypisanych przez użytkownika zarządzanego, które są przypisane do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę zarządzanych tożsamości przypisanych przez użytkownika za pomocą tego polecenia programu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Jeśli masz żadnych przypisanych przez użytkownika tożsamości zarządzanej przypisane do zestawu skalowania maszyn wirtualnych, przejdź do kroku 3, który pokazuje, jak zachować zarządzanych tożsamości przypisanych przez użytkownika podczas usuwania przypisany systemowo tożsamość zarządzaną z zestawu skalowania maszyn wirtualnych.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Aby usunąć przypisany systemowo tożsamości zarządzanej zestaw skalowania maszyn wirtualnych, który ma zarządzanych tożsamości przypisanych przez użytkownika, należy usunąć `SystemAssigned` z `{"identity":{"type:" "}}` wartość przy zachowaniu `UserAssigned` wartość i `userAssignedIdentities` słownika wartości, jeśli użytkownik używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub wcześniej, Zachowaj `identityIds` tablicy.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodawać i usuwać tożsamości zarządzanej użytkownik przypisany na maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do nawiązywania połączeń z punktem końcowym usługi Azure Resource Manager REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisz przypisanych przez użytkownika tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Tworzenie [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) skalowania maszyny wirtualnej można ustawić:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz przypisanych przez użytkownika tożsamości zarządzanej zgodnie z instrukcjami przedstawionymi tutaj: [Tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Tworzenie maszyny wirtualnej zestawu skalowania przy użyciu programu CURL do wywoływania punktu końcowego usługi Azure Resource Manager REST. Poniższy przykład obejmuje tworzenie maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* w grupie zasobów *myResourceGroup* przy użyciu tożsamości przypisanych przez użytkownika zarządzanego `ID1`, jak wskazano w treści żądania według wartości `"identity":{"type":"UserAssigned"}`. Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. |
 
   **Treść żądania**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisz tożsamości zarządzanej użytkownik przypisany do istniejącego zestawu skalowania maszyn wirtualnych platformy Azure

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Tworzenie przypisanych przez użytkownika tożsamości zarządzanej korzystając z instrukcji w tym miejscu [tworzenie zarządzanych tożsamości przypisanych przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby upewnić się, że nie można usunąć istniejącego użytkownika lub przypisany systemowo zarządzanych tożsamości, które są przypisane do zestawu skalowania maszyn wirtualnych, należy wyświetlić listę tożsamości ustawionych typów przypisanych do skalowania maszyn wirtualnych przy użyciu następującego polecenia CURL. Jeśli masz zarządzane tożsamości przypisane do zestawu skalowania maszyn wirtualnych, są one wymienione w `identity` wartość.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. |   
 

4. Jeśli nie masz żadnych użytkownika lub przypisany systemowo zarządzany tożsamości przypisane do skalowania maszyny wirtualnej zestawu, należy użyć następującego polecenia CURL wywołać punkt końcowy usługi Azure Resource Manager REST można przypisać pierwszy tożsamość zarządzaną użytkownik przypisany do maszyny wirtualnej zestaw skalowania.  Jeśli masz użytkownika lub przypisany systemowo identity(s) zarządzanych przypisane do zestawu skalowania maszyn wirtualnych, przejdź do kroku 5, pokazujący sposób dodawania wielu tożsamości zarządzanych przypisanych przez użytkownika do zestawu skalowania maszyn wirtualnych zachowując również przypisany systemowo zarządzane tożsamość.

   Poniższy przykład przypisuje przypisanych przez użytkownika tożsamości zarządzanej, `ID1` do maszyny wirtualnej zestawu skalowania o nazwie *myVMSS* w grupie zasobów *myResourceGroup*.  Zastąp `<ACCESS TOKEN>` wartością otrzymany w poprzednim kroku zleconą tokenu elementu nośnego dostępu i `<SUBSCRIPTION ID>` wartości odpowiednie dla danego środowiska.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **INTERFEJS API W WERSJI 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Jeśli masz istniejącą przypisanych przez użytkownika lub przypisany systemowo tożsamość zarządzaną przypisane do zestawu skalowania maszyn wirtualnych:
   
   **API VERSION 2018-06-01**

   Dodaj tożsamość zarządzanych użytkownik przypisany do `userAssignedIdentities` wartość słownika.

   Na przykład, jeśli masz przypisaną przez system tożsamość zarządzaną i zarządzanych tożsamości przypisanych przez użytkownika `ID1` aktualnie przypisane do skalowania maszyny wirtualnej i chcesz dodać przypisanych przez użytkownika tożsamości zarządzanej `ID2` do niego:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   Zachowaj tożsamości przypisanych przez użytkownika zarządzanego chcesz przechowywać w `identityIds` tablica wartości podczas dodawania nowej tożsamości zarządzanych przypisanych przez użytkownika.

   Na przykład, jeśli masz tożsamości przypisanych przez system lub użytkownik przypisany tożsamość zarządzaną `ID1` aktualnie przypisane do zestawu skalowania maszyny wirtualnej i chcesz dodać przypisanych przez użytkownika tożsamości zarządzanej `ID2` do niego:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuń tożsamość zarządzaną z przypisanego przez użytkownika z zestawu skalowania maszyn wirtualnych

1. Pobieranie elementu nośnego tokenu dostępu, której użyjesz w następnym kroku w nagłówku autoryzacji do utworzenia Twojego zestawu skalowania maszyn wirtualnych za pomocą tożsamości zarządzanej przypisana przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usuwaj żadnych istniejących tożsamości przypisanych przez użytkownika zarządzanego, które chcesz zachować przypisane do zestawu skalowania maszyn wirtualnych lub usuń przypisany systemowo tożsamości zarządzanej, musisz listy zarządzanych tożsamości za pomocą następującego polecenia CURL :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. |
   
   Jeśli masz zarządzane tożsamości przypisanych do maszyny Wirtualnej, są one wymienione w odpowiedzi `identity` wartość. 
    
   Na przykład w przypadku zarządzanych tożsamości przypisanych przez użytkownika `ID1` i `ID2` przypisane do zestawu skalowania maszyny wirtualnej i chcesz zachować `ID1` przypisane i zachowywanie tożsamości zarządzanej przypisana przez system:

   **API VERSION 2018-06-01**

   Dodaj `null` użytkownik przypisany zarządzanych tożsamości, które chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **INTERFEJS API W WERSJI 2017-12-01**

   Zachowaj tylko użytkownik przypisany zarządzanych identity(s) chcesz przechowywać w `identityIds` tablicy:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądania**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
   |*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Jeśli w zestawie skalowania maszyn wirtualnych znajdują się oba przypisana przez system i tożsamości przypisanych przez użytkownika zarządzanego, możesz usunąć wszystkich tożsamości przypisanych przez użytkownika zarządzanego, przełączając się używać tylko przypisany systemowo przy użyciu następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądania**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
|*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Jeśli w zestawie skalowania maszyn wirtualnych ma tylko tożsamości przypisanych przez użytkownika zarządzanej i chcesz usunąć wszystkie, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądania**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw `application/json`.        |
|*Autoryzacja*     | Wymagany. Ustawić prawidłową `Bearer` tokenu dostępu. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje na temat sposobu tworzenia, listy lub usuwanie tożsamości przypisanych przez użytkownika zarządzanego przy użyciu usługi REST, zobacz:

- [Utwórz listę lub usunąć przypisanych przez użytkownika tożsamości zarządzanej przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
