---
title: Użyj skalowania automatycznego platformy Azure za pomocą metryk gościa w szablonie zestawu skalowania systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalowanie automatyczne za pomocą metryki gościa w szablonie zestawu skalowania maszyn wirtualnych systemu Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868989"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Skalowanie automatyczne za pomocą metryki gościa w szablonie zestawu skalowania systemu Linux

Istnieją dwa ogólne typy metryki na platformie Azure, które są zbierane z maszyn wirtualnych i zestawów skalowania: Metryki hosta, a metryki gościa. Na wysokim poziomie Jeśli chcesz użyć standardowego procesora CPU, dysku i metryki sieci metryki hosta to dobrym rozwiązaniem. Jeśli jednak potrzebny większy wybór metryk, następnie metryki gościa powinny zostać zbadane.

Metryki hosta nie wymagają dodatkowej konfiguracji, ponieważ są one zbierane przez hosta maszyny Wirtualnej, metryk gościa wymagają zainstalowania [rozszerzenia diagnostyki Azure Windows](../virtual-machines/windows/extensions-diagnostics-template.md) lub [rozszerzenie diagnostyki platformy Azure w systemie Linux ](../virtual-machines/linux/diagnostic-extension.md) w maszynie Wirtualnej gościa. Jednym z typowych powodów metryki gościa zamiast metryki hosta jest, że metryki gościa zapewniają większy wybór metryki niż metryki hosta. Jednym z przykładów jest użycie pamięci metryk, które są dostępne za pośrednictwem metryki gościa tylko. Metryki hosta obsługiwane są wymienione [tutaj](../azure-monitor/platform/metrics-supported.md), a metryki gościa często używane są wymienione [tutaj](../azure-monitor/platform/autoscale-common-metrics.md). W tym artykule przedstawiono sposób modyfikowania [szablon zestawu skalowania możliwego do użycia podstawowego](virtual-machine-scale-sets-mvss-start.md) używać reguł skalowania automatycznego w oparciu o metryki gościa dla zestawów skalowania systemu Linux.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) miał utworzyliśmy szablonu zestawu skalowania podstawowe. Teraz możemy użyć szablonu, którego wcześniej i zmodyfikuj go, aby utworzyć szablon, który wdraża zestaw przy użyciu skalowania automatycznego na podstawie metryki gościa skalowania systemu Linux.

Najpierw należy dodać parametry dla `storageAccountName` i `storageAccountSasToken`. Agent diagnostyki przechowuje dane metryk w [tabeli](../cosmos-db/table-storage-how-to-use-dotnet.md) na tym koncie magazynu. Począwszy od agenta diagnostyki systemu Linux w wersji 3.0 przy użyciu klucza dostępu do magazynu nie jest już obsługiwana. Zamiast tego należy użyć [tokenu sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Następnie należy zmodyfikować zestawu skalowania `extensionProfile` obejmujący rozszerzenie diagnostyki. W tej konfiguracji należy określić identyfikator zasobu zestawie skalowania, aby zbierać metryki, oraz konto magazynu i tokenu sygnatury dostępu Współdzielonego na potrzeby przechowywania metryki. Określ, jak często metryki są agregowane (w tym przypadku na minutę) i które metryki umożliwiające śledzenie (w tym przypadków, procent używanej pamięci). Aby bardziej szczegółowe informacje dotyczące tej konfiguracji i metryki innego niż % użycie pamięci, zobacz [tej dokumentacji](../virtual-machines/linux/diagnostic-extension.md).

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

Na koniec należy dodać `autoscaleSettings` zasobów, aby skonfigurować automatyczne skalowanie na podstawie tych metryk. Ten zasób ma `dependsOn` klauzula, która odwołuje się do skalowania równa upewnij się, że zestaw skalowania istnieje przed przystąpieniem do skalowania automatycznego. Jeśli wybierzesz inne metryki automatycznego skalowania na, należy użyć `counterSpecifier` z konfiguracji rozszerzenia diagnostyki jako `metricName` w konfiguracji skalowania automatycznego. Aby uzyskać więcej informacji na temat konfiguracji skalowania automatycznego, zobacz [najlepszych praktykach dotyczących skalowania](../azure-monitor/platform/autoscale-best-practices.md) i [dokumentację referencyjną interfejsu API REST usługi Azure Monitor](/rest/api/monitor/autoscalesettings).

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
