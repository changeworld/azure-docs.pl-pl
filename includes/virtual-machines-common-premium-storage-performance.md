---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 12bcf665fafca3df7fc2d21c77c2f8d2fbec84fc
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58542314"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Usługi Azure premium storage: Projektowanie pod kątem wysokiej wydajności

Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Można użyć w instrukcjach podanych w tym dokumencie, w połączeniu z najlepsze rozwiązania w zakresie wydajności mające zastosowanie do technologii wykorzystywanych przez aplikację. Aby zilustrować wytycznych, użyliśmy SQL Server uruchomiony na usługę Premium Storage, na przykład w tym dokumencie.

Gdy zajmujemy się scenariuszy wydajności dla warstwy magazynu, w tym artykule, należy zoptymalizować warstwy aplikacji. Na przykład jeśli hostujesz farmy programu SharePoint w usłudze Azure Premium Storage umożliwia przykładów programu SQL Server z tego artykułu zoptymalizować serwera bazy danych. Ponadto można zoptymalizować, serwer sieci Web farmy programu SharePoint i serwera aplikacji, aby uzyskać większość wydajność.

Ten artykuł pomoże odpowiedzi następujące często zadawane pytania na temat optymalizowania wydajności aplikacji w magazynie Azure Premium Storage

* Jak zmierzyć wydajność aplikacji?  
* Dlaczego użytkownik nie ma oczekiwanego o wysokiej wydajności?  
* Jakie czynniki mają wpływ na wydajność aplikacji w magazynie Premium Storage?  
* Jak te czynniki mają wpływ wydajność aplikacji w magazynie Premium Storage?  
* Jak można zoptymalizować operacje We/Wy, przepustowości i opóźnienia  

Te wytyczne zostały zamieszczone specjalnie dla usługi Premium Storage, ponieważ obciążeń działających na usługę Premium Storage o wysokiej wydajności poufnych. Przykłady zostały zamieszczone, gdzie jest to odpowiednie. Można również zastosować niektóre z poniższych wskazówek do aplikacji działających na maszynach wirtualnych IaaS z dysków magazynu w warstwie standardowa.

> [!NOTE]
> Czasami prawdopodobnie problem z wydajnością dysku jest faktycznie wąskich gardeł. W takich sytuacjach należy zoptymalizować swoje [wydajność sieci](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md).
> Jeśli maszyna wirtualna obsługuje przyspieszonej łączności sieciowej, należy się upewnić, że jest ono włączone. Jeśli nie jest włączone, możesz je włączyć na już wdrożone maszyny wirtualne zarówno [Windows](../articles/virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../articles/virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed przystąpieniem do wykonywania, jeśli jesteś nowym użytkownikiem magazynu w warstwie Premium, najpierw przeczytać artykuł [wybierz typ dysku platformy Azure dla maszyn wirtualnych IaaS](../articles/virtual-machines/windows/disks-types.md) i [usługi Azure Storage dotyczące skalowalności i cele wydajności](../articles/storage/common/storage-scalability-targets.md) artykułów.

## <a name="application-performance-indicators"></a>Wskaźniki wydajności aplikacji

Możemy ocenić, czy aplikacja działa dobrze lub nie korzysta z takich jak wskaźniki wydajności, jak szybko aplikacji przetwarza żądanie użytkownika, jak dużo danych aplikacji jest przetwarzanie na żądanie, jak wiele żądań przetwarza aplikacji w konkretnym okres czasu, jak długo użytkownik będzie musiał czekać na uzyskanie odpowiedzi po przesłaniu żądania. Pomoc ze wszystkimi postanowieniami dotyczącymi te wskaźniki wydajności są operacje We/Wy, przepustowości lub przepustowości i opóźnień.

W tej sekcji omówiono typowe wskaźniki wydajności w ramach usługi Premium Storage. W poniższej sekcji wymagania aplikacji zbieranie, dowiesz się, jak mierzyć te wskaźniki wydajności aplikacji. Optymalizowanie wydajności aplikacji w dalszej części dowiesz się o czynniki wpływające na te wskaźniki wydajności i zalecenia dotyczące optymalizowania je.

## <a name="iops"></a>Operacje wejścia/wyjścia

Operacje We/Wy lub we/wy operacji na sekundę, to liczba żądań, które aplikacja wysyła do dysków w magazynie w ciągu jednej sekundy. Operacji We/Wy może odczytać lub zapisu sekwencyjnego lub losowych. Online aplikacji przetwarzania transakcji (OLTP), takich jak witryny sieci Web detalicznego handlu online należy natychmiast przetwarza wiele równoczesnych żądań użytkowników. Żądania użytkowników są insert i zaktualizuj transakcji bazy danych, które aplikacja musi przetworzyć szybko. W związku z tym aplikacje OLTP wymagają bardzo wysokiej operacje We/Wy. Takie aplikacje obsługiwać miliony żądań We/Wy małe i losowe. Jeśli masz taką aplikację, należy zaprojektować infrastruktury aplikacji, aby zoptymalizować operacje We/Wy. W dalszej części *Optymalizowanie wydajności aplikacji*, omówimy szczegółowo wszystkich czynników, które należy wziąć pod uwagę uzyskać wysoki operacje We/Wy.

Po dołączeniu dysku magazynu premium do Twojej dużej skali maszyny Wirtualnej, zapisów systemu Azure dla Ciebie gwarantowane liczba operacji We/Wy zgodnie ze specyfikacją dysku. Na przykład dysk P50 aprowizuje 7500 operacji We/Wy. Każdy dużej skali, rozmiar maszyny Wirtualnej ma również określony limit operacji We/Wy, która może kontynuować działanie. Na przykład standardowa maszyna wirtualna GS5 ma 80 000 operacji We/Wy Ogranicz.

## <a name="throughput"></a>Przepływność

Przepływność lub przepustowości jest ilość danych, który aplikacja wysyła na dyski magazynu w określonym przedziale czasu. Jeśli aplikacja działa operacji wejścia/wyjścia o dużych rozmiarach jednostki we/wy, wymaga wysokiej przepływności. Zwykle aplikacji do magazynu danych do wysyłania skanowania intensywnie wykorzystujących operacje dostępu dużej części danych w czasie i często wykonywały operacje zbiorcze. Innymi słowy takich aplikacji wymaga większej przepływności. Jeśli masz taką aplikację, należy zaprojektować swoją infrastrukturę, aby zoptymalizować przepływność. W następnej sekcji omówiono szczegółowo czynników należy dostosować do osiągnięcia tego.

Po dołączeniu dysku magazynu premium do dużej skali maszyny Wirtualnej, przepływność zapisów systemu Azure zgodnie z specyfikacją tego dysku. Na przykład dysk P50 aprowizuje 250 MB na drugim przepływność dysku. Każdy dużej skali, rozmiar maszyny Wirtualnej ma również jako limit przepływności określonych, który może kontynuować działanie. Na przykład standardowa maszyna wirtualna GS5 zawiera maksymalnie 2000 MB na sekundę.

Brak relacji między przepływność i operacje We/Wy, jak pokazano na poniższej formuły.

![Relacja operacje We/Wy i przepływności](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

W związku z tym jest ważne jest określenie optymalnej przepływności i wartości operacje We/Wy, których wymaga aplikacja. Przy próbie optymalizacji, jeden, druga pobiera dotyczy także. W dalszej części tego tematu *Optymalizowanie wydajności aplikacji*, omówimy bardziej szczegółowo o optymalizacji operacji We/Wy i przepływność.

## <a name="latency"></a>Opóźnienie

Opóźnienie to czas potrzebny aplikacji do odbierania pojedynczego żądania, wyślij go do dyski magazynu i czy wysłać odpowiedź do klienta. Jest to krytyczne miary wydajności aplikacji, oprócz operacje We/Wy i przepływność. Opóźnienie dysku magazynu premium to czas potrzebny do pobrania informacji dla żądania i przekazuje ją do swojej aplikacji. Usługa Premium Storage zapewnia stale niskimi opóźnieniami. Dyski w warstwie Premium są przeznaczone do zapewnienia milisekundowe opóźnienia większość operacji We/Wy. Jeśli zostanie włączone buforowanie na dyskach magazynu premium hosta tylko do odczytu, możesz uzyskać znacznie mniejsze opóźnienie odczytu. Omówimy buforowania dysku bardziej szczegółowo w dalszej części tego tematu na *Optymalizowanie wydajności aplikacji*.

Podczas optymalizacji aplikacji w taki sposób, aby uzyskać wyższą operacje We/Wy i przepływność ma wpływ na opóźnienia w aplikacji. Po dostrajanie wydajności aplikacji, zawsze należy przeprowadzić ocenę opóźnienie aplikacji, aby uniknąć zachowania nieoczekiwany duże opóźnienie.

Następujące operacje warstwy kontroli na dyskach zarządzanych może obejmować przenoszenie dysków z jednej lokalizacji magazynu do innego. Jest to zorganizowanych za pośrednictwem tła kopię danych, który może zająć kilka godzin, zależnie od ilości danych na dyskach zazwyczaj mniej niż 24 godziny. W tym czasie aplikacji mogą występować wyższe niż zwykle opóźnienia odczytu niektórych odczyty mogą uzyskać przekierowane do oryginalnej lokalizacji i może potrwać dłużej. Nie ma to wpływu na opóźnienie zapisu w tym okresie.

- Zaktualizuj typ magazynu.
- Odłącz i dołączanie dysku z jednej maszyny Wirtualnej do innego.
- Tworzenie dysku zarządzanego na podstawie dysku VHD.
- Tworzenie dysku zarządzanego na podstawie migawki.
- Konwertowanie dysków niezarządzanych do dysków zarządzanych.

# <a name="performance-application-checklist-for-disks"></a>Lista kontrolna dotycząca wydajności aplikacji dla dysków

Pierwszym etapem projektowanie wysoko wydajnych aplikacji uruchomionych na platformie Azure Premium Storage jest zrozumienie, wymagania dotyczące wydajności aplikacji. Po zgromadzeniu wymagań dotyczących wydajności, można zoptymalizować aplikację, aby uzyskać najbardziej optymalną wydajność.

W poprzedniej sekcji firma Microsoft opisano typowe wskaźniki wydajności, operacje We/Wy, przepustowości i opóźnień. Należy określić, które te wskaźniki wydajności są kluczowe znaczenie dla aplikacji w taki sposób, aby zapewnić obsługę odpowiedniego użytkownika. Na przykład wysoką operacje We/Wy dla Ciebie ważne aplikacje OLTP przetwarzanie milionów transakcji na sekundę. Wysoka przepływność jest krytyczne dla magazynu danych aplikacji, przetwarzania dużych ilości danych na sekundę. Bardzo małe opóźnienia jest kluczowa dla aplikacji w czasie rzeczywistym, takich jak wideo na żywo, przesyłanie strumieniowe witryn sieci Web.

Następnie zmierz wymagania maksymalną wydajność aplikacji w taki sposób, w okresie swojego istnienia. Użyj poniższej listy kontrolnej przykładowe jako rozpoczęcia. Zapisz wymagania dotyczące maksymalnej wydajności podczas normalnego, szczytowe i okresów obciążenia poza godzinami szczytu. Identyfikując wymagania dla wszystkich poziomów obciążeń, można określić ogólne wymagania dotyczące działania aplikacji. Na przykład zwykłe obciążenie pracą w witrynie sieci Web handlu elektronicznego będzie transakcje, które służy ona większość dni w roku. Szczytowego obciążenia w witrynie sieci Web będzie transakcje, które służy ona podczas navaneetha krishnan lub specjalne sprzedaży zdarzenia. Szczytowego obciążenia jest zazwyczaj doświadczonym przez ograniczony okres, ale mogą wymagać od aplikacji do skalowania dwa lub więcej razy, jego normalnego działania. Dowiedz się, 50. percentyl, 90. percentyl i wymagania 99. percentylu. Dzięki temu odfiltrować jakiekolwiek elementy odstające w wymagania dotyczące wydajności i możesz skoncentrować się na dotyczące optymalizacji dla prawej wartości.

## <a name="application-performance-requirements-checklist"></a>Lista kontrolna wymagania dotyczące wydajności aplikacji

| **Wymagania dotyczące wydajności** | **50. percentyl** | **90 Percentile** | **99  Percentile** |
| --- | --- | --- | --- |
| Maksymalnie z Transakcje na sekundę | | | |
| Operacje odczytu w % | | | |
| % Operacji zapisu | | | |
| % Losowe operacje | | | |
| % Operacji sekwencyjnego | | | |
| Rozmiar żądania operacji We/Wy | | | |
| Średnia przepustowość | | | |
| Maksymalnie z Przepływność | | | |
| Min. Opóźnienie | | | |
| Średni czas oczekiwania | | | |
| Maksymalnie z Procesor CPU | | | |
| Średnie użycie procesora CPU | | | |
| Maksymalnie z Memory (Pamięć) | | | |
| Średni pamięci | | | |
| Głębokość kolejki | | | |

> [!NOTE]
> Należy rozważyć skalowanie te liczby, oparte na oczekiwany przyszłego rozwoju aplikacji. Jest dobrze się Planowanie wzrostu wyprzedzeniem, ponieważ może on być trudniejsze do zmiany infrastruktura dla poprawy wydajności później.

Jeśli masz istniejącą aplikację i chcesz przenieść do usługi Premium Storage, należy najpierw utworzyć z listy kontrolnej zawierającej ponad istniejących aplikacji. Następnie tworzenie prototypów aplikacji w magazynie Premium Storage i projektowania aplikacji, w oparciu o wytycznymi opisany w *Optymalizowanie wydajności aplikacji* w dalszej części tego dokumentu. W następnym artykule opisano narzędzia, których można użyć w celu zbierania miar wydajności.

### <a name="counters-to-measure-application-performance-requirements"></a>Licznik do pomiaru wymagań dotyczących wydajności aplikacji

Jest najlepszym sposobem, aby zmierzyć wymagań dotyczących wydajności aplikacji, za pomocą narzędzi monitorowania wydajności, dostarczone przez system operacyjny serwera. Można użyć Monitora wydajności dla Windows i iostat dla systemu Linux. Te narzędzia przechwytują liczniki odpowiadający Każda miara wyjaśniono w powyższej sekcji. Gdy aplikacja jest uruchomiona na jej normalne, konieczne jest przechwycenie wartości z tych liczników szczycie i poza godzinami szczytu obciążeń.

Liczniki Monitora wydajności są dostępne dla procesora, pamięci, a każdy dysk logiczny w systemie i dysku fizycznego serwera. Korzystając z dysków magazynu premium storage z maszyną Wirtualną, są liczniki dysku fizycznego dla każdego dysku magazynu premium, a dysk logiczny w systemie liczniki są dla każdego woluminu, utworzone na dyskach magazynu premium storage. Konieczne jest przechwycenie wartości dla dysków, które hostują obciążenia aplikacji. Jeśli istnieje mapowanie jeden-do-jednego między dysków logicznych i fizycznych, może odnosić się do liczników dysków fizycznych; w przeciwnym razie można znaleźć liczniki dysk logiczny w systemie. W systemie Linux polecenie iostat generuje raport o wykorzystaniu Procesora i dysku. Raport o wykorzystaniu dysku zapewnia statystyk na urządzenie fizyczne lub partycji. Jeśli masz serwer bazy danych przy użyciu jego danych i dzienniki na oddzielnych dyskach, zbierać dane dla obu dyskach. Poniższej tabeli opisano liczniki dla dysków, procesory i pamięć:

| Licznik | Opis | Monitora wydajności | iostat |
| --- | --- | --- | --- |
| **Operacje We/Wy lub transakcji na sekundę** |Liczba żądania We/Wy na dysk magazynujący na sekundę. |Odczyty dysku/s <br> Zapisy dysku/s |tps <br> r/s <br> w/s |
| **Dysk odczyty i zapisy** |% operacji odczytu i zapisu operacji wykonywanych na dysku. |Czas odczytu dysku % <br> Czas zapisu na dysku % |r/s <br> w/s |
| **Przepływność** |Ilość danych odczytu lub zapisu na dysku na sekundę. |Bajty odczytu z dysku/s <br> Bajty zapisu na dysku/s |kB_read/s <br> kB_wrtn/s |
| **Opóźnienie** |Łączny czas wymagany do wykonania żądania We/Wy dysku. |Średnia dysku w s/Odczyt <br> Średnia dysku w s/Zapis |Operator await <br> svctm |
| **Rozmiar we/wy** |Rozmiar operacji We/Wy żądań problemy, aby dyski magazynu. |Bajty odczytu dysku <br> Bajty zapisu dysku |avgrq-sz |
| **Queue Depth** |Liczba oczekujących operacji We/Wy żądań oczekiwania, które można odczytać lub zapisywane na dysku magazynu. |Bieżąca długość kolejki dysku |avgqu sz |
| **Maksymalna liczba. Pamięć** |Ilość pamięci wymaganej do uruchomienia aplikacji sprawnie |% Przydzielonych bajtów w użyciu |Użyj vmstat |
| **Maksymalna liczba. PROCESOR CPU** |Kwota wymagana do uruchamiania aplikacji sprawnie procesora CPU |Czas procesora (%) |% util |

Dowiedz się więcej o [iostat](https://linux.die.net/man/1/iostat) i [monitora wydajności](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Optymalizowanie wydajności aplikacji

Główne czynniki wpływające na wydajność aplikacji uruchomionych na magazyn w warstwie Premium są żądania charakter operacji We/Wy, rozmiar maszyny Wirtualnej, rozmiaru dysku, liczby dysków, buforowania dysku, wielowątkowości i głębokość kolejki. Za pomocą pokrętła podał systemu, można kontrolować niektóre z tych czynników. Większość aplikacji może nie zapewniają możliwość bezpośrednio zmieniać rozmiar operacji We/Wy i głębokość kolejki. Na przykład jeśli używasz programu SQL Server, nie możesz wybrać głębokość rozmiar i kolejki we/wy. SQL Server wybiera optymalną we/wy rozmiar i kolejki głębokość wartości w celu uzyskania większość wydajności. Ważne jest zrozumienie wpływu oba rodzaje czynniki na wydajność aplikacji, aby aprowizować odpowiednich zasobów w celu spełnienia wymagań dotyczących wydajności.

W tej sekcji dotyczą Lista kontrolna wymagań aplikacji, który został utworzony, aby zidentyfikować, ile należy zoptymalizować wydajność aplikacji. Oparte na tym, będzie możliwe ustalenie, jakie czynniki w tej sekcji należy dostosować. Aby Monitor wpływ każdy czynnik na wydajność aplikacji, należy uruchomić narzędzi porównawczych w ustawieniach aplikacji. Zapoznaj się z sekcją Benchmarking na końcu tego artykułu kroków wspólnych narzędzi porównawczych systemem Windows i maszyn wirtualnych systemu Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optymalizacja operacji We/Wy, przepustowości i opóźnień, w skrócie

W poniższej tabeli podsumowano czynnikami wydajnościowymi i kroki niezbędne do optymalizacji operacji We/Wy, przepustowości i opóźnień. W sekcjach poniżej to podsumowanie opisano każdy czynnikiem jest znacznie bardziej szczegółowe informacje.

Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych i operacje We/Wy, przepływności i opóźnień dostępne dla każdego typu maszyny Wirtualnej, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md) lub [rozmiarów maszyn wirtualnych Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Przepływność** | **Opóźnienie** |
| --- | --- | --- | --- |
| **Przykładowy scenariusz** |Aplikacja przedsiębiorstwa OLTP wymagające bardzo duże transakcje stawki za drugim. |Dane organizacji, magazynowanie aplikacji przetwarzania dużych ilości danych. |Niemal w czasie rzeczywistym aplikacje wymagające natychmiastowej odpowiedzi żądań użytkownika, takie jak gier online. |
| Czynnikami wydajnościowymi | &nbsp; | &nbsp; | &nbsp; |
| **Rozmiar we/wy** |Mniejszy rozmiar operacji We/Wy daje wyższe operacje We/Wy. |Większego rozmiaru operacji We/Wy do daje większą przepływność. | &nbsp;|
| **Rozmiar maszyny wirtualnej** |Użyj rozmiaru maszyny Wirtualnej, pozwalającą na operacje We/Wy większe wymagania Twojej aplikacji. |Rozmiar maszyny Wirtualnej za pomocą limit przepływności jest większa niż wymaganiami aplikacji. |Użyj rozmiaru maszyny Wirtualnej, czy oferuje skalowanie limity większa niż wymaganiami aplikacji. |
| **Rozmiar dysku** |Użyj rozmiaru dysku, pozwalającą na operacje We/Wy większe wymagania Twojej aplikacji. |Rozmiar dysku za pomocą limit przepływności jest większa niż wymaganiami aplikacji. |Użyj rozmiaru dysku, czy oferuje skalowanie limity większa niż wymaganiami aplikacji. |
| **Maszyna wirtualna i limity skalowania dysku** |Limit operacji We/Wy wybrany rozmiar maszyny Wirtualnej powinna być większa niż łączna liczba operacji We/Wy wynika z dysków magazynu premium storage dołączono do niego. |Limit przepływności wybrany rozmiar maszyny Wirtualnej powinna być większa niż całkowita przepływność wynika z dysków magazynu premium storage dołączono do niego. |Limity skalowania wybrany rozmiar maszyny Wirtualnej musi być większa niż limity skalowania łączna liczba dysków magazynu premium dołączonych. |
| **Buforowanie dysku** |Włączenie pamięci podręcznej tylko do odczytu dla dysków magazynu premium storage z dużymi operacje odczytu, aby uzyskać wyższy operacje odczytu We/Wy. | &nbsp; |Włączenie pamięci podręcznej tylko do odczytu dla dysków magazynu premium storage gotowe mocno operacje można pobrać odczytu bardzo małe wartości opóźnienia. |
| **Rozkładanie** |Używać wielu dysków i stripe je ze sobą, aby uzyskać połączone wyższy limit operacji We/Wy i przepływność. Łączny limit dla maszyny Wirtualnej powinien być większy niż połączonej limitów dysków w warstwie premium dołączonych. | &nbsp; | &nbsp; |
| **Rozmiar woluminu rozłożonego** |Mniejszy rozmiar woluminu rozłożonego dla losowych małych wzorzec operacji We/Wy w aplikacji OLTP. Na przykład użyć rozmiar woluminu rozłożonego 64 KB dla aplikacji OLTP programu SQL Server. |Większy rozmiar woluminu rozłożonego dla sekwencyjne dużych wzorzec operacji We/Wy w aplikacji w magazynie danych. Na przykład użyć 256 KB, rozmiar woluminu rozłożonego dla aplikacji do magazynu danych programu SQL Server. | &nbsp; |
| **Wielowątkowość** |Użycie wielowątkowość do większej liczby żądań do usługi Premium Storage będzie prowadzić do wyższej operacje We/Wy i przepływność. Na przykład w programie SQL Server należy ustawić o wysokiej wartości MAXDOP do przydzielenia większej liczby procesorów CPU do programu SQL Server. | &nbsp; | &nbsp; |
| **Queue Depth** |Głębokość kolejki większej daje wyższe operacje We/Wy. |Głębokość kolejki większej daje większą przepływność. |Głębokość kolejki mniejszych daje mniejsze opóźnienia. |

## <a name="nature-of-io-requests"></a>Rodzaj żądań We/Wy

Żądanie operacji We/Wy jest jednostką operacji wejścia/wyjścia, która będzie wykonywać aplikacji. Identyfikowanie charakter żądań We/Wy, losowych lub sekwencyjnych, odczytu lub zapisu, małych i dużych, pomoże określić wymagania dotyczące wydajności aplikacji. Należy zrozumieć charakter żądań We/Wy, aby podejmować właściwe decyzje podczas projektowania infrastruktury aplikacji.

Rozmiar we/wy jest jednym z bardziej ważne czynniki. Rozmiar operacji We/Wy jest rozmiar żądania operacji wejścia/wyjścia wygenerowane przez aplikację. Rozmiar operacji We/Wy ma znaczący wpływ na wydajność, zwłaszcza na operacje We/Wy i przepustowości, która aplikacja jest w stanie osiągnąć. Następująca formuła przedstawiono relacje między operacje We/Wy, rozmiar operacji We/Wy i przepustowość/przepływność.  
    ![](media/premium-storage-performance/image1.png)

Niektóre aplikacje umożliwiają można zmieniać ich rozmiar operacji We/Wy, podczas gdy niektóre aplikacje nie obsługują. Na przykład SQL Server określa optymalny rozmiar operacji We/Wy, sama i nie udostępniać użytkownikom żadnych pokrętła, aby ją zmienić. Z drugiej strony, firma Oracle oferuje parametr o nazwie [DB\_BLOCK\_rozmiar](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) za pomocą którego można skonfigurować rozmiar żądania operacji We/Wy bazy danych.

Jeśli używasz aplikacji, która nie zezwala na zmianę rozmiaru operacji We/Wy, zastosuj wskazówki w tym artykule w celu zoptymalizowania wydajności kluczowy wskaźnik wydajności, który jest najbardziej odpowiednie do Twojej aplikacji. Na przykład:

* Aplikacja OLTP generuje miliony losowa i małych żądań We/Wy. Aby obsłużyć tego rodzaju żądań We/Wy, należy zaprojektować swoją infrastrukturę aplikacji, aby uzyskać wyższy operacje We/Wy.  
* Aplikacja magazynowania danych generuje duże i kolejnych żądań We/Wy. Aby obsłużyć tego rodzaju żądań We/Wy, należy zaprojektować swoją infrastrukturę aplikacji, aby uzyskać wyższą przepustowość lub przepływności.

Jeśli używasz aplikacji, które umożliwia zmianę rozmiaru operacji We/Wy, na użytek tej ogólną regułę można przyjąć rozmiar operacji We/Wy, oprócz innych wytyczne dotyczące wydajności

* Mniejszy rozmiar operacji We/Wy, aby uzyskać wyższy operacje We/Wy. Na przykład 8 KB dla aplikacji OLTP.  
* Większy rozmiar operacji We/Wy, aby uzyskać większą przepustowość/przepływność. Na przykład 1024 KB dla aplikacji magazynu danych.

Poniżej przedstawiono przykład obliczenie operacje We/Wy i przepływność/przepustowość dla swojej aplikacji. Rozważmy aplikację przy użyciu dysku P30. Które operacje We/Wy i przepływność/przepustowości dysk P30, można osiągnąć maksymalną jest 5000 operacji We/Wy i 200 MB na sekundę odpowiednio. Teraz Jeśli aplikacja wymaga maksymalna operacje We/Wy dysku P30, a następnie użyć mniejszego rozmiaru operacji We/Wy, takich jak 8 KB, wynikowy przepustowości, będzie można uzyskać jest 40 MB na sekundę. Jednak jeśli aplikacja wymaga maksymalną przepływność/przepustowość między dysk P30, użycia, większego rozmiaru operacji We/Wy, takich jak 1024 KB IOPS wynikowy będzie mniej, 200 operacje We/Wy. W związku z tym Dostosowywanie rozmiaru operacji We/Wy w taki sposób, że spełnia on wymagania zarówno aplikacji operacje We/Wy i przepływność/przepustowość. Poniższa tabela zawiera podsumowanie różnych rozmiarów We/Wy i ich odpowiednie operacje We/Wy i przepływność na dysk P30.

| Wymagania aplikacji | Rozmiar operacji We/Wy | Operacje wejścia/wyjścia | Przepustowość/przepływność |
| --- | --- | --- | --- |
| Maks. IOPS |8 KB |5000 |40 MB na sekundę |
| Maksymalna przepływność |1024 KB |200 |200 MB na sekundę |
| Maksymalna przepływność + wysokiej operacje We/Wy |64 KB |3,200 |200 MB na sekundę |
| Maksymalna liczba operacji We/Wy i Wysoka przepływność |32 KB |5000 |160 MB na sekundę |

Aby uzyskać operacje We/Wy i przepustowości jest większa niż maksymalna wartość dysku magazynu premium w jednym, należy użyć wielu dysków w warstwie premium rozłożony jednocześnie. Na przykład stripe dwa dyski P30 Pobierz połączone operacje We/Wy z 10 000 operacji We/Wy lub łączna przepływność 400 MB na sekundę. Jak wyjaśniono w następnej sekcji, należy użyć rozmiaru maszyny Wirtualnej, który obsługuje połączonych operacji We/Wy i przepływność dysku.

> [!NOTE]
> Zwiększenie operacje We/Wy lub innych również zwiększa przepływność, upewnij się, że nie trafisz przepływności lub limity operacji We/Wy dysku lub maszyny Wirtualnej podczas zwiększenie jeden.

Aby Monitor wpływ rozmiar operacji We/Wy na wydajność aplikacji, można uruchomić narzędzi porównawczych na maszyna wirtualna i dyski. Tworzenie wielu przebiegów testowych i użyj innego rozmiaru operacji We/Wy dla każdego uruchomienia, aby zobaczyć wpływ. Zapoznaj się z sekcją Benchmarking na końcu tego artykułu, aby uzyskać więcej informacji.

## <a name="high-scale-vm-sizes"></a>Rozmiary maszyn wirtualnych w dużej skali

Podczas projektowania aplikacji, jedną z pierwszych czynności w celu jest, wybierz Maszynę wirtualną do hostowania aplikacji. Usługa Premium Storage jest dostarczany z rozmiarami wysokiej skali maszyn wirtualnych, które można uruchomić aplikacji wymagających większej mocy obliczeniowej i o wysokiej wydajności We/Wy dysku lokalnym. Te maszyny wirtualne zapewniają szybsze procesory, wyższy stopień pamięć rdzeń i Solid-State dyskach półprzewodnikowych (SSD) na dysku lokalnym. Przykłady wysokiej skali maszyny wirtualne obsługujące usługę Premium Storage to maszyny wirtualne serii DS, DSv2 i GS.

Wysokiej skali maszyn wirtualnych są dostępne w różnych rozmiarach z różną liczbę rdzeni procesora CPU, pamięci, systemu operacyjnego i rozmiar dysku tymczasowego. Rozmiar każdej maszyny Wirtualnej ma również maksymalna liczba dysków z danymi, które można dołączyć do maszyny Wirtualnej. W związku z tym wybrany rozmiar maszyny Wirtualnej będzie miało wpływ na ile przetwarzania, pamięci i pojemność magazynu jest dostępny dla twojej aplikacji. Wpływa to również na zasoby obliczeniowe i kosztów magazynowania. Na przykład poniżej przedstawiono specyfikacji największy rozmiar maszyny Wirtualnej serii DS, DSv2 serii i serii GS:

| Rozmiar maszyny wirtualnej | Rdzenie procesora CPU | Memory (Pamięć) | Rozmiary dysków maszyny Wirtualnej | Maksymalnie z Dyski z danymi | Rozmiar pamięci podręcznej | Operacje wejścia/wyjścia | Limity pamięci podręcznej operacji We/Wy przepustowości |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS14 |16 |112 GB |SYSTEM OPERACYJNY = 1023 GB <br> Lokalny dysk SSD 224 GB = |32 |576 GB |50 000 OPERACJI WE/WY <br> 512 MB na sekundę |4000 operacje We/Wy i 33 MB na sekundę |
| Standard_GS5 |32 |448 GB |SYSTEM OPERACYJNY = 1023 GB <br> Lokalny dysk SSD = 896 GB |64 |4224 GB |80 000 OPERACJI WE/WY <br> 2000 MB na sekundę |5000 operacji We/Wy i 50 MB na sekundę |

Aby wyświetlić pełną listę wszystkich dostępnych rozmiarów maszyn wirtualnych platformy Azure, zapoznaj się [rozmiarów maszyn wirtualnych Windows](../articles/virtual-machines/windows/sizes.md) lub [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md). Wybierz rozmiar maszyny Wirtualnej, która może spełniać i skalowanie do wymagań dotyczących wydajności żądaną aplikację. Oprócz tego weź pod uwagę następujące ważne kwestie przy wyborze rozmiarów maszyn wirtualnych.

*Limity skalowania*  
Maksymalny limit operacji We/Wy na maszynie Wirtualnej i na dysku są różne i niezależne od siebie nawzajem. Upewnij się, że aplikacji umożliwiające obsługę operacji We/Wy w ramach maszyny Wirtualnej, a także dołączone dyski w warstwie premium. W przeciwnym razie wydajność aplikacji, mogą mieć ograniczenia przepustowości.

Na przykład załóżmy, że to wymaganie dotyczące aplikacji jest maksymalnie 4000 operacje We/Wy. Aby to osiągnąć, możesz aprowizować dysk P30 na maszynie Wirtualnej DS1. Dysk P30 może dostarczać maksymalnie 5000 operacji We/Wy. Jednak DS1 maszyny Wirtualnej jest ograniczona do 3,200 operacje We/Wy. W związku z tym wydajność aplikacji będzie ograniczone przez limit maszyny Wirtualnej na 3,200 operacje We/Wy i będzie pogorszenie wydajności. Aby uniknąć tej sytuacji, wybierz rozmiar maszyny Wirtualnej i dysku zarówno będzie spełniać wymagania aplikacji.

*Koszt działania*  
W wielu przypadkach istnieje możliwość, że koszty ogólne operację przy użyciu usługi Premium Storage jest niższa niż przy użyciu magazynu w warstwie standardowa.

Na przykład rozważmy aplikację konieczności 16 000 operacji We/Wy. Aby uzyskać to wydajność, konieczne będzie standardowy\_D14 IaaS maszyny Wirtualnej platformy Azure, dające maksymalna liczba IOPS z 16 000 przy użyciu 32 dysków 1 TB magazynu w warstwie standardowa. Każdy dysk o rozmiarze 1 TB magazynu w warstwie standardowa można osiągnąć maksymalnie 500 operacji We/Wy. Szacowany koszt tej maszyny Wirtualnej na miesiąc będzie 1,570 $. Koszt miesięczny 32 dyski magazynu w warstwie standardowa będzie 1,638 $. Szacowany łączny koszt miesięczny będzie 3,208 $.

Jednak jeśli przechowywana jest ta sama aplikacja w magazynie Premium Storage, konieczne będzie mniejszego rozmiaru maszyny Wirtualnej i mniejszą liczbę dysków magazynu premium storage, co zmniejsza całkowity koszt. Standardowa\_DS13 maszyna wirtualna spełnia wymagania w zakresie 16 000 wymagane operacje We/Wy przy użyciu cztery dyski P30. Maszyna wirtualna DS13 ma maksymalną liczbę IOPS dla 25,600 i każdy dysk P30 ma maksymalną liczbę IOPS 5000. Ogólne, ta konfiguracja może osiągnąć 5000 x 4 = 20 000 operacji We/Wy. Szacowany koszt tej maszyny Wirtualnej na miesiąc będzie 1,003 $. Koszt miesięczny cztery dyski magazynu premium P30 będzie 544.34 $. Szacowany łączny koszt miesięczny będzie 1,544 $.

W poniższej tabeli przedstawiono podział kosztów tego scenariusza Standard i Premium Storage.

| &nbsp; | **Standardowa** | **Premium** |
| --- | --- | --- |
| **Koszt maszyn wirtualnych na miesiąc** |$1,570.58 (standardowa\_D14) |$1,003.66 (standardowa\_DS13) |
| **Koszt dysków na miesiąc** |$1,638.40 (dyski 32 x 1 TB) |$544.34 (4 dyski x P30) |
| **Całkowity koszt na miesiąc** |$3,208.98 |$1,544.34 |

*Dystrybucje systemu Linux*  

Azure Premium Storage umożliwia skorzystanie z takiego samego poziomu wydajności dla maszyn wirtualnych z systemem Windows i Linux. Firma Microsoft obsługuje wiele odmian dystrybucje systemu Linux i można zobaczyć pełną listę [tutaj](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy zauważyć, że różne dystrybucje są lepiej dopasowane do różnych rodzajów obciążeń. Zostaną wyświetlone różne poziomy wydajności w zależności od dystrybucji, w których jest uruchomione Twoje obciążenie na. Przetestuj dystrybucje systemu Linux za pomocą aplikacji i wybierz ten, który sprawdza się najlepiej.

Podczas uruchamiania systemu Linux dzięki usłudze Premium Storage, należy sprawdzić najnowsze aktualizacje dotyczące wymaganych sterowników, aby zapewnić wysoką wydajność.

## <a name="premium-storage-disk-sizes"></a>Rozmiary dysków magazynu Premium

Usługa Azure Premium Storage oferuje osiem rozmiary dysków GA i trzech rozmiarach dysków, które obecnie znajdują się w wersji zapoznawczej. Rozmiar każdego dysku ma limit skalowania różne operacje We/Wy, przepustowości i magazynu. Wybierz rozmiar dysku magazynu Premium, w zależności od wymagań aplikacji i dużej skali rozmiaru maszyny Wirtualnej po prawej stronie. W poniższej tabeli przedstawiono rozmiary dysków 11 i ich funkcji. Rozmiary P4, P6, P15 P60, P70 i P80 są obecnie obsługiwane tylko w przypadku dysków zarządzanych.

| Typ magazynu dysków Premium  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GiB | 64 GiB | 128 GiB| 256 GiB| 512 GB            | 1024 GiB (1 TiB)    | 2048 GiB (2 TiB)    | 4095 GiB (4 TiB)    | 8192 GiB (8 TiB)    | 16,384 GiB (16 TiB)    | 32 767 giB (32 TiB)    |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500              | 15 000              | 20,000              |
| Przepływność na dysk | 25 MiB na sekundę  | 50 MiB na sekundę  | MiB 100 na sekundę |125 MiB na sekundę | 150 MiB na sekundę | 200 MiB na sekundę | 250 MiB na sekundę | 250 MiB na sekundę | 480 MiB na sekundę | 750 MiB na sekundę | 750 MiB na sekundę |

Jak wiele dysków, możesz wybrać, zależy od dysku rozmiar wybrane. Można użyć pojedynczego dysku P50 lub wiele dysków P10, zgodnie z wymaganiami aplikacji. Należy uwzględnić wymienione poniżej, dokonując wyboru uwagi dotyczące konta.

*Limity skalowania (operacje We/Wy i przepływność)*  
Operacje We/Wy i przepływność limitów rozmiar każdego dysku Premium jest różne i niezależny z limity skalowania maszyny Wirtualnej. Upewnij się, że łączna liczba operacji We/Wy i przepływność z dysków w ramach limitów skalowania wybrany rozmiar maszyny Wirtualnej.

Na przykład, jeśli wymagania aplikacji jest maksymalnie 250 MB/s przepływności i DS4 maszyny Wirtualnej za pomocą jednego dysku P30. DS4 maszyny Wirtualnej można przydzielić maksymalnie 256 MB na sekundę przepływności. Jednak jeden dysk P30 ma limit przepustowości 200 MB/s. W związku z tym aplikacja będzie ograniczone na 200 MB/s, ze względu na limit na dysku. Aby wyeliminować ten limit, aprowizować więcej niż jedną dysków z danymi do maszyny Wirtualnej lub zmienianie rozmiaru dysków P40 lub P50.

> [!NOTE]
> Odczyty obsłużone przez pamięć podręczną nie są uwzględnione w operacji We/Wy dysku i przepływności, dlatego nie podlega limity dysku. Pamięć podręczna ma oddzielne limit operacji We/Wy i przepływność na maszynę Wirtualną.
>
> Na przykład początkowo swoje odczyty i zapisy są 60MB/s i 40MB/s. Wraz z upływem czasu pamięci podręcznej warms i obsługują coraz większym odczyty z pamięci podręcznej. Następnie zapisu wyższej przepływności można uzyskać z dysku.

*Liczba dysków*  
Określ liczbę dysków, które mają być przez ocenę wymagań aplikacji. Każdego rozmiaru maszyny Wirtualnej jest również objęta limitem liczby dysków, które można dołączyć do maszyny Wirtualnej. Zazwyczaj jest dwa razy liczba rdzeni. Upewnij się, że rozmiar maszyny Wirtualnej, którą wybierzesz może obsługiwać liczbę dysków potrzebne.

Należy pamiętać, że dyski magazynu Premium mają wyższe możliwości wydajność w porównaniu do dysków magazynu w warstwie standardowa. W związku z tym jeśli aplikacja są migrowane z maszyny Wirtualnej IaaS platformy Azure przy użyciu magazynu w warstwie standardowa do usługi Premium Storage, potrzebne będą prawdopodobnie mniejszą liczbę dysków w warstwie premium do osiągnięcia tej samej lub większej wydajności aplikacji.

## <a name="disk-caching"></a>Buforowanie dysku

Wysoka skalowania maszyn wirtualnych korzystających z usługi Azure Premium Storage ma wielowarstwowych technologii buforowania, o nazwie BlobCache. BlobCache używa kombinacji maszyny wirtualnej ilość pamięci RAM i lokalny dysk SSD do buforowania. Ta pamięć podręczna jest dostępna dla stałych dysków usługi Premium Storage i dysków lokalnych maszyn wirtualnych. Domyślnie to ustawienie pamięci podręcznej ma wartość do odczytu/zapisu dla dysków systemu operacyjnego i tylko do odczytu dla dysków z danymi w serwisie usługi Premium Storage. Za pomocą dysku pamięci podręcznej, włączone na dysków usługi Premium Storage, dużej skali maszyn wirtualnych można uzyskać bardzo wysoki poziom wydajności, które przekraczają podstawowej wydajności dysków.

> [!WARNING]
> Buforowanie dysku jest obsługiwana tylko dla rozmiarów dysków do 4 TiB.
> Zmienianie ustawień pamięci podręcznej dysku platformy Azure powoduje odłączenie i ponowne dołączenie dysku docelowego. Jeśli jest to dysk systemu operacyjnego, maszyna wirtualna zostanie ponownie uruchomiona. Zatrzymaj wszystkie aplikacje/usługi, które mogą mieć wpływ to zakłóceń, zanim zmienisz to ustawienie pamięci podręcznej dysku.

Aby dowiedzieć się więcej o tym, jak działa BlobCache, należy zapoznać się z wewnątrz [usługi Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) wpis w blogu.

Należy włączyć pamięć podręczną dla odpowiedniego zestawu dysków. Czy należy włączyć buforowanie dysku na dysku w warstwie premium lub nie będzie zależeć od wzorca obciążenia dysku będzie obsługiwał. W poniższej tabeli przedstawiono domyślne ustawienia pamięci podręcznej dla systemu operacyjnego i dysków z danymi.

| **Typ dysku** | **Domyślne ustawienie pamięci podręcznej** |
| --- | --- |
| Dysk systemu operacyjnego |Odczyt/zapis |
| Dysk z danymi |ReadOnly |

Poniżej przedstawiono ustawienia pamięci podręcznej dysków zalecany w przypadku dysków danych

| **Ustawienia buforowania dysku** | **Zalecenie dotyczące kiedy należy używać tego ustawienia** |
| --- | --- |
| Brak |Skonfiguruj pamięci podręcznej hosta jako Brak w przypadku dysków tylko do zapisu i procesów. |
| ReadOnly |Konfigurowanie pamięci podręcznej hosta jako tylko do odczytu dla dysków tylko do odczytu i odczytu i zapisu. |
| Odczyt/zapis |Konfigurowanie pamięci podręcznej hosta jako odczytu i zapisu, tylko wtedy, gdy aplikacja poprawnie obsługuje zapisywania danych w pamięci podręcznej dysków trwałych, w razie potrzeby. |

*ReadOnly*  
Konfigurując tylko do odczytu pamięci podręcznej danych usługi Premium Storage dysków, można osiągnąć małe opóźnienia odczytu i uzyskać bardzo duże operacje odczytu We/Wy i przepływność aplikacji. Jest to ze względu na dwóch przyczyn

1. Odczyty wykonywane z pamięci podręcznej, który znajduje się w pamięci maszyny Wirtualnej i lokalny dysk SSD, jest znacznie szybsze niż operacje odczytu z dysku danych, który znajduje się w usłudze Azure blob storage.  
1. Magazyn w warstwie Premium nie są liczone odczyty z pamięci podręcznej na dysku na SEKUNDĘ i przepływności. W związku z tym aplikacja jest możliwość osiągnięcia wyższej łączna liczba operacji We/Wy i przepływność.

*Odczytu i zapisu*  
Dyski systemu operacyjnego mają domyślnie włączone buforowanie odczytu i zapisu. Niedawno Dodaliśmy obsługę buforowania na danych, jak również dysków odczytu i zapisu. Jeśli używasz buforowanie ReadWrite musi mieć właściwy sposób, aby zapisać dane z pamięci podręcznej dysków trwałych. Na przykład SQL Server będzie obsługiwać zapisywanie danych w pamięci podręcznej dyski magazynu trwałego na swój własny. Przy użyciu pamięci podręcznej odczytu i zapisu z aplikacji, która nie obsługuje przechowywanie wymaganych danych może prowadzić do utraty danych, jeśli wystąpiła awaria maszyny Wirtualnej.

Na przykład można zastosować następujących wytycznych programu SQL Server uruchomionego na usługę Premium Storage, wykonując następujące polecenie,

1. Konfigurowanie pamięci podręcznej "ReadOnly" na zawierających pliki danych dysków magazynu premium storage.  
   a.  Szybkie odczytuje z pamięci podręcznej jest niższy, czas wykonywania zapytań programu SQL Server, ponieważ dane strony są pobierane szybciej z pamięci podręcznej porównaniu bezpośrednio z danych dysków.  
   b.  Obsługuje operacje odczytu z pamięci podręcznej, oznacza, że jest dostępna z dysków danych w warstwie premium dodatkowej przepływności. Używać tej dodatkowej przepływności do pobierania więcej stron danych i inne operacje, takie jak/przywracania kopii zapasowej programu SQL Server, usługi batch obciążeń i odbudowania indeksu.  
1. Konfigurowanie pamięci podręcznej "None" w przypadku dysków magazynu premium zawierających pliki dziennika.  
   a.  Pliki dziennika mają głównie operacje zapisu obciążenie. W związku z tym że nie korzystają z pamięci podręcznej tylko do odczytu.

### <a name="optimize-performance-on-linux-vms"></a>Optymalizuj wydajność na maszynach wirtualnych z systemem Linux

Dla wszystkich dysków SSD w warstwie premium lub ultra dysków z pamięcią podręczną równa **tylko do odczytu** lub **Brak**, należy wyłączyć "bariery", podczas instalacji systemu plików. Nie potrzebujesz barier w tym scenariuszu, ponieważ operacje zapisu do dysków magazynu premium storage są trwałe dla tych ustawień pamięci podręcznej Podczas żądania zapisu zakończy się pomyślnie, dane został zapisany w magazynie trwałym. Aby wyłączyć "bariery", użyj jednej z następujących metod. Wybierz jeden dla systemu plików:
  
* Aby uzyskać **reiserFS**, aby wyłączyć bariery, użyj `barrier=none` opcji instalacji. (Aby włączyć bariery, użyj `barrier=flush`.)
* Aby uzyskać **ext3/ext4**, aby wyłączyć bariery, użyj `barrier=0` opcji instalacji. (Aby włączyć bariery, użyj `barrier=1`.)
* Aby uzyskać **XFS**, aby wyłączyć bariery, użyj `nobarrier` opcji instalacji. (Aby włączyć bariery, użyj `barrier`.)
* Dla usługi premium storage dysków z pamięcią podręczną równa **ReadWrite**, Włącz barier w celu zapewnienia trwałości zapisu.
* Dla etykiety woluminu utrwalić po ponownym uruchomieniu maszyny Wirtualnej należy zaktualizować/etc/fstab z odwołaniami powszechnie Unikatowy identyfikator (UUID) na dyskach. Aby uzyskać więcej informacji, zobacz [Dodawanie dysku zarządzanego do maszyny Wirtualnej z systemem Linux](../articles/virtual-machines/linux/add-disk.md).

Poniższe dystrybucje systemu Linux zostały zweryfikowane dla dysków SSD w warstwie premium. Gwarantujące wysoką wydajność i stabilność przy użyciu dysków SSD w warstwie premium zalecamy uaktualnienie maszyn wirtualnych, aby te wersje lub nowsze. 

Najnowsze usługi LIS (Linux Integration), wersja 4.0, niektóre wersje wymagają dla platformy Azure. Aby pobrać i zainstalować dystrybucji, postępuj zgodnie z łączy wymienionych w poniższej tabeli. Obrazy możemy dodać do listy, podczas wykonywania sprawdzania poprawności. Nasze walidacji pokazują, że wydajność zmienia się dla każdego obrazu. Wydajność zależy od charakterystyki i ustawienia obrazu. Obrazy różnych dostosowanych do różnych rodzajów obciążeń.

| Dystrybucja | Wersja | Obsługiwane jądra | Szczegóły |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> SUSE — w systemie sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 wymagane](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Zobacz uwagi w następnej sekcji* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 zalecane](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Zobacz uwagi w następnej sekcji* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 lub RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 lub RHCK z[LIS 4.1 lub nowszym](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 lub RHCK z[LIS 4.1 lub nowszym](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |

## <a name="lis-drivers-for-openlogic-centos"></a>Sterowników LIS systemie OpenLogic CentOS

Jeśli korzystasz z maszyn wirtualnych z OpenLogic CentOS, uruchom następujące polecenie, aby zainstalować najnowsze sterowniki:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Aby aktywować nowe sterowniki, uruchom ponownie maszynę Wirtualną.

## <a name="disk-striping"></a>Rozkładanie

Przy dużej skali, które maszyna wirtualna jest połączona z kilku stałych dysków magazynu premium storage, dyski można paski ze sobą, aby agregować ich operacje We/Wy, przepustowości i pojemność magazynu.

W Windows umożliwia funkcji miejsca do magazynowania dysków stripe razem. Należy skonfigurować jedną kolumnę dla każdego dysku w puli. W przeciwnym razie może być krótszy niż oczekiwano z powodu nierówna Dystrybucja ruchu między dyskami ogólną wydajność woluminu rozłożonego.

Ważne: Za pomocą interfejsu użytkownika Menedżera serwera, można ustawić całkowita liczba kolumn maksymalnie 8 dla woluminu rozłożonego. Podczas podłączania więcej niż ośmiu dysków, należy utworzyć wolumin za pomocą programu PowerShell. Przy użyciu programu PowerShell, można ustawić liczbę kolumn równa liczbie dysków. Na przykład w przypadku 16 dysków w zestawie pojedynczej usługi stripe; Określ 16 kolumn w *NumberOfColumns* parametru *New-VirtualDisk* polecenia cmdlet programu PowerShell.

W systemie Linux za pomocą narzędzia MDADM na dyskach stripe razem. Aby uzyskać szczegółowe instrukcje dotyczące dysków Rozkładanie w systemie Linux, zobacz [konfigurowanie macierzy RAID oprogramowania w systemie Linux](../articles/virtual-machines/linux/configure-raid.md).

*Rozmiar woluminu rozłożonego*  
Ważne konfiguracji w rozkładanie jest rozmiar woluminu rozłożonego. Rozmiar woluminu rozłożonego lub rozmiar bloku jest najmniejsza fragmentów danych, które aplikacja może uwzględnić w woluminu rozłożonego. Rozmiar woluminu rozłożonego, które można skonfigurować, zależy od typu aplikacji i sposobu ich żądania. Jeśli rozmiar woluminu rozłożonego problem, może spowodować niespójność we/wy, co prowadzi do pogorszenie wydajności aplikacji.

Na przykład jeśli żądanie operacji We/Wy, generowanych przez aplikację jest większy niż rozmiar woluminu rozłożonego dysku, system magazynowania zapisuje go między granicami jednostki na więcej niż jeden dysk. Gdy nadejdzie czas na dostęp do tych danych, jej do wyszukania w więcej niż jednej jednostki usługi stripe, można wykonać żądania. Skumulowany efekt takie zachowanie może prowadzić do obniżenia wydajności istotne. Z drugiej strony Jeśli rozmiar żądania operacji We/Wy jest mniejszy niż rozmiar woluminu rozłożonego i jest charakter losowy, żądań We/Wy może dodawać na tym samym dysku, co powoduje "wąskie gardło" i ostatecznie zmniejszenie wydajności operacji We/Wy.

W zależności od typu obciążenia aplikacja jest uruchomiona, wybierz rozmiar woluminu rozłożonego odpowiednie. W przypadku małych losowych operacji We/Wy żądań należy użyć mniejszy rozmiar woluminu rozłożonego. Natomiast dużych sekwencyjnych operacji We/Wy żądań użytku większy rozmiar woluminu rozłożonego. Dowiedz się, stripe zaleceń dotyczących rozmiarów dla aplikacji, będzie działać w magazynie Premium Storage. W przypadku programu SQL Server należy skonfigurować rozmiar woluminu rozłożonego 64 KB w przypadku obciążeń OLTP i 256 KB dla obciążeń magazynowania danych. Zobacz [najlepsze rozwiązania dotyczące programu SQL Server na maszynach wirtualnych Azure wydajności](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) Aby dowiedzieć się więcej.

> [!NOTE]
> Można można ze sobą stripe maksymalnie 32 dyski magazynu premium na maszyny Wirtualnej serii DS i 64 dysków magazynu premium storage na dla maszyny Wirtualnej serii GS.

## <a name="multi-threading"></a>Wielowątkowości

Azure został zaprojektowany tak platformy usługi Premium Storage jako równoległe na wielką skalę. W związku z tym aplikacji wielowątkowych zapewnia znacznie wyższą wydajność niż aplikacja jednowątkowa. Aplikacji wielowątkowych rozróżniane jego zadań podrzędnych w wielu wątkach i zwiększa wydajność pracy przy użyciu zasobów maszyny Wirtualnej i dysku, do maksymalnej liczby.

Na przykład jeśli aplikacja jest uruchomiona na jeden rdzeń maszyny Wirtualnej przy użyciu dwa wątki, procesora CPU można przełączać się między dwoma wątkami w celu osiągnięcia wydajności. Gdy jeden wątek oczekuje na dysku we/wy, aby zakończyć, Procesor przełączyć się do innego wątku. W ten sposób dwoma wątkami można wykonać więcej niż jednego wątku będą. Jeśli maszyna wirtualna ma więcej niż jednego rdzenia, dodatkowo zmniejsza czas działania od każdego rdzenia równolegle, można uruchomić zadania.

Nie można zmienić sposób gotowych aplikacji implementuje pojedynczego wątku lub wielowątkowości. Na przykład SQL Server jest zdolne do obsługi wielu procesorów i wielordzeniowych. Jednak program SQL Server decyduje, pod jakimi warunkami będzie wykorzystywać jeden lub więcej wątków do przetworzenia zapytania. Można uruchomić zapytania i tworzenie indeksów przy użyciu wielu wątków. Program SQL Server dla zapytania, które polega na dołączenie do dużych tabel i sortowanie danych przed powrotem do użytkownika, prawdopodobnie będzie używany wielu wątków. Jednak użytkownik nie może kontrolować, czy program SQL Server wykonuje zapytania za pomocą pojedynczego wątku lub wielu wątków.

Istnieją ustawienia konfiguracji, które można zmienić w celu wywierania wpływu na to wielowątkowości lub równoległego przetwarzania aplikacji. Na przykład w przypadku programu SQL Server jest konfiguracji maksymalnego stopnia równoległości. To ustawienie o nazwie MAXDOP, można skonfigurować maksymalną liczbę procesorów, których można użyć programu SQL Server, w przypadku równoległego przetwarzania. MAXDOP można skonfigurować dla poszczególnych zapytań lub operacje indeksów. Jest to korzystne w przypadku, gdy chcesz równoważyć zasoby systemu dla wydajności aplikacji krytycznych.

Na przykład załóżmy, że swoją aplikację przy użyciu programu SQL Server jest wykonywany w dużych zapytań i operacji indeksowania, w tym samym czasie. Załóżmy, chce działać wydajniej w porównaniu do wykonywania zapytań w dużych operacja indeksu. W takim przypadku można ustawić wartość MAXDOP operacji indeksowania wyższa niż wartość MAXDOP dla zapytania. Dzięki temu program SQL Server ma więcej liczbę procesorów, których mogą być wykorzystywane dla operacji indeksowania w porównaniu do liczby procesorów, które można przypisać do wykonywania zapytań w dużych. Należy pamiętać, że nie mają kontroli nad liczbę wątków używanych programu SQL Server dla każdej operacji. Można kontrolować maksymalną liczbę procesorów, które są przeznaczone do wielowątkowości.

Dowiedz się więcej o [stopni równoległości](https://technet.microsoft.com/library/ms188611.aspx) w programie SQL Server. Dowiedz się, takie ustawienia, które wpływają na wielowątkowości w aplikacji i ich konfiguracji w celu zoptymalizowania wydajności.

## <a name="queue-depth"></a>Głębokość kolejki

Głębokość kolejki lub długość kolejki lub rozmiar kolejki jest liczba oczekujących żądań We/Wy w systemie. Wartość głębokości kolejki określa, ile operacji We/Wy aplikacji można wyrównać, który będzie przetwarzać dyski magazynu. Ma to wpływ na wszystkich trzech aplikacji wskaźniki wydajności, które omówiono w tym artykule tj, operacje We/Wy, przepustowości i opóźnień.

Kolejki głębi i wielowątkowości są ściśle powiązane. Głębokość kolejki wartość wskazuje, ile wielowątkowości mogą być osiągnięte przez aplikację. W przypadku dużych głębokości kolejki, aplikacja może wykonać więcej operacji współbieżnie, oznacza to, bardziej wielowątkowości. Jeśli głębokość kolejki jest mała, nawet jeśli aplikacja jest wielowątkowa, nie będzie wystarczającej liczby żądań wyrównana do wykonania.

Zazwyczaj wyłączone zarówno aplikacje nie umożliwiają zmianę głębokości kolejki, ponieważ jeśli ustawione niepoprawnie wykona szkód więcej niż dobra. Aplikacje będą wartość prawej głębokości kolejki, aby uzyskać optymalną wydajność. Jednak ważne jest zrozumienie tę koncepcję, dzięki czemu można rozwiązywać problemy z wydajnością za pomocą aplikacji. Można zaobserwować skutki głębokości kolejki, uruchamiając narzędzi porównawczych w Twoim systemie.

Niektóre aplikacje Podaj ustawienia w celu wywierania wpływu na głębokości kolejki. Na przykład ustawienia MAXDOP (maksymalny stopień równoległości) w programie SQL Server szczegółowo opisane w poprzedniej sekcji. MAXDOP to sposób wpływania na głębokości kolejki i wielowątkowości, chociaż nie bezpośrednio zmienić wartości głębokości kolejki programu SQL Server.

*Głębokość kolejki wysoka*  
Głębokość kolejki o wysokiej wyrównana więcej operacji na dysku. Dysk wie następnego żądania w kolejce, jej wcześniej. W związku z tym dysku można zaplanować operacji wcześniejsze i przetworzyć je w optymalny sekwencji. Ponieważ aplikacja wysyła więcej żądań na dysku, dysku może przetwarzać więcej równolegle z systemem IOs. Ostatecznie aplikacja będzie można osiągnąć wyższy operacje We/Wy. Ponieważ aplikacja jest przetwarzać więcej żądań, zwiększa się również łączna przepływność aplikacji

Zazwyczaj aplikacja może osiągnąć maksymalną przepływność z 8-16 oczekujących operacji We-Wy na dysku dołączonym. Jeśli głębokości kolejki, aplikacja nie jest wypychanie wystarczająco dużo dla systemu IOs do systemu, a przetworzy mniejszą ilość w danym okresie. Innymi słowy mniejszej przepustowości.

Na przykład w programie SQL Server, ustawienie wartości MAXDOP dla zapytania "4" informuje programu SQL Server, można użyć maksymalnie cztery rdzenie do wykonania zapytania. Program SQL Server określi, co to jest najlepszą wartością głębokość kolejki i liczby rdzeni w celu wykonywania zapytań.

*Optimal queue depth*  
Głębokość kolejki bardzo duże również ma swoje wady. Głębokość kolejki jest zbyt wysoka, podejmie próbę dysków bardzo duże operacje We/Wy aplikacji. Jeśli aplikacja nie ma dysków trwałych, za pomocą wystarczające aprowizowane operacje We/Wy, to negatywnie wpłynąć na opóźnień działania aplikacji. Następujące formuły przedstawiono relacje między operacje We/Wy, opóźnienia i głębokość kolejki.  
    ![](media/premium-storage-performance/image6.png)

Nie należy konfigurować głębokość kolejki o wysokiej wartości, ale optymalne wartość, która może dostarczać wystarczającej liczby operacji We/Wy dla aplikacji, bez wywierania wpływu na opóźnienia. Na przykład, jeśli opóźnienie aplikacji musi być 1 milisekundy, głębokości kolejki, wymagane do osiągnięcia 5000 operacji We/Wy jest, głębokość kolejki = 5000 x 0,001 = 5.

*Głębokość kolejki dla woluminu rozłożonego*  
Dla woluminu rozłożonego Obsługa głębokości kolejki wystarczająco wysoka, taki sposób, że każdego dysku, ma głębokość kolejki szczytu indywidualnie. Na przykład, rozważmy aplikację, która umieszcza głębokości kolejki wynoszącej 2 wiąże się z czterech dysków w stripe. Dwa żądania We/Wy zostanie umieszczona na dwóch dyskach, a pozostałe dwa dyski będą bezczynności. W związku z tym skonfiguruj głębokość kolejki w taki sposób, że wszystkie dyski mogą być zajęta. Formuła poniżej pokazano, jak ustalić głębokość kolejki woluminy rozłożone.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Ograniczanie przepływności

Zapisów systemu Azure Premium Storage określona liczba operacji We/Wy i Przepływność w zależności od rozmiarów maszyn wirtualnych i rozmiarach dysków, które wybierzesz. W dowolnym momencie Twoja aplikacja próbuje dysku na SEKUNDĘ lub przepływności powyżej tych limitów co maszyna wirtualna lub dysk może obsługiwać, będzie ograniczać usługi Premium Storage. To sytuacji, w postaci pogorszenie wydajności w aplikacji. Oznacza to, czas oczekiwania, przepływność niższe lub niższe operacje We/Wy. Jeśli Usługa Premium Storage nie ograniczać, aplikacji całkowicie może się nie powieść przekroczenia, co jej zasoby są w stanie osiągnięcia. Tak aby uniknąć problemów z wydajnością z powodu dławienia, zawsze aprowizować wystarczających zasobów dla aplikacji. Uwzględnia Omówiliśmy w sekcjach rozmiary dysków powyżej i rozmiarów maszyn wirtualnych. Analiza porównawcza to najlepszy sposób, aby zorientować się, jakie zasoby potrzebne do hostowania aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat typów dostępnego miejsca na dysku:

* [Wybierz typ dysku](../articles/virtual-machines/windows/disks-types.md)  

Dla użytkowników programu SQL Server zapoznaj się z artykułami na najlepsze rozwiązania w zakresie wydajności dla programu SQL Server:

* [Performance Best Practices for SQL Server na maszynach wirtualnych platformy Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Usługa Azure Premium Storage zapewnia najwyższą wydajność programu SQL Server na maszynie Wirtualnej platformy Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
