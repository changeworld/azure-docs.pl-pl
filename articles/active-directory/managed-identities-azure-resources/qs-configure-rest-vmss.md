---
title: Konfigurowanie tożsamości zarządzanych w usłudze Azure VMSS przy użyciu rest — usługi Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez użytkownika w systemie VMSS platformy Azure przy użyciu funkcji CURL do wykonywania wywołań interfejsu API REST.
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
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253340"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyny wirtualnej przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość systemu w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule przy użyciu CURL do wywołania punktu końcowego REST usługi Azure Resource Manager, dowiedzieć się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestaw skalowania maszyny wirtualnej:

- Włączanie i wyłączanie tożsamości zarządzanej przypisanej przez system w zestawie skalowania maszyny wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyny wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, twoje konto wymaga następujących przypisań kontroli dostępu opartych na rolach platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej,](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) aby utworzyć zestaw skalowania maszyny wirtualnej i włączyć i usunąć system i/lub przypisaną przez użytkownika tożsamość zarządzaną z zestawu skalowania maszyny wirtualnej.
    - [Rola współautora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) w celu utworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - [Rola operatora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) do przypisywania i usuwania tożsamości przypisanej przez użytkownika z i do zestawu skalowania maszyny wirtualnej.
- Jeśli używasz systemu Windows, zainstaluj [podsystem windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [powłoki chmury Azure](../../cloud-shell/overview.md) w witrynie Azure portal.
- [Zainstaluj lokalną konsolę interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli)jeśli używasz [podsystemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub systemu [operacyjnego dystrybucji systemu Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz konsoli lokalnej interfejsu wiersza `az login` polecenia platformy Azure, zaloguj się na platformie Azure przy użyciu konta skojarzonego z subskrypcją platformy Azure, którą chcesz zarządzać tożsamościami zarządzanymi przypisanymi przez użytkownika.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamości zarządzanej przypisane przez system na zestaw skalowania maszyny wirtualnej przy użyciu CURL do wywołania punktu końcowego REST usługi Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia zestawu skalowania maszyny wirtualnej

Aby utworzyć zestaw skalowania maszyny wirtualnej z włączoną tożsamością zarządzaną przypisaną do systemu, należy utworzyć zestaw skalowania maszyny wirtualnej i pobrać token dostępu, aby użyć funkcji CURL do wywołania punktu końcowego Menedżera zasobów z wartością typu tożsamości zarządzanej przypisanej przez system.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) do zamknięcia i wdrożenia zestawu skalowania maszyny wirtualnej i powiązanych z nią zasobów przy użyciu [grupy az create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla zestawu skalowania maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz zestaw skalowania maszyny wirtualnej przy użyciu funkcji CURL, aby wywołać punkt końcowy USŁUGI Azure Resource Manager REST. Poniższy przykład tworzy zestaw skalowania maszyny wirtualnej o nazwie *myVMSS* w *myResourceGroup* z tożsamością zarządzaną przypisaną przez system, zidentyfikowaną w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej do systemu w istniejącym zestawie skalowania maszyny wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną do systemu w istniejącym zestawie skalowania maszyny wirtualnej, należy uzyskać token dostępu, a następnie użyć funkcji CURL, aby wywołać punkt końcowy REST Menedżera zasobów w celu zaktualizowania typu tożsamości.

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia CURL, aby wywołać punkt końcowy REST usługi Azure Resource Manager, aby włączyć przypisaną do `{"identity":{"type":"SystemAssigned"}` systemu tożsamość zarządzaną w zestawie skalowania maszyny wirtualnej, jak określono w treści żądania przez wartość zestawu skalowania maszyny wirtualnej o nazwie *myVMSS*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do zestawu `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`skalowania maszyny wirtualnej, należy wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika za pomocą tego polecenia CURL: . Jeśli do zestawu skalowania maszyny wirtualnej przypisanych przez użytkownika przypisano `identity` żadnych tożsamości zarządzanych, jak określono w wartości w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika, włączając tożsamość zarządzaną przypisaną przez system w zestawie skalowania maszyny wirtualnej.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisaną do systemu w zestawie skalowania maszyny wirtualnej `SystemAssigned` z `type` istniejącymi tożsamościami zarządzanymi przypisanymi przez użytkownika, należy dodać do wartości.  
   
   Na przykład jeśli zestaw skalowania maszyny wirtualnej ma `ID1` przypisane przez użytkownika tożsamości zarządzane i `ID2` przypisane do niego i chcesz dodać tożsamość zarządzaną przypisaną do systemu do zestawu skalowania maszyny wirtualnej, użyj następującego wywołania CURL. Zastąp `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` wartościami odpowiednimi dla danego środowiska.

   Wersja `2018-06-01` interfejsu API przechowuje tożsamości zarządzane `userAssignedIdentities` przypisane przez użytkownika w wartości `identityIds` w formacie słownika, `2017-12-01`w przeciwieństwie do wartości w formacie tablicy używanym w wersji interfejsu API .
   
   **WERSJA INTERFEJSU API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |
 
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
   
   **WERSJA INTERFEJSU API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyny wirtualnej

Aby wyłączyć tożsamość przypisaną systemowi w istniejącym zestawie skalowania maszyny wirtualnej, należy uzyskać token dostępu, a następnie `None`użyć funkcji CURL, aby wywołać punkt końcowy REST Menedżera zasobów, aby zaktualizować typ tożsamości do .

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj zestaw skalowania maszyny wirtualnej przy użyciu funkcji CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST w celu wyłączenia tożsamości zarządzanej przypisanej przez system.  Poniższy przykład wyłącza przypisaną do systemu tożsamość zarządzaną `{"identity":{"type":"None"}}` zidentyfikowaną w treści żądania przez wartość z zestawu skalowania maszyny wirtualnej o nazwie *myVMSS*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do zestawu `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`skalowania maszyny wirtualnej, należy wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika za pomocą tego polecenia CURL: . Jeśli do zestawu skalowania maszyny wirtualnej przypisano dowolną tożsamość zarządzaną przypisaną przez użytkownika, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika podczas usuwania tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyny wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną do systemu z zestawu skalowania maszyny `SystemAssigned` wirtualnej, który ma przypisane przez użytkownika tożsamości zarządzane, usuń z `{"identity":{"type:" "}}` wartości, zachowując `UserAssigned` wartość i wartości `userAssignedIdentities` słownika, jeśli używasz interfejsu API w wersji **2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub wcześniejszej, zachowaj tablicę. `identityIds`

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika w zestawie skalowania maszyny wirtualnej przy użyciu funkcji CURL do nawiązywać połączenia z punktem końcowym REST usługi Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyny wirtualnej

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla zestawu skalowania maszyny wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz tożsamość zarządzaną przypisaną przez użytkownika, korzystając z instrukcji znalezionych w tym miejscu: [Utwórz tożsamość zarządzaną przypisaną przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Utwórz zestaw skalowania maszyny wirtualnej przy użyciu funkcji CURL, aby wywołać punkt końcowy USŁUGI Azure Resource Manager REST. Poniższy przykład tworzy zestaw skalowania maszyny wirtualnej o nazwie *myVMSS* w grupie `ID1`zasobów *myResourceGroup* z tożsamością `"identity":{"type":"UserAssigned"}`zarządzaną przypisaną przez użytkownika, zidentyfikowaną w treści żądania przez wartość . Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.
 
   **WERSJA INTERFEJSU API 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

   **WERSJA INTERFEJSU API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |
 
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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyny wirtualnej platformy Azure

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Utwórz tożsamość zarządzaną przypisaną przez użytkownika, korzystając z instrukcji znalezionych w tym miejscu, [Utwórz tożsamość zarządzaną przypisaną przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby upewnić się, że nie usuniesz istniejących tożsamości zarządzanych przypisanych przez użytkownika lub system, które są przypisane do zestawu skalowania maszyny wirtualnej, należy wyświetlić listę typów tożsamości przypisanych do zestawu skalowania maszyny wirtualnej przy użyciu następującego polecenia CURL. Jeśli masz zarządzane tożsamości przypisane do zestawu skalowania maszyny `identity` wirtualnej, są one wymienione w wartości.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |   
 

4. Jeśli do zestawu skalowania maszyny wirtualnej nie przypisano żadnych tożsamości zarządzanych przypisanych przez użytkownika lub system, użyj następującego polecenia CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST, aby przypisać pierwszą tożsamość zarządzaną przypisaną przez użytkownika do maszyny wirtualnej zestawu skalowania.  Jeśli do zestawu skalowania maszyny wirtualnej przypisano użytkownikowi lub systemowi tożsamość zarządzana, przejdź do kroku 5, który pokazuje, jak dodać wiele tożsamości zarządzanych przypisanych przez użytkownika do zestawu skalowania maszyny wirtualnej, zachowując jednocześnie zarządzane przypisane do systemu Tożsamości.

   Poniższy przykład przypisuje tożsamość zarządzaną `ID1` przypisaną przez użytkownika do zestawu skalowania maszyny wirtualnej o nazwie *myVMSS* w grupie zasobów *myResourceGroup*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.

   **WERSJA INTERFEJSU API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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
    
   **WERSJA INTERFEJSU API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

5. Jeśli do zestawu skalowania maszyny wirtualnej przypisana jest istniejąca tożsamość zarządzana przypisana przez użytkownika lub przypisana do systemu:
   
   **WERSJA INTERFEJSU API 2018-06-01**

   Dodaj tożsamość zarządzaną przypisaną `userAssignedIdentities` przez użytkownika do wartości słownika.

   Na przykład, jeśli masz przypisaną do systemu tożsamość zarządzaną i tożsamość `ID1` zarządzaną przypisaną przez użytkownika, która jest `ID2` obecnie przypisana do skali maszyny wirtualnej i chcesz dodać do niej przypisaną przez użytkownika tożsamość zarządzaną:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

   **WERSJA INTERFEJSU API 2017-12-01**

   Zachowaj tożsamości zarządzane przypisane przez użytkownika, które `identityIds` chcesz zachować w wartości tablicy, dodając nową tożsamość zarządzaną przypisaną przez użytkownika.

   Na przykład, jeśli masz tożsamość przypisaną do systemu `ID1` i tożsamość zarządzaną przypisaną przez użytkownika aktualnie przypisaną do `ID2` zestawu skalowania maszyny wirtualnej i chcesz dodać do niej przypisaną przez użytkownika tożsamość zarządzaną:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyny wirtualnej

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku Autoryzacja, aby utworzyć zestaw skalowania maszyny wirtualnej z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które chcesz zachować przypisane do zestawu skalowania maszyny wirtualnej lub usuniesz tożsamość zarządzaną przypisaną do systemu, musisz wyświetlić listę tożsamości zarządzanych za pomocą następującego polecenia CURL :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. |
   
   Jeśli masz zarządzane tożsamości przypisane do maszyny Wirtualnej, są `identity` one wymienione w odpowiedzi w wartości. 
    
   Na przykład, jeśli masz przypisane przez `ID1` `ID2` użytkownika tożsamości zarządzane i przypisane do zestawu `ID1` skalowania maszyny wirtualnej, a chcesz zachować tylko przypisaną i zachować przypisaną do systemu tożsamość zarządzaną:

   **WERSJA INTERFEJSU API 2018-06-01**

   Dodaj `null` do tożsamości zarządzanej przypisanej przez użytkownika, którą chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

   **WERSJA INTERFEJSU API 2017-12-01**

   Zachowaj tylko tożsamości zarządzane przypisane przez użytkownika, które `identityIds` chcesz zachować w tablicy:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

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

Jeśli zestaw skalowania maszyny wirtualnej ma zarówno tożsamości zarządzane przypisane przez system, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na użycie tylko przypisanego do systemu polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Jeśli zestaw skalowania maszyny wirtualnej ma tylko tożsamości zarządzane przypisane przez użytkownika i chcesz usunąć je wszystkie, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu. | 

**Treść żądania**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje dotyczące tworzenia, wystawiania lub usuwania tożsamości zarządzanych przypisanych przez użytkownika przy użyciu funkcji REST, zobacz:

- [Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
