---
title: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux przy użyciu usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503580"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux przy użyciu usługi Azure Files NetApp dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i instalacji o wysokiej dostępności systemu SAP NetWeaver 7.50, za pomocą [usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
W przykładowych konfiguracji polecenia instalacji itp. Wystąpienie ASCS jest liczbą 00, wystąpienie Wywołujących jest numer 01, wystąpienie aplikacji głównej (PAS) 02 i wystąpienia aplikacji (AAS) to 03. QAS identyfikator systemu SAP jest używany. 

Warstwa bazy danych nie jest omówiona szczegółowo w tym artykule.  

Najpierw przeczytaj następujące uwagi SAP i dokumenty:

* [Dokumentacja usługi Azure plików NetApp][anf-azure-doc] 
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
* [Aplikacje środowiska SAP NetApp na Microsoft Azure przy użyciu usługi Azure Files NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Przegląd

Wysoka availability(HA) usługi centralne SAP Netweaver wymaga magazynu udostępnionego.
Aby to osiągnąć w systemie Red Hat Linux do tej pory było konieczne tworzenie klastrze oddzielonym GlusterFS o wysokiej dostępności. 

Teraz jest możliwe uzyskanie SAP Netweaver wysokiej dostępności przy użyciu magazynu udostępnionego, wdrożone w usłudze Azure Files NetApp. Za pomocą usługi Azure Files NetApp dla magazynu udostępnionego eliminuje potrzebę stosowania dodatkowych [klastra GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Program pacemaker nadal jest wymagany dla HA services(ASCS/SCS) centralne SAP Netweaver.

![Oprogramowanie SAP NetWeaver wysokiej dostępności Przegląd](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Wywołujących i bazy danych SAP HANA, użyj nazwy hosta wirtualnego i wirtualnych adresów IP. Na platformie Azure modułu równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia z przodu osobne adresy IP () SCS i Wywołujących.

> [!IMPORTANT]
> — Wiele identyfikatorów SID klastrowania SAP ASCS/Wywołujących z systemu Red Hat Linux jako system operacyjny gościa na maszynach wirtualnych Azure **nieobsługiwane**. — Wiele identyfikatorów SID klastrowania w tym artykule opisano instalację wielu wystąpień SAP ASCS/Wywołujących o różnych identyfikatorach SID w jednym klastrze program Pacemaker.

### <a name="ascs"></a>(A)SCS

* Konfiguracja frontonu
  * Adres IP 192.168.14.9
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
  * Adres IP 192.168.14.10
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

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury usługi Azure Files NetApp 

Oprogramowanie SAP NetWeaver wymaga magazynu udostępnionego dla katalogu transportu i profilu.  Przed kontynuowaniem instalacji dla infrastruktury plików NetApp platformy Azure, zapoznaj się z [dokumentacji usługi Azure Files NetApp][anf-azure-doc]. Sprawdź, jeśli wybranego regionu świadczenia usługi Azure oferuje usługi Azure Files NetApp. Kliknięcie następującego łącza przedstawia dostępność usługi Azure Files NetApp uporządkowane według regionów platformy Azure: [Azure NetApp pliki dostępności według regionów platformy Azure][anf-avail-matrix].

Usługa Azure files NetApp jest dostępna w kilku [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Przed wdrożeniem usługi Azure Files NetApp, żądanie dołączania do usługi Azure Files NetApp, zgodnie z [zarejestrować instrukcje plików Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów usługi Azure Files NetApp  

W krokach założono, że został już wdrożony [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Zasoby usługi Azure Files NetApp i maszyn wirtualnych, w której zostanie zainstalowany zasoby usługi Azure Files NetApp musi zostać wdrożony w tej samej sieci wirtualnej platformy Azure lub w równorzędnej sieci wirtualnych platformy Azure.  

1. Jeśli nie zostało wykonane, żądanie [dołączania do usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Tworzenie konta NetApp w wybranym regionie platformy Azure, zgodnie z [instrukcje dotyczące tworzenia konta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Konfigurowanie usługi Azure Files NetApp pojemność puli zgodnie z [instrukcje dotyczące sposobu konfigurowania usługi Azure Files NetApp pojemność puli](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura środowiska SAP Netweaver, przedstawione w tym artykule używa pojedynczej puli pojemność usługi Azure Files NetApp, jednostki SKU Premium. Jednostki SKU usługi Azure NetApp pliki Premium firma Microsoft zaleca w przypadku obciążeń aplikacji SAP Netweaver na platformie Azure.  
4. Delegowanie podsieci do plików NetApp platformy Azure, zgodnie z opisem w [instrukcje delegowania podsieci do usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Wdrażanie usługi Azure Files NetApp woluminów, zgodnie z [instrukcjami, aby utworzyć wolumin dla usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Wdrażanie woluminy w wyznaczonym usługi Azure Files NetApp [podsieci](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Należy pamiętać o tym, które zasoby usługi Azure Files NetApp i maszyn wirtualnych platformy Azure musi być w tej samej sieci wirtualnej platformy Azure lub w równorzędnej sieci wirtualnych platformy Azure. W tym przykładzie używamy dwa woluminy usługi Azure Files NetApp: oprogramowanie sap<b>QAS</b> i transSAP. Ścieżki do plików, które są instalowane do odpowiednich punktów instalacji są /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys itp.  

   1. wolumin sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. wolumin sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. wolumin sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. wolumin sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>wywołujących)
   5. wolumin transSAP (nfs://192.168.24.4/transSAP)
   6. wolumin sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. wolumin sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
W tym przykładzie użyliśmy usługi Azure Files NetApp dla wszystkich systemów plików oprogramowania SAP Netweaver aby zademonstrować, jak używać usługi Azure Files NetApp. Można także wdrożyć systemów plików SAP, które nie muszą być zainstalowane za pomocą systemu plików NFS jako [usługa Azure disk storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . W tym przykładzie <b>-e</b> muszą znajdować się w usłudze Azure Files NetApp i <b>f k</b> (czyli/usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS </b>/D<b>03</b>) może być wdrożony jako magazynu danych na dysku platformy Azure. 

### <a name="important-considerations"></a>Istotne zagadnienia

Rozważając usługi Azure Files NetApp środowiska SAP NetWeaver w architekturze SUSE wysokiej dostępności, należy pamiętać o następujących ważnych kwestiach:

- Pula minimalnej pojemności to 4 TiB. Rozmiar pojemności w puli musi być wielokrotności 4 TiB.
- Minimalna wielkość jest 100 GiB
- Usługa Azure Files NetApp i wszystkich maszyn wirtualnych, w której zostanie zainstalowany woluminy usługi Azure Files NetApp, muszą być w tej samej sieci wirtualnej platformy Azure lub w [nawiązać komunikacji równorzędnej między sieciami wirtualnymi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie. Dostępu plików NetApp na platformie Azure za pośrednictwem wirtualnej sieci równorzędnej w tym samym regionie jest teraz obsługiwane. Dostępu NetApp na platformie Azure za pośrednictwem globalnej komunikacji równorzędnej nie jest jeszcze obsługiwana.
- Wybranej sieci wirtualnej musi mieć podsieć delegować domenę do usługi Azure Files NetApp.
- Usługa Azure Files NetApp aktualnie obsługuje tylko NFSv3 
- Usługa Azure Files NetApp oferuje [Eksportuj zasady](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): dozwolone klientów, można kontrolować typ dostępu (Odczyt i zapis, tylko do odczytu itp.). 
- Funkcja Azure plików NetApp nie jest strefa pamiętać jeszcze. Obecnie funkcji usługi Azure Files NetApp nie jest wdrożona w wszystkich stref dostępności w regionie platformy Azure. Należy pamiętać o potencjalny wpływ opóźnienia w niektórych regionach platformy Azure. 

## <a name="setting-up-ascs"></a>Konfigurowanie () SCS

W tym przykładzie zasoby zostały wdrożone ręcznie za pośrednictwem [witryny Azure portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pomocą witryny Azure portal

Najpierw musisz utworzyć woluminy plików NetApp platformy Azure. Wdrażanie maszyn wirtualnych. Następnie utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli zaplecza.

1. Utwórz moduł równoważenia obciążenia (wewnętrzny)  
   1. Tworzenie adresów IP frontonu
      1. Adres IP 192.168.14.9 ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu. QAS. ASCS**)
         1. Ustawić przypisania jako statyczny i podaj adres IP (na przykład **192.168.14.9**)
         1. Kliknij przycisk OK
      1. Adres IP 192.168.14.10 Wywołujących ASCS
         * Powtórz powyższe kroki w obszarze "a" do utworzenia adresu IP dla Wywołujących (na przykład **192.168.14.10** i **frontonu. QAS. Wywołujących**)
   1. Utwórz pule zaplecza
      1. Tworzenie puli zaplecza dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz opcję pule zaplecza, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **wewnętrznej bazy danych. QAS**)
         1. Kliknij przycisk Dodaj maszynę wirtualną.
         1. Wybierz zestaw dostępności została wcześniej utworzona dla ASCS 
         1. Wybierz maszyny wirtualne (a) SCS klastra
         1. Kliknij przycisk OK
   1. Utworzenie sond kondycji.
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowego sondy kondycji (na przykład **kondycji. QAS. ASCS**)
         1. Wybierz opcję TCP jako protokół, port 620**00**, Zachowaj 5 interwał i próg złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**01** dla ASCS Wywołujących
            * Powtórz powyższe kroki w obszarze "c", aby utworzyć sondę kondycji dla Wywołujących (na przykład 621**01** i **kondycji. QAS. Wywołujących**)
   1. Reguły równoważenia obciążenia
      1. 32**00** protokołu TCP na potrzeby ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **modułu równoważenia obciążenia. QAS. ASCS.3200**)
         1. Wybierz adres IP frontonu dla ASCS, pula zaplecza i sondy kondycji utworzonego wcześniej (na przykład **frontonu. QAS. ASCS**)
         1. Zachowaj protokołu **TCP**, wprowadź numer portu **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Pamiętaj włączyć pływającego adresu IP**
         1. Kliknij przycisk OK
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 i protokołu TCP na potrzeby ASCS
      1. Dodatkowe porty dla Wywołujących ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 i TCP Aby uzyskać Wywołujących ASCS


> [!IMPORTANT]
> Nie należy włączać czasowe TCP na maszynach wirtualnych Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji nie powiedzie się. Ustaw parametr **net.ipv4.tcp_timestamps** do **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) do utworzenia podstawowego program Pacemaker klastra dla tego serwera () SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

    ```
    sudo vi /etc/hosts
    ```

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  SAP Tworzenie katalogów w woluminie plików NetApp platformy Azure.  
   Tymczasowo instalacji woluminu plików NetApp platformy Azure na jednej z maszyn wirtualnych i tworzyć katalogi SAP (ścieżki plików).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   "sudo" mkdir -p/sapmnt/QAS "sudo" mkdir -p /usr/sap/trans "sudo" mkdir -p /usr/sap/QAS/SYS "sudo" mkdir -p /usr/sap/QAS/ASCS00 "sudo" mkdir -p /usr/sap/QAS/ERS01
   
   chattr "sudo" + i/sapmnt/QAS "sudo" chattr + i chattr "sudo" /usr/sap/trans + i chattr "sudo" /usr/sap/QAS/SYS + i chattr "sudo" /usr/sap/QAS/ASCS00 + i /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   "sudo" yum -y instalacji systemu plików nfs utils agentów zasobów zasobów agentów — sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   "sudo" yum informacji zasobów agentów — sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Załadowano wtyczek: langpacks, identyfikator produktu, wyszukiwanie — wyłączone — repozytoria
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata jest ponad 2 tygodnie. Zainstaluj narzędzia yum cron? Lub uruchomić: szybkie makecache yum
   # <a name="installed-packages"></a>Zainstalowane pakiety
   # <a name="name---------resource-agents-sap"></a>Nazwa: w zasobów sap agentów
   # <a name="arch---------x8664"></a>Architektura: x86_64
   # <a name="version------395"></a>Wersja: 3.9.5
   # <a name="release------124el7"></a>Wersja: 124.el7
   # <a name="size---------100-k"></a>Rozmiar: 100 k
   # <a name="repo---------installed"></a>Repozytorium: zainstalowana
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>Z repozytorium: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Podsumowanie: Agentów zasobu klastra SAP i skrypt łącznika
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>ADRES URL: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Licencja: GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Opis: Agenci zasobów SAP i interfejs skrypt łącznika z
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Program pacemaker, aby umożliwić wystąpieniami platformy SAP mają być zarządzane w klastrze
   #          <a name="-environment"></a>: środowisko.
   ```


1. **[A]** Add mount entries

   ```
   "sudo" vi/etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Dodaj następujące wiersze do fstab, Zapisz i zamknij
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS systemu plików nfs rw trudna, rsize 65536, wsize = = 65536, vers = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS systemu plików nfs rw trudna, rsize 65536, wsize = = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans systemu plików nfs rw trudna, rsize = 65536, wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Ustaw właściwość ResourceDisk.EnableSwap y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Utwórz i użyć pliku wymiany dysku zasobów.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Ustawianie rozmiaru pliku wymiany z właściwością ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Ilość wolnego miejsca na dysku zasobów jest zależna od rozmiaru maszyny wirtualnej. Upewnij się, że należy ustawiać wartości, które są zbyt duże. Możesz sprawdzić obszaru wymiany przy użyciu polecenia swapon
   # <a name="size-of-the-swapfile"></a>Rozmiar pliku wymiany.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   ponowne uruchomienie waagent usługi "sudo"
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   anftstsapcl2 wstrzymania węzła komputerów "sudo"
   
   tworzenia zasobów komputerów "sudo" fs_QAS_ASCS device='192.168.24.5:/sapQAS/usrsapQASascs system plików \
     Katalog = "/ usr/sap/QAS/ASCS00" fstype = "systemu plików nfs" \
     --grupie g QAS_ASCS
   
   vip_QAS_ASCS IPaddr2 tworzenia zasobów komputerów "sudo" \
     IP = 192.168.14.9 cidr_netmask = 24 \
     --grupie g QAS_ASCS
   
   zasobów komputerów "sudo" Utwórz nc_QAS_ASCS port równoważenia obciążenia azure = 62000 \
     --grupie g QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   Stan komputerów "sudo"
   
   # <a name="node-anftstsapcl2-standby"></a>Węzeł anftstsapcl2: rezerwy dynamicznej
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Pełną listę zasobów:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure (stonith:fence_azure_arm):      Rozpoczęto anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>Grupa zasobów: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Rozpoczęto anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Rozpoczęto anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Zezwalaj na dostęp do SWPM. Ta zasada nie jest trwały. Jeśli ponowne uruchomienie komputera, musisz ponownie uruchom polecenie.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   "sudo" <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin SAPINST_USE_HOSTNAME = < virtual_hostname >
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   qasadm zmienić właściciela "sudo" /usr/sap/QAS/ASCS00 /usr/sap/QAS/ASCS00 sapsys chgrp "sudo"
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   "sudo" komputerów węzła unstandby anftstsapcl2 "sudo" komputerów węzła wstrzymania anftstsapcl1
   
   tworzenia zasobów komputerów "sudo" fs_QAS_AERS device='192.168.24.5:/sapQAS/usrsapQASers system plików \
     Katalog = "/ usr/sap/QAS/ERS01" fstype = "systemu plików nfs" \
    --grupie g QAS_AERS

   vip_QAS_AERS IPaddr2 tworzenia zasobów komputerów "sudo" \
     IP = 192.168.14.10 cidr_netmask = 24 \
    --grupie g QAS_AERS
   
   zasobów komputerów "sudo" Utwórz nc_QAS_AERS port równoważenia obciążenia azure = 62101 \
    --grupie g QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   Stan komputerów "sudo"
   
   # <a name="node-anftstsapcl1-standby"></a>Węzeł anftstsapcl1: rezerwy dynamicznej
   # <a name="online--anftstsapcl2-"></a>Online: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>Pełną listę zasobów:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure (stonith:fence_azure_arm):      Rozpoczęto anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>Grupa zasobów: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Rozpoczęto anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Pracę anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Rozpoczęto anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>Grupa zasobów: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Rozpoczęto anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Rozpoczęto anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Zezwalaj na dostęp do SWPM. Ta zasada nie jest trwały. Jeśli ponowne uruchomienie komputera, musisz ponownie uruchom polecenie.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   "sudo" <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin SAPINST_USE_HOSTNAME = < virtual_hostname >
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   qaadm zmienić właściciela "sudo" /usr/sap/QAS/ERS01 /usr/sap/QAS/ERS01 sapsys chgrp "sudo"
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh vi "sudo"
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Zmień polecenia ponownego uruchomienia polecenia start
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = lokalnego pf=$(_PF) $(_EN)
   Start_Program_01 = lokalnego pf=$(_PF) $(_EN)
   
   # <a name="add-the-keep-alive-parameter"></a>Dodaj parametr zachować aktywności
   umieścić/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   /sapmnt/QAS/profile/QAS_ERS01_anftstsapers vi "sudo"
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Zmień polecenia ponownego uruchomienia polecenia start
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = lokalnego pf=$(_PFL) $(_ER) NR=$(SCSID)
   Start_Program_00 = lokalnego pf=$(_PFL) $(_ER) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>Usuń Autostart z profilu Wywołujących
   # <a name="autostart--1"></a>Autostart = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Zmień konfigurację systemu Linux
   net.ipv4.tcp_keepalive_time=120 sysctl "sudo"
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>W węźle z zainstalowanym ASCS komentarz następujący wiersz
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ASCS00/exe: $LD_LIBRARY_PATH; Eksportuj LD_LIBRARY_PATH; /usr/SAP/QAS/ASCS00/exe/sapstartsrv pf = / usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh - D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>W węźle, w którym zostało zainstalowane Wywołujących przekształcić w komentarz następujący wiersz
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/exe: $LD_LIBRARY_PATH; Eksportuj LD_LIBRARY_PATH; /usr/SAP/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers - D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   właściwości komputerów z systemem "sudo" Ustaw tryb konserwacji = true
   
    rsc_sap_QAS_ASCS00 SAPInstance tworzenia zasobów komputerów "sudo" \
    InstanceName = QAS_ASCS00_anftstsapvh START_PROFILE = "/ sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    zasób meta-lepkości = 5000 migracji — próg = 1 \
    --grupie g QAS_ASCS
   
    rsc_sap_QAS_ERS01 SAPInstance tworzenia zasobów komputerów "sudo" \
    InstanceName = QAS_ERS01_anftstsapers START_PROFILE = "/ sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --grupie g QAS_AERS
      
    wspólnej ograniczenie komputerów "sudo" Dodaj g-QAS_AERS g-QAS_ASCS-5000 "sudo" komputerów ograniczenie lokalizacji rsc_sap_QAS_ASCS00 reguły wynik = 2000 runs_ers_QAS eq 1 "sudo" komputerów ograniczenia zlecenia g QAS_ASCS, a następnie g QAS_AERS rodzaj = opcjonalne symetryczne = false
    
    "sudo" komputerów węzła unstandby anftstsapcl1 "sudo" komputerów właściwością trybu konserwacji = false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    właściwości komputerów z systemem "sudo" Ustaw tryb konserwacji = true
    
    rsc_sap_QAS_ASCS00 SAPInstance tworzenia zasobów komputerów "sudo" \
    InstanceName = QAS_ASCS00_anftstsapvh START_PROFILE = "/ sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    zasób meta-lepkości = 5000 \
    --grupie g QAS_ASCS
   
    rsc_sap_QAS_ERS01 SAPInstance tworzenia zasobów komputerów "sudo" \
    InstanceName = QAS_ERS01_anftstsapers START_PROFILE = "/ sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --grupie g QAS_AERS
      
    wspólnej ograniczenie komputerów "sudo" Dodaj g-QAS_AERS z g-QAS_ASCS-5000 "sudo" komputerów ograniczenia zlecenia g QAS_ASCS, a następnie g QAS_AERS rodzajem = opcjonalne symetryczne = false
   
    "sudo" komputerów węzła unstandby anftstsapcl1 "sudo" komputerów właściwością trybu konserwacji = false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    Stan komputerów "sudo"
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Pełną listę zasobów:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure (stonith:fence_azure_arm):      Rozpoczęto anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>Grupa zasobów: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Rozpoczęto anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Rozpoczęto anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>Grupa zasobów: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Rozpoczęto anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Rozpoczęto anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Port sondy ASCS
   "sudo" zapory cmd — strefy = publicznego — Dodaj port = 62000/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 62000/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3200/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3200/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3600/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3600/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3900/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3900/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 8100/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 8100/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50013/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50013/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50014/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50014/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50016/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50016/tcp
   # <a name="probe-port-of-ers"></a>Port sondy Wywołujących
   "sudo" zapory cmd — strefy = publicznego — Dodaj port = 62101/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 62101/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3301/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 3301/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50113/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50113/tcp "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50114/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50114/tcp "sudo" Zapora cmd — strefy = publicznego — Dodaj port = 50116/tcp — stałe "sudo" zapory cmd — strefy = publicznego — Dodaj port = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   "sudo" vi/etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>Adres IP z konfiguracji frontonu modułu równoważenia obciążenia dla SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>Adres IP z konfiguracji frontonu modułu równoważenia obciążenia dla SAP NetWeaver ASCS Wywołujących
   192.168.14.10 anftstsapa01 anftstsapers 192.168.14.7 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   "sudo" mkdir -p/sapmnt/QAS "sudo" mkdir -p /usr/sap/trans

   chattr "sudo" + i/sapmnt/QAS "sudo" chattr + i /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   "sudo" yum -y zainstalować uuidd utils systemu plików nfs
   ```

1. **[A]** Add mount entries  

   ```
   "sudo" vi/etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Dodaj następujące wiersze do fstab, Zapisz i zamknij
   192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS systemu plików nfs rw trudna, rsize = 65536, wsize = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans systemu plików nfs rw trudna, rsize 65536, wsize = = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   "sudo" vi/etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Dodaj następujący wiersz do fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 systemu plików nfs rw trudna, rsize = 65536, wsize = 65536, vers = 3
   
   # <a name="mount"></a>Instalacja
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   "sudo" vi/etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Dodaj następujący wiersz do fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 systemu plików nfs rw trudna, rsize = 65536, wsize = 65536, vers = 3
   
   # <a name="mount"></a>Instalacja
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Ustaw właściwość ResourceDisk.EnableSwap y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Utwórz i użyć pliku wymiany dysku zasobów.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Ustawianie rozmiaru pliku wymiany z właściwością ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Ilość wolnego miejsca na dysku zasobów jest zależna od rozmiaru maszyny wirtualnej. Upewnij się, że należy ustawiać wartości, które są zbyt duże. Możesz sprawdzić obszaru wymiany przy użyciu polecenia swapon
   # <a name="size-of-the-swapfile"></a>Rozmiar pliku wymiany.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   ponowne uruchomienie waagent usługi "sudo"
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   "sudo" <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   "sudo" <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER = sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore listy
   ```

   This should list all entries and should look similar to
   ```
   DANE pliku: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. Plik klucza DAT: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. KLUCZ
   
   ŚRODOWISKO DOMYŚLNE KLUCZA: UŻYTKOWNIK 192.168.14.4:30313: SAPABAP1   DATABASE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   Jednostka su — hdbuserstore qasadm SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 przenoszenia zasobów komputerów #
   
   [root@anftstsapcl1 ~] # komputerów resource wyczyścić rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Usuń zakończone niepowodzeniem akcje w przypadku Wywołujących, które wystąpiły w ramach migracji
   [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 Oczyszczanie zasobów komputerów #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl2: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl2: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~] # echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Pełną listę zasobów:
   
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   
   Akcje zakończone niepowodzeniem:
   * rsc_sap_QAS_ERS01_monitor_11000 na anftstsapcl1 nie działa (7): wywołanie = 45, stan = zakończeniu exitreason = ",
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 Oczyszczanie zasobów komputerów #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~] # pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 Oczyszczanie zasobów komputerów # [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 Oczyszczanie zasobów komputerów #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl2: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl2: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~] # pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] rsc_sap_QAS_ASCS00 Oczyszczanie zasobów komputerów # [root@anftstsapcl2 ~] rsc_sap_QAS_ERS01 Oczyszczanie zasobów komputerów #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~] # pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] rsc_sap_QAS_ERS01 Oczyszczanie zasobów komputerów #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~] # pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~] # ataku typu kill 59545-9
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      Do grupy zasobów anftstsapcl1: g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Do grupy zasobów anftstsapcl1: g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Pracę anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Rozpoczęto anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Pracę anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Rozpoczęto anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
