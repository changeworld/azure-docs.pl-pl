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
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239918"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Integrowanie dzienników usługi Azure Active Directory za pomocą Splunk przy użyciu usługi Azure Monitor (wersja zapoznawcza)

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą Splunk przy użyciu usługi Azure Monitor. Najpierw czy należy skierować dzienniki do Centrum zdarzeń platformy Azure, a następnie zintegrować go z Splunk.

## <a name="prerequisites"></a>Wymagania wstępne

1. Dzienniki Centrum zdarzeń Azure zawierające działanie usługi Azure AD. Dowiedz się, jak [przesyłanie strumieniowe dzienników aktywności do Centrum zdarzeń](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Należy użyć następującego [instrukcje](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) Aby pobrać dodatek usługi Azure monitor dla Splunk i skonfigurować wystąpienie usługi Splunk.

## <a name="tutorial"></a>Samouczek 

1. Otwórz swoje wystąpienie Splunk, a następnie kliknij przycisk **danych — Podsumowanie**.
    ![Podsumowanie danych](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "danych — podsumowanie")

2. Przejdź do **Sourcetypes** kartę, a następnie wybierz pozycję **amal: aadal:audit** ![kartę Sourcetypes](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes kartę")

3. Zobaczysz Azure AD activity dzienniki, jak pokazano na poniższej ilustracji.
    ![Dzienniki aktywności](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "dzienników aktywności")

> [!NOTE]
> Jeśli nie możesz zainstalować dodatek w wystąpieniu usługi Splunk (na przykład, jeśli jest używany serwer proxy lub działające w chmurze Splunk), możesz przekazywać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk za pomocą tego [funkcji platformy Azure, która jest wyzwalana przez nowych komunikatów w Centrum zdarzeń](https://github.com/Microsoft/AzureFunctionforSplunkVS). " 
>

## <a name="next-steps"></a>Kolejne kroki

* [Interpretowanie schematu dzienniki inspekcji w usłudze Azure monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretowanie dzienników logowania schematu w usłudze Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Często zadawane pytania i znane problemy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)