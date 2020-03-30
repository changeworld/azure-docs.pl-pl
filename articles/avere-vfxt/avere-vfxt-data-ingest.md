---
title: Przenoszenie danych do usługi Avere vFXT dla platformy Azure
description: Jak dodać dane do nowego woluminu magazynu do użytku z avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153722"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>Przenoszenie danych do klastra vFXT — równoległe pozyskiwania danych

Po utworzeniu nowego klastra vFXT pierwszym zadaniem może być przeniesienie danych na nowy wolumin magazynu na platformie Azure. Jeśli jednak zwykła metoda przenoszenia danych wydaje polecenie prostej kopii od jednego klienta, prawdopodobnie zobaczysz powolną wydajność kopiowania. Kopiowanie jednowątkowe nie jest dobrym rozwiązaniem do kopiowania danych do magazynu zaplecza klastra VFXT avere.

Ponieważ klaster Avere vFXT for Azure jest skalowalną pamięcią podręczną wielu klientów, najszybszym i najbardziej efektywnym sposobem kopiowania danych do niego jest wiele klientów. Ta technika równoległa pozyskiwania plików i obiektów.

![Diagram przedstawiający wielonakładnikowy, wielowątkowy ruch danych: W lewym górnym rogu ikona lokalnego magazynu sprzętu ma wiele strzałek pochodzących z niego. Strzałki wskazują na cztery maszyny klienckie. Z każdego komputera klienckiego trzy strzałki wskazują w kierunku Avere vFXT. W avere vFXT wiele strzałek wskazuje magazyn obiektów Blob.](media/avere-vfxt-parallel-ingest.png)

Polecenia ``cp`` ``copy`` lub, które są powszechnie używane do przesyłania danych z jednego systemu magazynu do drugiego są procesy jednowątkowe, które kopiują tylko jeden plik naraz. Oznacza to, że serwer plików jest pozyskiwania tylko jeden plik na raz - co jest stratą zasobów klastra.

W tym artykule opisano strategie tworzenia wielowątnikowego, wielowątkowego systemu kopiowania plików w celu przenoszenia danych do klastra Avere vFXT. Wyjaśniono pojęcia transferu plików i punkty decyzyjne, które mogą być używane do wydajnego kopiowania danych przy użyciu wielu klientów i prostych poleceń kopiowania.

Wyjaśnia również niektóre narzędzia, które mogą pomóc. Narzędzie ``msrsync`` może służyć do częściowego zautomatyzowania procesu dzielenia zestawu danych ``rsync`` na zasobniki i używania poleceń. Skrypt ``parallelcp`` jest innym narzędziem, które odczytuje katalog źródłowy i automatycznie wydaje polecenia kopiowania. Ponadto ``rsync`` narzędzie może służyć w dwóch fazach, aby zapewnić szybszą kopię, która nadal zapewnia spójność danych.

Kliknij łącze, aby przejść do sekcji:

* [Przykład ręcznej kopiowania](#manual-copy-example) — dokładne wyjaśnienie za pomocą poleceń kopiowania
* [Przykład rsync dwufazowy](#use-a-two-phase-rsync-process)
* [Częściowo zautomatyzowany (msrsync) przykład](#use-the-msrsync-utility)
* [Przykład kopiowania równoległego](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>Szablon maszyny wirtualnej ingestor danych

Szablon Menedżera zasobów jest dostępny w usłudze GitHub, aby automatycznie utworzyć maszynę wirtualną za pomocą narzędzi do pozyskiwania danych równoległych wymienionych w tym artykule.

![diagram przedstawiający wiele strzałek z magazynu obiektów blob, magazynu sprzętowego i źródeł plików platformy Azure. Strzałki wskazują na "data ingestor vm", a stamtąd wiele strzałek wskazuje na Avere vFXT](media/avere-vfxt-ingestor-vm.png)

Maszyna wirtualna ingestor danych jest częścią samouczka, w którym nowo utworzona maszyna wirtualna instaluje klaster VFXT Avere i pobiera jego skrypt bootstrap z klastra. Przeczytaj [Bootstrap danych ingestor VM, aby](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) uzyskać szczegółowe informacje.

## <a name="strategic-planning"></a>Planowanie strategiczne

Podczas projektowania strategii kopiowania danych równolegle, należy zrozumieć kompromisy w rozmiarze pliku, liczby plików i głębokości katalogu.

* Gdy pliki są małe, metryką zainteresowania są pliki na sekundę.
* Gdy pliki są duże (10MiBi lub większa), metryka zainteresowania jest bajtów na sekundę.

Każdy proces kopiowania ma szybkość przepływności i szybkość przesyłania plików, które mogą być mierzone przez czas długości polecenia kopiowania i faktoringu rozmiaru pliku i liczby plików. Wyjaśnienie, jak mierzyć stawki, wykracza poza zakres tego dokumentu, ale ważne jest, aby zrozumieć, czy będziesz mieć do czynienia z małymi czy dużymi plikami.

## <a name="manual-copy-example"></a>Przykład kopiowania ręcznego

Kopię wielowątkową można utworzyć na kliencie, uruchamiając więcej niż jedno polecenie kopiowania jednocześnie w tle względem wstępnie zdefiniowanych zestawów plików lub ścieżek.

Polecenie Linux/UNIX ``cp`` zawiera ``-p`` argument, aby zachować własność i metadane mtime. Dodanie tego argumentu do poniższych poleceń jest opcjonalne. (Dodanie argumentu zwiększa liczbę wywołań systemu plików wysyłanych przez klienta do docelowego systemu plików w celu modyfikacji metadanych).

W tym prostym przykładzie kopiuje dwa pliki równolegle:

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

Po wydaniu `jobs` tego polecenia polecenie pokaże, że dwa wątki są uruchomione.

### <a name="predictable-filename-structure"></a>Przewidywalna struktura nazwy pliku

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

### <a name="unknown-filename-structure"></a>Nieznana struktura nazwy pliku

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

### <a name="when-to-add-mount-points"></a>Kiedy dodać punkty instalacji

Po dość równoległych wątków będzie względem jednego punktu instalacji systemu plików docelowych, będzie punkt, w którym dodanie większej liczby wątków nie daje większą przepływność. (Przepływność będzie mierzona w plikach/sekundzie lub bajtach/sekundę, w zależności od typu danych). Lub co gorsza, nadmierne wątki może czasami spowodować degradacji przepływności.

W takim przypadku można dodać punkty instalacji po stronie klienta do innych adresów IP klastra vFXT przy użyciu tej samej ścieżki zdalnego instalowania systemu plików:

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

### <a name="when-to-add-clients"></a>Kiedy dodać klientów

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

### <a name="create-file-manifests"></a>Tworzenie manifestów plików

Po zrozumieniu powyższych metod (wiele wątków kopiowania na miejsce docelowe, wiele miejsc docelowych na klienta, wielu klientów na system plików źródłowych dostępnych do sieci), rozważ to zalecenie: Zbuduj manifesty plików, a następnie użyj ich z kopią polecenia na wielu klientach.

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
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
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

## <a name="use-a-two-phase-rsync-process"></a>Użyj dwufazowego procesu rsync

Standardowe ``rsync`` narzędzie nie działa dobrze do wypełniania magazynu w chmurze za pośrednictwem systemu Avere vFXT for Azure, ponieważ generuje dużą liczbę operacji tworzenia i zmiany nazwy plików w celu zagwarantowania integralności danych. Jednak można bezpiecznie użyć ``--inplace`` opcji, ``rsync`` aby pominąć bardziej staranną procedurę kopiowania, jeśli zastosujesz się do tej opcji z drugim uruchomieniem, które sprawdza integralność pliku.

Standardowa ``rsync`` operacja kopiowania tworzy plik tymczasowy i wypełnia go danymi. Jeśli transfer danych zakończy się pomyślnie, nazwa pliku tymczasowego zostanie zmieniona na oryginalną nazwę pliku. Ta metoda gwarantuje spójność, nawet jeśli pliki są dostępne podczas kopiowania. Ale ta metoda generuje więcej operacji zapisu, co spowalnia ruch plików za pośrednictwem pamięci podręcznej.

Opcja ``--inplace`` zapisuje nowy plik bezpośrednio w jego ostatecznej lokalizacji. Pliki nie są gwarantowane być spójne podczas przesyłania, ale to nie jest ważne, jeśli są gruntowania systemu magazynu do użycia później.

Druga ``rsync`` operacja służy jako sprawdzanie spójności przy pierwszej operacji. Ponieważ pliki zostały już skopiowane, druga faza jest szybkie skanowanie, aby upewnić się, że pliki w miejscu docelowym zgodne z plikami w źródle. Jeśli jakiekolwiek pliki nie są zgodne, są one kopiowane ponownie.

Obie fazy można wystawić razem w jednym poleceniu:

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

Ta metoda jest prostą i efektywną czasem metodą dla zestawów danych do liczby plików, które może obsłużyć wewnętrzny menedżer katalogów. (Jest to zazwyczaj 200 milionów plików dla klastra 3-węzłowego, 500 milionów plików dla klastra z sześciowęzłami itd.)

## <a name="use-the-msrsync-utility"></a>Użyj narzędzia msrsync

Narzędzie ``msrsync`` może również służyć do przenoszenia danych do back-end core filer dla klastra Avere. To narzędzie zostało zaprojektowane w celu optymalizacji ``rsync`` wykorzystania przepustowości przez uruchomienie wielu procesów równoległych. Jest on dostępny w <https://github.com/jbd/msrsync>GitHub w .

``msrsync``dzieli katalog źródłowy na oddzielne "zasobniki", ``rsync`` a następnie uruchamia poszczególne procesy na każdym zasobniku.

Wstępne testy przy użyciu czterordzeniowej maszyny Wirtualnej wykazały najlepszą wydajność podczas korzystania z 64 procesów. Użyj ``msrsync`` opcji, ``-p`` aby ustawić liczbę procesów na 64.

Można również użyć ``--inplace`` argumentu z ``msrsync`` poleceniami. Jeśli używasz tej opcji, należy rozważyć uruchomienie drugiego polecenia (jak [w przypadku rsync](#use-a-two-phase-rsync-process), opisane powyżej), aby zapewnić integralność danych.

``msrsync``można zapisywać tylko do i z woluminów lokalnych. Źródło i miejsce docelowe muszą być dostępne jako instalacje lokalne w sieci wirtualnej klastra.

Aby ``msrsync`` użyć do wypełniania woluminu chmury platformy Azure za pomocą klastra Avere, wykonaj następujące instrukcje:

1. Instalowanie ``msrsync`` i jego wymagania wstępne (rsync i Python 2.6 lub nowsze)
1. Określ całkowitą liczbę plików i katalogów do skopiowania.

   Na przykład użyj narzędzia ``prime.py`` Avere ```prime.py --directory /path/to/some/directory``` z argumentami <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>(dostępnego przez pobranie adresu URL ).

   Jeśli nie ``prime.py``używasz, można obliczyć liczbę ``find`` elementów za pomocą narzędzia GNU w następujący sposób:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Podziel liczbę elementów przez 64, aby określić liczbę elementów na proces. Użyj tego numeru ``-f`` z opcją, aby ustawić rozmiar zasobników po uruchomieniu polecenia.

1. Wydaj ``msrsync`` polecenie kopiowania plików:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Jeśli ``--inplace``używasz , dodaj drugie wykonanie bez opcji sprawdzania, czy dane są poprawnie kopiowane:

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Na przykład to polecenie jest przeznaczone do przenoszenia 11 000 plików w 64 procesach z /test/source-repozytorium do /mnt/vfxt/repozytorium:

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>Używanie skryptu kopiowania równoległego

Skrypt ``parallelcp`` może być również przydatny do przenoszenia danych do magazynu zaplecza klastra vFXT.

Poniższy skrypt doda plik `parallelcp`wykonywalny . (Ten skrypt jest przeznaczony dla Ubuntu; jeśli ``parallel`` używasz innej dystrybucji, należy zainstalować oddzielnie.)

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

### <a name="parallel-copy-example"></a>Przykład kopiowania równoległego

W tym przykładzie użyto skryptu kopiowania równoległego do kompilacji ``glibc`` przy użyciu plików źródłowych z klastra Avere.
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

Pliki źródłowe są przechowywane w punkcie instalacji klastra Avere, a pliki obiektów są przechowywane na lokalnym dysku twardym.

Ten skrypt używa skryptu kopiowania równoległego powyżej. Opcja ``-j`` jest używana ``parallelcp`` ``make`` z i uzyskać równoległość.

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
