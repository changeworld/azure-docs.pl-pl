---
title: Optymalizacja wydajności programu MySQL w systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zoptymalizować MySQL uruchomionych na maszynie wirtualnej platformy Azure (VM) z systemem Linux.
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
ms.openlocfilehash: 0ba85e82824bc257869d9801f342bd6dbb0402d2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247453"
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Optymalizacja wydajności programu MySQL na maszynach wirtualnych z systemem Linux platformy Azure
Istnieje wiele czynników, które mają wpływ na wydajności programu MySQL na platformie Azure, zarówno w wybór sprzętu i konfiguracji oprogramowania. Ten artykuł koncentruje się na Optymalizacja wydajności przy użyciu magazynu, systemu i konfiguracje bazy danych.

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager](../../../resource-manager-deployment-model.md) i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby uzyskać informacji na temat optymalizacji maszyny Wirtualnej systemu Linux przy użyciu modelu usługi Resource Manager, zobacz [Optymalizowanie maszyny Wirtualnej systemu Linux na platformie Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Korzystanie z RAID na maszynie wirtualnej platformy Azure
Magazyn jest kluczowym czynnikiem, który wpływa na wydajność bazy danych w środowiskach chmury. W porównaniu do pojedynczego dysku RAID zapewniają szybszy dostęp za pośrednictwem współbieżności. Aby uzyskać więcej informacji, zobacz [poziomy RAID standardowa](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Można zwiększyć przepustowość We/Wy dysku i czas reakcji operacji We/Wy na platformie Azure za pomocą macierzy RAID. Nasze testy laboratoryjne wskazują, że może być podwójny przepustowość We/Wy dysku i czas reakcji operacji We/Wy można zmniejszyć przez połowę średnio po podwaja się liczba dysków RAID (od dwóch do czterech, 4, 8, itp.). Zobacz [dodatek a.](#AppendixA) Aby uzyskać szczegółowe informacje.  

Oprócz We/Wy dysku wydajności programu MySQL zwiększa się wraz ze zwiększeniem poziom macierzy RAID.  Zobacz [dodatek B](#AppendixB) Aby uzyskać szczegółowe informacje.  

Można również wziąć pod uwagę rozmiar fragmentu. Ogólnie rzecz biorąc gdy ma większy rozmiar fragmentu, otrzymasz niższe obciążenie, szczególnie w przypadku dużych zapisy. Jednak gdy rozmiar fragmentu jest zbyt duży, może dodać dodatkowe obciążenie, który uniemożliwia zalet RAID. Bieżący rozmiar domyślny wynosi 512 KB, który wynosi optymalne dla większości środowisk produkcyjnych. Zobacz [dodatku C](#AppendixC) Aby uzyskać szczegółowe informacje.   

Istnieją limity liczby dysków, możesz dodać do typów inną maszynę wirtualną. Limity te są szczegółowo opisane w [maszyny wirtualnej i w chmurze rozmiary usługi na platformie Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx). Konieczne będzie czterech dołączonych dysków z danymi z przykładu RAID, w tym artykule, mimo że można skonfigurować pod kątem RAID z mniejszą liczbą dysków.  

W tym artykule założono, masz już utworzoną maszynę wirtualną systemu Linux i MYSQL zainstalowano i skonfigurowano. Aby uzyskać więcej informacji na temat rozpoczynania pracy Zobacz, jak zainstalować oprogramowanie MySQL na platformie Azure.  

### <a name="set-up-raid-on-azure"></a>Konfigurowanie macierzy RAID na platformie Azure
Poniższe kroki pokazują jak utworzyć RAID na platformie Azure przy użyciu witryny Azure portal. Można też skonfigurować RAID za pomocą skryptów środowiska Windows PowerShell.
W tym przykładzie cztery dyski zostaną skonfigurowane RAID 0.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Dodaj dysk danych do maszyny wirtualnej
W witrynie Azure portal przejdź do pulpitu nawigacyjnego, a następnie wybierz maszynę wirtualną, do którego chcesz dodać dysk z danymi. W tym przykładzie maszyna wirtualna jest mysqlnode1.  

<!--![Virtual machines][1]-->

Kliknij przycisk **dysków** a następnie kliknij przycisk **Attach New**.

![Maszyny wirtualne, Dodaj dysk](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Utwórz nowy dysk 500 GB. Upewnij się, że **preferencji pamięci podręcznej hosta** ustawiono **Brak**.  Gdy skończysz, kliknij przycisk **OK**.

![Dołączanie pustego dysku](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Spowoduje to dodanie jeden pusty dysk o połączenie z maszyną wirtualną. Powtórz ten krok trzy razy, aby mieć cztery dyski danych w macierzy RAID.  

Możesz zobaczyć dodane dyski na maszynie wirtualnej, sprawdzając dziennik komunikatów jądra. Na przykład aby zobaczyć to w systemie Ubuntu, należy użyć następującego polecenia:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Tworzenie macierzy RAID za pomocą dodatkowych dysków
W poniższych krokach opisano sposób [Konfigurowanie programowej macierzy RAID w systemie Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Jeśli używasz XFS systemu plików, wykonaj następujące czynności, po utworzeniu RAID.
>
>

Aby zainstalować XFS systemie Debian, Ubuntu lub mennic systemu Linux, należy użyć następującego polecenia:  

    apt-get -y install xfsprogs  

Aby zainstalować XFS na RHEL, Fedora i CentOS, użyj następującego polecenia:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Skonfiguruj nową ścieżkę magazynu
Użyj następującego polecenia, aby skonfigurować nową ścieżkę magazynu:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Skopiuj oryginalne dane na nową ścieżkę magazynu
Użyj następującego polecenia, aby skopiować dane na nową ścieżkę magazynu:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Zmodyfikować uprawnienia, dzięki czemu mogą uzyskiwać dostęp do MySQL (Odczyt i zapis) dysku danych
Aby zmodyfikować uprawnienia, użyj następującego polecenia:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Dostosuj algorytm planowania operacji We/Wy dysku
Linux implementuje cztery rodzaje operacji We/Wy planowania algorytmów:  

* Algorytm aktualizujący nie działa (operacja: No)
* Algorytm ostateczny termin (ostatecznego terminu)
* Całkowicie uczciwe algorytm kolejkowania wiadomości (CFQ)
* Algorytm okresu budżetu (Anticipatory)  

Można wybrać różne planiści we/wy w różnych scenariuszach w celu zoptymalizowania wydajności. W środowisku całkowicie swobodnego dostępu nie istnieje duża różnica między CFQ a ostatecznym terminem algorytmów wydajności. Firma Microsoft zaleca, ustawić środowisko bazy danych MySQL na ostateczny termin stabilności. W przypadku partii sekwencyjnych operacji We/Wy CFQ może zmniejszyć wydajność operacji We/Wy dysku.   

Dyski SSD i innych urządzeń aktualizujący nie działa lub ostatecznego terminu może osiągnąć lepszą wydajność niż domyślnego harmonogramu.   

Przed jądra 2.5 domyślny algorytm planowania operacji We/Wy jest terminu ostatecznego. Począwszy od jądra 2.6.18 CFQ stało się domyślny algorytm planowania operacji We/Wy.  Można określić tego ustawienia w czasie rozruchu jądra lub dynamicznie zmodyfikować to ustawienie, gdy system jest uruchomiony.  

Poniższy przykład pokazuje, jak sprawdzić i Ustaw domyślnego harmonogramu algorytm aktualizujący nie działa w systemach z rodziny Debian dystrybucji.  

### <a name="view-the-current-io-scheduler"></a>Wyświetl bieżący harmonogram we/wy
Aby wyświetlić harmonogram, uruchom następujące polecenie:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Zostaną wyświetlone następujące dane wyjściowe, co oznacza bieżącego harmonogramu:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Zmień bieżące urządzenie algorytm planowania operacji We/Wy (/ dev/sda)
Uruchom następujące polecenia, aby zmienić bieżące urządzenie:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Ustawienie to samo /dev/sda nie jest przydatne. Umieszcza się na wszystkich dyskach danych zawierającej bazy danych.  
>
>

Powinny zostać wyświetlone następujące dane wyjściowe, wskazujący, że ten grub.cfg został przebudowany pomyślnie i czy domyślnego harmonogramu została zaktualizowana do aktualizujący nie działa:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Dla systemów z rodziny dystrybucji firmy Red Hat potrzebne są następujące polecenie:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Skonfiguruj ustawienia operacji pliku systemu
Jeden najlepszym rozwiązaniem jest wyłączenie *atime* funkcji rejestrowania w systemie plików. Atime jest czas ostatniego dostępu do pliku. Zawsze, gdy plik jest dostępny, system plików rejestruje sygnaturę czasową w dzienniku. Jednak te informacje są rzadko używane. Można ją wyłączyć, jeśli nie potrzebujesz, co zmniejsza całkowity czas dostępu do dysku.  

Aby wyłączyć rejestrowanie atime, musisz zmodyfikować plik system konfiguracji pliku/etc / fstab i Dodaj **noatime** opcji.  

Na przykład Edytuj plik/etc/fstab vim Dodawanie noatime, jak pokazano w następującym przykładzie:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Następnie należy ponownie zainstalować system plików za pomocą następującego polecenia:  

    mount -o remount /RAID0

Zmodyfikowane wynik testu. Po zmodyfikowaniu pliku testu, czas dostępu nie jest aktualizowana. W poniższych przykładach pokazano, jak kod wygląda przed i po modyfikacji.

Przed:        

![Kod przed modyfikacją dostępu][5]

Po:

![Kod po modyfikacji dostępu][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Zwiększyć maksymalną liczbę systemu obsługuje współbieżność wysoka
MySQL to wysokiej współbieżności bazy danych. Domyślna liczba równoczesnych dojścia jest 1024 dla systemu Linux, które nie zawsze jest wystarczająca. Wykonaj następujące kroki, aby zwiększyć maksymalną współbieżnych dojść systemu do obsługi wysokiej współbieżności środowiska MySQL.

### <a name="modify-the-limitsconf-file"></a>Zmodyfikuj plik limits.conf
Aby zwiększyć maksymalny dozwolony obsługuje jednoczesnych, należy dodać następujące cztery wiersze w pliku /etc/security/limits.conf. Należy pamiętać, że 65536 maksymalną liczbę, który może obsługiwać system.   

    * elastyczne nofile 65536
    * twarde nofile 65536
    * elastyczne nproc 65536
    * twarde nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Aktualizacja systemu pod kątem nowych limitów
Aby zaktualizować systemu, uruchom następujące polecenia:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Upewnij się, że limity są aktualizowane w czasie rozruchu
W pliku /etc/rc.local należy umieścić następujące polecenia uruchomienia, aby zostanie zastosowane w czasie rozruchu.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Optymalizacja bazy danych MySQL
Aby skonfigurować serwer MySQL na platformie Azure, można użyć tej samej strategii dostrajaniu wydajności, używane na maszynie lokalnej.  

Główne zasady optymalizacji operacji We/Wy są:   

* Zwiększ rozmiar pamięci podręcznej.
* Skróć czas reakcji operacji We/Wy.  

W celu zoptymalizowania ustawień serwera MySQL, możesz zaktualizować plik my.cnf, która jest domyślny plik konfiguracji dla komputerów klienckich i serwera.  

Główne czynniki wpływające na wydajność MySQL są następujące elementy konfiguracji:  

* **innodb_buffer_pool_size**: Pula buforów zawiera buforowane dane i indeksu. Jest to zazwyczaj równa 70 procent pamięci fizycznej.
* **innodb_log_file_size**: jest to rozmiar dziennika ponawiania. Powtórz dzienniki służy do upewnij się, że operacje zapisu są szybkie, niezawodne i możliwe do odzyskania po awarii. To jest równa 512 MB, co zapewni odpowiednią ilość miejsca dla operacji zapisu rejestrowania.
* **max_connections**: czasami aplikacje nie zamykaj połączeń prawidłowo. Większa wartość będzie nadać serwerowi więcej czasu na odtwarzanie bezczynny połączeń. Maksymalna liczba połączeń jest 10 000, ale to też zalecane maksimum wynosi 5000.
* **Innodb_file_per_table**: to ustawienie włącza lub wyłącza możliwość aparatu InnoDB przechowywania tabel w oddzielnych plikach. Włącz opcję, aby efektywnie stosowane kilka operacji zaawansowaną administrację. Z punktu widzenia wydajności można zwiększyć szybkość transmisji miejsce tabeli i zoptymalizować wydajność zarządzania pozostałości. To ustawienie zalecane dla tej opcji ma wartość ON.</br></br>
Z programu MySQL 5.6 domyślne ustawienie ma wartość ON, dzięki czemu jest wymagana żadna akcja. W przypadku starszych wersji ustawieniem domyślnym jest WYŁĄCZONY. Ustawienie należy zmienić przed załadowaniem danych, ponieważ dotyczy tylko nowo utworzonego tabelami.
* **innodb_flush_log_at_trx_commit**: wartość domyślna to 1, przy użyciu zakresu ustawione na 0 ~ 2. Wartość domyślna to najbardziej odpowiedniej opcji dla autonomicznej bazy danych MySQL. Ustawienie 2 umożliwia większość integralność danych i nadaje się do serwera głównego w klaster programu MySQL. Ustawienie 0 umożliwia utraty danych, co może wpłynąć na niezawodność (w niektórych przypadkach lepszą wydajność) i nadaje się do podrzędnego w klaster programu MySQL.
* **Innodb_log_buffer_size**: Bufor dziennika umożliwia transakcji do uruchomienia bez konieczności opróżniania dziennika na dysku, zanim zatwierdzania transakcji. Jeśli jednak ma duży obiekt binarny lub pola tekstowego, pamięć podręczna zostanie wchłonięta szybko i we/wy dysku częste zostaną wyzwolone. Jest lepiej zwiększyć rozmiar buforu, jeśli Innodb_log_waits zmiennej stanu nie jest 0.
* **query_cache_size**: najlepszym rozwiązaniem jest wyłączenie go od samego początku. Wartość query_cache_size 0 (jest to domyślne ustawienie w MySQL 5.6) i przyspieszyć działanie zapytań za pomocą innych metod.  

Zobacz [dodatku D](#AppendixD) porównanie wydajności przed i po optymalizacji.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Włącz dziennik wolnych zapytań programu MySQL do analizowania wąskie gardło
Dziennik wolnych zapytań MySQL może pomóc w identyfikacji spowalniać zapytania for MySQL. Po włączeniu dziennik wolnych zapytań MySQL, można użyć narzędzia MySQL, takie jak **mysqldumpslow** do identyfikowania wąskich gardeł wydajności.  

Domyślnie to nie jest włączona. Włączanie dzienników wolnych zapytań może używać niektórych zasobów procesora CPU. Zaleca się, że możesz włączyć tę opcję tymczasowo dla rozwiązywania problemów z wydajnością. Aby włączyć dziennik dotyczący wolnego zapytania:

1. Zmodyfikuj plik my.cnf, dodając następujące wiersze na końcu:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Uruchom ponownie serwer MySQL.

        service  mysql  restart

3. Sprawdź, czy ustawienie jest zastosowaniem przy użyciu **Pokaż** polecenia.

![NA wolne — — dziennik zapytań][7]   

![Wyniki wolne — — dziennik zapytań][8]

W tym przykładzie widać, że włączono funkcję wolnych zapytań. Następnie można użyć **mysqldumpslow** narzędzie, aby określić wąskie gardła wydajności i optymalizacji wydajności, takie jak dodanie indeksów.

## <a name="appendices"></a>Dodatki
Poniżej przedstawiono przykładowe wydajności testu dane utworzone w środowisku laboratoryjnym docelowych. Zapewniają one ogólne na temat tendencji dane wydajności, o wydajności różnych metod dostosowywania. Wyniki mogą się różnić w różnych wersjach środowiska lub produktu.

### <a name="AppendixA"></a>Dodatek a.  
**Wydajność dysku (IOPS) przy użyciu różnych poziomów macierzy RAID**

![Operacje We/Wy dysku, za pomocą różnych poziomów macierzy RAID][9]

**Polecenia testu**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> Obciążenie tego testu używa 64 wątki, próby osiągać górny limit RAID.
>
>

### <a name="AppendixB"></a>Załącznik B  
**Porównanie wydajności (przepływność) MySQL z różnych poziomów macierzy RAID**   
(XFS systemu plików)

![Porównanie wydajności MySQL z różnych poziomów macierzy RAID][10]  
![Porównanie wydajności MySQL z różnych poziomów macierzy RAID][11]

**Polecenia testu**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Porównanie wydajności (OLTP) MySQL z różnych poziomów macierzy RAID**  
![Porównanie wydajności (OLTP) MySQL z różnych poziomów macierzy RAID][12]

**Polecenia testu**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Dodatek C   
**Porównanie wydajności (IOPS) dysku dla różnych rozmiarach**  
(XFS systemu plików)

![][13]

**Polecenia testu**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Rozmiary plików używane do testowania tego 30 GB do 1 GB, odpowiednio, za pomocą macierzy RAID 0 (4 dyski) XFS systemu plików.

### <a name="AppendixD"></a>Załącznik D  
**Porównanie wydajności (przepływność) MySQL przed i po optymalizacji**  
(XFS File System)

![Porównanie wydajności (przepływność) MySQL przed i po optymalizacji][14]

**Polecenia testu**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Ustawienie konfiguracji domyślnej i Optymalizacja, jest następujący:**

| Parametry | Domyślne | Optymalizacja |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Brak |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Aby uzyskać bardziej szczegółowe [parametry konfiguracji optymalizacji](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), odnoszą się do [MySQL oficjalnych instrukcji](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Środowisko testowe**  

| Sprzęt | Szczegóły |
| --- | --- |
| Procesor CPU |AMD Opteron(tm) procesora 4171 HE / 4 rdzenie |
| Memory (Pamięć) |14 GB |
| Dysk |10 GB/dysk |
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

