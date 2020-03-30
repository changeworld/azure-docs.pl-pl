---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673450"
---
*Rozgrzewanie pamięci podręcznej*  
Dysk z buforowania hosta ReadOnly są w stanie dać wyższe We/Wy niż limit dysku. Aby uzyskać maksymalną wydajność odczytu z pamięci podręcznej hosta, należy najpierw rozgrzać pamięć podręczną tego dysku. Gwarantuje to, że odczyt ios, że narzędzie do porównywania będzie jeździć na woluminie CacheReads, faktycznie trafia do pamięci podręcznej, a nie bezpośrednio dysku. Trafienia w pamięci podręcznej powodują dodatkowe we/wy we/wy z dysku obsługującego pojedynczą pamięć podręczną.

> [!IMPORTANT]
> Należy rozgrzać pamięć podręczną przed uruchomieniem analizy porównawczej, za każdym razem, gdy maszyna wirtualna jest ponownie uruchamiana.

## <a name="tools"></a>Narzędzia

### <a name="iometer"></a>Iometr

[Pobierz narzędzie Iometer](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na maszynie wirtualnej.

#### <a name="test-file"></a>Plik testowy

Iometer używa pliku testowego, który jest przechowywany na woluminie, na którym można uruchomić test porównawczy. Dyski odczytuje i zapisuje na tym pliku testowym do pomiaru we/wy i przepływności dysku. Iometer tworzy ten plik testowy, jeśli go nie podałeś. Utwórz plik testowy o pojemności 200 GB o nazwie iobw.tst na woluminach CacheReads i NoCacheWrites.

#### <a name="access-specifications"></a>Specyfikacje dostępu

Specyfikacje, rozmiar we/wy żądania, % odczytu/zapisu, % losowe/sekwencyjne są konfigurowane przy użyciu zakładki "Specyfikacje dostępu" w Iometer. Utwórz specyfikację dostępu dla każdego ze scenariuszy opisanych poniżej. Utwórz specyfikacje dostępu i "Zapisz" o odpowiedniej\_nazwie, takiej jak\_RandomWrites 8K, RandomReads 8K. Wybierz odpowiednią specyfikację podczas uruchamiania scenariusza testowego.

Poniżej przedstawiono przykład specyfikacji dostępu dla maksymalnego scenariusza zapisu operacji We/Wy,  
    ![Przykład specyfikacji dostępu dla maksymalnego zapisu We/Wy](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maksymalna specyfikacja testu IOPS

Aby zademonstrować maksymalne we/wy, należy użyć mniejszego rozmiaru żądania. Użyj rozmiaru żądania 8K i utwórz specyfikacje dla losowych zapisów i odczytów.

| Specyfikacja dostępu | Rozmiar żądania | Losowy % | Odczyt % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 tys. |100 |0 |
| Losowe\_odczyty 8K |8 tys. |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maksymalne specyfikacje testów wydajności

Aby zademonstrować maksymalną przepływność, należy użyć większego rozmiaru żądania. Użyj 64 K rozmiar żądania i utworzyć specyfikacje dla losowych zapisów i odczytów.

| Specyfikacja dostępu | Rozmiar żądania | Losowy % | Odczyt % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 k. |100 |0 |
| Losowe\_odczyty 64K |64 k. |100 |100 |

#### <a name="run-the-iometer-test"></a>Uruchom test Iometer

Wykonaj poniższe czynności, aby rozgrzać pamięć podręczną

1. Utwórz dwie specyfikacje dostępu z wartościami pokazanymi poniżej,

   | Nazwa | Rozmiar żądania | Losowy % | Odczyt % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | Losowe\_odczyty 1MB |1 MB |100 |100 |
1. Uruchom test Iometer w celu zainicjowania dysku pamięci podręcznej z następującymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki 128. Ustaw czas trwania testu "Czas wykonywania" na 2 godziny na karcie "Konfiguracja testu".

   | Scenariusz | Wolumin docelowy | Nazwa | Czas trwania |
   | --- | --- | --- | --- |
   | Inicjowanie dysku pamięci podręcznej |Odczyty pamięci podręcznej |RandomWrites\_1MB |2 godz. |
1. Uruchom test Iometer w celu nagrzebienia dysku pamięci podręcznej z następującymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki 128. Ustaw czas trwania testu "Czas wykonywania" na 2 godziny na karcie "Konfiguracja testu".

   | Scenariusz | Wolumin docelowy | Nazwa | Czas trwania |
   | --- | --- | --- | --- |
   | Rozgrzej dysk pamięci podręcznej |Odczyty pamięci podręcznej |Losowe\_odczyty 1MB |2 godz. |

Po rozgrzaniu dysku pamięci podręcznej, przejdź do scenariuszy testowych wymienionych poniżej. Aby uruchomić test Iometer, należy użyć co najmniej trzech wątków roboczych dla **każdego** woluminu docelowego. Dla każdego wątku roboczego wybierz wolumin docelowy, ustaw głębokość kolejki i wybierz jedną ze specyfikacji zapisanych testów, jak pokazano w poniższej tabeli, aby uruchomić odpowiedni scenariusz testowy. W tabeli przedstawiono również oczekiwane wyniki dla we/wy i przepływności podczas uruchamiania tych testów. Dla wszystkich scenariuszy używany jest mały rozmiar we/wy 8 KB i wysoka głębokość kolejki 128.

| Scenariusz testowy | Wolumin docelowy | Nazwa | Wynik |
| --- | --- | --- | --- |
| Maksymalnie z Odczyt IOPS |Odczyty pamięci podręcznej |RandomWrites\_8K |50 000 IOPS |
| Maksymalnie z Zapis IOPS |NoCachePisy |Losowe\_odczyty 8K |64 000 iops |
| Maksymalnie z Połączone IOPS |Odczyty pamięci podręcznej |RandomWrites\_8K |100 000 IOPS |
| NoCachePisy |Losowe\_odczyty 8K | &nbsp; | &nbsp; |
| Maksymalnie z Odczyt MB/s |Odczyty pamięci podręcznej |RandomWrites\_64K |524 MB/s |
| Maksymalnie z Zapis MB/s |NoCachePisy |Losowe\_odczyty 64K |524 MB/s |
| Połączone MB/s |Odczyty pamięci podręcznej |RandomWrites\_64K |1000 MB/s |
| NoCachePisy |Losowe\_odczyty 64K | &nbsp; | &nbsp; |

Poniżej znajdują się zrzuty ekranu wyników testu Iometer dla połączonych scenariuszy we/wy i przepływności.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Połączone odczyty i zapisy maksymalne we/wy

![Połączone odczyty i zapisy maksymalne we/wy](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Połączone odczyty i zapisy maksymalnej przepływności

![Połączone odczyty i zapisy maksymalna przepływność](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO jest popularnym narzędziem do porównywania pamięci masowej na maszynach wirtualnych z systemem Linux. Ma elastyczność, aby wybrać różne rozmiary we/wy, sekwencyjne lub losowe odczyty i zapisy. Tworzy wątki robocze lub procesy do wykonywania określonych operacji we/wy. Można określić typ operacji we/wy, które każdy wątek roboczy musi wykonać przy użyciu plików zadań. Utworzyliśmy jeden plik zadania na scenariusz zilustrowany w poniższych przykładach. Można zmienić specyfikacje w tych plikach zadań, aby porównać różne obciążenia uruchomione w magazynie w wersji Premium. W przykładach używamy standardowego ds 14 VM z systemem **Ubuntu**. Użyj tej samej konfiguracji opisanej na początku sekcji Benchmarking i rozgrzej pamięć podręczną przed uruchomieniem testów porównawczych.

Przed rozpoczęciem [pobierz FIO](https://github.com/axboe/fio) i zainstaluj go na swojej maszynie wirtualnej.

Uruchom następujące polecenie dla Ubuntu,

```
apt-get install fio
```

Używamy czterech wątków roboczych do prowadzenia operacji zapisu i czterech wątków roboczych do prowadzenia operacji odczytu na dyskach. Pracownicy zapisu są jazdy ruchu na woluminie "nocache", który ma 10 dysków z pamięci podręcznej ustawionej na "Brak". Odczyt pracowników są jazdy ruchu na woluminie "readcache", który ma jeden dysk z pamięcią podręczną ustawioną na "ReadOnly".

#### <a name="maximum-write-iops"></a>Maksymalny zapis We/Wy

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę we/wy zapisu. Nazwij go "fiowrite.ini".

```ini
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

Należy zwrócić uwagę na następujące kluczowe elementy, które są zgodne z wytycznymi dotyczącymi projektowania omówione w poprzednich sekcjach. Specyfikacje te są niezbędne do prowadzenia maksymalnej liczby  

* Wysoka głębokość kolejki 256.  
* Mały rozmiar bloku 8 KB.  
* Wiele wątków wykonujących losowe zapisy.

Uruchom następujące polecenie, aby rozpocząć test FIO na 30 sekund,  

```
sudo fio --runtime 30 fiowrite.ini
```

Podczas pracy testu, można zobaczyć liczbę zapisu We/Wy, które dostarczają dyski Maszyny Wirtualnej i Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 zapewnia maksymalny limit we/wy zapisu wynoszący 50 000 we/wy.  
    ![Liczba zapisu we/wy maszyny Wirtualnej i dysków Premium](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maksymalny odczyt we/wy

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną liczbę do odczytu we/wy. Nazwij go "fioread.ini".

```ini
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

Należy zwrócić uwagę na następujące kluczowe elementy, które są zgodne z wytycznymi dotyczącymi projektowania omówione w poprzednich sekcjach. Specyfikacje te są niezbędne do prowadzenia maksymalnej liczby

* Wysoka głębokość kolejki 256.  
* Mały rozmiar bloku 8 KB.  
* Wiele wątków wykonujących losowe zapisy.

Uruchom następujące polecenie, aby rozpocząć test FIO na 30 sekund,

```
sudo fio --runtime 30 fioread.ini
```

Podczas pracy testu, można zobaczyć liczbę odczytu We/Wy, które dostarczają dyski maszyny Wirtualnej i Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 dostarcza ponad 64 000 odczytu we/wy. Jest to kombinacja wydajności dysku i pamięci podręcznej.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maksymalne odczytywanie i zapisywanie we/wy

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną łączną liczbę połączonych we/wy odczytu i zapisu. Nazwij go "fioreadwrite.ini".

```ini
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

Należy zwrócić uwagę na następujące kluczowe elementy, które są zgodne z wytycznymi dotyczącymi projektowania omówione w poprzednich sekcjach. Specyfikacje te są niezbędne do prowadzenia maksymalnej liczby

* Wysoka głębokość kolejki 128.  
* Mały blok o rozmiarze 4 KB.  
* Wiele wątków wykonujących losowe odczyty i zapisy.

Uruchom następujące polecenie, aby rozpocząć test FIO na 30 sekund,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Podczas pracy testu, można zobaczyć liczbę połączonych odczytu i zapisu We/Wy, które dostarczają dyski maszyny Wirtualnej i Premium. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 dostarcza ponad 100 000 połączonych funkcji We/Wy odczytu i zapisu. Jest to kombinacja wydajności dysku i pamięci podręcznej.  
    ![Połączone odczytywanie i zapis we/wy](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maksymalna łączna przepustowość

Aby uzyskać maksymalną łączną przepustowość odczytu i zapisu, należy użyć większego rozmiaru bloku i dużej głębokości kolejki z wieloma wątkami wykonującymi odczyty i zapisy. Można użyć rozmiaru bloku o rozmiarze 64 KB i głębokości kolejki 128.
