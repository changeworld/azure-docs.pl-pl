---
title: Automatyczne skalowanie w górę jednostek przepływności — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Włączyć automatyczne rozszerzanie przestrzeni nazw, aby automatycznie skalować w górę jednostek przepływności.
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
ms.openlocfilehash: d705993c7cd3816e89da21625dc5b003435b9128
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822745"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatyczne skalowanie w górę jednostek przepływności usługi Azure Event Hubs
Azure Event Hubs to wysoce skalowana platforma do strumieniowego przesyłania danych. W efekcie użycia usługi Event Hubs często zwiększa się po rozpoczęciu korzystania z usługi. Wymaga tych danych użycia, zwiększając wstępnie [jednostek przepływności](event-hubs-features.md#throughput-units) skalowania usługi Event Hubs i obsługiwać większe szybkości transferu. **Automatyczne rozszerzanie** funkcji usługi Event Hubs automatycznie jest skalowany w górę, zwiększając liczbę jednostek przepływności, aby zaspokoić potrzeby użycia wymagań. Zwiększenie jednostek przepływności zapobiega scenariuszy, w którym ograniczania przepływności:

* Stawki transferu danych przychodzących danych przekracza zestaw jednostek przepływności.
* Liczby żądań danych wychodzących przekracza zestaw jednostek przepływności.

Usługa Event Hubs zwiększa przepływność, gdy rośnie obciążenie operacjami po osiągnięciu progu minimalne, bez żadnych żądań kończy się niepowodzeniem z błędami ServerBusy.

## <a name="how-auto-inflate-works"></a>Jak działa automatyczne rozszerzanie

Ruch do centrów zdarzeń jest kontrolowane przez [jednostek przepływności](event-hubs-features.md#throughput-units). Pojedyncza jednostka przepływności umożliwia 1 MB na sekundę transferu danych przychodzących i dwa razy ilość danych wychodzących. Centra zdarzeń w wersji Standard można skonfigurować z 1-20 jednostek przepływności. Automatyczne rozszerzanie umożliwia zacznij od czegoś małego z jednostkami minimalna przepustowość wymagana, które wybierzesz. Funkcja następnie skaluje się automatycznie maksymalnego limitu jednostek przepływności, których potrzebujesz, w zależności od zwiększenia ilości ruchu. Automatyczne rozszerzanie zapewnia następujące korzyści:

- Wydajny mechanizm skalowania na początku są małe, i skalowanie w górę proporcjonalna do rozwoju środowiska.
- Automatycznie Skaluj do określonej górnego limitu bez ograniczania problemów.
- Większa kontrola nad skalowaniem, dlatego użytkownik decyduje o ile do skali.

## <a name="enable-auto-inflate-on-a-namespace"></a>Włącz automatyczne rozszerzanie w przestrzeni nazw

Można włączyć lub wyłączyć automatyczne rozszerzanie w przestrzeni nazw usługi Event Hubs przy użyciu jednej z następujących metod:

- [Witryny Azure portal](https://portal.azure.com).
- [Szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Włącz automatyczne rozszerzanie za pośrednictwem portalu


#### <a name="enable-at-the-time-of-creation"></a>Włącz w czasie tworzenia 
Można włączyć automatyczne rozszerzanie funkcji **podczas tworzenia przestrzeni nazw usługi Event Hubs**:
 
![Włącz automatyczne rozszerzanie podczas tworzenia Centrum zdarzeń czasu](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Po włączeniu tej opcji możesz zacząć od małej przy użyciu jednostek przepływności i skalowanie w górę użycie wymaga zwiększenia. Górny limit inflacji natychmiast wpływa na ceny, która jest zależna od liczby jednostek przepływności na godzinę.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Włączyć automatyczne rozszerzanie na istniejącym Centrum zdarzeń
Można także włączyć automatyczne rozszerzanie funkcji i zmodyfikować jego ustawienia, korzystając z poniższych instrukcji: 
 
1. Na **Event Hubs Namespace** wybierz opcję **wyłączone** w obszarze **automatyczne rozszerzanie jednostek przepływności**.  

    ![Wybierz jednostki przepływności na stronie przestrzeń nazw usługi Event Hubs](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. W **Ustawienia skalowania** strony, zaznacz pole wyboru **Włącz** (Jeśli nie została włączona funkcja automatycznego skalowania).

    ![Wybierz pozycję Włącz](./media/event-hubs-auto-inflate/scale-settings.png)
3. Wprowadź **maksymalna** liczby jednostek przepływności lub użyj paska przewijania, można ustawić wartości. 
4. (opcjonalnie) Aktualizacja **minimalne** liczby jednostek przepływności w górnej części tej strony. 


> [!NOTE]
> Po zastosowaniu automatyczne rozszerzanie konfiguracji zwiększenie jednostek przepływności, usługa Event Hubs emituje dzienniki diagnostyczne, które pozwalają dowiedzieć się, kiedy i dlaczego zwiększyć przepływność. Aby włączyć rejestrowanie diagnostyczne dla Centrum zdarzeń, wybierz **ustawień diagnostycznych** w menu po lewej stronie, na stronie Centrum zdarzeń w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [skonfigurować dzienniki diagnostyczne na potrzeby usługi Azure event hub](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Włącz automatyczne rozszerzanie przy użyciu szablonu usługi Azure Resource Manager

Podczas wdrażania szablonu usługi Azure Resource Manager, można włączyć automatyczne rozszerzanie. Na przykład ustawić `isAutoInflateEnabled` właściwości **true** i ustaw `maximumThroughputUnits` do 10. Na przykład:

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

Aby uzyskać kompletny szablon, zobacz [przestrzeni nazw usługi Event Hubs Utwórz i Włącz rozszerzanie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) szablon w witrynie GitHub.


## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)

