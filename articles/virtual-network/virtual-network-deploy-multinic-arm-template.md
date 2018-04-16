---
title: "Utwórz maszynę Wirtualną z wieloma kartami sieciowymi — szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft"
description: "Utwórz maszynę Wirtualną z wieloma kartami sieciowymi przy użyciu szablonu usługi Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Utwórz maszynę Wirtualną z wieloma kartami sieciowymi przy użyciu szablonu
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md).  Ten artykuł dotyczy używania modelu wdrażania usługi Resource Manager zalecanego przez firmę Microsoft w przypadku większości nowych wdrożeń zamiast [klasycznego modelu wdrażania](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poniższe kroki Użyj grupy zasobów o nazwie *IaaSStory* dla serwerów sieci WEB i grupy zasobów o nazwie *IaaSStory zaplecza* dla serwerów baz danych.

## <a name="prerequisites"></a>Wymagania wstępne
Przed utworzeniem serwerów bazy danych, należy utworzyć *IaaSStory* grupy zasobów z wszystkie niezbędne zasoby dotyczące tego scenariusza. Aby utworzyć tych zasobów, wykonaj następujące kroki:

1. Przejdź do [strony szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na stronie szablonu z prawej strony **grupy zasobów nadrzędnej**, kliknij przycisk **wdrażanie na platformie Azure**.
3. W razie potrzeby zmień wartości parametrów, a następnie wykonaj kroki w portalu Azure w wersji zapoznawczej, aby wdrożyć grupę zasobów.

> [!IMPORTANT]
> Upewnij się, że nazwy konta magazynu są unikatowe. Nie może mieć nazwy konta magazynu zduplikowanych na platformie Azure.
> 

## <a name="understand-the-deployment-template"></a>Zrozumienie szablonu wdrożenia
Przed przystąpieniem do wdrażania szablonu dostarczanego z tej dokumentacji, upewnij się, że rozumiesz, jakie operacje. W poniższych krokach przedstawiono omówienie szablonu:

1. Przejdź do [strony szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Kliknij przycisk **azuredeploy.json** można otworzyć pliku szablonu.
3. Powiadomienie *osType* parametrów wymienionych poniżej. Ten parametr jest używany do wybierania obrazów maszyny Wirtualnej serwera bazy danych, wraz z wielu systemów operacyjnych powiązane ustawienia.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Przewiń w dół listę zmiennych i sprawdź definicję **dbVMSetting** zmienne wymienione poniżej. Odbiera jeden z elementów tablicy zawarte w **dbVMSettings** zmiennej. Jeśli znasz terminologii rozwoju oprogramowania, możesz wyświetlić **dbVMSettings** zmiennej jako tablicy skrótów lub w słowniku.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Załóżmy, że użytkownik chce wdrożyć maszyn wirtualnych systemu Windows, programem SQL w wewnętrznej. Następnie wartość **osType** będzie *Windows*i **dbVMSetting** zmiennej może zawierać elementu wymienione poniżej, co stanowi pierwszą wartość **dbVMSettings** zmiennej.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Powiadomienie **vmSize** zawiera wartość *Standard_DS3*. Niektórych rozmiarów maszyn wirtualnych umożliwiają korzystanie z wielu kart sieciowych. Możesz sprawdzić rozmiarów maszyn wirtualnych, które obsługują wiele kart sieciowych, odczytując [rozmiarów maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i [rozmiarów maszyn wirtualnych systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykułów.

7. Przewiń w dół do **zasobów** i zwróć uwagę, pierwszego elementu. Opisuje konta magazynu. To konto magazynu będzie używane do obsługi dysków danych używany przez każdą bazę danych maszyny Wirtualnej. W tym scenariuszu każda baza danych maszyny Wirtualnej ma dysku systemu operacyjnego przechowywanego w regularnych magazynu oraz dwóch dysków danych przechowywanych w magazynie SSD (premium).

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Przewiń w dół do następnego zasobu wymienione poniżej. Ten zasób reprezentuje używane do dostępu do bazy danych w każdej bazie danych maszyny Wirtualnej karty Sieciowej. Zwróć uwagę na **kopiowania** funkcji w przypadku tego zasobu. Ten szablon umożliwia wdrażanie jako wiele maszyn wirtualnych można dowolnie na podstawie **dbCount** parametru. Dlatego należy utworzyć tę samą wartość, kart sieciowych dla dostępu do bazy danych, po jednej dla każdej maszyny Wirtualnej.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Przewiń w dół do następnego zasobu wymienione poniżej. Ten zasób reprezentuje używany do zarządzania w każdej bazie danych maszyny Wirtualnej karty Sieciowej. Ponownie potrzebujesz jednego z tych kart sieciowych dla każdej maszyny Wirtualnej bazy danych. Powiadomienie **grupy networkSecurityGroup** element NSG, która zezwala na dostęp do protokołu RDP/SSH do tej karty Sieciowej tylko połączeń.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Przewiń w dół do następnego zasobu wymienione poniżej. Ten zasób reprezentuje zbiór być współużytkowane przez wszystkie maszyny wirtualne z bazy danych dostępności. W ten sposób można zagwarantować, że zawsze będzie jedna maszyna wirtualna w zestawie uruchomiony podczas konserwacji.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Przewiń w dół do następnego zasobu. Ten zasób reprezentuje bazy danych maszyn wirtualnych, jak pokazano w pierwszym kilka wierszy wymienionych poniżej. Zwróć uwagę na **kopiowania** funkcji ponownie, zapewniając, że wiele maszyn wirtualnych są tworzone na podstawie **dbCount** parametru. Ponadto **dependsOn** kolekcji. Wyświetlane są dwie karty sieciowe są niezbędne do utworzenia przed wdrożeniem maszyny Wirtualnej, wraz z zestawu dostępności i konto magazynu.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Przewiń w dół w zasobu maszyny Wirtualnej do **networkProfile** elementu wymienione poniżej. Zwróć uwagę, że istnieją dwie karty sieciowe są odwołania dla każdej maszyny Wirtualnej. Podczas tworzenia wielu kart sieciowych maszyny wirtualnej, należy ustawić **głównej** właściwości jednej z kart sieciowych do *true*, a reszta do *false*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Wdrażanie szablonu ARM przy użyciu metody „kliknij, aby wdrożyć”

> [!IMPORTANT]
> Wykonaj [wstępne](#Pre-requisites) kroki przed zgodnie z poniższymi instrukcjami.
> 

Przykładowy szablon dostępny w repozytorium publicznym korzysta z pliku parametrów zawierającego wartości domyślne używane do generowania scenariusza opisanego powyżej. Aby wdrożyć tego szablonu można wdrożyć przy użyciu kliknij przycisk, wykonaj [to łącze](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), z prawej strony **grupy zasobów w wewnętrznej bazie danych (zobacz dokumentację)** kliknij **wdrażanie na platformie Azure**, Zastąp domyślne wartości parametrów, jeśli to konieczne i postępuj zgodnie z instrukcjami w portalu.

Na poniższym rysunku przedstawiono zawartość nową grupę zasobów, po wdrożeniu.

![Wewnętrzna grupa zasobów](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Wdrażanie szablonu przy użyciu programu PowerShell
Aby wdrożyć szablon został pobrany przy użyciu programu PowerShell, instalowanie i konfigurowanie programu PowerShell, wykonując kroki opisane w [Instalowanie i konfigurowanie programu PowerShell](/powershell/azure/overview) artykuł, a następnie wykonaj następujące kroki:

Uruchom  **`New-AzureRmResourceGroup`**  polecenia cmdlet, aby utworzyć grupę zasobów, przy użyciu szablonu.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Oczekiwane dane wyjściowe:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Wdrażanie szablonu przy użyciu interfejsu wiersza polecenia platformy Azure
Aby wdrożyć szablon przy użyciu interfejsu wiersza polecenia platformy Azure, wykonaj poniższe kroki.

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz artykuł [Instalowanie i konfigurowania interfejsu wiersza polecenia Azure](../cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do punktu, w którym należy wybrać konto platformy Azure i subskrypcję.
2. Uruchom polecenie **`azure config mode`**, aby włączyć tryb Resource Manager, jak pokazano poniżej.

    ```azurecli
    azure config mode arm
    ```

    Oczekiwane dane wyjściowe są następujące:

        info:    New mode is arm

3. Otwórz [pliku parametrów](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), zaznacz zawartością i zapisać go w pliku na swoim komputerze. W tym przykładzie zapisaliśmy plik parametrów w pliku *parameters.json*.
4. Uruchom polecenie cmdlet **`azure group deployment create`**, aby wdrożyć nową sieć wirtualną przy użyciu uprzednio pobranego i zmodyfikowanego pliku szablonu i pliku parametrów. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Oczekiwane dane wyjściowe:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

