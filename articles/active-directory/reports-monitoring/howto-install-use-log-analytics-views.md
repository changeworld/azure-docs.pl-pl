---
title: Jak zainstalować i korzystać z widoków analizy dzienników | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować widoki analizy dzienników i używać ich dla usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014415"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalowanie widoków analizy dzienników dla usługi Azure Active Directory i używanie ich

Widoki analizy dzienników usługi Azure Active Directory ułatwia analizowanie i przeszukiwanie dzienników aktywności usługi Azure AD w dzierżawie usługi Azure AD. Dzienniki aktywności usługi Azure AD obejmują:

* Dzienniki inspekcji: [Raport aktywności dzienników inspekcji](concept-audit-logs.md) daje dostęp do historii każdego zadania, które jest wykonywane w dzierżawie.
* Dzienniki logowania: W [raporcie aktywności logowania](concept-sign-ins.md)można określić, kto wykonał zadania, które są zgłaszane w dziennikach inspekcji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z widoków analizy dzienników, potrzebujesz:

* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [utworzyć obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Najpierw wykonaj kroki, aby [przekierować dzienniki aktywności usługi Azure AD do obszaru roboczego usługi Log Analytics.](howto-integrate-activity-logs-with-log-analytics.md)
* Pobierz widoki z [repozytorium GitHub](https://aka.ms/AADLogAnalyticsviews) na komputer lokalny.

## <a name="install-the-log-analytics-views"></a>Instalowanie widoków analizy dzienników

1. Przejdź do obszaru roboczego usługi Log Analytics. Aby to zrobić, najpierw przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz **pozycję Wszystkie usługi.** Wpisz **analizę dziennika** w polu tekstowym i wybierz pozycję Obszary **robocze usługi Log Analytics**. Wybierz obszar roboczy, do którego kierowano dzienniki aktywności, jako część wymagań wstępnych.
2. Wybierz **polecenie Projektant widoku**, wybierz pozycję **Importuj,** a następnie wybierz **pozycję Wybierz plik,** aby zaimportować widoki z komputera lokalnego.
3. Wybierz widoki pobrane z wymagań wstępnych i wybierz pozycję **Zapisz,** aby zapisać import. W tym celu należy wykonać w widoku **Zdarzenia inicjowania obsługi administracyjnej konta usługi Azure AD** i widoku Zdarzenia **logowania.**

## <a name="use-the-views"></a>Korzystanie z widoków

1. Przejdź do obszaru roboczego usługi Log Analytics. Aby to zrobić, najpierw przejdź do [witryny Azure portal](https://portal.azure.com) i wybierz **pozycję Wszystkie usługi.** Wpisz **analizę dziennika** w polu tekstowym i wybierz pozycję Obszary **robocze usługi Log Analytics**. Wybierz obszar roboczy, do którego kierowano dzienniki aktywności, jako część wymagań wstępnych.

2. Po wejściu do obszaru roboczego wybierz pozycję **Podsumowanie obszaru roboczego**. Powinny zostać wyświetlenia następujących trzech widoków:

    * **Zdarzenia inicjowania obsługi administracyjnej konta usługi Azure AD:** W tym widoku są wyświetlane raporty związane z inspekcją działania inicjowania obsługi administracyjnej, takie jak liczba nowych użytkowników aprowizacji i inicjowanie obsługi administracyjnej błędów, liczba użytkowników zaktualizowanych i aktualizowanych błędów oraz liczba użytkowników wyrejeprowaconych i odpowiadających im błędów.    
    * **Zdarzenia logowania:** W tym widoku są wyświetlane najbardziej odpowiednie raporty związane z monitorowaniem aktywności logowania, takie jak logowania według aplikacji, użytkownika, urządzenia, a także widok podsumowania śledzący liczbę logów w czasie.

3. Wybierz jeden z tych widoków, aby przejść do poszczególnych raportów. Można również ustawić alerty dotyczące dowolnego parametru raportu. Na przykład ustawmy alert za każdym razem, gdy występuje błąd logowania. Aby to zrobić, najpierw wybierz widok **Zdarzenia logowania,** wybierz raport **o błędach logowania w czasie,** a następnie wybierz **pozycję Analytics,** aby otworzyć stronę szczegółów z rzeczywistą kwerendą za raportem. 

    ![Szczegóły](./media/howto-install-use-log-analytics-views/details.png)


4. Wybierz **pozycję Ustaw alert**, a następnie wybierz pozycję **Zawsze, gdy wyszukiwanie dziennika niestandardowego jest &lt;logiką niezdefiniowana&gt; ** w sekcji Kryteria **alertu.** Ponieważ chcemy otrzymywać alerty za każdym razem, gdy wystąpi błąd logowania, ustaw **próg** domyślnej logiki alertu na **1,** a następnie wybierz **opcję Gotowe**. 

    ![Konfigurowanie logiki sygnału](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Wprowadź nazwę i opis alertu i ustaw ważność na **Ostrzeżenie**.

    ![Tworzenie reguły](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Wybierz grupę akcji, aby otrzymywać alerty. Ogólnie rzecz biorąc, może to być zespół, który chcesz powiadomić za pośrednictwem poczty e-mail lub wiadomości tekstowej, lub może to być zautomatyzowane zadanie za pomocą elementów webhook, runbook, funkcji, aplikacji logicznych lub zewnętrznych rozwiązań ITSM. Dowiedz się, jak [tworzyć grupy akcji i zarządzać nimi w witrynie Azure portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Wybierz **pozycję Utwórz regułę alertu,** aby utworzyć alert. Teraz będziesz otrzymywać alerty za każdym razem, gdy wystąpi błąd logowania.

## <a name="next-steps"></a>Następne kroki

* [Jak analizować dzienniki aktywności za pomocą dzienników usługi Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Wprowadzenie do dzienników usługi Azure Monitor w portalu Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)