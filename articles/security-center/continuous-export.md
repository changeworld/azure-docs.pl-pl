---
title: Eksportuj alerty Azure Security Center i zalecenia do rozwiązań Siem | Microsoft Docs
description: W tym artykule wyjaśniono, jak skonfigurować ciągły eksport alertów zabezpieczeń i zaleceń do rozwiązań Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cd26ed446ce676bcec85d8e413d3ec37ac236869
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521997"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Eksportowanie alertów zabezpieczeń i zaleceń (wersja zapoznawcza)

Azure Security Center generuje szczegółowe alerty zabezpieczeń i zalecenia. Można je wyświetlić w portalu lub za pomocą narzędzi programistycznych. Może być również konieczne wyeksportowanie tych informacji lub wysłanie ich do innych narzędzi do monitorowania w danym środowisku. 

W tym artykule opisano zestaw narzędzi (wersja zapoznawcza) służących do eksportowania alertów i zaleceń ręcznie lub w stały sposób ciągły.

Za pomocą tych narzędzi możesz:

* Generuj szczegółowe raporty jako woluminy CSV
* Eksportuj do Log Analytics obszarów roboczych
* Eksportuj do Event Hubs platformy Azure (w przypadku integracji z usługą rozwiązań Siem innej firmy)

## <a name="setting-up-a-continuous-export"></a>Konfigurowanie eksportu ciągłego

1. Na pasku bocznym Security Center kliknij pozycję **cennik & Ustawienia**.

1. Wybierz określoną subskrypcję, dla której chcesz skonfigurować eksportowanie danych.
    
1. Na pasku bocznym strony Ustawienia dla tej subskrypcji wybierz pozycję **eksport ciągły (wersja zapoznawcza)** .

    [Opcje eksportu![w Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) W tym miejscu są wyświetlane opcje eksportowania. Dla każdego dostępnego elementu docelowego eksportu istnieje karta. 

1. Wybierz typ danych, który chcesz wyeksportować, i wybierz spośród filtrów dla każdego typu (na przykład wyeksportuj tylko alerty o wysokiej ważności).

1. W obszarze "Eksportuj element docelowy" Wybierz miejsce, w którym chcesz zapisać dane. Dane można zapisywać w miejscu docelowym w innej subskrypcji (na przykład w centralnym wystąpieniu centrum zdarzeń lub w centralnym obszarze roboczym Log Analytics).

1. Kliknij pozycję **Zapisz**.

## <a name="continuous-export-through-azure-event-hubs"></a>Eksport ciągły za pomocą usługi Azure Event Hubs  

> [!NOTE]
> Najbardziej efektywną metodą przesyłania strumieniowego danych monitorowania do zewnętrznych narzędzi w większości przypadków jest użycie usługi Azure Event Hubs. [Ten artykuł](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) zawiera krótki opis sposobu przesyłania strumieniowego danych monitorowania z różnych źródeł do centrum zdarzeń oraz linki do szczegółowych wskazówek.

> [!NOTE]
> Jeśli wcześniej wyeksportowano Security Center alerty do SIEM przy użyciu dziennika aktywności platformy Azure, Poniższa procedura zastępuje tę metodologię.

### <a name="to-integrate-with-a-siem"></a>Aby zintegrować z usługą SIEM 

Po skonfigurowaniu ciągłego eksportowania wybranych Security Center danych do usługi Azure Event Hubs można skonfigurować odpowiedni łącznik na SIEM, postępując zgodnie z poniższymi instrukcjami.

Postępuj zgodnie z instrukcjami dotyczącymi Twojego SIEM na [tej stronie](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) i użyj odpowiedniego łącznika:

* **Splunk** — użyj [dodatku Azure monitor dla Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar** — Użyj [ręcznie skonfigurowanego źródła dziennika](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** — Użyj [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Jeśli używasz funkcji **wskaźnikowej platformy Azure**, Użyj dostępnego w tym celu [łącznika](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) Azure Security Center natywnych alertów.

Ponadto jeśli chcesz automatycznie przenieść ciągłe wyeksportowane dane ze skonfigurowanego centrum zdarzeń na platformę Azure Eksplorator danych, Skorzystaj z instrukcji w temacie pozyskiwanie [danych z centrum zdarzeń w usłudze azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-log-analytics-workspace"></a>Eksport ciągły do obszaru roboczego Log Analytics

Aby wyeksportować do obszaru roboczego Log Analytics, musisz mieć Security Centere warstwy Bezpłatna lub standardowa Log Analytics dostępne w obszarze roboczym. Jeśli używasz Azure Portal, rozwiązanie Security Center warstwy Bezpłatna jest automatycznie włączane po włączeniu eksportu ciągłego. Jeśli jednak konfigurujesz ustawienia eksportu ciągłego programowo, musisz ręcznie wybrać warstwę cenową dla wymaganego obszaru roboczego z poziomu **ustawień & cenowych**.  

Alerty zabezpieczeń i zalecenia są przechowywane odpowiednio w tabelach *SecurityAlert* i *SecurityRecommendations* . Nazwa rozwiązania Log Analytics zawierającego te tabele zależy od tego, czy korzystasz z warstwy Bezpłatna, czy standardowa (zobacz [Cennik](security-center-pricing.md)): Security lub SecurityCenterFree.

![Tabela * SecurityAlert * w Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

## <a name="manual-one-time-export-of-security-alerts"></a>Ręczne eksportowanie alertów zabezpieczeń jednorazowe

Aby pobrać raport CSV dotyczący alertów lub zaleceń, Otwórz stronę **alerty zabezpieczeń** lub **zalecenia** , a następnie kliknij przycisk **Pobierz raport w formacie CSV (wersja zapoznawcza)** .

[![pobierać dane alertów jako plik CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Te raporty zawierają alerty i zalecenia dotyczące zasobów z aktualnie wybranych subskrypcji w filtrze katalogów i subskrypcji w witrynie Azure Portal: ![filtr wybierania katalogu i subskrypcji](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania ciągłego eksportowania zaleceń i alertów. Wiesz również, jak pobrać dane alertów jako plik CSV. 

W przypadku pokrewnego materiału zapoznaj się z następującą dokumentacją: 

- [Dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentacja usługi Azure wskaźnikowego](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)