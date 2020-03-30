---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NW w rhel | Dokumenty firmy Microsoft
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
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 73b958149d9d6d907785fe1c2c56b8198bb91f70
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351100"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver w systemie Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania struktury klastra i instalowania wysoce dostępnego systemu SAP NetWeaver 7.50.
W przykładowych konfiguracjach, poleceniach instalacji itp. Używany jest numer wystąpienia ASCS 00, numer wystąpienia ERS 02 i identyfikator systemu SAP NW1. Nazwy zasobów (na przykład maszyny wirtualne, sieci wirtualne) w przykładzie zakładają, że do utworzenia zasobów użyto [szablonu ASCS/SCS][template-multisid-xscs] z prefiksem zasobu NW1.

Najpierw przeczytaj poniższe uwagi i dokumenty SAP

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux na platformie Microsoft Azure

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
* [Dokumentacja produktu do przechowywania Red Hat Gluster](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver w klastrze rozruszników serca](https://access.redhat.com/articles/3150081)
* Ogólna dokumentacja RHEL
  * [Omówienie dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatkami o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurowanie ASCS/ERS dla SAP Netweaver z autonomicznymi zasobami w RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurowanie SAP S/4HANA ASCS/ERS z autonomicznym serwerem Enqueue Server 2 (ENSA2) w rozruszniku serca na RHEL](https://access.redhat.com/articles/3974941)
* Dokumentacja RHEL specyficzne dla platformy Azure:
  * [Zasady pomocy technicznej dotyczące klastrów wysokiej dostępności RHEL — maszyny wirtualne platformy Microsoft Azure jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie klastra o wysokiej dostępności w systemie Microsoft Azure dla systemu Red Hat Enterprise 7.4 (lub nowszych)](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, SAP NetWeaver wymaga udostępnionego magazynu. GlusterFS jest skonfigurowany w oddzielnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS i baza danych SAP HANA używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy użycie [standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). Na poniższej liście przedstawiono konfigurację (A)SCS i ERS load balancer.

### <a name="ascs"></a>1lit. RC2 (rc2)

* Konfiguracja frontu
  * Adres IP 10.0.0.7
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
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
  * Adres IP 10.0.0.8
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * W przypadku korzystania z podstawowego modułu równoważenia obciążenia należy utworzyć reguły równoważenia obciążenia dla następujących portów
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Konfiguracja wewnętrznej bazy danych
  * Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra (A)SCS/ERS

## <a name="setting-up-glusterfs"></a>Konfigurowanie GlusterFS

SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportu i profilu. Przeczytaj [glusterfs na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla SAP NetWeaver,][glusterfs-ha] jak skonfigurować GlusterFS dla SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie (A)SCS

Szablon platformy Azure z usługi GitHub można użyć do wdrożenia wszystkich wymaganych zasobów platformy Azure, w tym maszyn wirtualnych, zestawu dostępności i modułu równoważenia obciążenia lub można ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pośrednictwem szablonu platformy Azure

Portal Azure Marketplace zawiera obraz systemu Red Hat Enterprise Linux, którego można użyć do wdrożenia nowych maszyn wirtualnych. Można użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności itp. Aby wdrożyć szablon, wykonaj następujące czynności:

1. Otwieranie [szablonu ASCS/SCS][template-multisid-xscs] w witrynie Azure portal  
1. Wprowadź następujące parametry
   1. Prefiks zasobu  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla zasobów, które są wdrażane.
   1. Typ stosu  
      Wybierz typ stosu SAP NetWeaver
   1. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji Linuksa. W tym przykładzie wybierz RHEL 7
   1. Typ bazy danych  
      Wybierz HANA
   1. Liczba systemów SAP  
      Liczba systemu SAP uruchamianego w tym klastrze. Wybierz pozycję 1.
   1. Dostępność systemu  
      Wybierz HA
   1. Nazwa użytkownika administratora, hasło administratora lub klucz SSH  
      Tworzony jest nowy użytkownik, którego można użyć do logowania się na komputerze.
   1. Identyfikator podsieci  
   Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda jak /subskrypcje/**&lt;&gt;identyfikator subskrypcji**/resourceGroups/**&lt;nazwa&gt;grupy zasobów**/providers/Microsoft.Network/virtualNetworks/**&lt;nazwa&gt;sieci wirtualnej**/podsieci/**&lt;nazwa&gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem witryny Azure portal

Najpierw należy utworzyć maszyny wirtualne dla tego klastra. Następnie należy utworzyć moduł równoważenia obciążenia i używać maszyn wirtualnych w puli wewnętrznej bazy danych.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Ustawianie domeny aktualizacji maksymalnej
1. Tworzenie maszyny wirtualnej 1  
   Użyj co najmniej RHEL 7, w tym przykładzie obrazu Red Hat Enterprise Linux 7.4<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Wybierz zestaw dostępności utworzony wcześniej  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej RHEL 7, w tym przykładzie obrazu Red Hat Enterprise Linux 7.4<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Wybierz zestaw dostępności utworzony wcześniej  
1. Dodawanie co najmniej jednego dysku danych do obu maszyn wirtualnych  
   Dyski danych są używane dla katalogu /usr/sap/`<SAPSID`>
1. Tworzenie modułu równoważenia obciążenia (wewnętrznego, standardowego):  
   1. Tworzenie adresów IP frontendu
      1. Adres IP 10.0.0.7 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli IP frontendu (na przykład **nw1-ascs-frontend)**
         1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla ERS (na przykład **10.0.0.8** i **nw1-aers-frontend)**
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
      1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw1-backend)**
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz pozycję Maszyna wirtualna.
      1. Wybierz maszyny wirtualne klastra (A)SCS i ich adresy IP.
      1. Kliknij pozycję Dodaj.
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz protokół TCP jako protokół, port 620**00**, zachowaj interwał 5 i próg w złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla ERS (na przykład 621**02** i **nw1-aers-hp)**
   1. Reguły równoważenia obciążenia
      1. Reguły równoważenia obciążenia dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **nw1-lb-ascs)**
         1. Wybierz adres IP frontendu, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **nw1-ascs-frontend,** **nw1-backend** i **nw1-ascs-hp**)
         1. Wybieranie **portów wysokiej haw**
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
         * Powtórz powyższe kroki, aby utworzyć reguły równoważenia obciążenia dla ERS (na przykład **nw1-lb-ers**)
1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia (wewnętrznego), wykonaj następujące kroki:  
   1. Tworzenie adresów IP frontendu
      1. Adres IP 10.0.0.7 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli IP frontendu (na przykład **nw1-ascs-frontend)**
         1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla ERS (na przykład **10.0.0.8** i **nw1-aers-frontend)**
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
      1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw1-backend)**
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz utworzony wcześniej zestaw dostępności
      1. Wybieranie maszyn wirtualnych klastra (A)SCS
      1. Kliknij przycisk OK
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz protokół TCP jako protokół, port 620**00**, zachowaj interwał 5 i próg w złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS ERS
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla ERS (na przykład 621**02** i **nw1-aers-hp)**
   1. Reguły równoważenia obciążenia
      1. 32**00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **nw1-lb-3200**)
         1. Wybierz adres IP frontendu, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **nw1-ascs-frontend)**
         1. Zachowaj protokół **TCP**, wprowadź port **3200**
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36**00,** 39**00,** 81**00,** 5**00**13, 5**00**14, 5**00**16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS ERS
         * Powtórz powyższe kroki dla portów 33**02**,**5 02**13, 5**02**14, 5**02**16 i TCP dla ASCS ERS

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

Wykonaj kroki opisane w [sekcji Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure,](high-availability-guide-rhel-pacemaker.md) aby utworzyć podstawowy klaster rozrusznika serca dla tego (A)serwera SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji SAP NetWeaver

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** Tworzenie katalogów udostępnionych

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Zainstaluj klienta GlusterFS i inne wymagania

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Sprawdź wersję resource-agents-sap

   Upewnij się, że wersja zainstalowanego pakietu resource-agents-sap jest co najmniej 3.9.5-124.el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** Dodawanie wpisów instalacji

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Zamontuj nowe akcje

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurowanie pliku SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>

1. **Konfiguracja [A]** RHEL

   Konfigurowanie RHEL zgodnie z opisem w SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalacja SAP NetWeaver ASCS/ERS

1. **[1]** Tworzenie wirtualnego zasobu IP i sondy kondycji dla wystąpienia ASCS

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** Zainstaluj SAP NetWeaver ASCS  

   Zainstaluj SAP NetWeaver ASCS jako root w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ASCS, na przykład <b>nw1-ascs</b>, <b>10.0.0.7</b> i numer wystąpienia użyty do sondy modułu równoważenia obciążenia, na przykład <b>00</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**NW1**/ASCS**00**, spróbuj skonfigurować właściciela i grupę folderu ASCS**00** i ponowić próbę.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Tworzenie wirtualnego zasobu IP i sondy kondycji dla wystąpienia ERS

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** Zainstaluj SAP NetWeaver ERS  

   Zainstaluj SAP NetWeaver ERS jako root w drugim węźle przy użyciu wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ERS, na przykład <b>nw1-aers</b>, <b>10.0.0.8</b> i numer wystąpienia użyty do sondy modułu równoważenia obciążenia, na przykład <b>02</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**NW1**/ERS**02**, spróbuj skonfigurować właściciela i grupę folderu ERS**02** i ponowić próbę.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** Dostosowywanie profili wystąpień ASCS/SCS i ERS

   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** Konfigurowanie keep alive

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest routowana za pośrednictwem modułu równoważenia obciążenia oprogramowania. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po konfigurowalnym przesunieniu czasu. Aby temu zapobiec, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienić ustawienia systemu Linux. Więcej informacji można znaleźć [w notatce SAP 1410736.][1410736]

   Parametr profilu ASCS/SCS enque/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Aktualizacja pliku /usr/sap/sapservices

   Aby zapobiec rozpoczęciu wystąpień przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane przez program Pacemaker muszą być komentowane z pliku /usr/sap/sapservices. Nie komentować wystąpienia SAP HANA, jeśli będzie on używany z HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** Tworzenie zasobów klastra SAP

  W przypadku korzystania z architektury serwera 1 (ENSA1) należy zdefiniować zasoby w następujący sposób:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Firma SAP wprowadziła obsługę serwera 2, w tym replikacji, od momentu wprowadzenia sap NW 7.52. Począwszy od platformy ABAP 1809, serwer 2 jest instalowany domyślnie. Zobacz SAP note [2630416,](https://launchpad.support.sap.com/#/notes/2630416) aby uzyskać pomoc techniczną serwera 2.
   Jeśli używasz architektury serwera enqueue server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zainstaluj agenta zasobów resource-agents-sap-4.1.1-12.el7.x86_64 lub nowszą i zdefiniuj zasoby w następujący sposób:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Jeśli uaktualniasz ze starszej wersji i przełączasz się na serwer 2 w kolejce, zobacz UWAGA SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Limity czasu w powyższej konfiguracji są tylko przykłady i może być konieczne dostosowanie do określonej konfiguracji SAP. 

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** Dodawanie reguł zapory dla ASCS i ERS w obu węzłach

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera aplikacji SAP NetWeaver

Niektóre bazy danych wymagają, aby instalacja wystąpienia bazy danych była wykonywana na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby móc ich używać w takich przypadkach.

Kroki poniżej założono, że zainstalować serwer aplikacji na serwerze innym niż ASCS/SCS i HANA serwerów. W przeciwnym razie niektóre z poniższych kroków (takich jak konfigurowanie rozpoznawania nazw hostów) nie są potrzebne.

1. Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Tworzenie katalogu sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Zainstaluj klienta GlusterFS i inne wymagania

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Dodawanie wpisów instalacji

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Zamontuj nowe akcje

   <pre><code>sudo mount -a
   </code></pre>

1. Konfigurowanie pliku SWAP
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie SAP NetWeaver jest zainstalowany na SAP HANA. Do tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania systemu SAP HANA na platformie Azure, zobacz [Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako katalog główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontendu modułu równoważenia obciążenia dla bazy danych, na przykład <b>nw1-db</b> i <b>10.0.0.13</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotowanie serwera aplikacji

   Wykonaj kroki opisane w rozdziale [Przygotowanie serwera aplikacji SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

1. Instalowanie serwera aplikacji SAP NetWeaver

   Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja bezpiecznego magazynu SAP HANA

   Zaktualizuj bezpieczny magazyn SAP HANA, aby wskazywał wirtualną nazwę konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów jako \<sapsid>adm

   <pre><code>hdbuserstore List
   </code></pre>

   Powinno to wymieniać wszystkie wpisy i wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Dane wyjściowe pokazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis musi zostać zmieniony, aby wskazać wirtualną nazwę hosta modułu równoważenia obciążenia. Upewnij się, że używasz tego samego portu **(30313** w powyższym wyjściu) i nazwy bazy danych **(HN1** w powyższym wyjściu)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

1. Ręczna migracja wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako katalog główny, aby przeprowadzić migrację wystąpienia ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenie jako katalog główny w węźle, w którym działa wystąpienie ASCS

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Użyj następującego polecenia, aby wyczyścić zasoby, których nie powiodło się.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Proces serwera wiadomości zabicia

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako katalog główny, aby zidentyfikować proces serwera wiadomości i zabić go.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli serwer wiadomości zostanie zabity tylko raz, `sapstart`zostanie on ponownie uruchomiony przez program . Jeśli zabijesz go wystarczająco często, rozrusznik serca ostatecznie przeniesie wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ASCS i ERS po teście.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Zabij proces serwera w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle, w którym jest uruchomione wystąpienie ASCS w celu zabicia serwera w kolejce.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Wystąpienie ASCS należy natychmiast awaryjnie do innego węzła. Wystąpienie ERS należy również awaryjnie po uruchomieniu wystąpienia ASCS. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ASCS i ERS po teście.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Zabij proces serwera replikacji w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenie jako katalog główny w węźle, w którym jest uruchomione wystąpienie ERS, aby zabić proces serwera replikacji w kolejce.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli polecenie zostanie uruchomione `sapstart` tylko raz, uruchomi się ponownie proces. Jeśli uruchomisz go `sapstart` wystarczająco często, nie uruchomi ponownie procesu i zasób będzie w stanie zatrzymania. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ERS po teście.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Zabij proces sapstartsrv w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako katalog główny w węźle, w którym jest uruchomiony system ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Proces sapstartsrv powinny być zawsze ponownie uruchamiane przez agenta zasobów rozrusznika jako część monitorowania. Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Ha dla SAP NW na maszynach wirtualnych platformy Azure na RHEL dla aplikacji SAP — przewodnik po wielu identyfikatorach SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i zaplanować odzyskiwanie po awarii sap HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure.](hana-overview-high-availability-disaster-recovery.md)
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]
