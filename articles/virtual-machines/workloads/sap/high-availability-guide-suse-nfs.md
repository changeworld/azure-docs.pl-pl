---
title: Wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server | Dokumentacja firmy Microsoft
description: Wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 93644b9a3487906a27db70bfe82cceccdc7ab45c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707226"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i zainstalować serwer systemu plików NFS o wysokiej dostępności, który może służyć do przechowywania danych udostępnionych o wysokiej dostępności systemu SAP.
Ten przewodnik opisuje sposób konfigurowania serwera systemu plików NFS o wysokiej dostępności, który jest używany przez dwa systemy SAP NW1 i NW2. Nazwy zasobów (na przykład maszyny wirtualne, sieci wirtualnych) w przykładzie założono użycie [szablonu serwera plików SAP][template-file-server] prefiksem zasobów **prod**.

Najpierw przeczytaj następujące uwagi SAP i dokumenty

* Uwaga SAP [1928533], która zawiera:
  * Listę rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Informacje o pojemności ważne w przypadku rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP i systemu operacyjnego (OS) i kombinacji bazy danych
  * Wymagana wersja jądra SAP dla Windows i Linux w systemie Microsoft Azure

* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP obsługiwane przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2205917] zawiera zalecane ustawienia systemu operacyjnego SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [1944799] zawiera wskazówki dotyczące programu SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [2178632] zawiera szczegółowe informacje o metrykach wszystkie funkcje monitorowania zgłoszone dla rozwiązania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP, w systemie Linux na platformie Azure.
* Uwaga SAP [1984787] zawiera ogólne informacje o systemie SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP.
* [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux.
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][dbms-guide]
* [SUSE Linux Enterprise wysoką dostępność rozszerzenia 12 z dodatkiem SP3 najlepsze praktyki prowadnic][sles-hae-guides]
  * Magazyn NFS o wysokiej dostępności przy użyciu DRBD i program Pacemaker
* [SUSE Linux Enterprise Server dla SAP aplikacji 12 z dodatkiem SP3 najlepsze praktyki prowadnic][sles-for-sap-bp]
* [Rozszerzenie o wysokiej dostępności SUSE 12 z dodatkiem SP3 — informacje o wersji][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, oprogramowanie SAP NetWeaver wymaga serwera systemu plików NFS. Serwer systemu plików NFS jest skonfigurowana w klastrze oddzielonym i mogą być używane przez wiele systemów SAP.

![Oprogramowanie SAP NetWeaver wysokiej dostępności Przegląd](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Serwer systemu plików NFS używa dedykowanego hosta wirtualnego i wirtualnych adresów IP dla wszystkich systemów SAP, które używają tego serwera systemu plików NFS. Na platformie Azure modułu równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia.        

* Konfiguracja frontonu
  * Adres IP 10.0.0.4 NW1
  * Adres IP 10.0.0.5 NW2
* Konfiguracja zaplecza
  * Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią klastra systemu plików NFS
* Port sondy
  * Port 61000 NW1
  * Port 61001 NW2
* Reguł równoważenia obciążenia
  * 2049 TCP dla NW1
  * 2049 UDP dla NW1
  * 2049 TCP dla NW2
  * 2049 UDP dla NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurowanie wysokiej dostępności serwera systemu plików NFS

Możesz użyć szablonu usługi Azure z usługi GitHub do wdrażania wszystkich wymaganych zasobów platformy Azure, w tym usługi virtual machines, dostępność zestawu i modułu równoważenia obciążenia lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux przy użyciu szablonu platformy Azure

W portalu Azure Marketplace zawiera obraz dla SUSE Linux Enterprise Server 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych.
Można użyć jednego z szablonów szybkiego startu w usłudze GitHub do wdrażania wszystkich wymaganych zasobów. Szablon umożliwia wdrożenie maszyn wirtualnych, moduł równoważenia obciążenia, dostępności, ustaw itp. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablonu serwera plików SAP][template-file-server] w witrynie Azure portal   
1. Wprowadź następujące parametry
   1. Prefiks zasobów  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla zasobów, które są wdrażane.
   2. SAP System Count  
      Wprowadź liczbę systemów SAP, korzystających z tego serwera plików. Spowoduje to wdrożenie wymaganą ilość konfiguracji frontonu, reguły równoważenia obciążenia i sondę porty, dyski itp.
   3. Typ systemu operacyjnego  
      Wybierz jeden z dystrybucje systemu Linux. W tym przykładzie wybierz systemu SLES 12.
   4. Nazwa użytkownika administratora i hasła administratora  
      Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
   5. Identyfikator podsieci  
      Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda /subscriptions/ **&lt;identyfikator subskrypcji&gt;** /resourceGroups/ **&lt;nazwy grupy zasobów&gt;** /providers/ Microsoft.Network/virtualNetworks/ **&lt;nazwa sieci wirtualnej&gt;** /subnets/ **&lt;Nazwa podsieci&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pomocą witryny Azure portal

Najpierw należy utworzyć maszyny wirtualne, dla tego klastra systemu plików NFS. Następnie utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli zaplecza.

1. Tworzenie grupy zasobów
1. Create a Virtual Network
1. Tworzenie zestawu dostępności  
   Domena aktualizacji Maksymalny zestaw
1. Tworzenie maszyny wirtualnej 1 Użyj co najmniej SLES4SAP 12 z dodatkiem SP3, w tym przykładzie SLES4SAP 12 SP3 BYOS obrazów SLES For SAP aplikacji 12 z dodatkiem SP3 (BYOS) jest używany  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Tworzenie maszyny wirtualnej 2 użyj co najmniej SLES4SAP 12 z dodatkiem SP3, w tym przykładzie obraz SLES4SAP 12 z dodatkiem SP3 BYOS  
   SLES For SAP aplikacji 12 z dodatkiem SP3 (BYOS) jest używany.  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Do obu maszyn wirtualnych, należy dodać jeden dysk danych dla każdego systemu SAP.
1. Utwórz moduł równoważenia obciążenia (wewnętrzny)  
   1. Tworzenie adresów IP frontonu
      1. Adres IP 10.0.0.4 NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu nw1**)
         1. Ustawić przypisania jako statyczny i podaj adres IP (na przykład **10.0.0.4**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.5 NW2
         * Powtórz powyższe kroki dla NW2
   1. Utwórz pule zaplecza
      1. Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią klastra systemu plików NFS dla NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz opcję pule zaplecza, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **zaplecza nw1**)
         1. Kliknij przycisk Dodaj maszynę wirtualną
         1. Wybierz zestaw dostępności została utworzona wcześniej
         1. Wybierz maszyny wirtualne klastra systemu plików NFS
         1. Kliknij przycisk OK
      1. Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią klastra systemu plików NFS dla NW2
         * Powtórz powyższe kroki, aby utworzyć pulę zaplecza dla NW2
   1. Utworzenie sond kondycji.
      1. Port 61000 NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowego sondy kondycji (na przykład **nw1 hp**)
         1. Wybierz opcję TCP jako protokół, port 610**00**, Zachowaj 5 interwał i próg złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 61001 NW2
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla NW2
   1. Reguł równoważenia obciążenia
      1. 2049 TCP dla NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **nw1-lb-2049**)
         1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład **frontonu nw1**)
         1. Zachowaj protokołu **TCP**, wprowadź numer portu **2049**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Pamiętaj włączyć pływającego adresu IP**
         1. Kliknij przycisk OK
      1. 2049 UDP dla NW1
         * Powtórz powyższe kroki dla portu 2049 i UDP dla NW1
      1. 2049 TCP dla NW2
         * Powtórz powyższe kroki dla portu 2049 i TCP dla NW2
      1. 2049 UDP dla NW2
         * Powtórz powyższe kroki dla portu 2049 i UDP dla NW2

> [!IMPORTANT]
> Nie należy włączać czasowe TCP na maszynach wirtualnych Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji nie powiedzie się. Ustaw parametr **net.ipv4.tcp_timestamps** do **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) do tworzenia klastra program Pacemaker podstawowe dla tego serwera systemu plików NFS.

### <a name="configure-nfs-server"></a>Skonfiguruj serwer systemu plików NFS

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Server Włącz systemu plików NFS

   Tworzenie katalogu głównego systemu plików NFS eksportu wpisu

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]**  Instalują składniki drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Tworzenie partycji dla urządzeń drbd

   Wyświetl listę wszystkich dysków danych o dostępności

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   lun0  lun1
   ```

   Tworzenie partycji dla każdego dysku danych

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  LVM Tworzenie konfiguracji

   Lista wszystkich dostępnych partycji

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Utwórz woluminy LVM dla każdej partycji

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Skonfigurować drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Upewnij się, że plik drbd.conf zawiera następujące dwa wiersze

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Zmień konfigurację drbd globalne

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Dodaj następujące wpisy do sekcji netto i obsługi.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]**  Tworzenie urządzeń drbd systemu plików NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Wstaw konfiguracji dla nowego urządzenia drbd i zakończenia

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Wstaw konfiguracji dla nowego urządzenia drbd i zakończenia

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Utwórz urządzenie drbd i uruchom ją

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Pomiń początkową synchronizację

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Ustawić węzła podstawowego

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Poczekaj, aż nowe urządzenia drbd są synchronizowane.

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Tworzenia systemów plików na urządzeniach drbd

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Konfiguracji wykrywania split-brain drbd

   Za pomocą drbd synchronizować dane z jednego hosta na inny, może wystąpić tak zwany mózg podziału. Mózg podziału jest scenariusz, w których oba węzły klastra promowane drbd urządzenia jako podstawowy, a poszło zsynchronizowane. Może być rzadkich sytuacji, ale nadal chcesz obsługiwać i rozwiązać podziałem tak szybko, jak to możliwe. W związku z tym jest ważne, aby otrzymywać powiadomienia, gdy wystąpiło mózg podziału.

   Odczyt [dokumentacji oficjalnego drbd](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) na temat sposobu konfigurowania powiadomień mózg podziału.

   Istnieje również możliwość automatycznego odzyskiwania po scenariuszu mózg podziału. Aby uzyskać więcej informacji, przeczytaj [zasady odzyskiwania mózg podziału automatyczne](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurowanie klastra Framework

1. **[1]**  Dodać urządzenia drbd systemu plików NFS dla systemu SAP NW1 do konfiguracji klastra

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Dodać urządzenia drbd systemu plików NFS dla systemu SAP NW2 do konfiguracji klastra

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Wyłącz tryb konserwacji
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Kolejne kroki

* [Zainstaluj SAP ASCS i bazy danych](high-availability-guide-suse.md)
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]
