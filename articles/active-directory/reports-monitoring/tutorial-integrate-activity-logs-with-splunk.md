---
title: Stream dzienniki usługi Azure Active Directory, aby Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7bc687d339a3089b8285078f7ad23c172452c5dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183445"
---
# <a name="integrate-azure-ad-logs-with-splunk-using-azure-monitor-preview"></a>Integrowanie dzienników usługi Azure AD za pomocą Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza)

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) przy użyciu Splunk przy użyciu usługi Azure Monitor. Skierować dzienniki do usługi Azure event hub, a następnie zintegrować Centrum zdarzeń za pomocą Splunk.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Dzienniki Centrum zdarzeń platformy Azure, który zawiera działania usługi Azure AD. Dowiedz się, jak [przesyłanie strumieniowe dzienników aktywności do Centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Dodatek usługi Azure Monitor dla Splunk. [Pobierz i skonfiguruj wystąpienie usługi Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Samouczek 

1. Otwórz swoje wystąpienie Splunk, a następnie wybierz pozycję **danych — Podsumowanie**.

    ![Przycisk "Podsumowanie danych"](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Wybierz **Sourcetypes** , a następnie wybierz pozycję **amal: aadal:audit**

    ![Na karcie Sourcetypes podsumowanie danych](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Działanie usługi Azure AD, dzienniki są wyświetlane na poniższej ilustracji:

    ![Dzienniki aktywności](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Jeśli nie możesz zainstalować dodatek w wystąpieniu usługi Splunk (na przykład, jeśli jest używany serwer proxy lub działające w chmurze Splunk), możesz przekazywać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk. Aby to zrobić, użyj tego [funkcji platformy Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), który jest wyzwalany przez nowych komunikatów w Centrum zdarzeń. 
>

## <a name="next-steps"></a>Kolejne kroki

* [Interpret audit logs schema in Azure Monitor (Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor)](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
