---
title: Przenoszenie danych do Avere vFXT dla platformy Azure
description: Jak dodawać dane do nowego woluminu magazynu do użycia z Avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: a3d6cb745c782d2a7166208f2a8dd1202a330b15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410123"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Przenoszenie danych do klastra vFXT — równoległego pozyskiwania 

Po utworzeniu nowego klastra vFXT pierwsze zadanie może być do przenoszenia danych do nowego woluminu magazynu. Jednak jeśli w zwykły sposób przenoszenia danych jest wykonywane polecenie prostą kopię z jednego klienta, zostanie wyświetlony ekran podobny wydajności kopiowania powolne. Jednowątkowe kopiowania nie jest dobra opcja w przypadku kopiowania danych do magazynu w klastrze vFXT Avere wewnętrznej bazy danych.

Ponieważ klaster vFXT Avere jest skalowalna wielu klientów pamięci podręcznej, to najszybszy i najbardziej efektywny sposób kopiowania danych do niego jest z wieloma klientami. Ta technika parallelizes pozyskiwanie pomiarów dotyczących plików i obiektów.

![Diagram przedstawiający wielu klientów wielowątkowych przenoszenia danych: W lewym górnym rogu ikonę magazynu sprzętu lokalnego ma wiele strzałki pochodzących z niego. Strzałki wskazują cztery komputery klienckie. Z każdego komputera klienckiego trzy strzałki wskazują Avere vFXT. Skieruj wiele strzałki Avere vFXT, do usługi Blob storage.](media/avere-vfxt-parallel-ingest.png) 

``cp`` Lub ``copy`` poleceń, które są często używane do korzystania z na przesyłanie danych z jednego magazynu systemu do drugiego to procesy jednowątkowe kopiować tylko jeden plik jednocześnie. Oznacza to, że serwer plików jest wprowadzane tylko jeden plik jednocześnie — czyli do marnowania zasobów klastra.

W tym artykule opisano Strategie tworzenia wielu klientów, wielowątkowe kopiowania systemu, aby przenieść dane do klastra vFXT Avere pliku. Wyjaśniono pojęcia transferu plików i punkty decyzyjne, które mogą służyć do wydajne danych kopiowania za pomocą wielu poleceń prostą kopię i klientów.

Objaśniono także niektóre narzędzia, które mogą pomóc. ``msrsync`` Narzędzie może służyć do częściowo zautomatyzować proces dzielenia zestawu danych do zasobników i za pomocą polecenia rsync. ``parallelcp`` Innego narzędzia, która odczytuje katalog źródłowy jest skrypt i problemy poleceń kopiowania.  

Kliknij link, aby przejść do sekcji:

* [Ręczne kopiowanie przykładowych](#manual-copy-example) — szczegółowe wyjaśnienia, za pomocą polecenia Kopiuj
* [Przykład częściowo automatyczna (msrsync)](#use-the-msrsync-utility-to-populate-cloud-volumes) 
* [Przykład równoległych kopii](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Szablon maszyny Wirtualnej systemem zbierania danych

Szablon usługi Resource Manager jest dostępna w witrynie GitHub, aby automatycznie utworzyć Maszynę wirtualną za pomocą narzędzi pozyskiwania danych równoległych wymienionych w tym artykule. 

![Diagram przedstawiający kilka strzałkami z magazynu obiektów blob, Magazyn sprzętu i źródła plików platformy Azure. Strzałki wskazują "danych dużych możliwościach skalowania maszyny wirtualnej" i z tego miejsca wielu strzałki wskazują Avere vFXT](media/avere-vfxt-ingestor-vm.png)

Danych dużych możliwościach skalowania maszyn wirtualnych stanowi część samouczka, gdzie nowo utworzonej maszyny Wirtualnej instaluje Avere vFXT klastra i pobierania jej uruchamiania skryptu z klastra. Odczyt [Bootstrap danych dużych możliwościach skalowania maszyn wirtualnych](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) Aby uzyskać szczegółowe informacje.

## <a name="strategic-planning"></a>Planowanie strategiczne

Podczas kompilowania strategii w celu kopiowania danych w sposób równoległy, należy zrozumieć kompromisów w rozmiar pliku, liczba plików i katalog głębi.

* W przypadku małych plików Metryka zainteresowań jest plików na sekundę.
* Kiedy pliki są duże (10MiBi lub nowszej), metryka zainteresowania wskazuje bajtów na sekundę.

Każdy proces kopiowania ma poziom przepływności i szybkości przesłanych plików, która może być mierzone na podstawie czasu długość polecenia Kopiuj i uwzględniając rozmiar pliku i liczba plików. Wyjaśnienie sposobu mierzenia stawki wykracza poza zakres tego dokumentu, ale konieczne jest zrozumienie, czy użytkownik będzie można zajmujących się małych lub dużych plików.

## <a name="manual-copy-example"></a>Ręczne kopiowanie przykładowych 

Można ręcznie utworzyć kopię wielowątkowych na komputerze klienckim, uruchamiając jednocześnie więcej niż jednego polecenia kopiowania w tle względem wstępnie zdefiniowanych zestawów pliki lub ścieżki.

Linux/UNIX ``cp`` polecenie zawiera argument ``-p`` zachować prawo własności i mtime metadanych. Dodanie tego argumentu do poniższych poleceń jest opcjonalne. (Dodanie argument zwiększa liczbę wywołań systemu plików wysłanych z klienta w systemie docelowym plików do modyfikacji metadanych).

Ten prosty przykład kopiuje dwa pliki w sposób równoległy:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po wykonaniu tego polecenia `jobs` polecenia pokażą, że dwa wątki są uruchomione.

### <a name="predictable-filename-structure"></a>Struktura przewidywalne, nazwa_pliku 

Jeśli Twojej nazwy plików są przewidywalne, można użyć wyrażenia do tworzenia wątków równoległych kopii. 

Na przykład, jeśli Twój katalog zawiera 1000 plików, które są numerowane od `0001` do `1000`, można użyć następujących wyrażeń do utworzenia dziesięć wątków równoległych na każdym skopiuj 100 plików:

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

### <a name="unknown-filename-structure"></a>Nieznana nazwa pliku, struktura

Jeśli Twoja struktura nazewnictwa plików nie jest przewidywalne, pliki można grupować wg nazwy katalogów. 

W tym przykładzie zbiera całe katalogi do wysłania do ``cp`` polecenia są uruchamiane w tle:

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

Po zebraniu są pliki, można uruchomić osobną kopię polecenia, aby rekursywnie skopiować podkatalogów i ich cała zawartość:

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>Kiedy należy dodać punkty instalacji

Po użytkownik ma wystarczającej liczby wątków równoległych na przechodząc względem punktu instalacji systemu plików jednym miejscu, nastąpi punktu, w których dodanie większej liczby wątków nie zapewnia wyższą przepływność. (Przepływność będzie mierzone w plikach na sekundę lub bajtów na sekundę, w zależności od typu danych.) Lub co gorsza, nadmiernie wątkowości może czasami powodować spadek przepustowości.  

W takiej sytuacji można dodać punktów instalacji po stronie klienta z innymi adresami IP vFXT klastra przy użyciu tej samej ścieżki instalacji zdalnej systemu plików:

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

Dodawanie klienta instalacji wskazuje umożliwia rozwidlenie wyłącza polecenia dodatkowych kopii do dodatkowych `/mnt/destination[1-3]` punkty instalacji, dodatkowo osiągnięcia równoległości.  

Na przykład jeśli pliki są bardzo duże, można zdefiniować polecenia kopiowania, aby stosować różne docelowej ścieżki, wysyłając więcej poleceń równolegle z klienta, wykonywania kopii.

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

W powyższym przykładzie wszystkie trzy docelowe punkty instalacji są jest objęte procesami kopiowania plików klienta.

### <a name="when-to-add-clients"></a>Kiedy należy dodać klientów

Ponadto po osiągnięto możliwości klienta, dodawanie większej liczby wątków kopiowania dodatkowych punktów instalacji nie przyniesie wszelkie dodatkowe pliki na sekundę lub zwiększa się liczba bajtów/s. W takiej sytuacji można wdrożyć innego klienta za pomocą tego samego zestawu punktów instalacji, uruchomionych swoje własne zestawy procesami kopiowania plików. 

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

### <a name="create-file-manifests"></a>Utwórz plik manifestów

Po zrozumienia metod powyżej (wielu kopii wątków na docelowym, wielu miejsc docelowych na klienta, wielu klientów na system plików źródłowych dostępnych sieci), należy wziąć pod uwagę tego zalecenia: Tworzenie pliku manifestów, a następnie używać ich za pomocą polecenia kopiowania na wielu klientach.

W tym scenariuszu UNIX ``find`` polecenie, aby utworzyć manifesty plików lub katalogów:

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

Przekierować tego wyniku do pliku: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

Następnie można wykonać iterację manifestu, liczba plików i określić rozmiary podkatalogów, za pomocą polecenia powłoki BASH:

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

Na koniec należy tworzyć polecenia kopiowania rzeczywistego pliku do klientów.  

Jeśli klienci znajdują się cztery, użyj tego polecenia:

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

Jeśli masz pięć komputerów klienckich, należy użyć podobny do poniższego:

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

Oraz sześć... Ekstrapolację zgodnie z potrzebami.

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

Zostanie wyświetlony *N* pliki wynikowe, po jednym dla każdej usługi *N* klientów, które zawiera nazwy ścieżek do katalogów poziomu czterech uzyskane w ramach danych wyjściowych z `find` polecenia. 

Umożliwia tworzenie polecenia Kopiuj każdego pliku:

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

Powyżej zapewni *N* pliki każdego za pomocą polecenia Kopiuj w każdym wierszu, który można uruchomić jako skrypt powłoki BASH na komputerze klienckim. 

Celem jest do równoległego uruchamiania wielu wątków z tych skryptów równolegle na klienta na wielu klientach.

## <a name="use-the-msrsync-utility-to-populate-cloud-volumes"></a>Użyj narzędzia msrsync do wypełniania woluminy chmury

``msrsync`` Narzędzie również służy do przenoszenia danych do filtr rdzeni wewnętrznej bazy danych, dla klastra Avere. To narzędzie jest przeznaczone do optymalizacji przepustowości, uruchamiając wiele równoległych ``rsync`` procesów. Jest on dostępny w witrynie GitHub pod https://github.com/jbd/msrsync.

``msrsync`` dzieli się katalog źródłowy w oddzielnych "zasobniki", a następnie uruchamia poszczególnych ``rsync`` procesów na każdego przedziału.

Wstępnego testowania za pomocą 4 rdzeniową maszynę Wirtualną wykazało uzyskania najlepszej wydajności, korzystając z 64 procesów. Użyj ``msrsync`` opcji ``-p`` można ustawić liczbę procesów 64.

Należy pamiętać, że ``msrsync`` mogą zapisywać wyłącznie do i z woluminów lokalnych. Źródłowe i docelowe muszą być dostępne jako instaluje lokalnych w sieci wirtualnej klastra.

Aby użyć msrsync do wypełniania woluminie chmury platformy Azure z klastrem Avere, wykonaj następujące instrukcje:

1. Zainstaluj msrsync i jego wymagania wstępne (rsync i języka Python w wersji 2.6 lub nowszej)
1. Określ, całkowita liczba plików i katalogów do skopiowania.

   Na przykład, użyj narzędzia Avere ``prime.py`` z argumentami ```prime.py --directory /path/to/some/directory``` (dostępne pobierając adresu url https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py).

   Jeśli nie używa ``prime.py``, można obliczyć liczbę elementów za pomocą Gnu ``find`` narzędzie w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dzielenie liczby elementów przez 64, aby określić liczbę elementów na proces. Użyj następującego numeru przy użyciu ``-f`` opcję, aby ustawić rozmiar zasobników, po uruchomieniu polecenia.

1. Wykonaj polecenie msrsync, aby skopiować pliki:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie zostało zaprojektowane, aby przenieść pliki 11 000 w procesach 64 z /test/source-repository /mnt/vfxt/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Użyj skryptu osobną kopię

``parallelcp`` Skrypt może być również przydatne w przypadku przenoszenia danych do magazynu w klastrze vFXT wewnętrznej bazy danych. 

Poniższy skrypt spowoduje dodanie pliku wykonywalnego `parallelcp`. (Ten skrypt jest przeznaczony dla systemu Ubuntu; w przypadku korzystania z innego punktów dystrybucji, należy zainstalować ``parallel`` oddzielnie.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>Przykład równoległych kopii

W tym przykładzie użyto równoległe Kopiuj skrypt, aby skompilować ``glibc`` przy użyciu plików źródłowych z Avere klastra. 
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Pliki źródłowe są przechowywane w punkcie instalacji Avere klastra, a pliki obiektów są przechowywane na lokalnym dysku twardym.

Ten skrypt używa równoległe Kopiuj skrypt powyżej. Opcja ``-j`` jest używana z ``parallelcp`` i ``make`` uzyskanie przetwarzania równoległego.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```

