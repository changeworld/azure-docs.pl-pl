---
title: Automatyczne wdrażanie maszyn wirtualnych z przewodnikiem szybki start konfiguracji aplikacji platformy Azure
description: Ten przewodnik Szybki start pokazuje, jak wdrożyć sklep konfiguracji aplikacji Azure App Configuration za pomocą modułu Azure PowerShell i szablonów usługi Azure Resource Manager. Następnie użyj wartości w magazynie, aby wdrożyć maszynę wirtualną.
author: lisaguthrie
ms.author: lcozzens
ms.date: 04/14/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 02afa2cb36323e0c3c38c2451b1924b636f7faed
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309109"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Szybki start: automatyczne wdrażanie maszyn wirtualnych z szablonem Konfiguracja aplikacji i Menedżer zasobów

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą poleceń cmdlet programu PowerShell lub skryptów. Ten przewodnik Szybki start pokazuje, jak wdrożyć sklep konfiguracji aplikacji Azure App Configuration za pomocą szablonów programu Azure PowerShell i usługi Azure Resource Manager. Następnie dowiesz się, jak używać wartości klucza w magazynie do wdrażania maszyny Wirtualnej.

Szablon warunek wstępny służy do tworzenia magazynu konfiguracji aplikacji, a następnie dodać wartości klucza do sklepu przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Szablon podstawowy odwołuje się do istniejących konfiguracji klucz-wartość z istniejącego magazynu konfiguracji. Pobrane wartości są używane do ustawiania właściwości zasobów utworzonych przez szablon, takich jak maszyna wirtualna w tym przykładzie.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto.](https://azure.microsoft.com/free/)

* Dla tego przewodnika Szybki start jest wymagany moduł Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby znaleźć wersję zainstalowaną na komputerze lokalnym. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i wprowadź swoje poświadczenia platformy Azure w podręcznym oknie przeglądarki:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Jeśli masz więcej niż jedną subskrypcję, wybierz subskrypcję, której chcesz użyć w tym przewodniku Szybki start, uruchamiając następujące polecenia cmdlet. Nie zapomnij zastąpić `<your subscription name>` nazwą subskrypcji:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Wdrażanie sklepu konfiguracji aplikacji platformy Azure

Aby można było zastosować wartości klucza do maszyny Wirtualnej, musisz mieć istniejący magazyn konfiguracji aplikacji platformy Azure. W tej sekcji opisano sposób wdrażania magazynu konfiguracji aplikacji platformy Azure przy użyciu szablonu usługi Azure Resource Manager. Jeśli masz już sklep z konfiguracami aplikacji, możesz przejść do następnej sekcji tego artykułu. 

1. Skopiuj i wklej następujący kod json do nowego pliku o nazwie *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Skopiuj i wklej następujący kod json do nowego pliku o nazwie *prereq.azuredeploy.parameters.json*. Zastąp **GET-UNIQUE** unikatową nazwą sklepu configuration store.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. W oknie programu PowerShell uruchom następujące polecenie, aby wdrożyć sklep konfiguracji aplikacji platformy Azure. Nie zapomnij zastąpić nazwy grupy zasobów, ścieżki pliku szablonu i ścieżki pliku parametru szablonu.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Dodawanie wartości klucza konfiguracji maszyny Wirtualnej

Można utworzyć magazyn konfiguracji aplikacji przy użyciu szablonu usługi Azure Resource Manager, ale należy dodać wartości kluczy przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. W tym przewodniku Szybki start można dodać wartości kluczy za pomocą witryny Azure portal.

1. Po zakończeniu wdrażania przejdź do nowo utworzonego sklepu konfiguracji aplikacji w [witrynie Azure portal](https://portal.azure.com).

1. Wybierz **pozycję Ustawienia** > **klawiszy dostępu**. Zanotuj podstawowy ciąg połączenia klucza tylko do odczytu. Ten ciąg połączenia użyjesz później, aby skonfigurować aplikację do komunikowania się z utworzonym sklepem konfiguracji aplikacji.

1. Wybierz pozycję**Tworzenie** **Eksploratora** > konfiguracji, aby dodać następujące pary klucz-wartość:

   |Klucz|Wartość|
   |-|-|
   |windowsOsVersion|2019-Centrum danych|
   |dyskSizeGB|1023|
  
   Wprowadź *szablon* **etykiety**, ale **zachowaj typ zawartości** pusty.

## <a name="deploy-vm-using-stored-key-values"></a>Wdrażanie maszyny Wirtualnej przy użyciu przechowywanych wartości klucza

Teraz, gdy dodano wartości klucza do magazynu, możesz przystąpić do wdrażania maszyny Wirtualnej przy użyciu szablonu usługi Azure Resource Manager. Szablon odwołuje się do utworzonych kluczy **windowsOsVersion** i **diskSizeGB.**

> [!WARNING]
> Szablony ARM nie mogą odwoływać się do kluczy w magazynie konfiguracji aplikacji z włączonym łączem prywatnym.

1. Skopiuj i wklej następujący kod json do nowego pliku o nazwie *azuredeploy.json*lub pobierz plik z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Skopiuj i wklej następujący kod json do nowego pliku o nazwie *azuredeploy.parameters.json*lub pobierz plik z [szablonów szybkiej platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Zastąp wartości parametrów w szablonie następującymi wartościami:

   |Parametr|Wartość|
   |-|-|
   |adminPassword|Hasło administratora maszyny Wirtualnej.|
   |nazwa aplikacjiConfigStoreName|Nazwa sklepu konfiguracji aplikacji platformy Azure.|
   |grupa appConfigStoreResourceGroup|Grupa zasobów zawierająca magazyn konfiguracji aplikacji.|
   |klucz wirtualny|*windowsOSVersion*|
   |diskSizeKey|*dyskSizeGB*|
   |adminUsername|Nazwa użytkownika administratora maszyny Wirtualnej.|
   |storageAccountName|Unikatowa nazwa konta magazynu skojarzonego z maszyną wirtualną.|
   |nazwa domenyLabel|Unikatowa nazwa domeny.|

1. W oknie programu PowerShell uruchom następujące polecenie, aby wdrożyć maszynę wirtualną. Nie zapomnij zastąpić nazwy grupy zasobów, ścieżki pliku szablonu i ścieżki pliku parametru szablonu.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>"
       -TemplateFile "<path to azuredeploy.json>" `
       -TemplateParameterFile "<path to azuredeploy.parameters.json>"
   ```

Gratulacje! Maszyna wirtualna została wdrożona przy użyciu konfiguracji przechowywanych w konfiguracji aplikacji platformy Azure.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, usuń grupę zasobów, magazyn konfiguracji aplikacji, maszynę wirtualną i wszystkie powiązane zasoby. Jeśli planujesz korzystać z magazynu konfiguracji aplikacji lub maszyny Wirtualnej w przyszłości, możesz pominąć jej usunięcie. Jeśli nie planujesz używania tego zadania w przyszłości, usuń wszystkie zasoby utworzone w ramach tego przewodnika Szybki start, uruchamiając następujące polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono maszynę wirtualną przy użyciu szablonu usługi Azure Resource Manager i wartości kluczy z konfiguracji aplikacji platformy Azure.

Aby dowiedzieć się więcej o tworzeniu innych aplikacji za pomocą usługi Azure App Configuration, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
> [Szybki start: tworzenie aplikacji ASP.NET Core za pomocą konfiguracji aplikacji platformy Azure](quickstart-aspnet-core-app.md)
