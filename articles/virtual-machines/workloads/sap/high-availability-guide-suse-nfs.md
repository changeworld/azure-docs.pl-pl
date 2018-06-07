---
title: Wysoka dostępność dla systemu plików NFS na maszynach wirtualnych Azure w systemie SUSE Linux Enterprise Server | Dokumentacja firmy Microsoft
description: Wysoka dostępność dla systemu plików NFS na maszynach wirtualnych Azure w systemie SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: sedusch
ms.openlocfilehash: 004baee6f3b1ed016ee0336a86d5ea3909d8f255
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656231"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność dla systemu plików NFS na maszynach wirtualnych Azure w systemie SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i zainstaluj serwer systemu plików NFS o wysokiej dostępności, który może służyć do przechowywania danych udostępnionych o wysokiej dostępności systemu SAP.
Ten przewodnik zawiera opis sposobu konfigurowania serwera NFS o wysokiej dostępności, który jest używany przez dwa systemy SAP, NW1 i NW2. Nazw zasobów (na przykład maszyn wirtualnych, sieci wirtualne), w tym przykładzie założono, że użyto [szablon serwera SAP pliku] [ template-file-server] z prefiksem zasobów **produkcyjnego**.

Przeczytaj następujące uwagi SAP i dokumenty najpierw

* Uwaga SAP [1928533], który zawiera:
  * Lista rozmiary maszyn wirtualnych Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Informacje o rozmiary maszyn wirtualnych Azure ważne pojemności
  * Obsługiwane oprogramowanie SAP i kombinacji bazy danych i systemu operacyjnego (OS)
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure

* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP SAP, obsługiwane na platformie Azure.
* Uwaga SAP [2205917] zawiera zalecane ustawienia systemu operacyjnego SUSE Linux Enterprise Server dla programu SAP aplikacji
* Uwaga SAP [1944799] ma SAP HANA wytyczne dla SUSE Linux Enterprise Server dla programu SAP aplikacji
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkie funkcje monitorowania metryki zgłoszone dla SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Uwaga SAP [1984787] zawiera ogólne informacje o systemie SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów Azure rozszerzone monitorowanie rozszerzenia dla programu SAP.
* [SAP społeczności WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje SAP dla systemu Linux.
* [Azure maszyn wirtualnych, planowania i wdrażania dla SAP w systemie Linux][planning-guide]
* [Maszyny wirtualne Azure wdrożenie SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Azure maszyn wirtualnych systemu DBMS wdrożenie SAP w systemie Linux][dbms-guide]
* [SAP HANA SR zoptymalizowana scenariusza][suse-hana-ha-guide]  
  Przewodnik zawiera wszystkie informacje wymagane do skonfigurowania replikacji systemu SAP HANA lokalnymi. Użyj tego podręcznika, jako linii bazowej.
* [Wysoce dostępny magazyn systemu plików NFS z DRBD i rozrusznik] [ suse-drbd-guide] zawiera wszystkie wymagane informacje, aby skonfigurować serwer NFS o wysokiej dostępności. Użyj tego podręcznika, jako linii bazowej.


## <a name="overview"></a>Przegląd

Aby osiągnąć wysoką dostępność, SAP NetWeaver wymaga, aby serwer systemu plików NFS. Serwer systemu plików NFS jest skonfigurowany w osobnym klastrze i mogą być używane przez wiele systemów SAP.

![Informacje o SAP NetWeaver wysokiej dostępności](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Serwer systemu plików NFS używa dedykowanego hosta wirtualnego i wirtualnych adresów IP dla każdego systemu SAP, który korzysta z tego serwera systemu plików NFS. Na platformie Azure usługi równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Poniższa lista przedstawia konfigurację usługi równoważenia obciążenia.        

* Konfiguracja serwera sieci Web
  * Adres IP 10.0.0.4 NW1
  * Adres IP 10.0.0.5 NW2
* Konfiguracja zaplecza
  * Połączone z interfejsów sieci podstawowej wszystkich maszyn wirtualnych, które powinny być częścią klastra systemu plików NFS
* Port sondy
  * Port 61000 NW1
  * Port 61001 NW2
* Reguły obciążenia
  * 2049 TCP dla NW1
  * UDP 2049 dla NW1
  * 2049 TCP dla NW2
  * UDP 2049 dla NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurowanie wysokiej dostępności serwera systemu plików NFS

Możesz użyć szablonu Azure z serwisu github do wdrożenia wszystkich wymaganych zasobów Azure, łącznie z maszyn wirtualnych, dostępności ustawić i modułu równoważenia obciążenia lub Ręczne wdrażanie zasobów.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux przy użyciu szablonu Azure

W portalu Azure Marketplace zawiera obraz systemu SUSE Linux Enterprise Server dla 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych.
Umożliwia jednego z szablonów Szybki Start w serwisie github wdrażanie wszystkich wymaganych zasobów. Szablon wdraża maszyny wirtualne, usługi równoważenia obciążenia, dostępność ustawić itd. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablon serwera SAP pliku] [ template-file-server] w portalu Azure   
1. Wprowadź następujące parametry
   1. Prefiks zasobów  
      Wprowadź prefiks, który ma być używany. Wartość jest używana jako prefiksu dla zasobów, które zostały wdrożone.
   2. Wprowadź liczbę systemu SAP liczby systemów SAP, korzystających z tego serwera plików. To spowoduje wdrożenie wymaganą ilość konfiguracji serwera sieci Web, reguły, równoważenia obciążenia sondowania porty, dyski itp.
   3. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucje systemu Linux. Na przykład wybierz SLES 12.
   4. Nazwa użytkownika i hasło administratora  
      Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
   5. Identyfikator podsieci  
      Identyfikator podsieci, do której maszyny wirtualne powinny być podłączone do. Pozostaw pole puste, jeśli chcesz utworzyć nową sieć wirtualną lub wybierz podsieć sieci VPN lub usługi Express Route wirtualnej sieci lokalnej nawiązać połączenia z maszyną wirtualną. Identyfikator zwykle wygląda /subscriptions/**&lt;identyfikator subskrypcji&gt;**/resourceGroups/**&lt;Nazwa grupy zasobów&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;wirtualnej nazwy sieciowej&gt;**/subnets/**&lt;nazwy podsieci&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie Linux za pomocą portalu Azure

Najpierw należy utworzyć maszyny wirtualnej dla tego klastra systemu plików NFS. Później Utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli wewnętrznej bazy danych.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Zestaw aktualizacji max domeny
1. Utwórz maszynę wirtualną 1   
   Użyj co najmniej z dodatkiem SP3 SLES4SAP 12, w tym przykładzie SLES4SAP 12 SP3 BYOS obrazu SLES dla SAP aplikacji 12 z dodatkiem SP3 (BYOS) jest używany  
   Wybierz utworzony wcześniej zestawu dostępności  
1. Tworzenie maszyny wirtualnej 2   
   Użyj co najmniej z dodatkiem SP3 z 12 SLES4SAP, w tym przykładzie SLES4SAP 12 SP3 BYOS obrazu  
   SLES dla SAP aplikacji 12 z dodatkiem SP3 (BYOS) jest używany.  
   Wybierz utworzony wcześniej zestawu dostępności  
1. Dodaj jeden dysk danych dla każdego systemu SAP do obu maszyn wirtualnych.
1. Tworzenie modułu równoważenia obciążenia (wewnętrzny)  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.0.0.4 NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu i kliknij przycisk Dodaj
         1. Wprowadź nazwę puli adresów IP frontonu (na przykład **nw1 frontonu**)
         1. Ustawić przypisania jako statyczny i wprowadź adres IP (na przykład **10.0.0.4**)
         1. Kliknij przycisk OK         
      1. Adres IP 10.0.0.5 NW2
         * Powtórz powyższe kroki dla NW2
   1. Tworzenie puli wewnętrznej bazy danych
      1. Połączone z interfejsów sieci podstawowej wszystkich maszyn wirtualnych, które powinny być częścią klastra systemu plików NFS na potrzeby NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz pul zaplecza i kliknij przycisk Dodaj
         1. Wprowadź nazwę dla nowej puli wewnętrznej bazy danych (na przykład **nw1 zaplecza**)
         1. Kliknij przycisk Dodaj maszynę wirtualną
         1. Wybierz zestaw dostępności utworzoną wcześniej
         1. Wybierz maszyny wirtualne klastra systemu plików NFS
         1. Kliknij przycisk OK
      1. Połączone z interfejsów sieci podstawowej wszystkich maszyn wirtualnych, które powinny być częścią klastra systemu plików NFS na potrzeby NW2
         * Powtórz powyższe kroki, aby utworzyć pulę zaplecza dla NW2
   1. Utwórz sondy kondycji
      1. Port 61000 NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowego sondy kondycji (na przykład **nw1 hp**)
         1. Wybierz protokół, port 610 TCP**00**, interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 61001 NW2
         * Powtórz powyższe kroki, aby utworzyć sondy kondycji dla NW2
   1. Reguły obciążenia
      1. 2049 TCP dla NW1
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **nw1-lb-2049**)
         1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonym wcześniej (na przykład **nw1 frontonu**)
         1. Zachowaj protokołu **TCP**, wprowadź port **2049**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że można włączyć pływającego adresu IP**
         1. Kliknij przycisk OK    
      1. UDP 2049 dla NW1
         * Powtórz powyższe kroki dla portu 2049 i UDP dla NW1
      1. 2049 TCP dla NW2
         * Powtórz powyższe kroki dla portu 2049 i TCP dla NW2
      1. UDP 2049 dla NW2
         * Powtórz powyższe kroki dla portu 2049 i UDP dla NW2

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznik

Postępuj zgodnie z instrukcjami [Konfigurowanie rozrusznik w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) utworzyć podstawowy klaster rozrusznik dla tego serwera systemu plików NFS.

### <a name="configure-nfs-server"></a>Skonfiguruj serwer systemu plików NFS

Następujące elementy są poprzedzane prefiksem albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzła 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Instalatora rozpoznawania nazwy hosta   

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład przedstawia sposób użycia pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Serwer włączyć systemu plików NFS

   Utwórz wpis katalogu głównego eksportu, uruchamiania serwera systemu plików NFS i włączenie funkcji autostart

   <pre><code>
   sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/

   sudo systemctl enable nfsserver
   sudo service nfsserver restart
   </code></pre>

1. **[A]**  Zainstalować składniki drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Tworzenie partycji dla urządzeń drbd

   Wyświetl listę wszystkich dysków dostępnych danych

   <pre><code>
   sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   lun0  lun1
   ```

   Tworzenie partycji dla każdego dysku danych

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  LVM Tworzenie konfiguracji

   Wyświetl listę wszystkich dostępnych partycji

   <pre><code>
   ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Utwórz woluminy LVM dla każdej partycji

   <pre><code>
   sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Skonfigurować drbd

   <pre><code>
   sudo vi /etc/drbd.conf
   </code></pre>

   Upewnij się, że plik drbd.conf zawiera następujące dwa wiersze

   <pre><code>
   include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Zmień konfigurację drbd globalne

   <pre><code>
   sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Dodaj następujące wpisy do obsługi i net sekcji.

   <pre><code>
   global {
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
             resync-rate 50M;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
        }
   }
   </code></pre>

1. **[A]**  Utworzyć urządzenia drbd systemu plików NFS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Wstaw Konfiguracja nowe urządzenie drbd i zakończenia

   <pre><code>
   resource <b>NW1</b>-nfs {
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

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Wstaw Konfiguracja nowe urządzenie drbd i zakończenia

   <pre><code>
   resource <b>NW2</b>-nfs {
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

   <pre><code>
   sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Pominąć synchronizacji początkowej

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Ustawić węzła podstawowego

   <pre><code>
   sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Poczekaj, aż nowych urządzeń drbd są zsynchronizowane.

   <pre><code>
   sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Tworzenie systemów plików na urządzeniach drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
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

1. **[A]**  Instalatora drbd wykrywania z podziałem informacji

   Korzystając z drbd umożliwia synchronizowanie danych z jednego hosta na inny, może wystąpić tak zwany podziału informacji. Podziału informacji jest scenariusz, w którym oba węzły klastra poziom jest podwyższany urządzenia drbd jako serwera podstawowego i został zsynchronizowany. Może być bardzo rzadko sytuacji, ale nadal chcesz obsługiwać i rozwiąż podziału informacji tak szybko jak to możliwe. Dlatego jest ważne otrzymać powiadomienie, gdy wystąpił podziału informacji.

   Odczyt [drbd oficjalna dokumentacja](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) na temat sposobu konfigurowania powiadomień inteligencji podziału.

   Istnieje również możliwość usuwania skutków scenariusza inteligencji podziału. Aby uzyskać więcej informacji, przeczytaj [zasady odzyskiwania inteligencji automatycznego podziału](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurowanie klastra Framework

1. **[1]**  Dodać urządzenia drbd systemu plików NFS systemu SAP NW1 do konfiguracji klastra

   <pre><code>
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
   
   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=3 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=4 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=5 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=6 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=7 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=8 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> exportfs_<b>NW1</b>_sidsys \
     exportfs_<b>NW1</b>_sapmntsid exportfs_<b>NW1</b>_trans exportfs_<b>NW1</b>_ASCS \
     exportfs_<b>NW1</b>_ASCSERS exportfs_<b>NW1</b>_SCS exportfs_<b>NW1</b>_SCSERS \
     nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Dodać urządzenia drbd systemu plików NFS systemu SAP NW2 do konfiguracji klastra

   <pre><code>
   # Enable maintenance mode
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
     options="rw,no_root_squash" clientspec="*" fsid=9 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=10 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=11 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=12 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=13 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=14 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=15 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=16 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> exportfs_<b>NW2</b>_sidsys \
     exportfs_<b>NW2</b>_sapmntsid exportfs_<b>NW2</b>_trans exportfs_<b>NW2</b>_ASCS \
     exportfs_<b>NW2</b>_ASCSERS exportfs_<b>NW2</b>_SCS exportfs_<b>NW2</b>_SCSERS \
     nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Wyłącz tryb konserwacji
   
   <pre><code>
   sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Kolejne kroki
* [Zainstaluj SAP ASCS i bazy danych](high-availability-guide-suse.md)
* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP][deployment-guide]
* [Wdrożenia usługi Azure DBMS maszyny wirtualnej dla programu SAP][dbms-guide]
* Aby dowiedzieć się jak ustanowić wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże), zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak nawiązać wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na maszynach wirtualnych Azure, zobacz [dostępności wysokiej programu SAP HANA na maszynach wirtualnych Azure (VM)][sap-hana-ha]
