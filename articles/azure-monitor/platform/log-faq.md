---
title: Log Analytics często zadawane pytania | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące usługi dzienników Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/01/2019
ms.openlocfilehash: 9eb921fc8ea19486db0fc3311764931f09e11464
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579308"
---
# <a name="log-analytics-faq"></a>Log Analytics — często zadawane pytania

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ta firma Microsoft — często zadawane pytania dotyczące obszaru roboczego Log Analytics Azure Monitor. Jeśli masz dodatkowe pytania dotyczące Log Analytics, przejdź do [forum dyskusyjnego](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) i Opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodamy je do tego artykułu, aby można je było szybko i łatwo znaleźć.


## <a name="new-logs-experience"></a>Nowe środowisko dzienników

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>P: Jaka jest różnica między nowym doświadczeniem dzienników a Log Analytics?

Odp.: są to te same rzeczy. [Log Analytics jest integrowana jako funkcja w Azure monitor](../../azure-monitor/azure-monitor-rebrand.md) , aby zapewnić bardziej ujednolicone środowisko monitorowania. Nowe środowiska dzienników w Azure Monitor są dokładnie takie same, jak w przypadku zapytań Log Analytics, do których wielu klientów już korzystali.

### <a name="q-can-i-still-use-log-search"></a>P: Czy można nadal używać przeszukiwania dzienników? 

Odp.: wyszukiwanie w dzienniku jest obecnie dostępne w portalu pakietu OMS i w Azure Portal w obszarze **dzienniki nazw (klasyczne)** . Portal pakietu OMS zostanie oficjalnie wycofany 15 stycznia 2019. Środowisko dzienników klasycznych w Azure Portal będzie stopniowo wycofywane i zastąpione nowym doświadczeniem dzienników. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>PYTANIE: Czy nadal mogę korzystać z portalu analizy zaawansowanej? 
Nowe środowisko dzienników w Azure Portal jest oparte na portalu analizy zaawansowanej, ale nadal można uzyskać do niego dostęp poza Azure Portal. Plan wycofania tego portalu zewnętrznego zostanie ogłoszony wkrótce.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>PYTANIE: Dlaczego nie widzę Eksploratora zapytań i nie zapisuj przycisków w nowym środowisku dzienników?

**Eksplorator zapytań**, przyciski **zapisywania** i **ustawiania alertów** nie są dostępne podczas eksplorowania dzienników w kontekście określonego zasobu. Do tworzenia alertów, zapisywania lub ładowania zapytania, dzienniki muszą być objęte zakresem obszaru roboczego. Aby otworzyć aplet dzienniki w kontekście obszaru roboczego, wybierz pozycję **wszystkie usługi** > **monitorowanie** **dzienników** > . Wybrano ostatnio używany obszar roboczy, ale można wybrać dowolny inny obszar roboczy. Aby uzyskać więcej informacji [, zobacz Wyświetlanie i analizowanie danych w log Analytics](../log-query/portals.md) .

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>PYTANIE: Jak mogę wyodrębnić pola niestandardowe w nowym środowisku dzienników? 

Odp.: Wyodrębnianie pól niestandardowych jest obecnie obsługiwane w klasycznym środowisku dzienników. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>PYTANIE: Gdzie mogę znaleźć widok listy w nowych dziennikach? 

A: widok listy nie jest dostępny w nowych dziennikach. W tabeli wyników znajduje się strzałka z lewej strony każdego rekordu. Kliknij tę strzałkę, aby otworzyć Szczegóły dla określonego rekordu. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>PYTANIE: Po uruchomieniu zapytania jest dostępna lista sugerowanych filtrów. Jak można zobaczyć filtry? 

Odp.: kliknij przycisk "filtry" w okienku po lewej stronie, aby wyświetlić podgląd nowej implementacji filtrów. Jest to teraz oparte na pełnym zestawie wyników, nie ograniczając przez limit rekordów 10 000 interfejsu użytkownika. Jest to obecnie lista najpopularniejszych filtrów i 10 najbardziej typowych wartości dla każdego filtru. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>PYTANIE: Dlaczego otrzymuję błąd: "Zarejestruj dostawcę zasobów" Microsoft. Insights "dla tej subskrypcji, aby włączyć to zapytanie" w dziennikach po przejściu z maszyny wirtualnej do szczegółów? 

Odp.: domyślnie wielu dostawców zasobów jest rejestrowanych automatycznie, jednak może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Spowoduje to skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Zakres rejestracji jest zawsze subskrypcją. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>PYTANIE: Dlaczego otrzymuję komunikat o błędzie dostępu podczas uzyskiwania dostępu do dzienników ze strony maszyny wirtualnej? 

Odp.: Aby wyświetlić dzienniki maszyn wirtualnych, musisz mieć uprawnienia do odczytu w obszarze roboczym, w którym są przechowywane dzienniki maszyn wirtualnych. W takich przypadkach administrator musi udzielić użytkownikowi uprawnienia do uprawnień na platformie Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>PYTANIE: Dlaczego można uzyskać dostęp do obszaru mój obszar roboczy w portalu pakietu OMS, ale występuje błąd "Brak dostępu" w Azure Portal?  

Odp.: Aby uzyskać dostęp do obszaru roboczego na platformie Azure, musisz mieć przypisane uprawnienia platformy Azure. Istnieją sytuacje, w których użytkownik może nie mieć odpowiednich uprawnień dostępu. W takich przypadkach administrator musi udzielić Ci uprawnień na platformie Azure. Aby uzyskać więcej informacji, zobacz [Portal pakietu OMS przejście na platformę Azure](oms-portal-transition.md) .

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>PYTANIE: Dlaczego nie mogę zobaczyć wyświetlania wpisów projektanta w dziennikach?

Odp.: Projektant widoków jest dostępny tylko w dziennikach dla użytkowników przypisanych z uprawnieniami współautora lub wyższym.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>PYTANIE: Czy nadal mogę korzystać z portalu analizy poza platformą Azure?

A. Tak, Strona dzienniki na platformie Azure i portalu analizy zaawansowanej bazują na tym samym kodzie. Log Analytics jest integrowana jako funkcja w Azure Monitor, aby zapewnić bardziej ujednolicone środowisko monitorowania. Nadal możesz uzyskać dostęp do portalu analitycznego przy użyciu adresu URL: https:\/\/Portal. loganalytics. IO/subscriptions/{Subscription}/ResourceGroups/{resourceGroupName}/Workspaces/{WorkspaceName}.



## <a name="general"></a>Ogólne

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>PYTANIE: Jak można zobaczyć moje widoki i rozwiązania w Azure Portal? 

Odp.: Lista widoków i zainstalowanych rozwiązań jest dostępna w Azure Portal. Kliknij opcję **Wszystkie usługi**. Na liście zasobów wybierz pozycję **Monitoruj**, a następnie kliknij pozycję **... Więcej**. Wybrano ostatnio używany obszar roboczy, ale można wybrać dowolny inny obszar roboczy. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>PYTANIE: Dlaczego nie mogę utworzyć obszarów roboczych w regionie Zachodnio-środkowe stany USA? 

Odp.: ten region ma tymczasowy limit pojemności. Ten limit jest planowany na podstawie końca września, 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>PYTANIE: Czy Log Analytics używać tego samego agenta co Azure Security Center?

Odp.: na początku czerwca 2017 Azure Security Center rozpocząć korzystanie z Microsoft Monitoring Agent do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center migracji platformy — często zadawane pytania](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>PYTANIE: Jakie testy są wykonywane przez rozwiązania AD i SQL Assessment?

Odp.: następujące zapytanie wyświetla opis wszystkich aktualnie wykonanych testów:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Wyniki można następnie wyeksportować do programu Excel w celu dalszej analizy.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>PYTANIE: Dlaczego widzę coś innego niż OMS w konsoli System Center Operations Manager?

Odp.: w zależności od tego, jaki pakiet zbiorczy aktualizacji Operations Manager jest włączony, może zostać wyświetlony węzeł usługi *System Center Advisor*, *Operational Insights*lub *log Analytics*.

Aktualizacja ciągu tekstowego pakietu *OMS* jest uwzględniona w pakiecie administracyjnym, który należy zaimportować ręcznie. Aby wyświetlić bieżący tekst i funkcje, postępuj zgodnie z instrukcjami zawartymi w artykule najnowszy pakiet zbiorczy aktualizacji System Center Operations Manager KB i Odśwież konsolę programu.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: czy istnieje lokalna wersja Log Analytics?

Odp.: nie. Log Analytics to skalowalna usługa w chmurze, która przetwarza i przechowuje duże ilości danych. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>PYTANIE: Jak mogę otrzymywać powiadomienia, gdy zbieranie danych zostało zatrzymane?

Odp.: wykonaj kroki opisane w sekcji [Tworzenie nowego alertu dziennika](../../azure-monitor/platform/alerts-metric.md) , aby otrzymywać powiadomienia o zatrzymaniu zbierania danych.

Podczas tworzenia alertu dla momentu, gdy zbieranie danych zostało zatrzymane, ustaw:

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**.
   - **Zapytanie wyszukiwania** na `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Alert logiki** **opiera się na** *liczbie wyników*, a **warunek** jest *większy niż*  **próg**  wynoszący *0*
   - **Okres** *30* minut i **częstotliwość alertów** do co *10* minut
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** do *zbierania danych została zatrzymana*
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą lub Utwórz nową [grupę akcji](../../azure-monitor/platform/action-groups.md) w taki sposób, że gdy alert dziennika spełnia kryteria, otrzymasz powiadomienie, jeśli brakuje pulsu przez ponad 15 minut.

## <a name="configuration"></a>Konfiguracja

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>PYTANIE: Czy mogę zmienić nazwę kontenera tabeli/obiektu BLOB użytego do odczytu z Diagnostyka Azure (funkcji wad)?

A. Nie, obecnie nie jest możliwe odczytywanie z dowolnych tabel ani kontenerów w usłudze Azure Storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>PYTANIE: Jakie adresy IP są używane przez usługę Log Analytics? Jak mogę upewnić się, że moja zapora zezwala tylko na ruch do usługi Log Analytics?

A. Usługa Log Analytics jest oparta na platformie Azure. Log Analytics adresy IP znajdują się w [zakresach adresów ip Microsoft Azure centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych.

W miarę dokonywania wdrożeń usług rzeczywiste adresy IP usługi Log Analytics zmieniają się. Nazwy DNS, które mają być dozwolone przez zaporę, są udokumentowane w [wymaganiach sieciowych](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>PYTANIE: Używam ExpressRoute do nawiązywania połączenia z platformą Azure. Czy mój Log Analytics ruchu używa mojego połączenia ExpressRoute?

A. Różne typy ruchu ExpressRoute są opisane w [dokumentacji ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Ruch do Log Analytics używa obwodu komunikacji równorzędnej ExpressRoute.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>PYTANIE: Czy istnieje prosty i prosty sposób przenoszenia istniejącego obszaru roboczego Log Analytics do innego Log Analytics obszaru roboczego/subskrypcji platformy Azure?

A. `Move-AzResource` polecenie cmdlet umożliwia przeniesienie obszaru roboczego Log Analytics, a także konta usługi Automation z jednej subskrypcji platformy Azure do innej. Aby uzyskać więcej informacji, zobacz [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Tę zmianę można również wprowadzić w Azure Portal.

Nie można przenosić danych z jednego obszaru roboczego Log Analytics do innego ani zmieniać regionu, w którym są przechowywane Log Analytics dane.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: Jak mogę dodać Log Analytics do System Center Operations Manager?

Odp.: Aktualizacja do najnowszego pakietu zbiorczego aktualizacji i Importowanie pakietów administracyjnych umożliwia łączenie Operations Manager z Log Analytics.

>[!NOTE]
>Operations Manager połączenie z Log Analytics jest dostępne tylko dla System Center Operations Manager 2012 z dodatkiem SP1 lub nowszym.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: jak potwierdzić, że Agent może komunikować się z Log Analytics?

Odp.: aby upewnić się, że Agent może komunikować się z obszarem roboczym Log Analytics, przejdź do: Panel sterowania, zabezpieczenia & Ustawienia, **Microsoft Monitoring Agent**.

Na karcie **Azure log Analytics (OMS)** odszukaj zielony znacznik wyboru. Zielona ikona znacznika wyboru potwierdza, że Agent jest w stanie komunikować się z usługą platformy Azure.

Żółta ikona ostrzeżenia oznacza, że agent ma problemy z komunikacją z Log Analytics. Jednym z typowych przyczyn jest zatrzymanie usługi Microsoft Monitoring Agent. Użyj menedżera kontroli usług, aby ponownie uruchomić usługę.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: Jak mogę zatrzymać komunikację agenta z Log Analytics?

Odp.: w System Center Operations Manager, Usuń komputer z listy Log Analytics zarządzanych komputerów. Operations Manager aktualizuje konfigurację agenta, aby nie był już raportowany do Log Analytics. W przypadku agentów podłączonych do Log Analytics bezpośrednio można je zatrzymywać za pośrednictwem: Panel sterowania, ustawienia & zabezpieczeń, **Microsoft Monitoring Agent**.
W obszarze **Azure log Analytics (OMS)** Usuń wszystkie obszary robocze wymienione na liście.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: Dlaczego otrzymuję błąd podczas próby przeniesienia mojego obszaru roboczego z jednej subskrypcji platformy Azure do innej?

Odp.: Aby przenieść obszar roboczy do innej subskrypcji lub grupy zasobów, musisz najpierw odłączyć konto usługi Automation w obszarze roboczym. Odłączanie konta usługi Automation wymaga usunięcia tych rozwiązań, jeśli są one zainstalowane w obszarze roboczym: Update Management, Change Tracking lub Start/Stop VMs during off-hours zostaną usunięte. Po usunięciu tych rozwiązań Odłącz konto usługi Automation, wybierając pozycję **połączone obszary robocze** w lewym okienku zasobu konta usługi Automation, a następnie kliknij przycisk **Odłącz obszar roboczy** na Wstążce.
 > Usunięte rozwiązania należy zainstalować ponownie w obszarze roboczym, a połączenie usługi Automation z obszarem roboczym należy zmienić po przeniesieniu.

Upewnij się, że masz uprawnienia do obu subskrypcji platformy Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>P: Dlaczego otrzymuję błąd podczas próby zaktualizowania zapisanego wyszukiwania?

Odp.: należy dodać element "ETag" w treści interfejsu API lub Azure Resource Manager właściwości szablonu:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Dane agenta
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>PYTANIE: Jak dużo danych mogę wysłać przez agenta, aby Log Analytics? Czy istnieje maksymalna ilość danych na klienta?
A. Nie ma żadnego limitu ilości przekazywanych danych, zależy od wybranej opcji cennika lub rezerwacji zgodnie z rzeczywistym użyciem. Log Analytics obszar roboczy jest zaprojektowana w celu automatycznego skalowania w górę w celu obsługi woluminu pochodzącego od klienta — nawet w przypadku terabajtów dziennie. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/monitor/).

Agent Log Analytics został zaprojektowany w celu zapewnienia, że ma niewielki wpływ. Wolumin danych różni się w zależności od tego, jakie rozwiązania zostały włączone. Szczegółowe informacje na temat ilości danych można znaleźć w temacie podział według rozwiązania na stronie [użycie](../../azure-monitor/platform/data-usage.md) .

Aby uzyskać więcej informacji, możesz przeczytać [blog klienta](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) pokazujący wyniki po ocenie wykorzystania zasobów (wpływ) agenta log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>PYTANIE: Jaka przepustowość sieci jest używana przez program Microsoft Management Agent (MMA) podczas wysyłania danych do Log Analytics?

A. Przepustowość to funkcja ilości wysłanych danych. Dane są kompresowane w miarę ich przesyłania przez sieć.

### <a name="q-how-much-data-is-sent-per-agent"></a>PYTANIE: Ile danych jest wysyłanych na agenta?

A. Ilość danych wysłanych na agenta zależy od następujących:

* Rozwiązania, które zostały włączone
* Liczba zbieranych dzienników i liczników wydajności
* Ilość danych w dziennikach

Ogólne użycie jest wyświetlane na stronie [użycie](../../azure-monitor/platform/data-usage.md) .

W przypadku komputerów, na których można uruchomić agenta typowe, użyj następującego zapytania, aby sprawdzić, ile danych jest wysyłanych:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Następne kroki

[Rozpocznij pracę z Azure monitor](../../azure-monitor/overview.md) , aby dowiedzieć się więcej na temat log Analytics i rozpocząć pracę w ciągu kilku minut.
