---
title: Wdrażanie i uaktualnianie aplikacji i usług za pomocą usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacje i usługi w klastrze usługi Service Fabric przy użyciu szablonu usługi Azure Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e2e1b2ae354d26c3d9729e3a3fdf39bee43647ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621466"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Zarządzanie aplikacjami i usługami jako zasoby usługi Azure Resource Manager

Możesz wdrażać aplikacje i usługi w klastrze usługi Service Fabric za pośrednictwem usługi Azure Resource Manager. Oznacza to, że zamiast wdrażania aplikacji i zarządzanie nimi za pomocą programu PowerShell lub interfejsu wiersza polecenia po musisz czekać, aż klaster będzie gotowa, można teraz express, aplikacje i usługi w formacie JSON i wdróż je w szablonie usługi Resource Manager tym samym co klaster. Proces rejestracji, aprowizacji i wdrażania aplikacji obejmuje tylko jeden krok.

Jest to zalecany sposób wdrożyć wszystkie ustawienia, nadzoru lub aplikacji do zarządzania klastrem, wymagających w klastrze. Obejmuje to [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), Watchdogs lub wszystkie aplikacje, które musi być uruchomiona w klastrze, przed wdrożeniem innymi aplikacjami lub usługami. 

Jeśli ma to zastosowanie, zarządzanie aplikacjami jako zasoby usługi Resource Manager w celu:
* Dziennik inspekcji: Menedżer zasobów inspekcje każdej operacji i przechowuje szczegółowe *dziennika aktywności* śledzenia wszelkie zmiany wprowadzone do tych aplikacji i klastra, może pomóc.
* Kontrola dostępu oparta na rolach (RBAC): Zarządzanie dostępem do klastrów, a także aplikacji wdrożonych w klastrze może odbywać się za pomocą tego samego szablonu usługi Resource Manager.
* Usługa Azure Resource Manager (za pośrednictwem witryny Azure portal) staje się jedną — — znajdziesz zarządzania klastrem i wdrożeń aplikacja o krytycznym znaczeniu.

Poniższy fragment kodu przedstawia różnych rodzajów zasobów, które mogą być zarządzane za pomocą szablonu:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Dodaj nową aplikację do szablonu usługi Resource Manager

1. Szablon usługi Resource Manager klastra przygotowanie do wdrożenia. Zobacz [tworzenia klastra usługi Service Fabric za pomocą usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) Aby uzyskać więcej informacji na ten temat.
2. Pomyśl o niektórych aplikacji, które planujesz wdrożenie w klastrze. Czy istnieją w tych, które będzie zawsze uruchamiana, inne aplikacje może zająć zależności? Czy planowane jest na wdrażanie, Zarządzanie klastrem ani instalacji aplikacji? Tego rodzaju aplikacje najlepiej odbywa się za pomocą szablonu usługi Resource Manager, zgodnie z powyższym opisem. 
3. Po mają wybierana. jakie aplikacje, które mają być wdrożone w ten sposób, aplikacje mają spakowane, zip i umieść w udziale plików. Udział musi być dostępny za pośrednictwem punktu końcowego REST dla usługi Azure Resource Manager można używać podczas wdrażania.
4. W szablonie usługi Resource Manager poniżej swojej deklaracji klastra opisują właściwości każdej aplikacji. Te właściwości obejmują liczby replik lub wystąpień i wszelkich łańcuchów zależności między zasobami (innymi aplikacjami lub usługami). Aby uzyskać listę właściwości kompleksowe, zobacz [Specyfikacja Swagger interfejsu API REST](https://aka.ms/sfrpswaggerspec). Należy pamiętać, że nie zastępuje to aplikacja lub usługa manifesty, ale raczej opisano niektóre co znajduje się w ich w ramach szablonu usługi Resource Manager klastra. Poniżej przedstawiono przykładowy szablon zawierający wdrażania usługi bezstanowej *Service1* i usługi stanowej *klienta2* jako część *Application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > *ApiVersion* musi być równa `"2017-07-01-preview"`. Tego szablonu można także wdrożyć niezależnie od klastra, tak długo, jak klastra została już wdrożona.

5. Wdrażanie! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Zarządzanie istniejącej aplikacji za pomocą usługi Resource Manager

Jeśli klaster już działa, a niektóre aplikacje, czy chcesz zarządzać jako menedżerem zasobów są już na nim wdrożone, zamiast usuwania aplikacji i ponowne ich wdrożenie, można użyć wywołania PUT przy użyciu tych samych interfejsów API do aplikacji, Pobierz potwierdzony jako zasoby usługi Resource Manager. 

> [!NOTE]
> Aby umożliwić uaktualniania klastra, aby zignorować aplikacje w złej kondycji klienta można określić "maxPercentUnhealthyApplications: 100" w sekcji" upgradeDescription/healthPolicy"; szczegółowy opis dla wszystkich ustawień znajdują się w [dokumentacji usługi sieci szkieletowe REST API klastra zasady uaktualniania](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Kolejne kroki

* Użyj [interfejsu wiersza polecenia usługi Service Fabric](service-fabric-cli.md) lub [PowerShell](service-fabric-deploy-remove-applications.md) do wdrażania innych aplikacji do klastra. 
* [Uaktualnianie klastra usługi Service Fabric](service-fabric-cluster-upgrade.md)

