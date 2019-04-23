---
title: Jak zainstalować i używać widoków analizy dzienników dla usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i korzystać z widokami analizy dzienników dla usługi Azure Active Directory
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
ms.openlocfilehash: 92a5d9539d254eb7d52e3277691c9d8ff5a41821
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005064"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalowanie i używanie widoków analizy dzienników dla usługi Azure Active Directory

Azure Active Directory rejestrować pomaga widoków usługi analytics, analizowane i wyszukiwania, które Dzienniki aktywności usługi Azure AD w dzierżawie usługi Azure AD. Usługi Azure AD activity dzienniki obejmują:

* Dzienniki inspekcji: [Raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań, która jest wykonywana w dzierżawie.
* Dzienniki logowania: Za pomocą [raport aktywności logowania](concept-sign-ins.md), można określić, kto wykonał zadania, które są zgłaszane w dziennikach inspekcji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z widokami analizy dzienników, potrzebne są:

* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [Utwórz obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Najpierw wykonaj kroki, aby [dzienników aktywności trasy usługi Azure AD, do obszaru roboczego usługi Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Pobierz widoków z [repozytorium GitHub](https://aka.ms/AADLogAnalyticsviews) na komputerze lokalnym.

## <a name="install-the-log-analytics-views"></a>Zainstaluj widoki analizy dzienników

1. Przejdź do obszaru roboczego usługi Log Analytics. Aby to zrobić, najpierw należy przejść do [witryny Azure portal](https://portal.azure.com) i wybierz **wszystkich usług**. Typ **usługi Log Analytics** w polu tekstowym, a następnie wybierz pozycję **obszarów roboczych usługi Log Analytics**. Wybierz obszar roboczy kierowane dzienników aktywności, jako część wymagań wstępnych.
2. Wybierz **Projektant widoków**, wybierz opcję **zaimportować** , a następnie wybierz **wybierz plik** do zaimportowania widoków z komputera lokalnego.
3. Wybierz widoki, pobranego z wymagań wstępnych i wybierz **Zapisz** można zapisać importu. Zrób to dla **zdarzenia aprowizacji konta usługi Azure AD** widoku i **zdarzenia logowania** widoku.

## <a name="use-the-views"></a>Użyj widoków

1. Przejdź do obszaru roboczego usługi Log Analytics. Aby to zrobić, najpierw należy przejść do [witryny Azure portal](https://portal.azure.com) i wybierz **wszystkich usług**. Typ **usługi Log Analytics** w polu tekstowym, a następnie wybierz pozycję **obszarów roboczych usługi Log Analytics**. Wybierz obszar roboczy kierowane dzienników aktywności, jako część wymagań wstępnych.

2. Gdy jesteś w obszarze roboczym, wybierz **podsumowanie obszaru roboczego**. Powinny zostać wyświetlone następujące trzy widoki:

    * **Konto usługi Azure AD inicjowania obsługi zdarzeń**: W tym widoku wyświetlane raporty związane z inspekcji działania obsługi, takie jak liczba nowych użytkowników zainicjowano obsługę administracyjną i problemy z aprowizowaniem, liczbę użytkowników, aktualizowane i zaktualizuj błędów oraz liczbę cofanie aprowizacji użytkowników i odpowiednie błędów.    
    * **Zdarzenia logowania**: Ten widok przedstawia najbardziej odpowiednie raporty dotyczące monitorowania aktywności logowania, takich jak logowania w aplikacji, użytkowników, urządzeń, a także śledzenia liczby operacji logowania wraz z upływem czasu widok podsumowania.

3. Wybierz jedną z tych widoków, aby przejść do poszczególnych raportów. Można również ustawić alerty na każdym z parametrów raportu. Na przykład możemy ustawić alert w przypadku za każdym razem, gdy występuje błąd logowania. Aby to zrobić, najpierw wybierz **zdarzenia logowania** widoku, wybierz opcję **błędy logowania wraz z upływem czasu** raportu, a następnie wybierz pozycję **Analytics** otwierających strony szczegółów rzeczywiste zapytanie za raportu. 

    ![Szczegóły](./media/howto-install-use-log-analytics-views/details.png)


4. Wybierz **ustawić Alert**, a następnie wybierz pozycję **jest zawsze wtedy, gdy niestandardowe wyszukiwanie w dzienniku &lt;logika niezdefiniowana&gt;**  w obszarze **kryteria alertu** sekcji. Ponieważ chcemy alertu zawsze, gdy występuje błąd logowania, należy ustawić **próg** domyślnej logiki alertu do **1** , a następnie wybierz **gotowe**. 

    ![Konfigurowanie logiki sygnału](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Wprowadź nazwę i opis alertu i Ustaw ważność **ostrzeżenie**.

    ![Utwórz regułę](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Wybierz grupę akcji do alertu. Ogólnie rzecz biorąc może to być zespołu, który chcesz powiadomić za pośrednictwem poczty e-mail lub wiadomości tekstowej lub może być zadania automatycznego przy użyciu elementów webhook, elementów runbook, functions, logic apps lub rozwiązaniami ITSM zewnętrznymi. Dowiedz się, jak [tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Wybierz **Utwórz regułę alertu** do utworzenia alertu. Teraz będziesz otrzymywać alerty za każdym razem, gdy występuje błąd logowania.

## <a name="next-steps"></a>Kolejne kroki

* [Jak analizować Dzienniki aktywności przy użyciu dzienników usługi Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Rozpoczynanie pracy z usługą Azure Monitor dzienników w witrynie Azure portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)