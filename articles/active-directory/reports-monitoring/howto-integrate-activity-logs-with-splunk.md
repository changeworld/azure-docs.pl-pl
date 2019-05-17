---
title: Integrowanie dzienników usługi Azure Active Directory za pomocą Splunk przy użyciu usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą SumoLogic przy użyciu usługi Azure Monitor
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
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f30e19a351f7b25f995a85cfd566bcba091ac27
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597824"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Instrukcje: Integrowanie dzienników usługi Azure Active Directory za pomocą Splunk przy użyciu usługi Azure Monitor

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) przy użyciu Splunk przy użyciu usługi Azure Monitor. Skierować dzienniki do usługi Azure event hub, a następnie zintegrować Centrum zdarzeń za pomocą Splunk.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Dzienniki Centrum zdarzeń platformy Azure, który zawiera działania usługi Azure AD. Dowiedz się, jak [przesyłanie strumieniowe dzienników aktywności do Centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Dodatek usługi Azure Monitor dla Splunk. [Pobierz i skonfiguruj wystąpienie usługi Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrowanie dzienników usługi Azure Active Directory 

1. Otwórz swoje wystąpienie Splunk, a następnie wybierz pozycję **danych — Podsumowanie**.

    ![Przycisk "Podsumowanie danych"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Wybierz **Sourcetypes** , a następnie wybierz pozycję **amal: aadal:audit**

    ![Na karcie Sourcetypes podsumowanie danych](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Działanie usługi Azure AD, dzienniki są wyświetlane na poniższej ilustracji:

    ![Dzienniki aktywności](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Jeśli nie możesz zainstalować dodatek w wystąpieniu usługi Splunk (na przykład, jeśli jest używany serwer proxy lub działające w chmurze Splunk), możesz przekazywać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk. Aby to zrobić, użyj tego [funkcji platformy Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), który jest wyzwalany przez nowych komunikatów w Centrum zdarzeń. 
>

## <a name="next-steps"></a>Kolejne kroki

* [Interpret audit logs schema in Azure Monitor (Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor)](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)