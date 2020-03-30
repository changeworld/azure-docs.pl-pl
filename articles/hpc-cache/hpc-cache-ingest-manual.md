---
title: Pozyskiwania danych usługi Azure HPC Cache — kopiowanie ręczne
description: Jak używać poleceń cp do przenoszenia danych do miejsca docelowego magazynu obiektów Blob w pamięci podręcznej HPC usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fc397088e46f0d2b623080f3deed24c386e7d8b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168480"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Pozyskiwania danych usługi Azure HPC Cache — metoda ręcznego kopiowania

Ten artykuł zawiera szczegółowe instrukcje dotyczące ręcznego kopiowania danych do kontenera magazynu obiektów Blob do użytku z pamięcią podręczną HPC usługi Azure. Używa wielowątkowy operacji równoległych w celu optymalizacji szybkości kopiowania.

Aby dowiedzieć się więcej o przenoszeniu danych do magazynu obiektów Blob dla pamięci podręcznej HPC platformy Azure, przeczytaj artykuł [Przenoszenie danych do magazynu obiektów Blob platformy Azure.](hpc-cache-ingest.md)

## <a name="simple-copy-example"></a>Przykład prostej kopii

Kopię wielowątkową można utworzyć na kliencie, uruchamiając więcej niż jedno polecenie kopiowania jednocześnie w tle względem wstępnie zdefiniowanych zestawów plików lub ścieżek.

Polecenie Linux/UNIX ``cp`` zawiera ``-p`` argument, aby zachować własność i metadane mtime. Dodanie tego argumentu do poniższych poleceń jest opcjonalne. (Dodanie argumentu zwiększa liczbę wywołań systemu plików wysyłanych z klienta do docelowego systemu plików w celu modyfikacji metadanych).

W tym prostym przykładzie kopiuje dwa pliki równolegle:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po wydaniu `jobs` tego polecenia polecenie pokaże, że dwa wątki są uruchomione.

## <a name="copy-data-with-predictable-file-names"></a>Kopiowanie danych z przewidywalnymi nazwami plików

Jeśli nazwy plików są przewidywalne, można użyć wyrażeń do tworzenia równoległych wątków kopiowania. 

Na przykład, jeśli katalog zawiera 1000 plików, które są `0001` ponumerowane sekwencyjnie od do `1000`, można użyć następujących wyrażeń, aby utworzyć dziesięć równoległych wątków, które każdy kopiuje 100 plików:

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>Kopiowanie danych z nieustrukturyzowanymi nazwami plików

Jeśli struktura nazewnictwa plików nie jest przewidywalna, można grupować pliki według nazw katalogów. 

W tym przykładzie zbiera całe ``cp`` katalogi do wysłania do poleceń uruchamianych jako zadania w tle:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Po zebraniu plików można uruchamiać polecenia kopiowania równoległego w celu cyklicznego kopiowania podkatalogów i całej ich zawartości:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>Kiedy dodać punkty instalacji

Po dość równoległych wątków będzie względem jednego punktu instalacji systemu plików docelowych, będzie punkt, w którym dodanie większej liczby wątków nie daje większą przepływność. (Przepływność będzie mierzona w plikach/sekundzie lub bajtach/sekundę, w zależności od typu danych). Lub co gorsza, nadmierne wątki może czasami spowodować degradacji przepływności.  

W takim przypadku można dodać punkty instalacji po stronie klienta do innych adresów instalacji usługi Azure HPC Cache przy użyciu tej samej ścieżki zdalnego instalowania systemu plików:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Dodanie punktów instalacji po stronie klienta umożliwia wydłużenie dodatkowych poleceń kopiowania do dodatkowych `/mnt/destination[1-3]` punktów instalacji, co pozwala osiągnąć dalsze równoległości.  

Na przykład jeśli pliki są bardzo duże, można zdefiniować polecenia kopiowania, aby użyć różnych ścieżek docelowych, wysyłając więcej poleceń równolegle od klienta wykonującego kopię.

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

W powyższym przykładzie wszystkie trzy punkty instalacji docelowej są kierowane przez procesy kopiowania plików klienta.

## <a name="when-to-add-clients"></a>Kiedy dodać klientów

Wreszcie po osiągnięciu możliwości klienta, dodanie większej liczby wątków kopiowania lub dodatkowych punktów instalacji nie przyniesie żadnych dodatkowych plików/s lub bajtów/s zwiększa. W takiej sytuacji można wdrożyć innego klienta z tym samym zestawem punktów instalacji, który będzie uruchamiał własne zestawy procesów kopiowania plików. 

Przykład:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>Tworzenie manifestów plików

Po zrozumieniu powyższych metod (wiele wątków kopiowania na miejsce docelowe, wiele miejsc docelowych na klienta, wielu klientów na system plików źródłowych dostępnych dla sieci), rozważ to zalecenie: Zbuduj manifesty plików, a następnie użyj ich z kopią polecenia na wielu klientach.

W tym scenariuszu ``find`` użyto polecenia UNIX do tworzenia manifestów plików lub katalogów:

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

Przekieruj ten wynik do pliku:`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Następnie można iterować za pomocą manifestu, za pomocą poleceń BASH do zliczania plików i określania rozmiarów podkatalogów:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Na koniec należy spreparować rzeczywiste polecenia kopiowania plików do klientów.  

Jeśli masz czterech klientów, użyj tego polecenia:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Jeśli masz pięciu klientów, użyj czegoś takiego:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

I dla sześciu.... Ekstrapolować w razie potrzeby.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Otrzymasz *N* wynikowe pliki, po jednym dla każdego z klientów *N,* który ma nazwy ścieżki do `find` poziomu cztery katalogi uzyskane jako część danych wyjściowych z polecenia. 

Użyj każdego pliku, aby utworzyć polecenie kopiowania:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Powyżej daje pliki *N,* każdy z polecenia kopiowania w wierszu, które mogą być uruchamiane jako skrypt BASH na kliencie. 

Celem jest uruchomienie wielu wątków tych skryptów jednocześnie na klienta równolegle na wielu klientach.
