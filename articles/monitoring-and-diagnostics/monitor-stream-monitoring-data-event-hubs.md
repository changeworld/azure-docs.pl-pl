---
title: Strumienia Azure monitorowania danych do usługi Event Hubs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wszystkie dane monitorowania Azure do Centrum zdarzeń można pobrać danych do partnera SIEM lub narzędzia do analizy strumienia.
author: johnkemnetz
manager: robb
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/05/2018
ms.author: johnkem
ms.openlocfilehash: 9cc4eb8d8f1494a7ea7a63297751f8e251aedf05
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Azure strumienia danych do Centrum zdarzeń do użycia przez monitorowania przez narzędzie zewnętrzne

Azure Monitor udostępnia pojedynczy potok dostęp do wszystkich dane monitorowania z Twoim środowiskiem platformy Azure, dzięki któremu można łatwo zdefiniować partnera SIEM i narzędzi, aby użyć danych monitorowania. Ten artykuł przeprowadzi Cię przez ustawienie różnych warstw danych z Twoim środowiskiem platformy Azure do wysłania do jednej centra zdarzeń w przestrzeni nazw lub zdarzenia koncentratora, gdzie mogą być zbierane przez narzędzie zewnętrzne.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Jakie dane może wysłać do Centrum zdarzeń? 

W środowisku platformy Azure istnieje kilka "warstwy" danych monitorowania i metody dostępu do danych w poszczególnych warstwach różni się nieco. Zazwyczaj te warstwy można przedstawić jako:

- **Monitorowanie danych aplikacji:** dane dotyczące wydajności i funkcji kodu zostały zapisane i działają na platformie Azure. Przykłady aplikacji danych monitorowania śledzenia wydajności, dzienniki aplikacji i danych telemetrycznych użytkownika. Zazwyczaj zbieranych danych monitorowania aplikacji w jednym z następujących sposobów:
  - Instrumentując kod przy użyciu zestawu SDK, takich jak [zestaw SDK usługi Application Insights](../application-insights/app-insights-overview.md).
  - Uruchamiając agenta monitorowania, która nasłuchuje dzienniki nowej aplikacji na maszynie uruchamiania aplikacji, takich jak [diagnostycznych agenta usługi Windows Azure](./azure-diagnostics.md) lub [agenta diagnostyki Azure Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Dane monitorowania systemu operacyjnego gościa:** dane dotyczące systemu operacyjnego, na którym aplikacja jest uruchomiona. Przykłady danych monitorowania systemu operacyjnego gościa jest systemu Linux lub zdarzenia systemu Windows. Aby zbierać dane tego typu, należy zainstalować agenta, takich jak [diagnostycznych agenta usługi Windows Azure](./azure-diagnostics.md) lub [agenta diagnostyki Azure Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Dane monitorowania zasobów platformy Azure:** danych o działaniu zasobów platformy Azure. W przypadku niektórych typów zasobów platformy Azure, takich jak maszyny wirtualne jest system operacyjny gościa i aplikacji do monitorowania wewnątrz tej usługi Azure. Dla innych zasobów platformy Azure, takich jak grupy zabezpieczeń sieci zasobów danych monitorowania jest najwyższej warstwy danych (ponieważ nie istnieje, system operacyjny gościa lub aplikacja była uruchomiona w tych zasobów). Te dane mogą być zbierane przy użyciu [ustawień diagnostycznych zasobu](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Monitorowanie danych platformy Azure:** dane dotyczące operacji i Zarządzanie subskrypcją platformy Azure lub dzierżawy, a także dane dotyczące wydajności i operacji Azure samej siebie. [Dziennik aktywności](./monitoring-overview-activity-logs.md), m.in. dane usługi kondycji i usługi Active Directory inspekcje są przykłady platformy danych monitorowania. Te dane mogą być zbierane przy użyciu także ustawienia diagnostyki.

Dane z dowolnej warstwy mogą być wysyłane do Centrum zdarzeń, gdzie mogą być pobierane do narzędzia partnera. Kolejne sekcje opisują sposób dane z każdej warstwy strumieniowe przesyłanie do Centrum zdarzeń można skonfigurować. W krokach założono już mieć zasobów w danej warstwie do monitorowania.

## <a name="set-up-an-event-hubs-namespace"></a>Konfigurowanie przestrzeni nazw usługi Event Hubs

Przed rozpoczęciem należy [tworzenia Centrum centra zdarzeń w przestrzeni nazw i zdarzenia](../event-hubs/event-hubs-create.md). Ta przestrzeń nazw i zdarzeń Centrum jest miejscem docelowym dla wszystkich danych monitorowania. Przestrzeń nazw usługi Event Hubs to logiczne grupowanie centra zdarzeń, które używają tych samych zasad dostępu, podobnie jak magazynu konto ma poszczególne obiekty BLOB w ramach tego konta magazynu. Należy pamiętać, kilka szczegóły dotyczące nazw centra zdarzeń i centra zdarzeń, które możesz utworzyć:
* Firma Microsoft zaleca używanie standardowych centra zdarzeń w przestrzeni nazw.
* Zazwyczaj konieczne jest tylko jedną jednostkę przepływności. Aby skalować w miarę wzrostu użycia z dziennika należy zawsze ręcznie zwiększyć liczbę jednostek przepływności dla przestrzeni nazw później lub Włącz automatyczne inflacji.
* Liczba jednostek przepływności pozwala zwiększyć przepływność skali centrów zdarzeń. Liczba partycji umożliwia parallelize zużycie przez wielu użytkowników. Możliwość jednej partycji do 20MBps lub około 20 000 komunikatów na sekundę. W zależności od narzędzia konsumowania danych może lub nie obsługuje korzystanie z wielu partycji. Jeśli nie masz pewności, o liczbie partycji można ustawić, zalecamy Rozpoczynanie od cztery partycje.
* Zalecane ustawienia przechowywania komunikatów w Centrum zdarzeń do 7 dni. Jeśli narzędzie odbierającą przestanie działać na więcej niż jeden dzień, zapewnia to narzędzie można odebrania miejsca, w którym (zdarzeń maksymalnie 7 dni).
* Zalecamy używanie domyślna grupa odbiorców Centrum zdarzeń. Nie istnieje potrzeba do tworzenia innych grup odbiorców lub użyj grupy oddzielne klientów, chyba że zostaną umieszczone dwóch różnych narzędzi, korzystać z tych samych danych z tego samego Centrum zdarzeń.
* Dla dziennika aktywności platformy Azure wybierz przestrzeń nazw usługi Event Hubs i Azure Monitor tworzy Centrum zdarzeń w tej przestrzeni nazw o nazwie "insights dzienniki operationallogs." Dla innych typów dziennika można wybrać istniejący Centrum zdarzeń (dzięki czemu można ponownie użyć tego samego Centrum zdarzeń insights — dzienniki operationallogs) lub Azure monitorów tworzenia Centrum zdarzeń według kategorii dziennika.
* Zazwyczaj należy otworzyć port 5671 i 5672 na maszynie konsumowania danych z Centrum zdarzeń.

Można również znaleźć [często zadawane pytania dotyczące usługi Azure zdarzeń koncentratory](../event-hubs/event-hubs-faq.md).

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Sposób konfigurowania platformy Azure danych monitorowania strumieniowe przesyłanie do Centrum zdarzeń

Monitorowanie danych platformy Azure pochodzi z dwóch głównych źródeł:
1. [Dziennik aktywności platformy Azure](./monitoring-overview-activity-logs.md), który zawiera tworzenia, aktualizowania i usuwania operacji z Menedżera zasobów, zmiany w [kondycji usługi Azure](../service-health/service-health-overview.md) który może mieć wpływ na zasoby w ramach subskrypcji, [kondycja zasobów](../service-health/resource-health-overview.md) przejścia stanu i kilka innych typów zdarzenia na poziomie subskrypcji. [W tym artykule szczegółowo wszystkich rodzajów zdarzeń, które pojawiają się w dzienniku aktywności Azure](./monitoring-activity-log-schema.md).
2. [Raportowanie usługi Active Directory na platformie Azure](../active-directory/active-directory-reporting-azure-portal.md), który zawiera historię śladu logowania działań i inspekcji zmian wprowadzonych w określonym dzierżawcy. Go nie jest jeszcze możliwe do transmisji danych usługi Azure Active Directory do Centrum zdarzeń.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Strumień danych dziennika aktywności platformy Azure do Centrum zdarzeń

Aby wysyłanie danych z dziennika aktywności platformy Azure w przestrzeni nazw usługi Event Hubs, należy skonfigurować profil dziennika w ramach subskrypcji. [W tym przewodniku wykonaj](./monitoring-stream-activity-logs-event-hubs.md) do skonfigurowania profilu dziennika w ramach subskrypcji. W tym raz na subskrypcję, który chcesz monitorować.

> [!TIP]
> Profil dziennika obecnie tylko służy do wybierania przestrzeni nazw usługi Event Hubs, w której utworzono Centrum zdarzeń z nazwy "insights operacyjne dziennikami." Go nie jest jeszcze można określić w profilu dziennika z własnej nazwy Centrum zdarzeń.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Sposób konfigurowania danych monitorowania zasobów platformy Azure strumieniowe przesyłanie do Centrum zdarzeń

Zasoby platformy Azure Emituj dwa rodzaje danych monitorowania:
1. [Dzienniki diagnostyczne zasobu](./monitoring-overview-of-diagnostic-logs.md)
2. [Metryki](monitoring-overview-metrics.md)

Oba typy danych są wysyłane do Centrum zdarzeń za pomocą ustawienia diagnostyczne zasobu. [W tym przewodniku wykonaj](./monitoring-stream-diagnostic-logs-to-event-hubs.md) Aby skonfigurować ustawienia diagnostyczne zasobu w określonego zasobu. Ustaw zasób ustawienie diagnostyczne dla każdego zasobu, z którego mają być zbierane dzienników.

> [!TIP]
> Zasady usługi Azure umożliwia upewnij się, że każdy zasób w zakresie niektórych zawsze skonfigurowano ustawienie diagnostyczne [przy użyciu efekt DeployIfNotExists w regule zasad](../azure-policy/policy-definition.md#policy-rule). Dzisiaj DeployIfNotExists jest obsługiwana tylko na wbudowanych zasad.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Jak skonfigurować dane monitorowania systemu operacyjnego gościa do być przesłana strumieniowo do Centrum zdarzeń?

Musisz zainstalować agenta w celu wysyłania danych monitorowania systemu operacyjnego gościa do Centrum zdarzeń. Dla systemu Windows lub Linux należy określić dane, które mają być wysyłane do Centrum zdarzeń, a także Centrum zdarzeń, do którego dane mają być wysyłane w pliku konfiguracji i przekazać plik konfiguracji do agenta uruchomionego na maszynie Wirtualnej.

### <a name="stream-linux-data-to-an-event-hub"></a>Strumień danych Linux do Centrum zdarzeń

[Agenta diagnostyki Azure Linux](../virtual-machines/extensions/diagnostics-linux.md) może służyć do wysyłania danych z maszyny z systemem Linux do Centrum zdarzeń monitorowania. W tym przez dodanie Centrum zdarzeń jako zbiornika w Twojej LAD ustawienia konfiguracji pliku chronionego JSON. [Zobacz ten artykuł, aby dowiedzieć się więcej na temat dodawania wychwytywania Centrum zdarzeń agentowi Linux Azure diagnostycznych](../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Nie można skonfigurować przesyłanie strumieniowe danych monitorowania systemu operacyjnego gościa do Centrum zdarzeń w portalu. Zamiast tego należy ręcznie edytować plik konfiguracji.

### <a name="stream-windows-data-to-an-event-hub"></a>Strumień danych systemu Windows do Centrum zdarzeń

[Agenta Windows Azure diagnostyki](./azure-diagnostics.md) może służyć do wysyłania danych z komputera z systemem Windows do Centrum zdarzeń monitorowania. W tym przez dodanie Centrum zdarzeń jako zbiornika w sekcji privateConfig WAD pliku konfiguracji. [Zobacz ten artykuł, aby dowiedzieć się więcej na temat dodawania wychwytywania Centrum zdarzeń agentowi diagnostyki w systemie Windows Azure](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Nie można skonfigurować przesyłanie strumieniowe danych monitorowania systemu operacyjnego gościa do Centrum zdarzeń w portalu. Zamiast tego należy ręcznie edytować plik konfiguracji.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Sposób konfigurowania aplikacji monitorowania dane przesyłane strumieniowo do Centrum zdarzeń

Monitorowanie danych aplikacji wymaga, że kodu został zinstrumentowany przy użyciu zestawu SDK, więc nie ma rozwiązanie ogólnego przeznaczenia do routingu monitorowania danych do Centrum zdarzeń na platformie Azure. Jednak [Azure Application Insights](../application-insights/app-insights-overview.md) jest jedną usługę, która może służyć do zbierania danych z poziomu aplikacji Azure. Jeśli korzystasz z usługi Application Insights, można przesyłać strumieniowo dane monitorowania do Centrum zdarzeń, w następujący sposób:

1. [Konfigurowanie Eksport ciągły](../application-insights/app-insights-export-telemetry.md) danych usługi Application Insights do konta magazynu.

2. Konfigurowanie aplikacji logiki wyzwalany przez czasomierz który [pobiera dane z magazynu obiektów blob](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) i [wypchnięcia jej jako wiadomości do Centrum zdarzeń](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Co mogę zrobić przy użyciu monitorowania danych są wysyłane do mojego Centrum zdarzeń

Routing monitorowanych danych do Centrum zdarzeń z monitorem Azure pozwala łatwo zintegrować z partnerem SIEM i narzędzi do monitorowania. Większość narzędzi wymagają parametry połączenia Centrum zdarzeń i pewnych uprawnień do subskrypcji platformy Azure można odczytać danych z Centrum zdarzeń. W tym miejscu jest niepełna lista narzędzi z integracją Azure Monitor:

* **IBM QRadar** -Microsoft Azure DSM i Protokół Centrum zdarzeń Azure firmy Microsoft są dostępne do pobrania z [witrynie pomocy technicznej IBM](http://www.ibm.com/support). Możesz [Dowiedz się więcej o integracji z platformy Azure, w tym miejscu](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** — w zależności od ustawień Splunk, istnieją dwie metody:
    1. [Dodatek Monitor Azure dla Splunk](https://splunkbase.splunk.com/app/3534/) jest dostępna w Splunkbase i projekt typu open source. [Dokumentacja jest tutaj](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Jeśli nie można zainstalować dodatkowe wystąpienia Splunk (np. Jeśli przy użyciu serwera proxy lub w chmurze Splunk uruchomiona), może przekazywać te zdarzenia do modułu zbierającego zdarzeń HTTP Splunk przy użyciu [tej funkcji, które jest uruchamiane przez nowe komunikaty w Centrum zdarzeń](https://github.com/sebastus/AzureFunctionForSplunkVS).
* **SumoLogic** — instrukcje dotyczące konfigurowania SumoLogic do pracy z danymi z Centrum zdarzeń są [dostępne tutaj](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>Następne kroki
* [Archiwizowanie dziennika aktywności na koncie magazynu](monitoring-archive-activity-log.md)
* [Zapoznanie się z omówieniem dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md)
* [Konfigurowanie alertu na podstawie zdarzenia dziennika aktywności](insights-auditlog-to-webhook-email.md)

