---
title: Wdrażanie i uaktualnianie za pomocą usługi Azure Resource Manager
description: Dowiedz się, jak wdrożyć aplikacje i usługi w klastrze sieci szkieletowej usług przy użyciu szablonu usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610254"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Zarządzanie aplikacjami i usługami jako zasobami usługi Azure Resource Manager

Aplikacje i usługi można wdrażać w klastrze sieci szkieletowej usług za pośrednictwem usługi Azure Resource Manager. Oznacza to, że zamiast wdrażania aplikacji i zarządzania nimi za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia po konieczności oczekiwania na gotowy klaster, można teraz wyrazić aplikacje i usługi w JSON i wdrożyć je w tym samym szablonie Menedżera zasobów co klaster. Proces rejestracji aplikacji, inicjowania obsługi administracyjnej i wdrażania odbywa się w jednym kroku.

Jest to zalecany sposób wdrażania wszelkich aplikacji do zarządzania konfiguracją, nadzoru lub klastra, które są wymagane w klastrze. Obejmuje to [aplikację aranżacji poprawek,](service-fabric-patch-orchestration-application.md)watchdogs lub wszelkie aplikacje, które muszą być uruchomione w klastrze przed wdrożeniem innych aplikacji lub usług. 

W stosownych przypadkach zarządzaj aplikacjami jako zasobami Menedżera zasobów, aby ulepszyć:
* Dziennik inspekcji: Menedżer zasobów przeprowadza inspekcje każdej operacji i przechowuje szczegółowy *dziennik aktywności,* który może pomóc w śledzeniu wszelkich zmian wprowadzonych w tych aplikacjach i klastrze.
* Kontrola dostępu oparta na rolach (RBAC): Zarządzanie dostępem do klastrów, a także aplikacji wdrożonych w klastrze można wykonać za pomocą tego samego szablonu Menedżera zasobów.
* Usługa Azure Resource Manager (za pośrednictwem witryny Azure portal) staje się kompleksowym zakładem obsługi do zarządzania klastrem i wdrożeniami aplikacji o znaczeniu krytycznym.

Poniższy fragment kodu przedstawia różne rodzaje zasobów, którymi można zarządzać za pomocą szablonu:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Dodawanie nowej aplikacji do szablonu Menedżera zasobów

1. Przygotuj szablon Menedżera zasobów klastra do wdrożenia. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie klastra sieci szkieletowej usług przy użyciu usługi Azure Resource Manager.](service-fabric-cluster-creation-via-arm.md)
2. Pomyśl o niektórych aplikacjach, które planujesz wdrożyć w klastrze. Czy są jakieś, które zawsze będą uruchomione, że inne aplikacje mogą przyjmować zależności? Czy planujesz wdrożenie jakichkolwiek aplikacji nadzoru klastra lub aplikacji instalacyjnych? Tego rodzaju aplikacje są najlepiej zarządzane za pomocą szablonu Menedżera zasobów, jak wspomniano powyżej. 
3. Po zorientowali się, jakie aplikacje, które mają być wdrażane w ten sposób, aplikacje muszą być spakowane, spakowane i umieścić na udział plików. Udział musi być dostępny za pośrednictwem punktu końcowego REST dla usługi Azure Resource Manager do wykorzystania podczas wdrażania.
4. W szablonie Menedżera zasobów poniżej deklaracji klastra opisz właściwości każdej aplikacji. Właściwości te obejmują liczbę replik lub wystąpień oraz wszelkie łańcuchy zależności między zasobami (inne aplikacje lub usługi). Aby uzyskać listę kompleksowych właściwości, zobacz [specyfikację Swagger interfejsu API REST](https://aka.ms/sfrpswaggerspec). Należy zauważyć, że nie zastępuje to manifestów aplikacji lub usługi, ale raczej opisuje niektóre z tych, co jest w nich jako część szablonu Menedżera zasobów klastra. Oto przykładowy szablon, który obejmuje wdrażanie usługi bezstanowej *Service1* i usługi stanowej *Service2* w ramach *Application1:*

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
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
   > *ApiVersion* musi być `"2019-03-01"`ustawiona na . Ten szablon można również wdrożyć niezależnie od klastra, o ile klaster został już wdrożony.

5. Wdrożyć! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Usuń zasób aplikacji dostawcy zasobów sieci szkieletowej usług
Następujące wyzwalają pakiet aplikacji do un-aprowizowana z klastra, a to oczyści miejsce na dysku używane:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Po prostu usunięcie Microsoft.ServiceFabric/clusters/application z szablonu ARM nie spowoduje anulowania obsługi administracyjnej aplikacji

>[!NOTE]
> Po zakończeniu usuwania nie powinieneś już widzieć wersji pakietu w SFX lub ARM. Nie można usunąć zasobu wersji typu aplikacji, z którego jest uruchomiona aplikacja; ARM/SFRP zapobiegnie temu. Jeśli spróbujesz anulować aprovision uruchomionego pakietu, środowisko wykonawcze SF zapobiegnie.


## <a name="manage-an-existing-application-via-resource-manager"></a>Zarządzanie istniejącą aplikacją za pośrednictwem Menedżera zasobów

Jeśli klaster jest już włączony, a niektóre aplikacje, którymi chcesz zarządzać jako zasoby Menedżera zasobów, są już wdrożone, zamiast usuwać aplikacje i ponownie je wdrażać, można użyć wywołania PUT przy użyciu tych samych interfejsów API, aby aplikacje zostały jako zasoby Menedżera zasobów. Aby uzyskać dodatkowe informacje, zobacz Co to [jest model zasobów aplikacji sieci szkieletowej usług?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Aby zezwolić na uaktualnienie klastra, aby zignorować niezdrowe aplikacje, klient może określić "maxPercentUnhealthyApplications: 100" w sekcji "upgradeDescription/healthPolicy"; szczegółowe opisy wszystkich ustawień znajdują się w [dokumentacji zasad aktualizacji klastra interfejsu API rest](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)rest .

## <a name="next-steps"></a>Następne kroki

* Użyj [interfejsu wiersza polecenia sieci szkieletowej usług](service-fabric-cli.md) lub [programu PowerShell,](service-fabric-deploy-remove-applications.md) aby wdrożyć inne aplikacje w klastrze. 
* [Uaktualnianie klastra sieci szkieletowej usług](service-fabric-cluster-upgrade.md)

