---
title: Jak zintegrować wspólny schemat alertów z Logic Apps
description: Dowiedz się, jak utworzyć aplikację logiki, która wykorzystuje wspólny schemat alertów do obsługi wszystkich alertów.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 569b97ecc24306741c3323ce5bc526f88645c1dd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702949"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Jak zintegrować wspólny schemat alertów z Logic Apps

W tym artykule pokazano, jak utworzyć aplikację logiki, która wykorzystuje wspólny schemat alertów do obsługi wszystkich alertów.

## <a name="overview"></a>Przegląd

[Typowy schemat alertów](https://aka.ms/commonAlertSchemaDocs) zawiera znormalizowany i rozszerzalny schemat JSON dla wszystkich różnych typów alertów. Typowy schemat alertów jest najbardziej przydatny, gdy jest używany programowo — za pomocą elementów webhook, Runbook i Logic Apps. W tym artykule pokazano, jak można utworzyć pojedynczą aplikację logiki do obsługi wszystkich alertów. Te same zasady można stosować do innych metod programistycznych. Aplikacja logiki opisana w tym artykule tworzy dobrze zdefiniowane zmienne dla [pól "Essential"](alerts-common-schema-definitions.md#essentials), a także opisuje, jak można obsłużyć logikę specyficzną dla [typu alertu](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Wymagania wstępne 

W tym artykule przyjęto założenie, że czytelnik zna 
* Konfigurowanie reguł alertów ([Metryka](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [Dziennik](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [Dziennik aktywności](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Konfigurowanie [grup akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Włączanie [wspólnego schematu alertu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) z poziomu grup akcji

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Tworzenie aplikacji logiki wykorzystującej wspólny schemat alertów

1. Postępuj zgodnie z [instrukcjami, aby utworzyć aplikację logiki](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Wybierz wyzwalacz: **po odebraniu żądania HTTP**.

    Wyzwalacze(media/action-groups-logic-app/logic-app-triggers.png "aplikacji logiki") ![aplikacji]logiki

1.  Wybierz pozycję **Edytuj** , aby zmienić wyzwalacz żądania HTTP.

    Wyzwalacze(media/action-groups-logic-app/http-request-trigger-shape.png "żądań HTTP") ![wyzwalają żądania]http


1.  Skopiuj i wklej następujący schemat:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Wybierz pozycję **+** **nowy krok** , a następnie wybierz pozycję **Dodaj akcję**.

    ![Dodawanie akcji](media/action-groups-logic-app/add-action.png "Dodaj akcję")

1. Na tym etapie można dodać różne łączniki (Microsoft Teams, zapasowe, Salesforce itp.) na podstawie określonych wymagań firmy. Możesz użyć nieaktualnego pola "Essential Fields". 

    Podstawowe pola(media/alerts-common-schema-integrations/logic-app-essential-fields.png "istotnych") ![pól]
    
    Alternatywnie można utworzyć logikę warunkową opartą na typie alertu przy użyciu opcji "Expression".

    (media/alerts-common-schema-integrations/logic-app-expressions.png "Wyrażenie aplikacji") logiki ![wyrażeń aplikacji logiki]
    
     [Pole "monitoringService"](alerts-common-schema-definitions.md#alert-context) umożliwia jednoznaczną identyfikację typu alertu, na podstawie którego można utworzyć logikę warunkową.

    
    Na przykład poniższy fragment kodu sprawdza, czy alert jest oparty na Application Insights alertu dziennika, a następnie drukuje wyniki wyszukiwania. W przeciwnym razie drukuje "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Dowiedz się więcej na temat [pisania wyrażeń aplikacji logiki](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat grup akcji](../../azure-monitor/platform/action-groups.md).
* [Dowiedz się więcej o typowym schemacie alertów](https://aka.ms/commonAlertSchemaDocs).

