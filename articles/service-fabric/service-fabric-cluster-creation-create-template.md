---
title: Tworzenie szablonu klastra sieci szkieletowej usług Azure
description: Dowiedz się, jak utworzyć szablon Menedżera zasobów dla klastra sieci szkieletowej usług. Konfigurowanie zabezpieczeń, usługi Azure Key Vault i usługi Azure Active Directory (Azure AD) do uwierzytelniania klienta.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: a00f2bc505acd89d9fb9488565b6235bf7d146ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258878"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Tworzenie szablonu Menedżera zasobów klastra sieci szkieletowej usług

[Klaster sieci szkieletowej usług Azure](service-fabric-deploy-anywhere.md) to połączony z siecią zestaw maszyn wirtualnych, na których mikrousługi są wdrażane i zarządzane. Klaster sieci szkieletowej usług uruchomiony na platformie Azure jest zasobem platformy Azure i jest wdrażany, zarządzany i monitorowany za pomocą Menedżera zasobów.  W tym artykule opisano sposób tworzenia szablonu Menedżera zasobów dla klastra sieci szkieletowej usług uruchomionego na platformie Azure.  Po zakończeniu szablonu można [wdrożyć klaster na platformie Azure](service-fabric-cluster-creation-via-arm.md).

Zabezpieczenia klastra są konfigurowane, gdy klaster jest najpierw skonfigurowany i nie można go później zmienić. Przed skonfigurowaniem klastra należy zapoznać się ze [scenariuszami zabezpieczeń klastra sieci szkieletowej usług][service-fabric-cluster-security]. Na platformie Azure sieć szkieletowa usług używa certyfikatu x509 do zabezpieczania klastra i jego punktów końcowych, uwierzytelniania klientów i szyfrowania danych. Usługa Azure Active Directory jest również zalecane do bezpiecznego dostępu do punktów końcowych zarządzania. Dzierżawy usługi Azure AD i użytkownicy muszą zostać utworzone przed utworzeniem klastra.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure AD w celu uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

Przed wdrożeniem klastra produkcyjnego do uruchamiania obciążeń produkcyjnych należy najpierw zapoznać się [z listą kontrolną Gotowość produkcyjna.](service-fabric-production-readiness-checklist.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
Przykładowe szablony Menedżera zasobów są dostępne w [przykładach platformy Azure w usłudze GitHub.](https://github.com/Azure-Samples/service-fabric-cluster-templates) Te szablony mogą służyć jako punkt wyjścia dla szablonu klastra.

W tym artykule użyto przykładowego szablonu i parametrów [szablonu klastra bezpiecznego z pięcioma węzłami.][service-fabric-secure-cluster-5-node-1-nodetype] Pobierz *azuredeploy.json* i *azuredeploy.parameters.json* na komputer i otwórz oba pliki w ulubionym edytorze tekstu.

> [!NOTE]
> W przypadku chmur krajowych (Azure Government, Azure China, `fabricSettings` Azure Germany) `AADLoginEndpoint` `AADTokenEndpointFormat` należy `AADCertEndpointFormat`również dodać do szablonu następujące elementy: i .

## <a name="add-certificates"></a>Dodawanie certyfikatów
Dodawanie certyfikatów do szablonu Menedżera zasobów klastra można było odwoływać się do magazynu kluczy zawierającego klucze certyfikatów. Dodaj te parametry i wartości magazynu kluczy w pliku parametrów szablonu Menedżera zasobów (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Dodawanie wszystkich certyfikatów do zestawu skalowania maszyny wirtualnej osProfile
Każdy certyfikat zainstalowany w klastrze musi być skonfigurowany w sekcji **osProfile** zasobu zestawu skalowania (Microsoft.Compute/virtualMachineScaleSets). Ta akcja nakazuje dostawcy zasobów zainstalowanie certyfikatu na maszynach wirtualnych. Ta instalacja obejmuje zarówno certyfikat klastra, jak i wszelkie certyfikaty zabezpieczeń aplikacji, które mają być używane dla aplikacji:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurowanie certyfikatu klastra sieci szkieletowej usług

Certyfikat uwierzytelniania klastra musi być skonfigurowany zarówno w zasobie klastra sieci szkieletowej usług (Microsoft.ServiceFabric/clusters), jak i w rozszerzeniu sieci szkieletowej usług dla zestawów skalowania maszyn wirtualnych w zasobie zestawu skalowania maszyny wirtualnej. To rozwiązanie umożliwia dostawcy zasobów sieci szkieletowej usług skonfigurować go do użytku do uwierzytelniania klastra i uwierzytelniania serwera dla punktów końcowych zarządzania.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Dodawanie informacji o certyfikacie jako zasób zestawu skalowania maszyny wirtualnej

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Dodawanie informacji o certyfikatach do zasobu klastra sieci szkieletowej usług

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Dodawanie konfiguracji usługi Azure AD do użycia na potrzeby dostępu klientów

Konfigurację usługi Azure AD można dodać do szablonu Menedżera zasobów klastra, odwołując się do magazynu kluczy zawierającego klucze certyfikatów. Dodaj te parametry i wartości usługi Azure AD w pliku parametrów szablonu usługi Resource Manager (*azuredeploy.parameters.json*). 

> [!NOTE]
> W systemie Linux dzierżawy usługi Azure AD i użytkownicy muszą zostać utworzone przed utworzeniem klastra.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure AD w celu uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Wypełnianie pliku parametrów wartościami

Na koniec użyj wartości danych wyjściowych z magazynu kluczy i poleceń programu Azure AD PowerShell, aby wypełnić plik parametrów.

Jeśli planujesz używać modułów RM programu PowerShell usługi azure, nie trzeba wypełniać informacji o certyfikatach klastra. Jeśli chcesz, aby system wygenerował certyfikat z podpisem własnym dla zabezpieczeń klastra, po prostu zachowaj je jako null. 

> [!NOTE]
> Aby moduły RM pobierały i wypełniały te puste wartości parametrów, nazwy parametrów są bardzo zgodne z poniższymi nazwami

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Jeśli używasz certyfikatów aplikacji lub używasz istniejącego klastra, który został przekazany do magazynu kluczy, musisz uzyskać te informacje i wypełnić je.

Moduły RM nie mają możliwości generowania konfiguracji usługi Azure AD dla Ciebie, więc jeśli planujesz użyć usługi Azure AD dla dostępu klienta, należy wypełnić go.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testowanie szablonu
Użyj następującego polecenia programu PowerShell, aby przetestować szablon Menedżera zasobów za pomocą pliku parametrów:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku, gdy napotkasz problemy i otrzymasz tajemnicze wiadomości, użyj opcji "-Debug".

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Na poniższym diagramie przedstawiono, gdzie magazyn kluczy i konfiguracja usługi Azure AD pasują do szablonu Menedżera zasobów.

![Mapa zależności Menedżera zasobów][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz szablon dla klastra, dowiedz się, jak [wdrożyć klaster na platformie Azure](service-fabric-cluster-creation-via-arm.md).  Jeśli jeszcze tego nie zrobiłeś, przeczytaj [listę kontrolną gotowość produkcyjną](service-fabric-production-readiness-checklist.md) przed wdrożeniem klastra produkcyjnego.

Aby dowiedzieć się więcej o składni JSON i właściwościach zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft.ServiceFabric/klastry](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageKondyje](/azure/templates/microsoft.storage/storageaccounts)
* [Sieć Microsoft.Network/virtual Sieci](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
