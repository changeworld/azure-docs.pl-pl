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
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147910"
---
*Trwa rozgrzewanie pamięci podręcznej*  
Dysk z buforowania hosta tylko do odczytu są mógł przekazać wyższym operacje We/Wy niż limit na dysku. Aby uzyskać ten maksymalną wydajność odczytu z pamięci podręcznej hosta, najpierw należy musi przećwiczeniu podstawowych zadań pamięci podręcznej tego dysku. Daje to gwarancję, że odczytu z systemem IOs, że na woluminie CacheReads regulują narzędzi porównawczych faktycznie trafienia pamięci podręcznej, a nie dysków bezpośrednio. Wynik trafień w pamięci podręcznej w dodatkowe operacje We/Wy z jednym pamięci podręcznej włączone dysku.

> [!IMPORTANT]
> Przed uruchomieniem testów porównawczych, za każdym razem, gdy maszyna wirtualna jest uruchamiana ponownie, musisz rozgrzewki pamięci podręcznej.

## <a name="tools"></a>Narzędzia

### <a name="iometer"></a>Iometer

[Pobierz narzędzie Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na maszynie Wirtualnej.

#### <a name="test-file"></a>Plik testu

Iometer używa pliku testu, który znajduje się na woluminie, na którym jest uruchamiany testów porównawczych. On dyski operacji odczytu i zapisu tego pliku testu, aby zmierzyć dysku, operacje We/Wy i przepływność. Iometer tworzy tego pliku testu, jeśli nie podano jeden. Utwórz plik testu 200 GB o nazwie iobw.tst na woluminach CacheReads i NoCacheWrites.

#### <a name="access-specifications"></a>Specyfikacja dostępu

Rozmiar operacji We/Wy, % odczytu/zapisu żądania specyfikacje, % losowe/sekwencyjne są konfigurowane na karcie "Dostęp do specyfikacji" w Iometer. Utwórz specyfikację dostępu dla poszczególnych scenariuszy opisanych poniżej. Utwórz specyfikacje dostępu i "Zapisz" na preferowany nadaj nazwę takich jak — RandomWrites\_8 kilobajtów RandomReads\_8 kilobajtów. Wybierz odpowiedni specyfikację, podczas uruchamiania scenariusza testu.

Poniżej przedstawiono przykład specyfikacji dostępu dla maksymalnej scenariusza operacje We/Wy zapisu  
    ![Przykład dostępu specyfikacje dotyczące maksymalnego zapisu na SEKUNDĘ](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maksymalna liczba IOPS test specyfikacji

Aby zademonstrować maksymalna liczba IOPs, należy użyć mniejszego rozmiaru żądania. Rozmiar żądania 8K i Utwórz specyfikacje dotyczące losowego zapisu i odczytu.

| Specyfikacja dostępu | Rozmiar żądania | % Losowe | % Odczytu |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8 kilobajtów |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Maksymalna przepływność testu specyfikacji

Aby zademonstrować maksymalną przepływność, należy użyć większy rozmiar żądania. Użyj 64 KB, rozmiar żądań i Utwórz specyfikacje dotyczące losowego zapisu i odczytu.

| Specyfikacja dostępu | Rozmiar żądania | % Losowe | % Odczytu |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64 K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Uruchom Iometer test

Wykonaj poniższe kroki, aby rozgrzewki pamięci podręcznej

1. Tworzenie dwóch specyfikacje dostępu przy użyciu wartości podanych poniżej,

   | Name (Nazwa) | Rozmiar żądania | % Losowe | % Odczytu |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. Uruchom test Iometer zainicjować dysk pamięci podręcznej z następującymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki wynoszącej 128. Ustaw czas trwania testu "Czas wykonywania" do 2 godzin na karcie "Setup testów".

   | Scenariusz | Wolumin docelowy | Name (Nazwa) | Czas trwania |
   | --- | --- | --- | --- |
   | Zainicjuj dysk pamięci podręcznej |CacheReads |RandomWrites\_1 MB |2 godz. |
1. Uruchom test Iometer rozgrzewania dysk pamięci podręcznej z następującymi parametrami. Użyj trzech wątków roboczych dla woluminu docelowego i głębokości kolejki wynoszącej 128. Ustaw czas trwania testu "Czas wykonywania" do 2 godzin na karcie "Setup testów".

   | Scenariusz | Wolumin docelowy | Name (Nazwa) | Czas trwania |
   | --- | --- | --- | --- |
   | Rozgrzewanie dysk pamięci podręcznej |CacheReads |RandomReads\_1 MB |2 godz. |

Po dysk pamięci podręcznej jest przygotowaniu, Kontynuuj przy użyciu scenariuszy testowania wymienionych poniżej. Aby uruchomić Iometer test, należy użyć co najmniej trzech wątków roboczych dla **każdego** docelowy wolumin. Dla każdego wątku roboczego wybierz wolumin docelowy, Ustawia głębokość kolejki, a następnie wybierz jedną z specyfikacją zapisane badania, jak pokazano w poniższej tabeli, aby uruchomić odpowiedni scenariusz testów. W tabeli przedstawiono również oczekiwanych wyników na SEKUNDĘ i przepływność podczas uruchamiania tych testów. W przypadku wszystkich scenariuszy używany jest mały rozmiar operacji We/Wy o rozmiarze 8 KB i wysoką głębokości 128.

| Scenariusz testów | Wolumin docelowy | Name (Nazwa) | Wynik |
| --- | --- | --- | --- |
| Maksymalnie z Operacje odczytu We/Wy |CacheReads |RandomWrites\_8K |50 000 OPERACJI WE/WY |
| Maksymalnie z Operacje We/Wy zapisu |NoCacheWrites |RandomReads\_8 kilobajtów |64 000 OPERACJI WE/WY |
| Maksymalnie z Łączna liczba IOPS |CacheReads |RandomWrites\_8K |100 000 OPERACJI WE/WY |
| NoCacheWrites |RandomReads\_8 kilobajtów | &nbsp; | &nbsp; |
| Maksymalnie z Odczyt MB/s |CacheReads |RandomWrites\_64K |524 MB/s |
| Maksymalnie z Zapis MB/s |NoCacheWrites |RandomReads\_64 K |524 MB/s |
| Połączone MB/s |CacheReads |RandomWrites\_64K |1000 MB/s |
| NoCacheWrites |RandomReads\_64 K | &nbsp; | &nbsp; |

Poniżej przedstawiono zrzuty ekranu przedstawiające Iometer wyniki testu dla połączonych scenariuszach operacje We/Wy i przepływność.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Połączone odczytuje i zapisuje maksymalna liczba IOPS

![Połączone odczyty i zapisy maksymalna liczba IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Połączone odczytuje i zapisuje maksymalna przepływność

![Maksymalna przepływność połączone odczyty i zapisy](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO to popularne narzędzie do magazynu testów porównawczych na maszynach wirtualnych z systemem Linux. Ma wybrać różne rozmiary we/wy, sekwencyjnych lub losowych operacji odczytu i zapisu. Jej spowoduje utworzenie wątków roboczych i realizowania innych procesów do wykonywania określonej operacji We/Wy. Można określić typ operacji We/Wy każdego wątku roboczego należy wykonać przy użyciu plików zadania. Utworzyliśmy jeden plik zadania na scenariusz pokazano w poniższych przykładach. Możesz zmienić specyfikacji w tych plikach zadania przeprowadzenie testu porównawczego różnych obciążeń uruchomionych na usługę Premium Storage. W przykładach użyto standardowego DS 14 w maszyny Wirtualnej z systemem **Ubuntu**. Użycie tej samej konfiguracji opisanych na początku sekcji Benchmarking i dostępu do ciepłych pamięci podręcznej przed uruchomieniem testów porównawczych.

Przed przystąpieniem do wykonywania [Pobierz FIO](https://github.com/axboe/fio) i zainstaluj go na maszynie wirtualnej.

Uruchom następujące polecenie na systemie Ubuntu

```
apt-get install fio
```

Cztery wątki robocze do obsługi operacji zapisu i cztery wątki robocze używamy opracowuje operacji odczytu na dyskach. Procesy robocze zapisu napędzają ruchu na wolumin "właściwość nocache", który zawiera dyski 10 z pamięcią podręczną równa "None". Procesy robocze odczytu napędzają ruchu w woluminie "readcache", który ma jeden dysk z pamięci podręcznej ustawioną na "ReadOnly".

#### <a name="maximum-write-iops"></a>Maksymalna liczba zapisu na sekundę

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną zapisu operacji We/Wy. Nadaj mu nazwę "fiowrite.ini".

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

Należy pamiętać, postępuj zgodnie z rzeczy, które są tworzone są zalecenia dotyczące projektowania omówione w poprzednich sekcjach. W tych specyfikacjach są istotne dla maksymalnej operacje We/Wy dysku  

* Głębokość kolejki o wysokiej 256.  
* Małych blokach o rozmiarze 8 KB.  
* Losowe zapisy wykonywanie wielu wątków.

Uruchom następujące polecenie, aby uruchamiał testu FIO przez 30 sekund  

```
sudo fio --runtime 30 fiowrite.ini
```

Podczas wykonywania testu, jesteś w stanie wyświetlić liczbę zapisu na SEKUNDĘ maszyny Wirtualnej i dyski w warstwie Premium są dostarczane. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 jest dostarczanie jego zapisu maksymalny limit operacji We/Wy 50 000 operacji We/Wy.  
    ![Liczba zapisu dysków maszyny Wirtualnej operacje We/Wy i Premium są dostarczane.](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maksymalny odczyt operacji We/Wy

Utwórz plik zadania z następującymi specyfikacjami, aby uzyskać maksymalną operacje odczytu We/Wy. Nadaj mu nazwę "fioread.ini".

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

Należy pamiętać, postępuj zgodnie z rzeczy, które są tworzone są zalecenia dotyczące projektowania omówione w poprzednich sekcjach. W tych specyfikacjach są istotne dla maksymalnej operacje We/Wy dysku

* Głębokość kolejki o wysokiej 256.  
* Małych blokach o rozmiarze 8 KB.  
* Losowe zapisy wykonywanie wielu wątków.

Uruchom następujące polecenie, aby uruchamiał testu FIO przez 30 sekund

```
sudo fio --runtime 30 fioread.ini
```

Podczas wykonywania testu, jesteś w stanie liczba odczytanych na SEKUNDĘ maszyny Wirtualnej, a dyski w warstwie Premium są dostarczane. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 świadczy ponad 64 000 operacji odczytu We/Wy. Jest to kombinacja dysku i wydajność pamięci podręcznej.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Maksymalny Odczyt i zapis operacji We/Wy

Utwórz plik zadania z następujących specyfikacji, aby uzyskać maksymalną połączone odczytu i zapisu na SEKUNDĘ. Nadaj mu nazwę "fioreadwrite.ini".

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

Należy pamiętać, postępuj zgodnie z rzeczy, które są tworzone są zalecenia dotyczące projektowania omówione w poprzednich sekcjach. W tych specyfikacjach są istotne dla maksymalnej operacje We/Wy dysku

* Głębokość kolejki o wysokiej 128.  
* Małych blokach o rozmiarze 4 KB.  
* Wiele wątków, wykonując losowych operacji odczytu i zapisu.

Uruchom następujące polecenie, aby uruchamiał testu FIO przez 30 sekund

```
sudo fio --runtime 30 fioreadwrite.ini
```

Podczas wykonywania testu, można wyświetlić liczbę połączonych odczytu i zapisu na SEKUNDĘ maszyny Wirtualnej, a dyski w warstwie Premium są dostarczane. Jak pokazano w poniższym przykładzie, maszyna wirtualna DS14 świadczy ponad 100 000 połączone odczytu i zapisu na SEKUNDĘ. Jest to kombinacja dysku i wydajność pamięci podręcznej.  
    ![Połączone odczytu i zapisu na SEKUNDĘ](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maksymalna łączna przepływność

Aby uzyskać maksymalną połączone odczytu i zapisu przepływności, większy rozmiar bloku i dużych głębokości za pomocą wielu wątków, wykonywanie operacji odczytu i zapisu. Można użyć blok o rozmiarze 64 KB i głębokości kolejki wynoszącej 128.
