---
title: Optymalizowanie MySQL w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zoptymalizować MySQL uruchomionych na maszynie wirtualnej platformy Azure (VM) systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 447532452a848c88fd927f42e4263cef4742dd89
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Optymalizowanie MySQL na maszynach wirtualnych systemu Linux platformy Azure
Istnieje wiele czynników, które mają wpływ na wydajność MySQL na platformie Azure, zarówno w wybór sprzęt wirtualny i konfiguracji oprogramowania. Ten artykuł dotyczy Optymalizacja wydajności za pomocą magazynu, systemu i konfiguracje bazy danych.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../resource-manager-deployment-model.md) i klasycznym. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Informacje o optymalizacji maszyny Wirtualnej systemu Linux z modelem Menedżera zasobów, zobacz [optymalizacji sieci maszyny Wirtualnej systemu Linux na platformie Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Korzystanie z RAID na maszynie wirtualnej platformy Azure
Magazyn jest kluczowym czynnikiem, która wpływa na wydajność bazy danych w środowisku chmury. W porównaniu do jednego dysku RAID zapewnia szybszy dostęp za pośrednictwem współbieżności. Aby uzyskać więcej informacji, zobacz [poziomy RAID standardowe](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Można zwiększyć przepływność We/Wy dysku i czas odpowiedzi operacji We/Wy na platformie Azure za pomocą macierzy RAID. Nasze testy laboratorium wskazują, że przepustowość operacji We/Wy dysku może być podwójny i czas odpowiedzi operacji We/Wy, można zmniejszyć przez połowę średnio gdy liczba dysków RAID jest podwójny (od dwóch do czterech czterech do ośmiu, itp.). Zobacz [dodatek a.](#AppendixA) szczegółowe informacje.  

Oprócz We/Wy dysku wydajność MySQL zwiększa zwiększyć poziom RAID.  Zobacz [dodatek B](#AppendixB) szczegółowe informacje.  

Można też wziąć pod uwagę rozmiar fragmentu. Ogólnie rzecz biorąc Jeśli masz większy rozmiar fragmentu otrzymasz niższe nakładów pracy, zwłaszcza w przypadku dużych operacji zapisu. Jednak jeśli rozmiar segmentu jest zbyt duży, może dodawać dodatkowe obciążenie, który uniemożliwia wykorzystaniu RAID. Bieżący rozmiar domyślny to 512 KB, który wynosi optymalne dla większości środowisk produkcyjnych. Zobacz [dodatku C](#AppendixC) szczegółowe informacje.   

Istnieją ograniczenia liczby dysków dla typów inną maszynę wirtualną można dodać. Te limity wyszczególnione w [maszyny wirtualnej i w chmurze rozmiary usługi Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Konieczne będzie czterech dysków dołączonych danych do wykonania w przykładzie RAID, w tym artykule, mimo że można skonfigurować RAID przy użyciu mniejszej liczby dysków.  

W tym artykule przyjęto założenie, zostały już utworzone maszyny wirtualnej systemu Linux i MYSQL zainstalowany i skonfigurowany. Aby uzyskać więcej informacji na wprowadzenie Zobacz jak zainstalować MySQL na platformie Azure.  

### <a name="set-up-raid-on-azure"></a>— Konfiguracja RAID na platformie Azure
Poniższe kroki pokazują, jak utworzyć RAID na platformie Azure przy użyciu portalu Azure. Można również skonfigurować RAID za pomocą skryptów środowiska Windows PowerShell.
W tym przykładzie będzie skonfigurowanie RAID 0 z czterech dysków.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Dodaj dysk danych do maszyny wirtualnej
W portalu Azure przejdź do pulpitu nawigacyjnego, a następnie wybierz maszynę wirtualną, do której chcesz dodać dysk z danymi. W tym przykładzie maszyna wirtualna jest mysqlnode1.  

<!--![Virtual machines][1]-->

Kliknij przycisk **dysków** , a następnie kliknij przycisk **dołączyć nowy**.

![Maszyny wirtualne, Dodaj dysk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Utwórz nowy dysk 500 GB. Upewnij się, że **hosta pamięci podręcznej preferencji** ustawiono **Brak**.  Po zakończeniu kliknij przycisk **OK**.

![Dołącz pusty dysk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Spowoduje to dodanie jednego pusty dysk do maszyny wirtualnej. Powtórz ten krok trzy razy, aby mieć cztery dyski danych macierzy RAID.  

Analizując dziennik komunikatów jądra widać dodane dyski na maszynie wirtualnej. Na przykład aby wyświetlić to Ubuntu, użyj następującego polecenia:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Utwórz RAID z dodatkowych dysków
W poniższych krokach opisano sposób [należy skonfigurować oprogramowanie RAID w systemie Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Jeśli korzystasz z systemu plików XFS, należy wykonać następujące czynności, po utworzeniu RAID.
>
>

Aby zainstalować XFS Debian, Ubuntu lub mennic Linux, użyj następującego polecenia:  

    apt-get -y install xfsprogs  

Aby zainstalować XFS Fedora, CentOS lub RHEL, użyj następującego polecenia:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Konfigurowanie nowej ścieżki do magazynu
Aby skonfigurować nową ścieżkę magazynu, użyj następującego polecenia:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Skopiuj oryginalnych danych do nowej ścieżki do magazynu
Aby skopiować dane do nowej ścieżki do magazynu, użyj następującego polecenia:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Modyfikowanie uprawnień dostępu do MySQL (odczytu i zapisu) dysk z danymi
Aby zmodyfikować uprawnienia, użyj następującego polecenia:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Dostosuj algorytm planowania operacji We/Wy dysku
Linux implementuje cztery typy operacji We/Wy planowania algorytmów:  

* Operacja algorytm (operacja: No)
* Algorytm ostateczny termin (terminu)
* Całkowicie odpowiedniego algorytmu kolejkowania wiadomości (CFQ)
* Algorytm okresu budżetu (Anticipatory)  

Można wybrać różne planiści we/wy w różnych scenariuszach w celu zoptymalizowania wydajności. W środowisku dostępu losowy nie istnieje znacząca różnica między CFQ a ostatecznym terminem algorytmów wydajności. Firma Microsoft zaleca ustawienie środowiska bazy danych MySQL na termin ostateczny stabilność. W przypadku partii sekwencyjnych operacji We/Wy, CFQ może zmniejszyć wydajność We/Wy dysku.   

Dysków SSD i innych urządzeń operacja lub ostatecznego terminu można osiągnąć lepszą wydajność niż harmonogram domyślny.   

Przed jądra 2.5 domyślny algorytm planowania operacji We/Wy jest terminu. Począwszy od jądra 2.6.18 CFQ stał się domyślny algorytm planowania operacji We/Wy.  Można określić tego ustawienia w czasie rozruchu jądra lub dynamicznie zmodyfikować to ustawienie, gdy w systemie.  

W poniższym przykładzie pokazano, jak można sprawdzić i ustaw harmonogram domyślny algorytm operacja rodziny Debian dystrybucji.  

### <a name="view-the-current-io-scheduler"></a>Wyświetlanie bieżącego harmonogramu we/wy
Aby wyświetlić harmonogramu, uruchom następujące polecenie:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Zostaną wyświetlone następujące dane wyjściowe, co oznacza bieżącego harmonogramu:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Zmień bieżącego urządzenia (/ dev/sda) algorytmu planowania operacji We/Wy
Uruchom następujące polecenia, aby zmienić bieżącego urządzenia:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Ustawienie to dla /dev/sda nie jest użyteczne. Należy ją ustawić na wszystkich dyskach danych lokalizację bazy danych.  
>
>

Powinny być widoczne następujące dane wyjściowe, wskazujące, że grub.cfg został przebudowany pomyślnie i czy operacja zaktualizowano harmonogram domyślny:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Red Hat dystrybucji rodziny potrzebne są następujące polecenie:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Skonfiguruj ustawienia operacji pliku systemu
Jeden najlepszym rozwiązaniem jest wyłączenie *atime* funkcji rejestrowania w systemie plików. Atime jest czas ostatniego dostępu do pliku. Zawsze, gdy plik jest dostępny, system plików rejestruje w dzienniku sygnaturę czasową. Jednak te informacje jest rzadko używana. Można ją wyłączyć, jeśli nie jest potrzebna, co zmniejsza całkowity czas dostępu do dysku.  

Aby wyłączyć rejestrowanie atime, musisz zmodyfikować plik system konfiguracji plik/etc / fstab i Dodaj **noatime** opcji.  

Na przykład edytować plik /etc/fstab vim, dodając noatime, jak pokazano w poniższym przykładzie:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Następnie należy ponownie zainstalować system plików za pomocą następującego polecenia:  

    mount -o remount /RAID0

Zmodyfikowane wyniku testu. Po zmodyfikowaniu pliku testowego czas dostępu nie jest aktualizowana. W poniższych przykładach pokazano kod wygląd przed i po modyfikacji.

Przed:        

![Kod przed modyfikacją dostępu][5]

Po:

![Kod po modyfikacji dostępu][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Zwiększ maksymalną liczbę systemu obsługuje dla wysokiej współbieżności
MySQL jest wysoka współbieżności bazy danych. Domyślna liczba równoczesnych dojścia jest 1024 dla systemu Linux, które nie zawsze jest wystarczające. Wykonaj następujące kroki, aby zwiększyć maksymalną współbieżnych dojść systemu do obsługi wysokiej współbieżności MySQL.

### <a name="modify-the-limitsconf-file"></a>Modyfikowanie pliku limits.conf
Aby zwiększyć maksymalny dozwolony dojść jednoczesnych, Dodaj następujące cztery wiersze w pliku /etc/security/limits.conf. Należy pamiętać, że 65536 maksymalną liczbę, który może obsługiwać system.   

    * elastyczne nofile 65536
    * twarde nofile 65536
    * elastyczne nproc 65536
    * twarde nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Zaktualizuj system nowych limitów
Aby zaktualizować system, uruchom następujące polecenia:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Upewnij się, że limity są aktualizowane w czasie rozruchu
W pliku /etc/rc.local należy umieścić następujące polecenia uruchamiania, aby zacznie obowiązywać w czasie rozruchu.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Optymalizacja bazy danych MySQL
Aby skonfigurować MySQL na platformie Azure, można użyć tej samej strategii dostrajania wydajności używanego na maszynie lokalnej.  

Główne zasady optymalizacji operacji We/Wy są:   

* Zwiększ rozmiar pamięci podręcznej.
* Zmniejsz we/wy, czas odpowiedzi.  

Aby zoptymalizować MySQL ustawienia serwera, należy zaktualizować plik my.cnf, który jest domyślny plik konfiguracji dla komputerów klienckich i serwera.  

Główne czynniki wpływające na wydajność MySQL są następujące elementy konfiguracji:  

* **innodb_buffer_pool_size**: Pula buforów zawiera buforowane dane i indeksu. Zwykle to wynosi 70 procent pamięci fizycznej.
* **innodb_log_file_size**: rozmiar dziennika ponownego wykonywania. Powtórz dzienniki służy do zapewnienia, że operacje zapisu są szybkie, niezawodne i możliwe do odzyskania po awarii. To jest równa 512 MB, co zapewni wystarczająco dużo miejsca do rejestrowania operacji zapisu.
* **max_connections**: czasami aplikacje nie zamykaj połączeń poprawnie. Im większa wartość zapewni serwera więcej czasu do odtworzenia idled połączenia. Maksymalna liczba połączeń jest 10 000, ale zalecana maksymalna liczba to 5000.
* **Innodb_file_per_table**: to ustawienie włącza lub wyłącza możliwość przechowywania tabel w oddzielnych plikach InnoDB. Włącz opcję, aby wydajnie stosowane kilka operacji zaawansowane administracji. Z punktu widzenia wydajności można zwiększyć szybkość transmisji miejsca tabeli i zoptymalizować wydajność zarządzania pozostałości. To ustawienie zalecane dla tej opcji ma wartość ON.</br></br>
Z MySQL 5.6 domyślne ustawienie ma wartość ON, więc nie jest wymagana żadna akcja. We wcześniejszych wersjach ustawieniem domyślnym jest WYŁĄCZONY. Ustawienie należy zmienić przed załadowaniem danych, ponieważ dotyczy tylko tabele nowo utworzony.
* **innodb_flush_log_at_trx_commit**: wartość domyślna to 1, w zakresie równa 0 ~ 2. Wartość domyślna to najbardziej odpowiednią opcją do autonomicznej bazy danych MySQL. Ustawienie 2 Włącza większości integralność danych i nadaje się do głównego w klaster programu MySQL. Ustawienie 0 umożliwia utraty danych, które mogą wpłynąć na niezawodność (w niektórych przypadkach z lepszą wydajność) i nadaje się do podrzędnych w klaster programu MySQL.
* **Innodb_log_buffer_size**: Bufor dziennika umożliwia transakcji uruchomić bez konieczności opróżnić dziennika na dysku, aby zatwierdzić transakcji. Jednak jeśli istnieje dużego obiektu binarnego lub pola tekstowego, pamięci podręcznej będą działały szybko i we/wy dysku częste zostanie wyzwolony. Jest lepiej Zwiększ rozmiar buforu, jeśli Innodb_log_waits zmiennej stanu nie jest 0.
* **query_cache_size**: najlepszym rozwiązaniem jest wyłączenie go od samego początku. Ustaw query_cache_size 0 (jest to domyślne ustawienie w MySQL 5.6) i skorzystać z innych metod w celu przyspieszenia zapytania.  

Zobacz [dodatku D](#AppendixD) porównanie przed i po optymalizacji wydajności.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Włącz dziennik MySQL powolne zapytań do analizowania wąskie gardło
Dziennik zapytań powolne MySQL mogą pomóc w identyfikacji powolne zapytania dla programu MySQL. Po włączeniu dziennika powolne zapytania MySQL, można użyć narzędzia MySQL, takie jak **mysqldumpslow** do identyfikowania wąskie gardło.  

Domyślnie to nie jest włączona. Włączenie dziennika powolne zapytanie może używać niektórych zasobów Procesora. Zaleca się włączenie to tymczasowo do rozwiązywania problemów wąskich gardeł wydajności. Aby włączyć dziennik zapytań wolne:

1. Należy zmodyfikować plik my.cnf, dodając następujące wiersze na końcu:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Uruchom ponownie serwer MySQL.

        service  mysql  restart

3. Sprawdź, czy ustawienie trwa efekt za pomocą **Pokaż** polecenia.

![Powolna zapytania dziennika na][7]   

![Powolna zapytania dziennika wyników][8]

W tym przykładzie widać, że włączono funkcję powolne zapytania. Następnie można użyć **mysqldumpslow** narzędzia, aby określić wąskich gardeł wydajności i optymalizacji wydajności, takie jak dodanie indeksów.

## <a name="appendices"></a>Dodatki
Poniżej przedstawiono przykładowe wydajności testu dane utworzone w środowisku laboratoryjnym docelowych. Zapewniają ogólne na temat tendencji danych wydajności z różnych dostrajanie podejścia wydajności. Wyniki mogą być różne w różnych wersjach środowiska lub produkt.

### <a name="AppendixA"></a>Dodatek A  
**Wydajność dysku (IOPS) z różnych poziomów macierzy RAID**

![Dysk IOPS z różnych poziomów macierzy RAID][9]

**Polecenia testu**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Obciążenie tego testu używa 64 wątki, próba nawiązania połączenia górny limit RAID.
>
>

### <a name="AppendixB"></a>Dodatek B  
**Porównanie wydajności (przepływność) MySQL z różnych poziomów macierzy RAID**   
(XFS w systemie plików)

![Porównanie wydajności MySQL z różnych poziomów macierzy RAID][10]  
![Porównanie wydajności MySQL z różnych poziomów macierzy RAID][11]

**Polecenia testu**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Porównanie wydajności (OLTP) MySQL z różnych poziomów macierzy RAID**  
![Porównanie wydajności (OLTP) MySQL z różnych poziomów macierzy RAID][12]

**Polecenia testu**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Dodatek C   
**Dysk porównanie wydajności (IOPS) dla różnych rozmiarach**  
(XFS w systemie plików)

![][13]

**Polecenia testu**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Rozmiary plików używane do testowania tego 30 GB i 1 GB, odpowiednio, macierz RAID 0 (4 dyski) XFS systemu plików.

### <a name="AppendixD"></a>Dodatek D  
**Porównanie wydajności (przepływność) MySQL przed i po optymalizacji**  
(XFS File System)

![Porównanie wydajności (przepływność) MySQL przed i po optymalizacji][14]

**Polecenia testu**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Ustawienie konfiguracji domyślnej i optymalizacji wygląda następująco:**

| Parametry | Domyślne | Optymalizacja |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Brak |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Aby uzyskać bardziej szczegółowe [parametry konfiguracji optymalizacji](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), odwoływać się do [instrukcje oficjalnego MySQL](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Środowisko testowe**  

| Sprzęt | Szczegóły |
| --- | --- |
| Procesor CPU |AMD Opteron(tm) procesora 4171 HE / 4 rdzenie |
| Memory (Pamięć) |14 GB |
| Dysk |10 GB/dysku |
| System operacyjny |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

