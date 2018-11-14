---
title: Jak zintegrować dzienniki usługi Azure Active Directory z usługą SumoLogic przy użyciu usługi Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą SumoLogic przy użyciu usługi Azure Monitor (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 4a39ee2fb057547c44c9eb08c85afdbb971ea5d5
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622418"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor-preview"></a>Integrowanie dzienników usługi Azure Active Directory za pomocą SumoLogic przy użyciu usługi Azure Monitor (wersja zapoznawcza)

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) przy użyciu SumoLogic przy użyciu usługi Azure Monitor. Skierować dzienniki do usługi Azure event hub, a następnie zintegrować Centrum zdarzeń za pomocą SumoLogic.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Dzienniki Centrum zdarzeń platformy Azure, który zawiera działania usługi Azure AD. Dowiedz się, jak [przesyłanie strumieniowe dzienników aktywności do Centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* SumoLogic logowanie jednokrotne włączone subskrypcji.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Kroki, aby zintegrować dzienniki usługi Azure AD z SumoLogic 

1. Po pierwsze, [przesyłanie strumieniowe dzienników usługi Azure AD do usługi Azure event hub](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Konfigurowanie wystąpienia SumoLogic do [zebrać dzienniki usługi Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Zainstaluj aplikację Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) używać wstępnie skonfigurowane pulpity nawigacyjne, które zapewniają analizę w czasie rzeczywistym w środowisku.

 ![Pulpit nawigacyjny](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Kolejne kroki

* [Interpret audit logs schema in Azure Monitor (Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor)](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
