---
title: Integracja splunku za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
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
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968708"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Jak: integrowanie dzienników usługi Azure Active Directory ze splunkiem przy użyciu usługi Azure Monitor

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) ze splunkiem przy użyciu usługi Azure Monitor. Najpierw rozsyłasz dzienniki do centrum zdarzeń platformy Azure, a następnie integrujesz centrum zdarzeń ze splunkiem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:

- Centrum zdarzeń platformy Azure zawierające dzienniki aktywności usługi Azure AD. Dowiedz się, jak [przesyłać strumieniowo dzienniki aktywności do centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 

-  [Microsoft Azure Add on for Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Integrowanie dzienników usługi Azure Active Directory 

1. Otwórz wystąpienie Splunk i wybierz pozycję **Podsumowanie danych**.

    ![Przycisk "Podsumowanie danych"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Wybierz kartę **Typy źródeł,** a następnie wybierz **pozycję amal: aadal:audit**

    ![Karta Źródła podsumowania danych](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Dzienniki aktywności usługi Azure AD są wyświetlane na poniższym rysunku:

    ![Dzienniki aktywności](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Jeśli nie można zainstalować dodatku w wystąpieniu Splunk (na przykład, jeśli używasz serwera proxy lub działa w Splunk Cloud), możesz przesłać te zdarzenia dalej do modułu zbierającego zdarzenia HTTP Splunk. Aby to zrobić, należy użyć tej [funkcji platformy Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), która jest wyzwalana przez nowe komunikaty w centrum zdarzeń. 
>

## <a name="next-steps"></a>Następne kroki

* [Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)