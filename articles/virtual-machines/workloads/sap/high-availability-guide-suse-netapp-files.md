---
title: Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp | Dokumentacja firmy Microsoft
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: b3b5a89b43eaa5c0851962aef414ec9c9b7440da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357730"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server za pomocą usługi Azure Files NetApp dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i instalacji o wysokiej dostępności systemu SAP NetWeaver 7.50, za pomocą [usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
W przykładowych konfiguracji, polecenia instalacji itp., wystąpienie ASCS jest liczba 00, liczby wystąpień Wywołujących 01, wystąpienie aplikacji głównej (PAS) jest 02 i wystąpienia aplikacji (AAS) to 03. QAS identyfikator systemu SAP jest używany. 

W tym artykule wyjaśniono, jak osiągnąć wysoką dostępność dla aplikacji SAP NetWeaver za pomocą usługi Azure NetApp Files. Warstwa bazy danych nie jest omówiona szczegółowo w tym artykule.

Najpierw przeczytaj następujące uwagi SAP i dokumenty:

* [Dokumentacja usługi Azure plików NetApp][anf-azure-doc] 
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
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP w systemie Linux][dbms-guide]
* [SUSE SAP HA przewodniki z najlepszymi rozwiązaniami] [ suse-ha-guide] przewodniki zawierają wszystkie wymagane informacje, aby skonfigurować Netweaver wysokiej dostępności i replikacji systemu SAP HANA w środowisku lokalnym. Użyj tych przewodników jako głównej linii bazowej. Zapewniają one znacznie bardziej szczegółowe informacje.
* [Rozszerzenie o wysokiej dostępności SUSE 12 z dodatkiem SP3 — informacje o wersji][suse-ha-12sp3-relnotes]
* [Aplikacje środowiska SAP NetApp na Microsoft Azure przy użyciu usługi Azure Files NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Wysoka availability(HA) usługi centralne SAP Netweaver wymaga magazynu udostępnionego.
Aby to osiągnąć w systemie SUSE Linux do tej pory było konieczne tworzenie oddzielnych klastra systemu plików NFS o wysokiej dostępności. 

Teraz jest możliwe uzyskanie SAP Netweaver wysokiej dostępności przy użyciu magazynu udostępnionego, wdrożone w usłudze Azure Files NetApp. Za pomocą usługi Azure Files NetApp dla magazynu udostępnionego eliminuje potrzebę stosowania dodatkowych [klastra systemu plików NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Program pacemaker nadal jest wymagany dla HA services(ASCS/SCS) centralne SAP Netweaver.


![Oprogramowanie SAP NetWeaver wysokiej dostępności Przegląd](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Wywołujących i bazy danych SAP HANA, użyj nazwy hosta wirtualnego i wirtualnych adresów IP. Na platformie Azure [moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) jest wymagana do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację (A) SCS i Wywołujących modułu równoważenia obciążenia.

> [!IMPORTANT]
> — Wiele identyfikatorów SID klastrowania SAP ASCS/Wywołujących z systemem SUSE Linux jako system operacyjny gościa na maszynach wirtualnych Azure **nieobsługiwane**. — Wiele identyfikatorów SID klastrowania w tym artykule opisano instalację wielu wystąpień SAP ASCS/Wywołujących o różnych identyfikatorach SID w jednym klastrze program Pacemaker


### <a name="ascs"></a>(A)SCS

* Konfiguracja frontonu
  * Adres IP 10.1.1.20
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
  * Adres IP 10.1.1.21
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

5. Wdrażanie usługi Azure Files NetApp woluminów, zgodnie z [instrukcjami, aby utworzyć wolumin dla usługi Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Wdrażanie woluminy w wyznaczonym usługi Azure Files NetApp [podsieci](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Należy pamiętać o tym, które zasoby usługi Azure Files NetApp i maszyn wirtualnych platformy Azure musi być w tej samej sieci wirtualnej platformy Azure lub w równorzędnej sieci wirtualnych platformy Azure. Na przykład sapmnt<b>QAS</b>, usrsap<b>QAS</b>itd są nazwy woluminów i sapmnt<b>qas</b>, usrsap<b>qas</b>itd są filepaths dla platformy Azure Woluminy plików NetApp.  

   1. wolumin sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. wolumin usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. wolumin usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. wolumin usrsap<b>QAS</b>wywołujących (nfs://10.1.0.4/usrsap<b>qas</b>wywołujących)
   5. wolumin trans (nfs://10.1.0.4/trans)
   6. wolumin usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>pas)
   7. wolumin usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
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

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Ręczne wdrażanie maszyn wirtualnych systemu Linux za pomocą witryny Azure portal

Najpierw musisz utworzyć woluminy plików NetApp platformy Azure. Wdrażanie maszyn wirtualnych. Następnie utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli zaplecza.

1. Tworzenie grupy zasobów
1. Create a Virtual Network
1. Tworzenie zestawu dostępności dla ASCS  
   Domena aktualizacji Maksymalny zestaw
1. Tworzenie maszyny wirtualnej 1  
   Użyj co najmniej SLES4SAP 12 z dodatkiem SP3, w tym przykładzie jest używany obraz SLES4SAP 12 z dodatkiem SP3  
   Wybierz utworzony wcześniej dla ASCS zestawu dostępności  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej SLES4SAP 12 z dodatkiem SP3, w tym przykładzie jest używany obraz SLES4SAP 12 z dodatkiem SP3  
   Wybierz utworzony wcześniej dla ASCS zestawu dostępności  
1. Utwórz zestaw dostępności dla wystąpienia aplikacji SAP (PAS, AAS)    
   Domena aktualizacji Maksymalny zestaw
1. Utwórz maszynę wirtualną z 3  
   Użyj co najmniej SLES4SAP 12 z dodatkiem SP3, w tym przykładzie jest używany obraz SLES4SAP 12 z dodatkiem SP3  
   Wybierz utworzony wcześniej dla PAS/AAS zestawu dostępności   
1. Utwórz maszynę wirtualną z 4  
   Użyj co najmniej SLES4SAP 12 z dodatkiem SP3, w tym przykładzie jest używany obraz SLES4SAP 12 z dodatkiem SP3  
   Wybierz utworzony wcześniej dla PAS/AAS zestawu dostępności  

## <a name="setting-up-ascs"></a>Konfigurowanie () SCS

W tym przykładzie zasoby zostały wdrożone ręcznie za pośrednictwem [witryny Azure portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Ręczne wdrażanie modułu równoważenia obciążenia platformy Azure za pośrednictwem witryny Azure portal

Najpierw musisz utworzyć woluminy plików NetApp platformy Azure. Wdrażanie maszyn wirtualnych. Następnie utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli zaplecza.

1. Utwórz moduł równoważenia obciążenia (wewnętrzny)  
   1. Tworzenie adresów IP frontonu
      1. Adres IP 10.1.1.20 ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu. QAS. ASCS**)
         1. Ustawić przypisania jako statyczny i podaj adres IP (na przykład **10.1.1.20**)
         1. Kliknij przycisk OK
      1. Adres IP 10.1.1.21 Wywołujących ASCS
         * Powtórz powyższe kroki w obszarze "a" do utworzenia adresu IP dla Wywołujących (na przykład **10.1.1.21** i **frontonu. QAS. Wywołujących**)
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
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **modułu równoważenia obciążenia. QAS. ASCS.3200**)
         1. Wybierz adres IP frontonu dla ASCS, pula zaplecza i sondy kondycji utworzonego wcześniej (na przykład **frontonu. QAS. ASCS**)
         1. Zachowaj protokołu **TCP**, wprowadź numer portu **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Pamiętaj włączyć pływającego adresu IP**
         1. Kliknij przycisk OK
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 i protokołu TCP na potrzeby ASCS
      1. Dodatkowe porty dla Wywołujących ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 33**01**, 5**01**13, 5**01**14, 5**01**16 i protokołu TCP na potrzeby Wywołujących ASCS

> [!IMPORTANT]
> Nie należy włączać czasowe TCP na maszynach wirtualnych Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji nie powiedzie się. Ustaw parametr **net.ipv4.tcp_timestamps** do **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra program Pacemaker

Postępuj zgodnie z instrukcjami w [konfigurowania program Pacemaker w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) do utworzenia podstawowego program Pacemaker klastra dla tego serwera () SCS.

### <a name="installation"></a>Instalacja

Następujące elementy mają prefiks albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzeł 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Zainstalować łącznik SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Nie należy używać łączników nazwy hostów węzły klastra. W przeciwnym razie klaster nie będzie działać. Jest to znane ograniczenie i SUSE pracuje nad poprawką. Poprawki zostaną wydane jako poprawki pakietu — suse — cloud łącznika systemu sap.

   Upewnij się, że zainstalowano nową wersję łącznika klastra SAP SUSE. Stary certyfikat został wywołany sap_suse_cluster_connector i nosi nazwę nowego **klastra łącznika, sap suse w-** .

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]**  SAP aktualizacji zasobu agentów  
   
   Poprawka dla pakietu zasobów agentów jest wymagana do korzystania z nowej konfiguracji opisanej w tym artykule. Możesz sprawdzić, jeśli poprawka jest już zainstalowany za pomocą następującego polecenia

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Dane wyjściowe powinny być podobne do

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Jeśli polecenie grep nie może znaleźć parametr IS_ERS, należy zainstalować poprawkę na [stronę pobierania SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

1. **[A]**  Tworzenia udostępnionych katalogów

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]**  Skonfigurować autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Utwórz plik o

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Usługi Azure Files NetApp obsługuje obecnie tylko NFSv3. Nie pomijaj nfsvers = 3 przełącznik.
   
   Uruchom ponownie `autofs` Zainstaluj nowe udziały
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]**  Skonfigurować wymiany plików

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


### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie SAP NetWeaver ASCS/Wywołujących

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia ASCS

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]**  Zainstalować oprogramowanie SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako główny urząd certyfikacji na pierwszym węźle za pomocą wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>anftstsapvh</b>, <b>10.1.1.20</b> i numer wystąpienia, który był używany przez sondy modułu równoważenia obciążenia, na przykład <b>00</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst. Można użyć parametru SAPINST_USE_HOSTNAME instalacji SAP, za pomocą nazwy wirtualnego hosta.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się utworzyć podfolder w/usr/sap/**QAS**/ASCS**00**, spróbuj ustawić właściciela i grupy ASCS**00** folder, a następnie spróbuj ponownie. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia Wywołujących

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]**  Zainstalować oprogramowanie SAP NetWeaver Wywołujących

   Zainstaluj oprogramowanie SAP NetWeaver Wywołujących jako główny urząd certyfikacji na drugi węzeł, za pomocą wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla Wywołujących, na przykład <b>anftstsapers</b>, <b>10.1.1.21</b> i numer wystąpienia, który był używany przez sondy modułu równoważenia obciążenia, na przykład <b>01</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst. Można użyć parametru SAPINST_USE_HOSTNAME instalacji SAP, za pomocą nazwy wirtualnego hosta.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub nowszej. Niższe wersje nie należy ustawiać uprawnienia poprawnie, a instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się utworzyć podfolder w/usr/sap/**QAS**/ERS**01**, spróbuj ustawić właściciela i grupy Wywołujących**01** folder, a następnie spróbuj ponownie.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]**  Adapt ASCS/SCS i Wywołujących wystąpienia profilów
 
   * Profil ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil Wywołujących

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]**  Skonfigurować podtrzymywaniu

   Komunikacja między serwerem aplikacji SAP NetWeaver i ASCS/SCS odbywa się za pomocą programowego modułu równoważenia obciążenia. Moduł równoważenia obciążenia rozłączy nieaktywnego połączenia po upłynięciu limitu czasu można konfigurować. Aby zapobiec takiej sytuacji należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienianie ustawień systemu Linux. Odczyt [1410736 Uwaga SAP] [ 1410736] Aby uzyskać więcej informacji.

   ASCS/SCS profilu parametr umieścić/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]**  Po instalacji, skonfigurować użytkowników oprogramowania SAP

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]**  Dodaj services ASCS i SAP Wywołujących do pliku sapservice

   Dodaj ASCS usługi wpisu do drugiego węzła i kopiowanie wpisu usługi Wywołujących w pierwszym węźle.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]**  Utworzenie zasobów klastra SAP

Jeśli przy użyciu architektury serwera 1 umieścić w kolejce (ENSA1), zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP wprowadzono obsługę umieścić serwer 2, w tym replikacji od SAP NW 7.52. Począwszy od 1809 platformy ABAP umieścić serwer 2 jest instalowany domyślnie. Zobacz SAP Uwaga [2630416](https://launchpad.support.sap.com/#/notes/2630416) obsługi serwera 2 umieścić w kolejce.
Jeśli przy użyciu architektury serwera 2 umieścić w kolejce ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Jeśli uaktualnienia ze starszej wersji, a przełączanie umieścić serwer 2, zobacz SAP Uwaga [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że kondycja klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera programu SAP NetWeaver aplikacji 

Niektóre bazy danych wymagają, że instalacja wystąpienia bazy danych jest wykonywana na serwerze aplikacji. Przygotowywanie maszyn wirtualnych serwera aplikacji, aby móc używać ich w tych przypadkach.

W poniższych krokach przyjęto założenie, zainstalować serwer aplikacji na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki przedstawione poniżej (np. Konfigurowanie rozpoznawania nazw hostów) nie są potrzebne.

Następujące elementy mają prefiks albo **[A]** — dotyczy to zarówno adresy dostawcy i AAS, **[P]** — dotyczy to tylko PAS lub **[S]** — dotyczy to tylko AAS.


1. **[A]**  Konfiguruj system operacyjny

   Zmniejsz rozmiar zmieniony pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [niska wydajność zapisu w systemie SLES 11/12 serwery z dużą ilość pamięci RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Konfigurowanie rozpoznawania nazw hostów

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. W tym przykładzie pokazano, jak przy użyciu pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach

   ```bash
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  Utwórz katalog sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  Utwórz katalog PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  Utwórz katalog AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Skonfigurować autofs na adresy dostawcy

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik za pomocą

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Uruchom ponownie `autofs` Zainstaluj nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  Skonfigurować autofs na AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik za pomocą

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Uruchom ponownie `autofs` Zainstaluj nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Skonfigurować wymiany plików

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

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie oprogramowanie SAP NetWeaver jest zainstalowany na platformie SAP HANA. Wszystkie obsługiwane bazy danych można użyć dla tej instalacji. Aby uzyskać więcej informacji na temat sposobu instalowania oprogramowania SAP HANA na platformie Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]. Aby uzyskać listę obsługiwanych baz danych, zobacz [1928533 Uwaga SAP][1928533].

* Uruchom program instalacyjny wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako katalogu głównego przy użyciu wirtualnego nazwę hosta, która mapuje do adresu IP z konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja programu SAP NetWeaver aplikacji serwera

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. **[A]**  Przygotowanie serwera aplikacji, wykonaj kroki opisane w rozdziale [przygotowania serwera aplikacji SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

2. **[A]**  Serwera aplikacji zainstalować oprogramowanie SAP NetWeaver zainstalować podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER zezwalający użytkownikowi użytkowników innych niż root nawiązać sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Aktualizacji oprogramowania SAP HANA bezpiecznego magazynu

   Aktualizacja oprogramowania SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualnego ustawień replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>
   hdbuserstore List
   </code></pre>

   To powinno wyświetlić listę wszystkich wpisów i powinien wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Dane wyjściowe pokazują, wskazuje wpis domyślny adres IP maszyny wirtualnej, a nie adres IP modułu równoważenia obciążenia. Ten wpis, trzeba je zmienić i wskazać do wirtualnej nazwy hosta modułu równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w powyższych danych wyjściowych) i nazwę bazy danych (**QAS** w powyższych danych wyjściowych)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testuj ustawienia klastra

Następujące testy są kopią przypadków testowych w [najlepszych praktyk przewodniki systemu SUSE][suse-ha-guide]. Są one kopiowane dla Twojej wygody. Zawsze również przeczytać prowadnice najlepsze praktyki i wykonać wszystkie dodatkowe testy, które mogły zostać dodane.

1. HAGetFailoverConfig, HACheckConfig i HACheckFailoverConfig testu

   Uruchom następujące polecenia jako \<sapsid > adm w węźle, w którym są uruchomione wystąpienie ASCS. Jeśli polecenia zakończyć się NIEPOWODZENIEM: Za mało pamięci, jego może być spowodowany przez myślniki nazwy hosta usługi. Jest to znany problem i zostanie naprawiony przez SUSE w pakiecie — suse — klaster łącznika systemu sap.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Ręcznego przeprowadzania migracji wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako użytkownik główny, aby przeprowadzić migrację wystąpienia ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. HAFailoverToNode testu

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Uruchom następujące polecenia jako \<sapsid > adm, aby przeprowadzić migrację wystąpienia ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Symulowanie awarii węzła 

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenie, jako katalog główny na węźle, gdzie jest uruchomione wystąpienie ASCS

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Jeśli używasz interwencja, program Pacemaker nie powinny automatycznie uruchamiać w węźle zabitych. Stan po uruchomieniu węzła ponownie powinien wyglądać następująco.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Użyj następujących poleceń, uruchom program Pacemaker w węźle zabitych, czyszczenie komunikatów interwencja i czyszczenie zasobów nie powiodło się.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testowanie ręczne ponowne uruchomienie wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Utworzenie blokady umieścić w kolejce za przykład Edycja użytkownika su01 transakcji. Uruchom następujące polecenia jako < sapsid\>adm w węźle, w którym jest uruchomione wystąpienie ASCS. Polecenia spowoduje zatrzymanie wystąpienia ASCS, a następnie uruchom go ponownie. Jeśli przy użyciu architektury serwera 1 umieścić w kolejce, blokady umieścić oczekuje się, utracone w tym teście. Jeśli przy użyciu architektury serwera 2 umieścić w kolejce, umieścić w kolejce zostaną zachowane. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Wystąpienie ASCS powinno być teraz wyłączone w program Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Ponownie uruchom wystąpienie ASCS, w tym samym węźle.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Zablokuj umieścić w kolejce elementu su01 transakcji powinien mieć utraty lub, jeśli przy użyciu architektury replikacji 1 serwer umieścić w kolejce i zaplecza zostało zresetowane. Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Kasowanie procesu serwera wiadomości

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako użytkownik główny, aby zidentyfikować proces serwera wiadomości i zamknij go.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Jeśli możesz tylko kill serwer komunikatów raz, zostanie ono uruchomione przez sapstart. Jeśli go często wystarczająco dużo, program Pacemaker będą ostatecznie kill przenieść to wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako użytkownik główny, aby wyczyścić stanu zasobów wystąpienia ASCS i Wywołujących po przeprowadzeniu testu.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Kasowanie procesu serwera umieścić w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Uruchom następujące polecenia jako użytkownik główny w węźle, gdzie jest uruchomione wystąpienie ASCS, można skasować serwera umieścić w kolejce.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Wystąpienie ASCS powinien natychmiast przełączyć w tryb failover do innego węzła. Wystąpienie Wywołujących powinien również pracy awaryjnej po uruchomieniu wystąpienia ASCS. Uruchom następujące polecenia jako użytkownik główny, aby wyczyścić stanu zasobów wystąpienia ASCS i Wywołujących po przeprowadzeniu testu.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Zakończ proces serwera replikacji umieścić w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenie jako główny w węźle, gdzie jest uruchomione wystąpienie Wywołujących, można skasować proces serwera umieścić w kolejce replikacji.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Jeśli uruchomisz wyłącznie polecenie raz, `sapstart` spowoduje ponowne uruchomienie tego procesu. Jeśli zostanie uruchomione, często `sapstart` nie będzie ponownie uruchomić proces i zasobów będzie w stanie zatrzymania. Uruchom następujące polecenia jako użytkownik główny, aby wyczyścić stanu zasobów wystąpienia Wywołujących po przeprowadzeniu testu.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Kasowanie procesu sapstartsrv umieścić w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako użytkownik główny w węźle, gdzie jest uruchomione ASCS.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Proces sapstartsrv powinien po ponownym uruchomieniu przez program Pacemaker agenta zasobów. Stan zasobu po przeprowadzeniu testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Kolejne kroki

* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP 
* HANA na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na maszynach wirtualnych platformy Azure, zobacz [wysokiej dostępności dla oprogramowania SAP HANA w usłudze Azure Virtual Machines (VMs)][sap-hana-ha]
