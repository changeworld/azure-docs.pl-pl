---
title: Archiwizuj raport & za pomocą usługi Azure Monitor — zarządzanie uprawnieniami usługi Azure AD
description: Dowiedz się, jak archiwizować dzienniki i tworzyć raporty za pomocą usługi Azure Monitor w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380188"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archiwizuj dzienniki i raportowanie dotyczące zarządzania uprawnieniami usługi Azure AD w usłudze Azure Monitor

Usługa Azure AD przechowuje zdarzenia inspekcji przez maksymalnie 30 dni w dzienniku inspekcji. Można jednak przechowywać dane inspekcji dłużej niż domyślny okres przechowywania, opisany w [jak długo dane raportowania magazynu usługi Azure AD?](../reports-monitoring/reference-reports-data-retention.md) Następnie można użyć skoroszytów i zapytań niestandardowych i raportów dotyczących tych danych.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurowanie usługi Azure AD do korzystania z usługi Azure Monitor
Przed użyciem skoroszytów usługi Azure Monitor należy skonfigurować usługę Azure AD, aby wysyłała kopię dzienników inspekcji do usługi Azure Monitor.

Archiwizowanie dzienników inspekcji usługi Azure AD wymaga, aby mieć usługę Azure Monitor w ramach subskrypcji platformy Azure. Możesz przeczytać więcej informacji o wymaganiach wstępnych i szacowanych kosztach korzystania z usługi Azure Monitor w [dziennikach aktywności usługi Azure AD w usłudze Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md)

**Rola wstępna**: Administrator globalny

1. Zaloguj się do witryny Azure portal jako użytkownik, który jest administratorem globalnym. Upewnij się, że masz dostęp do grupy zasobów zawierającej obszar roboczy usługi Azure Monitor.
 
1. Wybierz **pozycję Azure Active Directory,** a następnie kliknij pozycję Ustawienia **diagnostyczne** w obszarze Monitorowanie w menu nawigacji po lewej stronie. Sprawdź, czy istnieje już ustawienie wysyłania dzienników inspekcji do tego obszaru roboczego.

1. Jeśli nie ma jeszcze ustawienia, kliknij pozycję **Dodaj ustawienie diagnostyczne**. Skorzystaj z instrukcji w artykule [Integruj dzienniki usługi Azure AD z dziennikami usługi Azure Monitor,](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) aby wysłać dziennik inspekcji usługi Azure AD do obszaru roboczego usługi Azure Monitor.

    ![Okienko ustawień diagnostycznych](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Po wysłaniu dziennika do usługi Azure Monitor wybierz **obszary robocze usługi Log Analytics**i wybierz obszar roboczy zawierający dzienniki inspekcji usługi Azure AD.

1. Wybierz **opcję Użycie i szacowane koszty** oraz kliknij pozycję Przechowywanie **danych**. Zmień suwak na liczbę dni, przez które chcesz zachować dane, aby spełnić wymagania inspekcji.

    ![Okienko Obszarów roboczych usługi Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Później, aby wyświetlić zakres dat przechowywanych w obszarze roboczym, można użyć skoroszytu *Zarchiwizowanego zakresu dat dziennika:*  
    
    1. Wybierz **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję **Skoroszyty**. 
    
    1. Rozwiń sekcję **Rozwiązywanie problemów z usługą Azure Active Directory**i kliknij **zarchiwizowane zakres dat dziennika**. 


## <a name="view-events-for-an-access-package"></a>Wyświetlanie zdarzeń dla pakietu dostępu  

Aby wyświetlić zdarzenia dla pakietu dostępu, musisz mieć dostęp do obszaru roboczego monitora platformy Azure (zobacz [Zarządzanie dostępem do danych dziennika i obszarów roboczych w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) w celu uzyskania informacji) i w jednej z następujących ról: 

- Administrator globalny  
- Administrator zabezpieczeń  
- Czytelnik zabezpieczeń  
- Czytnik raportów  
- Administrator aplikacji  

Aby wyświetlić zdarzenia, użyj następującej procedury: 

1. W witrynie Azure Portal wybierz pozycję **Azure Active Directory,** a następnie kliknij pozycję **Skoroszyty**. Jeśli masz tylko jedną subskrypcję, przejdź do kroku 3. 

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, która zawiera obszar roboczy.  

1. Zaznacz skoroszyt o nazwie *Aktywność pakietu programu Access*. 

1. W tym skoroszycie wybierz zakres czasu (zmień na **Wszystkie,** jeśli nie jest pewien) i wybierz identyfikator pakietu dostępu z listy rozwijanej wszystkich pakietów dostępu, które miały działanie w tym zakresie czasu. Zostaną wyświetlone zdarzenia związane z pakietem dostępu, które wystąpiły podczas wybranego zakresu czasu.  

    ![Wyświetlanie zdarzeń pakietu dostępu](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Każdy wiersz zawiera czas, identyfikator pakietu dostępu, nazwę operacji, identyfikator obiektu, nazwę UPN i nazwę wyświetlaną użytkownika, który rozpoczął operację.  Dodatkowe szczegóły są zawarte w JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Tworzenie niestandardowych zapytań usługi Azure Monitor przy użyciu portalu Azure
Można utworzyć własne zapytania dotyczące zdarzeń inspekcji usługi Azure AD, w tym zdarzeń zarządzania uprawnieniami.  

1. W usłudze Azure Active Directory w witrynie Azure portal kliknij pozycję **Dzienniki** w sekcji Monitorowanie w menu nawigacji po lewej stronie, aby utworzyć nową stronę kwerendy.

1. Obszar roboczy powinien być wyświetlany w lewym górnym rogu strony kwerendy. Jeśli masz wiele obszarów roboczych usługi Azure Monitor, a obszar roboczy używany do przechowywania zdarzeń inspekcji usługi Azure AD nie jest wyświetlany, kliknij przycisk **Wybierz zakres**. Następnie wybierz poprawną subskrypcję i obszar roboczy.

1. Następnie w obszarze tekstowym kwerendy usuń ciąg "szukaj *" i zastąp go następującą kwerendą:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Następnie kliknij przycisk **Uruchom**. 

    ![Kliknij przycisk Uruchom, aby rozpocząć kwerendę](./media/entitlement-management-logs-and-reporting/run-query.png)

W tabeli domyślnie zostaną wyświetlona zdarzenia dziennika inspekcji dla zarządzania uprawnieniami z ostatniej godziny. Możesz zmienić ustawienie "Zakres czasu", aby wyświetlić starsze zdarzenia. Jednak zmiana tego ustawienia spowoduje wyświetlenie tylko zdarzeń, które wystąpiły po skonfigurowaniu usługi Azure AD do wysyłania zdarzeń do usługi Azure Monitor.

Jeśli chcesz poznać najstarsze i najnowsze zdarzenia inspekcji przechowywane w usłudze Azure Monitor, użyj następującej kwerendy:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Aby uzyskać więcej informacji na temat kolumn przechowywanych dla zdarzeń inspekcji w usłudze Azure Monitor, zobacz [Interpretowanie schematu dzienników inspekcji usługi Azure AD w usłudze Azure Monitor.](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Tworzenie niestandardowych zapytań usługi Azure Monitor przy użyciu programu Azure PowerShell

Możesz uzyskać dostęp do dzienników za pośrednictwem programu PowerShell po skonfigurowaniu usługi Azure AD do wysyłania dzienników do usługi Azure Monitor. Następnie należy wysyłać zapytania ze skryptów lub wiersza polecenia programu PowerShell, bez konieczności bycia administratorem globalnym w dzierżawie. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Upewnij się, że użytkownik lub podmiot zabezpieczeń usługi ma prawidłowe przypisanie roli

Upewnij się, że użytkownik lub podmiot usługi, który będzie uwierzytelniać się w usłudze Azure AD, znajdują się w odpowiedniej roli platformy Azure w obszarze roboczym usługi Log Analytics. Opcje roli są albo Log Analytics Reader lub Log Analytics Contributor. Jeśli jesteś już w jednej z tych ról, a następnie przejdź do [pobierania identyfikatora usługi Log Analytics z jedną subskrypcją platformy Azure.](#retrieve-log-analytics-id-with-one-azure-subscription)

Aby ustawić przypisanie roli i utworzyć kwerendę, wykonaj następujące czynności:

1. W witrynie Azure portal znajdź [obszar roboczy usługi Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Wybierz **pozycję Kontrola dostępu (IAM)**.

1. Następnie kliknij przycisk **Dodaj,** aby dodać przypisanie roli.

    ![Dodaj przypisanie roli](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalowanie modułu programu Azure PowerShell

Po uzyskaniu odpowiedniego przypisania roli uruchom program PowerShell i [zainstaluj moduł programu Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (jeśli jeszcze tego nie zrobiłeś), wpisując następujące wpisywanie:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Teraz możesz przystąpić do uwierzytelniania w usłudze Azure AD i pobrać identyfikator przeszukiwanego obszaru roboczego usługi Log Analytics.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Pobieranie identyfikatora usługi Log Analytics za pomocą jednej subskrypcji platformy Azure
Jeśli masz tylko jedną subskrypcję platformy Azure i jeden obszar roboczy usługi Log Analytics, wpisz następujące elementy, aby uwierzytelnić się w usłudze Azure AD, połącz się z tą subskrypcją i pobierz ten obszar roboczy:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Pobieranie identyfikatora usługi Log Analytics z wieloma subskrypcjami platformy Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) działa w jednej subskrypcji naraz. Tak, jeśli masz wiele subskrypcji platformy Azure, należy upewnić się, że łączysz się z tym, który ma obszar roboczy usługi Log Analytics z dzienników usługi Azure AD. 
 
 Następujące polecenia cmdlet wyświetlają listę subskrypcji i znajdują identyfikator subskrypcji, która ma obszar roboczy usługi Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Można ponownie uwierzytelnić i skojarzyć sesję programu PowerShell z `Connect-AzAccount –Subscription $subs[0].id`tą subskrypcją za pomocą polecenia, takiego jak . Aby dowiedzieć się więcej o tym, jak uwierzytelniać się na platformie Azure z programu PowerShell, w tym nieinteraktywnie, zobacz [Logowanie się za pomocą programu Azure PowerShell.](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)

Jeśli masz wiele obszarów roboczych usługi Log Analytics w tej subskrypcji, a następnie polecenie cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) zwraca listę obszarów roboczych. Następnie można znaleźć ten, który ma dzienniki usługi Azure AD. Pole `CustomerId` zwrócone przez to polecenie cmdlet jest takie samo jak wartość "Identyfikator obszaru roboczego" wyświetlana w portalu Azure w omówienie obszaru roboczego usługi Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Wysyłanie kwerendy do obszaru roboczego usługi Log Analytics
Na koniec po zidentyfikowaniu obszaru roboczego można użyć [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) do wysłania zapytania Kusto do tego obszaru roboczego. Te zapytania są napisane w [języku zapytania Kusto](https://docs.microsoft.com/azure/kusto/query/).
 
Na przykład można pobrać zakres dat rekordów zdarzeń inspekcji z obszaru roboczego usługi Log Analytics, za pomocą poleceń cmdlet programu PowerShell, aby wysłać zapytanie, takie jak:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Zdarzenia zarządzania uprawnieniami można również pobrać przy użyciu kwerendy, takiej jak:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Następne kroki:
- [Tworzenie interaktywnych raportów za pomocą skoroszytów usługi Azure Monitor](../../azure-monitor/app/usage-workbooks.md) 

