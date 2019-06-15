---
title: Stream danych monitorowania platformy Azure do usługi Event Hubs
description: Dowiedz się, jak przesyłać strumieniowo dane monitorowania platformy Azure do Centrum zdarzeń można pobrać danych do rozwiązania SIEM partnera lub narzędzia analizy.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: nikiest
ms.subservice: ''
ms.openlocfilehash: 8a4de244d0fa07bfc162625f577015317fca7e6a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069337"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne

W tym artykule przedstawiono konfigurowanie różnych warstw danych ze środowiska platformy Azure do wysłania do jednej usługi Event Hubs przestrzeni nazw lub zdarzenia koncentratora, gdzie mogą być zbierane przez narzędzie zewnętrzne.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Jakie dane może wysłać do Centrum zdarzeń?

W środowisku platformy Azure istnieje kilka "warstwy" danych monitorowania, a metoda uzyskiwania dostępu do danych z każdej warstwy różni się nieco. Zazwyczaj te warstwy można przedstawić jako:

- **Dane monitorowania aplikacji:** Dane dotyczące wydajności i funkcji kodu zostały napisane i działają na platformie Azure. Przykładami aplikacji, danych monitorowania ślady wydajności, dzienniki aplikacji i danych telemetrycznych użytkownika. Monitorowanie danych aplikacji zwykle są zbierane w jednym z następujących sposobów:
  - Instrumentując kodu za pomocą zestawu SDK, takich jak [zestawu SDK usługi Application Insights](../../azure-monitor/app/app-insights-overview.md).
  - Uruchamiając agent monitorowania, który nasłuchuje nowych aplikacji dzienników na maszynie działania aplikacji, takich jak [agenta diagnostyki Azure Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) lub [agenta diagnostyki Azure Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dane monitorowania systemu operacyjnego gościa:** Dane dotyczące systemu operacyjnego, na którym aplikacja jest uruchomiona. Przykładowe dane monitorowania systemu operacyjnego gościa będzie dzienników syslog systemu Linux lub zdarzeń systemu Windows. Aby zbierać dane tego typu, należy zainstalować agenta, takie jak [agenta diagnostyki Azure Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) lub [agenta diagnostyki Azure Linux](../../virtual-machines/extensions/diagnostics-linux.md).
- **Dane monitorowania zasobów platformy Azure:** Dane dotyczące operacji zasobu platformy Azure. W przypadku niektórych typów zasobów platformy Azure, takie jak maszyny wirtualne ma systemu operacyjnego gościa i aplikacji do monitorowania wewnątrz tej usługi platformy Azure. Dla innych zasobów platformy Azure, takich jak sieciowe grupy zabezpieczeń zasobu danych monitorowania jest najwyższej warstwy danych (ponieważ nie ma systemu operacyjnego gościa lub aplikacja działająca w tych zasobach). Te dane można zbierać w programach [ustawień diagnostycznych zasobu](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
- **Subskrypcja platformy Azure, danych monitorowania:** Dane dotyczące operacji i zarządzania subskrypcją platformy Azure, a także dane dotyczące kondycji i działanie systemu Azure sam. [Dziennika aktywności](./../../azure-monitor/platform/activity-logs-overview.md) zawiera większość subskrypcji monitorowania danych, takich jak zdarzenia kondycji usługi i inspekcji usługi Azure Resource Manager. Można zbierać dane przy użyciu profilu dziennika.
- **Monitorowanie danych dzierżawy platformy Azure:** Dane o poziomie dzierżawy usług platformy Azure, takich jak Azure Active Directory. Przeprowadza inspekcję Azure Active Directory i logowania są przykładami dzierżawy danych monitorowania. Te dane mogą być zbierane przy użyciu ustawienia diagnostyczne dzierżawy.

Dane z dowolnej warstwy mogą być wysyłane do Centrum zdarzeń, gdzie mogą być ściągane do narzędzia partnera. Niektóre źródła można skonfigurować do wysyłania danych bezpośrednio do Centrum zdarzeń, podczas gdy inny przetwarzania, takie jak aplikacja logiki może być wymagany do pobrania niezbędnych danych. W kolejnych sekcjach opisano sposób konfigurowania danych z każdej warstwy, aby być przesłana strumieniowo do Centrum zdarzeń. Założono w nim już ma zasoby w tej warstwie do monitorowania.

## <a name="set-up-an-event-hubs-namespace"></a>Konfigurowanie przestrzeni nazw usługi Event Hubs

Przed rozpoczęciem należy [Utwórz koncentrator przestrzeni nazw i zdarzeń usługi Event Hubs](../../event-hubs/event-hubs-create.md). Ta przestrzeń nazw i Centrum zdarzeń jest miejscem docelowym dla wszystkich danych monitorowania. Przestrzeń nazw usługi Event Hubs to logiczna grupa usługi event hubs, które używają tych samych zasad dostępu, podobnie jak magazynu kontu poszczególne obiekty BLOB w ramach tego konta magazynu. Należy pamiętać, kilka informacji na temat przestrzeń nazw usługi event hubs i centrów zdarzeń, które tworzysz:
* Zalecamy używanie przestrzeni nazw standardowe usługi Event Hubs.
* Typowo tylko jedną jednostkę przepływności jest to konieczne. Jeśli musisz skalować w górę wzrostu użycia dziennika zawsze ręcznie zwiększyć liczbę jednostek przepływności w przestrzeni nazw w dalszej części lub Włącz inflacji automatycznie.
* Liczbę jednostek przepływności, można zwiększyć skalę przepływności usługi event hubs. Liczba partycji można zrównoleglić użycie wielu odbiorców. Jedna partycja wykonać maksymalnie 20MBps lub około 20 000 komunikatów na sekundę. W zależności od narzędzia korzystanie z danych może być lub może nie obsługiwać korzystanie z różnych partycji. Jeśli nie masz pewności co do liczby partycji można ustawić, zalecamy rozpoczęcie od cztery partycje.
* Zalecane ustawienie przechowywanie komunikatów w Centrum zdarzeń do 7 dni. Jeśli narzędzie konsumencki przestanie działać więcej niż jeden dzień, dzięki temu narzędziu można wczytać tam, gdzie ją przerwaliśmy (zdarzenia maksymalnie 7 dni).
* Zalecamy używanie domyślna grupa odbiorców Centrum zdarzeń. Nie ma potrzeby do tworzenia innych grup odbiorców lub używają grupy odbiorców oddzielne, chyba że użytkownik chce posiadać dwóch różnych narzędzi, które korzystają z tych samych danych, z tym samym Centrum zdarzeń.
* W dzienniku aktywności platformy Azure wybierz przestrzeń nazw usługi Event Hubs i Azure Monitor tworzy Centrum zdarzeń w ramach tej przestrzeni nazw o nazwie "insights — dzienniki operational-logs." Dla innych typów dziennika można wybrać istniejącym Centrum zdarzeń (umożliwiając ponowne używanie tego samego Centrum zdarzeń usługi insights dzienniki operational-logs) lub usługi Azure Monitor Tworzenie Centrum zdarzeń dla każdej kategorii dziennika.
* Zazwyczaj wychodząca przez port 5671 i 5672 musi być otwarty na komputerze lub korzystanie z danych z Centrum zdarzeń w sieci Wirtualnej.

Można również znaleźć [Azure Event Hubs — często zadawane pytania](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>Monitorowanie danych dzierżawy platformy Azure

Dane monitorowania dzierżawy platformy Azure jest obecnie dostępny tylko dla usługi Azure Active Directory. Możesz użyć danych z [raportowania usługi Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md), który zawiera historię logowania działań i inspekcji dziennik zmian wprowadzonych w określonej dzierżawie.

### <a name="azure-active-directory-data"></a>Azure Active Directory data

Aby wysyłać dane z dziennika usługi Azure Active Directory do przestrzeni nazw usługi Event Hubs, należy skonfigurować ustawienie diagnostyczne dzierżawy w dzierżawie usługi AAD. [Postępuj zgodnie z tego przewodnika](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) skonfigurować ustawienie diagnostyczne dzierżawy.

## <a name="azure-subscription-monitoring-data"></a>Dane monitorowania subskrypcji platformy Azure

Dane monitorowania subskrypcji platformy Azure jest dostępna w [dziennika aktywności platformy Azure](./../../azure-monitor/platform/activity-logs-overview.md). Zawiera tworzenia, aktualizowanie i usuwanie operacji z usługi Resource Manager, zmiany w [usługę Azure service health](../../service-health/service-health-overview.md) , może mieć wpływ na zasoby w ramach subskrypcji, [kondycja zasobu](../../service-health/resource-health-overview.md) stanu przejścia i kilku innych typów zdarzeń na poziomie subskrypcji. [Ten artykuł szczegółowo opisuje wszystkie kategorie zdarzeń, które pojawiają się w dzienniku aktywności platformy Azure](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Dane dzienników aktywności

Aby przesyłanie danych z dziennika aktywności platformy Azure do przestrzeni nazw usługi Event Hubs, należy skonfigurować profil dziennika w ramach subskrypcji. [Postępuj zgodnie z tego przewodnika](./activity-logs-stream-event-hubs.md) do konfiguracji profilu dziennika w ramach Twojej subskrypcji. W tym po każdej subskrypcji, którą chcesz monitorować.

> [!TIP]
> Profil dziennika obecnie tylko służy do wybierania przestrzeni nazw usługi Event Hubs, w którym Centrum zdarzeń jest tworzone przy użyciu nazwy "insights-operational-logs." Go nie jest jeszcze można określić własną nazwę Centrum zdarzeń w profilu dziennika.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Dzienniki usługi Azure resource metryki i Diagnostyka

Zasoby platformy Azure tworzą dwa rodzaje danych monitorowania:
1. [Dzienniki diagnostyczne zasobu](diagnostic-logs-overview.md)
2. [Metryki](data-platform.md)

Oba typy danych są wysyłane do Centrum zdarzeń za pomocą ustawienie diagnostyczne zasobu. [Postępuj zgodnie z tego przewodnika](diagnostic-logs-stream-event-hubs.md) skonfigurować ustawienie diagnostyczne zasobu definiuje od określonego zasobu. Ustaw ustawienie diagnostyczne zasobu dla każdego zasobu, z której chcesz zbierać dzienniki.

> [!TIP]
> Można użyć usługi Azure Policy, aby upewnić się, że każdy zasób w określonym zakresie zawsze skonfigurowano ustawienie diagnostyczne [przy użyciu efektu DeployIfNotExists w regule zasad](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="guest-os-data"></a>Dane systemu operacyjnego gościa

Musisz zainstalować agenta, aby wysyłać dane monitorowania systemu operacyjnego gościa do Centrum zdarzeń. Dla Windows lub Linux należy określić dane, które mają być wysyłane do Centrum zdarzeń, a także Centrum zdarzeń, do którego dane mają być wysyłane w pliku konfiguracji i przekazać plik konfiguracji do agenta uruchomionego na maszynie Wirtualnej.

### <a name="linux-data"></a>Dane systemu Linux

[Agenta diagnostyki Azure Linux](../../virtual-machines/extensions/diagnostics-linux.md) może służyć do wysyłania danych z maszyny z systemem Linux w Centrum zdarzeń monitorowania. To zrobić, dodając Centrum zdarzeń jako obiekt sink w swojej LAD ustawienia chronionego pliku konfiguracji JSON. [Ten artykuł, aby dowiedzieć się więcej na temat dodawania obiekt sink zdarzenia Centrum agentowi diagnostyki Azure Linux](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Nie można skonfigurować przesyłanie strumieniowe danych monitorowania systemów operacyjnych gościa do Centrum zdarzeń w portalu. Zamiast tego należy ręcznie edytować plik konfiguracji.

### <a name="windows-data"></a>Windows danych

[Diagnostycznych platformy Azure Windows agent](./../../azure-monitor/platform/diagnostics-extension-overview.md) może służyć do wysyłania danych z maszyny Windows do Centrum zdarzeń monitorowania. To zrobić, dodając Centrum zdarzeń jako obiekt sink w sekcji privateConfig WAD pliku konfiguracji. [Ten artykuł, aby dowiedzieć się więcej na temat dodawania obiekt sink zdarzenia Centrum agentowi diagnostyki Azure Windows](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Nie można skonfigurować przesyłanie strumieniowe danych monitorowania systemów operacyjnych gościa do Centrum zdarzeń w portalu. Zamiast tego należy ręcznie edytować plik konfiguracji.

## <a name="application-monitoring-data"></a>Dane monitorowania aplikacji

Monitorowanie danych aplikacji wymaga, że kod został zinstrumentowany przy użyciu zestawu SDK, dzięki czemu nie występuje ogólnego przeznaczenia rozwiązania do monitorowania danych do Centrum zdarzeń w systemie Azure routing aplikacji. Jednak [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) co usługa, która może służyć do zbierania danych z poziomu aplikacji platformy Azure. Jeśli używasz usługi Application Insights obejmuje strumieniowe przesyłanie danych monitorowania do Centrum zdarzeń, wykonując następujące czynności:

1. [Skonfiguruj Eksport ciągły](../../azure-monitor/app/export-telemetry.md) danych usługi Application Insights do konta magazynu.

2. Konfigurowanie aplikacji Logic wyzwolone przez czasomierz, [ściąga dane z magazynu obiektów blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) i [wypycha go jako wiadomości do Centrum zdarzeń](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Co można zrobić za pomocą danych monitorowania, są wysyłane do mojego Centrum zdarzeń?

Routing danych monitorowania do Centrum zdarzeń za pomocą usługi Azure Monitor pozwala łatwo zintegrować ją z partnerem rozwiązania SIEM i narzędzi do monitorowania. Większość narzędzi wymagają parametry połączenia Centrum zdarzeń i pewnych uprawnień do Twojej subskrypcji platformy Azure można odczytać danych z Centrum zdarzeń. Tutaj znajduje się niepełna lista narzędzi obsługujących integrację usługi Azure Monitor:

* **IBM QRadar** — Microsoft Azure DSM i Protokół Centrum zdarzeń Azure firmy Microsoft są dostępne do pobrania z [witryna internetowa Pomocy technicznej firmy IBM](https://www.ibm.com/support). Możesz [dowiedzieć się więcej o integracji z platformą Azure w tym miejscu](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** — w zależności od ustawień Splunk, dostępne są dwie opcje:
    1. [Dodatek monitora platformy Azure dla programu Splunk](https://splunkbase.splunk.com/app/3534/) jest dostępna w Splunkbase i projekt open source. [Dokumentacja jest tutaj](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Jeśli nie możesz zainstalować dodatkowe wystąpienia Splunk (np.) Jeśli przy użyciu serwera proxy lub działające w chmurze Splunk), możesz przekazywać te zdarzenia do modułu zbierającego zdarzenia HTTP Splunk przy użyciu [tę funkcję, która jest wyzwalana przez nowych komunikatów w Centrum zdarzeń](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** — instrukcje dotyczące konfigurowania SumoLogic do pracy z danymi z Centrum zdarzeń są [dostępne tutaj](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)
* **ArcSight** — łącznik inteligentne ArcSight Azure Event Hub jest dostępny jako część [tutaj kolekcji inteligentnych łącznika ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852).
* **Serwer SYSLOG** — Jeśli chcesz strumień danych usługi Azure Monitor bezpośrednio do serwera syslog, możesz sprawdzić [tego repozytorium GitHub](https://github.com/miguelangelopereira/azuremonitor2syslog/).

## <a name="next-steps"></a>Następne kroki
* [Archiwizowanie dziennika aktywności na koncie magazynu](../../azure-monitor/platform/archive-activity-log.md)
* [Zapoznaj się z omówieniem dziennik aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Ustawianie alertu na podstawie zdarzenia dziennika aktywności](../../azure-monitor/platform/alerts-log-webhook.md)


