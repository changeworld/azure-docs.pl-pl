---
title: Log Analytics — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 08e915354df4f4aa1d9a183e78cbad47460b8d37
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356222"
---
# <a name="log-analytics-faq"></a>Log Analytics — często zadawane pytania

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ta FAQ firmy Microsoft znajduje się lista często zadawane pytania dotyczące usługi Log Analytics na platformie Microsoft Azure. Jeśli masz dodatkowe pytania dotyczące usługi Log Analytics, przejdź do strony [forum dyskusyjne](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.


## <a name="new-logs-experience"></a>Nowe środowisko dzienników

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>Pyt.: Jaka jest różnica między nowe środowisko dzienniki i usługi Log Analytics?

Odp.: Są one tak samo. [Usługa log Analytics jest integrowany jako funkcję w usłudze Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) zapewnienie bardziej jednolite środowisko monitorowania. Nowe środowisko dzienników w usłudze Azure Monitor jest dokładnie taka sama jak zapytań usługi Log Analytics, które już masz doświadczenie z wielu klientów.

### <a name="q-can-i-still-use-log-search"></a>Pyt.: Można nadal korzystać z wyszukiwania w dziennikach? 

Odp.: Wyszukiwanie w dzienniku aktualnie jest nadal dostępny w portalu pakietu OMS i w witrynie Azure portal w obszarze nazwy **dzienniki (wersja klasyczna)** . Portal pakietu OMS zostanie oficjalnie wycofana 15 stycznia 2019 r. Klasyczne środowisko dzienników w witrynie Azure portal zostaną stopniowo wycofane i zastąpione nowe środowisko dzienniki. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>PYTANIE: Można nadal korzystać z portalu Advanced Analytics? 
Nowe środowisko dzienników w witrynie Azure portal jest oparty na portalu usługi analiza zaawansowana, ale jest nadal dostępny spoza witryny Azure portal. Plan wycofywania tego zewnętrznego portalu zostanie ogłoszona wkrótce.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>PYTANIE: Dlaczego nie można wyświetlić Eksplorator zapytań i Zapisz przycisków w nowym środowisku dzienniki?

**Eksplorator zapytań**, **Zapisz** i **ustawić Alert** przyciski są niedostępne podczas eksplorowania dzienniki w kontekście określonego zasobu. Aby tworzyć alerty, Zapisz lub załadować zapytania, dzienniki musi należeć do obszaru roboczego zakresu. Aby otworzyć dzienniki w kontekście obszaru roboczego, wybierz **wszystkich usług** > **Monitor** > **dzienniki**. Wybrano ostatni używanych obszaru roboczego, ale można wybrać inny obszar roboczy. Zobacz [wyświetlania i analizowania danych w usłudze Log Analytics](../log-query/portals.md) Aby uzyskać więcej informacji.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>PYTANIE: Jak wyodrębnić pól niestandardowych w nowym środowisku dzienniki? 

Odp.: Pola niestandardowe wyodrębniania są obecnie obsługiwane w klasycznym korzystaj z dzienników. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>PYTANIE: Gdzie znaleźć widok listy w nowe dzienniki? 

Odp.: Widok listy nie jest dostępna w nowe dzienniki. Strzałka w lewo każdy rekord w tabeli wyników nie istnieje. Strzałki można otworzyć szczegóły dla określonego rekordu. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>PYTANIE: Po uruchomieniu kwerendy, listę sugerowanych filtrów są dostępne. Jak można sprawdzić filtry? 

Odp.: W okienku po lewej stronie, aby wyświetlić podgląd nowego wdrożenia filtrów, kliknij przycisk "Filtry". Teraz jest oparta na wynik Twojego pełnego ustawić zamiast jest ograniczona przez limit 10 000 rekordów interfejsu użytkownika. Obecnie jest lista najpopularniejszych filtry i 10 najczęściej dla każdego filtru. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>PYTANIE: Dlaczego otrzymuję błąd: "Zarejestruj dostawcę zasobów"Microsoft.Insights"dla tej subskrypcji. Aby włączyć to zapytanie" w dziennikach, po przechodzenia do szczegółów z maszyny Wirtualnej? 

Odp.: Domyślnie automatycznie zarejestrowano wielu dostawców zasobów, jednak należy ręcznie zarejestrować niektórzy dostawcy zasobów. Pozwoli to na skonfigurowanie subskrypcji do pracy za pomocą dostawcy zasobów. Zakres do rejestracji jest zawsze subskrypcji. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>PYTANIE: Dlaczego mogę komunikat o błędzie nie dostępu do błędu podczas uzyskiwania dostępu do dzienników ze strony maszyny Wirtualnej? 

Odp.: Aby wyświetlić dzienniki maszyny Wirtualnej, musisz być nadane uprawnienia do odczytu do obszarów roboczych, które są przechowywane dzienniki maszyny Wirtualnej. W takich przypadkach administrator musi udzielić użytkownikowi uprawnień na platformie Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>PYTANIE: Dlaczego mogę można skorzystać z mojego obszaru roboczego w portalu pakietu OMS, ale jest zgłaszany błąd "nie masz dostępu" w witrynie Azure portal?  

Odp.: Aby uzyskać dostęp do obszaru roboczego na platformie Azure, musi mieć przypisane uprawnienia platformy Azure. Istnieją przypadki, gdy nie masz wystarczających uprawnień dostępu. W takich przypadkach administrator musi przyznać uprawnienia w Azure.See [portalu pakietu OMS na platformę Azure](oms-portal-transition.md) Aby uzyskać więcej informacji.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>PYTANIE: Dlaczego nie nie widzę Projektant widoków zapisu w dziennikach? 
Odp.: Projektant widoków jest dostępna tylko w dziennikach dla użytkowników przypisanych z uprawnieniami współautora lub nowszej.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>PYTANIE: Można nadal korzystać z portalu usługi analiza spoza platformy Azure?
A. Tak, stronie dzienniki na platformie Azure i portalu analizy zaawansowanej opierają się na ten sam kod. Usługa log Analytics jest integrowany jako funkcję w usłudze Azure Monitor, aby zapewnić bardziej ujednoliconego środowiska monitorowania. Nadal możesz uzyskiwać dostęp do portalu analizy przy użyciu adresu URL: https:\/\/portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}.



## <a name="general"></a>Ogólne

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>PYTANIE: Jak mogę sprawdzić Mój widoki i rozwiązania w witrynie Azure portal? 

Odp.: Lista widoków i zainstalowane rozwiązania są dostępne w witrynie Azure portal. Kliknij opcję **Wszystkie usługi**. Na liście zasobów wybierz **Monitor**, następnie kliknij przycisk **... Więcej**. Wybrano ostatni używanych obszaru roboczego, ale można wybrać inny obszar roboczy. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>PYTANIE: Dlaczego nie mogę utworzyć obszary robocze w regionie zachodnio-środkowe stany USA? 

Odp.: W tym regionie osiągnięto tymczasowy limit pojemności. Ten limit jest planowana skierowanych do końca września maja 2019 r.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>PYTANIE: Usługa Log Analytics używa ten sam agent Azure Security Center?

Odp.: Na początku czerwca 2017 roku usługa Azure Security Center zaczęli korzystać z funkcji programu Microsoft Monitoring Agent do gromadzenia i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center platformy migracji — często zadawane pytania](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>PYTANIE: Jakie są sprawdzane przez usługi AD i rozwiązania SQL Assessment?

Odp.: Następujące zapytanie zawiera opis wszystkich testów, które aktualnie wykonywane:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Następnie można wyeksportować wyniki do programu Excel w celu dalszego przeglądu.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>PYTANIE: Dlaczego warto zobaczyć coś innego niż pakietu OMS w konsoli programu System Center Operations Manager

Odp.: Zależności od tego, jakie Update Rollup programu Operations Manager znajdują się na, może być wyświetlany węzeł *System Center Advisor*, *usługi Operational Insights*, lub *usługi Log Analytics*.

Aktualizacja ciąg tekstu *OMS* znajduje się w pakiecie administracyjnym, należy zaimportować ręcznie. Aby zobaczyć aktualny tekst i funkcjonalność, postępuj zgodnie z instrukcjami na najnowsze systemu Centrum Operations Manager aktualizacji pakietu zbiorczego wiedzy i Odśwież konsolę.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Pyt.: Czy istnieje — lokalną wersją usługi Log Analytics?

Odp.: Nie. Usługa log Analytics to usługa w chmurze skalowalne, która przetwarza i przechowuje duże ilości danych. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>PYTANIE: Jak rozwiązywać, jeśli nie jest już usługi Log Analytics zbiera dane?

Odp.: Dla subskrypcji i obszar roboczy utworzony przed 2 kwietnia 2018 r., która znajduje się na *bezpłatna* warstwy cenowej, jeśli więcej niż 500 MB danych zostanie wysłany w ciągu dnia, zatrzymuje zbieranie danych przez pozostałą część dnia. Osiągnięcia dziennego limitu jest typową przyczyną, usługi Log Analytics zatrzymuje proces zbierania danych lub danych prawdopodobnie brakuje.  

Usługa log Analytics tworzy zdarzenie typu *pulsu* i może służyć do określenia, jeśli zatrzymuje zbieranie danych. 

W polu wyszukiwania, aby sprawdzić, jeśli osiągnięcia dziennego limitu i brakujące dane, uruchom następujące zapytanie: `Heartbeat | summarize max(TimeGenerated)`

Aby sprawdzić określonego komputera, uruchom następujące zapytanie: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Po zatrzymaniu zbierania danych, w zależności od zakresu czasu wybranego, nie będą widzieć żadnych rekordów zwracanych.   

W poniższej tabeli opisano powody, dla których zatrzymuje zbieranie danych i zalecaną akcję, aby wznowić zbieranie danych:

| Zatrzymuje zbieranie danych z powodu                       | Aby wznowić zbieranie danych |
| -------------------------------------------------- | ----------------  |
| Osiągnięto limit bezpłatnych danych<sup>1</sup>       | Zaczekaj, aż do następnego miesiąca dla kolekcji do automatycznego ponownego uruchamiania lub<br> Zmień na płatną warstwę cenową |
| Subskrypcja platformy Azure jest w stanie wstrzymania ze względu na: <br> Bezpłatny okres próbny zakończył się <br> Azure — dostęp próbny wygasł <br> Co miesiąc limit wydatków osiągnięto (na przykład w ramach subskrypcji MSDN lub Visual Studio)                          | Konwertuj na płatną subskrypcję <br> Konwertuj na płatną subskrypcję <br> Usuń limit lub poczekaj na zresetowanie limitu |

<sup>1</sup> Jeśli obszar roboczy znajduje się na *bezpłatna* warstwy cenowej, możesz wysłać maksymalnie 500 MB danych dziennie do usługi. Po osiągnięciu dziennego limitu gromadzenie danych zatrzymuje się od następnego dnia. Dane wysłane podczas zatrzymania zbierania danych nie jest indeksowana i nie jest dostępna dla wyszukiwania. Po wznowieniu pracy zbierania danych, przetwarzanie odbywa się tylko w przypadku nowych danych wysyłane. 

Usługi log Analytics korzysta z czasu UTC, a każdy dzień rozpoczyna się o północy czasu UTC. Jeśli obszar roboczy osiągnie limit dzienny, przetwarzanie wznawia działanie po godzinie pierwszego dnia następnego UTC.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>PYTANIE: Jak mogę otrzymywać powiadomienia po zatrzymaniu zbierania danych?

Odp.: Wykonaj czynności opisane w [utworzyć nowego alertu dziennika](../../azure-monitor/platform/alerts-metric.md) zgłaszane po zatrzymaniu zbierania danych.

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

A. `Move-AzResource` Polecenie cmdlet pozwala na przechodzenie obszar roboczy usługi Log Analytics i konto usługi Automation z jedną subskrypcją platformy Azure do innego. Aby uzyskać więcej informacji, zobacz [AzResource przenoszenia](https://msdn.microsoft.com/library/mt652516.aspx).

Ta zmiana może również w witrynie Azure portal.

Nie można przenieść dane z jednego obszaru roboczego usługi Log Analytics do innego lub zmienić dane usługi Log Analytics są przechowywane w regionie.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Pyt.: Jak dodać usługi Log Analytics programu System Center Operations Manager?

Odp.:  Aktualizowanie do najnowszego pakietu zbiorczego aktualizacji i importowania pakietów administracyjnych umożliwia łączenie programu Operations Manager do usługi Log Analytics.

>[!NOTE]
>Połączenie programu Operations Manager do usługi Log Analytics jest dostępna tylko dla programu System Center Operations Manager 2012 z dodatkiem SP1 lub nowszy.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Pyt.: Jak można potwierdzić, że agent jest w stanie nawiązać połączenia z usługą Log Analytics?

Odp.: Aby upewnić się, że agent może komunikować się z usługą OMS, przejdź do: Kontrolowanie panelu, zabezpieczeń i ustawień, **Microsoft Monitoring Agent**.

W obszarze **Azure Log Analytics (OMS)** kartę, poszukaj zielony znacznik wyboru. Zielona ikona znacznika wyboru potwierdza, że agent jest w stanie komunikować się z usługą Azure.

Żółtą ikoną ostrzeżenia oznacza, że agent występują problemy z komunikacji z usługą Log Analytics. Jedną typową przyczyną jest to, że usługa Microsoft Monitoring Agent została zatrzymana. Aby ponownie uruchomić usługę, należy użyć Menedżera sterowania usługami.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Pyt.: Jak zatrzymać agenta komunikowanie się z usługą Log Analytics?

Odp.: W programie System Center Operations Manager należy usunąć komputer z listy zarządzanych komputerów pakietu OMS. Programu Operations Manager zaktualizuje konfigurację agenta nie jest już raport do usługi Log Analytics. Dla agentów bezpośrednio podłączone do usługi Log Analytics możesz zatrzymać je komunikację za pośrednictwem: Kontrolowanie panelu, zabezpieczeń i ustawień, **Microsoft Monitoring Agent**.
W obszarze **Azure Log Analytics (OMS)** , Usuń wszystkie obszary robocze na liście.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Pyt.: Dlaczego otrzymuję błąd przy próbie przenieść mój obszar roboczy z jedną subskrypcją platformy Azure do innego?

Odp.: Aby przenieść obszar roboczy do innej subskrypcji lub grupy zasobów, należy najpierw odłączyć konto usługi Automation, w obszarze roboczym. Odłączenie konta usługi Automation wymaga usunięcia tych rozwiązań, jeśli są zainstalowane w obszarze roboczym: Zarządzanie aktualizacjami, śledzenie zmian lub uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu są usuwane. Po usunięciu tych rozwiązań odłączanie konta usługi Automation, wybierając **połączone obszary robocze** w okienku po lewej stronie w usłudze Automation konta zasobu, a następnie kliknij przycisk **odłączanie obszaru roboczego** na Wstążce.
 > Usunięte rozwiązania konieczność ponownego zainstalowania w obszarze roboczym, a łącze automatyzacji do obszaru roboczego musi być przekształcone po przeniesieniu.

Upewnij się, że masz uprawnienia w obu subskrypcjach platformy Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>Pyt.: Dlaczego otrzymuję błąd podczas próby można zaktualizować zapisanego wyszukiwania?

Odp.: Należy dodać "element etag" w treści interfejsu API lub właściwości szablonu usługi Azure Resource Manager:
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
A. Bezpłatny plan Ustawia dzienny limit 500 MB danego obszaru roboczego. Plany w warstwach standardowa i premium nie mają limitu ilości danych, który zostanie przekazany. Jako usługa w chmurze, usługi Log Analytics jest przeznaczona do automatyczne skalowanie w górę do uchwytu woluminu pochodzące od klientów — nawet jeśli jest on terabajtów dziennie.

Agenta usługi Log Analytics zaprojektowano tak, aby upewnić się, że ma niewielkie rozmiary. Ilość danych w zależności od rozwiązania, które zostanie włączone. Można znaleźć szczegółowe informacje na temat ilości danych i wyświetlić podział według rozwiązania [użycia](../../azure-monitor/platform/data-usage.md) strony.

Aby uzyskać więcej informacji można znaleźć [blogu klienta](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) wyświetlanie ich wyników, po dokonaniu oceny wykorzystanie zasobów (zużycie) agenta pakietu OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>PYTANIE: Jaka przepustowość sieci jest używany przez program Microsoft Management Agent (MMA) podczas wysyłania danych do usługi Log Analytics?

A. Przepustowość jest funkcja na ilość wysyłanych danych. Dane są kompresowane podczas ich przesyłania przez sieć.

### <a name="q-how-much-data-is-sent-per-agent"></a>PYTANIE: Jak dużo danych jest wysyłany do jednego agenta?

A. Ilość danych wysyłanych na agenta zależy od:

* Rozwiązania, które mają włączone
* Liczba dzienniki i liczniki wydajności są zbierane
* Ilość danych w dziennikach

Bezpłatna warstwa cenowa jest dobrym sposobem na dołączanie kilka serwerów i miernika woluminu typowych danych. Ogólne użycie jest pokazywane w [użycia](../../azure-monitor/platform/data-usage.md) strony.

Dla komputerów, które można uruchomić agenta danych o komunikacji sieciowej użyj następującego zapytania, aby zobaczyć, jak dużo danych jest wysyłanych:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Kolejne kroki
* [Rozpoczynanie pracy z usługą Log Analytics](../../azure-monitor/overview.md) Dowiedz się więcej o usłudze Log Analytics i rozpocząć pracę w ciągu kilku minut.
