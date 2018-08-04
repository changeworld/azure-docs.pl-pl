---
title: Jak zintegrować dzienniki usługi Azure Active Directory z usługą Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503846"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Integrowanie dzienników usługi Azure AD za pomocą Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza)

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) przy użyciu Splunk przy użyciu usługi Azure Monitor. Skierować dzienniki do usługi Azure event hub, a następnie zintegrować Centrum zdarzeń za pomocą Splunk.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Dzienniki Centrum zdarzeń platformy Azure, który zawiera działania usługi Azure AD. Dowiedz się, jak [przesyłanie strumieniowe dzienników aktywności do Centrum zdarzeń](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
* Dodatek usługi Azure Monitor dla Splunk. [Pobierz i skonfiguruj wystąpienie usługi Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Samouczek 

1. Otwórz swoje wystąpienie Splunk, a następnie wybierz pozycję **danych — Podsumowanie**.

    ![Przycisk "Podsumowanie danych"](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. Wybierz **Sourcetypes** , a następnie wybierz pozycję **amal: aadal:audit**

    ![Na karcie Sourcetypes podsumowanie danych](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    Działanie usługi Azure AD, dzienniki są wyświetlane na poniższej ilustracji:

    ![Dzienniki aktywności](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> Jeśli nie możesz zainstalować dodatek w wystąpieniu usługi Splunk (na przykład, jeśli jest używany serwer proxy lub działające w chmurze Splunk), możesz przekazywać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk. Aby to zrobić, użyj tego [funkcji platformy Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), który jest wyzwalany przez nowych komunikatów w Centrum zdarzeń. 
>

## <a name="next-steps"></a>Kolejne kroki

* [Interpretowanie schematu dzienniki inspekcji w usłudze Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretowanie dzienników logowania schematu w usłudze Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Często zadawane pytania i znane problemy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
