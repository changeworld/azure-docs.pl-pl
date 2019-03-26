---
title: Stream dzienniki usługi Azure Active Directory z dziennikami usługi Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory przy użyciu dzienników usługi Azure Monitor (wersja zapoznawcza)
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056a529101ffc39170ea057832bcd50b283505be
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436273"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs-preview"></a>Integrowanie dzienników usługi Azure AD przy użyciu dzienników usługi Azure Monitor (wersja zapoznawcza)

Dzienniki platformy Azure Monitor pozwala przesyłać zapytania dotyczące danych do znalezienia określonych zdarzeń, analizować trendy i wykonywać korelację między różnych źródeł danych. Dzięki integracji z usługą Azure AD Dzienniki aktywności w dzienniki usługi Azure Monitor, można teraz wykonywać zadania, takie jak:

 * Porównanie usługi Azure AD logowania dzienników względem dzienników zabezpieczeń opublikowane przez usługę Azure Security Center

 * Rozwiązywanie problemów z wąskich gardeł wydajności na stronie logowania w swojej aplikacji, przez skorelowanie danych dotyczących wydajności aplikacji z usługi Azure Application Insights.  

Poniższy klip wideo z sesji Konferencji Ignite przedstawia korzyści z używania usługi Azure Monitor dzienników dla dzienników usługi Azure AD w scenariuszach praktyczne użytkownika.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) przy użyciu usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

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

## <a name="send-logs-to-azure-monitor-logs"></a>Wysyłanie dzienników do dzienników usługi Azure Monitor

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

## <a name="next-steps"></a>Następne kroki

* [Analizowanie usługi Azure AD dzienników aktywności przy użyciu dzienników usługi Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalowanie i używanie widoków analizy dzienników dla usługi Azure Active Directory](howto-install-use-log-analytics-views.md)
