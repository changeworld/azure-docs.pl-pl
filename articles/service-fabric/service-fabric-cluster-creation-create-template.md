---
title: Utwórz szablon klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć szablon usługi Resource Manager dla klastra usługi Service Fabric. Konfigurowanie zabezpieczeń usługi Azure Key Vault i Azure Active Directory (Azure AD) do uwierzytelniania klientów.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 2fdea1f088dd6eabdf7d72342c837d976133a1bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046190"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Utwórz szablon usługi Resource Manager klastra usługi Service Fabric

[Klastra usługi Azure Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych, w których mikrousługi są wdrażania i zarządzania nimi. Klaster usługi Service Fabric działających na platformie Azure jest zasobem platformy Azure i jest wdrażana, zarządzane i monitorowane, przy użyciu usługi Resource Manager.  W tym artykule opisano sposób tworzenia szablonu usługi Resource Manager dla klastra usługi Service Fabric działających na platformie Azure.  Gdy szablon będzie gotowy, możesz ją [wdrażania klastra na platformie Azure](service-fabric-cluster-creation-via-arm.md).

Zabezpieczenia klastra jest skonfigurowany, gdy klaster jest najpierw skonfigurować i nie można zmienić później. Przed rozpoczęciem konfigurowania klastra, przeczytaj [scenariusze zabezpieczeń klastra usługi Service Fabric][service-fabric-cluster-security]. Na platformie Azure Usługa Service Fabric używa x509 certyfikatów do zabezpieczenia klastra i jego punkty końcowe, uwierzytelnianie klientów i szyfrowania danych. Usługa Azure Active Directory zaleca się zabezpieczenie dostępu do punktów końcowych zarządzania. Usługa Azure AD dzierżawcy i użytkownicy musi zostać utworzona przed utworzeniem klastra.  Aby uzyskać więcej informacji, przeczytaj [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

Przed wdrożeniem klastra produkcyjnego do uruchamiania obciążeń produkcji, należy najpierw przeczytać [Lista kontrolna gotowości produkcji](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager
Menedżer zasobów przykładowe szablony są dostępne w [przykładów dla platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Te szablony może służyć jako punkt wyjścia do szablonu klastra.

W tym artykule wykorzystano [bezpiecznego klastra z pięcioma węzłami] [ service-fabric-secure-cluster-5-node-1-nodetype] przykładowy szablon i parametry szablonu. Pobierz *azuredeploy.json* i *azuredeploy.parameters.json* do komputera, a następnie otwórz oba pliki w swoim ulubionym edytorze tekstów.

> [!NOTE]
> Chmur krajowych (Azure Government, Azure (Chiny) platformy Azure w Niemczech), należy również dodać następujące `fabricSettings` do szablonu: `AADLoginEndpoint`, `AADTokenEndpointFormat` i `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Dodaj certyfikaty
Należy dodać certyfikaty do szablonu usługi Resource Manager klastra, odwołując się do magazynu kluczy, która zawiera klucze certyfikatu. Dodaj te parametry magazynu kluczy i wartości w pliku parametrów szablonu usługi Resource Manager (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Dodaj wszystkie certyfikaty do osProfile zestaw skali maszyny wirtualnej
Każdy certyfikat, który jest zainstalowany w klastrze muszą być skonfigurowane w **osProfile** sekcji skali zestawu zasobów (Microsoft.Compute/virtualMachineScaleSets). Ta akcja powoduje, że dostawca zasobów, aby zainstalować certyfikat na maszynach wirtualnych. Ta instalacja obejmuje zarówno certyfikat klastra, jak i wszelkich certyfikatów zabezpieczeń aplikacji, które będą używane dla aplikacji:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurowanie certyfikatu klastra usługi Service Fabric

Certyfikat uwierzytelniania w klastrze muszą być skonfigurowane w obu usługi Service Fabric zasobu klastra (Microsoft.ServiceFabric/clusters) i rozszerzenia usługi Service Fabric dla skalowania maszyn wirtualnych zestawy w zasobu zestawu skalowania maszyny wirtualnej. To rozwiązanie umożliwia dostawcy zasobów usługi Service Fabric skonfigurować go do użycia do uwierzytelniania w klastrze i uwierzytelnianie serwera dla punktów końcowych zarządzania.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Dodawanie zasobu zestawu skalowania maszyn wirtualnych informacje o certyfikacie

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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Dodaj informacje o certyfikacie do zasobu klastra usługi Service Fabric

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

Konfiguracji usługi Azure AD jest dodawanie do szablonu usługi Resource Manager klastra, odwołując się do magazynu kluczy, która zawiera klucze certyfikatu. Dodaj te parametry usługi Azure AD i wartości w pliku parametrów szablonu usługi Resource Manager (*azuredeploy.parameters.json*). 

> [!NOTE]
> Usługa Azure AD dzierżawcy i użytkownicy musi zostać utworzona przed utworzeniem klastra.  Aby uzyskać więcej informacji, przeczytaj [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Wypełnij plik parametrów z wartościami

Na koniec użyj wartości danych wyjściowych z poleceń programu PowerShell usługi Azure AD i usługi key vault, aby wypełnić plik parametrów.

Jeśli planujesz użyć modułów programu PowerShell usługi Azure Service fabric Menedżera zasobów, nie trzeba wypełnić informacji o certyfikacie klastra. Jeśli chcesz, aby system wygenerował samodzielnie podpisanego certyfikatu do zabezpieczania klastra, wystarczy przechowywać je jako wartości null. 

> [!NOTE]
> Dla modułów RM na odebranie i wypełnić te wartości parametrów empty nazwy parametrów znacznie być zgodne z nazwami poniżej

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

Jeśli używasz certyfikatów aplikacji lub korzystasz z istniejącego klastra, które zostały przekazane do magazynu kluczy, należy uzyskać te informacje i wypełnić je.

Moduły Menedżera zasobów nie mają możliwość generowania konfiguracji usługi Azure AD, dlatego jeśli planujesz używać usługi Azure AD dla dostępu klientów, należy go wypełnić.

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

## <a name="test-your-template"></a>Test szablonu
Użyj następującego polecenia programu PowerShell na potrzeby testów w pliku parametrów szablonu usługi Resource Manager:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

W przypadku napotkania problemów i Pobierz one niezrozumiałe komunikaty, następnie za pomocą "-Debug" jako opcję.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Na poniższym diagramie przedstawiono, w którym magazyn kluczy i konfiguracji usługi Azure AD mieści się w szablonie usługi Resource Manager.

![Mapa zależności usługi Resource Manager][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się teraz, gdy szablon dla klastra, jak [wdrożenia klastra Azure](service-fabric-cluster-creation-via-arm.md).  Jeśli zrobione, zapoznaj się z [Lista kontrolna gotowości produkcji](service-fabric-production-readiness-checklist.md) przed wdrożeniem w warunkach produkcyjnych klastra.

Aby dowiedzieć się więcej na temat składni JSON i właściwości zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
