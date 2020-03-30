---
title: Konfigurowanie tożsamości zarządzanych na maszynie Wirtualnej platformy Azure przy użyciu rest — usługi Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych przypisanych przez użytkownika na maszynie Wirtualnej platformy Azure przy użyciu funkcji CURL do wykonywania wywołań interfejsu API REST.
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
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244162"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu wywołań interfejsu API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość systemu w usłudze Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule przy użyciu CURL do wywołania punktu końcowego REST usługi Azure Resource Manager, dowiedzieć się, jak wykonać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie Wirtualnej platformy Azure:

- Włączanie i wyłączanie tożsamości zarządzanej przypisanej przez system na maszynie Wirtualnej platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie Wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją omówienie](overview.md). **Pamiętaj, aby przejrzeć [różnicę między tożsamością zarządzaną przypisaną do systemu a przypisaną przez użytkownika](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Jeśli używasz systemu Windows, zainstaluj [podsystem windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub użyj [powłoki chmury Azure](../../cloud-shell/overview.md) w witrynie Azure portal.
- [Zainstaluj lokalną konsolę interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli)jeśli używasz [podsystemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/about) lub systemu [operacyjnego dystrybucji systemu Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Jeśli używasz konsoli lokalnej interfejsu wiersza `az login` polecenia platformy Azure, zaloguj się na platformie Azure przy użyciu konta skojarzonego z subskrypcją platformy Azure, którą chcesz zarządzać tożsamościami zarządzanymi przypisanymi przez użytkownika.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana do systemu

W tej sekcji dowiesz się, jak włączyć i wyłączyć tożsamości zarządzanej przypisane do systemu na maszynie Wirtualnej platformy Azure przy użyciu CURL do wywołania punktu końcowego REST usługi Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Włączanie tożsamości zarządzanej przypisanej do systemu podczas tworzenia maszyny Wirtualnej platformy Azure

Aby utworzyć maszynę wirtualną platformy Azure z włączoną tożsamością zarządzaną przypisaną do systemu, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Utwórz [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) w celu uwzględnienia i wdrożenia maszyny wirtualnej i jej powiązanych zasobów przy użyciu polecenia [az group create](/cli/azure/group/#az-group-create). Ten krok możesz pominąć, jeśli masz już grupę zasobów, której chcesz użyć w zamian:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla maszyny Wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Tworzenie maszyny Wirtualnej przy użyciu CURL do wywołania punktu końcowego REST usługi Azure Resource Manager. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z tożsamością zarządzaną przypisaną do systemu, zidentyfikowaną w treści żądania przez wartość `"identity":{"type":"SystemAssigned"}`. Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**
   
   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 
   
   **Treść żądania**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Włączanie tożsamości przypisanej do systemu na istniejącej maszynie Wirtualnej platformy Azure

Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Użyj następującego polecenia CURL, aby wywołać punkt końcowy USŁUGI Azure Resource Manager REST, aby włączyć przypisaną `{"identity":{"type":"SystemAssigned"}` do systemu tożsamość zarządzaną na maszynie Wirtualnej, jak określono w treści żądania przez wartość maszyny wirtualnej o nazwie *myVM*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.
   
   > [!IMPORTANT]
   > Aby upewnić się, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`maszyny Wirtualnej, należy wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika za pomocą tego polecenia CURL: . Jeśli do maszyny wirtualnej przypisanych przez użytkownika przypisano żadnych tożsamości `identity` zarządzanych, jak określono w wartości w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika, włączając tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 
   
   **Treść żądania**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Aby włączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej z `SystemAssigned` istniejącymi tożsamościami zarządzanymi przypisanymi przez użytkownika, należy dodać do `type` wartości.  
   
   Na przykład jeśli maszyna wirtualna ma przypisane `ID1` przez `ID2` użytkownika tożsamości zarządzane i przypisane do niej i chcesz dodać tożsamości zarządzanej przypisane przez system do maszyny Wirtualnej, użyj następującego wywołania CURL. Zastąp `<ACCESS TOKEN>` i `<SUBSCRIPTION ID>` wartościami odpowiednimi dla danego środowiska.

   Wersja `2018-06-01` interfejsu API przechowuje tożsamości zarządzane `userAssignedIdentities` przypisane przez użytkownika w wartości `identityIds` w formacie słownika, `2017-12-01`w przeciwieństwie do wartości w formacie tablicy używanym w wersji interfejsu API .
   
   **WERSJA INTERFEJSU API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **WERSJA INTERFEJSU API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny Wirtualnej platformy Azure

Aby wyłączyć tożsamość zarządzaną przypisaną do systemu na maszynie wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Zaktualizuj maszynę wirtualną przy użyciu funkcji CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST w celu wyłączenia tożsamości zarządzanej przypisanej przez system.  Poniższy przykład wyłącza przypisaną do systemu tożsamość zarządzaną `{"identity":{"type":"None"}}` zidentyfikowaną w treści żądania przez wartość z maszyny Wirtualnej o nazwie *myVM*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.

   > [!IMPORTANT]
   > Aby upewnić się, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które są przypisane do `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`maszyny Wirtualnej, należy wyświetlić listę tożsamości zarządzanych przypisanych przez użytkownika za pomocą tego polecenia CURL: . Jeśli do maszyny wirtualnej przypisanych przez użytkownika przypisano żadnych tożsamości `identity` zarządzanych, jak określono w wartości w odpowiedzi, przejdź do kroku 3, który pokazuje, jak zachować tożsamości zarządzane przypisane przez użytkownika podczas wyłączania tożsamości zarządzanej przypisanej przez system na maszynie wirtualnej.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Aby usunąć tożsamość zarządzaną przypisaną przez system z maszyny wirtualnej, `{"identity":{"type:" "}}` do której `UserAssigned` przypisano `userAssignedIdentities` użytkownikowi tożsamości zarządzane, usuń `SystemAssigned` z wartości, zachowując wartość i wartości słownika, jeśli używasz **interfejsu API w wersji 2018-06-01**. Jeśli używasz **interfejsu API w wersji 2017-12-01** lub wcześniejszej, zachowaj tablicę. `identityIds`

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przypisaną przez użytkownika na maszynie Wirtualnej platformy Azure przy użyciu funkcji CURL do nawiązywać połączenia z punktem końcowym REST usługi Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny Wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Utwórz [interfejs sieciowy](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) dla maszyny Wirtualnej:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Utwórz tożsamość zarządzaną przypisaną przez użytkownika, korzystając z instrukcji znalezionych w tym miejscu: [Utwórz tożsamość zarządzaną przypisaną przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Tworzenie maszyny Wirtualnej przy użyciu CURL do wywołania punktu końcowego REST usługi Azure Resource Manager. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* w grupie zasobów `ID1` *myResourceGroup* z tożsamością zarządzaną przypisaną przez użytkownika, zidentyfikowaną w treści żądania przez wartość `"identity":{"type":"UserAssigned"}`. Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.
 
   **WERSJA INTERFEJSU API 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **WERSJA INTERFEJSU API 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny Wirtualnej platformy Azure

Aby przypisać tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [Współautor maszyny wirtualnej](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) i [Operator tożsamości zarządzanej.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nie są wymagane żadne dodatkowe przypisania ról katalogu usługi Azure AD.

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Utwórz tożsamość zarządzaną przypisaną przez użytkownika, korzystając z instrukcji znalezionych w tym miejscu, [Utwórz tożsamość zarządzaną przypisaną przez użytkownika](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Aby upewnić się, że nie usuniesz istniejących tożsamości zarządzanych przypisanych przez użytkownika lub system, które są przypisane do maszyny Wirtualnej, należy wyświetlić listę typów tożsamości przypisanych do maszyny Wirtualnej przy użyciu następującego polecenia CURL. Jeśli masz zarządzane tożsamości przypisane do zestawu skalowania maszyny wirtualnej, są one wymienione w `identity` wartości.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.

    Jeśli masz żadnych tożsamości zarządzanych przypisanych przez użytkownika lub system przypisane `identity` do maszyny Wirtualnej, jak określono w wartości w odpowiedzi, przejdź do kroku 5, który pokazuje, jak zachować tożsamości zarządzanej przypisane przez system podczas dodawania tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej.

4. Jeśli nie masz żadnych tożsamości zarządzanych przypisanych przez użytkownika przypisane do maszyny Wirtualnej, użyj następującego polecenia CURL, aby wywołać punkt końcowy usługi Azure Resource Manager REST, aby przypisać pierwszą tożsamość zarządzaną przypisaną przez użytkownika do maszyny Wirtualnej.

   Poniższe przykłady przypisuje tożsamość zarządzaną `ID1` przypisaną przez użytkownika do maszyny wirtualnej o nazwie *myVM* w grupie zasobów *myResourceGroup*.  Zamień `<ACCESS TOKEN>` na wartość otrzymaną w poprzednim kroku, gdy zażądano tokenu dostępu na okaziciela i `<SUBSCRIPTION ID>` wartość odpowiednią dla danego środowiska.

   **WERSJA INTERFEJSU API 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        |
 
   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **WERSJA INTERFEJSU API 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

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

5. Jeśli do maszyny Wirtualnej przypisana jest istniejąca tożsamość zarządzana przypisana przez użytkownika lub przypisana do systemu:
   
   **WERSJA INTERFEJSU API 2018-06-01**

   Dodaj tożsamość zarządzaną przypisaną `userAssignedIdentities` przez użytkownika do wartości słownika.
    
   Na przykład, jeśli masz przypisaną do systemu tożsamość zarządzaną i tożsamość `ID1` zarządzaną przypisaną przez użytkownika, aktualnie `ID2` przypisaną do maszyny Wirtualnej i chcesz dodać do niej tożsamość zarządzaną przypisaną przez użytkownika:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

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

   Na przykład, jeśli masz przypisaną do systemu tożsamość zarządzaną i tożsamość `ID1` zarządzaną przypisaną przez użytkownika, aktualnie `ID2` przypisaną do maszyny Wirtualnej i chcesz dodać do niej tożsamość zarządzaną przypisaną przez użytkownika: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny Wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika do maszyny Wirtualnej, twoje konto wymaga przypisania roli [współautora maszyny wirtualnej.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

1. Pobierz token dostępu na okaziciela, który będzie używany w następnym kroku w nagłówku autoryzacji, aby utworzyć maszynę wirtualną z tożsamością zarządzaną przypisaną przez system.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aby upewnić się, że nie usuniesz żadnych istniejących tożsamości zarządzanych przypisanych przez użytkownika, które chcesz zachować przypisane do maszyny Wirtualnej lub usuniesz przypisaną do systemu tożsamość zarządzaną, musisz wyświetlić listę zarządzanych tożsamości za pomocą następującego polecenia CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.
 
   Jeśli masz zarządzane tożsamości przypisane do maszyny Wirtualnej, są `identity` one wymienione w odpowiedzi w wartości.

   Na przykład, jeśli masz przypisane przez `ID1` `ID2` użytkownika tożsamości zarządzane i przypisane do `ID1` maszyny Wirtualnej, a tylko chcesz zachować przypisaną i zachować tożsamość przypisaną do systemu:
   
   **WERSJA INTERFEJSU API 2018-06-01**

   Dodaj `null` do tożsamości zarządzanej przypisanej przez użytkownika, którą chcesz usunąć:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Nagłówki żądań**

   |Nagłówek żądania  |Opis  |
   |---------|---------|
   |*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
   |*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.        | 

   **Treść żądania**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Jeśli maszyna wirtualna ma zarówno tożsamości zarządzane przypisane do systemu, jak i przypisane przez użytkownika, można usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika, przełączając się na używanie tylko tożsamości zarządzanej przypisanej do systemu za pomocą następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
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
    
Jeśli maszyna wirtualna ma tylko tożsamości zarządzane przypisane przez użytkownika i chcesz usunąć je wszystkie, użyj następującego polecenia:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Nagłówki żądań**

|Nagłówek żądania  |Opis  |
|---------|---------|
|*Typ zawartości*     | Wymagany. Ustaw wartość `application/json`.        |
|*Autoryzacji*     | Wymagany. Ustaw prawidłowy `Bearer` token dostępu.| 

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

- [Tworzenie, wyświetlanie listy lub usuwanie tożsamości zarządzanych przypisanych przez użytkownika przy użyciu wywołań interfejsu API REST](how-to-manage-ua-identity-rest.md)
