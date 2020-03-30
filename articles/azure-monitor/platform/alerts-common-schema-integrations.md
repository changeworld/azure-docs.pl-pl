---
title: Jak zintegrować wspólny schemat alertów z aplikacjami logiki
description: Dowiedz się, jak utworzyć aplikację logiki, która wykorzystuje wspólny schemat alertów do obsługi wszystkich alertów.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668234"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Jak zintegrować wspólny schemat alertów z aplikacjami logiki

W tym artykule pokazano, jak utworzyć aplikację logiki, która wykorzystuje wspólny schemat alertów do obsługi wszystkich alertów.

## <a name="overview"></a>Omówienie

[Wspólny schemat alertu](https://aka.ms/commonAlertSchemaDocs) zapewnia znormalizowany i rozszerzalny schemat JSON we wszystkich różnych typach alertów. Wspólny schemat alertów jest najbardziej przydatne, gdy wykorzystuje się programowo — za pośrednictwem webhooks, runbooks i aplikacji logiki. W tym artykule zademonstrujemy, jak można tworzyć pojedynczą aplikację logiki do obsługi wszystkich alertów. Te same zasady mogą być stosowane do innych metod programowych. Aplikacja logiki opisana w tym artykule tworzy dobrze zdefiniowane zmienne dla [pól "istotne",](alerts-common-schema-definitions.md#essentials)a także opisuje, jak można obsługiwać logikę [określonego typu alertu.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Wymagania wstępne 

W tym artykule założono, że czytelnik jest zaznajomiony z 
* Konfigurowanie reguł alertów[(metryka,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) [dziennik,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) [dziennik aktywności)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* Konfigurowanie [grup akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Włączanie [wspólnego schematu alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) z poziomu grup akcji

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Tworzenie aplikacji logiki z wykorzystaniem wspólnego schematu alertu

1. Wykonaj [opisane kroki, aby utworzyć aplikację logiki](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Wybierz wyzwalacz: **po odebraniu żądania HTTP**.

    ![Wyzwalacze aplikacji logiki](media/action-groups-logic-app/logic-app-triggers.png "Wyzwalacze aplikacji logiki")

1.  Wybierz **edytuj,** aby zmienić wyzwalacz żądania HTTP.

    ![Wyzwalacze żądań HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Wyzwalacze żądań HTTP")


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

1. Wybierz **+** **pozycję Nowy krok,** a następnie wybierz pozycję Dodaj **akcję**.

    ![Dodawanie akcji](media/action-groups-logic-app/add-action.png "Dodawanie akcji")

1. Na tym etapie można dodać różne łączniki (Microsoft Teams, Slack, Salesforce itp.) na podstawie określonych wymagań biznesowych. Możesz użyć "podstawowych pól" out-of-the-box. 

    ![Podstawowe pola](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Podstawowe pola")
    
    Alternatywnie można tworzyć logikę warunkową na podstawie typu alertu przy użyciu opcji "Wyrażenie".

    ![Wyrażenie aplikacji logiki](media/alerts-common-schema-integrations/logic-app-expressions.png "Wyrażenie aplikacji logiki")
    
     [Pole "monitoringService"](alerts-common-schema-definitions.md#alert-context) umożliwia jednoznaczną identyfikację typu alertu, na podstawie którego można utworzyć logikę warunkową.

    
    Na przykład poniższy urywek sprawdza, czy alert jest alertem dziennika opartym na usłudze Application Insights, a jeśli tak, drukuje wyniki wyszukiwania. W przeciwnym razie drukuje "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Dowiedz się więcej o [pisaniu wyrażeń aplikacji logiki](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o grupach akcji](../../azure-monitor/platform/action-groups.md).
* [Dowiedz się więcej o wspólnym schemacie alertów](https://aka.ms/commonAlertSchemaDocs).

