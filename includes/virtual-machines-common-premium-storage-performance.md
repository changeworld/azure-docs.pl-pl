---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88b19257a6a7d335e6a928a9eaf7526fbfd5b02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75942691"
---
## <a name="application-performance-indicators"></a>Wskaźniki wydajności aplikacji

Oceniamy, czy aplikacja działa dobrze, czy nie przy użyciu wskaźników wydajności, takich jak, jak szybko aplikacja przetwarza żądanie użytkownika, ile danych aplikacja przetwarza na żądanie, ile żądań jest przetwarzanie aplikacji w określonym czas, jak długo użytkownik musi czekać, aby uzyskać odpowiedź po przesłaniu żądania. Warunki techniczne dla tych wskaźników wydajności są, IOPS, przepływność lub przepustowość i opóźnienie.

W tej sekcji omówimy wspólne wskaźniki wydajności w kontekście magazynu w wersji Premium. W poniższej sekcji Wymagania dotyczące zbierania aplikacji dowiesz się, jak zmierzyć te wskaźniki wydajności dla aplikacji. W dalszej części optymalizacji wydajności aplikacji dowiesz się o czynnikach wpływających na te wskaźniki wydajności i zalecenia, aby je zoptymalizować.

## <a name="iops"></a>Liczba operacji we/wy na sekundę

Operacje We/Wy operacji wejścia/wyjścia na sekundę to liczba żądań wysyłanych przez aplikację na dyski magazynu w ciągu jednej sekundy. Operacja wejścia/wyjścia może być odczytana lub zapisująca, sekwencyjna lub losowa. Aplikacje przetwarzania transakcji online (OLTP), takie jak witryna internetowa sieci web, muszą natychmiast przetwarzać wiele równoczesnych żądań użytkowników. Żądania użytkowników są wstawiania i aktualizowania intensywnych transakcji bazy danych, które aplikacja musi przetwarzać szybko. W związku z tym aplikacje OLTP wymagają bardzo wysokich we/wy. Takie aplikacje obsługują miliony małych i losowych żądań we/wy. Jeśli masz taką aplikację, należy zaprojektować infrastrukturę aplikacji w celu optymalizacji dla usług We/Wy. W dalszej części *optymalizacja wydajności aplikacji,* omówimy szczegółowo wszystkie czynniki, które należy wziąć pod uwagę, aby uzyskać wysokie we/wy.

Po dołączeniu dysku magazynu premium do maszyny wirtualnej o dużej skali platforma Azure aprowizuje dla Ciebie gwarantowaną liczbę operacji we/wy zgodnie ze specyfikacją dysku. Na przykład dysk P50 aprowizuje 7500 IOPS. Każdy rozmiar maszyny wirtualnej o dużej skali ma również określony limit IOPS, jaki może utrzymać. Na przykład standardowa maszyna wirtualna GS5 ma limit 80 000 operacji We/Wy.

## <a name="throughput"></a>Przepływność

Przepływność lub przepustowość to ilość danych wysyłanych przez aplikację na dyski magazynu w określonym przedziale czasu. Jeśli aplikacja wykonuje operacje wejścia/wyjścia o dużych rozmiarach jednostek we/wy, wymaga wysokiej przepływności. Aplikacje magazynu danych mają tendencję do wykonywania intensywnych operacji skanowania, które uzyskują dostęp do dużych części danych w danym momencie i często wykonują operacje zbiorcze. Innymi słowy takie aplikacje wymagają wyższej przepływności. Jeśli masz taką aplikację, należy zaprojektować jego infrastruktury w celu optymalizacji pod kątem przepływności. W następnej sekcji szczegółowo omówimy czynniki, które należy dostroić, aby to osiągnąć.

Po dołączeniu dysku magazynu w wersji premium do maszyny Wirtualnej na dużą skalę platforma Azure zapewnia przepływność zgodnie z tą specyfikacją dysku. Na przykład dysk P50 aprowizuje przepływność dysku wynoszącą 250 MB na sekundę. Każdy rozmiar maszyny wirtualnej o dużej skali ma również określony limit przepływności, jaki może utrzymać. Na przykład maszyna wirtualna Standard GS5 ma maksymalną przepływność wynoszącą 2000 MB na sekundę.

Istnieje relacja między przepływnością a we/wy, jak pokazano w poniższej formule.

![Relacja we/wy i przepustowość](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

W związku z tym ważne jest, aby określić optymalną przepływność i wartości we/wy, które wymaga aplikacji. Jak spróbować zoptymalizować jeden, drugi również dostaje wpływ. W dalszej części *optymalizacja wydajności aplikacji*omówimy więcej szczegółów na temat optymalizacji usług We/Wy i przepływności.

## <a name="latency"></a>Opóźnienie

Opóźnienie to czas potrzebny aplikacji do odebrania pojedynczego żądania, wysłania go na dyski magazynu i wysłania odpowiedzi do klienta. Jest to krytyczna miara wydajności aplikacji oprócz usług We/Wy i przepływności. Opóźnienie dysku magazynu w wersji premium to czas potrzebny do pobrania informacji dla żądania i przekazania ich z powrotem do aplikacji. Magazyn w wersji Premium zapewnia stałe niskie opóźnienia. Dyski premium są przeznaczone do zapewnienia jednocyfrowych opóźnień milisekund dla większości operacji we/wy. Jeśli włączysz buforowanie hosta tylko do odczytu na dyskach magazynu w wersji premium, można uzyskać znacznie mniejsze opóźnienie odczytu. Omówimy buforowanie dysków bardziej szczegółowo w dalszej sekcji dotyczącej *optymalizacji wydajności aplikacji.*

Podczas optymalizacji aplikacji, aby uzyskać wyższe usługi We/Wy i przepływność, wpłynie to na opóźnienie aplikacji. Po dostrojeniu wydajności aplikacji, zawsze ocenić opóźnienie aplikacji, aby uniknąć nieoczekiwanego zachowania dużych opóźnień.

Następujące operacje płaszczyzny sterowania na dyskach zarządzanych mogą obejmować przenoszenie dysku z jednej lokalizacji magazynu do innej. Jest to organizowane za pomocą kopii danych w tle, która może potrwać kilka godzin, zazwyczaj mniej niż 24 godziny w zależności od ilości danych na dyskach. W tym czasie aplikacja może wystąpić wyższe niż zwykle opóźnienia odczytu, jak niektóre odczyty można uzyskać przekierowane do oryginalnej lokalizacji i może potrwać dłużej, aby zakończyć. Nie ma wpływu na opóźnienie zapisu w tym okresie.

- Zaktualizuj typ magazynu.
- Odłącz i podłącz dysk z jednej maszyny Wirtualnej do drugiej.
- Utwórz dysk zarządzany z dysku VHD.
- Utwórz dysk zarządzany z migawki.
- Konwertuj dyski niezarządzane na dyski zarządzane.

## <a name="performance-application-checklist-for-disks"></a>Lista kontrolna aplikacji wydajności dla dysków

Pierwszym krokiem w projektowaniu aplikacji o wysokiej wydajności działających w usłudze Azure Premium Storage jest zrozumienie wymagań dotyczących wydajności aplikacji. Po zebraniu wymagań dotyczących wydajności można zoptymalizować aplikację, aby osiągnąć najbardziej optymalną wydajność.

W poprzedniej sekcji wyjaśniliśmy typowe wskaźniki wydajności, we/wy, przepływność i opóźnienie. Należy określić, które z tych wskaźników wydajności są krytyczne dla aplikacji, aby zapewnić żądane środowisko użytkownika. Na przykład wysokie operacje We/Wy mają największe znaczenie dla aplikacji OLTP przetwarzając miliony transakcji w ciągu sekundy. Podczas gdy wysoka przepływność ma kluczowe znaczenie dla aplikacji magazynu danych przetwarzanych przez duże ilości danych w ciągu sekundy. Bardzo niskie opóźnienie ma kluczowe znaczenie dla aplikacji w czasie rzeczywistym, takich jak strony do strumieniowego przesyłania wideo na żywo.

Następnie zmierz maksymalne wymagania dotyczące wydajności aplikacji przez cały okres jej istnienia. Użyj przykładowej listy kontrolnej poniżej jako początek. Rejestrowanie maksymalnych wymagań dotyczących wydajności w okresach obciążenia normalnego, szczytowego i poza godzinami pracy. Identyfikując wymagania dla wszystkich poziomów obciążeń, można określić ogólne wymagania dotyczące wydajności aplikacji. Na przykład normalne obciążenie witryny e-commerce będzie transakcje, które służy przez większość dni w roku. Szczytowym obciążeniem strony internetowej będą transakcje, które obsługuje w okresie świątecznym lub specjalne wydarzenia sprzedażowe. Szczytowe obciążenie jest zazwyczaj doświadczenie przez ograniczony okres, ale może wymagać aplikacji do skalowania dwa lub więcej razy jego normalnej pracy. Dowiedz się, 50 percentyl, 90 percentyl, i 99 percentyl wymagania. Pomaga to odfiltrować wszelkie wartości odstające w wymaganiach dotyczących wydajności i można skupić swoje wysiłki na optymalizacji pod kątem właściwych wartości.

## <a name="application-performance-requirements-checklist"></a>Lista kontrolna wymagań dotyczących wydajności aplikacji

| **Wymagania dotyczące wydajności** | **50 percentyl** | **90 percentylów** | **99 percentyl** |
| --- | --- | --- | --- |
| Maksymalnie z Transakcje na sekundę | | | |
| % operacji odczytu | | | |
| % operacji zapisu | | | |
| % operacji losowych | | | |
| % operacji sekwencyjnych | | | |
| Rozmiar żądania we/wy | | | |
| Średnia przepustowość | | | |
| Maksymalnie z Przepływność | | | |
| Min. Opóźnienie | | | |
| Średnie opóźnienie | | | |
| Maksymalnie z Procesor CPU | | | |
| Średni procesor | | | |
| Maksymalnie z Memory (Pamięć) | | | |
| Średnia pamięć | | | |
| Głębokość kolejki | | | |

> [!NOTE]
> Należy rozważyć skalowanie tych liczb na podstawie oczekiwanego przyszłego wzrostu aplikacji. Dobrym pomysłem jest planowanie wzrostu z wyprzedzeniem, ponieważ może być trudniej zmienić infrastrukturę w celu poprawy wydajności później.

Jeśli masz istniejącą aplikację i chcesz przenieść do magazynu w wersji Premium, najpierw skompiluj powyższą listę kontrolną dla istniejącej aplikacji. Następnie skompiluj prototyp aplikacji w magazynie w wersji Premium i zaprojektuj aplikację na podstawie wytycznych opisanych w *optymalizacji wydajności aplikacji* w dalszej części tego dokumentu. W następnym artykule opisano narzędzia, których można używać do zbierania pomiarów wydajności.

### <a name="counters-to-measure-application-performance-requirements"></a>Liczniki do pomiaru wymagań dotyczących wydajności aplikacji

Najlepszym sposobem pomiaru wymagań dotyczących wydajności aplikacji jest użycie narzędzi do monitorowania wydajności dostarczanych przez system operacyjny serwera. Można użyć PerfMon dla Windows i iostat dla Linuksa. Narzędzia te przechwytują liczniki odpowiadające każdej miarie wyjaśnionej w powyższej sekcji. Należy przechwycić wartości tych liczników, gdy aplikacja jest uruchomiona jego normalnej, szczytowej i poza godzinami pracy obciążeń.

Liczniki PerfMon są dostępne dla procesora, pamięci i każdego dysku logicznego i fizycznego serwera. W przypadku korzystania z dysków magazynu w wersji premium z maszyną wirtualną liczniki dysków fizycznych są dla każdego dysku magazynu w wersji premium, a liczniki dysków logicznych są dla każdego woluminu utworzonego na dyskach magazynu w wersji premium. Należy przechwycić wartości dla dysków, które hostują obciążenie aplikacji. Jeśli istnieje mapowanie jeden do jednego między dyskami logicznymi i fizycznymi, można odwołać się do liczników dysków fizycznych; w przeciwnym razie odnoszą się do liczników dysku logicznego. W systemie Linux polecenie iostat generuje raport wykorzystania procesora i dysku. Raport wykorzystania dysku zawiera statystyki na urządzenie fizyczne lub partycję. Jeśli masz serwer bazy danych z jego danymi i loguje się na oddzielnych dyskach, zbierz te dane dla obu dysków. W poniższej tabeli opisano liczniki dysków, procesorów i pamięci:

| Licznik | Opis | Perfmon | Iostat |
| --- | --- | --- | --- |
| **Operacje We/Wy lub transakcje na sekundę** |Liczba żądań we/wy wystawionych na dysk magazynu na sekundę. |Odczyty dysku/s <br> Zapisy na dysku/s |Tps <br> r/s <br> w/s |
| **Odczyty i zapisy na dysku** |% operacji odczytu i zapisu wykonywanych na dysku. |% czasu odczytu dysku <br> % czasu zapisu dysku |r/s <br> w/s |
| **Przepływność** |Ilość danych odczytywanych z dysku lub zapisywanych na nim na sekundę. |Bajty odczytu dysku/s <br> Bajty zapisu dysku na sekundę |kB_read/s <br> kB_wrtn/s |
| **Opóźnienie** |Całkowity czas na wykonanie żądania we/wy dysku. |Średni dysk s/odczyt <br> Średni dysk s/Zapis |await <br> svctm ( svctm ) |
| **Rozmiar we/wy** |Rozmiar we/wy żąda problemów z dyskami magazynu. |Średni bajtów/odczytu dysku <br> Średni bajtów/zapisu dysku |avgrq-sz |
| **Głębokość kolejki** |Liczba zaległych żądań we/wy oczekujących na odczyt lub zapisanie na dysku magazynu. |Bieżąca długość kolejki dysku |śr. |
| **Max. Pamięci** |Ilość pamięci wymaganej do płynnego uruchamiania aplikacji |Zadeklarowane bajty w użyciu (%) |Użyj vmstat |
| **Max. Procesora** |Ilość procesora wymagana do płynnego uruchamiania aplikacji |% czasu procesora |%util |

Dowiedz się więcej o [iostat](https://linux.die.net/man/1/iostat) i [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Optymalizacja wydajności aplikacji

Głównymi czynnikami wpływającymi na wydajność aplikacji uruchomionej w magazynie w warstwie Premium są natura żądań we/wy, rozmiar maszyny Wirtualnej, rozmiar dysku, liczba dysków, buforowanie dysku, wielowątkowość i głębokość kolejki. Niektóre z tych czynników można kontrolować za pomocą pokręteł dostarczanych przez system. Większość aplikacji może nie dać możliwość bezpośredniego zmiany rozmiaru we/wy i głębokości kolejki. Na przykład jeśli używasz programu SQL Server, nie można wybrać rozmiar we/wy i głębokość kolejki. SQL Server wybiera optymalny rozmiar we/wy i wartości głębokości kolejki, aby uzyskać jak największą wydajność. Ważne jest, aby zrozumieć wpływ obu typów czynników na wydajność aplikacji, dzięki czemu można aprowizować odpowiednie zasoby, aby spełnić potrzeby w zakresie wydajności.

W tej sekcji zapoznaj się z listy kontrolnej wymagań aplikacji, które zostały utworzone, aby określić, ile trzeba zoptymalizować wydajność aplikacji. Na tej podstawie będziesz mógł określić, które czynniki z tej sekcji trzeba będzie dostroić. Aby zobaczyć wpływ każdego czynnika na wydajność aplikacji, uruchom narzędzia do analizy porównawczej w konfiguracji aplikacji. Zapoznaj się z artykułu Benchmarking, połączone na końcu, aby uzyskać kroki do uruchomienia wspólnych narzędzi porównawczych na maszynach wirtualnych z systemem Windows i Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optymalizuj we/wyrozumiali dla osób we/wyrozumiali i szybki wgląd

W poniższej tabeli podsumowano współczynniki wydajności i kroki niezbędne do optymalizacji we/wy, przepływności i opóźnienia. Sekcje następujące po tym podsumowaniu opisze każdy czynnik jest znacznie bardziej szczegółowe.

Aby uzyskać więcej informacji na temat rozmiarów maszyn wirtualnych i we/wy, przepływności i opóźnienia dostępnego dla każdego typu maszyny Wirtualnej, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/sizes.md) lub [rozmiary maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **Liczba operacji we/wy na sekundę** | **Przepływność** | **Opóźnienie** |
| --- | --- | --- | --- |
| **Przykładowy scenariusz** |Aplikacja ENTERPRISE OLTP wymagająca bardzo wysokich transakcji na sekundę. |Aplikacja do magazynowania danych przedsiębiorstwa przetwarza duże ilości danych. |Aplikacje w czasie zbliżonym do rzeczywistego wymagające natychmiastowej odpowiedzi na żądania użytkowników, takie jak gry online. |
| Czynniki wydajności | &nbsp; | &nbsp; | &nbsp; |
| **Rozmiar we/wy** |Mniejszy rozmiar we/wy daje wyższe we/wy. |Większy rozmiar we/wy, aby uzyskać wyższą przepływność. | &nbsp;|
| **Rozmiar maszyny Wirtualnej** |Użyj rozmiaru maszyny Wirtualnej, który oferuje usługi We/Wy większe niż wymagania aplikacji. |Użyj rozmiaru maszyny Wirtualnej z limitem przepływności większym niż wymagania aplikacji. |Użyj rozmiaru maszyny Wirtualnej, który oferuje limity skali większe niż wymagania aplikacji. |
| **Rozmiar dysku** |Użyj rozmiaru dysku, który oferuje usługi We/Wy większe niż wymagania aplikacji. |Użyj rozmiaru dysku z limitem przepływności większym niż wymaganie aplikacji. |Użyj rozmiaru dysku, który oferuje limity skali większe niż wymagania aplikacji. |
| **Limity skalowania maszyny Wirtualnej i dysku** |Limit we/wy wystąpienia wybranego rozmiaru maszyny Wirtualnej powinien być większy niż całkowita liczba we/wy sterowana przez podłączone do niego dyski magazynu. |Limit przepływności wybranego rozmiaru maszyny Wirtualnej powinien być większy niż całkowita przepływność napędzana przez dyski magazynu w jakości premium dołączone do niego. |Limity skali wybranego rozmiaru maszyny Wirtualnej muszą być większe niż całkowite limity skali dołączonych dysków magazynu w wersji premium. |
| **Buforowanie dysków** |Włącz pamięć podręczną tylko do odczytu na dyskach magazynu w warstwie Premium za pomocą operacji odczytu ciężkich, aby uzyskać wyższe operacje We/Wy odczytu. | &nbsp; |Włącz pamięć podręczną tylko do odczytu na dyskach magazynu w warstwie Premium z operacjami ciężkimi Ready, aby uzyskać bardzo niskie opóźnienia odczytu. |
| **Rozkładanie dysków** |Użyj wielu dysków i rozbieraj je razem, aby uzyskać połączony wyższy limit we/wy i przepływność. Łączny limit na maszynę wirtualną powinien być wyższy niż łączne limity dołączonych dysków premium. | &nbsp; | &nbsp; |
| **Rozmiar woluminu rozłożonego** |Mniejszy rozmiar paska dla losowego małego wzorca we/wy widocznego w aplikacjach OLTP. Na przykład użyj rozmiaru paska 64 KB dla aplikacji SQL Server OLTP. |Większy rozmiar paska dla sekwencyjnego dużego wzorca we/wy widocznego w aplikacjach usługi Data Warehouse. Na przykład użyj rozmiaru paska 256 KB dla aplikacji magazynu danych programu SQL Server. | &nbsp; |
| **Wielowątkowość** |Użyj wielowątkowości do wypychania większej liczby żądań do magazynu w wersji Premium, co doprowadzi do większej liczby we/wyrzucić i przepływność. Na przykład na sql server ustawić wysoką wartość MAXDOP przydzielić więcej procesorów do programu SQL Server. | &nbsp; | &nbsp; |
| **Głębokość kolejki** |Większa głębokość kolejki daje wyższe we/wy. |Większa głębokość kolejki daje wyższą przepustowość. |Mniejsza głębokość kolejki daje mniejsze opóźnienia. |

## <a name="nature-of-io-requests"></a>Charakter wniosków we/wy

Żądanie we/wy jest jednostką operacji wejścia/wyjścia, którą będzie wykonywać aplikacja. Identyfikowanie charakteru żądań we/wy, losowych lub sekwencyjnych, odczytu lub zapisu, małych lub dużych, pomoże określić wymagania dotyczące wydajności aplikacji. Ważne jest, aby zrozumieć charakter żądań we/wy, aby podejmować właściwe decyzje podczas projektowania infrastruktury aplikacji. IOs muszą być rozłożone równomiernie, aby osiągnąć najlepszą możliwą wydajność.

Rozmiar we/wy jest jednym z ważniejszych czynników. Rozmiar we/wy to rozmiar żądania operacji wejścia/wyjścia wygenerowanego przez aplikację. Rozmiar we/wy ma znaczący wpływ na wydajność, szczególnie na we/wy i przepustowość, które aplikacja jest w stanie osiągnąć. Poniższa formuła przedstawia relację między we/wy, rozmiar we/wy i przepustowość/przepustowość.  
    ![](media/premium-storage-performance/image1.png)

Niektóre aplikacje umożliwiają zmianę ich rozmiaru we/wy, podczas gdy niektóre aplikacje nie. Na przykład SQL Server określa optymalny rozmiar we/wy sam i nie zapewnia użytkownikom żadnych pokręteł, aby go zmienić. Z drugiej strony Oracle udostępnia parametr o nazwie [DB\_\_BLOCK SIZE,](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) za pomocą którego można skonfigurować rozmiar żądania we/wy bazy danych.

Jeśli używasz aplikacji, która nie pozwala na zmianę rozmiaru we/wy, użyj wskazówek w tym artykule, aby zoptymalizować wydajność kpi, który jest najbardziej odpowiedni dla aplikacji. Na przykład:

* Aplikacja OLTP generuje miliony małych i losowych żądań we/wy. Aby obsłużyć tego typu żądania we/wy, należy zaprojektować infrastrukturę aplikacji, aby uzyskać wyższe usługi We/Wy.  
* Aplikacja do magazynowania danych generuje duże i sekwencyjne żądania we/wy. Aby obsłużyć tego typu żądania we/wy, należy zaprojektować infrastrukturę aplikacji, aby uzyskać wyższą przepustowość lub przepływność.

Jeśli używasz aplikacji, która pozwala na zmianę rozmiaru we/wy, użyj tej reguły dla rozmiaru we/wy oprócz innych wytycznych dotyczących wydajności,

* Mniejszy rozmiar we/wy, aby uzyskać wyższe we/wy. Na przykład 8 KB dla aplikacji OLTP.  
* Większy rozmiar we/wy, aby uzyskać wyższą przepustowość/przepustowość. Na przykład 1024 KB dla aplikacji magazynu danych.

Oto przykład sposobu obliczania operacji We/Wy i przepustowości/przepustowości dla aplikacji. Należy wziąć pod uwagę aplikację przy użyciu dysku P30. Maksymalna liczba we/wy i przepustowość/przepustowość, które może osiągnąć dysk P30, wynosi odpowiednio 5000 IOPS i 200 MB na sekundę. Teraz, jeśli aplikacja wymaga maksymalnej usługi We/Wy z dysku P30 i używasz mniejszego rozmiaru we/wy, takiego jak 8 KB, wynikowa przepustowość, którą będzie można uzyskać, wynosi 40 MB na sekundę. Jeśli jednak aplikacja wymaga maksymalnej przepływności/przepustowości z dysku P30 i użyjesz większego rozmiaru we/wy, takiego jak 1024 KB, wynikowe usługi We/Wy będą mniejsze, 200 we/wy. W związku z tym dostroić rozmiar we/wy w taki sposób, aby spełniał wymagania dotyczące we/opy aplikacji i przepustowości/przepustowości. W poniższej tabeli podsumowano różne rozmiary we/wy oraz odpowiadające im we/wy i przepływność dla dysku P30.

| Wymaganie zastosowania | Rozmiar we/wy | Liczba operacji we/wy na sekundę | Przepustowość/przepustowość |
| --- | --- | --- | --- |
| Maks. liczba operacji we/wy na sekundę |8 KB |5000 |40 MB na sekundę |
| Maksymalna przepustowość |1024 KB |200 |200 MB na sekundę |
| Maksymalna przepustowość + wysoka liczba IOPS |64 KB |3,200 |200 MB na sekundę |
| Maksymalna liczba IOPS + wysoka przepustowość |32 KB |5000 |160 MB na sekundę |

Aby uzyskać usługi We/Wy i przepustowość wyższą niż maksymalna wartość pojedynczego dysku magazynu w wersji premium, należy użyć wielu dysków premium rozłożonych razem. Na przykład rozbieraj dwa dyski P30, aby uzyskać połączone we/wy operacji We/Wy 10 000 operacji We/Wy lub połączonej przepustowości 400 MB na sekundę. Jak wyjaśniono w następnej sekcji, należy użyć rozmiaru maszyny Wirtualnej, który obsługuje połączone we/wy i przepływność dysku.

> [!NOTE]
> W miarę zwiększania we/wy albo przepływność inne również zwiększa, upewnij się, że nie trafisz przepływności lub limitów We/Wy na dysku lub maszyny Wirtualnej podczas zwiększania jednego.

Aby zobaczyć wpływ rozmiaru we/wy na wydajność aplikacji, można uruchomić narzędzia do analizy porównawczej na maszynie Wirtualnej i dyskach. Utwórz wiele przebiegów testowych i użyj innego rozmiaru we/wy dla każdego uruchomienia, aby zobaczyć wpływ. Więcej informacji można znaleźć w artykule benchmarking, połączonym na końcu.

## <a name="high-scale-vm-sizes"></a>Duże rozmiary maszyn wirtualnych

Po rozpoczęciu projektowania aplikacji jedną z pierwszych rzeczy do zrobienia jest wybierz maszynę wirtualną do hostowania aplikacji. Magazyn w wersji Premium jest wyposażony w wysokiej wielkości maszyny wirtualne, które mogą uruchamiać aplikacje wymagające wyższej mocy obliczeniowej i wysokiej wydajności we/wy dysku lokalnego. Te maszyny wirtualne zapewniają szybsze procesory, wyższy stosunek pamięci do rdzenia i dysk SSD (SSD) dla dysku lokalnego. Przykładami maszyn wirtualnych o dużej skali obsługujących magazyn w wersji Premium są maszyny wirtualne z serii DS i GS.

Maszyny wirtualne o dużej skali są dostępne w różnych rozmiarach z różną liczbą rdzeni procesora, pamięcią, systemem operacyjnym i tymczasowym rozmiarem dysku. Każdy rozmiar maszyny Wirtualnej ma również maksymalną liczbę dysków danych, które można dołączyć do maszyny Wirtualnej. W związku z tym wybrany rozmiar maszyny Wirtualnej wpłynie na to, ile przetwarzania, pamięci i pojemności magazynu jest dostępny dla aplikacji. Wpływa również na koszt obliczeń i magazynu. Na przykład poniżej znajdują się specyfikacje największego rozmiaru maszyny Wirtualnej w serii DS i serii GS:

| Rozmiar maszyny wirtualnej | Rdzenie procesora CPU | Memory (Pamięć) | Rozmiary dysków maszyn wirtualnych | Maksymalnie z dyski danych | Rozmiar pamięci podręcznej | Liczba operacji we/wy na sekundę | Limity we/wy pamięci podręcznej przepustowości |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS14 |16 |112 GB |System operacyjny = 1023 GB <br> Lokalny ssd = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB na sekundę |4000 IOPS i 33 MB na sekundę |
| Standardowa_GS5 |32 |448 GB |System operacyjny = 1023 GB <br> Lokalny ssd = 896 GB |64 |4224 GB |80 000 IOPS <br> 2000 MB na sekundę |5000 IOPS i 50 MB na sekundę |

Aby wyświetlić pełną listę wszystkich dostępnych rozmiarów maszyn wirtualnych platformy Azure, zapoznaj się z [rozmiarami maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/sizes.md) lub [rozmiarami maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/sizes.md). Wybierz rozmiar maszyny Wirtualnej, który może spełniać i skalować do żądanych wymagań dotyczących wydajności aplikacji. Oprócz tego należy wziąć pod uwagę następujące ważne kwestie przy wyborze rozmiarów maszyn wirtualnych.

*Limity skali*  
Maksymalne limity we/wy na maszynę wirtualną i na dysku są różne i niezależne od siebie. Upewnij się, że aplikacja jest prowadzenie Usługi We/Wy w granicach maszyny Wirtualnej, jak również dysków w wersji premium dołączonych do niego. W przeciwnym razie wydajność aplikacji będzie występowanie ograniczania przepustowości.

Na przykład załóżmy, że wymaganie aplikacji jest maksymalnie 4000 Operacji We/Wy. Aby to osiągnąć, można aprowizować dysk P30 na maszynie wirtualnej DS1. Dysk P30 może dostarczyć do 5000 we/wy. Jednak maszyna wirtualna DS1 jest ograniczona do 3200 IOPS. W związku z tym wydajność aplikacji będzie ograniczona przez limit maszyny Wirtualnej na 3200 We/Wy i będzie obniżona wydajność. Aby zapobiec tej sytuacji, wybierz rozmiar maszyny Wirtualnej i dysku, które będą spełniać wymagania aplikacji.

*Koszt eksploatacji*  
W wielu przypadkach jest możliwe, że ogólny koszt operacji przy użyciu magazynu w wersji Premium jest niższa niż przy użyciu magazynu standardowego.

Rozważmy na przykład aplikację wymagającą 16 000 operacji We/Wy. Aby osiągnąć tę wydajność, trzeba\_będzie standard D14 Azure IaaS VM, który może dać maksymalną liczbę we/wy 16.000 przy użyciu 32 dysków magazynu standardowego 1 TB. Każdy standardowy dysk pamięci masowej o pojemności 1 TB może osiągnąć maksymalnie 500 we/wy. Szacowany koszt tej maszyny Wirtualnej miesięcznie wyniesie $1,570. Miesięczny koszt 32 standardowych dysków pamięci masowej wyniesie $1,638. Szacowany całkowity miesięczny koszt wyniesie $3,208.

Jeśli jednak hostowano tę samą aplikację w magazynie w wersji Premium, będziesz potrzebować mniejszego rozmiaru maszyny Wirtualnej i mniejszej liczby dysków magazynu w wersji premium, zmniejszając w ten sposób całkowity koszt. Standardowa\_maszyna wirtualna DS13 może spełnić wymagania 16 000 we/wy dotyczące usług We/Wy przy użyciu czterech dysków P30. Maszyna wirtualna DS13 ma maksymalną liczbę we/wy 25 600, a każdy dysk P30 ma maksymalną liczbę we/wy 5000 we/wy. Ogólnie rzecz biorąc, ta konfiguracja może osiągnąć 5000 x 4 = 20 000 we/wy. Szacowany koszt tej maszyny Wirtualnej miesięcznie wyniesie $1,003. Miesięczny koszt czterech dysków pamięci masowej premium P30 wyniesie $544.34. Szacowany całkowity miesięczny koszt wyniesie $1,544.

W poniższej tabeli podsumowano podział kosztów w tym scenariuszu dla magazynu standardowego i magazynu w warstwie Premium.

| &nbsp; | **Standardowa** | **Premium** |
| --- | --- | --- |
| **Koszt maszyny Wirtualnej na miesiąc** |$1,570.58 (Standard\_D14) |$1,003.66 (Standard\_DS13) |
| **Koszt dysków miesięcznie** |$1,638.40 (dyski 32 x 1 TB) |$544.34 (4 dyski P30) |
| **Całkowity koszt miesięczny** |zł. |1 544,34 usd |

*Linuksa*  

Dzięki usłudze Azure Premium Storage uzyskasz ten sam poziom wydajności dla maszyn wirtualnych z systemem Windows i Linux. Obsługujemy wiele smaków dystrybucji Linuksa, a pełną listę można zobaczyć [tutaj](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy pamiętać, że różne dystrybucje są lepiej dostosowane do różnych typów obciążeń. Zobaczysz różne poziomy wydajności w zależności od dystrybucji, na której działa obciążenie. Przetestuj dystrybucje Linuksa za pomocą aplikacji i wybierz tę, która działa najlepiej.

Podczas uruchamiania systemu Linux z magazynem w wersji Premium sprawdź najnowsze aktualizacje dotyczące wymaganych sterowników, aby zapewnić wysoką wydajność.

## <a name="premium-storage-disk-sizes"></a>Rozmiary dysków magazynu w wersji premium

Usługa Azure Premium Storage oferuje różne rozmiary, dzięki czemu można wybrać taki, który najlepiej odpowiada Twoim potrzebom. Każdy rozmiar dysku ma inny limit skali dla usług We/Wy, przepustowości i magazynu. Wybierz odpowiedni rozmiar dysku magazynu w wersji Premium w zależności od wymagań aplikacji i rozmiaru maszyny wirtualnej na dużą skalę. W poniższej tabeli przedstawiono rozmiary dysków i ich możliwości. Rozmiary P4, P6, P15, P60, P70 i P80 są obecnie obsługiwane tylko dla dysków zarządzanych.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Liczba wybranych dysków zależy od wybranego rozmiaru dysku. Można użyć jednego dysku P50 lub wielu dysków P10, aby spełnić wymagania aplikacji. Przy dokonywaniu wyboru należy wziąć pod uwagę kwestie wymienione poniżej.

*Limity skali (We/Wy i przepływność)*  
Limity we/wy i przepływności każdego rozmiaru dysku w wersji Premium są różne i niezależne od limitów skali maszyny Wirtualnej. Upewnij się, że całkowita liczba we/wy i przepływność z dysków mieści się w granicach skali wybranego rozmiaru maszyny Wirtualnej.

Na przykład jeśli wymaganie aplikacji jest maksymalnie 250 MB/s przepływności i używasz DS4 VM z jednym dyskiem P30. Maszyna wirtualna DS4 może zapewnić przepustowość do 256 MB/s. Jednak pojedynczy dysk P30 ma limit przepływności 200 MB/s. W związku z tym aplikacja będzie ograniczona do 200 MB/s ze względu na limit dysku. Aby przezwyciężyć ten limit, aprowizować więcej niż jeden dysk danych do maszyny Wirtualnej lub zmienić rozmiar dysków do P40 lub P50.

> [!NOTE]
> Odczyty obsługiwane przez pamięć podręczną nie są uwzględniane w iOPS dysku i przepływności, a zatem nie podlegają limitom dysku. Pamięć podręczna ma swój oddzielny limit we/wy i przepływność na maszynę wirtualną.
>
> Na przykład początkowo odczyty i zapisy są odpowiednio 60MB/s i 40MB/s. Z biegiem czasu pamięć podręczna nagrzewa się i obsługuje coraz więcej odczytów z pamięci podręcznej. Następnie można uzyskać wyższą przepływność zapisu z dysku.

*Liczba dysków*  
Określ liczbę dysków, które będą potrzebne, oceniając wymagania aplikacji. Każdy rozmiar maszyny Wirtualnej ma również limit liczby dysków, które można dołączyć do maszyny Wirtualnej. Zazwyczaj jest to dwa razy większa liczba rdzeni. Upewnij się, że rozmiar maszyny Wirtualnej, który wybierzesz może obsługiwać liczbę potrzebnych dysków.

Pamiętaj, że dyski magazynu w wersji premium mają wyższe możliwości wydajności w porównaniu do dysków magazynu standardowego. W związku z tym jeśli migrujesz aplikację z maszyny wirtualnej usługi Azure IaaS przy użyciu magazynu standardowego do magazynu w wersji Premium, prawdopodobnie będzie potrzebna mniejsza liczba dysków w wersji premium, aby osiągnąć taką samą lub wyższą wydajność dla aplikacji.

## <a name="disk-caching"></a>Buforowanie dysku

Maszyny wirtualne o dużej skali, które wykorzystują usługę Azure Premium Storage, mają wielowarstwową technologię buforowania o nazwie BlobCache. Usługa BlobCache używa kombinacji pamięci RAM maszyny wirtualnej i lokalnego identyfikatora SSD do buforowania. Ta pamięć podręczna jest dostępna dla dysków trwałych magazynu w warstwie Premium i dysków lokalnych maszyn wirtualnych. Domyślnie to ustawienie pamięci podręcznej jest ustawione na Odczyt/Zapis dla dysków systemu operacyjnego i Tylko do odczytu dla dysków danych hostowanych w magazynie w warstwie Premium. Po włączeniu buforowania dysków na dyskach magazynu w wersji Premium maszyny wirtualne o wysokiej skali mogą osiągnąć bardzo wysoki poziom wydajności, który przekracza podstawową wydajność dysku.

> [!WARNING]
> Buforowanie dysków nie jest obsługiwane dla dysków 4 TiB i większych. Jeśli wiele dysków są podłączone do maszyny Wirtualnej, każdy dysk, który jest mniejszy niż 4 TiB będzie obsługiwać buforowania.
>
> Zmienianie ustawień pamięci podręcznej dysku platformy Azure powoduje odłączenie i ponowne dołączenie dysku docelowego. Jeśli jest to dysk systemu operacyjnego, maszyna wirtualna jest ponownie uruchamiana. Zatrzymasz wszystkie aplikacje/usługi, na które może mieć wpływ to zakłócenie, przed zmianą ustawień pamięci podręcznej dysku.

Aby dowiedzieć się więcej o tym, jak działa usługa BlobCache, zapoznaj się z wpisem w blogu [usługi Inside Azure Premium Storage.](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)

Ważne jest, aby włączyć pamięć podręczną na prawym zestawie dysków. To, czy należy włączyć buforowanie dysku na dysku w wersji premium, czy nie, będzie zależało od wzorca obciążenia, który będzie obsługiwał dysk. W poniższej tabeli przedstawiono domyślne ustawienia pamięci podręcznej dla dysków systemu operacyjnego i danych.

| **Typ dysku** | **Domyślne ustawienie pamięci podręcznej** |
| --- | --- |
| Dysk systemu operacyjnego |Odczyt/zapis |
| Dysk z danymi |ReadOnly |

Poniżej przedstawiono zalecane ustawienia pamięci podręcznej dysków danych,

| **Ustawienie buforowania dysków** | **zalecenia dotyczące tego, kiedy stosować to ustawienie** |
| --- | --- |
| Brak |Skonfiguruj pamięć podręczną hosta jako Brak dla dysków tylko do zapisu i zapisu. |
| ReadOnly |Skonfiguruj pamięć podręczną hosta jako tylko do odczytu dla dysków tylko do odczytu i odczytu i zapisu. |
| Odczyt/zapis |Skonfiguruj pamięć podręczną hosta jako ReadWrite tylko wtedy, gdy aplikacja prawidłowo obsługuje zapisywanie danych w pamięci podręcznej na dyskach trwałych w razie potrzeby. |

*Readonly*  
Konfigurując buforowanie tylko do odczytu na dyskach danych magazynu w jakości Premium, można osiągnąć małe opóźnienie odczytu i uzyskać bardzo wysokie odczytywanie we/wy i przepływność dla aplikacji. Wynika to z dwóch powodów,

1. Odczyty wykonywane z pamięci podręcznej, która znajduje się w pamięci maszyny Wirtualnej i lokalnym dysku SSD, są znacznie szybsze niż odczyty z dysku danych, który znajduje się w magazynie obiektów blob platformy Azure.  
1. Magazyn w warstwie Premium nie zlicza odczytów obsługiwanych z pamięci podręcznej, w kierunku we/wy i przepływności dysku. W związku z tym aplikacja jest w stanie osiągnąć wyższą całkowitą liczbę we/wy i przepływność.

*Readwrite*  
Domyślnie dyski systemu operacyjnego mają włączoną buforowanie ReadWrite. Niedawno dodaliśmy obsługę buforowania ReadWrite na dyskach danych. Jeśli używasz buforowania ReadWrite, musisz mieć prawidłowy sposób zapisu danych z pamięci podręcznej na dyskach trwałych. Na przykład SQL Server obsługuje zapisywanie danych w pamięci podręcznej na dyskach magazynu trwałego na własną rękę. Za pomocą ReadWrite pamięci podręcznej z aplikacją, która nie obsługuje utrwalania wymaganych danych może prowadzić do utraty danych, jeśli maszyna wirtualna ulegnie awarii.

*Brak*  
Obecnie **none** jest obsługiwany tylko na dyskach z danymi. Nie jest obsługiwany na dyskach systemu operacyjnego. Jeśli na dysku systemu operacyjnego ustawisz **opcję Brak,** zastąpi to wewnętrznie i ustawi ją na **ReadOnly**.

Na przykład można zastosować te wskazówki do programu SQL Server uruchomionego w magazynie w wersji Premium, wykonując następujące czynności:

1. Skonfiguruj pamięć podręczną "ReadOnly" na dyskach magazynu premium obsługujących pliki danych.  
   a.  Szybkie odczyty z pamięci podręcznej obniżają czas kwerendy programu SQL Server, ponieważ strony danych są pobierane znacznie szybciej z pamięci podręcznej w porównaniu do bezpośrednio z dysków z danymi.  
   b.  Obsługa odczytów z pamięci podręcznej oznacza, że istnieje dodatkowa przepływność dostępna z dysków danych w warstwie Premium. SQL Server można użyć tej dodatkowej przepływności do pobierania większej liczby stron danych i innych operacji, takich jak kopia zapasowa/przywracanie, obciążenia wsadowe i przebudowy indeksu.  
1. Skonfiguruj pamięć podręczną "Brak" na dyskach magazynu premium obsługujących pliki dziennika.  
   a.  Pliki dziennika mają przede wszystkim operacje dużej pracy związane z zapisem. W związku z tym nie korzystają z pamięci podręcznej ReadOnly.

## <a name="optimize-performance-on-linux-vms"></a>Optymalizacja wydajności na maszynach wirtualnych z systemem Linux

W przypadku wszystkich dysków SSD premium lub dysków ultra z pamięcią podręczną ustawioną na **ReadOnly** lub **None**należy wyłączyć "bariery" podczas montażu systemu plików. Nie potrzebujesz barier w tym scenariuszu, ponieważ zapisy na dyskach magazynu w warstwie Premium są trwałe dla tych ustawień pamięci podręcznej. Po pomyślnym zakończeniu żądania zapisu dane zostały zapisane w magazynie trwałym. Aby wyłączyć "bariery", użyj jednej z następujących metod. Wybierz ten dla swojego systemu plików:
  
* W przypadku **reiserFS**, aby `barrier=none` wyłączyć bariery, użyj opcji montowania. (Aby włączyć bariery, `barrier=flush`użyj .)
* W przypadku **ext3/ext4**, aby `barrier=0` wyłączyć bariery, użyj opcji montowania. (Aby włączyć bariery, `barrier=1`użyj .)
* W przypadku **programu XFS**, `nobarrier` aby wyłączyć bariery, użyj opcji montowania. (Aby włączyć bariery, `barrier`użyj .)
* W przypadku dysków magazynu w warstwie Premium z pamięcią podręczną ustawioną na **ReadWrite,** włącz bariery dla trwałości zapisu.
* Aby etykiety woluminów utrzymywały się po ponownym uruchomieniu maszyny Wirtualnej, należy zaktualizować /etc/fstab za pomocą uniwersalnych unikatowych odwołań do identyfikatorów (UUID) do dysków. Aby uzyskać więcej informacji, zobacz [Dodawanie dysku zarządzanego do maszyny Wirtualnej z systemem Linux](../articles/virtual-machines/linux/add-disk.md).

Następujące dystrybucje Linuksa zostały zatwierdzone dla najwyższej jakości ssd. Aby uzyskać lepszą wydajność i stabilność dzięki najwyższej jakości dysku SSD, zalecamy uaktualnienie maszyn wirtualnych do jednej z tych lub nowszych wersji. 

Niektóre wersje wymagają najnowszych usług integracji systemu Linux (LIS), wersja 4.0, dla platformy Azure. Aby pobrać i zainstalować dystrybucję, kliknij łącze wymienione w poniższej tabeli. Dodajemy obrazy do listy po zakończeniu sprawdzania poprawności. Nasze weryfikacje pokazują, że wydajność jest różna dla każdego obrazu. Wydajność zależy od charakterystyki obciążenia i ustawień obrazu. Różne obrazy są dostrojone dla różnych rodzajów obciążeń.

| Dystrybucja | Wersja | Obsługiwane jądro | Szczegóły |
| --- | --- | --- | --- |
| Ubuntu | 12.04 lub nowsze| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14.04 lub nowsze| 3.13.0-44.73+  | &nbsp; |
| Debian | 7.x, 8.x lub nowszy| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 lub nowszy| 3.12.36-38.1+ | &nbsp; |
| SUSE | SLES 11 SP4 lub nowszy| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+ lub nowsze| 3.18.4+ | &nbsp; |
| CentOS | 6,5, 6,6, 6,7, 7,0 lub nowsze| &nbsp; | [LIS4 wymagane](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Zobacz notatkę w następnej sekcji* |
| CentOS | 7.1+ lub nowsze| 3.10.0-229.1.2.el7+ | [LIS4 zalecane](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Zobacz notatkę w następnej sekcji* |
| Red Hat Enterprise Linux (RHEL) | 6,8+, 7,2+ lub nowszych | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ lub nowsze | &nbsp; | UEK4 lub RHCK |
| Oracle | 7.0-7.1 lub nowsze | &nbsp; | UEK4 lub RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 lub nowsze | &nbsp; | UEK4 lub RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>Sterowniki LIS dla OpenLogic CentOS

Jeśli używasz maszyn wirtualnych OpenLogic CentOS, uruchom następujące polecenie, aby zainstalować najnowsze sterowniki:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

W niektórych przypadkach powyższe polecenie również uaktualni jądro. Jeśli wymagana jest aktualizacja jądra, może być konieczne ponowne uruchomienie powyższych poleceń po ponownym uruchomieniu, aby w pełni zainstalować pakiet microsoft-hyper-v.


## <a name="disk-striping"></a>Rozkładanie dysku

Gdy maszyna wirtualna o dużej skali jest dołączona z kilkoma dyskami trwałymi magazynu w wersji premium, dyski mogą być rozłożone ze sobą w celu agregowania ich we/wy, przepustowości i pojemności magazynu.

W systemie Windows można używać funkcji Miejsca do magazynowania do rozmieszczenia dysków razem. Należy skonfigurować jedną kolumnę dla każdego dysku w puli. W przeciwnym razie ogólna wydajność woluminu rozłożonego może być niższa niż oczekiwano, ze względu na nierównomierny rozkład ruchu na dyskach.

Ważne: Za pomocą interfejsu użytkownika Menedżera serwera można ustawić całkowitą liczbę kolumn do 8 dla woluminu rozłożonego. Podczas podłączania więcej niż ośmiu dysków należy użyć programu PowerShell, aby utworzyć wolumin. Za pomocą programu PowerShell można ustawić liczbę kolumn równą liczbie dysków. Na przykład, jeśli istnieje 16 dysków w jednym zestawie pasków; określić 16 kolumn w *parametrze NumberOfColumns* polecenia cmdlet *programu New-VirtualDisk* PowerShell.

W systemie Linux użyj narzędzia MDADM, aby rozbierać dyski razem. Szczegółowe informacje na temat rozkładania dysków w systemie Linux można znaleźć w [części Konfigurowanie macierzy RAID oprogramowania w systemie Linux](../articles/virtual-machines/linux/configure-raid.md).

*Rozmiar woluminu rozłożonego*  
Ważną konfiguracją w rozkładaniu dysku jest rozmiar paska. Rozmiar paska lub rozmiar bloku jest najmniejszym fragmentem danych, który aplikacja może rozwiązać na woluminie rozłożonym. Rozmiar paska, który konfigurujesz, zależy od typu aplikacji i jej wzorca żądania. Jeśli wybierzesz niewłaściwy rozmiar paska, może to prowadzić do niewspółosiowość we/wy, co prowadzi do pogorszenia wydajności aplikacji.

Na przykład jeśli żądanie we/wy generowane przez aplikację jest większy niż rozmiar paska dysku, system magazynu zapisuje go w granicach jednostki paskowej na więcej niż jednym dysku. Gdy nadszedł czas, aby uzyskać dostęp do tych danych, będzie musiał szukać w więcej niż jednej jednostce rozłożone, aby zakończyć żądanie. Skumulowany efekt takiego zachowania może prowadzić do znacznego pogorszenia wydajności. Z drugiej strony, jeśli rozmiar żądania we/wy jest mniejszy niż rozmiar paska i jeśli ma charakter losowy, żądania we/wy mogą sumować się na tym samym dysku, powodując wąskie gardło i ostatecznie pogarszając wydajność we/wy.

W zależności od typu obciążenia, które aplikacja jest uruchomiona, wybierz odpowiedni rozmiar paska. W przypadku losowych małych żądań we/wy użyj mniejszego rozmiaru paska. Natomiast w przypadku dużych żądań sekwencyjnego we/wy należy używać większego rozmiaru paska. Znajdź zalecenia dotyczące rozmiaru paska dla aplikacji, którą będziesz uruchamiać w magazynie w wersji Premium. W przypadku programu SQL Server skonfiguruj rozmiar paska 64 KB dla obciążeń OLTP i 256 KB dla obciążeń magazynowania danych. Aby dowiedzieć się więcej, [zobacz najważniejsze rozwiązania dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure.](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance)

> [!NOTE]
> Można rozbierać maksymalnie 32 dyski magazynu w wersji premium na maszynie wirtualnej serii DS i 64 dyski magazynu w wersji premium na maszynie wirtualnej z serii GS.

## <a name="multi-threading"></a>Wielowątkowe

Platforma Azure zaprojektowała platformę Magazynu Premium, aby była masowo równoległa. W związku z tym aplikacja wielowątkowa osiąga znacznie wyższą wydajność niż aplikacja jednowątkowa. Aplikacja wielowątkowa dzieli swoje zadania na wiele wątków i zwiększa wydajność jego wykonywania, wykorzystując zasoby maszyny Wirtualnej i dysku do maksimum.

Na przykład jeśli aplikacja jest uruchomiona na maszynie wirtualnej z jednym rdzeniem przy użyciu dwóch wątków, procesor może przełączać się między dwoma wątkami, aby osiągnąć wydajność. Podczas gdy jeden wątek oczekuje na we/wy dysku, aby zakończyć, procesor może przełączyć się do drugiego wątku. W ten sposób dwa wątki można wykonać więcej niż pojedynczy wątek będzie. Jeśli maszyna wirtualna ma więcej niż jeden rdzeń, dodatkowo skraca czas pracy, ponieważ każdy rdzeń może wykonywać zadania równolegle.

Może nie być w stanie zmienić sposób off-the-shelf aplikacji implementuje jednowątkowy lub wielowątkowy. Na przykład SQL Server jest zdolny do obsługi wielu procesorów i wielordzeniowych. Jednak SQL Server decyduje, w jakich warunkach będzie korzystać z jednego lub więcej wątków do przetwarzania kwerendy. Można uruchamiać kwerendy i budować indeksy przy użyciu wielu wątków. Dla kwerendy, która obejmuje łączenie dużych tabel i sortowanie danych przed zwróceniem do użytkownika, SQL Server prawdopodobnie użyje wielu wątków. Jednak użytkownik nie może kontrolować, czy SQL Server wykonuje kwerendę przy użyciu pojedynczego wątku lub wielu wątków.

Istnieją ustawienia konfiguracji, które można zmienić, aby wpłynąć na to wielowątkowość lub równoległe przetwarzanie aplikacji. Na przykład w przypadku PROGRAMU SQL Server jest to maksymalny stopień konfiguracji równoległości. To ustawienie o nazwie MAXDOP umożliwia skonfigurowanie maksymalnej liczby procesorów, których program SQL Server może używać podczas przetwarzania równoległego. Można skonfigurować MAXDOP dla poszczególnych zapytań lub operacji indeksu. Jest to korzystne, gdy chcesz zrównoważyć zasoby systemu dla aplikacji o krytycznym znaczeniu dla wydajności.

Załóżmy na przykład, że aplikacja korzystająca z programu SQL Server wykonuje dużą kwerendę i operację indeksu w tym samym czasie. Załóżmy, że chcesz operacji indeksu być bardziej wydajne w porównaniu do dużych zapytań. W takim przypadku można ustawić wartość MAXDOP operacji indeksu jest wyższa niż wartość MAXDOP dla kwerendy. W ten sposób SQL Server ma więcej procesorów, które można wykorzystać dla operacji indeksu w porównaniu do liczby procesorów, które można poświęcić do dużych zapytań. Należy pamiętać, że nie należy kontrolować liczbę wątków SQL Server będzie używany dla każdej operacji. Można kontrolować maksymalną liczbę procesorów przeznaczonych do wielowątkowej.

Dowiedz się więcej o [stopniach równoległości](https://technet.microsoft.com/library/ms188611.aspx) w programie SQL Server. Dowiedz się takie ustawienia, które wpływają na wielowątkową w aplikacji i ich konfiguracje, aby zoptymalizować wydajność.

## <a name="queue-depth"></a>Głębokość kolejki

Głębokość kolejki lub długość kolejki lub rozmiar kolejki to liczba oczekujących żądań we/wy w systemie. Wartość głębokości kolejki określa, ile operacji we/wy aplikacja może wyrównać, które będą przetwarzane dyski magazynu. Wpływa na wszystkie trzy wskaźniki wydajności aplikacji, które omówiliśmy w tym artykule viz., IOPS, przepływność i opóźnienie.

Głębokość kolejki i wielowątkowości są ściśle ze sobą powiązane. Wartość głębokość kolejki wskazuje, ile wielu wątków można osiągnąć przez aplikację. Jeśli głębokość kolejki jest duża, aplikacja może wykonać więcej operacji jednocześnie, innymi słowy, więcej wielowątkowych. Jeśli głębokość kolejki jest mały, mimo że aplikacja jest wielowątkowa, nie będzie miał wystarczającej liczby żądań w kolejce do równoczesnego wykonywania.

Zazwyczaj aplikacje poza półką nie pozwalają na zmianę głębokości kolejki, ponieważ jeśli ustawiono niepoprawnie, wyrządzi więcej szkody niż pożytku. Aplikacje ustawią odpowiednią wartość głębokości kolejki, aby uzyskać optymalną wydajność. Jednak ważne jest, aby zrozumieć tę koncepcję, dzięki czemu można rozwiązać problemy z wydajnością aplikacji. Można również zaobserwować skutki głębokości kolejki, uruchamiając narzędzia do analizy porównawczej w systemie.

Niektóre aplikacje zapewniają ustawienia wpływające na głębokość kolejki. Na przykład ustawienie MAXDOP (maksymalny stopień równoległości) w programie SQL Server wyjaśnione w poprzedniej sekcji. MAXDOP jest sposobem wpływania na głębokość kolejki i wielowątkowość, chociaż nie zmienia bezpośrednio wartości głębokość kolejki programu SQL Server.

*Duża głębokość kolejki*  
Głębokość dużej kolejki ustawia więcej operacji na dysku. Dysk zna następne żądanie w kolejce z wyprzedzeniem. W związku z tym dysk można zaplanować operacje z wyprzedzeniem i przetwarzać je w optymalnej kolejności. Ponieważ aplikacja wysyła więcej żądań na dysk, dysk może przetwarzać więcej równoległych we/wy. Ostatecznie aplikacja będzie w stanie osiągnąć wyższe WeOPS. Ponieważ aplikacja przetwarza więcej żądań, całkowita przepływność aplikacji również zwiększa.

Zazwyczaj aplikacja może osiągnąć maksymalną przepustowość z 8-16+ zaległych we/wy na podłączonym dysku. Jeśli głębokość kolejki jest jeden, aplikacja nie wypycha wystarczającej liczby we/wył. do systemu i będzie przetwarzać mniejszą ilość w danym okresie. Innymi słowy, mniej przepływności.

Na przykład w programie SQL Server ustawienie wartości MAXDOP dla kwerendy na "4" informuje SQL Server, że może używać maksymalnie czterech rdzeni do wykonania kwerendy. SQL Server określi, co jest najlepszą wartością głębokości kolejki i liczbę rdzeni do wykonywania kwerendy.

*Optymalna głębokość kolejki*  
Bardzo wysoka wartość głębokości kolejki ma również swoje wady. Jeśli wartość głębokości kolejki jest zbyt wysoka, aplikacja spróbuje prowadzić bardzo wysokie usługi We/Wy. Jeśli aplikacja ma trwałe dyski z wystarczającą aprowizacji Usługi We/Wy, może to negatywnie wpłynąć na opóźnienia aplikacji. Poniższa formuła pokazuje relację między we/wy, opóźnieniem i głębokością kolejki.  
    ![](media/premium-storage-performance/image6.png)

Nie należy konfigurować głębokość kolejki do dowolnej wysokiej wartości, ale do optymalnej wartości, która może dostarczyć wystarczającą liczbę we/wy dla aplikacji bez wpływu na opóźnienia. Na przykład jeśli opóźnienie aplikacji musi wynosić 1 milisekundę, głębokość kolejki wymagana do osiągnięcia 5000 operacji We/Wy jest, QD = 5000 x 0,001 = 5.

*Głębokość kolejki dla woluminu rozłożonego*  
W przypadku woluminu rozłożonego należy zachować wystarczająco wysoką głębokość kolejki, tak aby każdy dysk miał indywidualnie głębokość kolejki szczytowej. Rozważmy na przykład aplikację, która wypycha głębokość kolejki 2 i istnieją cztery dyski w pasku. Dwa żądania we/wy zostaną wysłane na dwa dyski, a pozostałe dwa dyski będą bezczynne. W związku z tym należy skonfigurować głębokość kolejki w taki sposób, aby wszystkie dyski mogły być zajęte. Poniższa formuła pokazuje, jak określić głębokość kolejki woluminów rozłożonych.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Ograniczanie przepływności

Usługa Azure Premium Storage apowiednie określona liczba we/wy i przepływność w zależności od rozmiarów maszyn wirtualnych i rozmiarów dysków, które wybierzesz. Za każdym razem, gdy aplikacja próbuje dysk We/Wy lub przepływność powyżej tych limitów, co maszyna wirtualna lub dysk może obsłużyć, magazyn w wersji Premium będzie ją ograniczać. To objawia się w postaci obniżonej wydajności w aplikacji. Może to oznaczać większe opóźnienie, niższą przepływność lub niższe we/wy. Jeśli magazyn w wersji Premium nie przepustnicy, aplikacja może całkowicie zakończyć się niepowodzeniem, przekraczając to, co jego zasoby są w stanie osiągnąć. Tak, aby uniknąć problemów z wydajnością z powodu ograniczania przepustowości, zawsze aprowizować wystarczające zasoby dla aplikacji. Należy wziąć pod uwagę, co omówiliśmy w rozmiarach maszyn wirtualnych i rozmiarach dysków powyżej. Analiza porównawcza to najlepszy sposób, aby dowiedzieć się, jakie zasoby będą potrzebne do hostowania aplikacji.

## <a name="next-steps"></a>Następne kroki
