---
title: Analizowanie dzienników aktywności usługi Azure Active Directory przy użyciu usługi Log Analytics (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak analizować Dzienniki aktywności usługi Azure Active Directory przy użyciu usługi Log Analytics (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: a508c8a2bb4a28372f5dd8f833f6e55adc3c39ed
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158744"
---
# <a name="analyze-azure-ad-activity-logs-with-log-analytics-preview"></a>Analizowanie usługi Azure AD dzienników aktywności z usługą Log Analytics (wersja zapoznawcza)

Po zakończeniu [integracji usługi Azure AD dzienników aktywności z usługą Log Analytics](howto-integrate-activity-logs-with-log-analytics.md), możliwości usługi Log Analytics można użyć, aby uzyskać wgląd w danym środowisku. Można także zainstalować [widoki usługi Log Analytics dla usługi Azure AD działania dzienniki](howto-install-use-log-analytics-views.md) uzyskać dostęp do gotowych raportów dotyczących inspekcji i zdarzeń logowania w danym środowisku.

W tym artykule dowiesz się, jak analizować Azure Dzienniki aktywności usługi AD w obszarze roboczym usługi Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne 

W tym samouczku potrzebne są:

* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [Utwórz obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Najpierw wykonaj kroki, aby [dzienników aktywności trasy usługi Azure AD, do obszaru roboczego usługi Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Przejdź do obszaru roboczego usługi Log Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **dzienniki** z **monitorowanie** sekcji, aby otworzyć obszar roboczy usługi Log Analytics. Obszar roboczy zostanie otwarty z zapytanie domyślne.

    ![Domyślne zapytanie](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Schemat działania usługi Azure AD dzienników

Dzienniki są przekazywane do **AuditLogs** i **SigninLogs** tabel w obszarze roboczym. Aby wyświetlić schemat dla tych tabel:

1. Domyślny widok zapytania w poprzedniej sekcji, zaznacz **schematu** i rozwiń obszar roboczy. 

2. Rozwiń **Zarządzanie dziennikami** sekcji, a następnie rozwiń opcję **AuditLogs** lub **SignInLogs** Aby wyświetlić schemat dziennika.
    ![Dzienniki inspekcji](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![logowanie za pomocą dzienników](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Dzienniki aktywności zapytań usługi Azure AD

Teraz, gdy dzienniki w obszarze roboczym możesz teraz uruchomić zapytania względem nich. Na przykład, aby uzyskać najważniejsze aplikacje używane w ostatnim tygodniu, Zastąp domyślne zapytanie poniżej, a następnie wybierz pozycję **uruchamiania**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Aby uzyskać zdarzeń inspekcji najważniejszych w ciągu ostatniego tygodnia, użyj następującego zapytania:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alerty dotyczące danych dziennika aktywności usługi Azure AD

Można również ustawić alerty na zapytanie. Na przykład aby skonfigurować alert, gdy więcej niż 10 aplikacji były używane w ostatnim tygodniu:

1. W obszarze roboczym wybierz **Ustaw alert** otworzyć **Utwórz regułę** strony. 
    ![Ustawianie alertu](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Wybierz domyślną **kryteria alertu** utworzone w alertu i Aktualizuj **próg** w metrykę do 10. 
    ![Kryteria alertu](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Wprowadź nazwę i opis alertu, a następnie wybierz poziom ważności. W naszym przykładzie, możemy ustawić ją na **komunikat o charakterze informacyjnym**.

4. Wybierz **grupy akcji** , alerty, gdy wystąpi sygnał. Można wybrać powiadomić Twój zespół za pośrednictwem poczty e-mail lub wiadomości tekstowej lub można zautomatyzować akcji przy użyciu elementów webhook, usługa Azure functions lub logic apps. Dowiedz się więcej o [alert grup w witrynie Azure portal, tworzenia i zarządzania nimi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Po skonfigurowaniu alert, wybierz **Utwórz alert** ją włączyć. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalowanie i używanie wbudowanych widoków dla działania usługi Azure AD dzienników

Możesz również pobrać wstępnie skompilowanych widoki usługi Log Analytics dla działania usługi Azure AD dzienniki. Widoki przedstawiają kilka raportów, które dotyczą typowych scenariuszy obejmujących inspekcji i zdarzeń logowania. Użytkownik może również powiadamiać na wszystkich danych zawartych w raportach, korzystając z procedury opisanej w poprzedniej sekcji.

* **Konto usługi Azure AD inicjowania obsługi zdarzeń**: W tym widoku wyświetlane raporty związane z inspekcji działania obsługi, takie jak liczba nowych użytkowników zainicjowano obsługę administracyjną i problemy z aprowizowaniem, liczbę użytkowników, aktualizowane i zaktualizuj błędów oraz liczbę cofanie aprowizacji użytkowników i odpowiednie błędów.    
* **Zdarzenia logowania**: Ten widok przedstawia najbardziej odpowiednie raporty dotyczące monitorowania aktywności logowania, takich jak logowania w aplikacji, użytkowników, urządzeń, a także śledzenia liczby operacji logowania wraz z upływem czasu widok podsumowania.
* **Użytkownicy wyrażania zgody**: Ten widok przedstawia raportów związanych z zgody użytkownika, takie jak zgody przyznaje przez użytkownika, logowania przez użytkowników, którzy uzyska zatwierdzenie, a także logowania przez aplikację dla wszystkich aplikacji opartych na zgody. 

Dowiedz się, jak [zainstalować widoki usługi Log Analytics dla dzienników aktywności usługi Azure AD i używać ich](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do zapytań w usłudze Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Tworzenie i zarządzanie grupami alertów w witrynie Azure portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalowanie i używanie widoki usługi Log Analytics dla usługi Azure Active Directory](howto-install-use-log-analytics-views.md)
