---
title: Eksportowanie alertów i zaleceń usługi Azure Security Center do SIEM | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak skonfigurować ciągły eksport alertów i zaleceń dotyczących zabezpieczeń do SIEM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158977"
---
# <a name="export-security-alerts-and-recommendations"></a>Eksportowanie alertów zabezpieczeń i zaleceń

Usługa Azure Security Center generuje szczegółowe alerty i zalecenia dotyczące zabezpieczeń. Można je wyświetlić w portalu lub za pomocą narzędzi programowych. Może być również konieczne wyeksportowanie tych informacji lub wysłanie ich do innych narzędzi monitorowania w twoim środowisku. 

W tym artykule opisano zestaw narzędzi, które umożliwiają eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły.

Za pomocą tych narzędzi można:

* Ciągłe eksportowanie do obszarów roboczych usługi Log Analytics
* Ciągłe eksportowanie do usługi Azure Event Hubs (w celu integracji z usługami SIEM innych firm)
* Eksport do pliku CSV (jednorazowo)


## <a name="setting-up-a-continuous-export"></a>Konfigurowanie ciągłego eksportu

Poniższe kroki są niezbędne, niezależnie od tego, czy konfigurujesz ciągły eksport do obszaru roboczego usługi Log Analytics, czy usługi Azure Event Hubs.

1. Na pasku bocznym centrum zabezpieczeń wybierz pozycję **Ustawienia & cennika**.

1. Wybierz określoną subskrypcję, dla której chcesz skonfigurować eksport danych.
    
1. Na pasku bocznym strony ustawień dla tej subskrypcji wybierz pozycję **Eksport ciągły**.

    [Opcje eksportowania w usłudze Azure Security Center ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) W tym miejscu zobaczysz opcje eksportu. Istnieje karta dla każdego dostępnego celu eksportu. 

1. Wybierz typ danych, który chcesz wyeksportować, i wybierz jeden z filtrów dla każdego typu (na przykład eksportuj tylko alerty o wysokiej ważności).

1. W obszarze "Eksportuj cel" wybierz miejsce, w którym chcesz zapisać dane. Dane można zapisywać w docelowych w innej subskrypcji (na przykład w wystąpieniu centrum zdarzeń centralnej lub centralnym obszarze roboczym usługi Log Analytics).

1. Kliknij przycisk **Zapisz**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Konfigurowanie integracji SIEM za pośrednictwem usługi Azure Event Hubs

Usługa Azure Event Hubs to doskonałe rozwiązanie do programowego korzystania z dowolnych danych przesyłania strumieniowego. W przypadku alertów i zaleceń usługi Azure Security Center jest to preferowany sposób integracji z usługą SIEM innej firmy.

> [!NOTE]
> Najbardziej skuteczną metodą przesyłania strumieniowego danych monitorowania do narzędzi zewnętrznych w większości przypadków jest użycie usługi Azure Event Hubs. [Ten artykuł](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) zawiera krótki opis sposobu przesyłania strumieniowego danych monitorowania z różnych źródeł do Centrum zdarzeń i łączy się ze szczegółowymi wskazówkami.

> [!NOTE]
> Jeśli wcześniej wyeksportowane alerty usługi Security Center do SIEM przy użyciu dziennika aktywności platformy Azure, poniższa procedura zastępuje tę metodologię.

Aby wyświetlić schematy zdarzeń eksportowanych typów danych, odwiedź [schematy zdarzeń Centrum zdarzeń](https://aka.ms/ASCAutomationSchemas)Centrum zdarzeń .


### <a name="to-integrate-with-a-siem"></a>Aby zintegrować się z SIEM 

Po skonfigurowaniu ciągłego eksportowania wybranych danych usługi Security Center do usługi Azure Event Hubs można skonfigurować odpowiedni łącznik dla urządzenia SIEM:

* **Usługa Azure Sentinel** — użyj oferowanego tam natywnego [łącznika danych](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) usługi Azure Security Center alertów.
* **Splunk** - Użyj dodatku [Azure Monitor dla Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** — używanie [ręcznie skonfigurowanego źródła dziennika](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – użyj [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Ponadto jeśli chcesz automatycznie przenieść stale eksportowane dane ze skonfigurowanego Centrum zdarzeń do Usługi Azure Data Explorer, użyj instrukcji w [ujmowaniu danych z Usługi Event Hub do Usługi Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Ciągłe eksportowanie do obszaru roboczego usługi Log Analytics

Jeśli chcesz analizować dane usługi Azure Security Center w obszarze roboczym usługi Log Analytics lub używać alertów platformy Azure razem z Usługą Security Center, skonfiguruj ciągłe eksportowanie do obszaru roboczego usługi Log Analytics.

Aby eksportować do obszaru roboczego usługi Log Analytics, w obszarze roboczym musi być włączone rozwiązania analizy dzienników usługi Security Center. Jeśli używasz witryny Azure portal, rozwiązanie bezpłatnej warstwy usługi Security Center jest automatycznie włączane po włączeniu ciągłego eksportu. Jeśli jednak ustawienia ciągłego eksportu są konfigurowane programowo, musisz ręcznie wybrać bezpłatną lub standardową warstwę cenową dla wymaganego obszaru roboczego z **poziomu ustawień & cennika**.  

### <a name="log-analytics-tables-and-schemas"></a>Tabele i schematy analizy dzienników

Alerty zabezpieczeń i zalecenia są przechowywane w *securityalert* i *SecurityRecommendations* tabel odpowiednio. Nazwa rozwiązania usługi Log Analytics zawierającego te tabele zależy od tego, czy korzystasz z warstwy bezpłatnej, czy standardowej (zobacz [cennik):](security-center-pricing.md)Security("Security and Audit") czy SecurityCenterFree.

![Tabela *SecurityAlert* w usłudze Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Aby wyświetlić schematy zdarzeń eksportowanych typów danych, odwiedź [schematy tabeli Analizy dzienników](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Wyświetlanie eksportowanych alertów i zaleceń dotyczących zabezpieczeń w usłudze Azure Monitor

W niektórych przypadkach można wyświetlić eksportowane alerty zabezpieczeń i/lub zalecenia w [usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Usługa Azure Monitor zapewnia ujednolicone środowisko alertów dla różnych alertów platformy Azure, w tym dziennika diagnostycznego, alertów metryk i alertów niestandardowych opartych na zapytaniach obszaru roboczego usługi Log Analytics.

Aby wyświetlić alerty i zalecenia z Usługi Security Center w usłudze Azure Monitor, skonfiguruj regułę alertu opartą na kwerendach usługi Log Analytics (alert dziennika):

1. Na stronie **Alerty** usługi Azure Monitor kliknij pozycję **Nowa reguła alertów**.

    ![Strona alertów usługi Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na stronie reguły tworzenia skonfiguruj nową regułę (w taki sam sposób, w jaki skonfigurujesz [regułę alertu dziennika w usłudze Azure Monitor):](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

    * W obszarze **Zasób**wybierz obszar roboczy usługi Log Analytics, do którego eksportowano alerty i zalecenia dotyczące zabezpieczeń.

    * W polu **Warunek**wybierz **pozycję Niestandardowe wyszukiwanie dzienników**. Na wyświetlona strona konfigurować kwerendę, okres odnośny i okres częstotliwości. W kwerendzie wyszukiwania można wpisać *securityalert* lub *SecurityRecommendation,* aby wysyłać zapytania do typów danych, do których usługa Security Center stale eksportuje, włączana jako funkcja Ciągłego eksportowania do usługi Log Analytics. 
    
    * Opcjonalnie skonfiguruj [grupę akcji,](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) którą chcesz wyzwolić. Grupy akcji mogą wyzwalać wysyłanie wiadomości e-mail, bilety ITSM, czaki internetowe i inne.
    ![Reguła alertu usługi Azure Monitor](./media/continuous-export/azure-monitor-alert-rule.png)

Nowe alerty lub zalecenia usługi Azure Security Center (w zależności od konfiguracji) będą teraz widoczne w alertach usługi Azure Monitor z automatycznym wyzwalaniem grupy akcji (jeśli są podane).

## <a name="manual-one-time-export-of-security-alerts"></a>Ręczny jednorazowy eksport alertów zabezpieczeń

Aby pobrać raport CSV dla alertów lub zaleceń, otwórz stronę **Alerty** lub **zalecenia zabezpieczeń** i kliknij przycisk **Pobierz raport CSV.**

[![Pobieranie alertów danych jako pliku CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Raporty te zawierają alerty i zalecenia dotyczące zasobów z aktualnie wybranych subskrypcji.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak skonfigurować ciągły eksport zaleceń i alertów. Dowiesz się również, jak pobrać dane alertów jako plik CSV. 

Materiały pokrewne można znaleźć w następującej dokumentacji: 

- [Dokumentacja usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Dokumentacja usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Dokumentacja usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Schematy automatyzacji przepływu pracy i ciągłego eksportowania typów danych](https://aka.ms/ASCAutomationSchemas)
