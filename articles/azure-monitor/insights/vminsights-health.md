---
title: Poznanie kondycji usługi Azure virtual machines | Dokumentacja firmy Microsoft
description: W tym artykule opisano zrozumieć kondycję maszyn wirtualnych i podstawowych systemów operacyjnych za pomocą usługi Azure Monitor dla maszyn wirtualnych.
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
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340142"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Poznanie kondycji usługi Azure virtual machines

System Azure zawiera usługi dla określonych ról lub zadania w obszarze monitorowania, ale nie zapewnia szczegółowe kondycji perspektywy systemów operacyjnych (OSs) hostowanych na maszynach wirtualnych platformy Azure (maszyny wirtualne). Chociaż można używać usługi Azure Monitor w różnych warunkach, nie ustalono, aby modelować i reprezentują kondycji podstawowych składników lub ogólną kondycję maszyn wirtualnych.

Za pomocą usługi Azure Monitor dla kondycji maszyn wirtualnych, można aktywnie monitorować dostępność i wydajność systemu operacyjnego gościa Windows lub Linux. Funkcja kondycji używa modelu, który reprezentuje najważniejsze składniki oraz ich wzajemne relacje alokowania elastycznego, wyświetla kryteria określa sposób pomiaru kondycji składnika, która wysyła alert, gdy wykryje warunek złej kondycji.

Wyświetlanie ogólnej kondycji maszyny Wirtualnej platformy Azure i podstawowego systemu operacyjnego, można zaobserwować z dwóch perspektyw: bezpośrednio z poziomu maszyny Wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów z usługi Azure Monitor.

W tym artykule pokazano, jak szybko ocenić, badanie i rozwiązywanie problemów z kondycją wykryciu usługi Azure Monitor dla funkcji kondycji maszyn wirtualnych.

Aby uzyskać informacje o konfigurowaniu usługi Azure Monitor do maszyn wirtualnych, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Szczegóły konfiguracji monitorowania

W tej sekcji opisano domyślne kryteria kondycji do monitorowania Windows Azure i maszyn wirtualnych systemu Linux. Wszystkie kryteria kondycji są wstępnie skonfigurowane, by wysyłały alert po wykryciu stanu złej kondycji.

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

Aby się zarejestrować, przejdź do [witryny Azure portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Wprowadzenie do usługi Azure Monitor na potrzeby kondycji maszyn wirtualnych

Przed użyciem funkcji kondycji dla pojedynczej maszyny Wirtualnej lub grupy maszyn wirtualnych, ważne jest zrozumieć sposób prezentowania informacji i reprezentują wizualizacji.

### <a name="view-health-directly-from-a-vm"></a>Wyświetl kondycję bezpośrednio z poziomu maszyny Wirtualnej

Aby wyświetlić kondycję maszyn wirtualnych platformy Azure, wybierz **Insights (wersja zapoznawcza)** w okienku po lewej stronie maszyny wirtualnej. Na stronie maszyny Wirtualnej insights **kondycji** karta jest domyślnie otwarty i wyświetla widok kondycji maszyny wirtualnej.

![Usługa Azure Monitor, aby uzyskać przegląd kondycji maszyn wirtualnych z wybranych maszyn wirtualnych](./media/vminsights-health/vminsights-directvm-health.png)

W **kondycji** , w obszarze **kondycji maszyny Wirtualnej gościa**, tabela przedstawia stan kondycji maszyny wirtualnej i całkowita liczba alertów dotyczących kondycji maszyny Wirtualnej zgłoszone przez składnik w złej kondycji.

Aby uzyskać więcej informacji, zobacz [alerty](#alerts).

Stany kondycji zdefiniowane dla maszyny Wirtualnej zostały opisane w poniższej tabeli:

|Ikona |Stan kondycji |Znaczenie |
|-----|-------------|---------------|
| |W dobrej kondycji |Maszyna wirtualna jest w ramach warunków zdefiniowanych kondycji. Ten stan wskazuje, że zostaną wykryte żadne problemy, a maszyna wirtualna działa normalnie. Monitor zbiorczy elementu nadrzędnego health zbiera informacje i odzwierciedla najlepszego przypadku lub najgorszy stan elementu podrzędnego.|
| |Krytyczny |Stan nie jest w ramach warunku zdefiniowanego kondycji wskazująca, że jeden lub więcej krytycznych problemów wykrytych. Te problemy, muszą być kierowane do przywrócenia normalnej funkcjonalności. Za pomocą zbiorczego monitora nadrzędnego stan kondycji i uwzględnianie odzwierciedla najlepszego przypadku lub najgorszy stan elementu podrzędnego.|
| |Ostrzeżenie |Stan jest między dwiema wartościami progowymi warunek zdefiniowanych kondycji, gdzie jeden wskazuje do stanu ostrzeżenia, a druga wskazuje stan krytyczny (można go skonfigurować trzy wartości progowe stanu kondycji) lub gdy niekrytyczne problem może spowodować problemy krytyczne, jeśli Liczba nierozpoznanych. Za pomocą nadrzędny monitor zbiorczy Jeśli jeden lub więcej elementów podrzędnych znajduje się w stanie ostrzeżenia nadrzędnego zostanie naliczona do stanu ostrzeżenia. Jeśli jeden element podrzędny jest w stanie krytycznym i inny podrzędny w stan ostrzegawczy, zbiorczy nadrzędnej, zostaną wyświetlone stan kondycji jako krytyczne.|
| |Nieznane |Nie można obliczyć stan z kilku powodów. W poniższej sekcji przedstawiono dodatkowe szczegóły i możliwe rozwiązania. |

Nieznana kondycja może być spowodowane następującymi problemami:

- Agent została ponownie skonfigurowana i nie jest już określony raporty do obszaru roboczego, gdy włączono usługi Azure Monitor dla maszyn wirtualnych. Aby skonfigurować agenta w celu przekazywania informacji do obszaru roboczego, zobacz [Dodawanie lub usuwanie obszaru roboczego](../platform/agent-manage.md#adding-or-removing-a-workspace).
- Maszyna wirtualna została usunięta.
- Obszar roboczy, skojarzone z usługą Azure Monitor dla maszyn wirtualnych został usunięty. Jeśli masz korzyści z pomocy technicznej Premier, można odzyskać obszaru roboczego. Przejdź do [Premier](https://premier.microsoft.com/) , a następnie otwórz żądanie pomocy technicznej.
- Zależności rozwiązania zostały usunięte. Aby ponownie włączyć ServiceMap i InfrastructureInsights rozwiązania w Twoim obszarze roboczym usługi Log Analytics, należy ponownie zainstalować te rozwiązania za pomocą [szablonu usługi Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Lub użyj opcji konfigurowania obszaru roboczego w karcie rozpocząć pracę.
- Maszyna wirtualna została zamknięta.
- Usługa maszyny Wirtualnej platformy Azure jest niedostępna lub obecnie jest wykonywana konserwacja.
- Obszar roboczy [danych dotyczących dziennego ani nie ogranicza przechowywania](../platform/manage-cost-storage.md) zostało spełnione.

Wybierz **Wyświetl diagnostykę kondycji** aby otworzyć stronę, która pokazuje wszystkie składniki maszyny wirtualnej, związane z nią kryteria kondycji, zmian stanu i innych problemów wykrytych przez monitorowanie składników powiązane z maszyną wirtualną.

Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji](#health-diagnostics).

W **kondycji** sekcji tabeli przedstawiono Przedstawienie prawidłowej wydajności składników monitorowane przez kryteria kondycji. Te składniki obejmują **Procesora**, **pamięci**, **dysku**, i **sieci**. Wybranie składnika powoduje otwarcie strony zawierającej zestawienie wszystkich monitorowania kryterium, a stan kondycji tego składnika.

Gdy uzyskujesz dostęp do kondycji z Maszyną wirtualną platformy Azure z systemem Windows, stan kondycji pięć podstawowych usług Windows jest wyświetlany w obszarze **podstawowych usług kondycji**. Wybranie jakiegokolwiek usług spowoduje otwarcie strony zawierającej zestawienie kryteria kondycji monitorowanie dla tego składnika, wraz z jej stan kondycji.

Wybierając nazwę kryteria kondycji powoduje otwarcie okienka właściwości. W tym okienku można przejrzeć szczegóły konfiguracji, w tym przypadku kryteria kondycji ma odpowiedni alert usługi Azure Monitor.

Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji i Praca z kryteria kondycji](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Łączny perspektywy maszyn wirtualnych

Aby wyświetlić zbierania kondycji dla wszystkich maszyn wirtualnych w grupie zasobów, wybierz **usługi Azure Monitor** z listy nawigacji w portalu, a następnie wybierz **maszyny wirtualne (wersja zapoznawcza)** .

![Maszyna wirtualna analizy, monitorowanie widoku z usługi Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

W **subskrypcji** i **grupy zasobów** list rozwijanych wybierz grupę odpowiedni zasób, który zawiera maszyny wirtualne powiązane z grupy, aby wyświetlić ich stan kondycji zgłaszane. Wybór tylko dotyczy funkcji kondycji i nie jest przenoszone do **wydajności** lub **mapy** karty.

**Kondycji** karta zawiera następujące informacje:

* Ile maszyn wirtualnych znajdują się w stanie krytycznych lub w złej kondycji, a ilu są w dobrej kondycji lub nie przesyła danych (nazywane stan nieznany).
* Które i jak wiele maszyn wirtualnych przez system operacyjny zgłasza stan złej kondycji.
* Ile maszyn wirtualnych jest w złej kondycji ze względu na problem wykryty z procesora, dysku, pamięć lub karty sieciowej, pogrupowane według stanu kondycji.
* Ile maszyn wirtualnych jest w złej kondycji ze względu na problem wykryty core systemu operacyjnego usługi pogrupowane według stanu kondycji.

Na **kondycji** karcie, można zidentyfikować krytycznych problemów wykrytych przez kryteria kondycji maszyny Wirtualnej i przejrzyj szczegóły alertu i skojarzone artykuły merytoryczne monitorowania. Te artykuły mogą pomóc w diagnostykę i Korygowanie problemów. Wybierz dowolne ważności, aby otworzyć [wszystkie alerty](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) strona jest filtrowana według tego ważności.

**Dystrybucji maszyny Wirtualnej przez system operacyjny** lista maszyn wirtualnych w rozbiciu na wersję Windows lub dystrybucja systemu Linux, wraz z ich wersji. W każdej kategorii systemu operacyjnego maszyny wirtualne są podzielone dalsze na podstawie kondycji maszyny Wirtualnej.

![Szczegółowe informacje z maszyny Wirtualnej maszyny wirtualnej dystrybucji perspektywy](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Zaznacz wszystkie kolumny, w tym **liczba maszyn wirtualnych**, **krytyczny**, **ostrzeżenie**, **dobra kondycja**, lub **nieznany**. Wyświetlanie listy filtrowane wyniki w **maszyn wirtualnych** strona, która odpowiada kolumnie, który został wybrany.

Na przykład, aby przejrzeć wszystkie maszyny wirtualne z systemem Red Hat Enterprise Linux w wersji 7.5, należy wybrać **liczba maszyn wirtualnych** wartość dla tego systemu operacyjnego, a zostanie wyświetlona lista maszyn wirtualnych zgodnych z filtrem i ich bieżącym stanem kondycji.

![Przykład kondycji zbiorczej maszyn wirtualnych Red Hat systemu Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

W **maszyn wirtualnych** strony, jeśli wybrana nazwa maszyny Wirtualnej w kolumnie **Nazwa_maszyny_wirtualnej**, są kierowane do **wystąpienia maszyny Wirtualnej** strony. Ta strona zawiera więcej szczegółów dotyczących alertów i kryteria problemów z kondycją, które mają wpływ na wybranej maszyny Wirtualnej. Szczegóły stanu kondycji filtru, wybierając **stan kondycji** ikony w lewym górnym rogu strony Aby sprawdzić składniki, które są w złej kondycji. Można również wyświetlić alerty dotyczące kondycji maszyny Wirtualnej zgłoszone przez składnik w złej kondycji według ważności alertu.

Z **listy maszyn wirtualnych** wyświetlić, wybierz nazwę maszyny Wirtualnej, aby otworzyć **kondycji** strony dla tej maszyny Wirtualnej, podobnie jak w przypadku wybrania **Insights (wersja zapoznawcza)** z maszyny Wirtualnej bezpośrednio.

![Szczegółowe maszyny Wirtualnej informacje dotyczące wybranej maszyny wirtualnej platformy Azure](./media/vminsights-health/vminsights-directvm-health.png)

**Insights (wersja zapoznawcza)** stronie wyświetlany jest stan kondycji zbiorczego dla maszyny Wirtualnej i alerty. Ten stan kondycji jest dzielony na kategorie według ważności, która reprezentuje maszynę Wirtualną alerty dotyczące kondycji wywoływane, gdy stan kondycji zmieniła się z dobrej kondycji na nieprawidłowy, na podstawie kryteriów. Wybieranie **maszyn wirtualnych w stan krytyczny** umożliwia otwarcie strony z listą co najmniej jeden maszyn wirtualnych z w krytycznym stanie kondycji.

Wybranie stanu kondycji dla jednego z programów maszyn wirtualnych **Diagnostyka kondycji** widoku maszyny wirtualnej. W tym widoku można określić, jakie kryteria kondycji jest odzwierciedlający problem stan kondycji. Gdy **Diagnostyka kondycji** zostanie otwarta strona, pokazuje wszystkich składników maszyn wirtualnych i ich kryteria kondycji skojarzone z bieżącym stanem kondycji.

Aby uzyskać więcej informacji, zobacz [Diagnostyka kondycji](#health-diagnostics).

Wybieranie **wyświetlić wszystkie kryteria kondycji** otwiera stronę, która pokazuje listę dostępnych za pomocą tej funkcji kryteria kondycji. Dane można dodatkowo filtrowane na podstawie następujących opcji:

* **Typ**. Istnieją trzy typy kryteria kondycji do oceny warunków i przedstawia ogólny stan kondycji monitorowanego maszyny wirtualnej:
    - **Jednostka**. Mierzy pewien aspekt maszyny Wirtualnej. Ten typ kryteriów kondycji może sprawdzanie licznika wydajności w celu ustalenia wydajności składnika, uruchomienie skryptu do wykonania transakcji syntetycznej lub oczekiwania na zdarzenie, które wskazuje na błąd. Filtr jest domyślnie do jednostki.
    - **Zależność**. Stanowi pakiet zbiorczy kondycji między różnymi jednostkami. Te kryteria kondycji temu kondycja jednostki, aby była zależna od kondycji innego typu jednostki, która opiera się na powodzenie operacji.
    - **Łączny**. Zapewnia zbiorczy stan kondycji podobne kryteria kondycji. Jednostki i zależności kryterium kondycji zwykle są skonfigurowane w ramach kryterium kondycji agregacji. Oprócz zapewnienia lepszej ogólnej organizacji wielu kryteriów kondycję różnych przeznaczone dla jednostki, kryterium agregacji kondycji udostępnia unikatowy stan kondycji dla różnych kategorii obiektów.

* **Kategoria**. Typ kryteria kondycji używane do grupowania podobnych kryteriów na potrzeby raportowania. Te kategorie są **dostępności** i **wydajności**.

Aby zobaczyć, których wystąpienia są w złej kondycji, wybierz wartość w polu **składnik w złej kondycji** kolumny. Na tej stronie tabela zawiera listę składników, które znajdują się w krytycznym stanie kondycji.

## <a name="health-diagnostics"></a>Diagnostyka kondycji

**Diagnostyka kondycji** strona umożliwia wizualizowanie model kondycji maszyny Wirtualnej. Ta strona zawiera listę wszystkich składników maszyn wirtualnych, kryteriów skojarzonej kondycji, zmian stanu i innych istotnych problemów identyfikowane za pomocą monitorowanych składników powiązane z maszyną wirtualną.

![Przykład strony Diagnostyka kondycji dla maszyny Wirtualnej](./media/vminsights-health/health-diagnostics-page-01.png)

Uruchomienie diagnostyki kondycji za pomocą następujących metod:

* Przez pakiet zbiorczy stan kondycji dla wszystkich maszyn wirtualnych z agregacji perspektywy maszyn wirtualnych w usłudze Azure Monitor:

    1. Na **kondycji** wybierz ikonę **krytyczny**, **ostrzeżenie**, **dobra kondycja**, lub **nieznany** stan kondycji, w sekcji **kondycji maszyny Wirtualnej gościa**.
    2. Przejdź do strony, która zawiera listę wszystkich maszyn wirtualnych dopasowywania kategorii filtrowane.
    3. Wybierz wartość w **stan kondycji** kolumnie, aby otworzyć diagnostykę kondycji ograniczone do tej maszyny Wirtualnej.

* Według systemu operacyjnego z agregacji perspektywy maszyn wirtualnych w usłudze Azure Monitor. W obszarze **dystrybucji maszyny Wirtualnej**, wybierając dowolną z wartości w kolumnie zostanie otwarty **maszyn wirtualnych** strony i powrócić do listy w tabeli dopasowywania kategorii filtrowane. Wybieranie wartości w obszarze **stan kondycji** kolumny otwiera kondycji Diagnostyka dla wybranej maszyny Wirtualnej.
 
* Z maszyny Wirtualnej w usłudze Azure Monitor dla maszyn wirtualnych gościa **kondycji** kartę, wybierając **Wyświetl diagnostykę kondycji**.

Diagnostyka kondycji porządkuje informacje o kondycji na dwie kategorie: dostępność i wydajność.
 
Bez filtrowania na dwie kategorie można wyświetlać wszystkie kryteria kondycji zdefiniowane dla składnika, takiego jak dysk logiczny w systemie, procesora CPU i tak dalej. Widoki te mogą znajdować się w widoku non stop kryteriów lub za pomocą filtrowania wyników według kategorii albo po wybraniu **dostępności** lub **wydajności**.

Ponadto kategorii kryteria są widoczne obok **kryteria kondycji** kolumny. Jeśli kryteria nie odpowiadają wybranej kategorii komunikat z informacją **kryteria kondycji, nie jest dostępne dla wybranej kategorii** pojawia się w **kryteria kondycji** kolumny.

Stan kryteria kondycji jest zdefiniowana za pomocą jednej z czterech typów: **Krytyczne**, **ostrzeżenie**, **dobrej kondycji**, i **nieznany**. Pierwsze trzy są konfigurowane, co oznacza, że można zmodyfikować wartości progowe monitorów bezpośrednio w **kryteria kondycji** okienko konfiguracji. Jest to możliwe również przy użyciu interfejsu API REST usługi Azure Monitor [zaktualizować monitor operacji](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Nieznany** nie można skonfigurować i jest zarezerwowane dla konkretnych scenariuszy.

**Diagnostyka kondycji** strona zawiera trzy główne sekcje:

* Model składników
* Kryteria kondycji
* Zmiany stanu

![Części strony diagnostyki kondycji](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Model składników

W lewej kolumnie **Diagnostyka kondycji** strona jest **składnika modelu**. Wszystkie składniki, które są skojarzone z maszyną Wirtualną, są wyświetlane w tej kolumnie, wraz z ich bieżącym stanem kondycji.

W poniższym przykładzie są składniki odkryte **dysku**, **dysku logicznego**, **procesora**, **pamięci**, i  **System operacyjny**. Wiele wystąpień tych składników są odnajdywane i wyświetlane w tej kolumnie.

Na przykład na poniższej ilustracji przedstawiono, że maszyna wirtualna ma dwa wystąpienia dysków logicznych **C:** i **D:** , które są w dobrej kondycji:

![Przykładowy model składnika prezentowane w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Kryteria kondycji

W środkowej kolumnie na stronie Diagnostyka kondycji jest **kryteria kondycji**. Model kondycji zdefiniowane dla maszyny Wirtualnej jest wyświetlany w formie drzewa hierarchicznego. Model kondycji dla maszyny Wirtualnej składa się z jednostek i kryteria kondycji agregacji.

![Kryteria kondycji przykład przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Kryterium kondycji mierzy kondycję monitorowanego wystąpienia, co może być wartość progową, stan jednostki i tak dalej. Kryterium kondycji ma dwa lub trzy progi stanu kondycji można skonfigurować, zgodnie z wcześniejszym opisem. W dowolnym momencie kryterium kondycji może być tylko jednego ze swoich potencjalnych stanów.

Model kondycji definiuje kryteria określające kondycji ogólnej obiektu docelowego i składniki obiektu docelowego. Hierarchia kryteriów jest wyświetlany w **kryteria kondycji** sekcji na **Diagnostyka kondycji** strony.

Zasady informacje zbiorcze o kondycji jest częścią konfiguracji kryteria kondycji agregacji (wartość domyślna jest równa **najgorszy z**). Domyślny zestaw kryteria kondycji uruchomiona w ramach tej funkcji można znaleźć [szczegóły konfiguracji monitorowania](#monitoring-configuration-details) dalszej części tego artykułu.

Można także użyć interfejsu API REST usługi Azure Monitor [monitorowanie wystąpienia listy przez zasób](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) w celu uzyskania listy wszystkich kryteria kondycji. Te kryteria zawiera szczegóły dotyczące konfiguracji działających w odniesieniu do zasobów maszyny Wirtualnej platformy Azure.

**Jednostki** typ kryteria kondycji może mieć jego konfigurację, zmodyfikować, wybierając łącze wielokropka po prawej stronie. Wybierz **Pokaż szczegóły** aby otworzyć okienko konfiguracji.

![Konfigurowanie przykładu kryteria kondycji](./media/vminsights-health/health-diagnostics-vm-example-02.png)

W okienku Konfiguracja kryteria kondycji wybrane, jeśli skorzystaj z przykładu **średni sekund na zapisu dysku**, próg, może być skonfigurowany z inną wartością liczbową. Monitor dwustanowy, co oznacza, można zmienić tylko z jest **dobra kondycja** do **ostrzeżenie**.

Inne kryteria kondycji czasami wykorzystują trzy stany, w którym można skonfigurować wartość ostrzegawczy i krytyczny stan kondycji wartości progowe. Możesz również zmodyfikować wartości progowej, przy użyciu interfejsu API REST usługi Azure Monitor [konfiguracji monitora](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Trwa stosowanie zmian konfiguracji kryteria kondycji z jednym wystąpieniem zastosuje je do wszystkich monitorowanych wystąpień. Na przykład w przypadku wybrania **dysku D:-1** , a następnie zmodyfikuj **średni sekund na zapisu dysku** próg, zmiana ma zastosowanie do wszystkich wystąpień odnalezione i monitorowane na maszynie Wirtualnej.


![Konfigurowanie kryteria kondycji w przykładzie monitora jednostki](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Jeśli chcesz dowiedzieć się więcej na temat kryteria kondycji, uwzględniliśmy artykuły merytoryczne, aby ułatwić identyfikację problemów, przyczyny i rozwiązania. Wybierz **służy do wyświetlania informacji** na stronie, aby wyświetlić powiązane wiedzy.

Aby przejrzeć wszystkie artykuły wiedzy zawarte w usłudze Azure Monitor dla kondycji maszyn wirtualnych, zobacz [usługi Azure Monitor kondycji wiedzy dokumentacji](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Zmiany stanu

Skrajna prawa kolumna z **Diagnostyka kondycji** strona jest **zmiany stanu**. Ta kolumna zawiera listę wszystkich zmian stanu skojarzonych z kryteria kondycji wybranego w **kryteria kondycji** sekcji lub Zmień stan maszyny wirtualnej w przypadku maszyny Wirtualnej została wybrana w zaufanym **składnika modelu** lub  **Kryteria kondycji** kolumnie tabeli.

![Zmiany stanu przykład przedstawiony w diagnostyce kondycji](./media/vminsights-health/health-diagnostics-page-statechanges.png)

W poniższej sekcji pokazano stan kryteria kondycji i czasu skojarzone. Te informacje pokazują najnowszy stan u góry kolumny.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Skojarzenie kolumny modelu składników, kryteria kondycji i zmiany stanu

Trzy kolumny są powiązane ze sobą. Po wybraniu wystąpienia w **składnika modelu** kolumnie **kryteria kondycji** kolumna jest filtrowana w celu widok tego składnika. Odpowiednio **zmiany stanu** kolumny jest aktualizowana, na podstawie kryteriów wybranych kondycji.

![Przykład wybierania monitorowanych wystąpień i wyniki](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Na przykład w przypadku wybrania *dysk — 1 D:* z listy w obszarze **składnika modelu**, **kryteria kondycji** filtrów *dysk — 1 D:* i  **Zmianie stanu** przedstawiono zmiany stanu na podstawie dostępności z *dysk — 1 D:* .

Aby wyświetlić stan kondycji zaktualizowane, można odświeżyć stronę diagnostyki kondycji, wybierając **Odśwież** łącza. W przypadku aktualizacji do stanu kondycji kryterium kondycji na podstawie wstępnie zdefiniowanych interwału sondowania, to zadanie pozwala uniknąć oczekiwania i odzwierciedla najnowszy stan kondycji. **Kryteria kondycji** jest filtr, który pozwala ograniczyć zakres wyniki oparte na stanie kondycji wybranego: Dobrej kondycji, ostrzeżenie, krytyczny, nieznana, a wszystkie. **Ostatniej aktualizacji** czasu w prawym górnym rogu reprezentuje czas ostatniego odświeżenia strony Diagnostyka kondycji.

## <a name="alerts"></a>Alerty

Usługa Azure Monitor kondycji maszyn wirtualnych integruje się z [Azure Alerts](../../azure-monitor/platform/alerts-overview.md). Zgłasza alert, gdy wstępnie zdefiniowanych kryteriów, po wykryciu, zmienić z dobrej kondycji stan złej kondycji. Alerty są pogrupowane według ważności, z ważność 0 do 4 ważność z ważność 0 jako najwyższy poziom.

Alerty nie są skojarzone z grupą akcji, aby otrzymywać powiadomienia, gdy alert został uruchomiony. Właściciel subskrypcji należy skonfigurować powiadomienia, wykonując kroki opisane w [Konfigurowanie alertów](#configure-alerts) sekcji.

Całkowita liczba alertów dotyczących kondycji maszyny Wirtualnej, pogrupowane według ważności jest dostępna na **kondycji** pulpit nawigacyjny w obszarze **alerty** sekcji. Po wybraniu łączną liczbę alertów albo liczbę odpowiadającą jej poziom ważności **alerty** zostanie otwarta strona i wyświetla listę wszystkich alertów pasujących wybór.

Na przykład w przypadku wybrania wiersz odpowiadający **poziom ważność 1**, zobaczysz następujący widok:

![Przykład wszystkie alerty o 1 poziom ważności](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**Wszystkie alerty** strona nie jest zakresem będą wyświetlane tylko alerty dopasowania wybór. Jest również filtrowany według **typ zasobu** aby pokazać tylko alerty dotyczące kondycji wygenerowane przez zasób maszyny Wirtualnej. Ten format jest odzwierciedlana na liście alertów, w kolumnie **zasób docelowy**, którym będzie wyświetlana maszyny Wirtualnej platformy Azure alert zostaje zgłoszone podczas złej kondycji warunek został spełniony.

Alerty z innych typów zasobów lub usług nie są przeznaczone do uwzględnienia w tym widoku. Te alerty zawierają alertów dzienników, które są oparte na kwerendach dziennika lub alertów metryk, które zwykle będzie wyświetlać z domyślnej usługi Azure Monitor [wszystkie alerty](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) strony.

Ten widok można filtrować, wybierając wartości w menu rozwijanych w górnej części strony.

|Kolumna |Opis |
|-------|------------|
|Subskrypcja |Wybierz subskrypcję platformy Azure. Tylko w wybranej subskrypcji są uwzględniane alerty w widoku. |
|Grupa zasobów |Wybierz pojedynczą grupę zasobów. Tylko alerty z usługą obiektów docelowych w wybranej grupie zasobów znajdują się w widoku. |
|Typ zasobu |Wybierz jeden lub więcej typów zasobów. Domyślnie tylko alerty docelowej **maszyn wirtualnych** jest zaznaczone, a następnie zawarte w tym widoku. W tej kolumnie jest dostępna tylko po określono grupę zasobów. |
|Resource |Wybierz zasób. Tylko alerty o zasobu jako obiekt docelowy znajdują się w widoku. W tej kolumnie jest dostępna tylko wtedy, gdy określono typu zasobu. |
|Severity |Wybierz alert o ważności lub **wszystkich** obejmujący alerty wszystkie poziomy ważności. |
|Warunek monitora |Wybierz warunek monitora do filtrowania alertów, jeśli zostały wyzwolone lub rozpoznana przez system, jeśli warunek nie jest już aktywna. Lub wybierz **wszystkich** obejmujący alerty wszystkie warunki. |
|Stan alertu |Wybierz stan alertu **New**, **potwierdzonym**, **zamknięte**, lub **wszystkich** obejmujący alerty wszystkich stanów. |
|Monitorowanie usługi |Wybierz usługę, lub wybierz **wszystkich** obejmujący wszystkie usługi. Tylko alerty z usługi Insights maszyny Wirtualnej są obsługiwane dla tej funkcji.|
|Przedział czasu| Tylko alerty wyzwalane w przedziale czasu wybranego znajdują się w widoku. Obsługiwane wartości to ostatniej godziny, ostatnich 24 godzin, w ciągu ostatnich 7 dni i ostatnich 30 dni. |

Po wybraniu alertu, **Alert szczegółów** zostanie wyświetlona strona. Ta strona zawiera szczegółowe informacje o alercie i pozwala zmienić jego stan.

Aby dowiedzieć się więcej na temat zarządzania alertami, zobacz [Utwórz, Wyświetl, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Tworzenie nowych alertów w oparciu o kryteria kondycji lub modyfikowania istniejących kondycji reguł alertów w usłudze Azure Monitor z poziomu portalu nie jest obecnie obsługiwane.


![W okienku szczegółów alertu wybranego alertu](./media/vminsights-health/alert-details-pane-01.png)

Stan alertu dla jednej lub wielu alertów można zmienić, wybierając je, a następnie wybierając **zmiany stanu** z **wszystkie alerty** strony w lewym górnym rogu. Wybierz jeden z stanów **Zmień stan alertu** okienko, Dodaj opis zmian w **komentarz** , a następnie wybierz opcję **Ok** aby zatwierdzić zmiany. Gdy informacje są weryfikowane, a zmiany zostaną zastosowane, śledzić postęp w obszarze **powiadomienia** w menu.

### <a name="configure-alerts"></a>Konfigurowanie alertów
Niektóre zadania zarządzania dla alertów nie można zarządzać w witrynie Azure portal. Te zadania muszą być wykonywane przy użyciu [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). W szczególności:

- Włączanie lub wyłączanie alertu dotyczącego kryteria kondycji
- Konfigurowanie powiadomienia o alertach kryteria kondycji

Każdy przykład używa [ARMClient](https://github.com/projectkudu/armclient) na komputerze Windows. Jeśli nie jesteś zaznajomiony z tej metody, zobacz [przy użyciu ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Włączanie lub wyłączanie reguły alertu

Aby włączyć lub wyłączyć alertu dotyczącego kondycji konkretnego kryteria, właściwość **alertGeneration** musi zostać zmodyfikowany za pomocą wartości **wyłączone** lub **włączone**.

Aby zidentyfikować *elementu monitorId* w kryteria kondycji konkretnego poniższy przykład przedstawia sposób wykonywania zapytań o tej wartości kryteriów **LogicalDisk\Avg dysku sekund na Transfer**:

1. W oknie terminalu wpisz **logowania armclient.exe**. Ten sposób wyświetli monit o logowanie do platformy Azure.

2. Wprowadź następujące polecenie, aby pobrać wszystkie aktywne w określonej maszyny Wirtualnej kryterium kondycji i zidentyfikować wartość *elementu monitorId* właściwości:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    Poniższy przykład przedstawia dane wyjściowe *armclient GET* polecenia. Zwróć uwagę na wartość *elementu MonitorId*. Ta wartość jest wymagana do kolejnego kroku miejscu należy określić identyfikator kryteria kondycji i zmodyfikować jego właściwości w celu utworzenia alertu.

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

3. Wprowadź następujące polecenie, aby zmodyfikować *alertGeneration* właściwości:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Wpisz polecenie GET, użyto w kroku 2, aby sprawdzić, czy wartość właściwości jest równa **wyłączone**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Skojarz grupy akcji z kryteria kondycji

Monitor kondycji maszyn wirtualnych platformy Azure obsługuje wiadomości SMS i powiadomień e-mail, gdy alerty są generowane na podstawie kryteriów zła kondycja. Aby skonfigurować powiadomienia, określ nazwę grupy akcji skonfigurowanego do wysyłania powiadomień programu SMS lub wiadomości e-mail.

>[!NOTE]
>Ta akcja muszą być wykonywane względem każdej monitorowanej maszyny Wirtualnej, którą chcesz otrzymywać powiadomienie o. Nie ma zastosowania do wszystkich maszyn wirtualnych w grupie zasobów.

1. W oknie terminalu wprowadź *logowania armclient.exe*. Ten sposób wyświetli monit o logowanie do platformy Azure.

2. Wprowadź następujące polecenie, aby skojarzyć grupy akcji przy użyciu reguł alertów:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Aby sprawdzić, czy wartość właściwości **actionGroupResourceIds** została pomyślnie zaktualizowana, wprowadź następujące polecenie:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    Dane wyjściowe powinny wyglądać następujące kryteria:
    
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

- Aby zidentyfikować, ograniczenia i ogólną wydajność maszyny Wirtualnej, zobacz [maszyny Wirtualnej platformy Azure w widoku wydajności](vminsights-performance.md).
- Aby dowiedzieć się więcej na temat zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
