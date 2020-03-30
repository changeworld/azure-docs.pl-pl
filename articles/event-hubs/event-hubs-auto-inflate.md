---
title: Automatyczne skalowanie jednostek przepływności — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Włącz automatyczne zawyżanie w obszarze nazw, aby automatycznie skalować jednostki przepływności w górę.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264472"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatyczne skalowanie jednostek przepływności usługi Azure Event Hubs
Usługa Azure Event Hubs to wysoce skalowalna platforma przesyłania strumieniowego danych. W związku z tym użycie usługi Event Hubs często zwiększa się po rozpoczęciu korzystania z usługi. Takie użycie wymaga zwiększenia wstępnie [zdefiniowanych jednostek przepływności](event-hubs-scalability.md#throughput-units) w celu skalowania centrów zdarzeń i obsługi większych szybkości transferu. Funkcja **Automatycznego zawyżania** centrów zdarzeń jest automatycznie skalowana w górę, zwiększając liczbę jednostek przepływności, aby spełnić potrzeby użytkowania. Zwiększenie jednostek przepływności zapobiega scenariuszom ograniczania przepustowości, w których:

* Szybkość transferu danych przychodzących przekracza jednostki przepływności.
* Szybkość żądania transferu danych wychodzących przekracza ustawione jednostki przepływności.

Usługa Usługi Usługi Event Hubs zwiększa przepływność, gdy obciążenie zwiększa się powyżej minimalnego progu, bez żadnych żądań w przypadku błędów ServerBusy.

## <a name="how-auto-inflate-works"></a>Jak działa auto-nadmuchiwane

Ruch w centrach zdarzeń jest kontrolowany przez [jednostki przepływności.](event-hubs-scalability.md#throughput-units) Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę transferu ruchu przychodzącego i dwa razy tyle ruchu wychodzącego. Standardowe koncentratory zdarzeń można skonfigurować za pomocą jednostek przepływności 1-20. Automatyczne nadmuchiwane umożliwia rozpoczęcie od małych z minimalnymi wymaganymi jednostkami przepływności, które wybierzesz. Następnie funkcja jest skalowana automatycznie do maksymalnego limitu potrzebnych jednostek przepływności, w zależności od wzrostu ruchu. Automatyczne nadmuchiwane zapewnia następujące korzyści:

- Wydajny mechanizm skalowania, aby rozpocząć małe i skalowane w miarę rozwoju.
- Automatyczne skalowanie do określonego górnego limitu bez ograniczania przepustowości.
- Większa kontrola nad skalowaniem, ponieważ można kontrolować, kiedy i ile skalować.

## <a name="enable-auto-inflate-on-a-namespace"></a>Włączanie automatycznego zawyżania w obszarze nazw

Funkcję Auto-inflate można włączyć lub wyłączyć w obszarze nazw Centra zdarzeń warstwy standardowej przy użyciu jednej z następujących metod:

- [Portal Azure](https://portal.azure.com).
- [Szablon usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Podstawowe obszary nazw centrów zdarzeń warstwy nie obsługują automatycznego nadmuchiwanie.

### <a name="enable-auto-inflate-through-the-portal"></a>Włącz automatyczne nadmuchiwane za pośrednictwem portalu


#### <a name="enable-at-the-time-of-creation"></a>Włącz w momencie tworzenia 
Funkcję Auto-zawyżania można **włączyć podczas tworzenia obszaru nazw Centrum zdarzeń:**
 
![Włącz automatyczne nadmuchiwane w czasie tworzenia centrum zdarzeń](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Po włączeniu tej opcji można rozpocząć małe z jednostek przepływności i skalowania w górę w miarę wzrostu potrzeb użycia. Górna granica inflacji nie wpływa natychmiast na ceny, która zależy od liczby jednostek przepływności używanych na godzinę.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Włączanie automatycznego zawyżania istniejącego centrum zdarzeń
Funkcję Auto-inflate można również włączyć i zmodyfikować jej ustawienia, korzystając z następujących instrukcji: 
 
1. Na stronie **Obszar nazw centrów zdarzeń** wybierz pozycję **Wyłączone** w obszarze **Automatyczne nadmuchiwanie jednostek przepływności**.  

    ![Wybieranie jednostek przepływności na stronie obszar nazw Centrów zdarzeń](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na stronie **Ustawienia skalowania** zaznacz pole wyboru **Włącz** (jeśli funkcja skalowania automatycznego nie została włączona).

    ![Wybierz pozycję Włącz](./media/event-hubs-auto-inflate/scale-settings.png)
3. Wprowadź **maksymalną** liczbę jednostek przepływności lub użyj paska przewijania, aby ustawić wartość. 
4. (opcjonalnie) Zaktualizuj **minimalną** liczbę jednostek przepływności u góry tej strony. 


> [!NOTE]
> Po zastosowaniu konfiguracji automatycznego zawyżania w celu zwiększenia jednostek przepływności usługa Usługi Event Hubs emituje dzienniki diagnostyczne, które zapewniają informacje o tym, dlaczego i kiedy zwiększona przepływność. Aby włączyć rejestrowanie diagnostyczne dla centrum zdarzeń, wybierz **ustawienia diagnostyczne** w menu po lewej stronie na stronie Centrum zdarzeń w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dzienników diagnostycznych dla Centrum zdarzeń platformy Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Włączanie automatycznego zawyżania za pomocą szablonu usługi Azure Resource Manager

Można włączyć automatyczne zawyżanie podczas wdrażania szablonu usługi Azure Resource Manager. Na przykład ustaw `isAutoInflateEnabled` właściwość na `maximumThroughputUnits` **true** i ustaw wartość 10. Przykład:

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

Aby uzyskać pełny szablon, zobacz [obszar nazw Tworzenie centrów zdarzeń i włącz zawyżanie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) szablonu w usłudze GitHub.


## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)

