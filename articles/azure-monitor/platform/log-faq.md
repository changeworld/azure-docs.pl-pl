---
title: Log Analytics — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi dzienników Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394785"
---
# <a name="log-analytics-faq"></a>Log Analytics — często zadawane pytania

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ta firma Microsoft — często zadawane pytania dotyczące obszaru roboczego Log Analytics Azure Monitor. Jeśli masz dodatkowe pytania dotyczące usługi Log Analytics, przejdź do strony [forum dyskusyjne](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.


## <a name="new-logs-experience"></a>Nowe środowisko dzienników

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>P: jaka jest różnica między nowe środowisko dzienniki i usługi Log Analytics?

Odp.: są to samo. [Usługa log Analytics jest integrowany jako funkcję w usłudze Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) zapewnienie bardziej jednolite środowisko monitorowania. Nowe środowisko dzienników w usłudze Azure Monitor jest dokładnie taka sama jak zapytań usługi Log Analytics, które już masz doświadczenie z wielu klientów.

### <a name="q-can-i-still-use-log-search"></a>P: czy można nadal korzystać z wyszukiwania w dziennikach? 

A: wyszukiwanie w dzienniku trwa nadal dostępne w portalu pakietu OMS i w witrynie Azure portal w obszarze nazwy **dzienniki (wersja klasyczna)** . Portal pakietu OMS zostanie oficjalnie wycofana 15 stycznia 2019 r. Klasyczne środowisko dzienników w witrynie Azure portal zostaną stopniowo wycofane i zastąpione nowe środowisko dzienniki. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>PYTANIE: Można nadal korzystać z portalu Advanced Analytics? 
Nowe środowisko dzienników w witrynie Azure portal jest oparty na portalu usługi analiza zaawansowana, ale jest nadal dostępny spoza witryny Azure portal. Plan wycofywania tego zewnętrznego portalu zostanie ogłoszona wkrótce.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>PYTANIE: Dlaczego nie można wyświetlić Eksplorator zapytań i Zapisz przycisków w nowym środowisku dzienniki?

**Eksplorator zapytań**, **Zapisz** i **ustawić Alert** przyciski są niedostępne podczas eksplorowania dzienniki w kontekście określonego zasobu. Aby tworzyć alerty, Zapisz lub załadować zapytania, dzienniki musi należeć do obszaru roboczego zakresu. Aby otworzyć dzienniki w kontekście obszaru roboczego, wybierz **wszystkich usług** > **Monitor** > **dzienniki**. Wybrano ostatni używanych obszaru roboczego, ale można wybrać inny obszar roboczy. Zobacz [wyświetlania i analizowania danych w usłudze Log Analytics](../log-query/portals.md) Aby uzyskać więcej informacji.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>PYTANIE: Jak wyodrębnić pól niestandardowych w nowym środowisku dzienniki? 

Odp.: niestandardowe wyodrębniania pól są obecnie obsługiwane w modelu klasycznym, które dzienniki środowiska. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>PYTANIE: Gdzie znaleźć widok listy w nowe dzienniki? 

Odp.: widok listy nie jest dostępna w nowe dzienniki. Strzałka w lewo każdy rekord w tabeli wyników nie istnieje. Strzałki można otworzyć szczegóły dla określonego rekordu. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>PYTANIE: Po uruchomieniu zapytania jest dostępna lista sugerowanych filtrów. Jak można zobaczyć filtry? 

Odp.: kliknij przycisk "filtry" w okienku po lewej stronie, aby wyświetlić podgląd nowej implementacji filtrów. Teraz jest oparta na wynik Twojego pełnego ustawić zamiast jest ograniczona przez limit 10 000 rekordów interfejsu użytkownika. Obecnie jest lista najpopularniejszych filtry i 10 najczęściej dla każdego filtru. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>PYTANIE: Dlaczego otrzymuję błąd: "Zarejestruj dostawcę zasobów"Microsoft.Insights"dla tej subskrypcji. Aby włączyć to zapytanie" w dziennikach, po przechodzenia do szczegółów z maszyny Wirtualnej? 

Odp.: domyślnie automatycznie zarejestrowano wielu dostawców zasobów, jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Pozwoli to na skonfigurowanie subskrypcji do pracy za pomocą dostawcy zasobów. Zakres do rejestracji jest zawsze subskrypcji. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>PYTANIE: Dlaczego mogę komunikat o błędzie nie dostępu do błędu podczas uzyskiwania dostępu do dzienników ze strony maszyny Wirtualnej? 

Odp.: Aby wyświetlić dzienniki maszyny Wirtualnej, musisz być nadane uprawnienia do odczytu do obszarów roboczych, które są przechowywane dzienniki maszyny Wirtualnej. W takich przypadkach administrator musi udzielić użytkownikowi uprawnień na platformie Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>PYTANIE: Dlaczego mogę można skorzystać z mojego obszaru roboczego w portalu pakietu OMS, ale jest zgłaszany błąd "nie masz dostępu" w witrynie Azure portal?  

Odp.: Aby uzyskać dostęp do obszaru roboczego na platformie Azure, musi mieć przypisane uprawnienia platformy Azure. Istnieją przypadki, gdy nie masz wystarczających uprawnień dostępu. W takich przypadkach administrator musi przyznać uprawnienia w Azure.See [portalu pakietu OMS na platformę Azure](oms-portal-transition.md) Aby uzyskać więcej informacji.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>PYTANIE: Dlaczego nie nie widzę Projektant widoków zapisu w dziennikach?

Odp.: Pokaż projektanta jest dostępna tylko w dziennikach dla użytkowników przypisanych z uprawnieniami współautora lub nowszej.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>PYTANIE: Można nadal korzystać z portalu usługi analiza spoza platformy Azure?

A. Tak, stronie dzienniki na platformie Azure i portalu analizy zaawansowanej opierają się na ten sam kod. Usługa log Analytics jest integrowany jako funkcję w usłudze Azure Monitor, aby zapewnić bardziej ujednoliconego środowiska monitorowania. Nadal możesz uzyskać dostęp do portalu analitycznego przy użyciu adresu URL: https:\/\/Portal. loganalytics. IO/subscriptions/{Subscription}/ResourceGroups/{resourceGroupName}/Workspaces/{WorkspaceName}.



## <a name="general"></a>Ogólne

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>PYTANIE: Jak mogę sprawdzić Mój widoki i rozwiązania w witrynie Azure portal? 

Odp.: Lista widoków i zainstalowane rozwiązania są dostępne w witrynie Azure portal. Kliknij opcję **Wszystkie usługi**. Na liście zasobów wybierz **Monitor**, następnie kliknij przycisk **... Więcej**. Wybrano ostatni używanych obszaru roboczego, ale można wybrać inny obszar roboczy. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>PYTANIE: Usługa Log Analytics używa ten sam agent Azure Security Center?

Odp.: w początku czerwca 2017 roku usługa Azure Security Center rozpoczęło się przy użyciu programu Microsoft Monitoring Agent do gromadzenia i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center platformy migracji — często zadawane pytania](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>PYTANIE: Jakie są sprawdzane przez usługi AD i rozwiązania SQL Assessment?

Odp.: następujące zapytanie zawiera opis wszystkich testów, które aktualnie wykonywane:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Następnie można wyeksportować wyniki do programu Excel w celu dalszego przeglądu.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>PYTANIE: Dlaczego warto zobaczyć coś innego niż pakietu OMS w konsoli programu System Center Operations Manager

Odp.: zależności od jakich Update Rollup programu Operations Manager znajdują się na, może być wyświetlany węzeł *System Center Advisor*, *usługi Operational Insights*, lub *usługi Log Analytics*.

Aktualizacja ciąg tekstu *OMS* znajduje się w pakiecie administracyjnym, należy zaimportować ręcznie. Aby zobaczyć aktualny tekst i funkcjonalność, postępuj zgodnie z instrukcjami na najnowsze systemu Centrum Operations Manager aktualizacji pakietu zbiorczego wiedzy i Odśwież konsolę.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: czy istnieje — lokalną wersją usługi Log Analytics?

Odpowiedź: nie. Usługa log Analytics to usługa w chmurze skalowalne, która przetwarza i przechowuje duże ilości danych. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>PYTANIE: Jak mogę otrzymywać powiadomienia po zatrzymaniu zbierania danych?

Odp.: wykonaj czynności opisane w [utworzyć nowego alertu dziennika](../../azure-monitor/platform/alerts-metric.md) zgłaszane po zatrzymaniu zbierania danych.

Podczas tworzenia alertu dla zatrzymania zbierania danych, ustaw:

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** wybierz **przeszukiwania dzienników niestandardowych**.
   - **Zapytanie wyszukiwania** na `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Alert logiki** **opiera się na** *liczbie wyników*, a **warunek** jest *większy niż*  **próg**  wynoszący *0*
   - **Okres** z *30* minut i **częstotliwość alertu** do każdego *10* minut
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** do *zbierania danych zatrzymane*
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą lub Utwórz nową [grupy akcji](../../azure-monitor/platform/action-groups.md) tak, aby po alertu dziennika odpowiadającego kryteriom, otrzymasz powiadomienie, jeśli masz pulsu, brak ponad 15 minut.

## <a name="configuration"></a>Konfigurowanie

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>PYTANIE: Czy można zmienić nazwy tabeli/kontenera obiektów blob używane do odczytywania z usługi Azure Diagnostics (WAD)?

A. Nie, nie jest obecnie możliwe do odczytu z dowolnego tabele lub kontenerów w usłudze Azure storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>PYTANIE: Adresy IP jest używana usługa Log Analytics? Jak zagwarantować, że moje zapora zezwala na tylko ruch do usługi Log Analytics?

A. Usługa Log Analytics jest oparty na platformie Azure. Adresy IP analizy dziennika są w [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Podczas wdrażania usługi zostaną wprowadzone, zmieniać rzeczywiste adresy IP usługi Log Analytics. Nazwy DNS, aby umożliwić za pośrednictwem zapory są udokumentowane w artykule [wymagania dotyczące sieciowej](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>PYTANIE: Usługa ExpressRoute jest używana do łączenia się z platformy Azure. Moje ruchu usługi Log Analytics używa Moje połączenie usługi ExpressRoute?

A. Różne rodzaje ruchu usługi ExpressRoute są opisane w [dokumentacja usługi ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Ruch do usługi Log Analytics używa obwód usługi ExpressRoute publicznej komunikacji równorzędnej.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>PYTANIE: Czy istnieje prosty i łatwy sposób można przenieść istniejący obszar roboczy usługi Log Analytics do innej subskrypcji Azure/obszaru roboczego usługi Log Analytics?

A. `Move-AzResource` Polecenie cmdlet pozwala na przechodzenie obszar roboczy usługi Log Analytics i konto usługi Automation z jedną subskrypcją platformy Azure do innego. Aby uzyskać więcej informacji, zobacz [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Ta zmiana może również w witrynie Azure portal.

Nie można przenieść dane z jednego obszaru roboczego usługi Log Analytics do innego lub zmienić dane usługi Log Analytics są przechowywane w regionie.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: jak dodać usługi Log Analytics programu System Center Operations Manager?

Odp.: Aktualizacja do najnowszego pakietu zbiorczego aktualizacji i importowania pakietów administracyjnych umożliwia łączenie programu Operations Manager do usługi Log Analytics.

>[!NOTE]
>Połączenie programu Operations Manager do usługi Log Analytics jest dostępna tylko dla programu System Center Operations Manager 2012 z dodatkiem SP1 lub nowszy.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: jak można potwierdzić, że agent jest w stanie nawiązać połączenia z usługą Log Analytics?

Odp.: aby upewnić się, że Agent może komunikować się z obszarem roboczym Log Analytics, przejdź do: Panel sterowania, zabezpieczenia & Ustawienia, **Microsoft Monitoring Agent**.

W obszarze **Azure Log Analytics (OMS)** kartę, poszukaj zielony znacznik wyboru. Zielona ikona znacznika wyboru potwierdza, że agent jest w stanie komunikować się z usługą Azure.

Żółtą ikoną ostrzeżenia oznacza, że agent występują problemy z komunikacji z usługą Log Analytics. Jedną typową przyczyną jest to, że usługa Microsoft Monitoring Agent została zatrzymana. Aby ponownie uruchomić usługę, należy użyć Menedżera sterowania usługami.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: jak zatrzymać agenta komunikowanie się z usługą Log Analytics?

Odp.: w System Center Operations Manager, Usuń komputer z listy Log Analytics zarządzanych komputerów. Programu Operations Manager zaktualizuje konfigurację agenta nie jest już raport do usługi Log Analytics. Dla agentów bezpośrednio podłączone do usługi Log Analytics, możesz je zatrzymać komunikację za pośrednictwem: Panelu sterowania, zabezpieczeń i ustawień, **Microsoft Monitoring Agent**.
W obszarze **Azure Log Analytics (OMS)** , Usuń wszystkie obszary robocze na liście.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Pyt.: Dlaczego otrzymuję błąd przy próbie przenieść mój obszar roboczy z jedną subskrypcją platformy Azure do innego?

Odp.: Aby przenieść obszar roboczy do innej subskrypcji lub grupy zasobów, musi najpierw odłączyć konto usługi Automation, w obszarze roboczym. Odłączanie konta usługi Automation wymaga usunięcia tych rozwiązań, jeśli są zainstalowane w obszarze roboczym: zarządzanie aktualizacjami, śledzenie zmian lub uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu są usuwane. Po usunięciu tych rozwiązań odłączanie konta usługi Automation, wybierając **połączone obszary robocze** w okienku po lewej stronie w usłudze Automation konta zasobu, a następnie kliknij przycisk **odłączanie obszaru roboczego** na Wstążce.
 > Usunięte rozwiązania konieczność ponownego zainstalowania w obszarze roboczym, a łącze automatyzacji do obszaru roboczego musi być przekształcone po przeniesieniu.

Upewnij się, że masz uprawnienia w obu subskrypcjach platformy Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Pyt.: Dlaczego otrzymuję błąd podczas próby można zaktualizować zapisanego wyszukiwania?

Odp.: należy dodać "element etag" w treści interfejsu API lub właściwości szablonu usługi Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Dane agenta
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>PYTANIE: Jak dużo danych można wysyłać za pośrednictwem agenta do usługi Log Analytics? Czy istnieje maksymalna ilość danych klienta?
A. Nie ma żadnego limitu ilości przekazywanych danych, zależy od wybranej opcji cennika lub rezerwacji zgodnie z rzeczywistym użyciem. Log Analytics obszar roboczy jest zaprojektowana w celu automatycznego skalowania w górę w celu obsługi woluminu pochodzącego od klienta — nawet w przypadku terabajtów dziennie. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/monitor/).

Agenta usługi Log Analytics zaprojektowano tak, aby upewnić się, że ma niewielkie rozmiary. Ilość danych w zależności od rozwiązania, które zostanie włączone. Można znaleźć szczegółowe informacje na temat ilości danych i wyświetlić podział według rozwiązania [użycia](../../azure-monitor/platform/data-usage.md) strony.

Aby uzyskać więcej informacji, możesz przeczytać [blog klienta](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) pokazujący wyniki po ocenie wykorzystania zasobów (wpływ) agenta log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>PYTANIE: Jaka przepustowość sieci jest używany przez program Microsoft Management Agent (MMA) podczas wysyłania danych do usługi Log Analytics?

A. Przepustowość jest funkcja na ilość wysyłanych danych. Dane są kompresowane podczas ich przesyłania przez sieć.

### <a name="q-how-much-data-is-sent-per-agent"></a>PYTANIE: Jak dużo danych jest wysyłany do jednego agenta?

A. Ilość danych wysyłanych na agenta zależy od:

* Rozwiązania, które mają włączone
* Liczba dzienniki i liczniki wydajności są zbierane
* Ilość danych w dziennikach

Ogólne użycie jest pokazywane w [użycia](../../azure-monitor/platform/data-usage.md) strony.

Dla komputerów, które można uruchomić agenta danych o komunikacji sieciowej użyj następującego zapytania, aby zobaczyć, jak dużo danych jest wysyłanych:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Następne kroki

[Rozpocznij pracę z Azure monitor](../../azure-monitor/overview.md) , aby dowiedzieć się więcej na temat log Analytics i rozpocząć pracę w ciągu kilku minut.
