---
title: Jak Wykres wydajności dzięki usłudze Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Wydajność jest funkcją monitora platformy Azure dla maszyn wirtualnych, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące sposobu używania go w różnych scenariuszach.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2019
ms.author: magoedte
ms.openlocfilehash: 4fa2553622d5ef2d08ec148b6a70aab6de257407
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848349"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Jak Wykres wydajności dzięki usłudze Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Usługa Azure Monitor dla maszyn wirtualnych zawiera zbiór wykresy wydajności przeznaczonych wykonuje kilka kluczowych wskaźników wydajności (KPI), aby ułatwić ustalenie, jak dobrze maszynę wirtualną. Wykresy pokazują wykorzystanie zasobów w określonym czasie, dzięki czemu możesz określić wąskie gardła, anomalie, lub przełącz się do perspektywy, wyświetlanie listy każdej maszyny w celu wyświetlenia wykorzystania zasobów, w oparciu o wybraną metryką. Dostępnych jest wiele elementów, należy wziąć pod uwagę podczas rozwiązywania problemów związanych z wydajnością, usługi Azure Monitor dla wskaźników wydajności systemu operacyjnego klucza monitory maszyny wirtualne dotyczące procesora, pamięci, karty sieciowej i wykorzystanie dysku. Wydajność uzupełniają funkcję monitorowania kondycji i pomaga ujawnić problemy, które wskazują możliwe awarii składników, dostrajanie pomocy technicznej i optymalizacji do osiągnięcia wydajności oraz obsługi planowania pojemności.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektywa wielu maszyn wirtualnych z usługi Azure Monitor
Z usługi Azure Monitor funkcja wydajności zapewnia widok wszystkie monitorowane maszyny wirtualne wdrażane w grupach roboczych w subskrypcji lub w danym środowisku. Aby uzyskać dostęp z usługi Azure Monitor, wykonaj następujące czynności. 

1. W witrynie Azure portal wybierz **Monitor**. 
2. Wybierz **maszyny wirtualne (wersja zapoznawcza)** w **rozwiązania** sekcji.
3. Wybierz **wydajności** kartę.

![Widok listy N pierwszych wydajności insights maszyny Wirtualnej](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Na **pierwszych N wykresy** kartę, jeśli masz więcej niż jeden obszar roboczy usługi Log Analytics, wybierz obszar roboczy z rozwiązania z włączoną **obszaru roboczego** selektor w górnej części strony. **Grupy** selektor zwróci subskrypcji, grupy zasobów [grup komputerów](../platform/computer-groups.md)i zestawy skalowania maszyn wirtualnych powiązanych z wybranym obszarem roboczym, którego można użyć, aby dokładniej przefiltrować komputerów wyniki przedstawione w wykresów na tej stronie i na innych stronach. Wybór tylko ma zastosowanie do funkcji, wydajności i nie przeniosą się do kondycji lub mapy.  

Domyślnie wykresy pokazują ostatnich 24 godzin. Za pomocą **TimeRange** selektor, możesz wyszukać zakresy czasu historycznych maksymalnie 30 dni, aby pokazać, jak wyglądał wydajności w przeszłości.   

Wykresy wykorzystanie pojemności pięć wyświetlany na stronie są:

* % Wykorzystania procesora CPU — przedstawiono najważniejsze pięć maszyn o największym wykorzystaniu procesora średniej 
* Dostępna pamięć - przedstawiono najważniejsze pięć maszyn za pomocą najmniejszej ilości dostępnej pamięci 
* Logiczne używane miejsce na dysku % — przedstawiono najważniejsze pięć maszyn z najwyższą przeciętna ilość miejsca używanych % wszystkie woluminy dysków 
* Bajty wysłane przepustowości — przedstawia pięć maszyn o najwyższej średniej ilości bajtów przesyłanych w 
* Szybkość odbierania bajtów — przedstawia pięć maszyn o najwyższej średniej ilości bajtów przesyłanych w 

Kliknij ikonę pinezki w prawym górnym rogu jednego z pięciu wykresy Przypnij wybranego wykresu do ostatniego platformy Azure pulpit nawigacyjny, który ostatnio wyświetlane.  Na pulpicie nawigacyjnym możesz zmienić rozmiar i zmienić położenie wykresu. Wybierając wykres na pulpicie nawigacyjnym przekierowanie do usługi Azure Monitor dla maszyn wirtualnych i załadować poprawny zakres i widoku.  

Kliknięcie ikony znajdującej się po lewej stronie ikonę pinezki na jednym z pięciu wykresy otwarcie **lista N najlepszych** widoku.  W tym miejscu zobaczysz wykorzystanie zasobów w przypadku tej metryki wydajności, poszczególnych maszyn wirtualnych w widoku listy i najwyższą trendu którego urządzenia.  

![Najważniejsze widok listy N metryki wydajności wybrane](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Po kliknięciu na maszynie wirtualnej **właściwości** podzielonego w okienku po prawej stronie, aby wyświetlić właściwości elementu zaznaczone, takie jak informacje o systemie zgłoszony przez system operacyjny, właściwości maszyny Wirtualnej platformy Azure, itd. Klikając jedną z opcji w obszarze **szybkich łączy** sekcji nastąpi przekierowanie do tej funkcji bezpośrednio z wybranej maszyny Wirtualnej.  

![W okienku właściwości maszyny wirtualnej](./media/vminsights-performance/vminsights-properties-pane-01.png)

Przełącz się do **zagregowane wykresy** kartę, aby wyświetlić metryki wydajności filtrowane według średniej lub percentyle miary.  

![Widok wydajności agregacji insights maszyny Wirtualnej](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Dostępne są następujące wykresy wykorzystanie pojemności:

* % Wykorzystania procesora CPU — ustawienia domyślne, pokazujący średnią i górnym używany 95. percentyl 
* Dostępna pamięć — ustawienia domyślne, przedstawiający średni, górny percentyl 5 i 10 
* Używane miejsce na dysku % logicznym - przedstawiający średnią i 95. percentyl wartości domyślne 
* Bajty wysłane przepustowości — ustawienia domyślne, pokazujący średnią liczbę bajtów wysłanych 
* Szybkość odbierania bajtów — wartości domyślne, pokazujący średnią liczbę bajtów odebranych

Możesz również zmienić stopień szczegółowości wykresy w zakresie czasu, wybierając **Avg**, **Min**, **Max**, **50.**,  **90**, i **95** w selektorze percentyl.   

Aby wyświetlić wykorzystanie zasobów przez poszczególne maszynę Wirtualną w widoku listy i zobacz, w jakim komputerze trendu o największym wykorzystaniu, wybierz **lista N najlepszych** kartę.  **Lista N najlepszych** strona zawiera najważniejsze 20 maszyn, posortowane według najczęściej wykorzystywanych przez 95. percentyl metryki *% wykorzystanie procesora CPU*.  Możesz zobaczyć więcej maszyn, wybierając **obciążenia więcej**, i wyniki rozszerzyć, aby wyświetlić maszyny pierwszych 500. 

>[!NOTE]
>Listy nie można wyświetlić więcej niż 500 maszyn w danym momencie.  
>

![Najważniejsze przykładowa strona listy N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Aby przefiltrować wyniki według określonej maszyny wirtualnej na liście, wprowadź jego nazwę komputera w **wyszukiwanie według nazwy** pola tekstowego.  

Jeśli wykorzystanie z metryki wydajności, zamiast będzie wyświetlić z **metryki** listy rozwijanej wybierz **dostępnej pamięci**, **logiczne używane miejsca na dysku %**,  **Sieci odebranych bajtów/s**, lub **sieci wysłanych bajtów/s** i listy aktualizacji do wyświetlenia wykorzystania ograniczone do tej metryki.  

Wybranie maszyny wirtualnej z listy powoduje otwarcie **właściwości** panelu w prawej części strony, a w tym miejscu można wybrać **szczegóły wydajności**.  **Szczegóły maszyny wirtualnej** zostanie otwarta strona i jest ograniczone do tej maszyny Wirtualnej, podobnie środowisko podczas uzyskiwania dostępu do maszyny Wirtualnej Insights wydajności bezpośrednio z poziomu maszyny Wirtualnej platformy Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Wyświetl wydajność bezpośrednio z Maszyną wirtualną platformy Azure
Aby uzyskać dostęp bezpośrednio z maszyny wirtualnej, wykonaj następujące czynności.

1. W witrynie Azure portal wybierz **maszyn wirtualnych**. 
2. Z listy wybierz Maszynę wirtualną i **monitorowanie** wybierz sekcję **Insights (wersja zapoznawcza)**.  
3. Wybierz **wydajności** kartę. 

Ta strona nie tylko zawiera wykresy wykorzystanie wydajności, ale także tabeli wyświetlane dla każdego dysku logicznego odnalezione i jego możliwości, wykorzystanie i łączna liczba średnia, każda miara.  

Dostępne są następujące wykresy wykorzystanie pojemności:

* % Wykorzystania procesora CPU — ustawienia domyślne, pokazujący średnią i górnym używany 95. percentyl 
* Dostępna pamięć — ustawienia domyślne, przedstawiający średni, górny percentyl 5 i 10 
* Używane miejsce na dysku % logicznym - przedstawiający średnią i 95. percentyl wartości domyślne 
* Dysk logiczny w systemie operacji We/Wy — pokazujący średnią i 95. percentyl wartości domyślne
* Logiczny dysku MB/s - przedstawiający średnią i 95. percentyl wartości domyślne
* Maksymalna liczba logicznych dysk używany % — pokazujący średnią i 95. percentyl wartości domyślne
* Bajty wysłane przepustowości — ustawienia domyślne, pokazujący średnią liczbę bajtów wysłanych 
* Szybkość odbierania bajtów — wartości domyślne, pokazujący średnią liczbę bajtów odebranych

Kliknij ikonę pinezki w prawym górnym rogu jeden z kodów PIN wykresy wybranego wykresu do ostatniego pulpitu nawigacyjnego platformy Azure można wyświetlić. Na pulpicie nawigacyjnym możesz zmienić rozmiar i zmienić położenie wykresu. Wybierając wykres na pulpicie nawigacyjnym przekieruje Cię do usługi Azure Monitor dla maszyn wirtualnych i ładuje widok szczegółów wydajności dla maszyny Wirtualnej.  

![Maszyna wirtualna insights wydajności bezpośrednio z maszyny Wirtualnej wyświetlić](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerts"></a>Alerty  
Metryki wydajności, włączone w ramach usługi Azure Monitor dla maszyn wirtualnych nie zawierają wstępnie skonfigurowanych reguł alertów. Istnieją [alerty dotyczące kondycji](vminsights-health.md#alerts) odpowiadający problemy z wydajnością wykryte na maszynie Wirtualnej platformy Azure, np. wysokie wykorzystanie procesora CPU, miejsca na dysku dostępna, niski małej ilości pamięci, itp.  Jednak te alerty dotyczące kondycji dotyczą tylko wszystkie maszyny wirtualne, włączone dla usługi Azure Monitor dla maszyn wirtualnych. 

Jednak może być tylko gromadzimy i przechowujemy podzbiór metryki wydajności, które są wymagane w obszarze roboczym usługi Log Analytics. Jeśli Twoja Strategia monitorowania wymaga analizy lub alerty, które obejmuje innych metryk wydajności, aby skutecznie obliczyć pojemności lub kondycji maszyny wirtualnej, jak i elastyczność, aby określić własne alerty kryteriom lub logiki, możesz to zrobić Konfigurowanie [zbieranie tych liczników wydajności](../platform/data-sources-performance-counters.md) w usłudze Log Analytics i zdefiniuj [alerty dzienników](../platform/alerts-log.md). Gdy usługi Log Analytics pozwala wykonywać złożone analizy przy użyciu innych typów danych i zapewnić dłuższe przechowywanie do obsługi analizy trendu, metryki z drugiej strony są uproszczone i zdolność do obsługi scenariuszy w czasie rzeczywistym w pobliżu. Są one zbierane przez [agenta diagnostyki Azure](../../virtual-machines/windows/monitor.md) i przechowywane w magazynie danych metryk usługi Azure Monitor, dzięki czemu możesz tworzyć alerty z mniejszymi opóźnieniami i przy niskich kosztach.

Przejrzyj Omówienie [zbieranie metryk i dzienników za pomocą usługi Azure Monitor](../platform/data-platform.md) Aby jeszcze lepiej zrozumieć podstawowe różnice i inne zagadnienia przed skonfigurowaniem zbierania tych dodatkowych metryk i reguł alertów.  

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak korzystać z funkcji health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](vminsights-maps.md). 