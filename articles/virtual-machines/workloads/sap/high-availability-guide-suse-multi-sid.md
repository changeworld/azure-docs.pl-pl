---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver w przewodniku SLES dla wielu identyfikatorów SID | Dokumenty firmy Microsoft
description: Przewodnik o wysokiej dostępności wielu identyfikatorów SID dla sap NetWeaver na SUSE Linux Enterprise Server dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 793851780e1154b6b6a21c88ea8cae063a277790
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350054"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Wysoka dostępność dla sap NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server for SAP applications multi-SID guide

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania wielu systemów SAP NetWeaver lub S4HANA o wysokiej dostępności (czyli wielu identyfikatorów SID) w klastrze dwóch węzłów na maszynach wirtualnych platformy Azure z systemem SUSE Linux Enterprise Server dla aplikacji SAP.  

W przykładowych konfiguracjach, polecenia instalacji itp trzy sap NetWeaver 7.50 systemy są wdrażane w jednym, dwa węzły wysokiej dostępności klastra. Identyfikatory SID systemów SAP to:
* **NW1**: Numer wystąpienia ASCS **00** i nazwa hosta wirtualnego **msnw1ascs**; Ers numer wystąpienia **02** i nazwa hosta wirtualnego **msnw1ers**.  
* **NW2**: Ascs numer wystąpienia **10** i wirtualna nazwa **hosta msnw2ascs**; Ers numer wystąpienia **12** i nazwa hosta wirtualnego **msnw2ers**.  
* **NW3**: Ascs numer wystąpienia **20** i wirtualna nazwa **hosta msnw3ascs**; Ers numer wystąpienia **22** i nazwa hosta wirtualnego **msnw3ers**.  

Artykuł nie obejmuje warstwy bazy danych i wdrażania udziałów SAP NFS. W przykładach w tym artykule używamy nazw wirtualnych nw2-nfs dla udziałów NW2 NFS i nw3-nfs dla udziałów NW3 NFS, przy założeniu, że klaster NFS został wdrożony.  

Przed rozpoczęciem należy najpierw zapoznać się z następującymi uwagami i dokumentami SAP:

* Uwaga SAP [1928533][1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux na platformie Microsoft Azure

* Uwaga SAP [2015553][2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* Sap Note [2205917][2205917] ma zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server dla aplikacji SAP
* Sap Note [1944799][1944799] ma wytyczne SAP HANA dla SUSE Linux Enterprise Server dla aplikacji SAP
* Uwaga SAP [2178632][2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Sap Note [2191498][2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692][2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Sap Note [1984787][1984787] ma ogólne informacje na temat SUSE Linux Enterprise Server 12.
* Uwaga SAP [1999351][1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* [Przewodniki po najlepszych praktykach SUSE SAP HA][suse-ha-guide] Przewodniki zawierają wszystkie wymagane informacje do skonfigurowania netweaver HA i SAP HANA System Replication lokalnie. Użyj tych przewodników jako ogólnej linii bazowej. Dostarczają one znacznie bardziej szczegółowych informacji.
* [Informacje o wersji SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Przewodnik klastra wielosydyskowego SUSE dla SLES 12 i SLES 15](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [Aplikacje SAP NetApp na platformie Microsoft Azure przy użyciu plików NetApp platformy Azure][anf-sap-applications-azure]
## <a name="overview"></a>Omówienie

Maszyny wirtualne, które uczestniczą w klastrze musi być dopasowywać, aby móc uruchomić wszystkie zasoby, w przypadku awarii występuje. Każdy identyfikator SID SAP może być awaryjnie od siebie niezależny w klastrze wysokiej dostępności wielu identyfikatorów SID.  W przypadku korzystania z ogrodzenia SBD urządzenia SBD mogą być współużytkowane przez wiele klastrów.  

Aby osiągnąć wysoką dostępność, SAP NetWeaver wymaga wysoce dostępnych udziałów NFS. W tym przykładzie zakładamy, że udziały SAP NFS są hostowane na wysoce [dostępnym serwerze plików NFS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)który może być używany przez wiele systemów SAP. Lub udziały są wdrażane na [woluminach NFS plików NetApp Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Obsługa klastrowania wielu identyfikatorów SID sap ASCS/ERS z systemem SUSE Linux jako system operacyjny gościa w maszynach wirtualnych platformy Azure jest ograniczona do **pięciu** identyfikatorów SAP SID w tym samym klastrze. Każdy nowy identyfikator SID zwiększa złożoność. Połączenie serwera replikacji SAP Enqueue 1 i serwera replikacji enqueue 2 w tym samym klastrze nie jest **obsługiwane**. Klastrowanie wieloustawowe opisuje instalację wielu wystąpień SAP ASCS/ERS z różnymi identyfikatorami SID w jednym klastrze rozrusznika serca. Obecnie klastrowanie wielu identyfikatorów SID jest obsługiwane tylko dla ASCS/ERS.  

> [!TIP]
> Klastrowanie wielu identyfikatorów SID SAP ASCS/ERS jest rozwiązaniem o większej złożoności. Jest to bardziej skomplikowane do wdrożenia. Wiąże się to również z większym nakładem administracyjnym podczas wykonywania czynności konserwacyjnych (takich jak łatanie systemu operacyjnego). Przed rozpoczęciem rzeczywistej implementacji poświęć trochę czasu, aby dokładnie zaplanować wdrożenie i wszystkie zaangażowane składniki, takie jak maszyny wirtualne, instalacje NFS, adresy VIP, konfiguracje modułu równoważenia obciążenia i tak dalej.  

Serwer NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS i baza danych SAP HANA używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy użycie [standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).  

Na poniższej liście przedstawiono konfigurację (A)SCS i ERS moduł równoważenia obciążenia dla tego przykładu klastra wielouchodyckiego z trzema systemami SAP. Dla każdego wystąpienia ASCS i ERS dla każdego identyfikatora SID potrzebny jest oddzielny adres IP, sondy kondycji i reguły równoważenia obciążenia. Przypisz wszystkie maszyny wirtualne, które są częścią klastra ASCS/ASCS do jednej puli wewnętrznej bazy danych.  

### <a name="ascs"></a>1lit. Scs

* Konfiguracja frontu
  * Adres IP nw1: 10.3.1.14
  * Adres IP NW2: 10.3.1.16
  * Adres IP nw3: 10.3.1.13
* Porty sondy
  * Port 620<strong>&lt;&gt;nr</strong>, dlatego dla portów sond NW1, NW2 i NW3 620**00**, 620**10** i 620**20**
* Reguły równoważenia obciążenia - 
* utworzyć po jednym dla każdego wystąpienia, czyli NW1/ASCS, NW2/ASCS i NW3/ASCS.
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
  * Adres IP NW1 10.3.1.15
  * Adres IP NW2 10.3.1.17
  * Adres IP NW3 10.3.1.19
* Port sondy
  * Port 621<strong>&lt;&gt;nr</strong>, dlatego dla portów sond NW1, NW2 i N# 621**02**, 621**12** i 621**22**
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

## <a name="sap-nfs-shares"></a>Udziały SAP NFS

SAP NetWeaver wymaga udostępnionego magazynu dla transportu, katalogu profilu i tak dalej. W przypadku systemu SAP o wysokiej dostępności ważne jest, aby mieć wysoce dostępne udziały NFS. Należy zdecydować się na architekturę dla udziałów SAP NFS. Jedną z opcji jest tworzenie [klastra wysoce dostępnego systemu NFS na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server,][nfs-ha]który może być współużytkowany przez wiele systemów SAP. 

Inną opcją jest wdrożenie udziałów na [woluminach NFS plików NFS plików NetApp platformy Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  Dzięki usłudze Azure NetApp Files uzyskasz wbudowaną wysoką dostępność udziałów SAP NFS.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Wdrażanie pierwszego systemu SAP w klastrze

Teraz, gdy zdecydowano się na architekturę udziałów SAP NFS, wdrożyć pierwszy system SAP w klastrze, zgodnie z odpowiednią dokumentacją.

* Jeśli korzystasz z serwera NFS o wysokiej dostępności, postępuj zgodnie [z wysoką dostępnością sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).  
* Jeśli używasz woluminów NFS plików usługi Azure NetApp Files, postępuj zgodnie [z wysoką dostępnością dla sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server z plikami NetApp usługi Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

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
* Przetestowano funkcję przeładunku awaryjnego klastra.  
* Zostaną wdrożone udziały nfs dla wszystkich systemów SAP.  

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji SAP NetWeaver

1. Dodaj konfigurację nowo wdrożonego systemu (czyli **NW2**, **NW3**) do istniejącego modułu równoważenia obciążenia platformy Azure, postępując zgodnie z instrukcjami [Wdrażaj moduł azure load balancer ręcznie za pośrednictwem witryny Azure portal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#deploy-azure-load-balancer-manually-via-azure-portal). Dostosuj adresy IP, porty sondy kondycji, reguły równoważenia obciążenia dla twojej konfiguracji.  

2. **[A]** Konfigurowanie rozpoznawania nazw dla dodatkowych systemów SAP. Można użyć serwera DNS lub `/etc/hosts` zmodyfikować na wszystkich węzłach. W tym przykładzie `/etc/hosts` pokazano, jak używać pliku.  Dostosuj adresy IP i nazwy hostów do środowiska. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
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

4. **[A]** Skonfiguruj do instalowania `autofs` systemów plików /sapmnt/SID i /usr/sap/SID/SYS dla dodatkowych systemów SAP wdrażanych w klastrze. W tym przykładzie **NW2** i **NW3**.  

   Zaktualizuj plik `/etc/auto.direct` za pomocą systemów plików dla dodatkowych systemów SAP wdrażanych w klastrze.  

   * Jeśli korzystasz z serwera plików NFS, postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#prepare-for-sap-netweaver-installation)
   * Jeśli korzystasz z plików NetApp platformy Azure, postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#prepare-for-sap-netweaver-installation) 

   Należy ponownie uruchomić `autofs` usługę, aby zainstalować nowo dodane udziały.  

### <a name="install-ascs--ers"></a>Zainstaluj ASCS / ERS

1. Utwórz zasoby klastra wirtualnego adresu IP i sondy kondycji dla wystąpienia ASCS dodatkowego systemu SAP, który wdrażasz w klastrze. Przykład pokazany w tym miejscu jest dla **NW2** i **NW3** ASCS, przy użyciu serwera NFS o wysokiej dostępności.  

   > [!IMPORTANT]
   > Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a zmienny adres IP staje się niedostępny.  
   > W przypadku istniejących klastrów rozruszników zalecamy w przeszłości zastąpienie netcata socatem. Obecnie zalecamy użycie agenta zasobów azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Dla SLES 15/15 SP1 wersja musi być co najmniej resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Należy pamiętać, że zmiana będzie wymagać krótkiego przestoju.  
   > W przypadku istniejących klastrów stymulatora, jeśli konfiguracja została już zmieniona, aby używać socat zgodnie z opisem w [umacnianiu wykrywania równoważenia obciążenia azure,](https://www.suse.com/support/kb/doc/?id=7024128)nie ma wymogu natychmiastowego przejścia na agenta zasobów azure-lb.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Podczas tworzenia zasobów mogą być przypisane do różnych zasobów klastra. Po zgrupowania zostaną one przeniesione do jednego z węzłów klastra. Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

2. **[1]** Zainstaluj SAP NetWeaver ASCS  

   Zainstaluj SAP NetWeaver ASCS jako root, używając wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ASCS. Na przykład dla systemu **NW2,** wirtualna nazwa hosta to <b>msnw2ascs</b>, <b>10.3.1.16</b> i numer wystąpienia użyty dla sondy modułu równoważenia obciążenia, na przykład <b>10</b>. dla systemu **NW3,** wirtualna nazwa hosta to <b>msnw3ascs</b>, <b>10.3.1.13</b> i numer wystąpienia użyty do sondy modułu równoważenia obciążenia, na przykład <b>20</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst. Do zainstalowania systemu SAP za pomocą nazwy hosta wirtualnego można użyć parametru SAPINST_USE_HOSTNAME.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**SID**/ASCS**Instance#**, spróbuj skonfigurować właściciela **na sid**adm i grupowanie na sapsys wystąpienia**ASCS#** i ponów próbę.

3. **[1]** Utwórz zasoby klastra wirtualnego adresu IP i sondy kondycji dla wystąpienia ERS dodatkowego systemu SAP, który wdrażasz w klastrze. Przykład pokazany w tym miejscu jest dla **NW2** i **NW3** ERS, przy użyciu serwera NFS o wysokiej dostępności. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Podczas tworzenia zasobów mogą być przypisane do różnych węzłów klastra. Po zgrupowania zostaną one przeniesione do jednego z węzłów klastra. Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane.  

   Następnie upewnij się, że zasoby nowo utworzonej grupy ERS są uruchomione w węźle klastra, naprzeciwko węzła klastra, w którym zainstalowano wystąpienie ASCS dla tego samego systemu SAP.  Na przykład, jeśli nw2 ASCS został zainstalowany na `slesmsscl1`, a następnie `slesmsscl2`upewnij się, że grupa NW2 ERS jest uruchomiona na .  Grupę NW2 ERS można `slesmsscl2` przeprowadzić, uruchamiając następujące polecenie: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** Zainstaluj SAP NetWeaver ERS

   Zainstaluj SAP NetWeaver ERS jako root w innym węźle, używając wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ERS. Na przykład dla systemu **NW2,** nazwa hosta wirtualnego będzie <b>msnw2ers</b>, <b>10.3.1.17</b> i numer wystąpienia, który był używany do sondy modułu równoważenia obciążenia, na przykład <b>12</b>. W przypadku systemu **NW3**, nazwa wirtualnego hosta <b>msnw3ers</b>, <b>10.3.1.19</b> i numer wystąpienia użytego do sondy modułu równoważenia obciążenia, na przykład <b>22</b>. 

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst. Do zainstalowania systemu SAP za pomocą nazwy hosta wirtualnego można użyć parametru SAPINST_USE_HOSTNAME.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub nowszego. Niższe wersje nie ustawiają poprawnie uprawnień, a instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**NW2**/ERS**Instance#**, spróbuj skonfigurować właściciela **sid**adm i grupy na sapsys folderu ERS**Instance#** i ponów próbę.

   Jeśli konieczne było migrowanie grupy ERS nowo wdrożonego systemu SAP do innego węzła klastra, nie zapomnij usunąć ograniczenia lokalizacji dla grupy ERS. Ograniczenie można usunąć, uruchamiając następujące polecenie (przykład podano dla systemów SAP **NW2** i **NW3**).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** Dostosuj profile wystąpień ASCS/SCS i ERS dla nowo zainstalowanych systemów SAP. Poniższy przykład dotyczy nw2. Należy dostosować profile ASCS/SCS i ERS dla wszystkich wystąpień SAP dodanych do klastra.  
 
 * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

 * Profil ERS

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** Skonfiguruj użytkowników SAP dla nowo wdrożonego systemu SAP, w tym przykładzie **NW2** i **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Dodaj do pliku usługi ASCS i ERS SAP `sapservice` dla nowo zainstalowanego systemu SAP. Poniższy przykład dotyczy systemów SAP **NW2** i **NW3.**  

   Dodaj wpis usługi ASCS do drugiego węzła i skopiuj wpis usługi ERS do pierwszego węzła. Wykonaj polecenia dla każdego systemu SAP w węźle, w którym zainstalowano wystąpienie ASCS dla systemu SAP.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** Tworzenie zasobów klastra SAP dla nowo zainstalowanego systemu SAP. 

   Jeśli używasz architektury serwera 1 w kolejce (ENSA1), zdefiniuj zasoby dla systemów SAP **NW2** i **NW3** w następujący sposób:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Firma SAP wprowadziła obsługę serwera 2, w tym replikacji, od momentu wprowadzenia sap NW 7.52. Począwszy od platformy ABAP 1809, serwer 2 jest instalowany domyślnie. Zobacz SAP note [2630416,](https://launchpad.support.sap.com/#/notes/2630416) aby uzyskać pomoc techniczną serwera 2.
   W przypadku korzystania z architektury serwera 2 w kolejce[(ENSA2)](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)należy zdefiniować zasoby dla systemów SAP **NW2** i **NW3** w następujący sposób:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Jeśli uaktualniasz ze starszej wersji i przełączasz się na serwer 2 w kolejce, zobacz UWAGA SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.
   Poniższy przykład pokazuje stan zasobów klastra, po sap systemów **NW2** i **NW3** zostały dodane do klastra. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Poniższy obraz ekspedycji pokazuje, jak zasoby będą wyglądać w HA Web Konsole (Hawk), z zasobów dla systemu SAP **NW2** rozszerzone.  

   [![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Kontynuuj instalację SAP 

Zakończ instalację SAP, korzystając z:

* [Przygotowanie serwerów aplikacji SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalowanie wystąpienia systemu DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#install-database)
* [Instalowanie podstawowego serwera aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#sap-netweaver-application-server-installation)
* Instalowanie co najmniej jednego dodatkowego wystąpienia aplikacji SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testowanie konfiguracji klastra z wieloma identyfikatorami SID

Następujące testy są podzbiór przypadków testowych w przewodnikach najlepszych rozwiązań SUSE. Są one wliczone w cenę. Aby uzyskać pełną listę testów klastra, należy znaleźć następującą dokumentację:

* Jeśli korzystasz z serwera NFS o wysokiej dostępności, postępuj zgodnie [z wysoką dostępnością sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).  
* Jeśli używasz woluminów NFS plików usługi Azure NetApp Files, postępuj zgodnie [z wysoką dostępnością dla sap NetWeaver na maszynach wirtualnych platformy Azure na serwerze SUSE Linux Enterprise Server z plikami NetApp usługi Azure dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)

Zawsze przeczytaj przewodniki dotyczące najlepszych rozwiązań SUSE i wykonaj wszystkie dodatkowe testy, które mogły zostać dodane.  
Testy, które są prezentowane są w dwóch węzłach, klaster multi-SID z trzech systemów SAP zainstalowanych.  

1. Test HAGetFailoverConfig i HACheckFailoverConfig

   Uruchom następujące polecenia <sapsid>jako adm w węźle, w którym jest aktualnie uruchomione wystąpienie ASCS. Jeśli polecenia nie powiodą się z FAIL: Niewystarczające pamięci, może to być spowodowane przez myślniki w nazwa hosta. Jest to znany problem i zostanie rozwiązany przez SUSE w pakiecie sap-suse-cluster-connector.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Ręczna migracja wystąpienia ASCS. W przykładzie pokazano migrację wystąpienia ASCS dla systemu SAP NW2.  
   Stan zasobu, przed rozpoczęciem testu:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Uruchom następujące polecenia jako katalog główny, aby przeprowadzić migrację wystąpienia NW2 ASCS.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Stan zasobu po teście:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Test HAFailoverToNode. Test przedstawiony w tym miejscu pokazuje migrację wystąpienia ASCS dla systemu SAP NW2.  

   Stan zasobu przed rozpoczęciem testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Uruchom następujące polecenia jako **nw2**adm, aby przeprowadzić migrację wystąpienia NW2 ASCS.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Stan zasobu po teście:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Uruchom następujące polecenie jako katalog główny w węźle, w którym jest uruchomione co najmniej jedno wystąpienie ASCS. W tym przykładzie wykonaliśmy `slesmsscl2`polecenie on , gdzie są uruchomione wystąpienia ASCS dla NW1 i NW3.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Jeśli używasz SBD, rozrusznik serca nie powinien automatycznie uruchamiać się na zabitym węźle. Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Użyj następujących poleceń, aby uruchomić rozrusznik serca w zabitym węźle, wyczyścić komunikaty SBD i wyczyścić nieudane zasoby.

   ```# run as root
   # list the SBD device(s)
   slesmsscl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   slesmsscl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   slesmsscl2:~ # systemctl start pacemaker
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW1_ERS02
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW2_ERS12
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Stan zasobu po teście:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]
