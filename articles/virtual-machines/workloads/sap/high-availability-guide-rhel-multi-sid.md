---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NW w przewodniku dla wielu identyfikatorów SYD | Dokumenty firmy Microsoft
description: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver w systemie Red Hat Enterprise Linux
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
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247439"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Wysoka dostępność dla sap NetWeaver na maszynach wirtualnych platformy Azure w red hat enterprise linux dla aplikacji SAP — przewodnik po wielu identyfikatorach SID

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

W tym artykule opisano sposób wdrażania wielu systemów SAP NetWeaver o wysokiej dostępności (czyli wielu identyfikatorów SID) w klastrze dwóch węzłów na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla aplikacji SAP.  

W przykładowych konfiguracjach, polecenia instalacji itp trzy sap NetWeaver 7.50 systemy są wdrażane w jednym, dwa węzły wysokiej dostępności klastra. Identyfikatory SID systemów SAP to:
* **NW1**: Numer wystąpienia ASCS **00** i nazwa hosta wirtualnego **msnw1ascs**; Ers numer wystąpienia **02** i nazwa hosta wirtualnego **msnw1ers**.  
* **NW2**: Ascs numer wystąpienia **10** i wirtualna nazwa **hosta msnw2ascs**; Ers numer wystąpienia **12** i nazwa hosta wirtualnego **msnw2ers**.  
* **NW3**: Ascs numer wystąpienia **20** i wirtualna nazwa **hosta msnw3ascs**; Ers numer wystąpienia **22** i nazwa hosta wirtualnego **msnw3ers**.  

Artykuł nie obejmuje warstwy bazy danych i wdrażania udziałów SAP NFS. W przykładach w tym artykule używamy sapMSID woluminu [usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) **sapMSID** dla udziałów NFS, przy założeniu, że wolumin jest już wdrożony. Zakładamy również, że wolumin Plików NetApp platformy Azure jest wdrażany za pomocą protokołu NFSv3 i że istnieją następujące ścieżki plików dla zasobów klastra dla wystąpień ASCS i ERS systemów SAP NW1, NW2 i NW3:  

* sapMSID woluminu (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW1)</b>
* sapMSID woluminu (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* sapMSID woluminu (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* sapMSID woluminu (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Przed rozpoczęciem należy najpierw zapoznać się z następującymi uwagami i dokumentami SAP:

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux na platformie Microsoft Azure
* [Dokumentacja plików netapp platformy Azure][anf-azure-doc]
* Uwaga SAP [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* SAP Note [2002167] ma zalecane ustawienia systemu operacyjnego dla systemu Red Hat Enterprise Linux
* SAP Note [2009879] ma wytyczne SAP HANA dla Red Hat Enterprise Linux
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Sap Note [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* [SAP Netweaver w klastrze rozruszników serca](https://access.redhat.com/articles/3150081)
* Ogólna dokumentacja RHEL
  * [Omówienie dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatkami o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurowanie ASCS/ERS dla SAP Netweaver z autonomicznymi zasobami w RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurowanie SAP S/4HANA ASCS/ERS z autonomicznym serwerem Enqueue Server 2 (ENSA2) w rozruszniku serca na RHEL](https://access.redhat.com/articles/3974941)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zasady pomocy technicznej dotyczące klastrów wysokiej dostępności RHEL — maszyny wirtualne platformy Microsoft Azure jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie klastra o wysokiej dostępności w systemie Microsoft Azure dla systemu Red Hat Enterprise 7.4 (lub nowszych)](https://access.redhat.com/articles/3252491)
* [Aplikacje SAP NetApp na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Maszyny wirtualne, które uczestniczą w klastrze musi być dopasowywać, aby móc uruchomić wszystkie zasoby, w przypadku awarii występuje. Każdy identyfikator SID SAP może być awaryjnie od siebie niezależny w klastrze wysokiej dostępności wielu identyfikatorów SID.  

Aby osiągnąć wysoką dostępność, SAP NetWeaver wymaga udziałów o wysokiej dostępności. W tej dokumentacji przedstawiamy przykłady z udziałami SAP wdrożonymi na [woluminach NFS plików NFS plików Usługi Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) Możliwe jest również hostowanie udziałów w wysoce dostępnym [klastrze GlusterFS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)który może być używany przez wiele systemów SAP.  

![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Obsługa klastrowania wielu identyfikatorów SID sap ASCS/ERS z systemem Red Hat Linux jako system operacyjny gościa w maszynach wirtualnych platformy Azure jest ograniczona do **pięciu** identyfikatorów SAP SID w tym samym klastrze. Każdy nowy identyfikator SID zwiększa złożoność. Połączenie serwera replikacji SAP Enqueue 1 i serwera replikacji enqueue 2 w tym samym klastrze nie jest **obsługiwane**. Klastrowanie wieloustawowe opisuje instalację wielu wystąpień SAP ASCS/ERS z różnymi identyfikatorami SID w jednym klastrze rozrusznika serca. Obecnie klastrowanie wielu identyfikatorów SID jest obsługiwane tylko dla ASCS/ERS.  

> [!TIP]
> Klastrowanie wielu identyfikatorów SID SAP ASCS/ERS jest rozwiązaniem o większej złożoności. Jest to bardziej skomplikowane do wdrożenia. Wiąże się to również z większym nakładem administracyjnym podczas wykonywania czynności konserwacyjnych (takich jak łatanie systemu operacyjnego). Przed rozpoczęciem rzeczywistej implementacji poświęć trochę czasu, aby dokładnie zaplanować wdrożenie i wszystkie zaangażowane składniki, takie jak maszyny wirtualne, instalacje NFS, adresy VIP, konfiguracje modułu równoważenia obciążenia i tak dalej.  

SAP NetWeaver ASCS, SAP NetWeaver SCS i SAP NetWeaver ERS używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy użycie [standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).  

Na poniższej liście przedstawiono konfigurację (A)SCS i ERS moduł równoważenia obciążenia dla tego przykładu klastra wielouchodyckiego z trzema systemami SAP. Dla każdego wystąpienia ASCS i ERS dla każdego identyfikatora SID potrzebny jest oddzielny adres IP, sondy kondycji i reguły równoważenia obciążenia. Przypisz wszystkie maszyny wirtualne, które są częścią klastra ASCS/ASCS, do jednej puli wewnętrznej bazy danych pojedynczego równoważenia obciążenia sieciowego.  

### <a name="ascs"></a>1lit. Scs

* Konfiguracja frontu
  * Adres IP nw1: 10.3.1.50
  * Adres IP NW2: 10.3.1.52
  * Adres IP nw3: 10.3.1.54

* Porty sondy
  * Port 620<strong>&lt;&gt;nr</strong>, dlatego dla portów sond NW1, NW2 i NW3 620**00**, 620**10** i 620**20**
* Reguły równoważenia obciążenia — należy utworzyć po jednym dla każdego wystąpienia, czyli NW1/ASCS, NW2/ASCS i NW3/ASCS.
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * W przypadku korzystania z podstawowego modułu równoważenia obciążenia należy utworzyć reguły równoważenia obciążenia dla następujących portów
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Konfiguracja frontu
  * Adres IP NW1 10.3.1.51
  * Adres IP NW2 10.3.1.53
  * Adres IP NW3 10.3.1.55

* Port sondy
  * Port 621<strong>&lt;&gt;nr</strong>, dlatego dla portów sond NW1, NW2 i N3 621**02**, 621**12** i 621**22**
* Reguły równoważenia obciążenia — należy utworzyć po jednym dla każdego wystąpienia, czyli NW1/ERS, NW2/ERS i NW3/ERS.
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * W przypadku korzystania z podstawowego modułu równoważenia obciążenia należy utworzyć reguły równoważenia obciążenia dla następujących portów
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Konfiguracja wewnętrznej bazy danych
  * Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra (A)SCS/ERS

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="sap-shares"></a>Udziały SAP

SAP NetWeaver wymaga udostępnionego magazynu dla transportu, katalogu profilu i tak dalej. W przypadku systemu SAP o wysokiej dostępności ważne jest, aby mieć udziały o wysokiej dostępności. Należy zdecydować się na architekturę dla udziałów SAP. Jedną z opcji jest wdrożenie udziałów na [woluminach NFS plików NFS plików NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)platformy Azure.  Dzięki usłudze Azure NetApp Files uzyskasz wbudowaną wysoką dostępność udziałów SAP NFS.

Inną opcją jest tworzenie [GlusterFS na maszynach wirtualnych platformy Azure na red hat enterprise linux dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), które mogą być współużytkowane przez wiele systemów SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Wdrażanie pierwszego systemu SAP w klastrze

Teraz, gdy zdecydowano się na architekturę udziałów SAP, wdrożyć pierwszy system SAP w klastrze, zgodnie z odpowiednią dokumentacją.

* Jeśli korzystasz z woluminów NFS plików Usługi Azure NetApp Files, postępuj zgodnie [z wysoką dostępnością maszyn wirtualnych platformy Azure dla systemu SAP NetWeaver w systemie Red Hat Enterprise Linux z plikami NetApp usługi Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* Jeśli używasz klastra GlusterFS, postępuj zgodnie z [glusterfs na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs).  

Dokumenty wymienione powyżej poprowadzi Cię przez kroki, aby przygotować niezbędną infrastrukturę, utworzyć klaster, przygotować system operacyjny do uruchomienia aplikacji SAP.  

> [!TIP]
> Zawsze przetestuj funkcję trybu failover klastra po wdrożeniu pierwszego systemu przed dodaniem dodatkowych identyfikatorów SAP SID do klastra. W ten sposób będziesz wiedzieć, że funkcje klastra działa, przed dodaniem złożoności dodatkowych systemów SAP do klastra.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Wdrażanie dodatkowych systemów SAP w klastrze

W tym przykładzie przyjęto założenie, że system **NW1** został już wdrożony w klastrze. Pokażemy, jak wdrożyć w klastrze systemy SAP **NW2** i **NW3**. 

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

### <a name="prerequisites"></a>Wymagania wstępne 

> [!IMPORTANT]
> Przed postępem w stosować się do instrukcji wdrażania dodatkowych systemów SAP w klastrze, postępuj zgodnie z instrukcjami, aby wdrożyć pierwszy system SAP w klastrze, ponieważ istnieją kroki, które są niezbędne tylko podczas pierwszego wdrożenia systemu.  

Niniejsza dokumentacja zakłada, że:
* Klaster rozrusznika serca jest już skonfigurowany i uruchomiony.  
* Co najmniej jeden system SAP (ascs / ers wystąpienie) jest już wdrożony i jest uruchomiony w klastrze.  
* Przetestowano funkcję trybu failover klastra.  
* Zostaną wdrożone udziały nfs dla wszystkich systemów SAP.  

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji SAP NetWeaver

1. Dodaj konfigurację nowo wdrożonego systemu (czyli **NW2**, **NW3**) do istniejącego modułu równoważenia obciążenia platformy Azure, postępując zgodnie z instrukcjami [Wdrażaj moduł azure load balancer ręcznie za pośrednictwem witryny Azure portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal). Dostosuj adresy IP, porty sondy kondycji, reguły równoważenia obciążenia dla twojej konfiguracji.  

2. **[A]** Rozpoznawanie nazw instalatora dla dodatkowych systemów SAP. Można użyć serwera DNS lub `/etc/hosts` zmodyfikować na wszystkich węzłach. W tym przykładzie `/etc/hosts` pokazano, jak używać pliku.  Dostosuj adresy IP i nazwy hostów do środowiska. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** Utwórz katalogi udostępnione dla dodatkowych systemów **SAP NW2** i **NW3** wdrażanych w klastrze. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Dodaj wpisy instalacji dla systemów plików /sapmnt/SID i /usr/sap/SID/SYS dla dodatkowych systemów SAP wdrażanych w klastrze. W tym przykładzie **NW2** i **NW3**.  

   Zaktualizuj plik `/etc/fstab` za pomocą systemów plików dla dodatkowych systemów SAP wdrażanych w klastrze.  

   * Jeśli korzystasz z plików NetApp platformy Azure, postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * Jeśli korzystasz z klastra GlusterFS, postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Zainstaluj ASCS / ERS

1. Utwórz zasoby klastra wirtualnego adresu IP i sondy kondycji dla wystąpień ASCS dodatkowych systemów SAP wdrażanych w klastrze. Przykład pokazano w tym miejscu jest dla **NW2** i **NW3** ASCS, przy użyciu NFS na woluminach usługi Azure NetApp Files z protokołem NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.  

2. **[1]** Zainstaluj SAP NetWeaver ASCS  

   Zainstaluj SAP NetWeaver ASCS jako root, używając wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ASCS. Na przykład dla systemu **NW2,** wirtualna nazwa hosta to <b>msnw2ascs</b>, <b>10.3.1.52</b> i numer wystąpienia użyty dla sondy modułu równoważenia obciążenia, na przykład <b>10</b>. W przypadku systemu **NW3**wirtualna nazwa hosta to <b>msnw3ascs</b>, <b>10.3.1.54</b> i numer wystąpienia użyty do sondy modułu równoważenia obciążenia, na przykład <b>20</b>. Zanotuj, który węzeł klastra został zainstalowany jako ASCS dla każdego identyfikatora SID SAP.  

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst. Do zainstalowania systemu SAP za pomocą nazwy hosta wirtualnego można użyć parametru SAPINST_USE_HOSTNAME.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**SID**/ASCS**Instance#**, spróbuj skonfigurować właściciela **na sid**adm i grupowanie na sapsys wystąpienia**ASCS#** i ponów próbę.

3. **[1]** Utwórz zasoby klastra wirtualnego adresu IP i sondy kondycji dla wystąpienia ERS dodatkowego systemu SAP, który wdrażasz w klastrze. Przykład pokazano w tym miejscu jest dla **NW2** i **NW3** ERS, przy użyciu NFS na woluminach usługi Azure NetApp Files z protokołem NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane.  

   Następnie upewnij się, że zasoby nowo utworzonej grupy ERS są uruchomione w węźle klastra, naprzeciwko węzła klastra, w którym zainstalowano wystąpienie ASCS dla tego samego systemu SAP.  Na przykład, jeśli nw2 ASCS został zainstalowany na `rhelmsscl1`, a następnie `rhelmsscl2`upewnij się, że grupa NW2 ERS jest uruchomiona na .  Grupę NW2 ERS można `rhelmsscl2` przeprowadzić, uruchamiając następujące polecenie dla jednego z zasobów klastra w grupie: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Zainstaluj SAP NetWeaver ERS

   Zainstaluj SAP NetWeaver ERS jako root w innym węźle, używając wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ERS. Na przykład dla systemu **NW2,** nazwa hosta wirtualnego będzie <b>msnw2ers</b>, <b>10.3.1.53</b> i numer wystąpienia, który był używany do sondy modułu równoważenia obciążenia, na przykład <b>12</b>. W przypadku systemu **NW3**, nazwa wirtualnego hosta <b>msnw3ers</b>, <b>10.3.1.55</b> i numer wystąpienia użytego do sondy modułu równoważenia obciążenia, na przykład <b>22</b>. 

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst. Do zainstalowania systemu SAP za pomocą nazwy hosta wirtualnego można użyć parametru SAPINST_USE_HOSTNAME.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub nowszego. Niższe wersje nie ustawiają poprawnie uprawnień, a instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**NW2**/ERS**Instance#**, spróbuj skonfigurować właściciela **sid**adm i grupy na sapsys folderu ERS**Instance#** i ponów próbę.

   Jeśli konieczne było migrowanie grupy ERS nowo wdrożonego systemu SAP do innego węzła klastra, nie zapomnij usunąć ograniczenia lokalizacji dla grupy ERS. Ograniczenie można usunąć, uruchamiając następujące polecenie (przykład podano dla systemów SAP **NW2** i **NW3**). Pamiętaj, aby usunąć tymczasowe ograniczenia dla tego samego zasobu, który został użyty w poleceniu do przeniesienia grupy klastra ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Dostosuj profile wystąpień ASCS/SCS i ERS dla nowo zainstalowanych systemów SAP. Poniższy przykład dotyczy nw2. Należy dostosować profile ASCS/SCS i ERS dla wszystkich wystąpień SAP dodanych do klastra.  
 
   * Profil ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * Profil ERS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Aktualizacja pliku /usr/sap/sapservices

   Aby zapobiec rozpoczęciu wystąpień przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane `/usr/sap/sapservices` przez program Pacemaker muszą być komentowane z pliku.  Poniższy przykład dotyczy systemów SAP **NW2** i **NW3.**  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Tworzenie zasobów klastra SAP dla nowo zainstalowanego systemu SAP.  

   Jeśli używasz architektury serwera 1 w kolejce (ENSA1), zdefiniuj zasoby dla systemów SAP **NW2** i **NW3** w następujący sposób:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Firma SAP wprowadziła obsługę serwera 2, w tym replikacji, od momentu wprowadzenia sap NW 7.52. Począwszy od platformy ABAP 1809, serwer 2 jest instalowany domyślnie. Zobacz SAP note [2630416,](https://launchpad.support.sap.com/#/notes/2630416) aby uzyskać pomoc techniczną serwera 2.
   W przypadku korzystania z architektury serwera 2 w kolejce[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)należy zdefiniować zasoby dla systemów SAP **NW2** i **NW3** w następujący sposób:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Jeśli uaktualniasz ze starszej wersji i przełączasz się na serwer 2 w kolejce, zobacz UWAGA SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Limity czasu w powyższej konfiguracji są tylko przykłady i może być konieczne dostosowanie do określonej konfiguracji SAP. 

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.
   Poniższy przykład pokazuje stan zasobów klastra, po sap systemów **NW2** i **NW3** zostały dodane do klastra. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Dodaj reguły zapory dla ASCS i ERS w obu węzłach.  W poniższym przykładzie przedstawiono reguły zapory dla systemów SAP **NW2** i **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Kontynuuj instalację SAP 

Zakończ instalację SAP, korzystając z:

* [Przygotowanie serwerów aplikacji SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalowanie wystąpienia systemu DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Instalowanie podstawowego serwera aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Instalowanie co najmniej jednego dodatkowego wystąpienia aplikacji SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testowanie konfiguracji klastra z wieloma identyfikatorami SID

Następujące testy są podzbiór przypadków testowych w przewodnikach najlepszych rozwiązań Red Hat. Są one wliczone w cenę. Aby uzyskać pełną listę testów klastra, należy znaleźć następującą dokumentację:

* Jeśli korzystasz z woluminów NFS plików Usługi Azure NetApp Files, postępuj zgodnie [z wysoką dostępnością maszyn wirtualnych platformy Azure dla sap NetWeaver w programie RHEL za pomocą plików NetApp usługi Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* Jeśli używasz `GlusterFS`wysokiej dostępności, postępuj zgodnie [z wysoką dostępnością maszyn wirtualnych platformy Azure dla SAP NetWeaver w aplikacji RHEL dla aplikacji SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)  

Zawsze czytaj przewodniki po najlepszych praktykach Red Hat i wykonaj wszystkie dodatkowe testy, które mogły zostać dodane.  
Testy, które są prezentowane są w dwóch węzłach, klaster multi-SID z trzech systemów SAP zainstalowanych.  

1. Ręczna migracja wystąpienia ASCS. W przykładzie pokazano migrację wystąpienia ASCS dla systemu SAP NW3.

   Stan zasobu przed rozpoczęciem testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Uruchom następujące polecenia jako katalog główny, aby przeprowadzić migrację wystąpienia NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Stan zasobu po teście:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Uruchom następujące polecenie jako katalog główny w węźle, w którym jest uruchomione co najmniej jedno wystąpienie ASCS. W tym przykładzie wykonaliśmy `rhelmsscl1`polecenie na , gdzie są uruchomione wystąpienia ASCS dla NW1, NW2 i NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Stan po teście i po węźle, który został uszkodzony został uruchomiony ponownie, powinien wyglądać tak.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Jeśli istnieją komunikaty dotyczące zasobów nie powiodło się, oczyścić stan zasobów nie powiodło się. Przykład:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]
