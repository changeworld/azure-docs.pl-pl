---
title: Azure maszyny wirtualnej wysoką dostępność dla programu SAP NetWeaver w systemie SUSE Linux Enterprise Server SAP aplikacji | Dokumentacja firmy Microsoft
description: Przewodnik wysokiej dostępności dla programu SAP NetWeaver w systemie SUSE Linux Enterprise Server dla programu SAP aplikacji
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 12efeba68f30aa8723acc32449ae05ffac4c1ac4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658761"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Wysoka dostępność dla programu SAP NetWeaver na maszynach wirtualnych Azure w systemie SUSE Linux Enterprise Server dla programu SAP aplikacji

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
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstalować w ramach klastra i instalowania systemu SAP NetWeaver 7.50 wysokiej dostępności.
W konfiguracji przykład polecenia instalacji itp. Liczby wystąpień ASCS 00 Wywołujących wystąpienia numer 02 oraz NW1 identyfikator systemu SAP jest używany. Nazw zasobów (na przykład maszyn wirtualnych, sieci wirtualne), w tym przykładzie założono, że użyto [zbieżność szablonu] [ template-converged] systemie SAP NW1 identyfikator na tworzenie zasobów.

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

![Informacje o SAP NetWeaver wysokiej dostępności](./media/high-availability-guide-suse/img_001.png)

Serwer systemu plików NFS, SAP NetWeaver ASCS SAP NetWeaver SCS, SAP NetWeaver Wywołujących i bazy danych SAP HANA używać nazwy hosta wirtualnego i wirtualnych adresów IP. Na platformie Azure usługi równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację (A) SCS i Wywołujących modułu równoważenia obciążenia.

### <a name="ascs"></a>(A)SCS

* Konfiguracja serwera sieci Web
  * Adres IP 10.0.0.7
* Konfiguracja zaplecza
  * Połączone z interfejsów sieci podstawowej wszystkich maszyn wirtualnych, które powinny być częścią (A) SCS/Wywołujących klastra
* Port sondy
  * Port 620**&lt;nr&gt;**
* Reguły obciążenia
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;** TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* Konfiguracja serwera sieci Web
  * Adres IP 10.0.0.8
* Konfiguracja zaplecza
  * Połączone z interfejsów sieci podstawowej wszystkich maszyn wirtualnych, które powinny być częścią (A) SCS/Wywołujących klastra
* Port sondy
  * Port 621**&lt;nr&gt;**
* Reguły obciążenia
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Konfigurowanie serwera systemu plików NFS o wysokiej dostępności

SAP NetWeaver wymaga magazynu udostępnionego katalogu transportu i profilu. Odczyt [wysokiej dostępności NFS na maszynach wirtualnych Azure w systemie SUSE Linux Enterprise Server] [ nfs-ha] na temat sposobu konfigurowania serwera NFS dla programu SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie SCS ()

Możesz użyć szablonu Azure z serwisu github do wdrożenia wszystkich wymaganych zasobów Azure, łącznie z maszyn wirtualnych, dostępności ustawić i modułu równoważenia obciążenia lub Ręczne wdrażanie zasobów.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux przy użyciu szablonu Azure

W portalu Azure Marketplace zawiera obraz systemu SUSE Linux Enterprise Server dla 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych. Obrazu z witryny marketplace zawiera agenta zasobów dla programu SAP NetWeaver.

Umożliwia jednego z szablonów Szybki Start w serwisie github wdrażanie wszystkich wymaganych zasobów. Szablon wdraża maszyny wirtualne, usługi równoważenia obciążenia, dostępność ustawić itd. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablon identyfikatora SID Multi ASCS/SCS] [ template-multisid-xscs] lub [zbieżność szablonu] [ template-converged] na platformie Azure portalu ASCS/SCS szablon tylko tworzy reguły równoważenia obciążenia dla programu SAP NetWeaver ASCS/SCS i wystąpień Wywołujących (tylko w systemie Linux) należy konwergentnej szablon tworzy również reguły równoważenia obciążenia dla bazy danych (na przykład Microsoft SQL Server lub SAP HANA). Jeśli planujesz zainstalowanie systemu SAP NetWeaver na podstawie i również chcesz zainstalować bazy danych na tej samej maszyny, użyj [zbieżność szablonu][template-converged].
1. Wprowadź następujące parametry
   1. Prefiks zasobów (tylko szablon identyfikatora SID Multi ASCS/SCS)  
      Wprowadź prefiks, który ma być używany. Wartość jest używana jako prefiksu dla zasobów, które zostały wdrożone.
   3. Identyfikator systemu SAP (tylko szablon konwergentnej)  
      Wprowadź identyfikator systemu SAP systemu SAP, które chcesz zainstalować. Ten identyfikator jest używany jako prefiksu dla zasobów, które zostały wdrożone.
   4. Typ stosu  
      Wybierz typ SAP NetWeaver stosu
   5. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucje systemu Linux. Na przykład wybierz SLES 12 BYOS
   6. Typ bazy danych  
      Wybierz HANA
   7. Rozmiar systemu SAP  
      Ilość protokoły SAP zapewnia nowy system. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, skontaktuj się z partnerem technologii SAP lub Integrator systemu
   8. Dostępność systemu  
      Wybierz opcję wysokiej dostępności
   9. Nazwa użytkownika i hasło administratora  
      Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
   10. Identyfikator podsieci  
   Identyfikator podsieci, do której maszyny wirtualne powinny być podłączone do.  Pozostaw pole puste, jeśli chcesz utworzyć nową sieć wirtualną lub wybierz tej samej podsieci, która użycia lub utworzenia jako część wdrożenia serwera systemu plików NFS. Identyfikator zwykle wygląda /subscriptions/**&lt;identyfikator subskrypcji&gt;**/resourceGroups/**&lt;Nazwa grupy zasobów&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;wirtualnej nazwy sieciowej&gt;**/subnets/**&lt;nazwy podsieci&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie Linux za pomocą portalu Azure

Najpierw należy utworzyć maszyny wirtualnej dla tego klastra systemu plików NFS. Później Utwórz moduł równoważenia obciążenia i używanie maszyn wirtualnych w puli wewnętrznej bazy danych.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Zestaw aktualizacji max domeny
1. Utwórz maszynę wirtualną 1   
   Użyj co najmniej z dodatkiem SP1 z 12 SLES4SAP, w tym obrazie przykład 12 SLES4SAP z dodatkiem SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES dla SAP aplikacji 12 z dodatkiem SP1 jest używany.  
   Wybierz utworzony wcześniej zestawu dostępności  
1. Tworzenie maszyny wirtualnej 2   
   Użyj co najmniej z dodatkiem SP1 z 12 SLES4SAP, w tym obrazie przykład 12 SLES4SAP z dodatkiem SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES dla SAP aplikacji 12 z dodatkiem SP1 jest używany.  
   Wybierz utworzony wcześniej zestawu dostępności  
1. Dodaj co najmniej jeden dysk danych do obydwie maszyny wirtualne  
   Dyski danych są używane dlausr/sap/`<SAPSID`> katalogu
1. Tworzenie modułu równoważenia obciążenia (wewnętrzny)  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.0.0.7 ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu i kliknij przycisk Dodaj
         1. Wprowadź nazwę puli adresów IP frontonu (na przykład **nw1-ascs frontonu**)
         1. Ustawić przypisania jako statyczny i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK
      1. Adres IP 10.0.0.8 Wywołujących ASCS
         * Powtórz powyższe kroki, aby utworzyć adres IP dla Wywołujących (na przykład **10.0.0.8** i **nw1-aers zaplecza**)
   1. Tworzenie puli wewnętrznej bazy danych
      1. Tworzenie puli wewnętrznej bazy danych dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pul zaplecza i kliknij przycisk Dodaj
         1. Wprowadź nazwę dla nowej puli wewnętrznej bazy danych (na przykład **nw1-ascs zaplecza**)
         1. Kliknij przycisk Dodaj maszynę wirtualną.
         1. Wybierz zestaw dostępności utworzoną wcześniej
         1. Wybierz maszyny wirtualne (a) SCS klastra
         1. Kliknij przycisk OK
      1. Tworzenie puli wewnętrznej bazy danych dla Wywołujących ASCS
         * Powtórz powyższe kroki, aby utworzyć pulę zaplecza dla Wywołujących (na przykład **nw1-aers zaplecza**)
   1. Utwórz sondy kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji, a następnie kliknij przycisk Dodaj
         1. Wprowadź nazwę nowego sondy kondycji (na przykład **nw1-ascs-hp**)
         1. Wybierz protokół, port 620 TCP**00**, interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK
      1. Port 621**02** dla ASCS Wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondy kondycji dla Wywołujących (na przykład 621**02** i **nw1-aers-hp**)
   1. Reguły obciążenia
      1. 32**00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **nw1-lb-3200**)
         1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonym wcześniej (na przykład **nw1-ascs frontonu**)
         1. Zachowaj protokołu **TCP**, wprowadź port **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że można włączyć pływającego adresu IP**
         1. Kliknij przycisk OK    
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 i TCP dla ASCS
      1. Dodatkowe porty dla Wywołujących ASCS
         * Powtórz powyższe kroki dla portów 33**02**, 5**02**13, 5**02**14, 5**02**16 i TCP dla Wywołujących ASCS

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznik

Postępuj zgodnie z instrukcjami [Konfigurowanie rozrusznik w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) tworzenia prostej rozrusznik klastra dla tego serwera () SCS.

### <a name="installation"></a>Instalacja

Następujące elementy są poprzedzane prefiksem albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzła 1 lub **[2]** — dotyczy to tylko węzeł 2.

1. **[A]**  Zainstalować łącznik SUSE
   
   <pre><code>
   sudo zypper install sap_suse_cluster_connector
   </code></pre>

1. **[A]**  SAP aktualizacji zasobów agentów  
   
   Poprawka dla pakietu zasobów agentów jest wymagany do użycia nowej konfiguracji, które jest opisane w tym artykule. Można sprawdzić, czy poprawka jest już zainstalowana przy użyciu następującego polecenia

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Dane wyjściowe powinny być podobne do

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Jeśli polecenie grep nie może znaleźć parametru IS_ERS, musisz zainstalować poprawki wymienione na [SUSE stronę pobierania](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

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
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji programu SAP NetWeaver

1. **[A]**  Tworzenia udostępnionych katalogów

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
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

1. **[A]**  Skonfigurować autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Utwórz plik o

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
   </code></pre>

   Uruchom ponownie autofs, aby zainstalować nowe udziały

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

   Uruchom ponownie agenta do aktywowania zmian

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie programu SAP NetWeaver ASCS/Wywołujących

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia ASCS

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że stan klastra jest prawidłowy i czy wszystkie zasoby są uruchomione. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]**  Zainstalować SAP NetWeaver ASCS  

   Instalowanie programu SAP NetWeaver ASCS jako katalog główny na pierwszym węźle, za pomocą wirtualnej nazwy hosta, który jest mapowany na adres IP frontonu modułu równoważenia obciążenia konfiguracji ASCS, na przykład <b>nw1 ascs</b>, <b>10.0.0.7</b> i wystąpienie numer, który był używany przez sondę modułu równoważenia obciążenia, na przykład <b>00</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER, aby zezwolić użytkownikowi inny niż główny nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się do utworzenia podfolderu w usr/sap/**NW1**/ASCS**00**, spróbuj ustawić właściciela i grupy ASCS**00** folderu i spróbuj ponownie.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Tworzenie zasobu wirtualnego adresu IP i sondy kondycji dla wystąpienia Wywołujących

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Upewnij się, że stan klastra jest prawidłowy i czy wszystkie zasoby są uruchomione. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]**  Zainstalować SAP NetWeaver Wywołujących  

   Instalowanie programu SAP NetWeaver Wywołujących jako główny na drugi węzeł, za pomocą wirtualnej nazwy hosta, który jest mapowany na adres IP frontonu modułu równoważenia obciążenia konfiguracji Wywołujących, na przykład <b>nw1 aers</b>, <b>10.0.0.8</b> i wystąpienie numer, który był używany przez sondę modułu równoważenia obciążenia, na przykład <b>02</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER, aby zezwolić użytkownikowi inny niż główny nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Użyj PL SP 20 SWPM 05 lub nowszej. Niższych wersjach nie należy ustawiać uprawnienia poprawnie i instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się do utworzenia podfolderu w usr/sap/**NW1**/ERS**02**, spróbuj ustawić właściciela i grupy Wywołujących**02** folderu i spróbuj ponownie.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adapt ASCS/SCS i Wywołujących wystąpienia profilów
 
   * ASCS/SCS profilu

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
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
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Skonfigurować podtrzymanie aktywności

   Komunikacja między serwerem SAP NetWeaver aplikacji i ASCS/SCS jest kierowany przez programowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po upływie limitu czasu w można konfigurować. Aby zapobiec takiej sytuacji należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS i zmienić ustawienia systemu Linux. Odczyt [1410736 Uwaga SAP] [ 1410736] Aby uzyskać więcej informacji.
   
   ASCS/SCS profilu parametru umieścić/encni/set_so_keepalive został już dodany w ostatnim kroku.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Po instalacji, skonfigurować użytkowników SAP
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  Dodawanie usług ASCS i Wywołujących SAP do pliku sapservice

   Dodaj ASCS usługi wpisu do drugiego węzła i kopiowanie wpisu usługi Wywołujących pierwszy węzeł.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Utworzenie zasobów klastra SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Upewnij się, że stan klastra jest prawidłowy i czy wszystkie zasoby są uruchomione. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowanie serwera programu SAP NetWeaver aplikacji

Niektóre bazy danych wymaga, aby instalacja wystąpienia bazy danych jest wykonywana na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby można było używać ich w tych przypadkach.

Poniższych kroków założono, zainstalować serwer aplikacji na serwerze innym niż serwer ASCS/SCS i HANA. W przeciwnym razie niektóre czynności (np. Konfigurowanie rozpoznawania nazwy hosta) nie są wymagane.

1. Ustawienia rozpoznawania nazwy hosta

   Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład przedstawia sposób użycia pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach
   ```bash
   sudo vi /etc/hosts
   ```
   Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Utwórz katalog sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Skonfiguruj autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik o

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Uruchom ponownie autofs, aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
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

   Uruchom ponownie agenta do aktywowania zmian

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie SAP NetWeaver jest instalowany na SAP HANA. Każdej z obsługiwanych bazy danych można użyć dla tej instalacji. Aby uzyskać więcej informacji na temat instalowania SAP HANA na platformie Azure, zobacz [dostępności wysokiej programu SAP HANA na maszynach wirtualnych Azure (VM)][sap-hana-ha]. Aby uzyskać listę obsługiwanych baz danych, zobacz [1928533 Uwaga SAP][1928533].

1. Uruchom instalację wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako główny przy użyciu wirtualnego nazwy hosta, który jest mapowany na adres IP z konfiguracji serwera sieci Web usługi równoważenia obciążenia dla bazy danych, na przykład <b>nw1-db</b> i <b>10.0.0.13</b>.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER, aby zezwolić użytkownikowi inny niż główny nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera programu SAP NetWeaver aplikacji

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP. 

1. Przygotowanie serwera aplikacji

Wykonaj kroki opisane w tym rozdziale [Przygotowanie serwera aplikacji SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

1. Instalowanie programu SAP NetWeaver serwera aplikacji

   Zainstaluj serwer aplikacje SAP NetWeaver podstawowej lub dodatkowej.

   Można użyć parametru sapinst SAPINST_REMOTE_ACCESS_USER, aby zezwolić użytkownikowi inny niż główny nawiązać sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja SAP HANA bezpiecznego magazynu

   Zaktualizuj bezpiecznego magazynu SAP HANA wskaż nazwę wirtualnego ustawień replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>
   hdbuserstore List
   </code></pre>

   To powinien zawierać wszystkie wpisy i powinien być podobny do
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   Dane wyjściowe zawierają wskazuje wpis domyślny adres IP maszyny wirtualnej, a nie adres IP usługi równoważenia obciążenia. Ten wpis musi zostać zmienione w celu wskaż nazwę hosta wirtualnego usługi równoważenia obciążenia. Pamiętaj używać tego samego portu (**30313** w powyższych danych wyjściowych)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Kolejne kroki
* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP][deployment-guide]
* [Wdrożenia usługi Azure DBMS maszyny wirtualnej dla programu SAP][dbms-guide]
* Aby dowiedzieć się jak ustanowić wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże), zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak nawiązać wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na maszynach wirtualnych Azure, zobacz [dostępności wysokiej programu SAP HANA na maszynach wirtualnych Azure (VM)][sap-hana-ha]
