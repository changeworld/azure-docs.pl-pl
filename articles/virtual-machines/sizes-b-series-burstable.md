---
title: Seria B z serii burstable - Maszyny wirtualne platformy Azure
description: W tym artykule opisano serię B serii serii burstable rozmiary maszyn wirtualnych platformy Azure.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161084"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych serii B z serii B

Maszyny wirtualne z serii B są idealne dla obciążeń, które nie wymagają ciągłej pełnej wydajności procesora, takich jak serwery sieci web, dowód koncepcji, małe bazy danych i środowiska kompilacji programistycznej. Te obciążenia zazwyczaj mają wymagania dotyczące wydajności serii. Seria B zapewnia możliwość zakupu rozmiaru maszyny Wirtualnej z wydajnością linii bazowej, a wystąpienie maszyny Wirtualnej tworzy kredyty, gdy używa mniej niż jego linii bazowej. Gdy maszyna wirtualna ma zgromadzone środki, maszyna wirtualna może pęknąć powyżej linii bazowej przy użyciu do 100% procesora wirtualnego, gdy aplikacja wymaga wyższej wydajności procesora CPU.

Seria B jest dostępna w następujących rozmiarach maszyn wirtualnych:

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Podstawowy procesor Akces dla maszyn wirtualnych | Maksymalna ilość procesora cpu maszyny wirtualnej | Kredyty początkowe | Kredyty bankowe /godz. | Maksymalne kredyty bankowe | Maks. liczba dysków danych | Maksymalna wydajność pamięci podręcznej i magazynu tymczasowego: IOPS/MB/s | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls jest obsługiwany tylko w systemie Linux

## <a name="workload-example"></a>Przykład obciążenia pracą

Rozważmy wniosek o zameldowanie/wyjście w biurze. Aplikacja wymaga serii procesora w godzinach pracy, ale nie dużo mocy obliczeniowej w godzinach wolnych od pracy. W tym przykładzie obciążenie wymaga 16vCPU maszyny wirtualnej z 64GiB pamięci RAM do wydajnej pracy.

Tabela zawiera dane o ruchu godzinowym, a wykres jest wizualną reprezentacją tego ruchu.

Charakterystyka B16:

Maksymalny przenik wydajności procesora: 16vCPU * 100% = 1600%

Wartość bazowa: 270%

![Wykres godzinowych danych o ruchu drogowym](./media/b-series-burstable/office-workload.png)

| Scenariusz | Time | Użycie procesora (%) | Kredyty zgromadzone<sup>1</sup> | Dostępne kredyty |
| --- | --- | --- | --- | --- |
| Wdrożenie B16ms | wdrażania | wdrażania  | 480 (kredyty początkowe) | 480 |
| Brak ruchu | 0:00 | 0 | 162 | 642 |
| Brak ruchu | 1:00 | 0 | 162 | 804 |
| Brak ruchu | 2:00 | 0 | 162 | 966 |
| Brak ruchu | 3:00 | 0 | 162 | 1128 |
| Brak ruchu | 4:00 | 0 | 162 | 1290 |
| Brak ruchu | 5:00 | 0 | 162 | 1452 |
| Niski ruch | 6:00 | 270 | 0 | 1452 |
| Pracownicy przychodzą do biura (aplikacja potrzebuje 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Pracownicy nadal przychodzą do biura (aplikacja potrzebuje 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Niski ruch | 9:00 | 270 | 0 | 240 |
| Niski ruch | 10:00 | 100 | 102 | 342 |
| Niski ruch | 11:00 | 50 | 132 | 474 |
| Niski ruch | 12:00 | 100 | 102 | 576 |
| Niski ruch | 13:00 | 100 | 102 | 678 |
| Niski ruch | 14:00 | 50 | 132 | 810 |
| Niski ruch | 15:00 | 100 | 102 | 912 |
| Niski ruch | 16:00 | 100 | 102 | 1014 |
| Pracownicy wyewidencjonowujący (aplikacja potrzebuje 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Niski ruch | 18:00 | 270 | 0 | 216 |
| Niski ruch | 19:00 | 270 | 0 | 216 |
| Niski ruch | 20:00 | 50 | 132 | 348 |
| Niski ruch | 21:00 | 50 | 132 | 480 |
| Brak ruchu | 22:00 | 0 | 162 | 642 |
| Brak ruchu | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Zakumulowane kredyty/kredyty wykorzystane `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`w ciągu godziny są równoważne: .  

W przypadku D16s_v3, która ma 16 procesorów wirtualnych i 64 GiB pamięci, stawka godzinowa wynosi 0,936 USD za godzinę (miesięcznie 673,92 USD), a w przypadku 16 mln B16 procesorów i 64 pamięci GiB stawka wynosi 0,794 USD za godzinę (miesięcznie 547,86 USD). <b>Daje to 15% oszczędności!</b>

## <a name="q--a"></a>Pytania i odpowiedzi

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Pyt.: Jak uzyskać 135% podstawowej wydajności z maszyny Wirtualnej?

**A:** 135% jest współużytkowane przez 8 procesorów wirtualnych, które tworzą rozmiar maszyny Wirtualnej. Na przykład jeśli aplikacja używa 4 z 8 rdzeni pracujących przy przetwarzaniu wsadowym, a każdy z tych 4 procesorów wirtualnych jest uruchomiony przy 30% wykorzystaniu, całkowita wydajność procesora maszyn wirtualnych będzie równa 120%.  Oznacza to, że maszyna wirtualna będzie budowanie czasu kredytu na podstawie 15% delta od wydajności podstawowej.  Ale oznacza to również, że gdy masz dostępne kredyty, że ta sama maszyna wirtualna może używać 100% wszystkich 8 procesorów wirtualnych dając, że maszyna wirtualna maksymalna wydajność procesora CPU 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: Jak mogę monitorować saldo kredytowe i zużycie

**Odp**.: W najbliższych tygodniach wprowadzimy 2 nowe dane, dane **kredytowe** pozwolą Ci zobaczyć, ile kredytów została naliczona twoja maszyna wirtualna, a wskaźnik **Zużytego Kredytu** pokazuje, ile kredytów procesora zużywała twoja maszyna wirtualna z banku.    Będzie można wyświetlić te metryki z okienka metryk w portalu lub programowo za pośrednictwem interfejsów API usługi Azure Monitor.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych metryk na platformie Azure, zobacz [Omówienie metryk na platformie Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated"></a>P: W jaki sposób są kumulowane kredyty?

**A:** Akumulacja maszyny Wirtualnej i stawki zużycia są ustawione w taki sposób, że maszyna wirtualna działająca na dokładnie jego podstawowym poziomie wydajności nie będzie miała ani akumulacji netto, ani zużycia kredytów pękających.  Maszyna wirtualna będzie miała wzrost netto kredytów, gdy działa poniżej jego poziomu wydajności podstawowej i będzie miał spadek netto kredytów, gdy maszyna wirtualna wykorzystuje procesor więcej niż jego podstawowy poziom wydajności.

**Przykład:** Wdrażam maszynę wirtualną przy użyciu rozmiaru B1ms dla mojej aplikacji bazy danych czasu i obecności. Ten rozmiar pozwala mojej aplikacji używać do 20% procesora wirtualnego jako mój punkt odniesienia, który jest 0,2 kredytów na minutę mogę użyć lub banku.

Moja aplikacja jest zajęta na początku i na końcu dnia pracy moich pracowników, między 7:00-9:00 AM i 4:00 - 18:00. W ciągu pozostałych 20 godzin dnia moja aplikacja jest zazwyczaj bezczynna, używając tylko 10% procesora wirtualnego. W godzinach poza szczytem zarabiam 0,2 kredytów na minutę, ale zużywam tylko 0,l kredytów na minutę, więc moja maszyna wirtualna będzie bank 0,1 x 60 = 6 kredytów na godzinę.  Przez 20 godzin, że jestem poza szczytem, będę bank 120 kredytów.  

W godzinach szczytu moja aplikacja średnio 60% wykorzystania vCPU, nadal zarabiam 0,2 kredytów na minutę, ale zużywam 0,6 kredytów na minutę, za koszt netto 0,4 kredytów na minutę lub 0,4 x 60 = 24 kredyty na godzinę. Mam 4 godziny dziennie szczytowego użycia, więc kosztuje 4 x 24 = 96 kredytów dla mojego szczytowego wykorzystania.

Jeśli wezmę 120 kredytów, które zarobiłem poza szczytem i odejmę 96 kredytów, których użyłem w godzinach szczytu, mam dodatkowe 24 kredyty dziennie, które mogę wykorzystać do innych wybuchów aktywności.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: Jak mogę obliczyć kredyty zgromadzone i wykorzystane?

**A**: Można użyć następującej formuły:

(Podstawowy przekształtnie procesora maszyn wirtualnych - użycie procesora) / 100 = Bank kredytów lub wykorzystanie na minutę

na przykład w powyższym wystąpieniu linia bazowa wynosi 20%, a jeśli używasz 10% akumulacji procesora (20%-10%)/100 = 0,1 kredytu na minutę.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Pyt.: Czy seria B obsługuje dyski danych magazynu w wersji Premium?

**A:** Tak, wszystkie rozmiary serii B obsługują dyski danych magazynu premium.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Dlaczego mój pozostały kredyt jest ustawiony na 0 po przesuwniu lub zatrzymaniu/uruchomieniu?

**A:** Gdy maszyna wirtualna jest "REDPLOYED" i maszyny Wirtualnej przenosi się do innego węzła, skumulowany kredyt jest tracona. Jeśli maszyna wirtualna jest zatrzymana/uruchomiona, ale pozostaje w tym samym węźle, maszyna wirtualna zachowuje zakumulowane środki. Za każdym razem, gdy maszyna wirtualna rozpoczyna się od nowa w węźle, pobiera początkowy kredyt, za Standard_B8ms jest to 240 minut.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Pyt.: Co się stanie, jeśli wyrozumieję nieobsługinięty obraz systemu operacyjnego w b1ls?

**A:** B1ls obsługuje tylko obrazy systemu Linux, a jeśli wdrożysz inny obraz systemu operacyjnego, możesz nie uzyskać najlepszego doświadczenia klienta.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.