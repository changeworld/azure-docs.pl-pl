---
title: Jak zintegrować dzienniki usługi Azure Active Directory z usługą Log Analytics przy użyciu usługi Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą Log Analytics przy użyciu usługi Azure Monitor (wersja zapoznawcza)
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
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 95dac6804f3226cac23bce95a1b09b2907d084b0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395649"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Integrowanie dzienników usługi Azure AD z usługą Log Analytics przy użyciu usługi Azure Monitor (wersja zapoznawcza)

Usługa log Analytics pozwala przesyłać zapytania dotyczące danych do znalezienia określonych zdarzeń, analizować trendy i wykonywać korelację między różnych źródeł danych. Dzięki integracji z usługą Azure AD Dzienniki aktywności w usłudze Log Analytics można teraz wykonywać zadania, takie jak:

 * Porównanie usługi Azure AD logowania dzienników względem dzienników zabezpieczeń opublikowane przez usługę Azure Security Center

 * Rozwiązywanie problemów z wąskich gardeł wydajności na stronie logowania w swojej aplikacji, przez skorelowanie danych dotyczących wydajności aplikacji z usługi Azure Application Insights.  
 
W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) z usługą Log Analytics przy użyciu usługi Azure Monitor.

## <a name="supported-reports"></a>Obsługiwane raporty

Dzienniki aktywności logowania i inspekcji dzienników aktywności do usługi Log Analytics może kierować do dalszej analizy. 

* **Dzienniki inspekcji**: [raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.
* **Dzienniki logowania**: przy użyciu [raportu działań dotyczącego logowań](concept-sign-ins.md) można określić, kto wykonał zadania zgłoszone w dziennikach inspekcji.

> [!NOTE]
> Dzienniki aktywności inspekcji i logowania związane z funkcjami B2C nie są obecnie obsługiwane.
>

## <a name="prerequisites"></a>Wymagania wstępne 

Aby używać tej funkcji, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.
* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [Utwórz obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz **usługi Azure Active Directory** > **ustawień diagnostycznych** -> **Dodaj ustawienie diagnostyczne**. Możesz również wybrać **Eksportuj ustawienia** z **dzienników inspekcji** lub **logowania** strony, aby przejść na stronę konfiguracji ustawień diagnostycznych.  
    
3. W **ustawień diagnostycznych** menu, wybierz opcję **wysyłanie do usługi Log Analytics** pole wyboru, a następnie wybierz pozycję **Konfiguruj**.

4. Wybierz obszar roboczy usługi Log Analytics, aby wysłać dzienniki do lub Utwórz nowy obszar roboczy w oknie dialogowym podana.  

5. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji do obszaru roboczego usługi Log Analytics, zaznacz **AuditLogs** pole wyboru. 
    * Aby wysłać dzienniki do logowania do obszaru roboczego usługi Log Analytics, zaznacz **SignInLogs** pole wyboru.

6. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyczne](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po około 15 minut Sprawdź, czy zdarzenia są przesyłane strumieniowo do obszaru roboczego usługi Log Analytics.

## <a name="next-steps"></a>Następne kroki

* [Analizowanie usługi Azure AD activity dzienników w usłudze Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Instalowanie i używanie widoki usługi Log Analytics dla usługi Azure Active Directory](howto-install-use-log-analytics-views.md)