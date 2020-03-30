---
title: Automatyczne skalowanie aplikacji uruchomionej w usłudze Azure Service Fabric Mesh
description: Dowiedz się, jak skonfigurować zasady automatycznego skalowania dla usług aplikacji sieci szkieletowej usług.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461980"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Tworzenie zasad skalowania automatycznego dla aplikacji siatki sieci szkieletowej usług
Jedną z głównych zalet wdrażania aplikacji w sieci szkieletowej usługi Mesh jest możliwość łatwego skalowania usług w lub obecnie. Powinno to być używane do obsługi różnych ilości obciążenia usług lub poprawy dostępności. Można ręcznie skalować usługi w lub obecnie lub skonfigurować zasady skalowania automatycznego.

[Automatyczne skalowanie](service-fabric-mesh-scalability.md#autoscaling-service-instances) umożliwia dynamiczne skalowanie liczby wystąpień usługi (skalowanie w poziomie). Automatyczne skalowanie zapewnia dużą elastyczność i umożliwia inicjowanie obsługi administracyjnej lub usuwanie wystąpień usługi na podstawie wykorzystania procesora CPU lub pamięci.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opcje tworzenia zasad skalowania automatycznego, wyzwalacza i mechanizmu
Zasady skalowania automatycznego są definiowane dla każdej usługi, którą chcesz skalować. Zasada jest zdefiniowana w pliku zasobów usługi YAML lub szablonie wdrożenia JSON. Każda zasada skalowania składa się z dwóch części: wyzwalacza i mechanizmu skalowania.

Wyzwalacz definiuje, gdy wywoływana jest zasada skalowania automatycznego.  Określ rodzaj wyzwalacza (średnie obciążenie) i metrykę do monitorowania (procesora CPU lub pamięci).  Górne i dolne progi obciążenia określone jako wartość procentowa. Interwał skalowania określa, jak często sprawdzać (w sekundach) określone wykorzystanie (takie jak średnie obciążenie procesora CPU) we wszystkich aktualnie wdrożonych wystąpieniach usługi.  Mechanizm jest uruchamiany, gdy monitorowana metryka spada poniżej dolnego progu lub wzrasta powyżej górnego progu.  

Mechanizm skalowania definiuje sposób wykonywania operacji skalowania po wyzwoleniu zasad.  Określ rodzaj mechanizmu (dodawanie/usuwanie repliki), minimalną i maksymalną liczbę replik (jako liczby całkowite).  Liczba replik usługi nigdy nie będzie skalowana poniżej minimalnej liczby lub powyżej maksymalnej liczby.  Należy również określić przyrost skali jako liczbę całkowitą, która jest liczbą replik, które zostaną dodane lub usunięte w operacji skalowania.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definiowanie zasad automatycznego skalowania w szablonie JSON

W poniższym przykładzie przedstawiono zasady skalowania automatycznego w szablonie wdrażania JSON.  Zasady skalowania automatycznego jest zadeklarowany we właściwości usługi do skalowania.  W tym przykładzie zdefiniowano wyzwalacz średniego obciążenia procesora CPU.  Mechanizm zostanie uruchomiony, jeśli średnie obciążenie procesora wszystkich wdrożonych wystąpień spadnie poniżej 0,2 (20%) lub przekracza 0,8 (80%).  Obciążenie procesora jest sprawdzane co 60 sekund.  Mechanizm skalowania jest zdefiniowany w celu dodania lub usunięcia wystąpień, jeśli zasada jest wyzwalana.  Wystąpienia usługi zostaną dodane lub usunięte w przyrostach jednego.  Zdefiniowano również minimalną liczbę wystąpień jednego i maksymalną liczbę wystąpień 40.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definiowanie zasad skalowania automatycznego w pliku zasobu service.yaml
W poniższym przykładzie przedstawiono zasady skalowania automatycznego w pliku zasobu usługi (YAML).  Zasady skalowania automatycznego jest zadeklarowany jako właściwość usługi do skalowania.  W tym przykładzie zdefiniowano wyzwalacz średniego obciążenia procesora CPU.  Mechanizm zostanie uruchomiony, jeśli średnie obciążenie procesora wszystkich wdrożonych wystąpień spadnie poniżej 0,2 (20%) lub przekracza 0,8 (80%).  Obciążenie procesora jest sprawdzane co 60 sekund.  Mechanizm skalowania jest zdefiniowany w celu dodania lub usunięcia wystąpień, jeśli zasada jest wyzwalana.  Wystąpienia usługi zostaną dodane lub usunięte w przyrostach jednego.  Zdefiniowano również minimalną liczbę wystąpień jednego i maksymalną liczbę wystąpień 40.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [ręcznie skalować usługę](service-fabric-mesh-tutorial-template-scale-services.md)
