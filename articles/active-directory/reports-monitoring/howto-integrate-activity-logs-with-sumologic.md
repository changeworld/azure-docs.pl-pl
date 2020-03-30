---
title: Dzienniki strumienia do sumologic przy użyciu usługi Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z sumologic przy użyciu usługi Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014385"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integruj dzienniki usługi Azure Active Directory z sumologic przy użyciu usługi Azure Monitor

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) z sumologic przy użyciu usługi Azure Monitor. Najpierw rozsyłasz dzienniki do centrum zdarzeń platformy Azure, a następnie integrujesz centrum zdarzeń z SumoLogic.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Centrum zdarzeń platformy Azure zawierające dzienniki aktywności usługi Azure AD. Dowiedz się, jak [przesyłać strumieniowo dzienniki aktywności do centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Subskrypcja z włączoną rejestracją jednokrotną SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Kroki integracji dzienników usługi Azure AD z sumologic 

1. Najpierw [przesyłaj strumieniowo dzienniki usługi Azure AD do centrum zdarzeń platformy Azure.](quickstart-azure-monitor-stream-logs-to-event-hub.md)
2. Skonfiguruj wystąpienie SumoLogic do [zbierania dzienników dla usługi Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Zainstaluj aplikację SumoLogic usługi Azure AD,](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) aby korzystać ze wstępnie skonfigurowanych pulpitów nawigacyjnych, które zapewniają analizę środowiska w czasie rzeczywistym.

   ![Pulpit nawigacyjny](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Następne kroki

* [Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)