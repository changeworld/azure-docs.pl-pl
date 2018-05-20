---
title: Wdrażanie i uaktualnianie aplikacji i usług z usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacje i usługi do klastra usługi sieć szkieletowa usług za pomocą szablonu usługi Azure Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: 8c8ee30a603d439c0fadd0c1569813762bdf4351
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Zarządzanie aplikacjami i usługami jako zasoby usługi Azure Resource Manager

Aplikacje i usługi można wdrożyć na klaster sieci szkieletowej usług za pośrednictwem usługi Azure Resource Manager. Oznacza to, że zamiast wdrażanie aplikacji i zarządzanie nimi za pomocą programu PowerShell lub interfejsu wiersza polecenia po potrzeby czekania na klaster będzie gotowa, można teraz express aplikacji i usług w formacie JSON i wdrożyć je w tym samym szablonie usługi Resource Manager co klaster. Proces rejestracji aplikacji, inicjowania obsługi administracyjnej i wdrażania wszystkie odbywa się w jednym kroku.

Jest to zalecany sposób wdrażania wszystkie ustawienia, ładu lub aplikacje do zarządzania klastra, które są potrzebne w klastrze. Obejmuje to [poprawki aplikacji aranżacji](service-fabric-patch-orchestration-application.md), Watchdogs lub wszystkie aplikacje, które muszą być uruchomione w klastrze, przed wdrożeniem innych aplikacji lub usług. 

W razie potrzeby Zarządzaj aplikacjami jako zasoby usługi Resource Manager, aby poprawić:
* Dziennik inspekcji: Menedżer zasobów inspekcja każdej operacji i przechowuje szczegółowe *dziennik aktywności* pomaga śledzić zmiany wprowadzone do tych aplikacji i klastra.
* Kontrola dostępu oparta na rolach (RBAC): zarządzanie dostępem do klastrów, a także aplikacje wdrożone w klastrze może odbywać się za pomocą tego samego szablonu usługi Resource Manager.
* Usługa Azure Resource Manager (za pośrednictwem portalu Azure) staje się na jednej stop sklep zarządzania klastrów i wdrożeń aplikacji krytycznych.

Poniższy fragment kodu przedstawia różne rodzaje zasoby, które mogą być zarządzane za pomocą szablonu:

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

1. Przygotuj szablon Menedżera zasobów klastra dla wdrożenia. Zobacz [tworzenia klastra usługi sieć szkieletowa usług za pomocą usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) uzyskać więcej informacji na ten.
2. Zastanowić aplikacje, które planujesz wdrożenie w klastrze. Czy istnieją, które będzie zawsze uruchamiana który innych aplikacji może potrwać zależności? Czy planujesz wdrożenie klastra zarządzania ani instalacji aplikacji? Tego rodzaju aplikacje najlepiej są zarządzane za pomocą szablonu usługi Resource Manager, zgodnie z powyższym opisem. 
3. Po w przypadku znalezienia aplikacji, które mają być wdrożone w ten sposób, aplikacje muszą być spakowane, pliki z rozszerzeniem zip i umieścić w udziale plików. Udział musi być dostępna przez punkt końcowy REST dla usługi Azure Resource Manager można używać podczas wdrażania.
4. W szablonie usługi Resource Manager poniżej Twoje zgłoszenie klastra opis właściwości każdej aplikacji. Te właściwości obejmują repliki lub wystąpienia licznika i łańcuchów wszelkie zależności między zasobami (inne aplikacje lub usługi). Aby uzyskać listę właściwości kompleksowe, zobacz [specyfikacji programu Swagger interfejsu API REST](https://aka.ms/sfrpswaggerspec). Należy pamiętać, że to rozwiązanie nie eliminuje aplikacja lub usługa manifesty, ale raczej opisano niektóre co znajduje się w ich w ramach szablonu usługi Resource Manager klastra. Poniżej przedstawiono przykładowy szablon, która obejmuje wdrażanie usługi bezstanowej *Service1* i usługi stanowej *klienta2* jako część *Application1*:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
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
  > *ApiVersion* musi mieć ustawioną `"2017-07-01-preview"`. Tego szablonu można także wdrożyć niezależnie od klastra, tak długo, jak klaster został już wdrożony.

5. Wdrażanie! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Zarządzanie istniejącą aplikację za pomocą Menedżera zasobów

Jeśli klaster jest już się i uzyskać niektóre aplikacje, że chcesz zarządzać jako Menedżera zasobów zasoby są już na nim wdrożone, zamiast usunięcie aplikacji i ponowne ich wdrożenie, można użyć wywołanie PUT przy użyciu tych samych interfejsów API do aplikacji potwierdzony jako zasoby usługi Resource Manager. 


## <a name="next-steps"></a>Kolejne kroki

* Użyj [interfejsu wiersza polecenia usługi sieć szkieletowa](service-fabric-cli.md) lub [PowerShell](service-fabric-deploy-remove-applications.md) do wdrażania innych aplikacji do klastra. 
* [Uaktualnić klaster sieci szkieletowej usług](service-fabric-cluster-upgrade.md)

