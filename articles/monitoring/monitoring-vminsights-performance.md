---
title: Jak Wykres wydajności maszyn wirtualnych z usługą Azure Monitor | Dokumentacja firmy Microsoft
description: Wydajność jest funkcją monitora platformy Azure dla maszyn wirtualnych, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące sposobu używania go w różnych scenariuszach.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2018
ms.author: magoedte
ms.openlocfilehash: 63549768f616e60e92c853047525c18cefdaddb4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386281"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Jak Wykres wydajności dzięki usłudze Azure Monitor dla maszyn wirtualnych
Usługa Azure Monitor dla maszyn wirtualnych zawiera zbiór wykresy wydajności przeznaczonych wykonuje kilka kluczowych wskaźników wydajności (KPI), aby ułatwić ustalenie, jak dobrze maszynę wirtualną. Wykresy pokazują wykorzystanie zasobów w określonym czasie, dzięki czemu możesz określić wąskie gardła, anomalie, lub przełącz się do perspektywy, wyświetlanie listy każdej maszyny w celu wyświetlenia wykorzystania zasobów, w oparciu o wybraną metryką. Dostępnych jest wiele elementów, należy wziąć pod uwagę podczas rozwiązywania problemów związanych z wydajnością, koncentruje się usługi Azure Monitor dla maszyn wirtualnych w systemie operacyjnym dyskowe widoczne za pośrednictwem procesora, pamięci, kart sieciowych i dysków. Wydajność uzupełniają funkcję monitorowania kondycji i pomaga ujawnić problemy, które wskazują możliwe awarii składników, dostrajanie pomocy technicznej i optymalizacji do osiągnięcia wydajności oraz obsługi planowania pojemności.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektywa wielu maszyn wirtualnych z usługi Azure Monitor
Z usługi Azure Monitor wydajności funkcji zawiera widok maszyn wirtualnych wielu wszystkie monitorowane maszyny wirtualne wdrażane w grupach zasobów w subskrypcji lub w środowisku.  Aby uzyskać dostęp z usługi Azure Monitor, wykonaj następujące czynności. 

1. W witrynie Azure portal wybierz **Monitor**. 
2. Wybierz **maszyny wirtualne (wersja zapoznawcza)** w **rozwiązania** sekcji.
3. Wybierz **wydajności** kartę.

![Widok listy N pierwszych wydajności insights maszyny Wirtualnej](./media/monitoring-vminsights-performance/vminsights-performance-aggview-01.png)

Na **pierwszych N wykresy** kartę, jeśli masz więcej niż jeden obszar roboczy usługi Log Analytics, wybierz obszar roboczy z rozwiązania z włączoną **obszaru roboczego** selektor w górnej części strony. **Grupy** selektor zwróci subskrypcji, grupy zasobów [grup komputerów](../log-analytics/log-analytics-computer-groups.md), a usługi VM scale sets komputerów powiązanych z wybranym obszarem roboczym, którego można użyć, aby dokładniej przefiltrować wyniki przedstawione w przypadku wykresów na tej stronie i na innych stronach. Wybór tylko ma zastosowanie do funkcji, wydajności i nie przeniosą się do kondycji lub mapy.  

Domyślnie wykresy pokazują ostatnich 24 godzin. Za pomocą **TimeRange** selektor, możesz wyszukać zakresy czasu historycznych maksymalnie 30 dni, aby pokazać, jak wyglądał wydajności w przeszłości.   

Wykresy wykorzystanie pojemności pięć wyświetlany na stronie są:

* Pokazuje, % wykorzystania procesora CPU — 5 pierwszych maszyn o największym wykorzystaniu procesora średniej 
* Dostępna pamięć - przedstawiono najważniejsze 5 maszyn za pomocą najmniejszej ilości dostępnej pamięci 
* Logiczne używane miejsce na dysku % — przedstawiono najważniejsze 5 maszyn z najwyższą przeciętna ilość miejsca używanych % wszystkie woluminy dysków 
* Bajty wysłane przepustowości — pokazuje 5 pierwszych maszyn o najwyższej średniej ilości bajtów przesyłanych w 
* Szybkość odbierania bajtów — pokazuje 5 pierwszych maszyn o najwyższej średniej ilości bajtów przesyłanych w 

Kliknięcie przycisku w prawym górnym rogu jednego z pięciu wykresy otwiera **lista N najlepszych** widoku.  W tym miejscu zobaczysz wykorzystanie zasobów w przypadku tej metryki wydajności, poszczególnych maszyn wirtualnych w widoku listy i najwyższą trendu którego urządzenia.  

![Najważniejsze widok listy N metryki wydajności wybrane](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

Po kliknięciu na maszynie wirtualnej **właściwości** podzielonego w okienku po prawej stronie, aby wyświetlić właściwości elementu zaznaczone, takie jak informacje o systemie zgłoszony przez system operacyjny, właściwości maszyny Wirtualnej platformy Azure, itd. Klikając jedną z opcji w obszarze **szybkich łączy** sekcji nastąpi przekierowanie do tej funkcji bezpośrednio z wybranej maszyny Wirtualnej.  

![W okienku właściwości maszyny wirtualnej](./media/monitoring-vminsights-performance/vminsights-properties-pane-01.png)

Przełącz się do **zagregowane wykresy** kartę, aby wyświetlić metryki wydajności filtrowane według średniej lub percentyle miary.  

![Widok wydajności agregacji insights maszyny Wirtualnej](./media/monitoring-vminsights-performance/vminsights-performance-aggview-02.png)

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

![Najważniejsze przykładowa strona listy N](./media/monitoring-vminsights-performance/vminsights-performance-topnlist-01.png)

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

![Maszyna wirtualna insights wydajności bezpośrednio z maszyny Wirtualnej wyświetlić](./media/monitoring-vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerting-and-alert-management"></a>Zarządzanie alertów i alert 
Metryki wydajności, włączone w ramach usługi Azure Monitor dla maszyn wirtualnych nie zawierają wstępnie skonfigurowanych reguł alertów. Gdy istnieją alerty dotyczące kondycji odpowiadający problemy z wydajnością wykryte na maszynie Wirtualnej platformy Azure, np. wysokie wykorzystanie procesora CPU, niski dostępnej pamięci, mało miejsca na dysku, itp., te alerty dotyczące kondycji, są stosowane tylko do wszystkich maszyn wirtualnych podłączone do tego samego obszaru roboczego usługi Log Analytics zintegrowana z usługą Azure Monitor dla maszyn wirtualnych. Jeśli chcesz zachować elastyczność, aby określić swoje własne kryteria lub logiki, można tworzyć niestandardowe reguły alertów, wykonując [Utwórz, Wyświetl, alerty i zarządzaj nimi przy użyciu usługi Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak korzystać z funkcji health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](monitoring-vminsights-health.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](monitoring-vminsights-maps.md). 