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
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: aac6ca2db815aa3ca427b281e146874dc142107a
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51716019"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms-preview"></a>Poznanie kondycji maszynach wirtualnych platformy Azure z usługą Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Azure obejmuje wiele usług, wykonujących indywidualnie określonej roli lub zadania w obszarze monitorowania, ale dostarczanie perspektywy szczegółowe kondycji systemu operacyjnego, hostowane na maszynach wirtualnych platformy Azure nie była dostępna.  Chociaż można monitorować różne warunki przy użyciu usługi Log Analytics lub Azure Monitor, nie zostało zaprojektowane do modelowania i reprezentują kondycji podstawowych składników lub ogólną kondycję maszyny wirtualnej.  Dzięki usłudze Azure Monitor dla funkcji kondycji maszyn wirtualnych proaktywnie monitoruje dostępność i wydajność, Windows lub Linux systemu operacyjnego gościa za pomocą modelu, reprezentujące najważniejsze składniki oraz ich relacji, kryteria, które określa, jak mierzyć kondycję tych składniki i wysyłania alertów, gdy zostanie wykryty warunek złej kondycji.  

Wyświetlanie ogólną kondycję maszyn wirtualnych platformy Azure i bazowych systemu operacyjnego, można zaobserwować z dwóch perspektyw z usługą Azure Monitor dla kondycji maszyn wirtualnych, bezpośrednio z maszyny wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów z usługi Azure Monitor.

Ten artykuł pomoże Ci zrozumieć, jak można szybko ocenić, badania i rozwiązywania wykrytych problemów kondycji.

Aby uzyskać informacje o konfigurowaniu usługi Azure Monitor do maszyn wirtualnych, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-onboard.md).

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
- Procent czasu bezczynności dysku fizycznego
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
- Procesor Czas DPC (%)
- Procesor Czas procesora (%)
- Łączna liczba procentowa czasu procesora
- Czas całkowity DPC (%)
- System operacyjny dostępne megabajty pamięci

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Wprowadzenie do środowiska kondycji
Przed zagłębieniem się w funkcji kondycji dla pojedynczej maszyny wirtualnej lub grupy maszyn wirtualnych, ważne jest, że firma Microsoft zapewnia krótkie wprowadzenie, aby zrozumieć sposób prezentowania informacji i reprezentują wizualizacji.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Wyświetl kondycję bezpośrednio z maszyny wirtualnej 
Aby wyświetlić kondycję maszyn wirtualnych platformy Azure, wybierz **Insights (wersja zapoznawcza)** z okienka po lewej stronie maszyny wirtualnej. Na stronie maszyny Wirtualnej insights **kondycji** jest domyślnie otwarty i wyświetla widok kondycji maszyny wirtualnej.  

![Usługa Azure Monitor, aby uzyskać przegląd kondycji maszyn wirtualnych z wybranych maszyn wirtualnych](./media/vminsights-health/vminsights-directvm-health.png)

Na **kondycji** kartę w sekcji **kondycji maszyny Wirtualnej gościa**, w tabeli przedstawiono bieżący stan kondycji maszyny wirtualnej i całkowita liczba alertów dotyczących kondycji maszyny Wirtualnej zgłoszone przez składnik w złej kondycji. Zapoznaj się [alerty i Zarządzanie alertami](#alerting-and-alert-management) Aby uzyskać więcej informacji.  

Stany kondycji zdefiniowane dla maszyny Wirtualnej są następujące: 

* **Dobra** — wykryte nie problemy dla maszyny Wirtualnej i czy działa on zgodnie z potrzebami.  
* **Krytyczne** — co najmniej jeden problem krytyczny, są wykrywane, które należy uwzględnić w celu przywrócenia normalnej funkcjonalności, zgodnie z oczekiwaniami. 
* **Ostrzeżenie** — co najmniej jeden problem, są wykrywane, które muszą być kierowane lub stan kondycji może stać się krytyczna.  
* **Nieznany** — Jeśli usługa nie był w stanie nawiązać połączenie z maszyną Wirtualną, a jego stan zmieni się stan nieznany.  

Wybieranie **Wyświetl diagnostykę kondycji** spowoduje otwarcie strony, przedstawiający wszystkie składniki maszyny Wirtualnej, kryteriów skojarzonej kondycji, zmian stanu i innych istotnych problemów napotykanych przez monitorowanie składników powiązane z maszyną wirtualną. Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji](#health-diagnostics). 

W obszarze **kondycja składnika** sekcji tabeli pojawi się stan kondycji zbiorczy kategorii podstawowej wydajności monitorowanych przez kryteria kondycji dla tych obszarów, w szczególności **Procesora**,  **Pamięć**, **dysku**, i **sieci**.  Wybranie któregokolwiek ze składników powoduje otwarcie strony z listą wszystkich kryterium kondycji poszczególnych monitorowania aspekty tego składnika i stan kondycji odpowiednimi każdego z nich.  

Podczas uzyskiwania dostępu do kondycji, w maszynie Wirtualnej platformy Azure z systemem operacyjnym Windows, stan kondycji 5 pierwszych podstawowych usług Windows są wyświetlane w sekcji **podstawowych usług kondycji**.  Wybranie jednej z usług powoduje otwarcie strony z listą kryteria kondycji monitorowania tego składnika, a jej stan kondycji.  Kliknięcie nazwy kryteria kondycji zostanie otwarty w okienku właściwości, a w tym miejscu możesz przejrzeć szczegóły konfiguracji, w tym przypadku kryteria kondycji ma odpowiedni alert usługi Azure Monitor zdefiniowane. Aby dowiedzieć się więcej, zobacz [Diagnostyka kondycji i Praca z kryteria kondycji](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Perspektywa agregacji maszyny wirtualnej
Aby wyświetlić kondycji zbieranie danych dla wszystkich maszyn wirtualnych w grupie zasobów, z listy nawigacji w portalu wybierz **usługi Azure Monitor** , a następnie wybierz **maszyny wirtualne (wersja zapoznawcza)**.  

![Maszyna wirtualna analizy, monitorowanie widoku z usługi Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Z **subskrypcji** i **grupy zasobów** list rozwijanych wybierz grupę odpowiedni zasób, który zawiera maszyny wirtualne powiązany z grupą, aby wyświetlić ich stan kondycji zgłaszane.  Wybór tylko ma zastosowanie funkcja Kondycja i nie jest przenoszone wydajności lub mapy.

Na **kondycji** kartę, jesteś w stanie się następujące czynności:

* Ile maszyn wirtualnych znajdują się w stan krytyczny lub w złej kondycji, a ile jest w dobrej kondycji lub nie przesyłanie danych (nazywane stan nieznany)?
* Którą maszyny wirtualne według systemu operacyjnego (OS) zgłasza stan złej kondycji i ile?
* Ile maszyn wirtualnych jest w złej kondycji ze względu na problem wykryty z procesora, dysku, pamięć lub karty sieciowej, pogrupowane według stanu kondycji?  
* Ile maszyn wirtualnych jest w złej kondycji ze względu na problem wykryty core systemu operacyjnego usługi pogrupowane według stanu kondycji?

W tym miejscu można szybko zidentyfikować najważniejsze problemy krytyczne, wykrytych przez kryteria kondycji Proaktywne monitorowanie maszyny Wirtualnej i przejrzyj szczegóły alertu kondycji maszyny Wirtualnej i skojarzone bazy wiedzy przeznaczona do pomagają w diagnostykę i korygowanie problemu.  Wybierz dowolne ważności, aby otworzyć [wszystkie alerty](../../monitoring-and-diagnostics/monitoring-overview-alerts.md#all-alerts-page) strona jest filtrowana według tego ważności.

**Dystrybucji maszyny Wirtualnej przez system operacyjny** lista maszyn wirtualnych w rozbiciu na wersję Windows lub dystrybucja systemu Linux, wraz z ich wersji. W każdej kategorii systemu operacyjnego maszyny wirtualne są podzielone dalsze na podstawie kondycji maszyny Wirtualnej. 

![Szczegółowe informacje z maszyny Wirtualnej maszyny wirtualnej dystrybucji perspektywy](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Stany kondycji zdefiniowane dla maszyny Wirtualnej są następujące: 

* **Dobra** — wykryte nie problemy dla maszyny Wirtualnej i czy działa on zgodnie z potrzebami.  
* **Krytyczne** — co najmniej jeden problem krytyczny, są wykrywane, które należy uwzględnić w celu przywrócenia normalnej funkcjonalności, zgodnie z oczekiwaniami. 
* **Ostrzeżenie** — co najmniej jeden problem, są wykrywane, które muszą być kierowane lub stan kondycji może stać się krytyczna.  
* **Nieznany** — Jeśli usługa nie był w stanie nawiązać połączenie z maszyną Wirtualną, a jego stan zmieni się stan nieznany.  

Możesz kliknąć dowolny element kolumny — **liczba maszyn wirtualnych**, **krytyczny**, **ostrzeżenie**, **dobra kondycja** lub **nieznany** do Przechodzenie do szczegółów w **maszyn wirtualnych** strony wyświetlenie listy filtrowane wyniki pasujące kolumny wybranej. Na przykład, jeśli chcemy przejrzeć wszystkie uruchomione maszyny wirtualne **Red Hat Enterprise Linux w wersji 7.5**, kliknij pozycję **liczba maszyn wirtualnych** wartość dla tego systemu operacyjnego i otworzy następującą stronę, wyświetlanie listy maszyn wirtualnych zgodnych Ten filtr i ich stan kondycji obecnie znane.  

![Przykład kondycji zbiorczej maszyn wirtualnych Red Hat systemu Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Na **maszyn wirtualnych** strony, jeśli wybrana nazwa maszyny Wirtualnej w kolumnie **Nazwa_maszyny_wirtualnej**, nastąpi przekierowanie do strony wystąpienia maszyny Wirtualnej z bardziej szczegółowymi informacjami, alertów oraz problemów z kondycją kryteria ustaliła, że są wpływ na wybranej maszyny Wirtualnej.  W tym miejscu można filtrować Szczegóły stanu kondycji, klikając **stan kondycji** ikony w lewym górnym rogu strony Aby sprawdzić składniki, które są w złej kondycji lub można wyświetlić alerty dotyczące kondycji maszyny Wirtualnej zgłoszone przez składnik w złej kondycji pogrupowane według ważności alertu.    

W widoku listy maszyn wirtualnych, klikając nazwę maszyny Wirtualnej spowoduje otwarcie **kondycji** strony, w tym wybrane podobnie maszyny Wirtualnej, jak w przypadku wybrania **Insights (wersja zapoznawcza)** z maszyny Wirtualnej bezpośrednio.

![Szczegółowe maszyny Wirtualnej informacje dotyczące wybranej maszyny wirtualnej platformy Azure](./media/vminsights-health/vminsights-directvm-health.png)

W tym miejscu pokazuje pakiet zbiorczy **stan kondycji** dla maszyny wirtualnej i **alerty**skategoryzowane według ważności, którą reprezentują wywoływane, gdy stan kondycji zmieni się z kondycji na nieprawidłowy dla alerty dotyczące kondycji maszyny Wirtualnej kryteria kondycji.  Wybieranie **maszyn wirtualnych w stan krytyczny** spowoduje otwarcie strony z listą przynajmniej jednej maszyny wirtualnej, które znajdują się w krytycznym stanie kondycji.  Klikając stan kondycji dla jednej z maszyn wirtualnych, na liście będą wyświetlane **Diagnostyka kondycji** widoku maszyny wirtualnej.  W tym miejscu znajdziesz kryteria kondycji, które jest odzwierciedlający problem z kondycją stanu. Gdy **Diagnostyka kondycji** zostanie otwarta strona, pokazuje wszystkie składniki maszyny Wirtualnej i ich kryteria kondycji skojarzone z bieżącym stanem kondycji.  Zapoznaj się [kondycji diagnostycznych](#health-diagnostics) sekcji, aby uzyskać więcej informacji.  

Wybieranie **wyświetlić wszystkie kryteria kondycji** otwiera stronę, która pokazuje listę dostępnych za pomocą tej funkcji kryteria kondycji.  Dane można dodatkowo filtrowane na podstawie następujących opcji:

* **Typ** — istnieją trzy rodzaje kondycji typów kryteria w celu oceny warunków, w którym można rzutować ogólnej kondycji stanu monitorowanych maszyny Wirtualnej.  
    a. **Jednostka** — mierzy pewien aspekt maszyny wirtualnej. Ten typ kryteriów kondycji może być sprawdzenie licznika wydajności w celu ustalenia wydajności składnika, uruchomienie skryptu do przeprowadzenia transakcji syntetycznej lub poszukaj zdarzenia wskazującego błąd.  Domyślnie filtr jest równa jednostki.  
    b. **Zależność** — udostępnia informacje zbiorcze o kondycji, między różnymi jednostkami. Te kryteria kondycji temu kondycja jednostki, aby była zależna od kondycji obiektu innego rodzaju jednostki, która opiera się na powodzenie operacji.  
    c. **Łączny** — zapewnia zbiorczy stan kondycji podobne kryteria kondycji. Kryterium kondycji jednostki i zależności będą zazwyczaj konfigurowane w obszarze kryterium kondycji agregacji. Oprócz zapewnienia lepszej ogólnej organizacji wielu kryteriów kondycję różnych przeznaczone dla jednostki, kryterium agregacji kondycji udostępnia unikatowy stan kondycji dla różnych kategorii obiektów.

* **Kategoria** — typ kryteria kondycji używane do pogrupowania kryteriów typu podobne do celów raportowania.  Są one **dostępności** lub **wydajności**.

Możesz przejść do dalszych szczegółów, aby zobaczyć, które wystąpienia jest w złej kondycji, klikając wartość w polu **składnik w złej kondycji** kolumny.  Na stronie tabeli przedstawiono składniki, które znajdują się w krytycznym stanie kondycji.    

## <a name="health-diagnostics"></a>Diagnostyka kondycji
**Diagnostyka kondycji** strona umożliwia wyświetlenie wszystkich składników maszyny wirtualnej skojarzonej kondycji kryteria, zmian stanu i innych istotnych problemów napotykanych przez monitorowane obiekty powiązane z maszyną wirtualną. 

![Przykład strony Diagnostyka kondycji dla maszyny Wirtualnej](./media/vminsights-health/health-diagnostics-page-01.png)

Diagnostyka kondycji można uruchomić w następujący sposób.

* Przez pakiet zbiorczy stan kondycji dla wszystkich maszyn wirtualnych z agregacji perspektywy maszyn wirtualnych w usłudze Azure Monitor.  Na **kondycji** kliknij na ikonę **krytyczny**, **ostrzeżenie**, **dobra kondycja**, lub **nieznany** stan kondycji, w sekcji **kondycji maszyny Wirtualnej gościa** i przejdź do strony, która zawiera listę wszystkich maszyn wirtualnych dopasowywania kategorii filtrowane.  Klikając pozycję na wartość w **stan kondycji** kolumny spowoduje to otwarcie diagnostyki kondycji ograniczone do tej konkretnej maszyny Wirtualnej.      

* Według systemu operacyjnego z agregacji perspektywy maszyn wirtualnych w usłudze Azure Monitor. W obszarze **dystrybucji maszyny Wirtualnej**, wybierając dowolną z wartości w kolumnie zostanie otwarty **maszyn wirtualnych** strony i powrócić do listy w tabeli dopasowywania kategorii filtrowane.  Kliknięcie przycisku wartości w obszarze **stan kondycji** kolumny otwiera kondycji Diagnostyka dla wybranej maszyny Wirtualnej.    
 
* Z maszyny Wirtualnej w usłudze Azure Monitor dla maszyn wirtualnych gościa **kondycji** kartę, wybierając **Wyświetl diagnostykę kondycji** 

Diagnostyka kondycji porządkuje informacje o kondycji na następujące kategorie: 

* Dostępność
* Wydajność
 
Wszystkie kryteria kondycji zdefiniowane dla wybranego obiektu docelowego wyświetla się w odpowiedniej kategorii. 

Stan kondycji kryteria kondycji jest definiowany przez jeden z trzech stanów — *krytyczny*, *ostrzeżenie*, i *dobra kondycja*. Istnieje inny stan *nieznany*, który nie jest skojarzony ze stanem kondycji, ale reprezentuje stan monitorowania znane przez funkcję.  

Poniższa tabela zawiera szczegółowe informacje o stanach kondycji reprezentowane w diagnostyce kondycji.

|Ikona |Stan kondycji |Znaczenie |
|-----|-------------|------------|
| |W dobrej kondycji |Kondycja jest dobra, warunków zdefiniowanych kondycji, jeśli. W przypadku zbiorczego monitora nadrzędnego telefoniczny ustala kondycję i odzwierciedla najlepszego przypadku lub najgorszy stan elementu podrzędnego.|
| |Krytyczny |Stan kondycji jest krytyczny, jeśli nie znajduje się w warunku zdefiniowanego kondycji. W przypadku zbiorczego monitora nadrzędnego telefoniczny ustala kondycję i odzwierciedla najlepszego przypadku lub najgorszy stan elementu podrzędnego.|
| |Ostrzeżenie |Stan kondycji jest ostrzeżenie, jeśli jest między dwiema wartościami progowymi kondycji zdefiniowany warunek, gdzie jeden wskazuje *ostrzeżenie* wskazuje stan, a druga *krytyczny* stanu. W przypadku nadrzędnym pakiet zbiorczy monitor, jeśli jeden lub więcej podrzędnych jest w stanie ostrzeżenia, a następnie element nadrzędny będzie odzwierciedlać *ostrzeżenie* stanu. Jeśli jest elementem podrzędnym, który znajduje się w *krytyczny* i inny podrzędny w *ostrzeżenie* stanu zbiorczego nadrzędnego wyświetli stan kondycji *krytyczny*.|
| |Nieznane |Stan kondycji jest *nieznany* stan, gdy stan kondycji nie można obliczyć z kilku powodów, takich jak nie może zbierać dane usługi niezainicjowanej itp.| 

Strona Diagnostyka kondycji zawiera trzema głównymi sekcjami:

* Model składnika 
* Kryteria kondycji
* Zmiany stanu 

![Części strony diagnostyki kondycji](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Model składników
Kolumna skrajnie po lewej stronie Diagnostyka kondycji jest model składnika. Wszystkie składniki i odnalezionych wystąpień, które są skojarzone z maszyną Wirtualną, są wyświetlane w tej kolumnie. 

W poniższym przykładzie składniki odkryte to dysk, dysk logiczny w systemie, procesora, pamięci i systemu operacyjnego. Wiele wystąpień tych składników są wykrywane i wyświetlane w tej kolumnie, za pomocą dwóch wystąpień dysku logicznego **/**, **/rozruchu**, i   **/mnt/zasobów**, jedno wystąpienie karty sieciowej **eth0**, dwa wystąpienia dysku **sda** i **sdb**, dwa wystąpienia procesora **0 i 1**, a **Red Hat Enterprise Linux Server w wersji 7.4 (Maipo) (System operacyjny)**. 

![Przykładowy model składnika prezentowane w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kryteria kondycji
W środkowej kolumnie na stronie Diagnostyka kondycji jest **kryteria kondycji** kolumny. Model kondycji zdefiniowane dla maszyny Wirtualnej jest wyświetlany w formie drzewa hierarchicznego. Model kondycji dla maszyny Wirtualnej składa się z jednostki, zależności i kryteria kondycji agregacji.  

![Kryteria kondycji przykład przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kryterium kondycji mierzy kondycję monitorowanego wystąpienia przy użyciu kryteriów, których może to być wartość progową lub stan jednostki itp. Kryterium kondycji ma dwa lub trzy stany kondycji, zgodnie z opisem w powyższej sekcji. W dowolnym danym momencie kryterium kondycji może być tylko jednego ze swoich potencjalnych stanów. 

Ogólna kondycja obiektu docelowego jest określana na podstawie kondycji każdego z jego kryteria kondycji zdefiniowane w modelu kondycji. Są to kombinacja kryteria kondycji ukierunkowanych bezpośrednio na elemencie docelowym kryteria kondycji przeznaczone dla składników rzutowanie do elementu docelowego przy użyciu kryterium kondycji zależności. Taka hierarchia jest zaprezentowana w **kryteria kondycji** sekcji na stronie Diagnostyka kondycji. W zasadach zestawienia kondycji jest częścią konfiguracji kryteria kondycji agregacji i zależności. Można znaleźć listy domyślny zestaw kryteria kondycji uruchomiona w ramach tej funkcji w sekcji [szczegóły konfiguracji monitorowania](#monitoring-configuration-details).  

W poniższym przykładzie kryterium agregacji kondycji **zestawienie usług Windows Core** oparte na Windows maszyny wirtualnej ocenia kondycję najważniejszych usług Windows, w oparciu o kryteria kondycji poszczególnych usług. Stan poszczególnych usług, takich jak DNS, DHCP, itp., są obliczane i kondycji zbiorczej odpowiedni pakiet zbiorczy kondycji kryterium (jak pokazano poniżej).  

![Przykładowy pakiet zbiorczy kondycji](./media/vminsights-health/health-diagnostics-windows-svc-rollup.png)

Kondycję **zestawienie usług Windows Core** zbiera na temat kondycji **dostępności systemu operacyjnego**, który ostatecznie zestawiany w **dostępności** maszyny wirtualnej. 

Kryteria kondycji **jednostki** typ może mieć ich konfiguracji, modyfikować, klikając łącze wielokropka, aby znacznie rogu i wybierając opcję **Pokaż szczegóły** aby otworzyć okienko konfiguracji. 

![Konfigurowanie przykładu kryteria kondycji](./media/vminsights-health/health-diagnostics-linuxvm-example-03.png)

W okienku konfiguracja kryteriów wybranych kondycji, w tym przykładzie **dysku logicznego % wolnego miejsca** można skonfigurować z inną wartością liczbową przekroczyła wartość progową, ponieważ monitor dwustanowy, co oznacza go tylko zmieni się z dobrej kondycji na krytyczny.  Innego kryterium kondycji mogą być trzy stanu, w którym można skonfigurować wartość próg ostrzegawczy i krytyczny stan kondycji.  

>[!NOTE]
>Stosowanie zmian konfiguracji kryteria kondycji do jednego wystąpienia jest stosowany do wszystkich monitorowanych wystąpień.  Na przykład w przypadku wybrania **/mnt/zasobów** i modyfikować **dysku logicznego % wolnego miejsca** próg nie dotyczy tylko tego wystąpienia, ale wszystkich wystąpień dysku logicznego odnalezione i monitorowane w MASZYNA WIRTUALNA.
>

![Konfigurowanie kryteria kondycji w przykładzie monitora jednostki](./media/vminsights-health/health-diagnostics-linuxvm-example-04.png)

Jeśli chcesz dowiedzieć się więcej o wskaźnika kondycji, artykuły merytoryczne są uwzględniane które pozwala zidentyfikować problemy, przyczyny i rozwiązania.  Kliknij pozycję **służy do wyświetlania informacji** łącze na stronie zostanie otwarta nowa karta w przeglądarce, przedstawiający artykułu merytorycznego określone.  W dowolnym momencie można przejrzeć wszystkie artykuły wiedzy kryterium kondycji dołączone do usługi Azure Monitor kondycji maszyn wirtualnych funkcji [tutaj](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Zmiany stanu
Kolumna najdalej z prawej strony na stronie Diagnostyka kondycji jest **zmiany stanu**. Wyświetla listę wszystkich zmian stanu skojarzonych z kryteria kondycji, które wybrano w **kryteria kondycji** sekcji lub Zmień stan maszyny wirtualnej w przypadku maszyny Wirtualnej została wybrana w zaufanym **składnika modelu** lub **Kryteria kondycji** kolumnie tabeli. 

![Zmiany stanu przykład przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Ta sekcja obejmuje kryteria kondycji i czasu skojarzone, posortowane według najnowszy stan u góry.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Skojarzenie modelu składników, kryteria kondycji i stan zmiany kolumn 
Trzy kolumny są powiązane ze sobą. Gdy użytkownik wybierze wystąpienie odnalezionych w modelu składników **kryteria kondycji** sekcji jest filtrowana w celu widoku składnika i odpowiednio **zmianę stanu** jest aktualizowany w oparciu o wybrane kryteria kondycji. 

![Przykład wybierania monitorowanych wystąpień i wyniki](./media/vminsights-health/health-diagnostics-linuxvm-example-02.png)

W powyższym przykładzie po wybraniu jednej **katalogu/mnt (dysk logiczny)**, drzewa kryteria kondycji jest filtrowana w celu **katalogu/mnt (dysk logiczny)**. **Dostępności** i **wydajności** karty są filtrowane w związku z tym zbyt. **Zmiany stanu** kolumna pokazuje zmiany stanu, w oparciu o dostępności **katalogu/mnt (dysk logiczny)**. 

Aby wyświetlić stan kondycji zaktualizowane, można odświeżyć stronę diagnostyki kondycji, klikając **Odśwież** łącza.  W przypadku aktualizacji do stanu kondycji kryterium kondycji na podstawie wstępnie zdefiniowanych interwału sondowania, to zadanie pozwala uniknąć oczekiwania i odzwierciedla najnowszy stan kondycji.  **Kryteria kondycji** filtr zezwala na do zakresu wyniki oparte na stanie kondycji wybranego - dobrej kondycji, ostrzeżenie, krytyczny, nieznane i wszystkie.  **Ostatniej aktualizacji** czasu w prawym górnym rogu reprezentuje czas ostatniego po stronie Diagnostyka kondycji została odświeżona.  

## <a name="alerting-and-alert-management"></a>Zarządzanie alertów i alert 
Usługa Azure Monitor kondycji maszyn wirtualnych funkcji integruje się z [Azure Alerts](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) i zgłasza alert, gdy kryteria kondycji wstępnie zdefiniowanych zmienią się z dobrej kondycji stan złej kondycji, gdy zostanie wykryty warunek. Alerty są pogrupowane według ważności — od 0 do 4, ważność, za pomocą ważność 0 reprezentuje najwyższy poziom ważności.  

Łączna liczba alertów dotyczących kondycji maszyny Wirtualnej, pogrupowane według ważności jest dostępna w **kondycji** pulpitu nawigacyjnego w sekcji **alerty**. Po wybraniu łączną liczbę alertów albo liczbę odpowiadającą jej poziom ważności **alerty** zostanie otwarta strona i wyświetla listę wszystkich alertów pasujących wybór.  Na przykład, jeśli został wybrany wiersz odpowiadający **poziom ważność 1**, a następnie zostanie wyświetlony następujący widok:

![Przykład wszystkie alerty o 1 poziom ważności](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Na **alerty** strony, to nie tylko obejmuje Pokaż alerty spełniające wybraną, ale również są filtrowane według **typ zasobu** tylko w celu wyświetlenia alertów dotyczących kondycji wygenerowane przez zasób maszyny wirtualnej.  Znajduje to odzwierciedlenie na liście alertów, w kolumnie **zasób docelowy**, gdzie pokazuje maszyny Wirtualnej platformy Azure, gdy kryteria kondycji konkretnego złej kondycji warunek został spełniony dla został zgłoszony alert.  

Alerty z innych typów zasobów lub usług nie są przeznaczone do uwzględnienia w tym widoku, takie jak alerty dziennika oparte na usłudze Log Analytics zapytania lub alertów metryk, który będzie zwykle wyświetlisz z domyślnej usługi Azure Monitor [wszystkie alerty](../../monitoring-and-diagnostics/monitoring-overview-alerts.md#all-alerts-page) strony. 

Ten widok można filtrować, wybierając wartości w menu rozwijanych w górnej części strony.

|Kolumna |Opis | 
|-------|------------| 
|Subskrypcja |Wybierz subskrypcję platformy Azure. Tylko w wybranej subskrypcji są uwzględniane alerty w widoku. | 
|Grupa zasobów |Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. | 
|Typ zasobu |Wybierz jeden lub więcej typów zasobów. Domyślnie tylko alerty docelowej **maszyn wirtualnych** jest zaznaczone, a następnie zawarte w tym widoku. W tej kolumnie jest dostępna tylko po określono grupę zasobów. | 
|Zasób |Wybierz zasób. Tylko alerty o zasobu jako obiekt docelowy znajdują się w widoku. W tej kolumnie jest dostępna tylko po został określony jako typ zasobu. | 
|Ważność |Wybiera alert o ważności lub wybierz *wszystkich* obejmujący alerty wszystkie poziomy ważności. | 
|Stan monitora |Wybierz warunek monitora, aby filtrować alerty, jeśli zostały one *Fired* przez system lub *rozwiązane* przez system, jeśli warunek nie jest już aktywna. Lub wybierz *wszystkich* obejmujący alerty wszystkie warunki. | 
|Stan alertu |Wybierz stan alertu *New*, *potwierdzonym*, *zamknięte*, lub wybierz *wszystkich* obejmujący alerty wszystkich stanów. | 
|Monitorowanie usługi |Wybierz usługę, lub wybierz *wszystkich* obejmujący wszystkie usługi. Tylko alerty *Insights maszyny Wirtualnej* są obsługiwane w przypadku tej funkcji.| 
|Przedział czasu| Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. | 

**Alert szczegółów** zostanie wyświetlona strona, po wybraniu alertu, który udostępnia szczegółowe informacje o alercie, dzięki czemu można zmienić jego stan. Aby dowiedzieć się więcej na temat zarządzania alertami, zobacz [Utwórz, Wyświetl, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor](../../monitoring-and-diagnostics/alert-metric.md).  

>[!NOTE]
>W tej chwili jest nieobsługiwany do tworzenia nowych alertów w oparciu o kryteria kondycji lub modyfikowania istniejących kondycji reguł alertów w usłudze Azure Monitor z portalu.  
>

![W okienku szczegółów alertu wybranego alertu](./media/vminsights-health/alert-details-pane-01.png)

Zgłoś alert, również można zmienić stanu dla jednego lub wielu alertów, wybierając je, a następnie wybierając **zmiany stanu** z **wszystkie alerty** strony, w lewym górnym rogu. Na **Zmień stan alertu** okienku wybierz jedną z państw, Dodaj opis zmian w **komentarz** pola, a następnie kliknij przycisk **Ok** aby zatwierdzić zmiany. Podczas weryfikowania informacji i zmiany zostaną zastosowane, możesz śledzić postęp w sekcji **powiadomienia** z menu.  

## <a name="next-steps"></a>Kolejne kroki
Aby zidentyfikować wąskie gardła i ogólnego użycia za pomocą wydajność maszyn wirtualnych, zobacz [wydajność maszyny Wirtualnej platformy Azure w widoku](vminsights-performance.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](vminsights-maps.md). 
