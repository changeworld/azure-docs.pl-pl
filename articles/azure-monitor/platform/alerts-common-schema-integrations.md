---
title: Jak zintegrować wspólny schemat alertów za pomocą aplikacji logiki
description: Dowiedz się, jak utworzyć aplikację logiki, która korzysta z wspólnego schematu alertu do obsługi wszystkich alertów.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734887"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Jak zintegrować wspólny schemat alertów za pomocą aplikacji logiki

W tym artykule pokazano, jak utworzyć aplikację logiki, która korzysta z wspólnego schematu alertu do obsługi wszystkich alertów.

## <a name="overview"></a>Omówienie

[Wspólny schemat alertu](https://aka.ms/commonAlertSchemaDocs) zawiera standardowe i rozszerzalny schemat JSON dla wszystkich różnych typów alertów. Wspólny schemat alertu jest najbardziej przydatne, gdy wykorzystywane programowo — za pośrednictwem elementów webhook, elementów runbook i logic apps. W tym artykule pokażemy, jak można tworzyć aplikację logiki pojedynczego do obsługi wszystkich alertów. Te same zasady mogą dotyczyć innych metod programistycznych. Aplikację logiki, opisane w tym artykule tworzy dobrze zdefiniowane zmienne dla [pola "podstawowe"](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields), a także opisuje, jak można obsługiwać [typu alertu](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) logikę specyficzną dla.


## <a name="prerequisites"></a>Wymagania wstępne 

W tym artykule założono, że czytelnik jest zapoznać się z 
* Konfigurowanie reguły alertów ([metryki](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [dziennika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [dziennika aktywności](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Konfigurowanie [grupy akcji](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Włączanie [wspólny schemat alertu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) z w obrębie grupy akcji

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Tworzenie aplikacji logiki, wykorzystując wspólny schemat alertu

1. Postępuj zgodnie z [instrukcji opisanych w do tworzenia aplikacji logiki](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Wybierz wyzwalacz: **Po odebraniu żądania HTTP**.

    ![Wyzwalacze aplikacji logiki](media/action-groups-logic-app/logic-app-triggers.png "wyzwalacze aplikacji logiki")

1.  Wybierz **Edytuj** zmienić wyzwalacza żądania HTTP.

    ![Wyzwalaniem na żądanie HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "wyzwalaniem na żądanie HTTP")


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

1. Wybierz **+** **nowy krok** , a następnie wybierz **Dodaj akcję**.

    ![Dodaj akcję](media/action-groups-logic-app/add-action.png "Dodaj akcję")

1. Na tym etapie można dodać wiele łączników (Microsoft Teams, Slack, Salesforce, itp.), oparte na konkretnych potrzeb biznesowych. Można użyć "pól podstawowych" out-of--box. 

    ![Pól podstawowych](media/alerts-common-schema-integrations/logic-app-essential-fields.png "pól podstawowych")
    
    Alternatywnie można tworzyć logikę warunkową w oparciu o typ alertu przy użyciu opcji "Expression".

    ![Wyrażenie aplikacji logiki](media/alerts-common-schema-integrations/logic-app-expressions.png "wyrażenie aplikacji logiki")
    
     [Pola "monitoringService"](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) służy do jednoznacznego identyfikowania typu alertu, oparte na którym możesz tworzyć logikę warunkową.

    
    Na przykład poniżej fragment kodu sprawdza, czy alert usługi Application Insights na podstawie alertu dziennika, a jeśli tak drukuje wyniki wyszukiwania. W przeciwnym wypadku, wydrukowaniu "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Dowiedz się więcej o [wyrażeń aplikacji logiki](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o grupach akcji](../../azure-monitor/platform/action-groups.md).
* [Dowiedz się więcej o wspólnym schemacie alertu](https://aka.ms/commonAlertSchemaDocs).

