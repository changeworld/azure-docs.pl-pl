---
title: Instalator programu SAP HANA System replikacji na maszynach wirtualnych platformy Azure (VM) | Dokumentacja firmy Microsoft
description: Ustanów wysokiej dostępności SAP HANA na maszynach wirtualnych platformy Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: 1965438e64af84d0c808b0684f9e81c797193bff
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure (VM)

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

W infrastrukturze lokalnej, możesz użyć albo HANA replikacji systemu lub magazynu udostępnionego ustanowienie wysoką dostępność dla programu SAP HANA.
Na replikacji systemu HANA maszyny wirtualne Azure na platformie Azure jest jedynym obsługiwanym funkcji wysokiej dostępności do tej pory. SAP HANA replikacji składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane w węźle pomocniczym synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowanie maszyn wirtualnych, zainstaluj platformę klaster, zainstalować i skonfigurować replikacji systemu SAP HANA.
W konfiguracji przykład instalacji poleceń itp. liczby wystąpień 03 i HANA systemu identyfikator HN1 jest używany.

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
* [SAP HANA SR wydajności zoptymalizowanych pod kątem scenariusza] [ suse-hana-ha-guide] zawiera wszystkie wymagane informacje, aby skonfigurować lokalne replikacji systemu SAP HANA. Użyj tego podręcznika, jako linii bazowej.

## <a name="overview"></a>Przegląd

Aby osiągnąć wysoką dostępność, SAP HANA jest zainstalowany na dwóch maszyn wirtualnych. Dane są replikowane za pomocą replikacji systemu HANA.

![Omówienie SAP HANA wysokiej dostępności](./media/sap-hana-high-availability/ha-suse-hana.png)

Używa ustawień SAP HANA SR dedykowanych nazwę hosta wirtualnego i wirtualnych adresów IP adresy. Na platformie Azure usługi równoważenia obciążenia jest wymagany do użycia wirtualnego adresu IP. Poniższa lista przedstawia konfigurację usługi równoważenia obciążenia.

* Konfiguracja serwera sieci Web
  * Adres IP 10.0.0.13 hn1-db
* Konfiguracja zaplecza
  * Połączone z interfejsów sieci podstawowej wszystkich maszyn wirtualnych, które powinny być częścią HANA replikacji systemu
* Port sondy
  * Port 62503
* Reguły obciążenia
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Wdrażanie systemu Linux

Agent zasobów dla SAP HANA znajduje się w systemie SUSE Linux Enterprise Server aplikacje SAP.
W portalu Azure Marketplace zawiera obraz systemu SUSE Linux Enterprise Server dla 12 aplikacje SAP, który służy do wdrażania nowych maszyn wirtualnych.

### <a name="deploy-with-template"></a>Wdrażanie za pomocą szablonu
Umożliwia jednego z szablonów Szybki Start w serwisie github wdrażanie wszystkich wymaganych zasobów. Szablon wdraża maszyny wirtualne, usługi równoważenia obciążenia, dostępność ustawić itd. Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablonu bazy danych] [ template-multisid-db] lub [zbieżność szablonu] [ template-converged] w portalu Azure. 
   Szablon bazy danych tylko tworzy reguły równoważenia obciążenia dla bazy danych, natomiast konwergentnej szablon tworzy także reguły równoważenia obciążenia ASCS/SCS i wystąpienia Wywołujących (tylko w systemie Linux). Jeśli planujesz zainstalowanie systemu SAP NetWeaver na podstawie i również chcesz zainstalować wystąpienie ASCS/SCS na tej samej maszyny, użyj [zbieżność szablonu][template-converged].
1. Wprowadź następujące parametry
    1. Identyfikator systemu SAP  
       Wprowadź identyfikator systemu SAP systemu SAP, które chcesz zainstalować. Identyfikator będzie służyć jako prefiks dla zasobów, które zostały wdrożone.
    1. Typ stosu (dotyczy tylko jeśli używasz szablonu konwergentnej)   
       Wybierz typ SAP NetWeaver stosu
    1. Typ systemu operacyjnego  
       Wybierz jedną z dystrybucje systemu Linux. Na przykład wybierz SLES 12.
    1. Typ bazy danych  
       Wybierz HANA
    1. Rozmiar systemu SAP  
       Ilość protokoły SAP, aby zapewnić przechodzi do nowego systemu. Jeśli nie wiadomo jak wiele protokoły SAP wymaga systemu, skontaktuj się z partnerem technologii SAP lub Integrator systemu
    1. Dostępność systemu  
       Wybierz opcję wysokiej dostępności
    1. Nazwa użytkownika i hasło administratora  
       Tworzony jest nowy użytkownik, który może służyć do logowania się do komputera.
    1. Nowy lub istniejący podsieci  
       Określa, czy należy utworzyć nową sieć wirtualną i podsieć lub istniejącą podsieć powinna być używana. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz istniejącą.
    1. Identyfikator podsieci  
    Identyfikator podsieci, do której maszyny wirtualne powinny być podłączone do. Aby połączyć maszynę wirtualną do sieci lokalnej, wybierz podsieć sieci wirtualnej sieci VPN lub usługi Express Route. Identyfikator zwykle wygląda /subscriptions/`<subscription ID`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

### <a name="manual-deployment"></a>Ręczne wdrażanie

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Zestaw aktualizacji max domeny
1. Tworzenie modułu równoważenia obciążenia (wewnętrzny)  
   Wybierz utworzony w drugiej sieci Wirtualnej
1. Utwórz maszynę wirtualną 1  
   Użyj co najmniej z dodatkiem SP1 z 12 SLES4SAP, w tym przykładzie używamy obrazu SLES4SAP 12 z dodatkiem SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES dla SAP 12 z dodatkiem SP2 (Premium)  
   Wybierz utworzony wcześniej zestawu dostępności  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej z dodatkiem SP1 z 12 SLES4SAP, w tym przykładzie używamy obrazu SLES4SAP 12 SP1 BYOS https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES dla SAP 12 z dodatkiem SP2 (Premium)  
   Wybierz utworzony wcześniej zestawu dostępności  
1. Dodawanie dysków z danymi
1. Konfigurowanie usługi równoważenia obciążenia
    1. Utwórz pulę IP frontonu
        1. Otwórz moduł równoważenia obciążenia, wybierz puli adresów IP frontonu i kliknij przycisk Dodaj
        1. Wprowadź nazwę puli adresów IP nowego serwera sieci Web (na przykład frontonu hana)
        1. Ustawić przypisania jako statyczny i wprowadź adres IP (na przykład **10.0.0.13**)
        1. Kliknij przycisk OK
        1. Po utworzeniu puli adresów IP frontonu, zanotuj jego adresu IP
    1. Tworzenie puli wewnętrznej bazy danych
        1. Otwórz moduł równoważenia obciążenia, wybierz pul zaplecza i kliknij przycisk Dodaj
        1. Wprowadź nazwę dla nowej puli wewnętrznej bazy danych (na przykład zaplecze hana)
        1. Kliknij przycisk Dodaj maszynę wirtualną
        1. Wybierz zestaw dostępności utworzoną wcześniej
        1. Wybierz maszyny wirtualne klastra SAP HANA
        1. Kliknij przycisk OK
    1. Tworzenie sondy kondycji
        1. Otwórz moduł równoważenia obciążenia, wybierz sondy kondycji, a następnie kliknij przycisk Dodaj
        1. Wprowadź nazwę nowego sondy kondycji (na przykład hp hana)
        1. Wybierz protokół, port 625 TCP**03**, interwał 5 i próg złej kondycji 2
        1. Kliknij przycisk OK
    1. SAP HANA 1.0: Tworzenie reguły równoważenia obciążenia
        1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
        1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**15)
        1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład hana frontonu)
        1. Zachowaj protokołu TCP, wprowadź port 3**03**15
        1. Zwiększ limit czasu bezczynności do 30 minut
        1. **Upewnij się, że można włączyć pływającego adresu IP**
        1. Kliknij przycisk OK
        1. Powtórz powyższe kroki dla portu 3**03**17
    1. SAP HANA 2.0: Tworzenie reguły dla bazy danych systemu równoważenia obciążenia
        1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
        1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**13)
        1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład hana frontonu)
        1. Zachowaj protokołu TCP, wprowadź port 3**03**13
        1. Zwiększ limit czasu bezczynności do 30 minut
        1. **Upewnij się, że można włączyć pływającego adresu IP**
        1. Kliknij przycisk OK
        1. Powtórz powyższe kroki dla portu 3**03**14
    1. SAP HANA 2.0: Tworzenie reguły dla pierwszej równoważenia obciążenia bazy danych dzierżawy
        1. Otwórz moduł równoważenia obciążenia, wybierz reguły równoważenia obciążenia i kliknij przycisk Dodaj
        1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**40)
        1. Wybierz adres IP frontonu, puli zaplecza i sondy kondycji utworzonego wcześniej (na przykład hana frontonu)
        1. Zachowaj protokołu TCP, wprowadź port 3**03**40
        1. Zwiększ limit czasu bezczynności do 30 minut
        1. **Upewnij się, że można włączyć pływającego adresu IP**
        1. Kliknij przycisk OK
        1. Powtórz powyższe kroki dla portu 3**03**41 i 3**03**42

Aby uzyskać więcej informacji na temat wymagane porty dla SAP HANA odczytu rozdział [połączeń z bazami danych dzierżawy](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) z [baz danych programu SAP HANA dzierżawy](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) przewodnik lub [2388694 Uwaga SAP] [2388694].


## <a name="create-pacemaker-cluster"></a>Tworzenie klastra rozrusznik

Postępuj zgodnie z instrukcjami [Konfigurowanie rozrusznik w systemie SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) utworzyć podstawowy klaster rozrusznik dla tego serwera HANA. Umożliwia także tego samego klastra rozrusznik SAP HANA i SAP NetWeaver (A) SCS.

## <a name="installing-sap-hana"></a>Instalowanie programu SAP HANA

Następujące elementy są poprzedzane prefiksem albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzła 1 lub **[2]** — dotyczy to tylko węzeł 2 rozrusznik klastra.

1. **[A]**  Układ dysku Instalatora
    1. LVM  
       
       Ogólnie zaleca LVM dla woluminów, które przechowują dane i pliki dziennika. Poniższy przykład założenia, że maszyny wirtualne czterech dyskach danych dołączonych, które powinny być używane do tworzenia dwa woluminy.

       Wyświetl listę wszystkich dostępnych dysków
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Przykładowe dane wyjściowe
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Utwórz woluminy fizyczne dla wszystkich dysków, które chcesz użyć.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Utwórz grupę woluminu plików danych, jedna grupa woluminu dla plików dziennika i jeden do udostępnionego katalogu SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
        Utwórz woluminy logiczne. Liniowy wolumin zostanie utworzony przy użyciu lvcreate bez -i przełącznika. Zalecamy, aby utworzyć wolumin rozłożony lepszą wydajność We/Wy, -i argument powinien być taki sam jak numer woluminu fizycznych źródłowej. W tym dokumencie 2 woluminy fizyczne są używane do ilość danych, więc argumentu przełącznika -i 2. 1 fizyczny wolumin jest używany dla woluminu dziennika i dlatego nie - jawnie jest używany przełącznik. Użyj -i przełącznika i Zastąp numer do tej samej podstawowej numer woluminu fizycznego, gdy używasz więcej niż 1 wielkość poszczególnych danych, dziennika lub udostępnione woluminy.

       <pre><code>
       sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Utwórz katalogi instalacji i skopiuj identyfikator UUID wszystkie woluminy logiczne
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Tworzenie wpisów fstab trzy logiczne woluminów
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Wstaw ten wiersz, aby /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Zainstaluj nowe woluminy
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Zwykły dysków  
       Wersja demonstracyjna dla systemów można umieścić HANA plików danych i dziennika na jednym dysku. Poniższe polecenia utworzyć partycję na /dev/disk/azure/scsi1/lun0 i sformatuj go przy xfs.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Wstaw ten wiersz, aby /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Utwórz katalog docelowy i zainstalować dysk.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]**  Instalatora rozpoznawania nazwy hosta dla wszystkich hostów  
    Można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład przedstawia sposób użycia pliku/etc/hosts.
   Zastąp adres IP i nazwy hosta w poniższych poleceniach
    ```bash
    sudo vi /etc/hosts
    ```
    Wstaw następujące wiersze do/etc/hosts. Zmienianie adresu IP i nazwy hosta do danego środowiska    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Zainstalować HANA HA pakietów  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Aby zainstalować replikacji systemu SAP HANA, wykonaj rozdział 4 przewodnika SAP HANA SR wydajności zoptymalizowanych pod kątem scenariusza w https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/

1. **[A]**  Uruchamiania z dysku DVD HANA hdblcm
    * Wybierz opcję instalacji -> 1
    * 1 -> Wybierz dodatkowe składniki do instalacji
    * Wprowadź ścieżkę instalacji [/ hana/udostępnione]: -> ENTER
    * Wprowadź nazwę hosta lokalnego [.]: -> ENTER
    * Czy chcesz dodać dodatkowe hosty systemu? (t/n) [n]: -> ENTER
    * Wprowadź identyfikator systemu SAP HANA: <SID of HANA e.g. HN1>
    * Wprowadź liczby wystąpień [00]:   
  Liczby wystąpień HANA. Użyj 03, jeśli używany szablon Azure i stosować ręcznego wdrażania
    * Wybierz tryb bazy danych / wprowadź indeks [1]: -> ENTER
    * Użycie systemu wybierz / Wprowadź indeks [4]:  
  Wybierz system użycia
    * Wprowadź lokalizację woluminów danych [/ hana/data/HN1]: -> ENTER
    * Wprowadź lokalizację woluminy dziennika [/ HN1-hana/dziennika]: -> ENTER
    * Ogranicz maksymalną wielkość pamięci alokacji? [n]: -> ENTER
    * Wprowadź nazwę hosta certyfikat dla hosta "..." [...]: -> ENTER
    * Wprowadź nazwę użytkownika agenta hosta SAP (sapadm) hasło:
    * Upewnij się, SAP hosta Agent użytkownika (sapadm) hasło:
    * Wprowadź administratora systemu (hdbadm) hasło:
    * Upewnij się, Administrator systemu (hdbadm) hasło:
    * Wprowadź katalog macierzysty administratora systemu [/ usr/sap/HN1/głównej]: -> ENTER
    * Wprowadź powłoka logowania administratora systemu [/ bin/sh]: -> ENTER
    * Wprowadź identyfikator użytkownika administratora [1001]: -> ENTER
    * Wprowadź identyfikator użytkownika grupy (sapsys) [79]: -> ENTER
    * Wprowadź hasło użytkownika (SYSTEM) bazy danych:
    * Potwierdź hasło użytkownika (SYSTEM) bazy danych:
    * Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: -> ENTER
    * Czy chcesz kontynuować? (t/n):   
  Sprawdź poprawność podsumowania, a następnie wprowadź t, aby kontynuować

1. **[A]**  Uaktualnij agenta hosta SAP  
  Pobierz najnowsze archiwum SAP Agent hosta z [SAP Softwarecenter] [ sap-swcenter] i uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżki do archiwum, aby wskazywał pobranego pliku.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji programu SAP HANA systemu 2.0

Następujące elementy są poprzedzane prefiksem albo **[A]** — mające zastosowanie do wszystkich węzłów, **[1]** — dotyczy to tylko węzła 1 lub **[2]** — dotyczy to tylko węzeł 2 rozrusznik klastra.

1. **[1]**  Tworzenia dzierżawy bazy danych

   Jeśli korzystasz z programu SAP HANA w wersji 2.0 lub MDC, należy utworzyć bazę danych dzierżawy systemu SAP NetWeaver. Zastąp NW1 identyfikatora SID systemu SAP.

   Zaloguj się jako `<hanasid`> adm, a następnie wykonaj następujące polecenie

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Konfigurowanie replikacji systemu na pierwszym węźle
   
   Zaloguj się jako `<hanasid`> adm i kopia zapasowa bazy danych

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Skopiuj pliki infrastruktury kluczy publicznych systemu do dodatkowej

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Tworzenie lokacji głównej.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Konfigurowanie replikacji systemu na drugi węzeł
    
    Zarejestruj drugi węzeł, aby uruchomić replikację systemu. Zaloguj się jako `<hanasid`> adm i uruchom następujące polecenie

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji programu SAP HANA 1.0 systemu

1. **[1]**  Tworzenia wymaganych użytkowników

    Zaloguj się jako katalogu głównego i uruchom następujące polecenie. Upewnij się, że Zastąp bold ciągów (HANA systemu identyfikator HN1 i numer wystąpienia 03) z wartościami instalację programu SAP HANA.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]**  Utworzyć wpis magazynu kluczy
   
    Zaloguj się jako katalogu głównego i uruchom następujące polecenie, aby utworzyć nowy wpis magazynu kluczy.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]**  Kopii zapasowej bazy danych

   Zaloguj się jako katalogu głównego i kopia zapasowa bazy danych

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Jeśli używasz instalacji wielodostępnej również kopię zapasową bazy danych dzierżawy

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Konfigurowanie replikacji systemu na pierwszym węźle
    
    Zaloguj się jako `<hanasid`> adm i Utwórz lokacji głównej.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Konfigurowanie replikacji systemu na węźle pomocniczym.

    Zaloguj się jako `<hanasid`> adm i zarejestruj lokacji dodatkowej.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Utworzenie zasobów klastra SAP HANA

   Najpierw utwórz topologii HANA. Uruchom następujące polecenia w jednym z węzłów klastra rozrusznik.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Następnie należy utworzyć zasoby HANA.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Upewnij się, że stan klastra jest prawidłowy i czy wszystkie zasoby są uruchomione. Nie jest to ważne w węźle, które zasoby są uruchomione.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Konfiguracja klastra testu
W tym rozdziale opisano, jak można przetestować konfigurację. Każdy test założono, że są głównymi i wzorzec SAP HANA jest uruchomiona na maszynie wirtualnej hn1-db-0.

#### <a name="fencing-test"></a>Test ogrodzenia

Można przetestować instalacji agenta ogrodzenia, wyłączając interfejs sieciowy na węzeł hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

Teraz Pobierz ponownie uruchomiona lub zatrzymana w zależności od konfiguracji klastra maszyny wirtualnej.
Jeśli ustawisz akcji stonith wyłączone, maszyna wirtualna ma zostać zatrzymana i zasoby są migrowane do uruchomionej maszyny wirtualnej.

Po ponownym uruchomieniu maszyny wirtualnej SAP HANA zasobu nie można uruchomić jako dodatkowej po ustawieniu AUTOMATED_REGISTER = "false". W takim przypadku należy skonfigurować wystąpienie HANA jako dodatkowej, wykonując polecenie:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Testowania ręcznego przełączania trybu failover

Ręcznego przełączania trybu failover można przetestować, zatrzymując usługę rozrusznik na węzeł hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Po przejściu w tryb failover należy ponownie uruchomić usługę. Jeśli ustawisz AUTOMATED_REGISTER = "false", z zasobem hn1-db-0 kończy się niepowodzeniem, można uruchomić jako dodatkowej SAP HANA. W takim przypadku należy skonfigurować wystąpienie HANA jako dodatkowej, wykonując polecenie:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Testowanie migracji

Węzeł główny SAP HANA można migrować, wykonując następujące polecenie
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Jeśli ustawisz AUTOMATED_REGISTER = "false", ta sekwencja poleceń należy zmigrować węzła głównego SAP HANA i grupy, która zawiera wirtualny adres IP hn1-db-1.
Zasób SAP HANA hn1-db-0 nie można uruchomić jako pomocnicze. W takim przypadku należy skonfigurować wystąpienie HANA jako dodatkowej, wykonując polecenie:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które muszą zostać usunięte ponownie.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Należy wyczyścić stanu zasobu węzła pomocniczego

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Kolejne kroki
* [Azure maszyn wirtualnych, planowania i wdrażania dla programu SAP][planning-guide]
* [Maszyny wirtualne Azure wdrożenia SAP][deployment-guide]
* [Wdrożenia usługi Azure DBMS maszyny wirtualnej dla programu SAP][dbms-guide]
* Aby dowiedzieć się jak ustanowić wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże), zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md). 
