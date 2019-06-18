---
title: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux | Dokumentacja firmy Microsoft
description: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: sedusch
ms.openlocfilehash: 4e224a1abf72bfa068bebaf971e34c492b15d7c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142989"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i instalacji o wysokiej dostępności systemu SAP NetWeaver 7.50.
W przykładowych konfiguracji polecenia instalacji itp. Numer wystąpienia ASCS 00 numer 02 wystąpienia Wywołujących i służy NW1 identyfikator systemu SAP. Nazwy zasobów (na przykład maszyny wirtualne, sieci wirtualnych) w przykładzie założono użycie [szablonu ASCS/SCS] [ template-multisid-xscs] przy użyciu prefiksu NW1 zasobu do utworzenia zasobów.

Najpierw przeczytaj następujące uwagi SAP i dokumenty

* Uwaga SAP [1928533], która zawiera:
  * Listę rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Informacje o pojemności ważne w przypadku rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP i systemu operacyjnego (OS) i kombinacji bazy danych
  * Wymagana wersja jądra SAP dla Windows i Linux w systemie Microsoft Azure

* Uwaga SAP [2015553] wymieniono wymagania wstępne dotyczące wdrażania oprogramowania SAP obsługiwane przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2002167] zawiera zalecane ustawienia systemu operacyjnego Red Hat Enterprise Linux
* SAP Note [2009879] has SAP HANA Guidelines for Red Hat Enterprise Linux
* Uwaga SAP [2178632] zawiera szczegółowe informacje o metrykach wszystkie funkcje monitorowania zgłoszone dla rozwiązania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] zawiera informacje o licencjonowaniu SAP, w systemie Linux na platformie Azure.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów rozszerzenia platformy Azure Enhanced Monitoring dla rozwiązania SAP.
* [WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane informacje o SAP dla systemu Linux.
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP w systemie Linux][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][dbms-guide]
* [W dokumentacji produktu Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [Oprogramowanie SAP Netweaver w klastrze program pacemaker](https://access.redhat.com/articles/3150081)
* Dokumentacja systemu RHEL ogólne
  * [Omówienie dodatek wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatek wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatek wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurowanie ASCS/Wywołujących środowiska SAP NetWeaver przy użyciu autonomicznego zasobów w RHEL w wersji 7.5](https://access.redhat.com/articles/3569681)
  * [Skonfiguruj S/4HANA, SAP ASCS/Wywołujących przy użyciu serwera autonomicznego umieścić 2 (ENSA2) w program Pacemaker w systemie RHEL ](https://access.redhat.com/articles/3974941)
* Usługa Azure RHEL dokumentacji:
  * [Zasady pomocy technicznej w przypadku klastrów o wysokiej dostępności systemu RHEL — maszyny wirtualne platformy Microsoft Azure jako elementów członkowskich klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie Red Hat Enterprise Linux 7.4 (lub nowszy) o wysokiej dostępności klastra w systemie Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Omówienie

Aby uzyskać wysoką dostępność, oprogramowanie SAP NetWeaver wymaga magazynu udostępnionego. GlusterFS jest skonfigurowany w klastrze oddzielonym i mogą być używane przez wiele systemów SAP.

![Oprogramowanie SAP NetWeaver wysokiej dostępności Przegląd](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Wywołujących i bazy danych SAP HANA, użyj nazwy hosta wirtualnego i wirtualnych adresów IP. Na platformie Azure modułu równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację (A) SCS i Wywołujących modułu równoważenia obciążenia.

> [!IMPORTANT]
> — Wiele identyfikatorów SID klastrowania SAP ASCS/Wywołujących z systemu Red Hat Linux jako system operacyjny gościa na maszynach wirtualnych Azure **nieobsługiwane**. — Wiele identyfikatorów SID klastrowania w tym artykule opisano instalację wielu wystąpień SAP ASCS/Wywołujących o różnych identyfikatorach SID w jednym klastrze program Pacemaker.

### <a name="ascs"></a>(A)SCS

* Konfiguracja frontonu
  * Adres IP 10.0.0.7
* Konfiguracja zaplecza
  * Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią (A) SCS/Wywołujących klastra
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * 32<strong>&lt;nr&gt;</strong>  TCP
  * 36<strong>&lt;nr&gt;</strong>  TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong>  TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Konfiguracja frontonu
  * Adres IP 10.0.0.8
* Konfiguracja zaplecza
  * Podłączone do podstawowych interfejsów sieciowych wszystkich maszyn wirtualnych, które powinny być częścią (A) SCS/Wywołujących klastra
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * 32<strong>&lt;nr&gt;</strong>  TCP
  * 33<strong>&lt;nr&gt;</strong>  TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-glusterfs"></a>Konfigurowanie GlusterFS

Oprogramowanie SAP NetWeaver wymaga magazynu udostępnionego dla katalogu transportu i profilu. Odczyt [GlusterFS na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux for SAP NetWeaver] [ glusterfs-ha] na temat sposobu konfigurowania GlusterFS środowiska SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie () SCS

Możesz użyć szablonu usługi Azure z usługi GitHub do wdrażania wszystkich wymaganych zasobów platformy Azure, łącznie z maszynami wirtualnymi, dostępność zestawu i modułu równoważenia obciążenia lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux przy użyciu szablonu platformy Azure

W portalu Azure Marketplace zawiera obraz Red Hat Enterprise Linux, który służy do wdrażania nowych maszyn wirtualnych. Można użyć jednego z szablonów szybkiego startu w usłudze GitHub do wdrażania wszystkich wymaganych zasobów. Szablon umożliwia wdrożenie maszyn wirtualnych, moduł równoważenia obciążenia, dostępności, ustaw itp. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablonu ASCS/SCS] [ template-multisid-xscs] w witrynie Azure portal  
1. Wprowadź następujące parametry
   1. Prefiks zasobów  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla zasobów, które są wdrażane.
   1. Typ stosu  
      Wybierz typ stos oprogramowania SAP NetWeaver
   1. Typ systemu operacyjnego  
      Wybierz jeden z dystrybucje systemu Linux. W tym przykładzie wybierz RHEL 7
   1. Typ bazy danych  
      Wybierz platformy HANA
   1. Liczba systemów SAP  
      Numer systemu SAP, które są uruchamiane w tym klastrze. Wybierz 1.
   1. Dostępność systemu  
      Wybierz opcję wysokiej dostępności
   1. Nazwa użytkownika administratora, hasło administratora lub protokołu SSH  
      Tworzony jest nowy użytkownik, który może służyć do logowania się na tym komputerze.
   1. Identyfikator podsieci  
   Jeśli chcesz wdrożyć maszynę Wirtualną w istniejącej sieci wirtualnej, w którym masz zdefiniowanej podsieci maszyny Wirtualnej powinien być przypisany do nazwy identyfikator odpowiednią podsieć. Identyfikator zwykle wygląda /subscriptions/ **&lt;identyfikator subskrypcji&gt;** /resourceGroups/ **&lt;nazwy grupy zasobów&gt;** /providers/ Microsoft.Network/virtualNetworks/ **&lt;nazwa sieci wirtualnej&gt;** /subnets/ **&lt;Nazwa podsieci&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pomocą witryny Azure portal

Najpierw należy utworzyć maszyny wirtualne, dla tego klastra. Następnie utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli zaplecza.

1. Tworzenie grupy zasobów
1. Create a Virtual Network
1. Tworzenie zestawu dostępności  
   Domena aktualizacji Maksymalny zestaw
1. Tworzenie maszyny wirtualnej 1  
   Użyj co najmniej RHEL 7, na tej ilustracji przykład Red Hat Enterprise Linux 7.4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej RHEL 7, na tej ilustracji przykład Red Hat Enterprise Linux 7.4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Dodaj co najmniej jeden dysk danych do obu maszyn wirtualnych  
   Dyski danych są używanedla/usr/sap/`<SAPSID`> katalog
1. Utwórz moduł równoważenia obciążenia (wewnętrzny)  
   1. Tworzenie adresów IP frontonu
      1. Adres IP 10.0.0.7 ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **nw1-ascs-frontend**)
         1. Ustawić przypisania jako statyczny i podaj adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 Wywołujących ASCS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla Wywołujących (na przykład **10.0.0.8** i **nw1-aers-backend**)
   1. Utwórz pule zaplecza
      1. Tworzenie puli zaplecza dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz opcję pule zaplecza, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **nw1-ascs-backend**)
         1. Kliknij przycisk Dodaj maszynę wirtualną.
         1. Wybierz zestaw dostępności została utworzona wcześniej
         1. Wybierz maszyny wirtualne (a) SCS klastra
         1. Kliknij przycisk OK
      1. Tworzenie puli zaplecza dla Wywołujących ASCS
         * Powtórz powyższe kroki, aby utworzyć pulę zaplecza dla Wywołujących (na przykład **nw1-aers-backend**)
   1. Utworzenie sond kondycji.
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowego sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz opcję TCP jako protokół, port 620**00**, Zachowaj 5 interwał i próg złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS Wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla Wywołujących (na przykład 621**02** i **nw1-aers-hp**)
   1. Reguły równoważenia obciążenia
      1. 32**00** protokołu TCP na potrzeby ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **nw1-lb-3200**)
         1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład **nw1-ascs-frontend**)
         1. Zachowaj protokołu **TCP**, wprowadź numer portu **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Pamiętaj włączyć pływającego adresu IP**
         1. Kliknij przycisk OK
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 i protokołu TCP na potrzeby ASCS
      1. Dodatkowe porty dla Wywołujących ASCS
         * Powtórz powyższe kroki dla portów 33**02**, 5**02**13, 5**02**14, 5**02**16 i protokołu TCP na potrzeby Wywołujących ASCS

> [!IMPORTANT]
> Nie należy włączać czasowe TCP na maszynach wirtualnych Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji nie powiedzie się. Ustaw parametr **net.ipv4.tcp_timestamps** do **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) do utworzenia podstawowego program Pacemaker klastra dla tego serwera () SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]**  Tworzenia udostępnionych katalogów

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

1. **[A]**  GlusterFS zainstalować klienta i inne wymagania

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]**  Sprawdzanie wersji zasobu agentów sap

   Upewnij się, że wersję zainstalowanego pakietu zasobów agentów sap jest co najmniej 3.9.5-124.el7
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


1. **[A]**  Wpisy instalacji dodatku

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Zainstaluj nowe udziały

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Skonfigurować wymiany plików

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta aby aktywować zmiany

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]**  Konfiguracji systemu RHEL

   Konfigurowanie systemu RHEL, zgodnie z opisem w Uwaga SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie SAP NetWeaver ASCS/Wywołujących

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia ASCS

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

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

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

1. **[1]**  Zainstalować oprogramowanie SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako główny urząd certyfikacji na pierwszym węźle za pomocą wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>nw1 ascs</b>, <b>10.0.0.7</b> i numer, który był używany przez sondy modułu równoważenia obciążenia, na przykład wystąpienia <b>00</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się utworzyć podfolder w/usr/sap/**NW1**/ASCS**00**, spróbuj ustawić właściciela i grupy ASCS**00** folder, a następnie spróbuj ponownie.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia Wywołujących

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
 
   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

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

1. **[2]**  Zainstalować oprogramowanie SAP NetWeaver Wywołujących  

   Zainstaluj oprogramowanie SAP NetWeaver Wywołujących jako główny urząd certyfikacji na drugi węzeł, za pomocą wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla Wywołujących, na przykład <b>nw1 aers</b>, <b>10.0.0.8</b> i numer, który był używany przez sondy modułu równoważenia obciążenia, na przykład wystąpienia <b>02</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się utworzyć podfolder w/usr/sap/**NW1**/ERS**02**, spróbuj ustawić właściciela i grupy Wywołujących**02** folder, a następnie spróbuj ponownie.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]**  Adapt ASCS/SCS i Wywołujących wystąpienia profilów

   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil Wywołujących

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]**  Skonfigurować podtrzymywaniu

   Komunikacja między serwerem aplikacji SAP NetWeaver i ASCS/SCS odbywa się za pomocą programowego modułu równoważenia obciążenia. Moduł równoważenia obciążenia rozłączy nieaktywnego połączenia po upłynięciu limitu czasu można konfigurować. Aby tego uniknąć, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienianie ustawień systemu Linux. Odczyt [1410736 Uwaga SAP] [ 1410736] Aby uzyskać więcej informacji.

   ASCS/SCS profilu parametr umieścić/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Należy zaktualizować plik /usr/sap/sapservices

   Aby zapobiec początku wystąpienia przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane przez program Pacemaker musi być oznaczone jako komentarz z pliku /usr/sap/sapservices. Nie komentarz wystąpienie SAP HANA jeśli będą używane z starszy HANA

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]**  Utworzenie zasobów klastra SAP

  Jeśli przy użyciu architektury serwera 1 umieścić w kolejce (ENSA1), zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP wprowadzono obsługę umieścić serwer 2, w tym replikacji od SAP NW 7.52. Począwszy od 1809 platformy ABAP umieścić serwer 2 jest instalowany domyślnie. Zobacz SAP Uwaga [2630416](https://launchpad.support.sap.com/#/notes/2630416) obsługi serwera 2 umieścić w kolejce.
   Jeśli przy użyciu architektury serwera 2 umieścić w kolejce ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zainstaluj agenta zasobów zasobów agentów — sap-4.1.1-12.el7.x86_64 lub nowszej i zdefiniować zasoby w następujący sposób:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Jeśli uaktualnienia ze starszej wersji, a przełączanie umieścić serwer 2, zobacz SAP Uwaga [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

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

1. **[A]**  Dodawanie reguły zapory dla ASCS i Wywołujących w obu węzłach

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

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera programu SAP NetWeaver aplikacji

Niektóre bazy danych wymagają, że instalacja wystąpienia bazy danych jest wykonywana na serwerze aplikacji. Przygotowywanie maszyn wirtualnych serwera aplikacji, aby móc używać ich w tych przypadkach.

W poniższych krokach przyjęto założenie, zainstalować serwer aplikacji na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki przedstawione poniżej (np. Konfigurowanie rozpoznawania nazw hostów) nie są potrzebne.

1. Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

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

1. Utwórz katalog sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Zainstaluj klienta GlusterFS oraz inne wymagania

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Dodaj pozycje instalacji

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Zainstaluj nowe udziały

   <pre><code>sudo mount -a
   </code></pre>

1. Konfigurowanie pliku wymiany
 
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

   Uruchom ponownie agenta aby aktywować zmiany

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie oprogramowanie SAP NetWeaver jest zainstalowany na platformie SAP HANA. Wszystkie obsługiwane bazy danych można użyć dla tej instalacji. Aby uzyskać więcej informacji na temat sposobu instalowania oprogramowania SAP HANA na platformie Azure, zobacz [wysoką dostępność środowiska SAP Hana na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux][sap-hana-ha]. Aby uzyskać listę obsługiwanych baz danych, zobacz [1928533 Uwaga SAP][1928533].

1. Uruchom program instalacyjny wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako katalogu głównego przy użyciu wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych, na przykład <b>nw1 db</b> i <b>10.0.0.13</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja programu SAP NetWeaver aplikacji serwera

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotowanie serwera aplikacji

   Wykonaj kroki opisane w rozdziale [przygotowania serwera aplikacji SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

1. Instalowanie serwera aplikacji SAP NetWeaver

   Instalacja serwera aplikacji SAP NetWeaver podstawowej lub dodatkowej.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja oprogramowania SAP HANA bezpiecznego magazynu

   Aktualizacja oprogramowania SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualnego ustawień replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów jako \<sapsid > usługi adm

   <pre><code>hdbuserstore List
   </code></pre>

   To powinno wyświetlić listę wszystkich wpisów i powinien wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Dane wyjściowe pokazują, wskazuje wpis domyślny adres IP maszyny wirtualnej, a nie adres IP modułu równoważenia obciążenia. Ten wpis, trzeba je zmienić i wskazać do wirtualnej nazwy hosta modułu równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w powyższych danych wyjściowych) i nazwę bazy danych (**HN1** w powyższych danych wyjściowych)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

1. Ręcznego przeprowadzania migracji wystąpienia ASCS

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

   Uruchom następujące polecenia jako użytkownik główny, aby przeprowadzić migrację wystąpienia ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

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

   Uruchom następujące polecenie, jako katalog główny na węźle, gdzie jest uruchomione wystąpienie ASCS

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Stan po uruchomieniu węzła ponownie powinien wyglądać następująco.

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

   Użyj następującego polecenia, aby wyczyścić zasoby nie powiodło się.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

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

1. Kasowanie procesu serwera wiadomości

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

   Uruchom następujące polecenia jako użytkownik główny, aby zidentyfikować proces serwera wiadomości i zamknij go.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli możesz tylko kill serwer komunikatów raz, zostanie ono uruchomione przez sapstart. Jeśli go często wystarczająco dużo, program Pacemaker będą ostatecznie kill przenieść to wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako użytkownik główny, aby wyczyścić stanu zasobów wystąpienia ASCS i Wywołujących po przeprowadzeniu testu.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

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

1. Kasowanie procesu serwera umieścić w kolejce

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

   Uruchom następujące polecenia jako użytkownik główny w węźle, gdzie jest uruchomione wystąpienie ASCS, można skasować serwera umieścić w kolejce.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Wystąpienie ASCS powinien natychmiast przełączyć w tryb failover do innego węzła. Wystąpienie Wywołujących powinien również pracy awaryjnej po uruchomieniu wystąpienia ASCS. Uruchom następujące polecenia jako użytkownik główny, aby wyczyścić stanu zasobów wystąpienia ASCS i Wywołujących po przeprowadzeniu testu.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

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

1. Zakończ proces serwera replikacji umieścić w kolejce

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

   Uruchom następujące polecenie jako główny w węźle, gdzie jest uruchomione wystąpienie Wywołujących, można skasować proces serwera umieścić w kolejce replikacji.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli uruchomisz wyłącznie polecenie raz, sapstart spowoduje ponowne uruchomienie procesu. Jeśli zostanie ono uruchomione często wystarczający będzie sapstart nie ponowne uruchomienie tego procesu i zasobów będzie w stanie zatrzymania. Uruchom następujące polecenia jako użytkownik główny, aby wyczyścić stanu zasobów wystąpienia Wywołujących po przeprowadzeniu testu.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

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

1. Kasowanie procesu sapstartsrv umieścić w kolejce

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

   Uruchom następujące polecenia jako użytkownik główny w węźle, gdzie jest uruchomione ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Proces sapstartsrv zawsze należy ponownie uruchomić przez agenta zasobów program Pacemaker w ramach monitorowania. Stan zasobu po przeprowadzeniu testu:

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

## <a name="next-steps"></a>Kolejne kroki

* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]
