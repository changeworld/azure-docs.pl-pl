---
title: Przegląd dziennika aktywności platformy Azure
description: Aby dowiedzieć się o dzienniku aktywności platformy Azure i jak go używać do zrozumienia zdarzeń występujących w ramach subskrypcji platformy Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6fc00bf0dfb83f349da91989a579f31be2027ff0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071686"
---
# <a name="overview-of-azure-activity-log"></a>Przegląd dziennika aktywności platformy Azure

**Dziennika aktywności platformy Azure** zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. W tym zakresie danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji dla zdarzeń kondycji usługi. Dziennik aktywności była wcześniej znana jako _dzienników inspekcji_ lub _operacyjne dzienniki_, ponieważ kategoria administracyjna raporty zdarzeń płaszczyznę kontroli dla subskrypcji. 

Korzystanie z dziennika aktywności, aby określić _co_, _kto_, i _podczas_ dla dowolnego zapisu (PUT, POST, DELETE) wykonywanych na zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. 

Dziennik aktywności nie obejmują operacji odczytu (GET) ani operacji dotyczących zasobów, które używają modelu klasyczną/frontonu REDDOG.

## <a name="comparison-to-diagnostic-logs"></a>Porównanie z dzienników diagnostycznych
Brak jednego dziennika aktywności dla każdej subskrypcji platformy Azure. Zawiera ona informacje o operacjach w zasobie z zewnątrz ("płaszczyzna kontroli"). [Dzienniki diagnostyczne](diagnostic-logs-overview.md) są emitowane przez zasób i podaj informacje o działaniu tego zasobu ("płaszczyzny danych"). Należy włączyć ustawienia diagnostyczne dla każdego zasobu.

![Dzienniki aktywności w porównaniu do dzienników diagnostycznych](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Dziennik aktywności platformy Azure jest przede wszystkim dla działań, które wystąpią w usłudze Azure Resource Manager. Żądania nie Śledź zasobów przy użyciu modelu klasyczną/frontonu REDDOG. Niektóre typy zasobów klasycznego mają dostawcy zasobów serwera proxy w usłudze Azure Resource Manager (na przykład Microsoft.ClassicCompute). Jeśli kontaktujesz się z typem zasobu klasycznym za pośrednictwem usługi Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy, operacje są wyświetlane w dzienniku aktywności. Gdy wchodzisz w interakcję z typem zasobu Classic poza serwery proxy usługi Azure Resource Manager, akcji tylko są rejestrowane w dzienniku operacji. Dziennik operacji można przeglądać w osobnej sekcji portalu.

## <a name="activity-log-retention"></a>Przechowywanie dziennika aktywności
Po utworzeniu wpisy dziennika aktywności nie są zmodyfikowany lub usunięty przez system. Ponadto nie można zmienić je w interfejsie lub programowo. Zdarzenia dziennika aktywności są przechowywane przez 90 dni. Do przechowywania danych przez dłuższy czas [zbieranie go w usłudze Azure Monitor](activity-log-collect.md) lub [wyeksportować je do magazynu lub centrów zdarzeń](activity-log-export.md).

## <a name="view-the-activity-log"></a>Wyświetl dziennik aktywności
Wyświetl dziennik aktywności, aby wszystkie zasoby z **Monitor** menu w witrynie Azure portal. Wyświetlanie dziennika aktywności dla danego zasobu z **dziennika aktywności** opcji w menu tego zasobu. Możesz również pobrać rekordów dziennika aktywności przy użyciu programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST.  Zobacz [wyświetlanie i pobieranie aktywności platformy Azure, rejestrowanie zdarzeń](activity-log-view.md).

![Wyświetl dziennik aktywności](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Zbieranie dzienników aktywności w usłudze Azure Monitor
Zbieranie dzienników aktywności do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor, aby je przeanalizować za pomocą innych danych monitorowania i przechowywania danych przez czas dłuższy niż 90 dni. Zobacz [zbierać i analizować Dzienniki aktywności platformy Azure, w obszarze roboczym Log Analytics w usłudze Azure Monitor](activity-log-collect.md).

![Zapytanie dziennika aktywności](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Eksportuj Dziennik aktywności
Eksportuj Dziennik aktywności do usługi Azure Storage w celu archiwizacji lub Prześlij go strumieniowo do Centrum zdarzeń w celu pozyskiwania przez usługi innych firm lub rozwiązania do analizy niestandardowych. Zobacz [Eksportuj Dziennik aktywności platformy Azure](activity-log-export.md). Ponadto można przeanalizować zdarzenia dziennika aktywności w usłudze Power BI przy użyciu [ **pakiet zawartości usługi Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Alert dotyczący dziennika aktywności
Można utworzyć alert, gdy określonych zdarzeń są tworzone w dzienniku aktywności z [alertu dziennika aktywności](activity-log-alerts.md). Można również utworzyć usługi alertów przy użyciu [zapytanie dziennika](alerts-log-query.md) po dziennik aktywności jest połączony z obszarem roboczym usługi Log Analytics, ale nic nie kosztuje się zapytania alertów. Nie ma żadnych kosztów w przypadku alertów dziennika aktywności.

## <a name="categories-in-the-activity-log"></a>Kategorie w dzienniku aktywności
Każde zdarzenie w dzienniku aktywności ma określonej kategorii, które są opisane w poniższej tabeli. Aby uzyskać szczegółowe informacje o wypełniana z tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md). 

| Category | Opis |
|:---|:---|
| Administracyjne | Zawiera rekord wszystkich tworzenia, aktualizowania, usuwania i akcji operacje wykonywane przy użyciu usługi Resource Manager. Przykłady zdarzeń administracyjnych _Utwórz maszynę wirtualną_ i _usunąć grupy zabezpieczeń sieci_.<br><br>Każdej akcji podjętej przez użytkownika lub aplikacji przy użyciu usługi Resource Manager jest modelowane jako operacji na określonego typu zasobu. Jeśli typ operacji _zapisu_, _Usuń_, lub _akcji_, rekordy początkowego i powodzenie lub niepowodzenie tej operacji, które są rejestrowane w kategorii administracyjnej. Zdarzenia administracyjne również zawierają zmiany do kontroli dostępu opartej na rolach w ramach subskrypcji. |
| Service Health | Zawiera rekord wszelkie zdarzenia kondycji usługi, które miały miejsce w systemie Azure. Przykładem zdarzenia usługi Service Health _SQL Azure w regionie wschodnie stany USA ma przestój_. <br><br>Zdarzenia usługi Service Health są dostępne w pięciu odmian: _Wymagana akcja_, _wspierana odzyskiwania_, _zdarzenia_, _konserwacji_, _informacji_, lub  _Zabezpieczenia_. Te zdarzenia są tworzone tylko w przypadku zasobów w subskrypcji, która będzie mieć wpływ na zdarzenie.
| Kondycja zasobów | Zawiera rekord wszystkie zdarzenia dotyczące kondycji zasobów, które wystąpiły z zasobami platformy Azure. Na przykład zdarzenie kondycji zasobów _maszyny wirtualnej kondycji stan zmienił się na niedostępny_.<br><br>Zdarzenia dotyczące kondycji zasobów może reprezentować jedną z czterech stanów kondycji: _Dostępne_, _niedostępny_, _negatywny wpływ na dostępność_, i _nieznany_. Ponadto można podzielić zdarzenia dotyczące kondycji zasobu jako _zainicjowane platformy_ lub _użytkownik zainicjował_. |
| Alerty | Zawiera rekord aktywacji dla alertów platformy Azure. Na przykład zdarzenie alertu _procent użycia procesora CPU na myVM zostało ponad 80 w ciągu ostatnich 5 minut_.|
| Automatyczne skalowanie | Zawiera rekord wszystkie zdarzenia związane z działaniem aparat skalowania automatycznego na podstawie żadnych ustawień automatycznego skalowania, zdefiniowanych w ramach subskrypcji. Przykładem zdarzenia skalowania automatycznego jest _skalowania automatycznego skalowania w górę akcja nie powiodła się_. |
| Zalecenie | Zawiera zalecenia zdarzenia z usługi Azure Advisor. |
| Bezpieczeństwo | Zawiera rekord wszystkie alerty wygenerowane przez usługę Azure Security Center. Na przykład zdarzenie związane z zabezpieczeniami _podejrzane podwójne rozszerzenie pliku wykonywane_. |
| Zasady | Zawiera rekordy wszystkie efekt działania operacje wykonywane przez usługę Azure Policy. Przykłady zdarzenia dotyczące zasad _inspekcji_ i _Odmów_. Każdej akcji podjętej przez zasady są modelowane jako operacja dotycząca zasobu. |


## <a name="next-steps"></a>Następne kroki

* [Tworzenie profilu dziennika w celu Eksportuj Dziennik aktywności platformy Azure](activity-log-export.md)
* [Stream dziennika aktywności platformy Azure do usługi Event Hubs](activity-logs-stream-event-hubs.md)
* [Archiwizowanie dziennika aktywności platformy Azure do magazynu](archive-activity-log.md)

