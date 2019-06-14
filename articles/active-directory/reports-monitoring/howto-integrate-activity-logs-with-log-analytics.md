---
title: Stream dzienniki usługi Azure Active Directory z dziennikami usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory przy użyciu dzienników usługi Azure Monitor
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
ms.openlocfilehash: 213fb6e73ae2fc4314320d0e3e593632d8eb7f85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437319"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrowanie dzienników usługi Azure AD przy użyciu dzienników usługi Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Dzienniki platformy Azure Monitor pozwala przesyłać zapytania dotyczące danych do znalezienia określonych zdarzeń, analizować trendy i wykonywać korelację między różnych źródeł danych. Dzięki integracji z usługą Azure AD Dzienniki aktywności w dzienniki usługi Azure Monitor, można teraz wykonywać zadania, takie jak:

 * Porównanie usługi Azure AD logowania dzienników względem dzienników zabezpieczeń opublikowane przez usługę Azure Security Center

 * Rozwiązywanie problemów z wąskich gardeł wydajności na stronie logowania w swojej aplikacji, przez skorelowanie danych dotyczących wydajności aplikacji z usługi Azure Application Insights.  

Poniższy klip wideo z sesji Konferencji Ignite przedstawia korzyści z używania usługi Azure Monitor dzienników dla dzienników usługi Azure AD w scenariuszach praktyczne użytkownika.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) przy użyciu usługi Azure Monitor.

## <a name="supported-reports"></a>Obsługiwane raporty

Dzienniki aktywności logowania i inspekcji dzienników aktywności można kierować do dzienników usługi Azure Monitor w celu dalszej analizy. 

* **Dzienniki inspekcji**: [Raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań, która jest wykonywana w dzierżawie.
* **Dzienniki logowania**: Za pomocą [raport aktywności logowania](concept-sign-ins.md), można określić, kto wykonał zadania, które są zgłaszane w dziennikach inspekcji.

> [!NOTE]
> Dzienniki aktywności inspekcji i logowania związane z funkcjami B2C nie są obecnie obsługiwane.
>

## <a name="prerequisites"></a>Wymagania wstępne 

Aby używać tej funkcji, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.
* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [Utwórz obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Wysyłanie dzienników do usługi Azure Monitor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz **usługi Azure Active Directory** > **ustawień diagnostycznych** -> **Dodaj ustawienie diagnostyczne**. Możesz również wybrać **Eksportuj ustawienia** z **dzienników inspekcji** lub **logowania** strony, aby przejść na stronę konfiguracji ustawień diagnostycznych.  
    
3. W **ustawień diagnostycznych** menu, wybierz opcję **wysyłania do obszaru roboczego usługi Log Analytics** pole wyboru, a następnie wybierz pozycję **Konfiguruj**.

4. Wybierz obszar roboczy usługi Log Analytics, aby wysłać dzienniki do lub Utwórz nowy obszar roboczy w oknie dialogowym podana.  

5. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji do obszaru roboczego usługi Log Analytics, zaznacz **AuditLogs** pole wyboru. 
    * Aby wysłać dzienniki do logowania do obszaru roboczego usługi Log Analytics, zaznacz **SignInLogs** pole wyboru.

6. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyczne](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po około 15 minut Sprawdź, czy zdarzenia są przesyłane strumieniowo do obszaru roboczego usługi Log Analytics.

## <a name="next-steps"></a>Kolejne kroki

* [Analizowanie usługi Azure AD dzienników aktywności przy użyciu dzienników usługi Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalowanie i używanie widoków analizy dzienników dla usługi Azure Active Directory](howto-install-use-log-analytics-views.md)