---
title: Narzędzia do wydajności systemu Linux
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak rozwiązywać typowe problemy i rozwiązywać je podczas korzystania z usługi Azure Kubernetes Service (AKS)
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925607"
---
# <a name="linux-performance-troubleshooting"></a>Rozwiązywanie problemów z wydajnością systemu Linux

Wyczerpanie zasobów na komputerach z systemem Linux jest częstym problemem i może objawiać się za pomocą wielu różnych objawów. Ten dokument zawiera ogólny przegląd dostępnych narzędzi ułatwiających diagnozowanie takich problemów.

Wiele z tych narzędzi akceptuje interwał, w którym można produkować dane wyjściowe. Ten format wyjściowy zazwyczaj znacznie ułatwia wykrywanie wzorców. Jeśli zostanie zaakceptowana, przykładowe `[interval]`wywołanie będzie zawierać .

Wiele z tych narzędzi ma bogatą historię i szeroki zestaw opcji konfiguracyjnych. Ta strona zawiera tylko prosty podzbiór wywołań, aby wyróżnić typowe problemy. Kanoniczne źródło informacji jest zawsze dokumentacją referencyjną dla każdego konkretnego narzędzia. Dokumentacja ta będzie znacznie dokładniejsza niż to, co jest tutaj zawarte.

## <a name="guidance"></a>Wskazówki

Bądź systematyczny w podejściu do badania problemów z wydajnością. Dwa typowe podejścia to USE (wykorzystanie, nasycenie, błędy) i RED (poziom, błędy, czas trwania). RED jest zwykle używany w kontekście usług monitorowania opartego na żądaniach. USE jest zwykle używany do monitorowania zasobów: dla każdego zasobu na komputerze, wykorzystanie monitora, nasycenie i błędy. Cztery główne rodzaje zasobów na dowolnym komputerze to procesor, pamięć, dysk i sieć. Wysokie wykorzystanie, nasycenie lub poziom błędu dla któregokolwiek z tych zasobów wskazuje na możliwy problem z systemem. Gdy występuje problem, zbadaj główną przyczynę: dlaczego opóźnienie we/wy dysku jest wysokie? Czy dyski lub jednostka SKU maszyny wirtualnej są ograniczone? Jakie procesy zapisują się na urządzeniach i do jakich plików?

Kilka przykładów typowych problemów i wskaźników do ich zdiagnozowania:
- Ograniczanie we/wy we/wy: użyj iostat do pomiaru liczby we/wy na urządzenie. Upewnij się, że żaden pojedynczy dysk nie przekracza limitu, a suma dla wszystkich dysków jest mniejsza niż limit dla maszyny wirtualnej.
- Ograniczanie przepustowości: użyj iostat jak dla IOPS, ale pomiaru przepływności odczytu/zapisu. Upewnij się, że przepływność na urządzenie i agregacja są poniżej limitów przepustowości.
- Wyczerpanie SNAT: może to objawiać się jako wysokie połączenia aktywne (wychodzące) w SAR. 
- Utrata pakietów: może to być mierzone przez serwer proxy za pośrednictwem licznika retransmisji TCP względem liczby wysłanych/odebranych. Oba `sar` `netstat` i może pokazać te informacje.

## <a name="general"></a>Ogólne

Narzędzia te mają charakter ogólny i obejmują podstawowe informacje o systemie. Są one dobrym punktem wyjścia do dalszych badań.

### <a name="uptime"></a>Uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

zapewnia czas pracy systemu oraz 1, 5 i 15-minutowe średnie obciążenia. Te średnie obciążenia w przybliżeniu odpowiadają wątkom wykonującym pracę lub oczekującym na wykonanie pracy awaryjnej. W absolutnej liczby te mogą być trudne do interpretacji, ale mierzone w czasie mogą nam powiedzieć przydatne informacje:

- 1-minutowa średnia > 5-minutowa średnia oznacza, że obciążenie rośnie.
- 1-minutowa średnia < 5-minutowa średnia oznacza, że obciążenie maleje.

czas pracy bez przestojów może również oświetlić, dlaczego informacje nie są dostępne: problem mógł zostać rozwiązany samodzielnie lub przez ponowne uruchomienie, zanim użytkownik będzie mógł uzyskać dostęp do urządzenia.

Średnie obciążenia wyższe niż liczba dostępnych wątków procesora CPU może wskazywać na problem z wydajnością z danym obciążeniem.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg zrzuca bufor jądra. Zdarzenia, takie jak OOMKill dodać wpis do buforu jądra. Znalezienie OOMKill lub innych komunikatów wyczerpania zasobów w dziennikach dmesg jest silnym wskaźnikiem problemu.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`zapewnia szeroki przegląd bieżącego stanu systemu. Nagłówki zawierają kilka przydatnych informacji zbiorczych:

- stan zadań: bieganie, spanie, zatrzymywane.
- Wykorzystanie procesora CPU, w tym przypadku głównie pokazano czas bezczynny.
- całkowitej, wolnej i używanej pamięci systemowej.

`top`mogą pominąć krótkotrwałe procesy; alternatywnych, `htop` `atop` takich jak i zapewniają podobne interfejsy, jednocześnie naprawiając niektóre z tych niedociągnięć.

## <a name="cpu"></a>Procesor CPU

Narzędzia te dostarczają informacji o wykorzystaniu procesora CPU. Jest to szczególnie przydatne w przypadku wyjścia tocznego, gdzie wzory stają się łatwe do wykrycia.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`drukuje podobne informacje o procesorze do góry, ale w podziale według wątku procesora. Wyświetlanie wszystkich rdzeni jednocześnie może być przydatne do wykrywania wysoce niezrównoważonego użycia procesora CPU, na przykład gdy aplikacja jednowątkowa używa jednego rdzenia przy 100% wykorzystaniu. Ten problem może być trudniejszy do wykrycia po zagregowanym przez wszystkie procesory w systemie.

### <a name="vmstat"></a>vmstat ( vmstat )

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`zawiera podobne `mpstat` `top`informacje i , wyliczając liczbę procesów oczekujących na CPU (r kolumna), statystyki pamięci i procent czasu procesora CPU spędzonego w każdym stanie roboczym.

## <a name="memory"></a>Memory (Pamięć)

Pamięć jest bardzo ważnym i na szczęście łatwym zasobem do śledzenia. Niektóre narzędzia mogą zgłaszać zarówno `vmstat`procesor, jak i pamięć, takie jak . Ale narzędzia, takie jak `free` może nadal być przydatne do szybkiego debugowania.

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`przedstawia podstawowe informacje o całkowitej pamięci, a także używanej i wolnej pamięci. `vmstat`może być bardziej przydatne nawet do analizy pamięci podstawowej ze względu na jego zdolność do zapewnienia wyjścia toczenia.

## <a name="disk"></a>Dysk

Te narzędzia mierzą we/wy na dysku We/Wy, kolejki oczekiwania i całkowitą przepływność. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`zapewnia głęboki wgląd w wykorzystanie dysku. To wywołanie `-x` przechodzi dla rozszerzonych `-y` statystyk, aby pominąć średnie systemu `1 1` drukowania początkowego wydruku od rozruchu i określić chcemy 1-sekundowy interwał, kończący się po jednym bloku danych wyjściowych. 

`iostat`ujawnia wiele przydatnych statystyk:

- `r/s`i `w/s` są odczyty na sekundę i zapisuje na sekundę. Suma tych wartości to We/Wy.
- `rkB/s`i `wkB/s` są kilobajtami odczytu/zapisu na sekundę. Sumą tych wartości jest przepływność.
- `await`to średni czas iowait w milisekundach dla żądań w kolejce.
- `avgqu-sz`to średni rozmiar kolejki w podanym przedziale.

Na maszynie Wirtualnej platformy Azure:

- suma `r/s` poszczególnych urządzeń blokowych nie `w/s` może przekraczać limitów jednostek SKU tego dysku.
- suma `rkB/s` poszczególnych `wkB/s` urządzeń blokowych nie może przekraczać limitów jednostki SKU tego dysku
- suma `r/s` wszystkich `w/s` urządzeń blokowych nie może przekraczać limitów dla jednostki SKU maszyny Wirtualnej.
- suma `rkB/s` i 'wkB/s dla wszystkich urządzeń blokowych nie może przekraczać limitów dla jednostki SKU maszyny Wirtualnej.

Należy zauważyć, że dysk systemu operacyjnego liczy się jako dysk zarządzany najmniejszej jednostki SKU odpowiadającej jego pojemności. Na przykład dysk systemu operacyjnego o wartości 1024 GB odpowiada dyskowi P30. Efemeryczne dyski systemu operacyjnego i dyski tymczasowe nie mają indywidualnych limitów dysków; są one ograniczone tylko przez pełne limity maszyn wirtualnych.

Wartości niezerowe await lub avgqu-sz są również dobrymi wskaźnikami rywalizacji we/wy.

## <a name="network"></a>Network (Sieć)

Narzędzia te mierzą statystyki sieci, takie jak przepustowość, awarie transmisji i wykorzystanie. Głębsza analiza może ujawnić szczegółowe statystyki TCP dotyczące przeciążenia i porzuconych pakietów.

### <a name="sar"></a>Współczynnik absorpcji ( sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`jest potężnym narzędziem do szerokiego zakresu analiz. W tym przykładzie używa możliwości pomiaru statystyk sieciowych, ale jest równie wydajny do pomiaru zużycia procesora i pamięci. W tym `sar` przykładzie wywołuje `-n` `DEV` z flagą, aby określić słowo kluczowe (urządzenie sieciowe), wyświetlanie przepływności sieci przez urządzenie.

- Suma `rxKb/s` i `txKb/s` jest całkowita przepływność dla danego urządzenia. Gdy ta wartość przekracza limit dla aprowizowanych kart sieciowych platformy Azure, obciążeń na komputerze będzie wystąpić zwiększone opóźnienie sieci.
- `%ifutil`wykorzystania danego urządzenia. W miarę zbliżania się tej wartości do 100%, obciążenia będą doświadczać zwiększonego opóźnienia sieci.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

To wywołanie `sar` używa `TCP,ETCP` słów kluczowych do badania połączeń TCP. Trzecia kolumna ostatniego wiersza, "retrans", to liczba retransmitów TCP na sekundę. Wysokie wartości dla tego pola wskazują na zawodne połączenie sieciowe. W pierwszym i trzecim wierszu "aktywny" oznacza połączenie pochodzące z urządzenia lokalnego, podczas gdy "zdalny" wskazuje połączenie przychodzące.  Typowym problemem na platformie Azure jest `sar` wyczerpanie portów SNAT, które może pomóc w wykryciu. Wyczerpanie portów SNAT będzie manifestować jako wysokie wartości "aktywne", ponieważ problem wynika z dużej szybkości wychodzących, inicjowanych lokalnie połączeń TCP.

W `sar` miarę upływu czasu drukuje toczenia, a następnie drukuje ostatnie wiersze danych wyjściowych zawierające średnie wyniki z wywołania.

### <a name="netstat"></a>Netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`może introspekcji szerokiej gamy statystyk sieci, tutaj wywoływane z wyjściem podsumowania. Istnieje wiele przydatnych pól w zależności od problemu. Jednym z przydatnych pól w sekcji TCP jest "nieudane próby połączenia". Może to być wskazanie wyczerpania portu SNAT lub innych problemów z wykonywaniem połączeń wychodzących. Wysoki wskaźnik retransmitowanych segmentów (również w sekcji TCP) może wskazywać na problemy z dostarczaniem pakietów. 
