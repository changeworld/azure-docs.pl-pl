---
title: Procedura uaktualniania/migrowanych jednostki SKU PrimaryNodeType do bardziej zaawansowanych wersji | Dokumentacja firmy Microsoft
description: Informacje o sposobie uaktualniania/migracji jednostki SKU dla PrimaryNodeType do bardziej zaawansowanych wersji za pomocą poleceń programu PowerShell i polecenia interfejsu wiersza polecenia.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 96956543a44b6d5d967e3bae3fd833b08baf3d6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642737"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Uaktualnienie/migracji jednostki SKU dla typu węzła podstawowego do nowszej wersji produktu

W tym artykule opisano sposób uaktualniania/migracji SKU z podstawowy typ węzła z klastra sieci szkieletowej usług do nowszej wersji produktu przy użyciu programu Azure PowerShell

## <a name="add-a-new-virtual-machine-scale-set"></a>Dodaj nowy zestaw skali maszyny wirtualnej

Wdróż nowy zestaw skali maszyny wirtualnej i modułu równoważenia obciążenia. Konfiguracja rozszerzenia sieci szkieletowej usług (szczególnie typ węzła) nowy zestaw skali maszyny wirtualnej należy się, że próbujesz tak samo jak stare zestawu skalowania maszyny wirtualnej można uaktualnić. Sprawdź w programie Service Fabric explorer, że nowe węzły są dostępne

#### <a name="azure-powershell"></a>Azure PowerShell

W poniższym przykładzie użyto programu Azure PowerShell, aby wdrożyć zaktualizowany szablon usługi Resource Manager *template.json* przy użyciu grupy zasobów o nazwie *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile template.json -TemplateParameterFile parameters.json
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenie używa interfejsu wiersza polecenia Azure Service Fabric do wdrażania zaktualizowany szablon usługi Resource Manager *template.json* przy użyciu grupy zasobów o nazwie *myResourceGroup*:

```CLI
az group deployment create --resource-group myResourceGroup --template-file template.json --parameters parameters.json
```

Zobacz następujący przykład, aby zmodyfikować szablon json, aby dodać nowy zasób zestawu skali maszyny wirtualnej z węzła podstawowego typu w istniejącym klastrze

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```

## <a name="remove-old-virtual-machine-scale-set"></a>Usuń stary zestaw skali maszyny wirtualnej

1. Wyłącz wystąpień maszyny Wirtualnej stary zestaw z zamiarem Usuń węzeł skalowania maszyny wirtualnej. Ta operacja może potrwać bardzo długo. Można wyłączyć w całości i będzie można umieścić w kolejce. Zaczekaj, aż wszystkie węzły są wyłączone. 
#### <a name="azure-powershell"></a>Azure PowerShell
W poniższym przykładzie użyto programu PowerShell usługi Azure Service Fabric, aby wyłączyć nazwane wystąpienia węzła *NTvm1_0* od starego skali maszyny wirtualnej ustawić nazwane *NTvm1*:
```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode
```
#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Polecenie używa interfejsu wiersza polecenia Azure Service Fabric wyłączyć nazwane wystąpienia węzła *NTvm1_0* od starego skali maszyny wirtualnej ustawić nazwane *NTvm1*:
```CLI
sfctl node disable --node-name "_NTvm1_0" --deactivation-intent RemoveNode
```
2. Usuń zestaw skalowania ukończone. Zaczekaj, aż stan inicjowania obsługi zestawu skalowania jest powiodło się.
#### <a name="azure-powershell"></a>Azure PowerShell
W poniższym przykładzie użyto programu Azure PowerShell, aby usunąć zestaw o nazwie skalowania pełną *NTvm1* z grupy zasobów o nazwie *myResourceGroup*:
```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```
#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Polecenie używa interfejsu wiersza polecenia Azure Service Fabric usunąć pełny zestaw o nazwie skalowania *NTvm1* z grupy zasobów o nazwie *myResourceGroup*:

```CLI
az vmss delete --name NTvm1 --resource-group myResourceGroup
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Usuń moduł równoważenia obciążenia związane z stary zestaw skali

Usuń moduł równoważenia obciążenia związane z stary zestaw skali. Ten krok spowoduje, że przez krótki czas przestoju klastra

#### <a name="azure-powershell"></a>Azure PowerShell

W poniższym przykładzie użyto programu Azure PowerShell, aby usunąć usługi równoważenia obciążenia o nazwie *LB-Mójklaster NTvm1* powiązany zestaw z grupy zasobów o nazwie starego skalowania *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie używa interfejsu wiersza polecenia Azure Service Fabric, aby usunąć usługi równoważenia obciążenia o nazwie *LB-Mójklaster NTvm1* powiązany zestaw z grupy zasobów o nazwie starego skalowania *myResourceGroup*:

```CLI
az network lb delete --name LB-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Usuń publiczny adres IP powiązane z stary zestaw skali

Ustawienia DNS sklepu publicznego adresu IP powiązane z starej skali wartość do zmiennej, a następnie usuń publiczny adres IP

#### <a name="azure-powershell"></a>Azure PowerShell

W poniższym przykładzie użyto programu Azure PowerShell do przechowywania ustawień DNS dla publicznego adresu IP o nazwie *LBIP-Mójklaster NTvm1* do zmiennej i Usuń adres IP:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenie używa interfejsu wiersza polecenia Azure Service Fabric, aby uzyskać ustawienia DNS dla publicznego adresu IP o nazwie *LBIP-Mójklaster NTvm1* i Usuń adres IP:

```CLI
az network public-ip show --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
az network public-ip delete --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Aktualizacja publiczny adres IP powiązane z nowy zestaw skali

Ustawienia aktualizacji DNS publiczny adres IP powiązane z nowej skali ustawiony za pomocą ustawień DNS publiczny adres IP powiązane z stary zestaw skali

#### <a name="azure-powershell"></a>Azure PowerShell
W poniższym przykładzie użyto programu Azure PowerShell, aby zaktualizować ustawienia serwera DNS o nazwie publicznego adresu IP *LBIP-Mójklaster NTvm3* przy użyciu ustawień DNS przechowywane w zmiennych w poprzednim kroku:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm3  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenie używa interfejsu wiersza polecenia Azure Service Fabric można zaktualizować ustawień DNS publicznego adresu IP o nazwie *LBIP-Mójklaster NTvm3* z ustawień DNS starych publicznego adresu IP zebrane w poprzednim kroku:

```CLI
az network public-ip update --name LBIP-myCluster-NTvm3 --resource-group myResourceGroup --dns-name myCluster
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Usuwanie wiedzy węzła sieci szkieletowej usługi FM

Powiadomienia usługi sieć szkieletowa węzły, które działają zostały usunięte z klastra. (Uruchom to polecenie dla wszystkich maszyn wirtualnych poprzedniego wystąpienia zestawu skali maszyny wirtualnej) (Jeśli poziom trwałości stary zestaw skali maszyny wirtualnej silver lub złota, ten krok może nie być potrzebne. Ponieważ tego kroku jest wykonywane przez system automatycznie.)

#### <a name="azure-powershell"></a>Azure PowerShell
W poniższym przykładzie użyto programu PowerShell usługi Azure Service Fabric powiadomiono usługi service fabric, który węzeł o nazwie *NTvm1_0* został usunięty:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Polecenie używa interfejsu wiersza polecenia Azure Service Fabric powiadomiono usługi service fabric, który węzeł o nazwie *NTvm1_0* został usunięty:

```CLI
sfctl node remove-state --node-name _NTvm1_0
```
