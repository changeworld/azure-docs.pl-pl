---
title: Automatyczne skalowanie w górę jednostek przepływności usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Włącz zwiększyć automatycznie w przestrzeni nazw, aby automatycznie skalować jednostki przepływności
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 20ee0e6cff2a07cbd62a79799eada5708c7a0f07
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28018613"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatyczne skalowanie w górę jednostek przepływności usługi Azure Event Hubs

Usługa Azure Event Hubs to wysoce skalowalna danych przesyłania strumieniowego platformy. Tak użycie usługi Event Hubs często zwiększa się po uruchomieniu do korzystania z usługi. Takie wymaga zwiększenia jednostki przepływności ustalonej skalować centra zdarzeń i obsługi większych szybkości transferu. *Zwiększyć automatycznie* funkcji usługi Event hubs jest automatycznie skalowany liczby jednostek przepływności, aby spełnić potrzeby użycia. Zwiększenie jednostek przepływności uniemożliwia ograniczania scenariuszy, w którym:

* Szybkości transferu danych przekracza zestaw jednostek przepływności.
* Szybkości żądania wyjście danych przekracza zestaw jednostek przepływności.

## <a name="how-auto-inflate-works"></a>Jak zwiększyć automatycznie działa

Ruch centra zdarzeń jest kontrolowana przez jednostki przepływności. Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę przychodzące i dwa razy ilość wyjście. Centra zdarzeń w wersji Standard można skonfigurować za pomocą 1-20 jednostek przepływności. Podniesienie automatycznie pozwala na rozpoczęcie od czegoś małego z jednostkami minimalna wymagana przepustowość. Funkcja następnie może obsłużyć automatycznie limit maksymalnej liczby jednostek przepływności, które są potrzebne, w zależności od wzrost ruchu. Podniesienie automatycznie zapewnia następujące korzyści:

- Wydajny mechanizm skalowania rozpoczęcie od czegoś małego i skalowanie w górę można powiększać.
- Automatycznie skalować górny limit określony bez ograniczania problemy.
- Większa kontrola nad skalowania, możesz kontrolować, kiedy i ile do skalowania.

## <a name="enable-auto-inflate-on-a-namespace"></a>Włącz zwiększyć automatycznie w przestrzeni nazw

Można włączyć lub wyłączyć zwiększyć automatycznie w przestrzeni nazw usługi Event Hubs przy użyciu jednej z następujących metod:

1. [Portalu Azure](https://portal.azure.com).
2. Szablon usługi Azure Resource Manager.

### <a name="enable-auto-inflate-through-the-portal"></a>Włącz zwiększyć automatycznie za pośrednictwem portalu

Można włączyć funkcję zwiększyć automatycznie, podczas tworzenia usługi Event Hubs przestrzeni nazw:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Po włączeniu tej opcji możesz uruchomić małych na jednostek przepływności i skalować w miarę wzrostu użycie. Górny limit inflacji nie bezpośrednio wpływa na cennika, która jest zależna od liczby jednostek przepływności na godzinę.

Można również włączyć funkcję automatycznego zwiększyć za pomocą **skali** opcji w okienku ustawień w portalu:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Włącz automatyczne-zwiększyć przy użyciu szablonu usługi Azure Resource Manager

Podniesienie automatycznej można włączyć podczas wdrażania szablonu usługi Azure Resource Manager. Na przykład ustawić `isAutoInflateEnabled` właściwości **true** i ustaw `maximumThroughputUnits` do 10.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Zakończenie szablonu, zobacz [utworzyć centra zdarzeń w przestrzeni nazw i Włącz zwiększyć](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) szablonu w witrynie GitHub.

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)

