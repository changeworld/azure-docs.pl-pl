---
title: Dodawanie rozwiązania do zarządzania Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania na platformie Azure są Kolekcja reguł nabycia logiki, wizualizacji i danych zawierających metryki przestawiać wokół obszaru określonego problemu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 0cffdbcaa128dce52758e4b618b93dd763961361
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131479"
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Dodawanie rozwiązania do zarządzania usługi Analiza dzienników Azure do swojego obszaru roboczego

Dziennik rozwiązań do zarządzania Analytics to zbiór **logiki**, **wizualizacji**, i **reguły pozyskiwania danych** zawierających metryki przestawiać wokół określonego obszar problem. W tym artykule wymieniono obsługiwane przez analizy dzienników rozwiązania do zarządzania i przedstawiono sposób dodawania i usuwania dla obszaru roboczego przy użyciu portalu Azure.

Zezwalaj na lepszy wgląd do rozwiązania do zarządzania:

* Ułatwić badanie i rozwiązywanie problemów z działaniem szybciej
* Zbieranie i skorelowania różnych typów danych komputera
* Pomogą Ci aktywnego z działaniami, które udostępnia rozwiązania.

> [!NOTE]
> Analizy dzienników obejmuje funkcje wyszukiwania dziennika, więc nie trzeba zainstalować rozwiązania do zarządzania, aby je włączyć. Jednak otrzymasz wizualizacjami danych, sugerowane wyszukiwania i szczegółowe informacje, dodając rozwiązania do zarządzania do swojego obszaru roboczego.

Korzystając z tego artykułu, należy dodać rozwiązania do zarządzania do obszaru roboczego przy użyciu portalu Azure Marketplace. Po dodaniu rozwiązania, dane są zbierane z serwerów w ramach infrastruktury i wysyłane do analizy dzienników. Przetwarzanie zazwyczaj trwa kilka minut na godzinę. Po usługa przetwarza dane, można go wyświetlić w analizy dzienników.

Gdy nie jest już potrzebne, można łatwo usunąć rozwiązania do zarządzania. Podczas usuwania rozwiązania do zarządzania jego dane nie są wysyłane do analizy dzienników. Jeśli na warstwy cenowej bezpłatna, usuwanie rozwiązania można zmniejszyć ilość danych używane, pomaga pozostają w obszarze dzienny limit przydziału danych.

## <a name="view-available-management-solutions"></a>Widok zarządzania dostępnych rozwiązań

Azure marketplace zawiera listę [rozwiązań do zarządzania dla analizy dzienników](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Rozwiązania do zarządzania można zainstalować z portalu Azure marketplace, klikając **Pobierz teraz** łącze u dołu każdego z rozwiązań.

## <a name="add-a-management-solution"></a>Dodaj rozwiązanie do zarządzania
1. Jeśli nie zostało to jeszcze zrobione, zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu subskrypcji platformy Azure.
2. Wybierz **Utwórz zasób** > **monitorowanie i zarządzanie** > **zobaczyć wszystkie**.  
    ![Monitorowanie + blok zarządzania](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. Po prawej stronie **rozwiązań do zarządzania**, kliknij przycisk **więcej**.
5. W **rozwiązań do zarządzania** bloku, wybierz rozwiązanie do zarządzania, które mają zostać dodane do obszaru roboczego.  
    ![Monitorowanie + blok zarządzania](./media/log-analytics-add-solutions/management-solutions.png)  
6. W bloku rozwiązania zarządzania, przejrzyj informacje dotyczące rozwiązania do zarządzania, a następnie kliknij **Utwórz**.
7. W bloku *nazwy rozwiązania do zarządzania* wybierz obszar roboczy, który chcesz skojarzyć z rozwiązaniem do zarządzania.
8. Opcjonalnie można zmienić ustawienia obszaru roboczego dla subskrypcji platformy Azure, grupy zasobów i lokalizacji. Można również wybrać **opcje automatyzacji**. Kliknij przycisk **Utwórz**.  
    ![obszar roboczy rozwiązania](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Aby rozpocząć korzystanie z rozwiązania do zarządzania, które zostały dodane do obszaru roboczego, przejdź do **analizy dzienników** > **subskrypcje** > ***nazwa obszaru roboczego***  >  **Omówienie**. Zostanie wyświetlony nowy Kafelek rozwiązania do zarządzania. Kliknij Kafelek, aby go otworzyć i uruchomić za pomocą rozwiązania, po zebraniu danych dla rozwiązania.

## <a name="remove-a-management-solution"></a>Usuń rozwiązanie do zarządzania

1. W [portalu Azure](https://portal.azure.com), przejdź do **analizy dzienników** > **subskrypcje** > ***nazwa obszaru roboczego*** i następnie w ***nazwa obszaru roboczego*** bloku, kliknij przycisk **rozwiązań**.
2. Na liście rozwiązań do zarządzania wybierz rozwiązanie, które chcesz usunąć.
3. W bloku rozwiązania dla obszaru roboczego kliknij **usunąć**.  
    ![Usuwanie rozwiązania](./media/log-analytics-add-solutions/solution-delete.png)  
4. W oknie dialogowym potwierdzenia kliknij **tak**.

## <a name="offers-and-pricing-tiers"></a>Oferty i warstw cenowych

W poniższej tabeli przedstawiono rozwiązania do zarządzania należącego do każdej oferty usługi Operations Management Suite.
W tabeli przedstawiono również warstw cenowych, które są dostępne dla poszczególnych rozwiązań do zarządzania.
Wszystkie rozwiązania w poniższej tabeli są dostępne z poziomu portalu Azure i galerii rozwiązań w portalu usługi Analiza dzienników.

| Rozwiązanie do zarządzania                                                                       | Oferta                                                                     | Warstwa cenowa<sup>1</sup>                                                 | Uwagi |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Activity Log Analytics](log-analytics-activity.md)                                                                   | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | 90 dni dane są dostępne bezpłatnie<br>Dane nie mogą ulec zakończenia warstwa bezpłatna |
| [Ocena usługi AD](log-analytics-ad-assessment.md)                                           | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Stan replikacji usługi AD](log-analytics-ad-replication-status.md)                           | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Nie można dodać z portalu Azure/portalu marketplace. |
| [Kondycja agenta](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Dane nie mogą ulec zakończenia warstwa bezpłatna<br> Nie można dodać z portalu Azure/portalu marketplace. |
| [Zarządzanie alertami](log-analytics-solution-alert-management.md)                            | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Nie można dodać z portalu Azure/portalu marketplace. |
| [Łącznik aplikacji Insights (wersja zapoznawcza)](log-analytics-app-insights-connector.md)                                               | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Automatyzacja hybrydowego procesu roboczego](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automation and Control</li></ul>                                  | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | Wymaga obszaru roboczego analizy dzienników powiązać konto automatyzacji |
| [Analiza bramy aplikacji Azure](log-analytics-azure-networking-analytics.md)    | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Grupy zabezpieczeń sieci Azure analityka](log-analytics-azure-networking-analytics.md)     | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Analiza Azure SQL (wersja zapoznawcza)](log-analytics-azure-sql.md)                                                       | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br>Na&nbsp;węzła&nbsp;(OMS)                                                                          | Wymaga obszaru roboczego analizy dzienników powiązać konto automatyzacji|
| [Analiza w usłudze Azure Web Apps](log-analytics-azure-web-apps-analytics.md)     | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
|[Tworzenie kopii zapasowych](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Wgląd w dane i analizy</li></ul>                                   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)                                                                       | Wymaga klasycznego magazyn kopii zapasowych.<br> Nie można dodać z portalu Azure/portalu marketplace. |
| [Pojemność i wydajność (wersja zapoznawcza)](log-analytics-capacity.md)                                                   | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Śledzenie zmian](log-analytics-change-tracking.md)                                       | <ul><li>Automation and Control</li></ul>                                  | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | Wymaga obszaru roboczego analizy dzienników powiązać konto automatyzacji |
| [Containers](log-analytics-containers.md)                                                 | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Łącznik zarządzania usługami IT](log-analytics-itsmc-overview.md)                                                | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)     | |
| HDInsight HBase monitorowania <br>(Wersja zapoznawcza)                                                  | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Analiza usługi Key Vault](log-analytics-azure-key-vault.md)                   | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [B2B aplikacje logiki](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Nie można dodać z portalu Azure/portalu marketplace. |
| [Ocena złośliwego oprogramowania](log-analytics-malware.md)                                            | <ul><li>Zabezpieczenia i zgodność</li></ul>                                 | Bezpłatna<br> Autonomiczna<br>Na&nbsp;węzła&nbsp;(OMS)                                                                           | Po dodaniu rozwiązań zabezpieczeń i zgodności po 19 czerwca 2017 [rozliczeń jest na węzeł](https://azure.microsoft.com/pricing/details/security-compliance/), niezależnie od warstwy cenowej obszaru roboczego. Mogą przez pierwsze 60 dni.  |
| [Monitor wydajności sieci](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Wgląd w dane i analizy</li></ul>                                   | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | |
| [Analiza usługi Office 365 (wersja zapoznawcza)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Zabezpieczenia i inspekcja](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Zabezpieczenia&nbsp;i&nbsp;zgodności</li></ul>                       | Bezpłatna<br> Autonomiczna<br>Na&nbsp;węzła&nbsp;(OMS)                                                                           | Zbieranie dzienników zdarzeń zabezpieczeń wymaga tego rozwiązania<br>Po dodaniu rozwiązań zabezpieczeń i zgodności po 19 czerwca 2017 [rozliczeń jest na węzeł](https://azure.microsoft.com/pricing/details/security-compliance/), niezależnie od warstwy cenowej obszaru roboczego. Mogą przez pierwsze 60 dni. |
| [Usługa sieci szkieletowej Analytics (wersja zapoznawcza)](log-analytics-service-fabric.md)                     | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Mapa usług (wersja zapoznawcza)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Wgląd w dane i analizy</li></ul>                      | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | Dostępne w wschodnie stany USA, Europa Zachodnia, zachodnie środkowe stany USA    |
| [Site Recovery](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Wgląd w dane i analizy</li></ul>                                   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)                                                                       | Wymaga klasycznego magazynu usługi Site Recovery.<br> Nie można dodać z portalu Azure/portalu marketplace. |
| [Ocena serwera SQL](log-analytics-sql-assessment.md)                                         | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy<br>(Wersja zapoznawcza)                                              | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | Wymaga obszaru roboczego analizy dzienników powiązać konto automatyzacji |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Nie można dodać z portalu Azure/portalu marketplace. |
| [Do oceny programu System Center Operations Manager (wersja zapoznawcza)](log-analytics-scom-assessment.md)  | <ul><li>Wgląd w dane i analizy</li><li>Log Analytics</li></ul>        | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Zarządzanie aktualizacjami](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automation and Control</li></ul>                                  | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | Wymaga obszaru roboczego analizy dzienników powiązać konto automatyzacji |
| [Informacje o zgodności aktualizacji (wersja zapoznawcza)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Bezpłatne danych lub w węzłach<br>Dane nie mogą ulec warstwę bezpłatna centralnych zasad dostępu.<br> Nie można dodać z portalu Azure/portalu marketplace. |
| [Gotowość do uaktualnienia](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | Bezpłatne danych lub w węzłach<br>Dane nie mogą ulec warstwę bezpłatna centralnych zasad dostępu.<br> Nie można dodać z portalu Azure/portalu marketplace. |
| [VMware monitorowania (wersja zapoznawcza)](log-analytics-vmware.md)                                | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Standardowa (Standard)<br> Premium&nbsp;(OMS)<br> Na&nbsp;GB&nbsp;(autonomiczna)<br> Na&nbsp;węzła&nbsp;(OMS)   | |
| [Podczas transmisji danych 2.0 (wersja zapoznawcza)](log-analytics-wire-data.md)                                                                 | <ul><li>Wgląd w dane i analizy</li></ul>                                   | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)                                                                         | Dostępne w wschodnie stany USA, Europa Zachodnia, zachodnie środkowe stany USA |

<sup>1</sup> *standardowe* i *Premium (OMS)* warstwy cenowej są dostępne tylko dla klientów, którzy utworzone ich obszaru roboczego analizy dzienników przed 21 września 2016 roku.

### <a name="community-provided-management-solutions"></a>Społeczność podane rozwiązania do zarządzania

Są dostępne z rozwiązania społeczności podane [galerii szablonu Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) i bezpośrednio z autorów.

| Rozwiązanie do zarządzania               | Oferta                                                                     | Warstwy cenowe                         | Uwagi |
| ---                               | ---                                                                       | ---                                   | ---   |
| Wszystkie rozwiązania podane społeczności  | <ul><li>Szczegółowe informacje o&nbsp;i&nbsp;analityka</li><li>Log Analytics</li></ul>   | Bezpłatna<br> Na&nbsp;węzła&nbsp;(OMS)     |   Wymaga obszaru roboczego analizy dzienników powiązać konto automatyzacji |




## <a name="data-collection-details"></a>Szczegóły danych kolekcji
W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o jak zbierane są dane dla rozwiązania do zarządzania analizy dzienników i źródeł danych. Tabele są pogrupowane według oferty rozwiązania, które są równoważne [subskrypcji warstw cenowych](https://go.microsoft.com/fwlink/?linkid=827926). Rozwiązania analizy dziennika działanie jest dostępne dla wszystkich warstw cenowych bezpłatnie.

Agent Windows analizy dziennika i agent programu System Center Operations Manager zasadniczo są takie same. Agent systemu Windows zawiera dodatkowe funkcje, aby zezwolić na nawiązywanie obszaru roboczego analizy dzienników i rozsyłanie za pomocą serwera proxy. Jeśli używasz agenta programu Operations Manager, musi być celem jako agent pakietu OMS do komunikowania się z analizy dzienników. Agenci programu Operations Manager w tej tabeli są OMS agentów, które są połączone z programem Operations Manager. Zobacz [połączenie programu Operations Manager do analizy dzienników](log-analytics-om-agents.md) informacji o podłączaniu istniejące środowisko programu Operations Manager do analizy dzienników.

> [!NOTE]
> Typ agenta, którego używasz Określa, jak dane są wysyłane do analizy dzienników z następujących warunków:
> - Możesz użyć agenta systemu Windows lub dołączone do programu Operations Manager agent pakietu OMS.
> - Gdy wymagana jest programu Operations Manager, danych agenta programu Operations Manager dla rozwiązania są zawsze wysyłane do analizy dzienników przy użyciu grupy zarządzania programu Operations Manager. Ponadto jeśli programu Operations Manager jest wymagane, tylko agenta programu Operations Manager jest używany przez rozwiązanie.
> - Gdy programu Operations Manager nie jest wymagane, a tabela pokazuje, że dane agenta są wysyłane do analizy dzienników programu Operations Manager przy użyciu grupy zarządzania, a następnie programu Operations Manager agent dane są zawsze przesyłane do analizy dzienników przy użyciu grup zarządzania. Agentów systemu Windows obejścia grupy zarządzania i przekazuj dane bezpośrednio do analizy dzienników.
> - Gdy danych agenta programu Operations Manager nie są wysyłane przy użyciu grupy zarządzania, następnie dane są wysyłane bezpośrednio do analizy dzienników — pomijanie grupy zarządzania.

### <a name="insight--analytics--log-analytics"></a>Szczegółowe informacje o & Analytics / Log Analytics

| Rozwiązanie do zarządzania | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Activity Log Analytics | Azure |   |   |   |   |   | na powiadomienia |
| Ocena usługi AD |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dni |
| Stan replikacji usługi AD |Windows |&#8226; |&#8226; |  |  |&#8226; |5 dni |
| Kondycja agenta | System Windows i Linux | &#8226; | &#8226; |   |   | &#8226; | 1 min |
| Zarządzania alertami (Nagios) |Linux |&#8226; |  |  |  |  |po przybyciu |
| Zarządzania alertami (Zabbix) |Linux |&#8226; |  |  |  |  |1 min |
| Zarządzania alertami (program Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3 minuty |
| Łącznik usługi Application Insights (wersja zapoznawcza) | Azure |   |   |   |   |   | na powiadomienia |
| Analiza dotycząca usługi Azure Application Gateway | Azure |   |   |   |   |   | na powiadomienia |
| Analiza sieciowej grupy zabezpieczeń platformy Azure | Azure |   |   |   |   |   | na powiadomienia |
| Analiza usługi Azure SQL (wersja zapoznawcza) |Windows |  |  |  |  |  | 10 minut |
| Zarządzanie pojemnością |Windows |&#8226; |&#8226; |  |  |&#8226; |po przybyciu |
| Containers | System Windows i Linux | &#8226; | &#8226; |   |   |   | 3 minuty |
| Key Vault Analytics |Windows |  |  |  |  |  |na powiadomienia |
| Monitor wydajności sieci | Windows | &#8226; | &#8226; |   |   |   | TCP uzgodnienia co 5 sekund, dane wysyłane co 3 minuty |
| Analiza usługi Office 365 (wersja zapoznawcza) |Windows |  |  |  |  |  |na powiadomienia |
| Service Fabric Analytics |Windows |  |  |&#8226; |  |  |5 minut |
| Mapa usługi | System Windows i Linux | &#8226; | &#8226; |   |   |   | 15 sekund |
| Ocena serwera SQL |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dni |
| SurfaceHub |Windows |&#8226; |  |  |  |  |po przybyciu |
| Do oceny programu System Center Operations Manager (wersja zapoznawcza) | Windows | &#8226; | &#8226; |   |   | &#8226; | 7 dni |
| Analiza uaktualnień (wersja zapoznawcza) | Windows | &#8226; |   |   |   |   | 2 dni |
| Monitorowanie VMware (wersja zapoznawcza) | Linux | &#8226; |   |   |   |   | 3 minuty |
| Dane o komunikacji sieciowej |Systemu Windows (2012 R2 / 8.1 lub nowszy) |&#8226; |&#8226; |  |  |  | 1 min |


### <a name="automation--control"></a>Automatyzacja i kontrola

| Rozwiązanie do zarządzania | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Hybrydowy proces roboczy usługi Automation | Windows | &#8226; | &#8226; |   |   |   | Nie dotyczy |
| Śledzenie zmian |Windows |&#8226; |&#8226; |  |  |&#8226; |co godzinę |
| Śledzenie zmian |Linux |&#8226; |  |  |  |  |co godzinę |
| Zarządzanie aktualizacjami | Windows |&#8226; |&#8226; |  |  |&#8226; |co najmniej 2 razy dziennie i 15 minut po zainstalowaniu aktualizacji |

### <a name="security--compliance"></a>Zabezpieczenia i zgodność z przepisami

| Rozwiązanie do zarządzania | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Ocena oprogramowania chroniącego przed złośliwym kodem |Windows |&#8226; |&#8226; |  |  |&#8226; |co godzinę |
| Bezpieczeństwo i inspekcji<sup>1</sup> | System Windows i Linux | częściowe | częściowe | częściowe |   | częściowe | różne |

<sup>1</sup> rozwiązania zabezpieczenia i inspekcji mogą zbierać dzienniki z systemu Windows, programu Operations Manager i systemu Linux. Zobacz [źródeł danych](#data-sources) danych zbierania informacji o:

- Dziennik systemu
- Dzienniki zdarzeń zabezpieczeń systemu Windows
- Dzienniki zapory systemu Windows
- Dzienniki zdarzeń systemu Windows



### <a name="protection--recovery"></a>Ochrona i odzyskiwanie

| Rozwiązanie do zarządzania | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | Azure |   |   |   |   |   | Nie dotyczy |
| Azure Site Recovery | Azure |   |   |   |   |   | Nie dotyczy |


### <a name="data-sources"></a>Źródła danych


| Źródło danych | Platforma | Agent monitorowania firmy Microsoft | Agent programu Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dzienniki aktywności platformy Azure |Windows |  |  |  |  |  |na powiadomienia |
| Dzienniki diagnostyczne platformy Azure |Windows |  |  |  |  |  |na powiadomienia |
| Metryki diagnostycznych platformy Azure |Windows |  |  |  |  |  |na powiadomienia |
| ETW |Windows |  |  |&#8226; |  |  |5 minut |
| Dzienniki usług IIS |Windows |&#8226; |&#8226; |&#8226; |  |  |5 minut |
| Liczniki wydajności |Windows |&#8226; |&#8226; |  |  |  |jako zaplanowane, co najmniej 10 sekund |
| Liczniki wydajności |Linux |&#8226; |  |  |  |  |jako zaplanowane, co najmniej 10 sekund |
| Dziennik systemu |Linux |&#8226; |  |  |  |  |z usługi Azure storage: 10 minut; od agenta: Przy nadejściu |
| Dzienniki zdarzeń zabezpieczeń systemu Windows |Windows |&#8226; |&#8226; |&#8226; |  |  |dla usługi Azure storage: 10 min; dla agenta: Przy nadejściu |
| Dzienniki zapory systemu Windows |Windows |&#8226; |&#8226; |  |  |  |po przybyciu |
| Dzienniki zdarzeń systemu Windows |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |dla usługi Azure storage: 10 min; dla agenta: Przy nadejściu |



## <a name="preview-management-solutions-and-features"></a>Podgląd rozwiązania do zarządzania i funkcje
Uruchamiając usługi i wskazówek devops, możemy partnera z klientami w celu opracowywania funkcji i rozwiązań.

Prywatnej wersji zapoznawczej firma Microsoft może dla niewielkiej liczby klientów dostępu early implementacji funkcji lub rozwiązanie w celu uzyskania opinii i poprawiają. Ta implementacja wczesne ma minimalne funkcje i możliwości operacyjnych.

Naszym celem jest szybko spróbuj rzeczy, więc można odnaleźć co działa, a które nie działają. Firma Microsoft Iterowanie za pomocą tego procesu, dopóki opinie od klientów prywatnej wersji zapoznawczej poinformuje, że jesteśmy gotowi publicznej wersji zapoznawczej.

W publicznej wersji zapoznawczej możemy udostępnić funkcji lub rozwiązania dla wszystkich użytkowników, aby uzyskać więcej opinii i zweryfikować naszych skalowania i wydajność. W tej fazie:

* Funkcje w wersji zapoznawczej są wyświetlane na karcie Ustawienia i można ją włączyć przez dowolnego użytkownika.
* Podgląd rozwiązania są dodawane za pomocą galerii lub za pomocą skryptu.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Co należy wiedzieć o funkcje w wersji zapoznawczej i rozwiązania?
Firma Microsoft Cieszymy się o nowych funkcjach i rozwiązań do zarządzania i firma Microsoft chętnie pracy z Tobą w celu ich rozwijania.

Funkcje w wersji zapoznawczej i rozwiązania nie są odpowiednie dla wszystkich. Przed pytaniem przyłączyć prywatnej wersji zapoznawczej lub włączenie publicznej wersji zapoznawczej, upewnij się, że OK pracy dzięki czemuś, co jest w fazie projektowania.

Podczas włączania funkcji testowania za pośrednictwem portalu, jest wyświetlane ostrzeżenie przypominający o jest funkcja w wersji zapoznawczej.

#### <a name="for-both-private-and-public-preview"></a>Dla obu *prywatnej* i *publicznego* podglądu
Poniższe informacje dotyczą podglądy publicznymi i prywatnymi:

* Czynności może nie zawsze działać poprawnie.
  * Problemy z zakresu od są niewielkie określenia dokuczliwości za pośrednictwem na coś nie działa na wszystkich.
* Istnieje możliwość skorzystania z wersji zapoznawczej mieć negatywny wpływ na systemy / środowiska.
  * Firma Microsoft należy unikać ujemna czynności wykonywane systemów używasz, ale czasami nieoczekiwany wydarzenia.
* Utrata danych / może spowodować uszkodzenie.
* Firma Microsoft może poprosić o zbierania dzienników diagnostycznych lub innych danych, aby pomóc w rozwiązywaniu problemów.
* Funkcja lub rozwiązania mogą zostać usunięte (tymczasowo lub na stałe).
  * Oparte na naszych learnings w wersji zapoznawczej firma Microsoft może zdecydować o zwalnia funkcji lub rozwiązania.
* Podgląd może nie działać lub może nie przetestowano we wszystkich konfiguracjach i firma Microsoft może ograniczyć:
  * Systemy operacyjne, które mogą być używane (na przykład funkcja może dotyczą tylko Linux znajduje się w wersji zapoznawczej).
  * Typ agent (MMA, Operations Manager), który może służyć (na przykład funkcja może nie działać z programem Operations Manager znajduje się w wersji zapoznawczej).  
* Podgląd rozwiązań i funkcje nie są objęte umową dotyczącą poziomu usług.
* Opłaty za użycie wiąże się z użycia funkcji w wersji zapoznawczej.
* Funkcje lub możliwości, że wymagane dla funkcji / rozwiązanie, aby być przydatne mogą być brakujące lub niekompletne.
* Funkcje / rozwiązania nie mogą być dostępne we wszystkich regionach.
* Funkcje / rozwiązania nie może być lokalizowany.
* Funkcje / rozwiązania mają limit na liczbę klientów lub urządzeń, które może być używany.
* Może być konieczne do przeprowadzenia konfiguracji i rozwiązaniu/funkcję za pomocą skryptów.
* Interfejs użytkownika (UI) jest niekompletne i mogą ulec zmianie z dnia na dzień.
* Podglądy publiczny nie może być odpowiednie dla produkcyjnego / krytyczne systemów.

#### <a name="for-private-preview"></a>Aby uzyskać *prywatnej* podglądu
Oprócz powyższych elementów specyficznych dla prywatnych podglądy są następujące informacje:

* Oczekujemy aby zapewnić nam opinii na temat środowiska, aby firma Microsoft może ulepszać funkcji/rozwiązania.
* Firma Microsoft może skontaktować się z Tobą opinii przy użyciu ankietach, połączenia telefoniczne lub wiadomości e-mail.
* Elementy zawsze nie działa prawidłowo.
* Firma Microsoft może wymagać Non-ujawnienie umowy (NDA) udziału lub mogą obejmować poufnej zawartości.
  * Przed przystąpieniem do obsługi blogów, tweeting lub inny sposób komunikowania się z innym firmom Sprawdź, czy przy użyciu programu Menedżera odpowiedzialną za Podgląd, aby poznać wszystkie ograniczenia na ujawnienie.
* Nie należy uruchamiać w środowisku produkcyjnym / krytyczne systemów.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Jak uzyskać dostęp do funkcji prywatnej wersji zapoznawczej i rozwiązań?
Zapraszamy klientom prywatnej podglądy przez kilka różnych sposobów, w zależności od wersji zapoznawczej.

* Udzielenie odpowiedzi na miesięczne Ankieta klienta i udzielenie nam uprawnień monitowanie o zwiększa szanse trwa zaproszenie do prywatnej wersji zapoznawczej.
* Zespół obsługi klienta firmy Microsoft można wyznaczyć użytkownik.
* Możesz utworzyć konto elementy opublikowane w serwisie twitter [msopsmgmt](https://twitter.com/msopsmgmt).
* Możesz utworzyć konto na podstawie zdarzeń udostępnionego społeczności szczegóły — przyjrzeć się firmie Microsoft spełniają ups konferencji w społeczności online.

## <a name="next-steps"></a>Kolejne kroki
* [Wyszukaj dzienniki](log-analytics-log-searches.md) do wyświetlenia szczegółowych informacji zbieranych przez rozwiązania do zarządzania.
