---
title: Jak wykres wydajności z usługi Azure Monitor dla maszyn wirtualnych
description: Wydajność jest funkcją usługi Azure Monitor dla maszyn wirtualnych, która automatycznie odnajduje składniki aplikacji w systemach Windows i Linux i mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje na temat używania go w różnych scenariuszach.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283722"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Jak wykres wydajności z usługi Azure Monitor dla maszyn wirtualnych

Usługa Azure Monitor dla maszyn wirtualnych zawiera zestaw wykresów wydajności, które są przeznaczone dla kilku kluczowych wskaźników wydajności (KPI), aby ułatwić określenie, jak dobrze działa maszyna wirtualna. Wykresy pokazują wykorzystanie zasobów w okresie czasu, dzięki czemu można zidentyfikować wąskie gardła, anomalie lub przełączyć się do perspektywy zawierającej listę każdego komputera, aby wyświetlić wykorzystanie zasobów na podstawie wybranej metryki. Chociaż istnieje wiele elementów, które należy wziąć pod uwagę podczas radzenia sobie z wydajnością, usługa Azure Monitor dla maszyn wirtualnych monitoruje kluczowe wskaźniki wydajności systemu operacyjnego związane z procesorem, pamięcią, kartą sieciową i wykorzystaniem dysku. Wydajność uzupełnia funkcję monitorowania kondycji i pomaga ujawnić problemy, które wskazują na możliwą awarię składnika systemu, obsługę dostrajania i optymalizacji w celu osiągnięcia wydajności lub planowania pojemności pomocy technicznej.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektywa wielu maszyn wirtualnych z usługi Azure Monitor

Z usługi Azure Monitor funkcja wydajności zapewnia widok wszystkich monitorowanych maszyn wirtualnych wdrożonych w grupach roboczych w subskrypcjach lub w twoim środowisku. Aby uzyskać dostęp z usługi Azure Monitor, wykonaj następujące kroki. 

1. W witrynie Azure portal wybierz pozycję **Monitor**. 
2. Wybierz pozycję **Maszyny wirtualne** w sekcji **Rozwiązania.**
3. Wybierz kartę **Wydajność.**

![Widok listy top n statystyk maszyn wirtualnych](media/vminsights-performance/vminsights-performance-aggview-01.png)

Jeśli na karcie **Wykresy górne N** masz więcej niż jeden obszar roboczy usługi Log Analytics, wybierz obszar roboczy włączony z rozwiązaniem z selektora **obszaru roboczego** u góry strony. Selektor **grup** zwróci subskrypcje, grupy zasobów, [grupy komputerów](../platform/computer-groups.md)i zestawy skalowania maszyn wirtualnych komputerów związanych z wybranym obszarem roboczym, których można użyć do dalszego filtrowania wyników prezentowanych na wykresach na tej stronie i na innych stronach. Wybór dotyczy tylko funkcji Wydajność i nie jest przesuń do aplikacji Zdrowie ani mapa.  

Domyślnie wykresy pokazują ostatnie 24 godziny. Za pomocą selektora **TimeRange** można wyszukiwać historyczne zakresy czasu do 30 dni, aby pokazać, jak wydajność wyglądała w przeszłości.

Wykresy wykorzystania pięciu pojemności pokazane na stronie to:

* % wykorzystania procesora - pokazuje pięć najlepszych maszyn o najwyższym średnim wykorzystaniu procesora 
* Dostępna pamięć - pokazuje pięć najlepszych maszyn z najniższą średnią ilością dostępnej pamięci 
* Miejsce na dysku logicznym używane % — pokazuje pięć pierwszych maszyn o najwyższej średniej ilości miejsca na dysku % na wszystkich woluminach dysku 
* Bytes Sent Rate - pokazuje pięć najlepszych maszyn z najwyższą średnią wysłanych bajtów 
* Bytes Receive Rate - pokazuje pięć najlepszych maszyn z najwyższą średnią otrzymanych bajtów 

Kliknięcie ikony pinezki w prawym górnym rogu jednego z pięciu wykresów spowoduje przypięcie wybranego wykresu do ostatniego ostatnio oglądanego pulpitu nawigacyjnego platformy Azure.  Na pulpicie nawigacyjnym można zmienić rozmiar i zmienić położenie wykresu. Wybranie wykresu z pulpitu nawigacyjnego spowoduje przekierowanie do usługi Azure Monitor dla maszyn wirtualnych i załadowanie prawidłowego zakresu i widoku.  

Kliknięcie ikony znajdującej się po lewej stronie ikony pinezki na jednym z pięciu wykresów powoduje otwarcie widoku **Lista N górnej.**  W tym miejscu widać wykorzystanie zasobów dla tej metryki wydajności według poszczególnych maszyn wirtualnych w widoku listy i który komputer jest trendy najwyższy.  

![Widok listy N dla wybranej metryki wydajności](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Po kliknięciu na maszynie wirtualnej, **właściwości** okienka jest rozwijany po prawej stronie, aby wyświetlić właściwości wybranego elementu, takie jak informacje o systemie zgłaszanym przez system operacyjny, właściwości maszyny Wirtualnej platformy Azure, itp. Kliknięcie jednej z opcji w sekcji **Szybkie łącza** spowoduje przekierowanie do tej funkcji bezpośrednio z wybranej maszyny Wirtualnej.  

![Okienko Właściwości maszyny wirtualnej](./media/vminsights-performance/vminsights-properties-pane-01.png)

Przełącz się na kartę **Wykresy zagregowane,** aby wyświetlić metryki skuteczności filtrowane według średnich lub percentyli.  

![Widok agregacji wydajności aplikacji VM insights insights](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Dostępne są następujące wykresy wykorzystania pojemności:

* % wykorzystania procesora CPU — domyślne wartości pokazujące średnią i 95 percentyl 
* Dostępna pamięć — domyślne ustawienia pokazujące średnią, 5. i 10 percentyl 
* Użyte miejsce na dysku logicznym % — domyślne wartości pokazujące średnią i 95 percentyl 
* Współczynnik wysłanych bajtów — domyślne wyświetlanie średnich bajtów wysłanych 
* Współczynnik odbioru bajtów — domyślne wartości wyświetleń średnich odebranych bajtów

Można również zmienić ziarnistość wykresów w zakresie czasu, wybierając **opcję Średnia**, **Min**, **Max**, **50th**, **90th**i **95 w** selektorze percentyla.

Aby wyświetlić wykorzystanie zasobów przez poszczególne maszyny Wirtualne w widoku listy i zobaczyć, która maszyna jest trendy z najwyższym wykorzystaniem, wybierz **top N listy** kartę.  Strona **Lista Top N** pokazuje 20 najlepszych maszyn posortowanych według najczęściej wykorzystywanych przez 95 percentyl dla metrycznego wykorzystania *procesora %*.  Możesz zobaczyć więcej maszyn, wybierając **opcję Załaduj więcej,** a wyniki rozszerzają się, aby pokazać 500 najlepszych maszyn. 

>[!NOTE]
>Lista nie może wyświetlać więcej niż 500 maszyn naraz.  
>

![Przykład strony Listy N u góry N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Aby filtrować wyniki na określonej maszynie wirtualnej na liście, wprowadź jego nazwę komputera w polu tekstowym **Wyszukiwanie według nazwy.**  

Jeśli wolisz wyświetlić wykorzystanie z innej metryki wydajności, z listy rozwijanej **Metryka** wybierz pozycję **Dostępna pamięć**, Miejsce na **dysku logicznym używane %**, **Bajt/s Odebrane w sieci**lub **Bajty/s wysłane w sieci** i aktualizacje listy, aby wyświetlić wykorzystanie o zakresie do tej metryki.  

Wybranie maszyny wirtualnej z listy powoduje otwarcie panelu **Właściwości** po prawej stronie strony, a w tym miejscu można wybrać **opcję Szczegóły wydajności**.  Strona **Szczegóły maszyny wirtualnej** otwiera się i jest objęty zakresem tej maszyny Wirtualnej, podobnie w środowisku podczas uzyskiwania dostępu do wydajności usługi VM Insights bezpośrednio z maszyny Wirtualnej platformy Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Wyświetlanie wydajności bezpośrednio z maszyny Wirtualnej platformy Azure

Aby uzyskać dostęp bezpośrednio z maszyny wirtualnej, wykonaj następujące kroki.

1. W portalu Azure wybierz pozycję **Maszyny wirtualne**. 
2. Z listy wybierz maszynę wirtualną, a następnie w sekcji **Monitorowanie** wybierz pozycję **Insights**.  
3. Wybierz kartę **Wydajność.** 

Ta strona zawiera nie tylko wykresy wykorzystania wydajności, ale także tabelę przedstawiającą dla każdego wykrytego dysku logicznego, jego pojemność, wykorzystanie i średnią całkowitą według każdej miary.  

Dostępne są następujące wykresy wykorzystania pojemności:

* % wykorzystania procesora CPU — domyślne wartości pokazujące średnią i 95 percentyl 
* Dostępna pamięć — domyślne ustawienia pokazujące średnią, 5. i 10 percentyl 
* Użyte miejsce na dysku logicznym % — domyślne wartości pokazujące średnią i 95 percentyl 
* IOPS dysku logicznego - domyślne ustawienia pokazujące średnią i 95 percentyl
* Dysk logiczny MB/s - domyślne wartości pokazujące średnią i 95 percentyl
* Max Logical Disk Used % - domyślne wartości pokazujące średnią i 95 percentyl
* Współczynnik wysłanych bajtów — domyślne wyświetlanie średnich bajtów wysłanych 
* Współczynnik odbioru bajtów — domyślne wartości wyświetleń średnich odebranych bajtów

Kliknięcie ikony pinezki w prawym górnym rogu dowolnego wykresu przypina wybrany wykres do ostatniego wyświetlanego pulpitu nawigacyjnego platformy Azure. Na pulpicie nawigacyjnym można zmienić rozmiar i zmienić położenie wykresu. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje do usługi Azure Monitor dla maszyn wirtualnych i ładuje widok szczegółów wydajności maszyny wirtualnej.  

![VM insights Wydajność bezpośrednio z widoku maszyny Wirtualnej](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Wyświetlanie wydajności bezpośrednio z zestawu skalowania maszyny wirtualnej platformy Azure

Aby uzyskać dostęp bezpośrednio z zestawu skalowania maszyny wirtualnej platformy Azure, wykonaj następujące kroki.

1. W portalu Azure wybierz pozycję **Zestawy skalowania maszyny wirtualnej**.
2. Z listy wybierz maszynę wirtualną, a następnie w sekcji **Monitorowanie** wybierz pozycję **Insights,** aby wyświetlić kartę **Wydajność.**

Ta strona ładuje widok wydajności usługi Azure Monitor, o zakresie do wybranego zestawu skalowania. Dzięki temu można zobaczyć Top N wystąpień w skali ustawionej w zestawie monitorowanych metryk, wyświetlić wydajność agregacji w zestawie skalowania i zobaczyć trendy dla wybranych metryk w poszczególnych wystąpieniach n zestaw skalowania. Wybranie wystąpienia z widoku listy umożliwia załadowanie jego mapy lub przejście do szczegółowego widoku wydajności dla tego wystąpienia.

Kliknięcie ikony pinezki w prawym górnym rogu dowolnego wykresu przypina wybrany wykres do ostatniego wyświetlanego pulpitu nawigacyjnego platformy Azure. Na pulpicie nawigacyjnym można zmienić rozmiar i zmienić położenie wykresu. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje do usługi Azure Monitor dla maszyn wirtualnych i ładuje widok szczegółów wydajności maszyny wirtualnej.  

![VM insights Wydajność bezpośrednio z widoku zestawu skalowania maszyny wirtualnej](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Można również uzyskać dostęp do szczegółowego widoku wydajności dla określonego wystąpienia z widoku Wystąpienia dla zestawu skalowania. Przejdź do **pozycji Wystąpienia** w sekcji **Ustawienia,** a następnie wybierz pozycję **Insights**.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [skoroszytów dołączonych](vminsights-workbooks.md) do usługi Azure Monitor dla maszyn wirtualnych w celu dalszej analizy wydajności i metryk sieci.  

- Aby dowiedzieć się więcej o odnalezionych zależnościach aplikacji, zobacz [Wyświetlanie usługi Azure Monitor dla map maszyn wirtualnych.](vminsights-maps.md)
