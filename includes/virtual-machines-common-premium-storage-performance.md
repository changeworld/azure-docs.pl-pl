# <a name="azure-premium-storage-design-for-high-performance"></a>Magazynu Azure Premium: Projekt o wysokiej wydajności

Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji wysokiej wydajności przy użyciu usługi Azure Premium Storage. Korzystając z instrukcjami podanymi w tym dokumencie łączyć się z najlepszymi rozwiązaniami wydajności mające zastosowanie do technologii używanych przez aplikację. Aby zilustrować wytyczne, użyliśmy program SQL Server uruchomiony na magazyn w warstwie Premium, na przykład w tym dokumencie.

Natomiast scenariusze wydajności warstwy magazynu w tym artykule można rozwiązać, konieczne będzie optymalizacji warstwy aplikacji. Na przykład jeśli przechowujesz farmy programu SharePoint w usłudze Azure Premium Storage umożliwia przykłady programu SQL Server z tego artykułu zoptymalizować serwer bazy danych. Ponadto zoptymalizować serwera sieci Web farmy programu SharePoint i serwera aplikacji, aby uzyskać większości wydajności.

Ten artykuł pomoże odpowiedzi po często zadawane pytania dotyczące optymalizacji wydajności aplikacji w usłudze Azure Premium Storage

* Sposób mierzenia wydajności aplikacji?  
* Dlaczego są nie widać oczekiwanego wysokiej wydajności?  
* Jakie czynniki wpływają wydajność aplikacji na magazyn w warstwie Premium?  
* Jak te czynniki wpływają wydajność aplikacji na magazyn w warstwie Premium  
* Jak można zoptymalizować IOPS, przepustowości i opóźnień?  

Firma Microsoft umieściła wytyczne specjalnie z myślą o magazyn w warstwie Premium ponieważ obciążeń uruchomionych na magazyn w warstwie Premium są wysokiej wydajności poufnych. Firma Microsoft umieściła przykłady, gdzie jest to odpowiednie. Można również zastosować niektóre z tych wskazówek do aplikacji działających na maszynach wirtualnych IaaS z dyskami magazynu w warstwie standardowa.

Przed rozpoczęciem, jeśli jesteś nowym użytkownikiem magazyn w warstwie Premium, najpierw przeczytać artykuł [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla obciążeń maszyny wirtualnej Azure](../articles/virtual-machines/windows/premium-storage.md) i [skalowalności magazynu Azure i cele wydajności](../articles/storage/common/storage-scalability-targets.md)artykułów.

## <a name="application-performance-indicators"></a>Wskaźniki wydajności aplikacji
Możemy oceny, czy aplikacja działa również lub nie używa wydajności, takich jak wskaźniki, tempa aplikacji przetwarza żądanie użytkownika, jak dużo danych przetwarza aplikację na żądanie, ile żądań przetwarza aplikacji w określonej okres czasu, jak długo użytkownik musi czekać na uzyskanie odpowiedzi po przesłaniu żądania. Terminy techniczne dla tych wskaźników wydajności są IOPS, przepustowości lub przepustowości i opóźnień.

W tej sekcji omówiono typowe wskaźniki wydajności w kontekście magazyn w warstwie Premium. W poniższej sekcji, zbieranie wymagania dotyczące aplikacji, dowiesz się, jak do mierzenia tych wskaźników wydajności dla aplikacji. W dalszej optymalizacji wydajności aplikacji będzie Poznaj czynników wpływających na te wskaźniki wydajności i zalecenia w celu optymalizacji ich.

## <a name="iops"></a>Operacje wejścia/wyjścia
IOPS to liczba żądań, które wysyła aplikacji do magazynu dysków w ciągu sekundy. Operacji wejścia/wyjścia można odczytać lub zapisać, sekwencyjnych lub losowych. Aplikacje OLTP, takie jak witryny sieci Web platformy handlowej online należy natychmiast przetwarzać wiele żądań równoczesnych użytkowników. Użytkownik żąda są insert i aktualizowanie transakcji bazy danych, których aplikacja musi przetworzyć w odpowiednim. W związku z tym aplikacje OLTP wymagają bardzo wysokiej IOPS. Takie aplikacje będą obsługiwać miliony małych i losowe żądań We/Wy. Jeśli masz takiej aplikacji, należy zaprojektować infrastruktury aplikacji, aby zoptymalizować dla IOPS. W dalszej części artykułu *optymalizacji wydajności aplikacji*, omówiono szczegółowo czynników, które należy wziąć pod uwagę można uzyskać wysoką wartość IOPS.

Po dołączeniu dysku magazynu premium do Twojej wysokiej skali maszyny Wirtualnej, Azure przepisy dla Ciebie gwarantowane liczbę IOPS zgodnie ze specyfikacją dysku. Na przykład dysk P50 inicjuje 7500 IOPS. Każdy dużą skalę rozmiaru maszyny Wirtualnej ma również określonego limitu IOPS może kontynuować działanie. Na przykład standardowe GS5 maszyna wirtualna ma 80 000 IOPS ograniczenia.

## <a name="throughput"></a>Przepływność
Przepływność lub przepustowości jest ilość danych aplikacji wysyła do dysków w magazynie w określonych odstępach czasu. Jeśli aplikacja wykonuje operacje wejścia/wyjścia o dużych rozmiarach jednostki we/wy, wymaga wysokiej przepływności. Aplikacje magazynu danych zwykle do wystawiania skanowania intensywne operacje dostęp dużej części danych w czasie i często wykonywać operacje zbiorcze. Innymi słowy takich aplikacji wymaga wyższej przepustowości. Jeśli masz takiej aplikacji, należy zaprojektować jego infrastruktury w celu zoptymalizowania przepustowości. W następnej sekcji omówiono szczegółowo czynników, musisz dostosować to osiągnąć.

Po dołączeniu dysku magazynu premium na dużą skalę maszyny Wirtualnej, Azure przepisy przepływności zgodnie z tym specyfikację dysku. Na przykład dysk P50 inicjuje 250 MB na dysku drugi przepływności. Każdy dużą skalę rozmiaru maszyny Wirtualnej ma również jako określonych limit przepustowości, co może kontynuować działanie. Na przykład standardowe GS5 maszyna wirtualna ma maksymalną przepustowość 2000 MB na sekundę. 

Brak relacji między przepływności i IOPS, jak pokazano poniżej formuły.

![](media/premium-storage-performance/image1.png)

Dlatego jest ważne jest określenie optymalnej przepływności IOPS wartości i wymagane przez aplikację. Przy próbie optymalizacji, jeden innych pobiera dotyczy także. W dalszej części artykułu *optymalizacji wydajności aplikacji*, omówimy w bardziej szczegółowe informacje o optymalizacji IOPS i przepustowość.

## <a name="latency"></a>Opóźnienie
Czas oczekiwania jest czas aplikacji pojedynczego żądania odbierania i wysyłania go do magazynu dysków wysłać odpowiedź do klienta. Jest to miara krytyczne wydajności aplikacji oprócz IOPS i przepływności. Opóźnienie dysku magazynu premium jest czas potrzebny na pobranie informacji dla żądania i przekazuje go aplikacji. Magazyn w warstwie Premium zapewnia stale małe opóźnienia. Jeśli włączysz buforowania na dyski premium magazynu na hoście tylko do odczytu, możesz uzyskać dużo mniejsze opóźnienia odczytu. Omówimy buforowania dysku bardziej szczegółowo w dalszej części artykułu na *optymalizacji wydajności aplikacji*.

Optymalizując Twojej aplikacji w celu uzyskania większej IOPS i przepływności, ma wpływ na opóźnienie aplikacji. Po dostrajania wydajności aplikacji, należy zawsze ocenia opóźnienie aplikacji, aby uniknąć nieoczekiwanego duże opóźnienie zachowania.

## <a name="gather-application-performance-requirements"></a>Gromadzenie wymagań dotyczących wydajności aplikacji
Pierwszy krok w projektowaniu wysoka wydajność aplikacji uruchomionych na usłudze Azure Premium Storage jest, aby poznać wymagania dotyczące wydajności aplikacji. Po zebraniu wymagania dotyczące wydajności, aby zoptymalizować aplikacji do osiągnięcia najbardziej optymalną wydajność.

W poprzedniej sekcji możemy wyjaśniono wspólnych wskaźników wydajności, IOPS, przepustowości i opóźnień. Należy określić, która z tych wskaźników wydajności są krytyczne dla aplikacji do dostarczania środowisko żądanego użytkownika. Na przykład wysokiej IOPS przyjazny dla aplikacji OLTP przetwarzania miliony transakcje na sekundę. Wysokiej przepływności jest krytyczne dla magazynu danych aplikacji przetwarzania dużych ilości danych na sekundę. Bardzo małe opóźnienia jest kluczowa dla aplikacji w czasie rzeczywistym, takich jak wideo na żywo przesyłania strumieniowego witryn sieci Web.

Następnie zmierzyć wymagania maksymalną wydajność aplikacji przez cały cykl jej życia. Użyj listy kontrolnej próbki poniżej jako rozpoczęcia. Zarejestruj wymagania dotyczące maksymalnej wydajności podczas normalnego, okresów obciążenia szczytowego i poza godzinami szczytu. Przez ustalenie wymagań dotyczących wszystkich poziomów obciążeń, można określić wymaganie ogólną wydajność aplikacji. Na przykład zwykłe obciążenie pracą z witryną internetową handlu elektronicznego będzie transakcje, które służy ona większość dni w roku. Obciążenia szczytowego witryny sieci Web będzie transakcje, które służy ona podczas zdarzenia specjalne sprzedaży lub świąt. Obciążenia szczytowego jest zwykle doświadczenie w ograniczonym okresie, ale może wymagać aplikacji do skalowania dwa lub więcej razy swoich normalnych operacji. Dowiedz się 50. percentyl, percentyl 90 i wymagania percentyl 99. Dzięki temu odfiltrowywania żadnych wartości odstających w wymagania dotyczące wydajności i skoncentrować się wysiłków na optymalizacji dla prawej wartości.

**Lista kontrolna wymagań dotyczących wydajności aplikacji**

| **Wymagania dotyczące wydajności** | **50. percentyl** | **Percentyl 90** | **Percentyl 99** |
| --- | --- | --- | --- |
| Maksymalnie z Transakcje na sekundę | | | |
| Operacje odczytu % | | | |
| Operacje zapisu % | | | |
| % Losowe operacje | | | |
| % Kolejnych operacji. | | | |
| Rozmiar żądania We/Wy | | | |
| Średnia przepustowość | | | |
| Maksymalnie z Przepływność | | | |
| Min. Opóźnienie | | | |
| Średni czas oczekiwania | | | |
| Maksymalnie z Procesor CPU | | | |
| Średnie użycie procesora CPU | | | |
| Maksymalnie z Memory (Pamięć) | | | |
| Średnia pamięci | | | |
| Głębokość kolejki | | | |

> [!NOTE]
> Należy rozważyć skalowania tych numerów oparte na oczekiwany przyszłego rozwoju aplikacji. Jest warto zaplanować wzrost wcześniejsze, ponieważ może być trudniejsza do infrastruktury dla poprawy wydajności później zmienić.
>
>

Jeśli masz istniejącą aplikację i chcesz przenieść magazyn w warstwie Premium, należy najpierw utworzyć listę kontrolną powyżej istniejącej aplikacji. Następnie kompilacji prototyp aplikacji na magazyn w warstwie Premium i projektowania aplikacji według wskazówek opisanych w *optymalizacji wydajności aplikacji* w dalszej części tego dokumentu. W następnej sekcji opisano narzędzia, których można użyć do zbierania miar wydajności.

Utwórz listę kontrolną podobne do istniejącej aplikacji dla prototypu. Za pomocą narzędzi Benchmarking można symulować obciążeń i mierzyć wydajność aplikacji prototypu. Zobacz sekcję dotyczącą [Benchmarking](#benchmarking) Aby dowiedzieć się więcej. Wykonując, dzięki czemu można określić, czy magazyn w warstwie Premium mogą odpowiadać lub przekroczenia wymagań dotyczących wydajności aplikacji. Następnie można wdrożyć tych samych wskazówek dla aplikacji produkcyjnej.

### <a name="counters-to-measure-application-performance-requirements"></a>Liczniki do mierzenia wymagania dotyczące wydajności aplikacji
Jest najlepszy sposób, aby zmierzyć wymagania dotyczące wydajności aplikacji, aby użyć narzędzia do monitorowania wydajności systemu operacyjnego serwera. Można użyć Monitora wydajności dla systemu Windows i iostat dla systemu Linux. Te narzędzia przechwytywania liczniki odpowiadający Każda miara wyjaśniono w powyższej sekcji. Gdy aplikacja jest uruchomiona, jej normalny, obciążeń szczycie i poza godzinami szczytu konieczne jest przechwycenie wartości tych liczników.

Liczniki Monitora wydajności są dostępne dla procesora, pamięci, a każdy dysk logiczny i dysku fizycznego serwera. Korzystając z dysków w warstwie premium magazynu z maszyny Wirtualnej, liczników dysków fizycznych są dla każdego dysku magazynu premium i są dysku logicznego liczniki dla każdego woluminu tworzone na dyskach magazynu premium. Konieczne jest przechwycenie wartości dysków obsługujące obciążenia aplikacji. Jeśli jest mapowanie jeden-do-jednego między dysków logicznych i fizycznych, mogą odwoływać się do dysku fizycznego liczników; w przeciwnym razie zapoznaj się liczniki dysku logicznego. W systemie Linux polecenie iostat generuje raport o wykorzystaniu Procesora i dysku. Raport o wykorzystaniu dysku zawiera dane statystyczne na urządzeniu fizycznym lub partycji. Jeśli masz serwer bazy danych z danych i dziennika na oddzielnych dyskach zbierać dane dla obydwa dyski. Poniższej tabeli opisano liczniki dotyczące dysków, procesora i pamięci:

| Licznik | Opis | Monitora wydajności | Iostat |
| --- | --- | --- | --- |
| **Liczba IOPS lub transakcje na sekundę** |Liczba żądań We/Wy wystawiony dla dysku magazynu na sekundę. |Odczyty dysku/s <br> Zapisy dysku/s |tps <br> r/s <br> p/s |
| **Dysk odczyty i zapisy** |% operacji odczytu i zapisu operacji wykonywanych na dysku. |Czas odczytu dysku (%) <br> Czas zapisu na dysku % |r/s <br> p/s |
| **Przepływność** |Ilość danych odczytu lub zapisu na dysku na sekundę. |Bajty odczytu z dysku/s <br> Bajty zapisu na dysku/s |kB_read/s <br> kB_wrtn/s |
| **Opóźnienie** |Łączny czas wymagany do ukończenia żądania We/Wy dysku. |Średnia dysku w s/Odczyt <br> Średnia dysku w s/Zapis |await <br> svctm |
| **Rozmiar operacji We/Wy** |Rozmiar operacji We/Wy żądań problemy z dyskami magazynu. |Bajty odczytu dysku <br> Bajty zapisu dysku |avgrq sz |
| **Głębokość kolejki** |Liczba oczekujących operacji We/Wy żądań oczekujących odczytywane lub zapisywane na dysku magazynu. |Bieżąca długość kolejki dysku |avgqu sz |
| **Maks. Pamięci** |Ilość pamięci wymaganą do bezproblemowe działanie aplikacji |Zadeklarowane bajty w użyciu (%) |Użyj vmstat |
| **Maks. PROCESOR CPU** |Ilość procesorów wymaganych bezproblemowe działanie aplikacji |Czas procesora (%) |% util |

Dowiedz się więcej o [iostat](https://linux.die.net/man/1/iostat) i [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Optymalizacja wydajności aplikacji
Główne czynniki wpływające na wydajność aplikacji uruchomionych na magazyn w warstwie Premium są charakter z żądania We/Wy, rozmiar maszyny Wirtualnej, rozmiar dysku, liczbę dysków, dysku pamięci podręcznej, Multithreading i głębokość kolejki. Niektóre z nich można kontrolować z pokrętła obsługiwanych przez system. Większość aplikacji może nie zapewniają możliwość zmiany rozmiaru we/wy i głębokość kolejki bezpośrednio. Na przykład jeśli używasz programu SQL Server, nie można wybrać głębokość rozmiaru i kolejki we/wy. SQL Server wybiera optymalną we/wy rozmiaru i kolejki głębokość wartości do większości wydajność. Ważne jest zrozumienie wpływu obu typów czynników na wydajność aplikacji, dzięki czemu można udostępnić odpowiednie zasoby, aby spełnić potrzeby w zakresie wydajności.

W tej sekcji odnoszą się do listy kontrolnej wymagania dotyczące aplikacji, który został utworzony, aby określić, ile trzeba zoptymalizować wydajność aplikacji. W oparciu o który, będzie możliwe ustalenie, jakie czynniki w tej sekcji należy do dopasowywania. Obecności skutków każdy czynnik na wydajność aplikacji, należy uruchomić narzędzia najlepszymi w ustawieniach aplikacji. Zapoznaj się [Benchmarking](#Benchmarking) sekcji na końcu tego artykułu czynności, aby uruchomić narzędziom najlepszymi w systemach Windows i maszyn wirtualnych systemu Linux.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optymalizacja IOPS, przepustowości i opóźnień w skrócie

Poniższa tabela zawiera podsumowanie czynniki wydajności i kroki niezbędne do optymalizacji IOPS, przepustowości i opóźnień. W sekcjach poniżej to podsumowanie opisano każdy czynnik jest znacznie bardziej szczegółowo.

Aby uzyskać więcej informacji na rozmiarów maszyn wirtualnych i na IOPS, przepustowości i opóźnień dostępne dla każdego typu maszyny Wirtualnej, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md) lub [rozmiarów maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Przepływność** | **Opóźnienie** |
| --- | --- | --- | --- |
| **Przykładowy scenariusz** |Enterprise OLTP aplikacji wymagających bardzo duże transakcje na drugi szybkości. |Magazynowania aplikacji przetwarzania dużych ilości danych danych przedsiębiorstwa. |Niemal w czasie rzeczywistym aplikacji wymagających błyskawicznych odpowiedzi na żądania użytkownika, takie jak gier online. |
| Czynniki wydajności | &nbsp; | &nbsp; | &nbsp; |
| **Rozmiar operacji We/Wy** |Mniejszego rozmiaru we/wy daje wyższa wartość IOPS. |Większy rozmiar we/wy do daje wyższej przepustowości. | &nbsp;|
| **Rozmiar maszyny Wirtualnej** |Użyj rozmiar maszyny Wirtualnej, który oferuje IOPS większą niż wymagań aplikacji. |Rozmiar maszyny Wirtualnej za pomocą limit przepływności większy od wymagań aplikacji. |Użyj rozmiar maszyny Wirtualnej, że oferty skalować limity większe wymagania Twojej aplikacji. |
| **Rozmiar dysku** |Użyj rozmiar dysku, który oferuje IOPS większą niż wymagań aplikacji. |Rozmiar dysku za pomocą limit przepływności większy od wymagań aplikacji. |Użyj rozmiaru dysku, czy oferuje skalowanie limity większe wymagania Twojej aplikacji. |
| **Maszyna wirtualna i limity skalowania dysku** |Limit IOPS wybrany rozmiar maszyny Wirtualnej powinna być większa niż łączna liczba IOPS wynikają z dysków w warstwie premium magazynu dołączone do niego. |Limit przepustowości wybrany rozmiar maszyny Wirtualnej powinna być większa niż całkowita przepływność wynikają z dysków w warstwie premium magazynu dołączone do niego. |Limity skalowania wybranego rozmiaru maszyny Wirtualnej musi być większa niż limity skalowania całkowita premium dołączonych dysków magazynowania. |
| **Buforowanie dysku** |Włącz pamięć podręczną tylko do odczytu na dyski magazynu premium z dużym operacje odczytu, uzyskanie wyższej IOPS odczytu. | &nbsp; |Włączenie pamięci podręcznej tylko do odczytu dla dysków w warstwie premium magazynu z gotowy duże operacje można pobrać odczytu bardzo niskich opóźnień. |
| **Rozkładanie** |Przy użyciu wielu dysków i paskowych je ze sobą, aby uzyskać Scalonej wyższy limit IOPS i przepustowość. Należy zauważyć, że łączny limit dla maszyny Wirtualnej powinna być większa niż łączna limity premium dołączonych dysków. | &nbsp; | &nbsp; |
| **Rozmiar usługi STRIPE** |Mniejszy rozmiar stripe dla losowych małych wzorca we/wy w aplikacji OLTP. Przykład dla aplikacji OLTP programu SQL Server za pomocą usługi stripe rozmiar 64KB. |Większy rozmiar stripe sequential dużych wzorzec we/wy w magazynie danych aplikacji. Np. Użyj rozmiaru stripe 256KB dla aplikacji magazynu danych programu SQL Server. | &nbsp; |
| **Wielowątkowość** |Użycie wielowątkowość do dystrybuowania większej liczby żądań do magazyn w warstwie Premium, która będzie prowadzić do wyższej IOPS i przepustowość. Na przykład ustawić wysokiej wartości MAXDOP do przydzielenia większej liczby procesorów CPU do programu SQL Server na serwerze SQL. | &nbsp; | &nbsp; |
| **Głębokość kolejki** |Głębokość kolejki większej daje wyższa wartość IOPS. |Głębokość kolejki większej daje wyższej przepustowości. |Mniejsze głębokość kolejki daje niższe opóźnienia. |

## <a name="nature-of-io-requests"></a>Rodzaj żądań We/Wy
Żądania We/Wy jest jednostką operacji wejścia/wyjścia, której aplikacja będzie wykonywać. Identyfikujący rodzaj żądań We/Wy, losowych lub sekwencyjnych, odczytu lub zapisu, małych i dużych, pomoże określić wymagania dotyczące wydajności aplikacji. Jest bardzo ważne zrozumieć charakter żądań We/Wy, aby prawidłowych decyzji podczas projektowania infrastruktury aplikacji.

Rozmiar operacji We/Wy jest jednym z bardziej ważne czynniki. Rozmiar operacji We/Wy jest rozmiarem żądania operacji wejścia/wyjścia wygenerowane przez aplikację. Rozmiar operacji We/Wy ma znaczący wpływ na wydajność, szczególnie w przypadku IOPS oraz przepustowość zapewni aplikacji. Następująca formuła przedstawiono relacje między IOPS, rozmiaru we/wy i przepustowości/przepływności.  
    ![](media/premium-storage-performance/image1.png)

Niektóre aplikacje pozwalają na zmiany ich rozmiaru we/wy, gdy niektóre aplikacje nie. Na przykład SQL Server określa optymalny rozmiar we/wy się i nie zapewnia użytkowników z dowolnego pokrętła je zmienić. Z drugiej strony, Oracle zawiera parametr o nazwie [DB\_ZABLOKOWAĆ\_rozmiar](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) za pomocą którego można skonfigurować rozmiaru żądania We/Wy bazy danych.

Jeśli używasz aplikacji, która nie zezwala na zmianę rozmiaru we/wy, postępuj zgodnie z wytycznymi w tym artykule w celu zoptymalizowania wydajności wskaźnika KPI, który najlepiej pasuje do aplikacji. Na przykład:

* Aplikacja OLTP generuje miliony małych i losowe żądań We/Wy. Do obsługi tych typów żądań We/Wy, należy zaprojektować infrastruktury aplikacji, można pobrać wyższa wartość IOPS.  
* Aplikacja magazynowania danych generuje duży i kolejnych żądań We/Wy. Do obsługi tych typów żądań We/Wy, należy zaprojektować infrastrukturę aplikacji uzyskanie większej przepustowości lub przepływności.

Jeśli używasz aplikacji, dzięki czemu można zmienić rozmiar we/wy, użyj tego zasadą dla rozmiaru we/wy, oprócz pozostałych wskazówek wydajności

* Mniejszego rozmiaru we/wy, można pobrać wyższa wartość IOPS. Na przykład 8 KB aplikacji OLTP.  
* Większy rozmiar we/wy, aby uzyskać wyższą przepustowość/przepływność. Na przykład 1024 KB dla aplikacji magazynu danych.

Oto przykład na obliczenie IOPS i przepływności/przepustowość dla aplikacji. Należy wziąć pod uwagę przy użyciu dysku P30 aplikacji. Maksymalna, który można osiągnąć IOPS i przepływności/przepustowość dysku P30 jest 5000 IOPS i 200 MB na sekundę odpowiednio. Teraz Jeśli aplikacja wymaga maksymalna liczba IOPS z dysku P30 i użyć mniejszego rozmiaru we/wy, takich jak 8 KB, wynikowy przepustowość można uzyskać jest 40 MB na sekundę. Jednak jeśli aplikacja wymaga maksymalnej przepływności/przepustowości z dysku P30 i używana będzie większy rozmiar we/wy, takich jak 1024 KB, IOPS wynikowy będzie mniejsza, 200 IOPS. W związku z tym dostroić rozmiaru we/wy, tak, że spełnia on wymagania IOPS i przepływności/przepustowość zarówno Twojej aplikacji. W poniższej tabeli przedstawiono różne rozmiary we/wy oraz ich odpowiednich IOPS i przepływność dysku P30.

| Wymagania aplikacji | Rozmiar operacji We/Wy | Operacje wejścia/wyjścia | Przepływność/przepustowość |
| --- | --- | --- | --- |
| Maks. IOPS |8 KB |5000 |40 MB na sekundę |
| Przepustowość maksymalna |1024 KB |200 |200 MB / s |
| Maksymalna przepustowość + wysokiej IOPS |64 KB |3,200 |200 MB / s |
| Maksymalna liczba IOPS + wysokiej przepływności |32 KB. |5000 |160 MB na sekundę |

Aby uzyskać IOPS i wyższa niż wartość maksymalna dysku magazynu premium pojedynczego przepustowości, użyj wielu dysków w warstwie premium rozkładane razem. Na przykład usługi stripe dwóch P30 dysków, aby uzyskać łączna liczba IOPS z 10 000 IOPS lub łączna przepustowość 400 MB na sekundę. Zgodnie z objaśnieniem w następnej sekcji, musisz użyć rozmiaru maszyny Wirtualnej, która obsługuje połączonych IOPS i przepływność dysku.

> [!NOTE]
> Zwiększania IOPS lub innych również zwiększa przepływność upewnij się, że nie naciśnięciu przepływności lub limity liczby dysku lub maszyny Wirtualnej podczas zwiększenie dowolnego z nich.
>
>

Aby Monitor skutków rozmiaru we/wy na wydajność aplikacji, należy uruchomić narzędzia najlepszymi maszyny Wirtualnej i dysków. Tworzenie wielu uruchomień testów i użyj innego rozmiaru we/wy przy każdym uruchomieniu, aby zobaczyć wpływ. Zapoznaj się [Benchmarking](#Benchmarking) sekcji na końcu tego artykułu, aby uzyskać więcej informacji.

## <a name="high-scale-vm-sizes"></a>Wysoka skalowalność rozmiarów maszyn wirtualnych
Po ponownym uruchomieniu, projektowania aplikacji, jeden z pierwszego czynności wykonywane jest, wybierz Maszynę wirtualną na potrzeby hostowania aplikacji. Magazyn w warstwie Premium jest dostarczany z rozmiary wysokiej skali maszyny Wirtualnej, które można uruchomić aplikacji wymagających wyższej moc obliczeniową i wysokiej wydajności We/Wy dysku lokalnym. Te maszyny wirtualne zapewniają szybkich procesorów, większy współczynnik pamięci — podstawowe i Solid-State Drive (SSD) dla dysku. Przykłady wysokiej skali maszyny wirtualne obsługujące magazyn w warstwie Premium serii DS, DSv2 i GS maszyn wirtualnych.

Wysokiej skali maszyny wirtualne są dostępne w różnych rozmiarach z różną liczbę rdzeni Procesora, pamięci, systemu operacyjnego i rozmiar dysku tymczasowym. Rozmiar każdej maszyny Wirtualnej ma również maksymalną liczbę dysków z danymi, które można załączyć do maszyny Wirtualnej. W związku z tym wybrany rozmiar maszyny Wirtualnej będzie miało wpływ na ilość przetwarzania, pamięci, a pojemność magazynu jest dostępna dla aplikacji. Wpływa to również na mocy obliczeniowej i kosztów magazynowania. Na przykład poniżej przedstawiono specyfikację największy rozmiar maszyny Wirtualnej w serii DS, DSv2 serii i serii GS:

| Rozmiar maszyny wirtualnej | Rdzenie procesora CPU | Memory (Pamięć) | Rozmiary dysków maszyny Wirtualnej | Maksymalnie z dyski danych | Rozmiar pamięci podręcznej | Operacje wejścia/wyjścia | Limity przepustowości we/wy w pamięci podręcznej |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_DS14 |16 |112 GB |SYSTEM OPERACYJNY = 1023 GB. <br> Lokalny dysk SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB na sekundę |4000 IOPS i 33 MB na sekundę |
| Standard_GS5 |32 |448 GB |SYSTEM OPERACYJNY = 1023 GB. <br> Lokalny dysk SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2000 MB na sekundę |5000 IOPS i 50 MB / s |

Aby wyświetlić pełną listę wszystkich dostępnych rozmiarów maszyny Wirtualnej platformy Azure, zobacz [rozmiarów maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/sizes.md) lub [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md). Wybierz rozmiar maszyny Wirtualnej, które spełniają i skalować do wymagań dotyczących wydajności żądanej aplikacji. Oprócz tego uwzględniać następujące istotne kwestie, wybierając rozmiarów maszyn wirtualnych.

*Limity skalowania*  
Maksymalnych IOPS dla poszczególnych maszyn wirtualnych i dysk są różne i od siebie niezależne. Upewnij się, że aplikacja sterujące IOPS w ramach maszyny Wirtualnej, a także do niego dołączony dysków w warstwie premium. W przeciwnym razie wydajności aplikacji może wystąpić ograniczenie.

Na przykład załóżmy, że wymagania aplikacji jest maksymalnie 4000 IOPS. Można to osiągnąć, należy udostępnić P30 dysku na maszynie Wirtualnej DS1. Dysk P30 można dostarczyć do 5000 IOPS. Jednak DS1 maszyny Wirtualnej jest ograniczona do 3,200 IOPS. W rezultacie wydajność aplikacji będzie ograniczone przez limit maszyny Wirtualnej na 3,200 IOPS i będzie pogorszenie wydajności. Aby zapobiec tej sytuacji, należy wybrać rozmiar maszyny Wirtualnej i dysku, żeby zarówno spełnia wymagania dotyczące aplikacji.

*Koszt operacji*  
W wielu przypadkach istnieje możliwość, że Twoje całkowity koszt operację przy użyciu magazyn w warstwie Premium jest niższa niż przy użyciu magazynu w warstwie standardowa.

Rozważmy na przykład aplikacji wymagających 16 000 IOPS. Aby osiągnąć ten wydajności, konieczne będzie Standard\_D14 IaaS maszyny Wirtualnej platformy Azure, której można nadać maksymalną liczbę IOPS 16000 przy użyciu 32 dyskach 1 TB magazynu w warstwie standardowa. Każdy dysk magazynu w warstwie standardowa 1TB, można osiągnąć maksymalnie 500 IOPS. Szacowany koszt tej maszyny Wirtualnej na miesiąc będzie 1,570 $. Miesięczny koszt 32 dyski magazynu w warstwie standardowa będzie 1,638 $. Szacowany całkowity miesięczny koszt będzie 3,208 $.

Jednak jeśli ta sama aplikacja na magazyn w warstwie Premium jest hostowany, konieczne będzie mniejszego rozmiaru maszyny Wirtualnej i mniej dyski magazynu premium, co zmniejsza całkowity koszt. Standard\_DS13 maszyny Wirtualnej można wymaganie 16 000 IOPS za pomocą czterech P30 dysków. Maszyna wirtualna DS13 ma maksymalną liczbę IOPS 25,600 i każdego dysku P30 jest maksymalna liczba IOPS 5000. Ogólne, ta konfiguracja może osiągnąć 5000 x 4 = 20 000 IOPS. Szacowany koszt tej maszyny Wirtualnej na miesiąc będzie 1,003 $. Miesięczny koszt cztery dyski magazynu premium P30 będzie 544.34 $. Szacowany całkowity miesięczny koszt będzie 1,544 $.

W poniższej tabeli przedstawiono podziału kosztów tego scenariusza Standard i Premium Storage.

| &nbsp; | **Standardowa** | **Premium** |
| --- | --- | --- |
| **Koszt maszyny wirtualnej na miesiąc** |1,570.58 $ (standardowa\_D14) |1,003.66 $ (standardowa\_DS13) |
| **Koszt dysków na miesiąc** |$1,638.40 (dyski 32 x 1 TB) |$544.34 (4 dyski x P30) |
| **Całkowity koszt na miesiąc** |$3,208.98 |$1,544.34 |

*Dystrybucjach systemu Linux*  

Usługa Azure Premium Storage, możesz uzyskać taki sam poziom wydajności dla maszyn wirtualnych z systemu Windows i Linux. Firma Microsoft obsługuje wiele odmian dystrybucjach systemu Linux, aby zobaczyć pełną listę [tutaj](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Należy pamiętać, że różne dystrybucjach są lepiej dostosowane dla różnych typów obciążeń. Zostanie wyświetlone różne poziomy wydajności w zależności od distro, obciążenie na którym działa. Testowanie dystrybucjach systemu Linux z aplikacją, a następnie wybierz jedną, która najlepiej pasuje.

Podczas uruchamiania systemu Linux z magazyn w warstwie Premium, Sprawdź najnowsze informacje wymagane sterowniki, aby zapewnić wysoką wydajność.

## <a name="premium-storage-disk-sizes"></a>Rozmiary dysków Premium magazynu
Usługa Azure Premium Storage oferuje obecnie siedem rozmiary dysków. Rozmiar każdego dysku ma limit innej skali IOPS, przepustowości i magazynu. Wybierz rozmiar dysku magazynu Premium w zależności od wymagań aplikacji i dużą skalę rozmiaru maszyny Wirtualnej po prawej. W poniższej tabeli przedstawiono rozmiary siedmiu dysków i ich funkcji. Rozmiary P4 i P6 są obecnie obsługiwane tylko w przypadku zarządzania dyskami.

| Typ dysków Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 32 GB | 64 GB | 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB / s  | 100 MB na sekundę | 150 MB na sekundę | 200 MB / s | 250 MB na sekundę | 250 MB na sekundę | 


Jak wiele dysków, możesz wybrać, zależy od dysku rozmiar wybranych. Można użyć jednego dysku P50 lub wielu dysków P10 zgodnie z wymaganiami aplikacji. Należy uwzględnić wymienione poniżej podczas wprowadzania wybór uwagi dotyczące konta.

*Limity skalowania (IOPS i przepustowości)*  
IOPS i przepływności limit rozmiar każdego dysku Premium jest inna i niezależne od limity skalowania maszyny Wirtualnej. Upewnij się, że całkowita liczba IOPS i przepływność z dysków są w ramach limitów skalowania wybrany rozmiar maszyny Wirtualnej.

Na przykład, jeśli wymagania aplikacji jest maksymalnie 250 MB/s przepustowości i DS4 maszyny Wirtualnej za pomocą jednego dysku P30. DS4 maszyny Wirtualnej można przypisać maksymalnie 256 MB/s przepustowości. Jednak jeden dysk P30 ma limit przepustowości 200 MB/s. W rezultacie aplikacji będą ograniczone na 200 MB/s z powodu limitu dysku. Aby rozwiązać ten limit, udostępnić więcej niż jeden dysk danych do maszyny Wirtualnej lub zmień rozmiar dysków P40 lub P50.

> [!NOTE]
> Odczyty obsłużone przez pamięć podręczną nie znajdują się w dysku IOPS i przepływności, dlatego nie podlega limity dysku. Pamięć podręczna ma oddzielne IOPS i przepływność limit dla maszyny Wirtualnej.
>
> Na przykład początkowo Twojej odczyty i zapisy są 60MB/s i 40MB/s odpowiednio. Wraz z upływem czasu pamięci podręcznej warms i służy coraz większą odczytami z pamięci podręcznej. Następnie można uzyskać zapisu wyższej przepustowości z dysku.
>
>

*Liczba dysków*  
Określ liczbę dysków, które mają być przez proces oceny wymagań aplikacji. Rozmiar każdej maszyny Wirtualnej ma również limit na liczbę dysków, które można załączyć do maszyny Wirtualnej. Zazwyczaj jest to dwa razy liczba rdzeni. Upewnij się, że wybrany rozmiar maszyny Wirtualnej może obsługiwać liczbę dysków potrzebne.

Należy pamiętać, że dyski magazyn w warstwie Premium mają wyższe możliwości wydajność w porównaniu do magazynu w warstwie standardowa dysków. W związku z tym w przypadku migracji aplikacji z maszyny Wirtualnej IaaS platformy Azure przy użyciu standardowego magazynu do magazynu w warstwie Premium, prawdopodobnie konieczne będzie mniej dysków premium do osiągnięcia tej samej lub wyższej wydajności aplikacji.

## <a name="disk-caching"></a>Buforowanie dysku
Wysoka maszyn wirtualnych skali, korzystających z usługi Azure Premium Storage jest wielowarstwowych technologii buforowania, nazywany BlobCache. BlobCache korzysta z kombinacji pamięci RAM maszyny wirtualnej i lokalny dysk SSD dla buforowania. Ta pamięć podręczna jest dostępny na dyski stałe magazyn w warstwie Premium i dyski lokalne maszyny Wirtualnej. Domyślnie to ustawienie pamięci podręcznej ma wartość do odczytu/zapisu dla dysków systemu operacyjnego i tylko do odczytu dla dysków z danymi hostowanych na magazyn w warstwie Premium. Z dysku Buforowanie włączone na dyskach magazyn w warstwie Premium, wysokiej skali maszyn wirtualnych można osiągnąć bardzo wysoki poziom wydajności, które przekraczają podstawowej wydajności dysku.

> [!WARNING]
> Zmiany ustawień pamięci podręcznej dysku Azure odłącza i ponownie dołącza dysk docelowy. Jeśli dysk systemu operacyjnego, maszyna wirtualna zostanie ponownie. Zatrzymaj wszystkie aplikacje/usługi, które mogą wpłynąć na tym przerw w działaniu przed zmianą ustawienie dyskowej pamięci podręcznej.
>
>

Aby dowiedzieć się więcej na temat działania BlobCache, należy zapoznać się z wewnątrz [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) wpis w blogu.

Należy włączyć pamięć podręczną dla prawidłowego zestawu dysków. Określa, czy należy włączyć buforowanie dysku na dysku premium lub nie będzie zależeć od wzorzec obciążenia tego dysku będą obsługi. W poniższej tabeli przedstawiono domyślne ustawienia pamięci podręcznej dla systemu operacyjnego i dysków z danymi.

| **Typ dysku** | **Domyślne ustawienie pamięci podręcznej** |
| --- | --- |
| Dysk systemu operacyjnego |ReadWrite |
| Dysk z danymi |Tylko do odczytu |

Poniżej przedstawiono ustawienia pamięci podręcznej dysku zalecanych dla dysków z danymi

| **Ustawienia buforowania na dysku** | **Zalecenie na użycie tego ustawienia** |
| --- | --- |
| Brak |Konfigurowanie hosta pamięci podręcznej None tylko do zapisu i intensywnie zapisu dysków. |
| Tylko do odczytu |Skonfiguruj pamięci podręcznej hosta jako tylko do odczytu dla dysków tylko do odczytu i zapisu i odczytu. |
| ReadWrite |Konfigurowanie pamięci podręcznej hosta jako ReadWrite tylko wtedy, gdy aplikacja poprawnie obsługuje zapisywania danych z pamięci podręcznej na stałe dyski w razie potrzeby. |

*Tylko do odczytu*  
Konfigurując buforowanie danych magazyn w warstwie Premium dyski tylko do odczytu, możesz osiągnąć Niskie opóźnienie odczytu i uzyskać bardzo duże IOPS odczytu i przepływność aplikacji. Jest to powodu dwóch przyczyn

1. Odczyty wykonywane z pamięci podręcznej, która znajduje się na lokalny dysk SSD i ilość pamięci maszyny Wirtualnej, jest znacznie szybsze niż Odczyty dysku danych, który znajduje się w magazynie obiektów blob Azure.  
2. Magazyn w warstwie Premium nie będą uwzględniane odczyty z pamięci podręcznej na dysku IOPS i przepustowość. W związku z tym aplikacja jest możliwość osiągnięcia wyższej IOPS całkowitej i przepustowość.

*ReadWrite*  
Domyślnie dysków systemu operacyjnego mają włączone buforowanie odczytu i zapisu. Niedawno dodano obsługę ReadWrite buforowanie na danych, jak również dysków. Jeśli używasz ReadWrite buforowanie musi mieć odpowiednie sposób zapisywania danych z pamięci podręcznej trwałe dysków. Na przykład SQL Server będzie obsługiwać zapisywania danych z pamięci podręcznej dyski magazynu trwałego samodzielnie. Z aplikacji, która nie obsługuje przechowywanie wymagane dane przy użyciu pamięci podręcznej odczytu i zapisu może prowadzić do utraty danych, jeśli wystąpiła awaria maszyny Wirtualnej.

Na przykład do programu SQL Server uruchomionego na magazyn w warstwie Premium, wykonując następujące czynności, można zastosować te wskazówki

1. Konfigurowanie pamięci podręcznej "ReadOnly" premium magazynu dysków zawierających pliki danych.  
   a.  Szybka odczytuje z pamięci podręcznej niższe czas zapytania programu SQL Server, ponieważ dane strony są pobierane z pamięci podręcznej w znacznie szybciej porównaniu bezpośrednio danych z dysków.  
   b.  Obsługująca odczyty z pamięci podręcznej, oznacza, że jest dostępny z dysków danych w warstwie premium dodatkowej przepływności. SQL Server można korzystać tego dodatkowe przepływności kierunku pobieranie więcej danych stron i inne operacje, takie jak Kopia zapasowa i przywracanie, partii obciążeń i spowoduje odbudowanie indeksu.  
2. Konfigurowanie pamięci podręcznej "None" premium magazynu dysków zawierających pliki dziennika.  
   a.  Pliki dziennika mają głównie operacje zapisu ciężki. W związku z tym że nie korzystają z pamięci podręcznej tylko do odczytu.

## <a name="disk-striping"></a>Rozkładanie
Gdy wysoką skalowalność, której maszyna wirtualna jest dołączona z kilku magazynu trwałego dysków w warstwie premium, dyski można ze sobą rozkładane agregacji ich IOPs, przepustowości i pojemności magazynu.

W systemie Windows można miejsca do magazynowania dysków stripe razem. Należy skonfigurować jedną kolumnę dla każdego dysku w puli. W przeciwnym razie ogólną wydajność woluminów rozłożonych może być niższe niż oczekiwano z powodu nierówna Dystrybucja ruchu między dyskami.

Ważne: Przy użyciu interfejsu użytkownika Menedżera serwera, można ustawić łączna liczba kolumn maksymalnie 8 dla woluminy rozłożone. Podczas podłączania więcej niż 8 dysków, należy utworzyć wolumin za pomocą programu PowerShell. Przy użyciu programu PowerShell, można ustawić liczbę kolumn równa liczbie dysków. Na przykład, jeśli istnieją 16 dysków w jednej rozłożony; Określ 16 kolumn w *NumberOfColumns* parametr *New-VirtualDisk* polecenia cmdlet programu PowerShell.

W systemie Linux Użyj narzędzia MDADM do dysków stripe razem. Aby uzyskać szczegółowy opis kroków na dyskach zastosowanie rozkładania na systemie Linux, zobacz [skonfigurować RAID oprogramowania w systemie Linux](../articles/virtual-machines/linux/configure-raid.md).

*Rozmiar usługi STRIPE*  
Ważne konfiguracji w rozkładanie jest rozmiar przeplotu. Rozmiar przeplotu lub rozmiar bloku jest najmniejsza fragmentów danych aplikacji można rozwiązać na woluminy rozłożone. Rozmiar stripe skonfigurowanych zależy od typu aplikacji i jej wzorzec żądania. Jeśli wybierzesz stripe niewłaściwy rozmiar, może spowodować niespójność we/wy, co prowadzi do pogorszenie wydajności aplikacji.

Na przykład jeśli żądanie We/Wy wygenerowanych przez aplikację jest większy niż rozmiar stripe dysku, czy system magazynowania zapisuje go w granicach jednostki usługi stripe na więcej niż jeden dysk. Gdy nadejdzie czas na dostęp do danych, jej do wyszukania w więcej niż jednej jednostki usługi stripe do wykonania żądania. Skrócenie takie zachowanie może prowadzić do pogorszenia wydajności znacznej. Z drugiej strony Jeśli rozmiar żądania We/Wy jest mniejszy niż rozmiar przeplotu i jest charakter losowy, żądań We/Wy mogą sumują się na tym samym dysku, które są przyczyną wąskiego gardła i ostatecznie zmniejszenie wydajności we/wy.

W zależności od typu obciążenia aplikacja jest uruchomiona, wybierz odpowiednie stripe rozmiar. Losowe małych żądań We/Wy Użyj mniejszej stripe. Dla dużych sekwencyjnych operacji We/Wy żądań stosować większy rozmiar przeplotu. Dowiedz się, stripe rozmiar zaleceń dotyczących aplikacji się, że będzie działać na magazyn w warstwie Premium. Dla programu SQL Server należy skonfigurować usługi stripe rozmiarze 64KB dla obciążeń OLTP i 256KB dla obciążeń magazynowania danych. Zobacz [wydajności najlepsze rozwiązania dotyczące programu SQL Server na maszynach wirtualnych Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) Aby dowiedzieć się więcej.

> [!NOTE]
> Jednocześnie można możesz paskowych maksymalnie 32 dyski magazynu premium na serii DS maszyny Wirtualnej i 64 dyski magazynu premium na serię GS maszyny Wirtualnej.
>
>

## <a name="multi-threading"></a>Wielowątkowości
Azure zostało zaprojektowane platformy magazyn w warstwie Premium jako równoległemu. W związku z tym aplikacji wielowątkowych osiąga o wiele większą wydajność niż jednowątkowe aplikacji. Aplikacji wielowątkowych podzieli zadań przez wiele wątków i zwiększa efektywność działania przy użyciu zasobów maszyny Wirtualnej i dysku do maksymalnej.

Na przykład jeśli aplikacja jest uruchomiona na jednym rdzeniu maszyny Wirtualnej przy użyciu dwóch wątków, Procesor można przełączać się między dwoma wątkami do osiągnięcia wydajności. Gdy jeden wątek oczekuje na dysku we/wy, aby zakończyć, procesor CPU przełączyć się do innego wątku. W ten sposób dwoma wątkami można wykonywać więcej niż jednego wątku czy. Jeśli maszyna wirtualna ma więcej niż jednego rdzenia, dalsze zmniejsza czas działania, ponieważ każdy core mogą wykonywać zadania równolegle.

Nie można zmienić sposób standardowych aplikacji implementuje pojedynczego wątku lub wielowątkowości. Na przykład SQL Server jest może obsługiwać wielu procesorów i procesorami wielordzeniowymi. Jednak program SQL Server decyduje, pod jakimi warunkami będzie korzystać co najmniej jeden wątek przetwarzania zapytania. Można go uruchamiać zapytania i tworzą indeksy, używając wielowątkowości. Dla zapytania, które obejmuje przyłączanie dużych tabel i sortowanie danych przed powrotem do użytkownika wiele wątków prawdopodobnie będzie używany przez serwer SQL. Jednak użytkownik nie może kontrolować, czy program SQL Server wykonuje zapytanie za pomocą pojedynczego wątku lub wiele wątków.

Brak ustawienia konfiguracji, które można zmienić w celu wywierania wpływu to wielowątkowości lub równoległego przetwarzania aplikacji. Na przykład w przypadku programu SQL Server jest maksymalny stopień równoległości konfiguracji. To ustawienie o nazwie MAXDOP, pozwala skonfigurować maksymalną liczbę procesorów, które program SQL Server można użyć podczas równoległego przetwarzania. MAXDOP można skonfigurować dla poszczególnych zapytań lub operacji dotyczących indeksu. Jest to korzystne, gdy chcesz saldo zasoby systemu dla aplikacji krytyczne wydajności.

Na przykład załóżmy, że w aplikacji przy użyciu programu SQL Server jest wykonywany dużych zapytań i operacji indeksu w tym samym czasie. Załóżmy, chcę teraz operacji indeksowania za więcej wydajność w porównaniu do dużych zapytania. W takim przypadku można ustawić wartości MAXDOP operacja indeksu będzie większa niż wartość MAXDOP dla zapytania. Dzięki temu serwer SQL ma więcej liczbę procesorów, które można wykorzystać dla operacji indeksowania w porównaniu do liczbę procesorów, które można przypisać do dużych zapytania. Należy pamiętać, że nie Dostosuj liczbę wątków używanych programu SQL Server dla każdej operacji. Można kontrolować maksymalną liczbę procesorów jest przeznaczony dla wielowątkowości.

Dowiedz się więcej o [stopni równoległości](https://technet.microsoft.com/library/ms188611.aspx) w programie SQL Server. Dowiedz się takie ustawienia wpływające na wielowątkowości w aplikacji i ich konfiguracji w celu optymalizacji wydajności.

## <a name="queue-depth"></a>Głębokość kolejki
Głębokość kolejki lub długość kolejki lub rozmiar kolejki jest liczba oczekujących żądań We/Wy w systemie. Wartość głębokość kolejki określa, ile operacji We/Wy aplikacji można wyrównać, który będzie przetwarzania dyski magazynu. Ma wpływ na wszystkie wskaźników wydajności aplikacji trzy wspomniano w tym tj artykułu IOPS, przepustowości i opóźnień.

Kolejka głębokość i wielowątkowości są ściśle powiązane. Głębokość kolejki wskazuje, ile wielowątkowości może zostać osiągnięty przez aplikację. W przypadku dużych głębokości kolejki, aplikacja może zostać uruchomiony więcej operacji jednocześnie, innymi słowy, więcej wielowątkowości. Jeśli głębokość kolejki jest mały, nawet jeśli aplikacja jest wielowątkowych, nie będzie wystarczającej liczby żądań wyrównana równoczesne wykonywanie.

Zazwyczaj poza półki aplikacje nie zezwalają na zmianę głębokości kolejki, ponieważ jeśli ustawione niepoprawnie czy obrażeniami więcej niż dobra. Aplikacje zostaną wartość prawej głębokości kolejki, aby uzyskać optymalną wydajność. Jednak ważne jest zrozumienie to pojęcie, dzięki czemu można rozwiązywać problemy z wydajnością z aplikacją. Można również obserwować skutków głębokości kolejki, uruchamiając najlepszymi narzędzi w systemie.

Niektóre aplikacje Podaj ustawienia w celu wywierania wpływu głębokość kolejki. Na przykład ustawienie MAXDOP (maksymalny stopień równoległości) w programie SQL Server szczegółowo opisane w poprzedniej sekcji. MAXDOP to sposób wpływać głębokość kolejki i wielowątkowości, chociaż nie zmienia bezpośrednio głębokość kolejki programu SQL Server.

*Głębokość kolejki wysoka*  
Głębokość kolejki wysokiej linii więcej operacji na dysku. Dysk wie, przy następnym żądaniu w swojej kolejki wcześniejsze. W związku z tym dysk można zaplanować operacji wcześniejsze i przetwarzanie ich w optymalny sekwencji. Ponieważ aplikacja wysyła więcej żądań na dysku, dysku może przetwarzać więcej równolegle z systemem IOs. Ostatecznie aplikacja będzie można osiągnąć wyższy IOPS. Ponieważ aplikacja jest przetwarzanie żądań więcej, zwiększa się również całkowitą przepływność aplikacji

Zazwyczaj aplikacji można uzyskać maksymalną przepływność z 8-16 oczekujących operacji We-Wy na dysku dołączonym. Jeśli głębokości kolejki, aplikacja nie jest wypychanie za mało systemu IOs do systemu, a przetworzy mniejszej ilości w danym okresie. Innymi słowy mniejszej przepustowości.

Na przykład w programie SQL Server, ustawiając wartość MAXDOP dla zapytania "4" informuje programu SQL Server czy można użyć maksymalnie czterech rdzeni do wykonania zapytania. Programu SQL Server określa, co jest najlepszym głębokość kolejki i liczba rdzeni w celu wykonywania zapytań.

*Optymalną głębokość kolejki*  
Głębokość kolejki bardzo duże ma również jego wady. Głębokość kolejki jest zbyt wysoka, aplikacja będzie próbować dysków bardzo wysoka liczba IOPS. Jeśli aplikacja ma trwały dysków o wystarczające elastycznie IOPS, to negatywnie wpłynąć na opóźnienia aplikacji. Następującej formuły przedstawiono relacje między IOPS, opóźnienia i głębokość kolejki.  
    ![](media/premium-storage-performance/image6.png)

Nie należy konfigurować głębokość kolejki wysokiej wartości, ale do optymalnego wartość, która może zapewnić wystarczającą ilość IOPS dla aplikacji bez wpływu na opóźnienia. Na przykład, jeśli opóźnienie aplikacji musi być 1 milisekund, wymaganego do osiągnięcia 5000 IOPS głębokość kolejki jest, QD = 5000 x 0,001 = 5.

*Głębokość kolejki dla woluminów rozłożonych*  
Dla woluminu rozłożonego Obsługa głębokości kolejki wystarczająco wysoka, tak, aby każdy dysk miała głębokość kolejki szczytu pojedynczo. Rozważmy na przykład aplikacja, który wypycha głębokości kolejki wynoszącej 2 i istnieją 4 dyski w paskowego. Dwa żądania We/Wy nastąpi przejście do dwóch dysków i pozostałych dwóch dysków będzie bezczynności. W związku z tym skonfiguruj głębokość kolejki w taki sposób, że wszystkie dyski może być zajęta. Formuła poniżej pokazano, jak określić głębokości kolejki wynoszącej woluminy rozłożone.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Ograniczanie przepływności
Azure Premium Storage przepisy określona liczba IOPS i Przepływność w zależności od rozmiarów maszyn wirtualnych i rozmiary dysków, którą wybierzesz. W dowolnym momencie aplikacja próbuje dysku IOPS lub przepływności powyżej tych limitów co maszyna wirtualna lub dysk może obsłużyć, będzie ograniczane przez Magazyn w warstwie Premium. To manifesty w formie pogorszenie wydajności w aplikacji. To oznacza większego opóźnienia, zmniejszenia przepustowości lub zmniejszyć liczbę IOPS. Jeśli magazyn w warstwie Premium nie ograniczenie przepustowości, aplikacji całkowicie może zakończyć się niepowodzeniem przekroczenia co jej zasoby są w stanie realizacji. Tak aby uniknąć problemów z wydajnością z powodu dławienia, zawsze dostarczać wystarczających zasobów dla aplikacji. Wziąć pod uwagę, omówiono w sekcjach rozmiary dysku powyżej i rozmiarów maszyn wirtualnych. Przeprowadzenia testów porównawczych, to najlepszy sposób, aby dowiedzieć się, jakie zasoby potrzebne do obsługi aplikacji.

## <a name="benchmarking"></a>Benchmarking
Przeprowadzenia testów porównawczych, to proces symulowanie różnych obciążeń w swojej aplikacji i pomiaru wydajności aplikacji dla poszczególnych obciążeń. Korzystając z procedury opisanej w sekcji wcześniej, po zebraniu wymagania dotyczące wydajności aplikacji. Przez uruchomienie narzędzia najlepszymi na maszynach wirtualnych hosting aplikacji, należy określić, że poziomy wydajności, które aplikacji można uzyskać z magazyn w warstwie Premium. W tej sekcji możemy umożliwiają przykłady przeprowadzenia testów porównawczych standardowa maszyna VM DS14 udostępniane z dysków Azure Premium Storage.

Odpowiednio użyliśmy najlepszymi narzędziom Iometer i FIO, systemu Windows i Linux. Te narzędzia zduplikować wiele wątków symulując produkcji, takich jak obciążenie i mierzyć wydajność systemu. Za pomocą narzędzi można również skonfigurować parametry, takie jak głębokość bloku rozmiar i kolejki, które normalnie nie można zmienić dla aplikacji. Zapewnia większą elastyczność i możliwość dysków maksymalnej wydajności na dużą skalę udostępniane z dysków w warstwie premium dla różnych typów aplikacji obciążeń maszyny Wirtualnej. Aby dowiedzieć się więcej na temat każdego z najlepszymi narzędzi można znaleźć w [Iometer](http://www.iometer.org/) i [FIO](http://freecode.com/projects/fio).

Do wykonania poniższych przykładach, utworzyć Maszynę wirtualną DS14 standardowe i Dołącz 11 dyski Premium Storage do maszyny Wirtualnej. 11 dysków skonfiguruj 10 dysków z buforowania jako "None" na hoście, a paskowych je na wolumin o nazwie NoCacheWrites. Konfigurowanie buforowania jako "ReadOnly" na pozostałych dysku hosta i Utwórz wolumin o nazwie CacheReads z tego dysku. Przy użyciu tej konfiguracji, można wyświetlić maksymalną wydajność odczytu i zapisu z standardowe DS14 maszyny Wirtualnej. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia maszyny Wirtualnej DS14 z dysków w warstwie premium, przejdź do [tworzenie i używanie konta magazynu Premium dla dysku danych maszyny wirtualnej](../articles/virtual-machines/windows/premium-storage.md).

*Wstępne wypełnianie pamięci podręcznej*  
Dysk z buforowania na hoście tylko do odczytu będzie można nadać IOPS wyższe niż limit na dysku. Aby uzyskać ten maksymalną wydajność odczytu z pamięci podręcznej hosta, najpierw musisz musi rozgrzewki pamięci podręcznej dysku. Dzięki temu, że IOs odczytu najlepszymi narzędzie, które będą miały na woluminie CacheReads faktycznie trafienia pamięci podręcznej, a nie dysków bezpośrednio. Wynik trafień w pamięci podręcznej w dodatkowe IOPS z pamięci podręcznej pojedynczego włączone dysku.

> **Ważne:**  
> Przed uruchomieniem przeprowadzenia testów porównawczych, za każdym razem, gdy ponownego uruchomienia maszyny Wirtualnej musi rozgrzewki pamięci podręcznej.
>
>

#### <a name="iometer"></a>Iometer
[Pobierz narzędzie Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na maszynie Wirtualnej.

*Plik testowy*  
Iometer używa plik testu, który jest przechowywana w woluminie, na którym będzie uruchomiona najlepszymi testu. Go dyski odczytuje i zapisuje w tym pliku testowego do mierzenia dysku IOPS i przepustowość. Iometer tworzy ten plik testu, jeśli nie podano jeden. Utwórz plik testu 200GB o nazwie iobw.tst na woluminach CacheReads i NoCacheWrites.

*Specyfikacje dostępu*  
Specyfikacje żądań We/Wy rozmiar % odczytu/zapisu, % losowe/sekwencyjne są skonfigurowane na karcie "Specyfikacja programu Access" w Iometer. Specyfikacja dostępu tworzone dla poszczególnych scenariuszy opisanych poniżej. Tworzenie specyfikacji dostępu i przycisk Zapisz, odpowiedni nazywanie jak — RandomWrites\_8 kilobajtów RandomReads\_8 kilobajtów. Wybierz odpowiedni specyfikacji podczas uruchamiania testu scenariusza.

Przykład dostępu specyfikacji maksymalne IOPS zapisu scenariusz przedstawiono poniżej,  
    ![](media/premium-storage-performance/image8.png)

*Maksymalna liczba IOPS specyfikacje testu*  
Aby zaprezentować maksymalna liczba IOPs, należy użyć mniejszego rozmiaru żądania. Użyj rozmiar żądania 8 KB i Utwórz specyfikacje zapisów losowych i odczytów.

| Specyfikacja dostępu | Rozmiar żądania | Losowe % | % Odczytu |
| --- | --- | --- | --- |
| RandomWrites\_8 kilobajtów |8 KB |100 |0 |
| RandomReads\_8 kilobajtów |8 KB |100 |100 |

*Maksymalna przepustowość specyfikacje testu*  
Aby zademonstrować maksymalną przepustowość, użyj większy rozmiar żądania. Użyj 64 KB rozmiaru żądania i Utwórz specyfikacje zapisów losowych i odczytów.

| Specyfikacja dostępu | Rozmiar żądania | Losowe % | % Odczytu |
| --- | --- | --- | --- |
| RandomWrites\_64 KB |64K |100 |0 |
| RandomReads\_64 KB |64K |100 |100 |

*Uruchamianie testu Iometer*  
Wykonaj poniższe kroki, aby rozgrzewki pamięci podręcznej

1. Utwórz dwa specyfikacje dostępu z wartościami pokazano poniżej,

   | Name (Nazwa) | Rozmiar żądania | Losowe % | % Odczytu |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
2. Uruchom test Iometer inicjowania dysku pamięci podręcznej z następującymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki wynoszącej 128. Ustaw czas trwania testu "Czas wykonywania" 2hrs na karcie "Testowania instalacji".

   | Scenariusz | Wolumin docelowy | Name (Nazwa) | Czas trwania |
   | --- | --- | --- | --- |
   | Inicjowanie dysku pamięci podręcznej |CacheReads |RandomWrites\_1 MB |2hrs |
3. Uruchom test Iometer rozgrzewania dysku pamięci podręcznej z następującymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki wynoszącej 128. Ustaw czas trwania testu "Czas wykonywania" 2hrs na karcie "Testowania instalacji".

   | Scenariusz | Wolumin docelowy | Name (Nazwa) | Czas trwania |
   | --- | --- | --- | --- |
   | Ciepłych dysk pamięci podręcznej |CacheReads |RandomReads\_1 MB |2hrs |

Po pamięci podręcznej dysku jest przygotowaniu miejsca, przejdź do scenariuszy testowania wymienionych poniżej. Aby uruchomić Iometer test, należy użyć co najmniej trzech wątków roboczych dla **każdego** woluminu docelowego. Dla każdego wątku roboczego wybierz wolumin docelowy, Ustawia głębokość kolejki, a następnie wybierz jedno specyfikacji zapisane testu, jak pokazano w poniższej tabeli, do uruchamiania odpowiednich scenariusza testu. W tabeli przedstawiono również oczekiwanych rezultatów IOPS i przepływności podczas uruchamiania tych testów. W przypadku wszystkich scenariuszy mały rozmiar we/wy 8 KB i głębokości kolejki wysoki 128 jest używany.

| Scenariusz testów | Wolumin docelowy | Name (Nazwa) | Wynik |
| --- | --- | --- | --- |
| Maksymalnie z IOPS odczytu |CacheReads |RandomWrites\_8 kilobajtów |50 000 IOPS |
| Maksymalnie z Zapis IOPS |NoCacheWrites |RandomReads\_8 kilobajtów |IOPS 64 000 |
| Maksymalnie z Łączna liczba IOPS. |CacheReads |RandomWrites\_8 kilobajtów |100 000 IOPS |
| NoCacheWrites |RandomReads\_8 kilobajtów | &nbsp; | &nbsp; |
| Maksymalnie z Odczyt MB/s |CacheReads |RandomWrites\_64 KB |524 MB/s |
| Maksymalnie z Zapisane MB/s |NoCacheWrites |RandomReads\_64 KB |524 MB/s |
| Łączna MB/s |CacheReads |RandomWrites\_64 KB |1000 MB/s |
| NoCacheWrites |RandomReads\_64 KB | &nbsp; | &nbsp; |

Poniżej znajdują się zrzuty ekranu Iometer wyniki testu dla połączonych scenariuszy IOPS i przepływności.

*Łączna odczyty i zapisy maksymalna liczba IOPS*  
![](media/premium-storage-performance/image9.png)

*Łączna odczyty i zapisy maksymalną przepustowość*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO jest popularnych narzędzi do magazynu testów porównawczych na maszynach wirtualnych systemu Linux. Ma elastyczność, aby wybrać różne rozmiary we/wy, sekwencyjnych lub losowych odczytuje i zapisuje. Go spowoduje utworzenie wątków roboczych lub procesów do wykonywania określonej operacji We/Wy. Można określić typ operacji We/Wy każdego wątku roboczego należy wykonać przy użyciu plików zadania. Utworzono jeden plik zadania na scenariuszu przedstawiono w poniższych przykładach. Specyfikacje w tych plikach zadania do testu wydajności różnych obciążeń uruchomionych na magazyn w warstwie Premium, można zmienić. W przykładach użyto uruchomiona standardowa maszyna VM 14 DS **Ubuntu**. Użyj tej samej konfiguracji opisanych na początku [przeprowadzenia testów porównawczych sekcji](#Benchmarking) i ciepłych zapasowej pamięci podręcznej przed uruchomieniem testów porównawczych.

Przed rozpoczęciem [Pobierz FIO](https://github.com/axboe/fio) i zainstalować ją na maszynie wirtualnej.

Uruchom następujące polecenie dla Ubuntu,

```
apt-get install fio
```

Używamy cztery wątków roboczych dla wspierania operacje zapisu i cztery wątków roboczych dla kierowania operacji odczytu na dyskach. Pracownicy zapisu będzie kierowania ruchu na wolumin "nocache", który zawiera dyski 10 z pamięci podręcznej na wartość "None". Pracownicy odczytu będzie kierowania ruchu na woluminie "readcache", którego dysk 1 z pamięci podręcznej ustawioną na "ReadOnly".

*Zapis maksymalna liczba IOPS*  
Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę IOPS zapisu. Nazwa "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Należy pamiętać, wykonaj klucza elementy zgodnie z wytycznymi projektowania omówione w poprzednich sekcjach. W tych specyfikacjach są niezbędne do kierowania maksymalna liczba IOPS,  

* Głębokości kolejki wysokiej 256.  
* Mała blok o rozmiarze 8KB.  
* Wiele wątków wykonywania losowe zapisy.

Uruchom następujące polecenie, aby rozpocząć poza testu FIO 30 sekund  

```
sudo fio --runtime 30 fiowrite.ini
```

Podczas wykonywania testu, można zobaczyć liczbę zapisu IOPS maszyny Wirtualnej i dysków w warstwie Premium są dostarczane. Jak pokazano w poniższym przykładzie, DS14 maszyny Wirtualnej jest dostarczanie jego zapisu maksymalnego limitu IOPS 50 000 iops.  
    ![](media/premium-storage-performance/image11.png)

*Odczyt maksymalna liczba IOPS*  
Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę IOPS odczytu. Nazwa "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Należy pamiętać, wykonaj klucza elementy zgodnie z wytycznymi projektowania omówione w poprzednich sekcjach. W tych specyfikacjach są niezbędne do kierowania maksymalna liczba IOPS,

* Głębokości kolejki wysokiej 256.  
* Mała blok o rozmiarze 8KB.  
* Wiele wątków wykonywania losowe zapisy.

Uruchom następujące polecenie, aby rozpocząć poza testu FIO 30 sekund

```
sudo fio --runtime 30 fioread.ini
```

Podczas wykonywania testu będzie widoczna liczba odczytanych IOPS maszyny Wirtualnej i są dostarczane dysków Premium. Jak pokazano w poniższym przykładzie, DS14 maszyny Wirtualnej jest dostarczanie więcej niż 64 000 IOPS odczytu. To jest kombinacją dysku i wydajność pamięci podręcznej.  
    ![](media/premium-storage-performance/image12.png)

*Maksymalna odczytu i zapisu IOPS*  
Utwórz plik zadania z następujących specyfikacji, aby uzyskać maksymalną łączyć odczytu i zapisu IOPS. Nazwa "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Należy pamiętać, wykonaj klucza elementy zgodnie z wytycznymi projektowania omówione w poprzednich sekcjach. W tych specyfikacjach są niezbędne do kierowania maksymalna liczba IOPS,

* Głębokość kolejki wysoki 128.  
* Mała blok o rozmiarze 4KB.  
* Wiele wątków wykonywania losowe odczytuje i zapisuje.

Uruchom następujące polecenie, aby rozpocząć poza testu FIO 30 sekund

```
sudo fio --runtime 30 fioreadwrite.ini
```

Podczas wykonywania testu będzie mógł wyświetlić liczbę połączonych odczytu i zapisu IOPS maszyny Wirtualnej, a dyski w warstwie Premium są dostarczane. Jak pokazano w poniższym przykładzie, DS14 maszyny Wirtualnej jest dostarczanie więcej niż 100 000 Scalonej odczytu i zapisu IOPS. To jest kombinacją dysku i wydajność pamięci podręcznej.  
    ![](media/premium-storage-performance/image13.png)

*Maksymalny łączny przepływności*  
Aby uzyskać maksymalną połączyć odczytu i zapisu przepływności, korzystania z wielu wątków wykonywania odczyty i zapisy większy rozmiar bloku i dużych głębokości. Można użyć blok o rozmiarze 64KB i głębokości kolejki wynoszącej 128.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o usłudze Azure Premium Storage:

* [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure](../articles/virtual-machines/windows/premium-storage.md)  

Dla użytkowników programu SQL Server zapoznaj się z artykułami na najlepsze rozwiązania w zakresie wydajności programu SQL Server:

* [Wydajność najlepsze rozwiązania dotyczące programu SQL Server na maszynach wirtualnych Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Usługa Azure Premium Storage oferuje najwyższej wydajności programu SQL Server w maszynie Wirtualnej platformy Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
