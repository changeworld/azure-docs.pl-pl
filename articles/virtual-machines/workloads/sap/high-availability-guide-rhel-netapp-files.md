---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla systemu SAP NW w systemie RHEL z plikami NetApp platformy Azure| Dokumenty firmy Microsoft
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
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351256"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Wysoka dostępność maszyn wirtualnych platformy Azure dla systemu SAP NetWeaver w systemie Red Hat Enterprise Linux z plikami NetApp usługi Azure dla aplikacji SAP

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

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania struktury klastra i instalowania wysoce dostępnego systemu SAP NetWeaver 7.50 przy użyciu [plików NetApp Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
W przykładowych konfiguracjach, poleceniach instalacji itp. Ascs wystąpienie jest numer 00, ers wystąpienie jest numer 01, wystąpienie aplikacji podstawowej (PAS) jest 02 i Application instance (AAS) jest 03. SAP System ID QAS jest używany. 

Warstwa bazy danych nie jest szczegółowo omówiona w tym artykule.  

Najpierw przeczytaj następujące uwagi i dokumenty SAP:

* [Dokumentacja plików netapp platformy Azure][anf-azure-doc] 
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

Wysoka dostępność(HA) dla usług centralnych SAP Netweaver wymaga współdzielonego magazynu.
Aby osiągnąć to na Red Hat Linux do tej pory konieczne było zbudowanie oddzielnego wysoce dostępnego klastra GlusterFS. 

Teraz można osiągnąć SAP Netweaver HA przy użyciu udostępnionego magazynu, wdrożony na usługi Azure NetApp Files. Korzystanie z usługi Azure NetApp Files dla udostępnionego magazynu eliminuje potrzebę dodatkowego [klastra GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Rozrusznik serca jest nadal potrzebny dla ha usług centralnych SAP Netweaver (ASCS / SCS).

![Omówienie wysokiej dostępności sap NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS i baza danych SAP HANA używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy użycie [standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia z oddzielnymi przednimi ustawieniami IP dla (A)SCS i ERS.

### <a name="ascs"></a>1lit. Scs

* Konfiguracja frontu
  * Adres IP 192.168.14.9
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 36<strong>&lt;&gt; nr</strong> TCP
  * 39<strong>&lt;&gt; nr</strong> TCP
  * 81<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Konfiguracja frontu
  * Adres IP 192.168.14.10
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Reguły równoważenia obciążenia
  * W przypadku korzystania ze standardowego modułu równoważenia obciążenia wybierz **porty wysokiej jakości**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 33<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* Konfiguracja wewnętrznej bazy danych
  * Połączone z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra (A)SCS/ERS

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury plików NetApp platformy Azure 

SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportu i profilu.  Przed przystąpieniem do konfiguracji infrastruktury plików NetApp platformy Azure zapoznaj się z [dokumentacją usługi Azure NetApp Files.][anf-azure-doc] Sprawdź, czy wybrany region platformy Azure oferuje usługi Azure NetApp Files. Poniższe łącze przedstawia dostępność plików NetApp usługi Azure według regionu platformy Azure: [Dostępność plików usługi Azure NetApp przez region Azure][anf-avail-matrix].

Pliki usługi Azure NetApp są dostępne w kilku [regionach platformy Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Przed wdrożeniem plików NetApp platformy Azure należy zażądać dołączania do plików NetApp platformy Azure, postępując zgodnie z [instrukcjami rejestrowania plików NetApp platformy Azure.][anf-register] 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów plików NetApp platformy Azure  

Kroki zakładamy, że już wdrożono [usługę Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Zasoby usługi Azure NetApp Files i maszyny wirtualne, w których zostaną zainstalowane zasoby plików NetApp platformy Azure, muszą zostać wdrożone w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.  

1. Jeśli jeszcze tego nie zrobiłeś, poproś o [dołączanie do usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Utwórz konto NetApp w wybranym regionie platformy Azure, postępując zgodnie z [instrukcjami dotyczącymi utworzenia konta NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Skonfiguruj pulę pojemności plików NetApp platformy Azure, postępując zgodnie z [instrukcjami dotyczącymi konfigurowania puli pojemności plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura SAP Netweaver przedstawiona w tym artykule używa pojedynczej puli pojemności plików NetApp azure, SKU Premium. Firma Microsoft zaleca azure NetApp Files Premium SKU dla obciążenia aplikacji SAP Netweaver na platformie Azure.  
4. Delegowanie podsieci do plików netapp platformy Azure zgodnie z opisem w [instrukcjach Delegowanie podsieci do plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Wdrażanie woluminów plików NetApp platformy Azure, postępując zgodnie z [instrukcjami, aby utworzyć wolumin dla plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Wdrażanie woluminów w wyznaczonej [podsieci](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Plików NetApp platformy Azure . Należy pamiętać, że zasoby plików NetApp platformy Azure i maszyny wirtualne platformy Azure muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. W tym przykładzie używamy dwóch woluminów usługi Azure NetApp Files: sap<b>QAS</b> i transSAP. Ścieżki plików, które są zamontowane do odpowiednich punktów instalacji są /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys itp.  

   1. objętość sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. tom sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. objętość sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. głośność sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. głośność transSAP (nfs://192.168.24.4/transSAP)
   6. objętość sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. objętość sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
W tym przykładzie użyliśmy usługi Azure NetApp Files dla wszystkich systemów plików SAP Netweaver, aby zademonstrować, jak można używać plików NetApp platformy Azure. Systemy plików SAP, które nie muszą być montowane za pośrednictwem systemu plików NFS, można również wdrożyć jako [magazyn dysków platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) W tym <b>przykładzie a-e</b> musi być na usługi Azure NetApp Files i <b>f-g</b> (czyli /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) można wdrożyć jako magazyn dysku platformy Azure. 

### <a name="important-considerations"></a>Istotne zagadnienia

Rozważając usługi Azure NetApp Files dla sap Netweaver na architekturze wysokiej dostępności SUSE, należy pamiętać o następujących ważnych zagadnień:

- Minimalna pula miejsc wynosi 4 TiB. Wielkość puli pojemności można zwiększyć w odstępach co 1 TiB.
- Minimalna objętość to 100 GiB
- Usługi Azure NetApp Files i wszystkie maszyny wirtualne, na których będą zainstalowane woluminy usługi Azure NetApp Files, muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w [sieciach wirtualnych równorzędnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie. Dostęp do plików NetApp usługi Azure za pomocą sieci wirtualnej komunikacji równorzędnej w tym samym regionie jest teraz obsługiwany. Dostęp do usługi Azure NetApp za pomocą globalnej komunikacji równorzędnej nie jest jeszcze obsługiwany.
- Wybrana sieć wirtualna musi mieć podsieć delegowaną do plików NetApp platformy Azure.
- Usługa Azure NetApp Files oferuje [zasady eksportu:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)można kontrolować dozwolonych klientów, typ dostępu (odczyt&zapisu, tylko do odczytu itp.). 
- Funkcja usługi Azure NetApp Files nie jest jeszcze świadoma strefy. Obecnie funkcja Usługi Azure NetApp Files nie jest wdrażana we wszystkich strefach dostępności w regionie platformy Azure. Należy pamiętać o potencjalnych implikacje opóźnienia w niektórych regionach platformy Azure. 
- Woluminy usługi Azure NetApp Files można wdrożyć jako woluminy NFSv3 lub NFSv4.1. Oba protokoły są obsługiwane dla warstwy aplikacji SAP (ASCS/ERS, serwery aplikacji SAP). 

## <a name="setting-up-ascs"></a>Konfigurowanie (A)SCS

W tym przykładzie zasoby zostały wdrożone ręcznie za pośrednictwem [witryny Azure portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem witryny Azure portal

Najpierw musisz utworzyć woluminy plików NetApp platformy Azure. Wdrażanie maszyn wirtualnych. Następnie należy utworzyć moduł równoważenia obciążenia i używać maszyn wirtualnych w puli wewnętrznej bazy danych.

1. Tworzenie modułu równoważenia obciążenia (wewnętrznego, standardowego):  
   1. Tworzenie adresów IP frontendu
      1. Adres IP 192.168.14.9 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli ip frontendu (na przykład **frontendu. qas. ASCS**)
         1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **192.168.14.9**)
         1. Kliknij przycisk OK
      1. Adres IP 192.168.14.10 dla ASCS ERS
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla ERS (na przykład **192.168.14.10** i **frontend. qas. ERS**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
      1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **wewnętrznej bazy danych. qas**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz pozycję Maszyna wirtualna. 
      1. Wybierz maszyny wirtualne klastra (A)SCS i ich adresy IP.
      1. Kliknij pozycję Dodaj.
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **kondycji. qas. ASCS**)
         1. Wybierz protokół TCP jako protokół, port 620**00**, zachowaj interwał 5 i próg w złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**01** dla ASCS ERS
            * Powtórz powyższe kroki w obszarze "c", aby utworzyć sondę kondycji dla ERS (na przykład 621**01** i **kondycji. qas. ERS**)
   1. Reguły równoważenia obciążenia
      1. Reguły równoważenia obciążenia dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **lb. qas. ASCS**)
         1. Wybierz adres IP frontendu dla ASCS, puli wewnętrznej bazy danych i sondy kondycji utworzonej wcześniej (na przykład **frontend. qas. ASCS**, **zaplecze. QAS** i **zdrowia. qas. ASCS**)
         1. Wybieranie **portów wysokiej haw**
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
         * Powtórz powyższe kroki, aby utworzyć reguły równoważenia obciążenia dla ERS (na przykład **lb. qas. ERS**)
1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia (wewnętrznego), wykonaj następujące kroki:  
   1. Tworzenie adresów IP frontendu
      1. Adres IP 192.168.14.9 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pulę adresów IP frontu i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli ip frontendu (na przykład **frontendu. qas. ASCS**)
         1. Ustaw przypisanie na Statyczne i wprowadź adres IP (na przykład **192.168.14.9**)
         1. Kliknij przycisk OK
      1. Adres IP 192.168.14.10 dla ASCS ERS
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla ERS (na przykład **192.168.14.10** i **frontend. qas. ERS**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pule wewnętrznej bazy danych i kliknij przycisk Dodaj
      1. Wprowadź nazwę nowej puli wewnętrznej bazy danych (na przykład **wewnętrznej bazy danych. qas**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz zestaw dostępności utworzony wcześniej dla ASCS 
      1. Wybieranie maszyn wirtualnych klastra (A)SCS
      1. Kliknij przycisk OK
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **kondycji. qas. ASCS**)
         1. Wybierz protokół TCP jako protokół, port 620**00**, zachowaj interwał 5 i próg w złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**01** dla ASCS ERS
            * Powtórz powyższe kroki w obszarze "c", aby utworzyć sondę kondycji dla ERS (na przykład 621**01** i **kondycji. qas. ERS**)
   1. Reguły równoważenia obciążenia
      1. 32**00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **lb. qas. ASCS.3200**)
         1. Wybierz adres IP frontendu dla ASCS, puli wewnętrznej bazy danych i sondy kondycji utworzonej wcześniej (na przykład **frontend. qas. ASCS**)
         1. Zachowaj protokół **TCP**, wprowadź port **3200**
         1. Wydłużenie limitu czasu bezczynnego do 30 minut
         1. **Upewnij się, że włączysz pływający adres IP**
         1. Kliknij przycisk OK
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 36**00,** 39**00,** 81**00,** 5**00**13, 5**00**14, 5**00**16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS ERS
         * Powtórz powyższe kroki w obszarze "d" dla portów 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 i TCP dla ASCS ERS

      > [!Note]
      > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

      > [!IMPORTANT]
      > Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Wyłącz mapowanie identyfikatorów (w przypadku korzystania z systemu NFSv4.1)

Instrukcje w tej sekcji mają zastosowanie tylko wtedy, gdy przy użyciu woluminów usługi Azure NetApp Files z protokołem NFSv4.1. Wykonaj konfigurację na wszystkich maszynach wirtualnych, na których zostaną zainstalowane woluminy NFSv4.1 plików Azure NetApp.  

1. Sprawdź ustawienie domeny nfs. Upewnij się, że domena jest skonfigurowana jako domyślna domena plików NetApp, **`defaultv4iddomain.com`** a mapowanie jest ustawione na **nikogo.**  

    > [!IMPORTANT]
    > Upewnij się, że domena `/etc/idmapd.conf` systemu plików NFS jest zgodna z domyślną **`defaultv4iddomain.com`** konfiguracją domeny w plikach NetApp usługi Azure: . Jeśli występuje niezgodność między konfiguracją domeny na kliencie systemu plików NFS (tj. maszyną wirtualną) a serwerem NFS, czyli konfiguracją usługi Azure NetApp, uprawnienia dla plików na `nobody`woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Sprawdź . Należy ustawić na **Y**. Aby utworzyć strukturę `nfs4_disable_idmapping` katalogu, w której się znajduje, wykonaj polecenie mount. Nie będzie można ręcznie utworzyć katalogu w obszarze /sys/modules, ponieważ dostęp jest zarezerwowany dla jądra / sterowników.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Aby uzyskać więcej informacji `nfs4_disable_idmapping` na https://access.redhat.com/solutions/1749883temat zmiany parametru, zobacz .

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

Wykonaj kroki opisane w [sekcji Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure,](high-availability-guide-rhel-pacemaker.md) aby utworzyć podstawowy klaster rozrusznika serca dla tego (A)serwera SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji SAP NetWeaver

Następujące elementy są poprzedzone **[A]** - ma zastosowanie do wszystkich węzłów, **[1]** - dotyczy tylko węzła 1 lub **[2]** - dotyczy tylko węzła 2.

1. **[A]** Rozpoznawanie nazw hostów instalatora

   Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach

    ```
    sudo vi /etc/hosts
    ```

   Wstaw następujące wiersze do /etc/hosts. Zmienianie adresu IP i nazwy hosta w celu dopasowania go do środowiska

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

1. **[1]** Tworzenie katalogów SAP w woluminie Plików NetApp platformy Azure.  
   Tymczasowo zainstaluj wolumin Plików NetApp platformy Azure na jednej z maszyn wirtualnych i utwórz katalogi SAP (ścieżki plików).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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
    ``` 

1. **[A]** Tworzenie katalogów udostępnionych

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Instalowanie klienta systemu plików NFS i innych wymagań

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Sprawdź wersję resource-agents-sap

   Upewnij się, że wersja zainstalowanego pakietu resource-agents-sap jest co najmniej 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Dodawanie wpisów instalacji

   W przypadku korzystania z systemu NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   W przypadku korzystania z systemu NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Upewnij się, że podczas montażu woluminów jest zgodna z wersją protokołu NFS woluminów plików NetApp. Jeśli woluminy usługi Azure NetApp Files są tworzone jako woluminy NFSv3, użyj odpowiedniej konfiguracji NFSv3. Jeśli woluminy usługi Azure NetApp Files są tworzone jako woluminy NFSv4.1, postępuj zgodnie z instrukcjami, aby wyłączyć mapowanie identyfikatorów i upewnij się, że używa odpowiedniej konfiguracji NFSv4.1. W tym przykładzie woluminy usługi Azure NetApp Files zostały utworzone jako woluminy NFSv3.  

   Zamontuj nowe akcje

   ```
   sudo mount -a  
   ```

1. **[A]** Konfigurowanie pliku SWAP

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Uruchom ponownie agenta, aby aktywować zmianę

   ```
   sudo service waagent restart
   ```

1. **Konfiguracja [A]** RHEL

   Konfigurowanie RHEL zgodnie z opisem w SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalacja SAP NetWeaver ASCS/ERS

1. **[1]** Tworzenie wirtualnego zasobu IP i sondy kondycji dla wystąpienia ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Zainstaluj SAP NetWeaver ASCS  

   Zainstaluj SAP NetWeaver ASCS jako root w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ASCS, na przykład <b>anftstsapvh</b>, <b>192.168.14.9</b> i numer wystąpienia użytego do sondy modułu równoważenia obciążenia, na przykład <b>00</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**QAS**/ASCS**00**, spróbuj skonfigurować właściciela i grupę folderu ASCS**00** i ponowić próbę.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Tworzenie wirtualnego zasobu IP i sondy kondycji dla wystąpienia ERS

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Zainstaluj SAP NetWeaver ERS  

   Zainstaluj SAP NetWeaver ERS jako root w drugim węźle przy użyciu wirtualnej nazwy hosta, która mapuje adres IP konfiguracji frontendu modułu równoważenia obciążenia dla ERS, na przykład <b>anftstsapers</b>, <b>192.168.14.10</b> i numer wystąpienia, który został użyty do sondy modułu równoważenia obciążenia, na przykład <b>01</b>.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Jeśli instalacja nie może utworzyć podfolderu w /usr/sap/**QAS**/ERS**01**, spróbuj skonfigurować właściciela i grupę folderu ERS**01** i ponowić próbę.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Dostosowywanie profili wystąpień ASCS/SCS i ERS

   * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * Profil ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Konfigurowanie keep alive

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest routowana za pośrednictwem modułu równoważenia obciążenia oprogramowania. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po konfigurowalnym przesunieniu czasu. Aby temu zapobiec, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienić ustawienia systemu Linux. Więcej informacji można znaleźć [w notatce SAP 1410736.][1410736]

   Parametr profilu ASCS/SCS enque/encni/set_so_keepalive został już dodany w ostatnim kroku.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Aktualizacja pliku /usr/sap/sapservices

   Aby zapobiec rozpoczęciu wystąpień przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane przez program Pacemaker muszą być komentowane z pliku /usr/sap/sapservices. Nie komentować wystąpienia SAP HANA, jeśli będzie on używany z HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Tworzenie zasobów klastra SAP

   W przypadku korzystania z architektury serwera 1 (ENSA1) należy zdefiniować zasoby w następujący sposób:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Firma SAP wprowadziła obsługę serwera 2, w tym replikacji, od momentu wprowadzenia sap NW 7.52. Począwszy od platformy ABAP 1809, serwer 2 jest instalowany domyślnie. Zobacz SAP note [2630416,](https://launchpad.support.sap.com/#/notes/2630416) aby uzyskać pomoc techniczną serwera 2.
   Jeśli używasz architektury serwera enqueue server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zainstaluj agenta zasobów resource-agents-sap-4.1.1-12.el7.x86_64 lub nowszą i zdefiniuj zasoby w następujący sposób:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Jeśli uaktualniasz ze starszej wersji i przełączasz się na serwer 2 w kolejce, zobacz UWAGA SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Limity czasu w powyższej konfiguracji są tylko przykłady i może być konieczne dostosowanie do określonej konfiguracji SAP. 

   Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Dodaj reguły zapory dla ASCS i ERS w obu węzłach Dodaj reguły zapory dla ASCS i ERS w obu węzłach.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera aplikacji SAP NetWeaver

   Niektóre bazy danych wymagają, aby instalacja wystąpienia bazy danych była wykonywana na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby móc ich używać w takich przypadkach.  

   Kroki poniżej założono, że zainstalować serwer aplikacji na serwerze innym niż ASCS/SCS i HANA serwerów. W przeciwnym razie niektóre z poniższych kroków (takich jak konfigurowanie rozpoznawania nazw hostów) nie są potrzebne.  

   Następujące elementy są poprzedzone **[A]** - ma zastosowanie zarówno do PAS, jak i AAS, **[P]** - mają zastosowanie tylko do PAS lub **[S]** - mają zastosowanie tylko do AAS.  

1. **[A]** Rozpoznawanie nazw hostów instalatora Można użyć serwera DNS lub zmodyfikować /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach:  

   ```
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do /etc/hosts. Zmień adres IP i nazwa hosta, aby dopasować go do środowiska.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Utwórz katalog sapmnt Utwórz katalog sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Instalowanie klienta systemu plików NFS i innych wymagań  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Dodawanie wpisów instalacji  
   W przypadku korzystania z systemu NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   W przypadku korzystania z systemu NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Zamontuj nowe akcje

   ```
   sudo mount -a
   ```

1. **[P]** Tworzenie i instalowanie katalogu PAS  
   W przypadku korzystania z systemu NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   W przypadku korzystania z systemu NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Tworzenie i instalowanie katalogu AAS  
   W przypadku korzystania z systemu NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   W przypadku korzystania z systemu NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Konfigurowanie pliku SWAP
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Uruchom ponownie agenta, aby aktywować zmianę

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie SAP NetWeaver jest zainstalowany na SAP HANA. Do tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania systemu SAP HANA na platformie Azure, zobacz [Wysoka dostępność sap HANA na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako katalog główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontendu modułu równoważenia obciążenia bazy danych.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotowanie serwera aplikacji

   Wykonaj kroki opisane w rozdziale [Przygotowanie serwera aplikacji SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

1. Instalowanie serwera aplikacji SAP NetWeaver

   Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   Parametr sapinst można użyć SAPINST_REMOTE_ACCESS_USER, aby umożliwić użytkownikowi niebędącemu rootem łączenie się z sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Aktualizacja bezpiecznego magazynu SAP HANA

   Zaktualizuj bezpieczny magazyn SAP HANA, aby wskazywał wirtualną nazwę konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów jako \<sapsid>adm

   ```
   hdbuserstore List
   ```

   Powinno to wymieniać wszystkie wpisy i wyglądać podobnie do
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Dane wyjściowe pokazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis musi zostać zmieniony, aby wskazać wirtualną nazwę hosta modułu równoważenia obciążenia. Upewnij się, że używasz tego samego portu **(30313** w powyższym wyjściu) i nazwy bazy danych **(QAS** w powyższym wyjściu)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

1. Ręczna migracja wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Uruchom następujące polecenia jako katalog główny, aby przeprowadzić migrację wystąpienia ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Uruchom następujące polecenie jako katalog główny w węźle, w którym działa wystąpienie ASCS

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Użyj następującego polecenia, aby wyczyścić zasoby, których nie powiodło się.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Proces serwera wiadomości zabicia

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Uruchom następujące polecenia jako katalog główny, aby zidentyfikować proces serwera wiadomości i zabić go.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Jeśli serwer wiadomości zostanie zabity tylko raz, `sapstart`zostanie on ponownie uruchomiony przez program . Jeśli zabijesz go wystarczająco często, rozrusznik serca ostatecznie przeniesie wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ASCS i ERS po teście.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Zabij proces serwera w kolejce

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Uruchom następujące polecenia jako katalog główny w węźle, w którym jest uruchomione wystąpienie ASCS w celu zabicia serwera w kolejce.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   Wystąpienie ASCS należy natychmiast awaryjnie do innego węzła. Wystąpienie ERS należy również awaryjnie po uruchomieniu wystąpienia ASCS. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ASCS i ERS po teście.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Zabij proces serwera replikacji w kolejce

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Uruchom następujące polecenie jako katalog główny w węźle, w którym jest uruchomione wystąpienie ERS, aby zabić proces serwera replikacji w kolejce.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Jeśli polecenie zostanie uruchomione `sapstart` tylko raz, uruchomi się ponownie proces. Jeśli uruchomisz go `sapstart` wystarczająco często, nie uruchomi ponownie procesu i zasób będzie w stanie zatrzymania. Uruchom następujące polecenia jako katalog główny, aby oczyścić stan zasobu wystąpienia ERS po teście.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Zabij proces sapstartsrv w kolejce

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Uruchom następujące polecenia jako katalog główny w węźle, w którym jest uruchomiony system ASCS.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Proces sapstartsrv powinny być zawsze ponownie uruchamiane przez agenta zasobów rozrusznika jako część monitorowania. Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Następne kroki

* [Ha dla SAP NW na maszynach wirtualnych platformy Azure na RHEL dla aplikacji SAP — przewodnik po wielu identyfikatorach SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i zaplanować odzyskiwanie po awarii sap HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure.](hana-overview-high-availability-disaster-recovery.md)
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na maszynach wirtualnych platformy Azure, zobacz [Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)][sap-hana-ha]
