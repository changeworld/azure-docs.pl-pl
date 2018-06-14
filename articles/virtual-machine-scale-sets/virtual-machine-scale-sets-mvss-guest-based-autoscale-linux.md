---
title: Azure automatycznego skalowania za pomocą metryki gościa w szablonie zestaw skalowania systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak w szablonie zestawu skalowania maszyn wirtualnych systemu Linux przy użyciu gościa metryki automatycznego skalowania
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781432"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Przy użyciu metryk gościa w szablonie Linux zestaw skalowania automatycznego skalowania

Istnieją dwa typy metryki na platformie Azure, które są zbierane z maszyn wirtualnych i skalowanie zestawów: niektóre pochodzą z hosta maszyny Wirtualnej i innych pochodzi z gościa maszyny Wirtualnej. Na wysokim poziomie Jeśli używasz standardowego Procesora, dysku i metryki sieci metryki hosta to prawdopodobnie świetnie sprawdza się. Jeśli jednak potrzebujesz większy wybór metryk, następnie metryki gościa są prawdopodobnie lepszym rozwiązaniem. Spójrzmy na różnice między nimi:

Metryka hosta jest prostszy i bardziej niezawodny. Nie wymagają dodatkowej konfiguracji, ponieważ są one zbierane przez hosta maszyny Wirtualnej, metryki gościa wymagają zainstalowania [rozszerzenie systemu Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) lub [rozszerzenia diagnostyki Azure Linux](../virtual-machines/linux/diagnostic-extension.md)na maszynie Wirtualnej gościa. Typową przyczyną metryki gościa zamiast metryki hosta jest, że metryki gościa zapewniają większy wybór metryk niż metryki hosta. Przykładem takiego jest metryki zużycie pamięci, które są dostępne za pośrednictwem metryki gościa tylko. Metryki hosta obsługiwane są wyświetlane [tutaj](../monitoring-and-diagnostics/monitoring-supported-metrics.md), i znajduje się gościa często używane metryki [tutaj](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). W tym artykule przedstawiono sposób modyfikowania [minimalnej wielkości Ustaw szablon](./virtual-machine-scale-sets-mvss-start.md) używać reguł skalowania automatycznego oparciu metryki gościa dla zestawów skalowania systemu Linux.

## <a name="change-the-template-definition"></a>Zmiany definicji szablonu

Ustaw szablon minimalnej wielkości są widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), i szablon dla wdrażania skalowania Linux zestawu z gości skalowania automatycznego widoczne [tutaj](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Przeanalizujmy różnicowego używany do tworzenia tego szablonu (`git diff minimum-viable-scale-set existing-vnet`) element przez element:

Najpierw Dodaj parametry `storageAccountName` i `storageAccountSasToken`. Diagnostyka agenta przechowuje dane w [tabeli](../cosmos-db/table-storage-how-to-use-dotnet.md) na tym koncie magazynu. Począwszy od agenta w wersji 3.0 Diagnostyka systemu Linux przy użyciu klucz dostępu do magazynu nie jest już obsługiwana. Zamiast tego należy użyć [tokenu sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Następnie zmodyfikuj zestaw skalowania `extensionProfile` do rozszerzenie diagnostyki. W tej konfiguracji należy określić identyfikator zasobu skali ustawioną zbieranie metryk, a także konta magazynu i tokenu sygnatury dostępu Współdzielonego do przechowywania metryki. Określ, jak często są agregowane metryki (w tym przypadku co minutę) i które metryk do śledzenia (w tym case, procent używanej pamięci). Aby uzyskać bardziej szczegółowe informacje dotyczące tej konfiguracji i metryki innego niż % wykorzystanie pamięci, zobacz [tej dokumentacji](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Na koniec należy dodać `autoscaleSettings` zasobów, aby skonfigurować skalowania automatycznego oparte na tych metryk. Ten zasób ma `dependsOn` klauzuli, który odwołuje się do skali ustawioną upewnij się, że zestaw skalowania istnieje przed podjęciem próby automatycznego skalowania go. Jeśli na różne metryki automatycznego skalowania, należy użyć `counterSpecifier` z konfiguracji rozszerzenia diagnostyki jako `metricName` w konfiguracji automatycznego skalowania. Aby uzyskać więcej informacji dotyczących konfiguracji automatycznego skalowania, zobacz [najlepsze rozwiązania w zakresie skalowania automatycznego](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) i [dokumentacji interfejsu API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Kolejne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
