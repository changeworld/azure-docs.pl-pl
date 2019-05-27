---
title: Monitorowanie kondycji maszyny wirtualnej z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak poznanie kondycji maszyny wirtualnej, a system operacyjny z usługą Azure Monitor dla maszyn wirtualnych.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 9fa76c9637a6dcdca48bf45e8ee2aa9305a4f64f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130454"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Poznanie kondycji usługi Azure virtual machines

Azure obejmuje wiele usług, indywidualnie wykonywania określonej roli lub zadania w obszarze monitorowania, które nie zapewniały perspektywy szczegółowe kondycji systemu operacyjnego, hostowane na maszynach wirtualnych platformy Azure. Podczas monitorowania może się w różnych warunkach, za pomocą usługi Azure Monitor, nie został on zaprojektowany do modelowania i reprezentują kondycji podstawowych składników lub ogólną kondycję maszyny wirtualnej. Dzięki usłudze Azure Monitor dla funkcji kondycji maszyn wirtualnych proaktywnie monitoruje dostępność i wydajność, Windows lub Linux systemu operacyjnego gościa za pomocą modelu, reprezentujące najważniejsze składniki oraz ich relacji, kryteria, które określa, jak mierzyć kondycję tych składniki i wysyłania alertów, gdy zostanie wykryty warunek złej kondycji.  

Wyświetlanie ogólną kondycję maszyn wirtualnych platformy Azure i bazowych systemu operacyjnego, można zaobserwować z dwóch perspektyw z usługą Azure Monitor dla kondycji maszyn wirtualnych, bezpośrednio z maszyny wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów z usługi Azure Monitor.

Ten artykuł pomoże Ci zrozumieć, jak można szybko ocenić, badania i rozwiązywania wykrytych problemów kondycji.

Aby uzyskać informacje o konfigurowaniu usługi Azure Monitor do maszyn wirtualnych, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Szczegóły konfiguracji monitorowania

W tej sekcji opisano kryteria kondycji domyślne, które są zdefiniowane w celu monitorowania Windows Azure i maszyn wirtualnych systemu Linux. Wszystkie kryteria kondycji wstępnie skonfigurowanych alert po spełnieniu warunku złej kondycji. 

### <a name="windows-vms"></a>Maszyny wirtualne z systemem Windows

- Dostępne megabajty pamięci 
- Przeciętna sekundy na zapis (dysk logiczny)
- Przeciętna sekundy na zapis (dysk)
- Średnia dysku logicznego sekund na odczyt
- Średnia dysku logicznego sekundy na Transfer
- Przeciętna sekund na odczyt
- Przeciętna sekundy na Transfer
- Bieżąca długość kolejki dysku (dysk logiczny)
- Bieżąca długość kolejki dysku (dysk)
- Procent czasu bezczynności dysku
- Błąd systemu plików lub uszkodzeniem
- Wolne miejsce na dysku logicznym (%) Niska
- Mała ilość wolnego miejsca (MB) dysku logicznego
- Procent czasu bezczynności dysku logicznego
- Strony pamięci na sekundę
- Procent przepustowości używane odczytu
- Procent przepustowości używane łącznie
- Procent przepustowości używane zapisu
- Wartość procentowa rozmiaru przydzielonej pamięci w użyciu
- Procent czasu bezczynności dysku
- Kondycja usługi klienta DHCP
- Kondycja usługi klienta DNS
- Kondycja usługi RPC
- Kondycja usługi serwera
- Procent użycia łączny czas Procesora
- Kondycja usługi systemu Windows dziennik zdarzeń
- Kondycja usługi zapory Windows
- Kondycja usługi zdalnego zarządzania Windows

### <a name="linux-vms"></a>Maszyny wirtualne z systemem Linux
- Średnia szybkość transferu Dysku w s/Transfer 
- Średnia szybkość transferu Czas dysku w s/Odczyt 
- Średnia szybkość transferu Dysku w s/Zapis 
- Kondycja dysku
- Wolne miejsce na dysku logicznym
- Wolne miejsce na dysku logicznym %
- Dysk logiczny % wolnych węzłów i
- Kondycja karty sieciowej
- Łączna liczba procentowa czasu procesora
- System operacyjny dostępne megabajty pamięci

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Wprowadzenie do środowiska kondycji

Przed zagłębieniem się w funkcji kondycji dla pojedynczej maszyny wirtualnej lub grupy maszyn wirtualnych, ważne jest, że firma Microsoft zapewnia krótkie wprowadzenie, aby zrozumieć sposób prezentowania informacji i reprezentują wizualizacji.  

### <a name="view-health-directly-from-a-virtual-machine"></a>Wyświetl kondycję bezpośrednio z maszyny wirtualnej 

Aby wyświetlić kondycję maszyn wirtualnych platformy Azure, wybierz **Insights (wersja zapoznawcza)** z okienka po lewej stronie maszyny wirtualnej. Na stronie maszyny Wirtualnej insights **kondycji** jest domyślnie otwarty i wyświetla widok kondycji maszyny wirtualnej.  

![Usługa Azure Monitor, aby uzyskać przegląd kondycji maszyn wirtualnych z wybranych maszyn wirtualnych](./media/vminsights-health/vminsights-directvm-health.png)

Na **kondycji** kartę w sekcji **kondycji maszyny Wirtualnej gościa**, w tabeli przedstawiono bieżący stan kondycji maszyny wirtualnej i całkowita liczba alertów dotyczących kondycji maszyny Wirtualnej zgłoszone przez składnik w złej kondycji. Aby uzyskać więcej informacji, zobacz [alerty](#alerts) uzyskać dodatkowe szczegóły dotyczące zgłaszania alertów środowiska.  

Stany kondycji zdefiniowane dla maszyny Wirtualnej zostały opisane w poniższej tabeli: 

|Ikona |Stan kondycji |Znaczenie |
|-----|-------------|---------------|
| |W dobrej kondycji |Stan kondycji jest w dobrej kondycji, jeśli znajduje się on w zdefiniowanych zdrowotnych, wskazując nie wykryto dla maszyny Wirtualnej problemów i działa zgodnie z potrzebami. Za pomocą zbiorczego monitora nadrzędnego telefoniczny ustala kondycję i odzwierciedla najlepszego przypadku lub najgorszy stan elementu podrzędnego.|
| |Krytyczne |Stan kondycji jest krytyczny, jeśli nie znajduje się w stanie kondycji zdefiniowanych, wskazujący, że co najmniej jeden krytyczne wykryto problemy, które należy uwzględnić w celu przywrócenia normalnej funkcjonalności. Za pomocą zbiorczego monitora nadrzędnego telefoniczny ustala kondycję i odzwierciedla najlepszego przypadku lub najgorszy stan elementu podrzędnego.|
| |Ostrzeżenie |Stan kondycji jest ostrzeżenie, jeśli jest między dwiema wartościami progowymi kondycji zdefiniowany warunek, gdzie jeden wskazuje *ostrzeżenie* wskazuje stan, a druga *krytyczny* stanu (trzy wartości progowe stanu kondycji może można skonfigurować), gdy problem niekrytyczne wykrycia lub co może spowodować problemy krytyczne, jeśli nie uda się rozwiązać. Z listą nadrzędny monitor, jeśli jeden lub więcej podrzędnych jest w stanie ostrzeżenia, a następnie element nadrzędny będzie odzwierciedlać *ostrzeżenie* stanu. Jeśli jest elementem podrzędnym, który znajduje się w *krytyczny* i inny podrzędny w *ostrzeżenie* stanu zbiorczego nadrzędnego wyświetli stan kondycji *krytyczny*.|
| |Nieznana |Stan kondycji jest *nieznany* kiedy nie można obliczyć z kilku powodów. Zobacz następujący przypis <sup>1</sup> dodatkowe szczegóły i możliwe rozwiązania, aby je rozwiązać. |

<sup>1</sup> nieznany stan kondycji jest spowodowane przez następujące problemy:

- Ponownie skonfigurować agenta, a nie jest już określony raporty do obszaru roboczego, gdy włączono usługi Azure Monitor dla maszyn wirtualnych. Aby skonfigurować agenta w celu przekazywania informacji do obszaru roboczego, zobacz [Dodawanie lub usuwanie obszaru roboczego](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Maszyna wirtualna została usunięta.
- Obszar roboczy, skojarzone z usługą Azure Monitor dla maszyn wirtualnych zostaną usunięte. Aby odzyskać obszaru roboczego, w przypadku planu Premier możesz otworzyć żądania pomocy technicznej za pomocą korzyści z pomocy technicznej [Premier](https://premier.microsoft.com/).
- Zależności rozwiązania zostały usunięte. Aby ponownie włączyć ServiceMap i InfrastructureInsights rozwiązania w Twoim obszarze roboczym usługi Log Analytics, można ponownie zainstalować przy użyciu [szablonu usługi Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions) , ma pod warunkiem lub za pomocą opcji konfigurowania obszaru roboczego na znaleziono Rozpoczynanie pracy kartę.
- Maszyny Wirtualnej zostało zatrzymane.
- Usługi platformy Azure maszyna wirtualna jest niedostępna lub obecnie jest wykonywana konserwacja.
- Obszar roboczy [danych dotyczących dziennego ani nie ogranicza przechowywania](../platform/manage-cost-storage.md) jest spełniony.

Wybieranie **Wyświetl diagnostykę kondycji** spowoduje otwarcie strony, przedstawiający wszystkie składniki maszyny Wirtualnej, kryteriów skojarzonej kondycji, zmian stanu i innych istotnych problemów napotykanych przez monitorowanie składników powiązane z maszyną wirtualną. Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji](#health-diagnostics). 

W obszarze **kondycja składnika** sekcji tabeli pojawi się stan kondycji zbiorczy kategorii podstawowej wydajności monitorowanych przez kryteria kondycji dla tych obszarów, w szczególności **Procesora**,  **Pamięć**, **dysku**, i **sieci**.  Wybranie któregokolwiek ze składników powoduje otwarcie strony z listą wszystkich kryterium kondycji poszczególnych monitorowania aspekty tego składnika i stan kondycji odpowiednimi każdego z nich.  

Podczas uzyskiwania dostępu do kondycji, w maszynie Wirtualnej platformy Azure z systemem operacyjnym Windows, stan kondycji górnej pięciu podstawowych Windows services są wyświetlane w sekcji **podstawowych usług kondycji**.  Wybranie jednej z usług powoduje otwarcie strony z listą kryteria kondycji monitorowania tego składnika, a jej stan kondycji.  Kliknięcie nazwy kryteria kondycji zostanie otwarty w okienku właściwości, a w tym miejscu możesz przejrzeć szczegóły konfiguracji, w tym przypadku kryteria kondycji ma odpowiedni alert usługi Azure Monitor zdefiniowane. Aby dowiedzieć się więcej, zobacz [Diagnostyka kondycji i Praca z kryteria kondycji](#health-diagnostics).  

### <a name="aggregate-virtual-machine-perspective"></a>Perspektywa agregacji maszyny wirtualnej

Aby wyświetlić kondycji zbieranie danych dla wszystkich maszyn wirtualnych w grupie zasobów, z listy nawigacji w portalu wybierz **usługi Azure Monitor** , a następnie wybierz **maszyny wirtualne (wersja zapoznawcza)**.  

![Maszyna wirtualna analizy, monitorowanie widoku z usługi Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Z **subskrypcji** i **grupy zasobów** list rozwijanych wybierz grupę odpowiedni zasób, który zawiera maszyny wirtualne powiązany z grupą, aby wyświetlić ich stan kondycji zgłaszane.  Wybór tylko ma zastosowanie funkcja Kondycja i nie jest przenoszone wydajności lub mapy.

Na **kondycji** kartę, jesteś w stanie się następujące czynności:

* Ile maszyn wirtualnych znajdują się w stan krytyczny lub w złej kondycji, a ile jest w dobrej kondycji lub nie przesyłanie danych (nazywane stan nieznany)?
* Którą maszyny wirtualne według systemu operacyjnego (OS) zgłasza stan złej kondycji i ile?
* Ile maszyn wirtualnych jest w złej kondycji ze względu na problem wykryty z procesora, dysku, pamięć lub karty sieciowej, pogrupowane według stanu kondycji?  
* Ile maszyn wirtualnych jest w złej kondycji ze względu na problem wykryty core systemu operacyjnego usługi pogrupowane według stanu kondycji?

W tym miejscu można szybko zidentyfikować najważniejsze problemy krytyczne, wykrytych przez kryteria kondycji Proaktywne monitorowanie maszyny Wirtualnej i przejrzyj szczegóły alertu kondycji maszyny Wirtualnej i skojarzone bazy wiedzy przeznaczona do pomagają w diagnostykę i korygowanie problemu.  Wybierz dowolne ważności, aby otworzyć [wszystkie alerty](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) strona jest filtrowana według tego ważności.

**Dystrybucji maszyny Wirtualnej przez system operacyjny** lista maszyn wirtualnych w rozbiciu na wersję Windows lub dystrybucja systemu Linux, wraz z ich wersji. W każdej kategorii systemu operacyjnego maszyny wirtualne są podzielone dalsze na podstawie kondycji maszyny Wirtualnej. 

![Szczegółowe informacje z maszyny Wirtualnej maszyny wirtualnej dystrybucji perspektywy](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Możesz kliknąć dowolny element kolumny — **liczba maszyn wirtualnych**, **krytyczny**, **ostrzeżenie**, **dobra kondycja** lub **nieznany** do Przechodzenie do szczegółów w **maszyn wirtualnych** strony wyświetlenie listy filtrowane wyniki pasujące kolumny wybranej. Na przykład, jeśli chcemy przejrzeć wszystkie uruchomione maszyny wirtualne **Red Hat Enterprise Linux w wersji 7.5**, kliknij pozycję **liczba maszyn wirtualnych** wartość dla tego systemu operacyjnego i otworzy następującą stronę, wyświetlanie listy maszyn wirtualnych zgodnych Ten filtr i ich stan kondycji obecnie znane.  

![Przykład kondycji zbiorczej maszyn wirtualnych Red Hat systemu Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Na **maszyn wirtualnych** strony, jeśli wybrana nazwa maszyny Wirtualnej w kolumnie **Nazwa_maszyny_wirtualnej**, nastąpi przekierowanie do strony wystąpienia maszyny Wirtualnej z bardziej szczegółowymi informacjami, alertów oraz problemów z kondycją kryteria ustaliła, że są wpływ na wybranej maszyny Wirtualnej.  W tym miejscu można filtrować Szczegóły stanu kondycji, klikając **stan kondycji** ikony w lewym górnym rogu strony Aby sprawdzić składniki, które są w złej kondycji lub można wyświetlić alerty dotyczące kondycji maszyny Wirtualnej zgłoszone przez składnik w złej kondycji pogrupowane według ważności alertu.    

W widoku listy maszyn wirtualnych, klikając nazwę maszyny Wirtualnej spowoduje otwarcie **kondycji** strony, w tym wybrane podobnie maszyny Wirtualnej, jak w przypadku wybrania **Insights (wersja zapoznawcza)** z maszyny Wirtualnej bezpośrednio.

![Szczegółowe maszyny Wirtualnej informacje dotyczące wybranej maszyny wirtualnej platformy Azure](./media/vminsights-health/vminsights-directvm-health.png)

W tym miejscu pokazuje pakiet zbiorczy **stan kondycji** dla maszyny wirtualnej i **alerty**skategoryzowane według ważności, którą reprezentują wywoływane, gdy stan kondycji zmieni się z kondycji na nieprawidłowy dla alerty dotyczące kondycji maszyny Wirtualnej kryteria kondycji.  Wybieranie **maszyn wirtualnych w stan krytyczny** spowoduje otwarcie strony z listą przynajmniej jednej maszyny wirtualnej, które znajdują się w krytycznym stanie kondycji.  Klikając stan kondycji dla jednej z maszyn wirtualnych, na liście będą wyświetlane **Diagnostyka kondycji** widoku maszyny wirtualnej.  W tym miejscu znajdziesz kryteria kondycji, które jest odzwierciedlający problem z kondycją stanu. Gdy **Diagnostyka kondycji** zostanie otwarta strona, pokazuje wszystkie składniki maszyny Wirtualnej i ich kryteria kondycji skojarzone z bieżącym stanem kondycji. Aby uzyskać więcej informacji, zobacz [kondycji diagnostycznych](#health-diagnostics).  

Wybieranie **wyświetlić wszystkie kryteria kondycji** otwiera stronę, która pokazuje listę dostępnych za pomocą tej funkcji kryteria kondycji.  Dane można dodatkowo filtrowane na podstawie następujących opcji:

* **Typ** — istnieją trzy rodzaje kondycji typów kryteria w celu oceny warunków, w którym można rzutować ogólnej kondycji stanu monitorowanych maszyny Wirtualnej.  
    a. **Jednostka** — mierzy pewien aspekt maszyny wirtualnej. Ten typ kryteriów kondycji może być sprawdzenie licznika wydajności w celu ustalenia wydajności składnika, uruchomienie skryptu do przeprowadzenia transakcji syntetycznej lub poszukaj zdarzenia wskazującego błąd.  Domyślnie filtr jest równa jednostki.  
    b. **Zależność** — udostępnia informacje zbiorcze o kondycji, między różnymi jednostkami. Te kryteria kondycji temu kondycja jednostki, aby była zależna od kondycji obiektu innego rodzaju jednostki, która opiera się na powodzenie operacji.  
    c. **Łączny** — zapewnia zbiorczy stan kondycji podobne kryteria kondycji. Kryterium kondycji jednostki i zależności będą zazwyczaj konfigurowane w obszarze kryterium kondycji agregacji. Oprócz zapewnienia lepszej ogólnej organizacji wielu kryteriów kondycję różnych przeznaczone dla jednostki, kryterium agregacji kondycji udostępnia unikatowy stan kondycji dla różnych kategorii obiektów.

* **Kategoria** — typ kryteria kondycji używane do pogrupowania kryteriów typu podobne do celów raportowania.  Są one **dostępności** lub **wydajności**.

Możesz przejść do dalszych szczegółów, aby zobaczyć, które wystąpienia jest w złej kondycji, klikając wartość w polu **składnik w złej kondycji** kolumny.  Na stronie tabeli przedstawiono składniki, które znajdują się w krytycznym stanie kondycji.    

## <a name="health-diagnostics"></a>Diagnostyka kondycji

**Diagnostyka kondycji** strona pozwala umożliwia zwizualizowanie Model kondycji maszyny Wirtualnej, wyświetlanie listy wszystkich składników maszyn wirtualnych, skojarzone kryteria kondycji, zmian stanu i innych istotnych problemów identyfikowane za pomocą monitorowanych składników powiązane do maszyny Wirtualnej.

![Przykład strony Diagnostyka kondycji dla maszyny Wirtualnej](./media/vminsights-health/health-diagnostics-page-01.png)

Diagnostyka kondycji można uruchomić w następujący sposób.

* Przez pakiet zbiorczy stan kondycji dla wszystkich maszyn wirtualnych z agregacji perspektywy maszyn wirtualnych w usłudze Azure Monitor.  Na **kondycji** kliknij na ikonę **krytyczny**, **ostrzeżenie**, **dobra kondycja**, lub **nieznany** stan kondycji, w sekcji **kondycji maszyny Wirtualnej gościa** i przejdź do strony, która zawiera listę wszystkich maszyn wirtualnych dopasowywania kategorii filtrowane.  Klikając pozycję na wartość w **stan kondycji** kolumny spowoduje to otwarcie diagnostyki kondycji ograniczone do tej konkretnej maszyny Wirtualnej.      

* Według systemu operacyjnego z agregacji perspektywy maszyn wirtualnych w usłudze Azure Monitor. W obszarze **dystrybucji maszyny Wirtualnej**, wybierając dowolną z wartości w kolumnie zostanie otwarty **maszyn wirtualnych** strony i powrócić do listy w tabeli dopasowywania kategorii filtrowane.  Kliknięcie przycisku wartości w obszarze **stan kondycji** kolumny otwiera kondycji Diagnostyka dla wybranej maszyny Wirtualnej.    
 
* Z maszyny Wirtualnej w usłudze Azure Monitor dla maszyn wirtualnych gościa **kondycji** kartę, wybierając **Wyświetl diagnostykę kondycji** 

Diagnostyka kondycji porządkuje informacje o kondycji na następujące kategorie: 

* Dostępność
* Wydajność
 
Wszystkie kryteria kondycji zdefiniowane dla określonego składnika, takich jak dysk logiczny procesor CPU, itp. mogą być wyświetlane bez filtrowania na dwie kategorie, (które znajduje się całej wszystkie kryteria) lub filtrować wyniki według kategorii albo wybierając **dostępności**  lub **wydajności** opcje na stronie. Ponadto kategorii kryteria są widoczne obok niego w **kryteria kondycji** kolumny. Jeśli kryteria nie odpowiada wybranej kategorii, będzie wyświetlany komunikat **kryteria kondycji, nie jest dostępne dla wybranej kategorii** w **kryteria kondycji** kolumny.  

Stan kryteria kondycji jest zdefiniowana za pomocą jednej z czterech stanów — *krytyczny*, *ostrzeżenie*, *dobra kondycja*, i *nieznany*. Pierwsze trzy są konfigurowalne, co oznacza, można zmodyfikować wartości progowe monitorów bezpośrednio z poziomu okienka konfiguracji kryteria kondycji lub za pomocą interfejsu API REST usługi Azure Monitor [zaktualizować monitor operacji](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Nieznany* nie jest konfigurowalne i zarezerwowane dla konkretnych scenariuszy.  

Strona Diagnostyka kondycji zawiera trzema głównymi sekcjami:

* Model składnika 
* Kryteria kondycji
* Zmiany stanu 

![Części strony diagnostyki kondycji](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Model składników

Kolumna skrajnie po lewej stronie Diagnostyka kondycji jest model składnika. Wszystkie składniki, które są skojarzone z maszyną Wirtualną, są wyświetlane w tej kolumnie, wraz z ich bieżącym stanem kondycji. 

W poniższym przykładzie składniki odkryte to dysk, dysk logiczny w systemie, procesora, pamięci i systemu operacyjnego. Wiele wystąpień tych składników są odnajdywane i wyświetlane w tej kolumnie. Na przykład na poniższym obrazie przedstawiono, że maszyna wirtualna ma dwa wystąpienia dysku logicznego — C: i D:, które są w dobrej kondycji.  

![Przykładowy model składnika prezentowane w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kryteria kondycji

W środkowej kolumnie na stronie Diagnostyka kondycji jest **kryteria kondycji** kolumny. Model kondycji zdefiniowane dla maszyny Wirtualnej jest wyświetlany w formie drzewa hierarchicznego. Model kondycji dla maszyny Wirtualnej składa się z jednostek i kryteria kondycji agregacji.  

![Kryteria kondycji przykład przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kryterium kondycji mierzy kondycję monitorowanych wystąpień przy użyciu kryteriów, które mogą być wartość progową, stan jednostki itp. Kryterium kondycji ma dwa lub trzy progi stanu kondycji można skonfigurować zgodnie z wcześniejszym opisem. W dowolnym danym momencie kryterium kondycji może być tylko jednego ze swoich potencjalnych stanów. 

Ogólna kondycja obiektu docelowego jest określany przez kondycji każdego z jego kryteria kondycji zdefiniowane w modelu kondycji. Jest kombinacją kryteria kondycji ukierunkowanych bezpośrednio na elemencie docelowym kryteria kondycji celem rzutowanie do elementu docelowego przy użyciu kryterium agregacji kondycji składników. Taka hierarchia jest zaprezentowana w **kryteria kondycji** sekcji na stronie Diagnostyka kondycji. W zasadach zestawienia kondycji jest częścią konfiguracji kryteria kondycji agregacji (domyślnym ustawieniem jest *najgorszy z*). Można znaleźć listy domyślny zestaw kryteria kondycji uruchomiona w ramach tej funkcji w sekcji [szczegóły konfiguracji monitorowania](#monitoring-configuration-details), można użyć interfejsu API REST usługi Azure Monitor [monitorowanie wystąpienia — listy według zasobu Operacja](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) w celu uzyskania listy wszystkich kryteria kondycji i jego szczegółową konfigurację działających w odniesieniu do zasobów maszyny Wirtualnej platformy Azure.  

**Jednostka** typ kryteria kondycji może mieć ich konfiguracji, modyfikować, klikając łącze wielokropka, aby znacznie rogu i wybierając opcję **Pokaż szczegóły** aby otworzyć okienko konfiguracji. 

![Konfigurowanie przykładu kryteria kondycji](./media/vminsights-health/health-diagnostics-vm-example-02.png)

W okienku Konfiguracja kondycji wybranych kryteriów, korzystając z przykładu **średni sekund na zapisu dysku**, przekroczyła wartość progową, może być skonfigurowany z inną wartością liczbową. Monitor dwustanowy, czyli jedynie zmian wprowadzonych w dobrej kondycji na ostrzeżenie jest. Innego kryterium kondycji mogą być trzy stany, w którym można skonfigurować wartość próg ostrzegawczy i krytyczny stan kondycji. Można również zmodyfikować wartości progowej, przy użyciu interfejsu API REST usługi Azure Monitor [zaktualizować monitor operacji](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Trwa stosowanie zmian konfiguracji kryteria kondycji do jednego wystąpienia jest stosowany do wszystkich monitorowanych wystąpień.  Na przykład w przypadku wybrania **dysku D:-1** i modyfikować **średni sekund na zapisu dysku** próg, nie dotyczy tylko tego wystąpienia, ale wszystkie wystąpienia dysku odnalezione i monitorowane na maszynie Wirtualnej.
>

![Konfigurowanie kryteria kondycji w przykładzie monitora jednostki](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Jeśli chcesz dowiedzieć się więcej na temat wskaźnika kondycji, artykuły merytoryczne uwzględniono pomóc w zidentyfikowaniu problemów, przyczyny i rozwiązania. Kliknij pozycję **służy do wyświetlania informacji** łącze na stronie zostanie otwarta nowa karta w przeglądarce, przedstawiający artykułu merytorycznego określone. W dowolnym momencie można przejrzeć wszystkie artykuły wiedzy kryterium kondycji dołączone do usługi Azure Monitor kondycji maszyn wirtualnych funkcji [tutaj](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Zmiany stanu

Kolumna najdalej z prawej strony na stronie Diagnostyka kondycji jest **zmiany stanu**. Wyświetla listę wszystkich zmian stanu skojarzonych z kryteria kondycji, które wybrano w **kryteria kondycji** sekcji lub Zmień stan maszyny wirtualnej w przypadku maszyny Wirtualnej została wybrana w zaufanym **składnika modelu** lub **Kryteria kondycji** kolumnie tabeli. 

![Zmiany stanu przykład przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Ta sekcja obejmuje kryteria kondycji i czasu skojarzone, posortowane według najnowszy stan u góry.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Skojarzenie modelu składników, kryteria kondycji i stan zmiany kolumn 

Trzy kolumny są powiązane ze sobą. Po wybraniu odnalezione wystąpienia w **składnika modelu** sekcji **kryteria kondycji** sekcji jest filtrowana w celu widoku składnika i odpowiednio **zmianę stanu**Zaktualizowano sekcję na podstawie kryteriów wybranych kondycji. 

![Przykład wybierania monitorowanych wystąpień i wyniki](./media/vminsights-health/health-diagnostics-vm-example-01.png)

W powyższym przykładzie po wybraniu **dysk — 1 D:**, drzewa kryteria kondycji jest filtrowana w celu **dysk — 1 D:**. **Zmiany stanu** kolumna pokazuje zmiany stanu, w oparciu o dostępności **dysk — 1 D:**. 

Aby wyświetlić stan kondycji zaktualizowane, można odświeżyć stronę diagnostyki kondycji, klikając **Odśwież** łącza.  W przypadku aktualizacji do stanu kondycji kryterium kondycji na podstawie wstępnie zdefiniowanych interwału sondowania, to zadanie pozwala uniknąć oczekiwania i odzwierciedla najnowszy stan kondycji.  **Kryteria kondycji** filtr zezwala na do zakresu wyniki oparte na stanie kondycji wybranego - *dobra kondycja*, *ostrzeżenie*, *krytyczne*, *Nieznany*, i *wszystkich*.  **Ostatniej aktualizacji** czasu w prawym górnym rogu reprezentuje czas ostatniego po stronie Diagnostyka kondycji została odświeżona.  

## <a name="alerts"></a>Alerty

Usługa Azure Monitor kondycji maszyn wirtualnych funkcji integruje się z [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) i zgłasza alert, gdy kryteria kondycji wstępnie zdefiniowanych zmienią się z dobrej kondycji stan złej kondycji, gdy zostanie wykryty warunek. Alerty są pogrupowane według ważności — od 0 do 4, ważność, za pomocą ważność 0 reprezentuje najwyższy poziom ważności. 

Alerty nie są skojarzone z grupą akcji, aby otrzymywać powiadomienia, gdy alert został wywołany. Właściciel subskrypcji musi skonfigurować powiadomienia zgodnie z krokami [w dalszej części tej sekcji](#configure-alerts).   

Łączna liczba alertów dotyczących kondycji maszyny Wirtualnej, pogrupowane według ważności jest dostępna w **kondycji** pulpitu nawigacyjnego w sekcji **alerty**. Po wybraniu łączną liczbę alertów albo liczbę odpowiadającą jej poziom ważności **alerty** zostanie otwarta strona i wyświetla listę wszystkich alertów pasujących wybór.  Na przykład, jeśli został wybrany wiersz odpowiadający **poziom ważność 1**, a następnie zostanie wyświetlony następujący widok:

![Przykład wszystkie alerty o 1 poziom ważności](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Na **alerty** strony, to nie tylko obejmuje Pokaż alerty spełniające wybraną, ale również są filtrowane według **typ zasobu** tylko w celu wyświetlenia alertów dotyczących kondycji wygenerowane przez zasób maszyny wirtualnej.  Jest odzwierciedlana na liście alertów, w kolumnie **zasób docelowy**, gdzie pokazuje maszyny Wirtualnej platformy Azure, gdy kryteria kondycji konkretnego złej kondycji warunek został spełniony dla został zgłoszony alert.  

Alerty z innych typów zasobów lub usług nie są przeznaczone do uwzględnienia w tym widoku, takie jak alertów dzienników opartych na kwerendach dziennika lub alertów metryk, który będzie zwykle wyświetlisz z domyślnej usługi Azure Monitor [wszystkie alerty](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) strony. 

Ten widok można filtrować, wybierając wartości w menu rozwijanych w górnej części strony.

|Kolumnowy |Opis | 
|-------|------------| 
|Subskrypcja |Wybierz subskrypcję Azure. Tylko w wybranej subskrypcji są uwzględniane alerty w widoku. | 
|Grupa zasobów |Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. | 
|Typ zasobu |Wybierz jeden lub więcej typów zasobów. Domyślnie tylko alerty docelowej **maszyn wirtualnych** jest zaznaczone, a następnie zawarte w tym widoku. W tej kolumnie jest dostępna tylko po określono grupę zasobów. | 
|Resource |Wybierz zasób. Tylko alerty o zasobu jako obiekt docelowy znajdują się w widoku. W tej kolumnie jest dostępna tylko po został określony jako typ zasobu. | 
|Severity |Wybiera alert o ważności lub wybierz *wszystkich* obejmujący alerty wszystkie poziomy ważności. | 
|Stan monitora |Wybierz warunek monitora, aby filtrować alerty, jeśli zostały one *Fired* przez system lub *rozwiązane* przez system, jeśli warunek nie jest już aktywna. Lub wybierz *wszystkich* obejmujący alerty wszystkie warunki. | 
|Stan alertu |Wybierz stan alertu *New*, *potwierdzonym*, *zamknięte*, lub wybierz *wszystkich* obejmujący alerty wszystkich stanów. | 
|Monitorowanie usługi |Wybierz usługę, lub wybierz *wszystkich* obejmujący wszystkie usługi. Tylko alerty *Insights maszyny Wirtualnej* są obsługiwane w przypadku tej funkcji.| 
|Zakres czasu| Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. | 

**Alert szczegółów** zostanie wyświetlona strona, po wybraniu alertu, który udostępnia szczegółowe informacje o alercie, dzięki czemu można zmienić jego stan. Aby dowiedzieć się więcej na temat zarządzania alertami, zobacz [Utwórz, Wyświetl, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>W tym czas, tworzenie nowych alertów w oparciu o kryteria kondycji, lub zmodyfikuj istniejące kondycji reguły alertów w usłudze Azure Monitor z poziomu portalu nie jest obsługiwana.  
>

![W okienku szczegółów alertu wybranego alertu](./media/vminsights-health/alert-details-pane-01.png)

Zgłoś alert, również można zmienić stanu dla jednego lub wielu alertów, wybierając je, a następnie wybierając **zmiany stanu** z **wszystkie alerty** strony, w lewym górnym rogu. Na **Zmień stan alertu** okienku wybierz jedną z państw, Dodaj opis zmian w **komentarz** pola, a następnie kliknij przycisk **Ok** aby zatwierdzić zmiany. Podczas weryfikowania informacji i zmiany zostaną zastosowane, możesz śledzić postęp w sekcji **powiadomienia** z menu.  

### <a name="configure-alerts"></a>Konfigurowanie alertów
Zarządzania niektórych alertów zadań nie mogą być zarządzane w witrynie Azure portal i muszą zostać wykonane za pomocą [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). W szczególności:

- Włączanie lub wyłączanie alertu dotyczącego kryteria kondycji 
- Konfigurowanie powiadomień o alertach kryteria kondycji 

Metoda używana w każdym przykładzie korzysta [ARMClient](https://github.com/projectkudu/armclient) na komputerze Windows. Jeśli nie jesteś zaznajomiony z tej metody, zobacz [przy użyciu ARMClient](../platform/rest-api-walkthrough.md#use-armclient).  

#### <a name="enable-or-disable-alert-rule"></a>Włączanie lub wyłączanie reguły alertu

Aby włączyć lub wyłączyć alert kryteria kondycji konkretnego właściwość kryteria kondycji *alertGeneration* należy modyfikować za pomocą wartości **wyłączone** lub **włączone**. Aby zidentyfikować *elementu monitorId* kryteria kondycji konkretnego, poniższy przykład pokazują, jak utworzyć zapytanie dotyczące tej wartości kryteriów **LogicalDisk\Avg dysku sekund na Transfer**.

1. W oknie terminalu wpisz **logowania armclient.exe**. Ten sposób wyświetli monit o logowanie do platformy Azure.

2. Wpisz następujące polecenie, aby pobrać wszystkie aktywne w określonej maszyny wirtualnej kryterium kondycji i zidentyfikować wartość *elementu monitorId* właściwości. 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Dane wyjściowe tego polecenia można znaleźć w poniższym przykładzie. Zwróć uwagę na wartość *elementu MonitorId*. Ta wartość jest wymagana do kolejnego kroku, w którym należy określić identyfikator kryteria kondycji i modyfikowania jego właściwości w celu utworzenia alertu.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Wpisz następujące polecenie, aby zmodyfikować *alertGeneration* właściwości.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Wpisz polecenie GET, użyto w kroku 2, aby sprawdzić wartość właściwości jest równa **wyłączone**.  

#### <a name="associate-action-group-with-health-criteria"></a>Skojarz grupę akcji z kryteria kondycji

Monitor kondycji maszyn wirtualnych platformy Azure obsługuje wiadomości SMS i powiadomień e-mail, gdy alerty są generowane, gdy kryteria kondycji staje się nieprawidłowy. Aby skonfigurować powiadomienia, musisz Zanotuj nazwę grupy akcji, który jest skonfigurowany do wysyłania powiadomień programu SMS lub wiadomości e-mail. 

>[!NOTE]
>Ta akcja musi wykonać względem każdej maszyny Wirtualnej monitorowanej, że chcesz otrzymywać powiadomienie, nie ma zastosowania do wszystkich maszyn wirtualnych w grupie zasobów.  

1. W oknie terminalu wpisz **logowania armclient.exe**. Ten sposób wyświetli monit o logowanie do platformy Azure.

2. Wpisz następujące polecenie, aby skojarzyć grupy akcji przy użyciu reguł alertów.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Aby sprawdzić wartość właściwości **actionGroupResourceIds** zostało pomyślnie zaktualizowane, wpisz następujące polecenie.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Dane wyjściowe powinny wyglądać w następujący sposób:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Kolejne kroki

Aby zidentyfikować wąskie gardła i ogólnego użycia za pomocą wydajność maszyn wirtualnych, zobacz [wydajność maszyny Wirtualnej platformy Azure w widoku](vminsights-performance.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](vminsights-maps.md). 
