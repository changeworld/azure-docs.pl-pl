---
title: Wysoka dostępność usług NFS na maszynach wirtualnych platformy Azure w sles | Dokumenty firmy Microsoft
description: Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 4dce0a675f5841591da00a322b72718964d382ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348870"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania struktury klastra i instalowania serwera systemu plików NFS o wysokiej dostępności, który może służyć do przechowywania udostępnionych danych systemu SAP o wysokiej dostępności.
W tym przewodniku opisano sposób konfigurowania serwera NFS o wysokiej dostępności, który jest używany przez dwa systemy SAP, NW1 i NW2. Nazwy zasobów (na przykład maszyny wirtualne, sieci wirtualne) w przykładzie zakładają, że użyto [szablonu serwera plików SAP][template-file-server] z prefiksem zasobu **prod**.

Najpierw przeczytaj poniższe uwagi i dokumenty SAP

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux na platformie Microsoft Azure

* Uwaga SAP [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* Sap Note [2205917] ma zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server dla aplikacji SAP
* Sap Note [1944799] ma wytyczne SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Sap Note [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Sap Note [1984787] ma ogólne informacje na temat SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* [SUSE Linux Enterprise High Availability Extension 12 DODATKU SP3 — najważniejsze wskazówki — przewodniki po najlepszych praktykach][sles-hae-guides]
  * Wysoka dostępna pamięć masowa NFS z DRBD i rozrusznikiem serca
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 — najważniejsze wskazówki — przewodniki po najlepszych praktykach][sles-for-sap-bp]
* [Informacje o wersji SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, SAP NetWeaver wymaga serwera NFS. Serwer NFS jest skonfigurowany w oddzielnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Serwer NFS używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP dla każdego systemu SAP korzystającego z tego serwera NFS. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia.        

* Konfiguracja frontu
  * Adres IP 10.0.0.4 dla NW1
  * Adres IP 10.0.0.5 dla NW2
* Konfiguracja wewnętrznej bazy danych
  * Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra NFS
* Port sondy
  * Port 61000 dla NW1
  * Port 61001 dla NW2
* Reguły równoważenia obciążenia (w przypadku korzystania z podstawowego modułu równoważenia obciążenia)
  * 2049 TCP dla NW1
  * 2049 UDP dla NW1
  * 2049 TCP dla NW2
  * 2049 UDP dla NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurowanie serwera systemu plików NFS o wysokiej dostępności

Można użyć szablonu platformy Azure z usługi GitHub, aby wdrożyć wszystkie wymagane zasoby platformy Azure, w tym maszyny wirtualne, zestaw dostępności i moduł równoważenia obciążenia lub można wdrożyć zasoby ręcznie.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pośrednictwem szablonu platformy Azure

Portal Azure Marketplace zawiera obraz dla systemu SUSE Linux Enterprise Server for SAP Applications 12, którego można użyć do wdrożenia nowych maszyn wirtualnych.
Można użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności itp. Aby wdrożyć szablon, wykonaj następujące czynności:

1. Otwieranie [szablonu serwera plików SAP][template-file-server] w witrynie Azure portal   
1. Wprowadź następujące parametry
   1. Prefiks zasobu  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla zasobów, które są wdrażane.
   2. Liczba systemów SAP  
      Wprowadź liczbę systemów SAP, które będą korzystać z tego serwera plików. Spowoduje to wdrożenie wymaganej ilości konfiguracji frontendu, reguł równoważenia obciążenia, portów sondy, dysków itp.
   3. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji Linuksa. W tym przykładzie wybierz SLES 12
   4. Nazwa użytkownika i hasło administratora  
      Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
   5. Identyfikator podsieci  
      Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda jak /subskrypcje/**&lt;&gt;identyfikator subskrypcji**/resourceGroups/**&lt;nazwa&gt;grupy zasobów**/providers/Microsoft.Network/virtualNetworks/**&lt;nazwa&gt;sieci wirtualnej**/podsieci/**&lt;nazwa&gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem witryny Azure portal

Najpierw należy utworzyć maszyny wirtualne dla tego klastra NFS. Następnie należy utworzyć moduł równoważenia obciążenia i używać maszyn wirtualnych w pulach wewnętrznej bazy danych.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Ustawianie domeny aktualizacji maksymalnej
1. Utwórz maszynę wirtualną 1 Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3 BYOS SLES dla aplikacji SAP 12 SP3 (BYOS)  
   Wybierz zestaw dostępności utworzony wcześniej  
1. Tworzenie maszyny wirtualnej 2 Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie obraz SLES4SAP 12 SP3 BYOS  
   SLES Dla aplikacji SAP 12 SP3 (BYOS) jest używany  
   Wybierz zestaw dostępności utworzony wcześniej  
1. Dodaj jeden dysk danych dla każdego systemu SAP do obu maszyn wirtualnych.
1. Tworzenie modułu równoważenia obciążenia (wewnętrznego). Zalecamy [standardowy moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).  
   1. Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć standardowy moduł równoważenia obciążenia:
      1. Tworzenie adresów IP frontendu
         1. Adres IP 10.0.0.4 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej puli IP frontendu (na przykład **nw1-frontend)**
            1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **10.0.0.4**)
            1. Kliknij przycisk OK
         1. Adres IP 10.0.0.5 dla NW2
            * Powtórz powyższe kroki dla NW2
      1. Tworzenie pul zaplecza
         1. Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra NFS
            1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw-backend)**
            1. Wybierz sieć wirtualną
            1. Kliknij pozycję Dodaj maszynę wirtualną
            1. Wybierz maszyny wirtualne klastra NFS i ich adresy IP.
            1. Kliknij pozycję Add (Dodaj).
      1. Tworzenie sond kondycji
         1. Port 61000 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej sondy kondycji (na przykład **nw1-hp**)
            1. Wybierz protokół TCP jako protokół, port 610**00**, zachowaj interwał 5 i próg w złej kondycji 2
            1. Kliknij przycisk OK
         1. Port 61001 dla NW2
            * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla NW2
      1. Reguły równoważenia obciążenia
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **nw1-lb**)
         1. Wybierz adres IP frontendu, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **nw1-frontend**. **nw-backend** i **nw1-hp**)
         1. Wybierz **porty wysokiej haw**.
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
         * Powtórz powyższe kroki, aby utworzyć regułę równoważenia obciążenia dla NW2
   1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia, postępuj zgodnie z poniższymi instrukcjami:
      1. Tworzenie adresów IP frontendu
         1. Adres IP 10.0.0.4 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej puli IP frontendu (na przykład **nw1-frontend)**
            1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **10.0.0.4**)
            1. Kliknij przycisk OK
         1. Adres IP 10.0.0.5 dla NW2
            * Powtórz powyższe kroki dla NW2
      1. Tworzenie pul zaplecza
         1. Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra NFS
            1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw-backend)**
            1. Kliknij pozycję Dodaj maszynę wirtualną
            1. Wybierz utworzony wcześniej zestaw dostępności
            1. Wybieranie maszyn wirtualnych klastra NFS
            1. Kliknij przycisk OK
      1. Tworzenie sond kondycji
         1. Port 61000 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej sondy kondycji (na przykład **nw1-hp**)
            1. Wybierz protokół TCP jako protokół, port 610**00**, zachowaj interwał 5 i próg w złej kondycji 2
            1. Kliknij przycisk OK
         1. Port 61001 dla NW2
            * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla NW2
      1. Reguły równoważenia obciążenia
         1. 2049 TCP dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
            1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **nw1-lb-2049**)
            1. Wybierz adres IP wewnętrznej bazy danych, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **nw1-frontend)**
            1. Zachowaj protokół **TCP**, wprowadź port **2049**
            1. Wydłużenie limitu czasu bezczynnego do 30 minut
            1. **Upewnij się, że włączysz pływający adres IP**
            1. Kliknij przycisk OK
         1. 2049 UDP dla NW1
            * Powtórz powyższe kroki dla portu 2049 i UDP dla NW1
         1. 2049 TCP dla NW2
            * Powtórz powyższe kroki dla portu 2049 i TCP dla NW2
         1. 2049 UDP dla NW2
            * Powtórz powyższe kroki dla portu 2049 i UDP dla NW2

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

Wykonaj kroki opisane w [sekcji Konfigurowanie rozrusznika serca na suse linux enterprise server na platformie Azure,](high-availability-guide-suse-pacemaker.md) aby utworzyć podstawowy klaster rozrusznika serca dla tego serwera NFS.

### <a name="configure-nfs-server"></a>Konfigurowanie serwera systemu plików NFS

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Włącz serwer systemu plików NFS

   Tworzenie głównego wpisu eksportu nfs

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Instalowanie komponentów drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Tworzenie partycji dla urządzeń drbd

   Wyświetlanie listy wszystkich dostępnych dysków danych

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

1. **[A]** Tworzenie konfiguracji LVM

   Wyświetl listę wszystkich dostępnych partycji

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Tworzenie woluminów LVM dla każdej partycji

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Konfigurowanie drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Upewnij się, że plik drbd.conf zawiera następujące dwa wiersze

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Zmienianie globalnej konfiguracji drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Dodaj następujące wpisy do sekcji obsługi i sieci.

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

1. **[A]** Tworzenie urządzeń drbd NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Włóż konfigurację nowego urządzenia drbd i wyjdź

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

   Włóż konfigurację nowego urządzenia drbd i wyjdź

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

   Utwórz urządzenie drbd i uruchom je

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Pomiń początkową synchronizację

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Ustawianie węzła podstawowego

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Poczekaj, aż nowe urządzenia drbd zostaną zsynchronizowane

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Tworzenie systemów plików na urządzeniach drbd

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

1. **[A]** Konfiguracja wykrywania podzielonego mózgu

   Podczas korzystania z drbd do synchronizacji danych z jednego hosta do drugiego, może wystąpić tak zwany podział mózgu. Podzielony mózg to scenariusz, w którym oba węzły klastra promowały urządzenie drbd jako podstawowe i nie były zsynchronizowane. Może to być rzadka sytuacja, ale nadal chcesz obsługiwać i rozwiązać podzielony mózg tak szybko, jak to możliwe. Dlatego ważne jest, aby być powiadamianym, gdy doszło do podziału mózgu.

   Przeczytaj [oficjalną dokumentację drbd,](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) jak skonfigurować powiadomienie o podzielonym mózgu.

   Jest również możliwe, aby automatycznie odzyskać od scenariusza podziału mózgu. Aby uzyskać więcej informacji, przeczytaj [artykuł Automatyczne zasady odzyskiwania mózgu podzielonego](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurowanie struktury klastra

1. **[1]** Dodaj urządzenia drbd NFS dla systemu SAP NW1 do konfiguracji klastra

   > [!IMPORTANT]
   > Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a zmienny adres IP staje się niedostępny.  
   > W przypadku istniejących klastrów rozruszników zalecamy w przeszłości zastąpienie netcata socatem. Obecnie zalecamy użycie agenta zasobów azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Dla SLES 15/15 SP1 wersja musi być co najmniej resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Należy pamiętać, że zmiana będzie wymagać krótkiego przestoju.  
   > W przypadku istniejących klastrów stymulatora, jeśli konfiguracja została już zmieniona, aby używać socat zgodnie z opisem w [umacnianiu wykrywania równoważenia obciążenia azure,](https://www.suse.com/support/kb/doc/?id=7024128)nie ma wymogu natychmiastowego przejścia na agenta zasobów azure-lb.

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
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Dodaj urządzenia drbd NFS dla systemu SAP NW2 do konfiguracji klastra

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
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   Opcja `crossmnt` w `exportfs` zasobach klastra jest dostępna w naszej dokumentacji dla zgodności z powrotem ze starszymi wersjami SLES.  

1. **[1]** Wyłącz tryb konserwacji
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Instalowanie SAP ASCS i bazy danych](high-availability-guide-suse.md)
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]
