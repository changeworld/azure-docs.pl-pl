---
title: Używanie skalowania automatycznego platformy Azure z metrykami gościa w szablonie zestawu skalowania systemu Linux
description: Dowiedz się, jak automatycznie skalować przy użyciu danych gościa w szablonie zestawu skalowania maszyny wirtualnej systemu Linux
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271949"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Skalowanie automatyczne przy użyciu danych gościa w szablonie zestawu skalowania systemu Linux

Istnieją dwa szerokie typy metryk na platformie Azure, które są zbierane z maszyn wirtualnych i zestawów skalowania: metryki hosta i metryki gościa. Na wysokim poziomie, jeśli chcesz użyć standardowych metryk procesora CPU, dysku i sieci, metryki hosta są dobrym rozwiązaniem. Jeśli jednak potrzebujesz większego wyboru danych, należy przeanalizować dane gościa.

Metryki hosta nie wymagają dodatkowej konfiguracji, ponieważ są zbierane przez maszynę wirtualną hosta, podczas gdy metryki gościa wymagają [zainstalowania rozszerzenia diagnostyki systemu Windows Azure](../virtual-machines/windows/extensions-diagnostics-template.md) lub [rozszerzenia diagnostyki platformy Azure systemu Linux](../virtual-machines/linux/diagnostic-extension.md) na maszynie wirtualnej gościa. Częstym powodem używania metryk gościa zamiast metryk hosta jest to, że metryki gościa zapewniają większy wybór metryk niż metryki hosta. Jednym z takich przykładów są metryki zużycia pamięci, które są dostępne tylko za pośrednictwem metryk gościa. Obsługiwane dane hosta są wymienione [w tym miejscu,](../azure-monitor/platform/metrics-supported.md)a często używane dane gościa są wymienione [tutaj](../azure-monitor/platform/autoscale-common-metrics.md). W tym artykule pokazano, jak zmodyfikować [podstawowy szablon zestawu skali realnej,](virtual-machine-scale-sets-mvss-start.md) aby używać reguł skalowania automatycznego na podstawie metryk gości dla zestawów skalowania systemu Linux.

## <a name="change-the-template-definition"></a>Zmienianie definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) utworzyliśmy podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujemy go, aby utworzyć szablon, który wdraża zestaw skalowania systemu Linux z automatyczną skalą opartą na metrykach gościa.

Najpierw dodaj parametry `storageAccountName` `storageAccountSasToken`dla i . Agent diagnostyki przechowuje dane metryki w [tabeli](../cosmos-db/table-storage-how-to-use-dotnet.md) na tym koncie magazynu. Od programu Linux Diagnostics Agent w wersji 3.0 przy użyciu klucza dostępu do magazynu nie jest już obsługiwana. Zamiast tego należy użyć [tokenu sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Następnie zmodyfikuj zestaw skalowania, `extensionProfile` aby uwzględnić rozszerzenie diagnostyki. W tej konfiguracji określ identyfikator zasobu zestawu skalowania do zbierania metryk, a także konto magazynu i token sygnatury dostępu Współdzielonego do przechowywania metryk. Określ, jak często metryki są agregowane (w tym przypadku co minutę) i które metryki do śledzenia (w tym przypadku procent używanej pamięci). Aby uzyskać bardziej szczegółowe informacje na temat tej konfiguracji i metryk innych niż procent używanej pamięci, zobacz [tę dokumentację](../virtual-machines/linux/diagnostic-extension.md).

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

Na koniec dodaj `autoscaleSettings` zasób, aby skonfigurować skalowanie automatyczne na podstawie tych metryk. Ten zasób ma klauzulę, `dependsOn` która odwołuje się do zestawu skalowania, aby upewnić się, że zestaw skalowania istnieje przed próbą automatycznego skalowania. Jeśli wybierzesz inną metrykę do skalowania `counterSpecifier` automatycznego, należy użyć `metricName` z konfiguracji rozszerzenia diagnostyki jako w konfiguracji skalowania automatycznego. Aby uzyskać więcej informacji na temat konfiguracji skalowania automatycznego, zobacz [najważniejsze wskazówki dotyczące skalowania automatycznego i](../azure-monitor/platform/autoscale-best-practices.md) [dokumentację referencyjną interfejsu API REST usługi Azure Monitor](/rest/api/monitor/autoscalesettings).

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





## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
