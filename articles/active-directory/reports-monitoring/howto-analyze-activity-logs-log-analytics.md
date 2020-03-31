---
title: Analizowanie dzienników aktywności przy użyciu dzienników usługi Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak analizować dzienniki aktywności usługi Azure Active Directory przy użyciu dzienników usługi Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008289"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników usługi Azure Monitor

Po [zintegrowaniu dzienników aktywności usługi Azure AD z dziennikami usługi Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)można użyć funkcji dzienników usługi Azure Monitor, aby uzyskać wgląd w środowisko. Można również zainstalować [widoki analizy dzienników dla dzienników aktywności usługi Azure AD,](howto-install-use-log-analytics-views.md) aby uzyskać dostęp do wstępnie utworzonych raportów wokół inspekcji i zdarzeń logowania w twoim środowisku.

W tym artykule dowiesz się, jak analizować dzienniki aktywności usługi Azure AD w obszarze roboczym usługi Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

Aby podążać za nimi, potrzebujesz:

* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [utworzyć obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Najpierw wykonaj kroki, aby [przekierować dzienniki aktywności usługi Azure AD do obszaru roboczego usługi Log Analytics.](howto-integrate-activity-logs-with-log-analytics.md)
*  [Dostęp](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) do obszaru roboczego analizy dzienników
* Następujące role w usłudze Azure Active Directory (jeśli uzyskujesz dostęp do usługi Log Analytics za pośrednictwem portalu usługi Azure Active Directory)
    - Administrator zabezpieczeń
    - Czytelnik zabezpieczeń
    - Czytnik raportów
    - Administrator globalny
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Przechodzenie do obszaru roboczego usługi Log Analytics

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Dzienniki** z sekcji **Monitorowanie,** aby otworzyć obszar roboczy usługi Log Analytics. Obszar roboczy zostanie otwarty z kwerendą domyślną.

    ![Kwerenda domyślna](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Wyświetlanie schematu dzienników aktywności usługi Azure AD

Dzienniki są wypychane do **tabel AuditLogs** i **SigninLogs** w obszarze roboczym. Aby wyświetlić schemat dla tych tabel:

1. Z domyślnego widoku kwerendy w poprzedniej sekcji wybierz pozycję **Schemat** i rozwiń obszar roboczy. 

2. Rozwiń sekcję **Zarządzanie dziennikami,** a następnie rozwiń **dzienniki inspekcji** lub **dzienniki logowania,** aby wyświetlić schemat dziennika.
    ![Inspekcja](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![dzienników Logowania](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Kwerenda dzienniki aktywności usługi Azure AD

Teraz, gdy masz dzienniki w obszarze roboczym, można teraz uruchamiać zapytania przeciwko nim. Na przykład, aby uzyskać najlepsze aplikacje używane w ostatnim tygodniu, zastąp kwerendę domyślną następującym i wybierz **przycisk Uruchom**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Aby uzyskać najważniejsze zdarzenia inspekcji w ciągu ostatniego tygodnia, użyj następującej kwerendy:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alerty dotyczące danych dziennika aktywności usługi Azure AD

Można również skonfigurować alerty w zapytaniu. Na przykład, aby skonfigurować alert, gdy w ostatnim tygodniu użyto więcej niż 10 aplikacji:

1. W obszarze roboczym wybierz pozycję **Ustaw alert,** aby otworzyć stronę **Utwórz regułę.**

    ![Ustawianie alertu](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Wybierz **domyślne kryteria alertu** utworzone w alertie i zaktualizuj **próg** w metryki domyślnej do 10.

    ![Kryteria alertu](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Wprowadź nazwę i opis alertu i wybierz poziom ważności. Na przykład możemy ustawić go na **Informacyjny**.

4. Wybierz **grupę akcji,** która będzie ostrzegana po wystąpieniu sygnału. Możesz powiadomić zespół za pośrednictwem poczty e-mail lub wiadomości tekstowej lub zautomatyzować akcję przy użyciu zestawów webhook, funkcji platformy Azure lub aplikacji logiki. Dowiedz się więcej o [tworzeniu grup alertów i zarządzaniu nimi w witrynie Azure portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Po skonfigurowaniu alertu wybierz pozycję **Utwórz alert,** aby go włączyć. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalowanie i używanie wstępnie utworzonych widoków dla dzienników aktywności usługi Azure AD

Można również pobrać wstępnie utworzone widoki analizy dziennika dla dzienników aktywności usługi Azure AD. Widoki zawierają kilka raportów związanych z typowymi scenariuszami obejmującymi inspekcję i zdarzenia logowania. Można również alerty na temat wszelkich danych podanych w raportach, wykonując kroki opisane w poprzedniej sekcji.

* **Zdarzenia inicjowania obsługi administracyjnej konta usługi Azure AD:** W tym widoku są wyświetlane raporty związane z inspekcją działania inicjowania obsługi administracyjnej, takie jak liczba nowych użytkowników aprowizacji i inicjowanie obsługi administracyjnej błędów, liczba użytkowników zaktualizowanych i aktualizowanych błędów oraz liczba użytkowników wyrejeprowaconych i odpowiadających im błędów.    
* **Zdarzenia logowania:** W tym widoku są wyświetlane najbardziej odpowiednie raporty związane z monitorowaniem aktywności logowania, takie jak logowania według aplikacji, użytkownika, urządzenia, a także widok podsumowania śledzący liczbę logów w czasie.
* **Użytkownicy wykonujący zgodę:** Ten widok zawiera raporty związane z zgodą użytkownika, takie jak zgoda udzielona przez użytkownika, logowania przez użytkowników, którzy udzielili zgody, a także logowania przez aplikację dla wszystkich aplikacji opartych na zgodzie. 

Dowiedz się, jak [zainstalować widoki analizy dzienników dla dzienników aktywności usługi Azure AD i używać ich](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do zapytań w dziennikach usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Tworzenie grup alertów i zarządzanie nimi w witrynie Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalowanie widoków analizy dzienników dla usługi Azure Active Directory i używanie ich](howto-install-use-log-analytics-views.md)
