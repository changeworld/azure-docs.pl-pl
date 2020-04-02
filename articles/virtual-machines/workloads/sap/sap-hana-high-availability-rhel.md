---
title: Wysoka dostępność maszyn wirtualnych SAP HANA na platformie Azure na platformie RHEL | Dokumenty firmy Microsoft
description: Ustanowienie wysokiej dostępności sap HANA na maszynach wirtualnych platformy Azure (maszyny wirtualne).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/31/2020
ms.author: radeltch
ms.openlocfilehash: f1ae2c3c949e8bdbf30c8bef496177d56cd2dcbd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521409"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Wysoka dostępność maszyn wirtualnych SAP HANA na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

W przypadku lokalnego programowania można użyć replikacji systemu HANA lub użyć udostępnionego magazynu w celu ustalenia wysokiej dostępności dla systemu SAP HANA.
Na maszynach wirtualnych platformy Azure (maszyny wirtualne) replikacja systemu HANA na platformie Azure jest obecnie jedyną obsługiwana funkcją wysokiej dostępności.
Sap HANA Replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, instalowania struktury klastra oraz instalowania i konfigurowania replikacji systemu SAP HANA.
W przykładowych konfiguracjach używane są polecenia instalacji, numer wystąpienia **03**i identyfikator systemu HANA **HN1.**

Najpierw przeczytaj następujące uwagi i dokumenty SAP:

* Uwaga SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane oprogramowanie SAP oraz system operacyjny (OS) i kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemów Windows i Linux na platformie Microsoft Azure.
* Uwaga SAP [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure.
* SAP Note [2002167] ma zalecane ustawienia systemu operacyjnego dla systemu Red Hat Enterprise Linux
* SAP Note [2009879] ma wytyczne SAP HANA dla Red Hat Enterprise Linux
* Uwaga SAP [2178632] zawiera szczegółowe informacje na temat wszystkich metryk monitorowania zgłoszonych dla sap na platformie Azure.
* Sap Note [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga SAP [2243692] ma informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Uwaga SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla systemu SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane notatki SAP dla Linuksa.
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP w systemie Linux][dbms-guide]
* [Replikacja systemu SAP HANA w klastrze rozruszników serca](https://access.redhat.com/articles/3004101)
* Ogólna dokumentacja RHEL
  * [Omówienie dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja dodatkami o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Dokumentacja dodatku o wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentacja RHEL specyficzne dla platformy Azure:
  * [Zasady pomocy technicznej dotyczące klastrów wysokiej dostępności RHEL — maszyny wirtualne platformy Microsoft Azure jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie klastra o wysokiej dostępności w systemie Microsoft Azure dla systemu Red Hat Enterprise 7.4 (lub nowszych)](https://access.redhat.com/articles/3252491)
  * [Instalowanie systemu SAP HANA w systemie Red Hat Enterprise Linux do użytku na platformie Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Omówienie

Aby osiągnąć wysoką dostępność, SAP HANA jest zainstalowany na dwóch maszynach wirtualnych. Dane są replikowane przy użyciu replikacji systemu HANA.

![Omówienie wysokiej dostępności sap hana](./media/sap-hana-high-availability-rhel/ha-hana.png)

Konfiguracja replikacji systemu SAP HANA używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

* Konfiguracja front-endu: adres IP 10.0.0.13 dla hn1-db
* Konfiguracja zaplecza: Połączenie z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: Port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Wdrażanie dla systemu Linux

Portal Azure Marketplace zawiera obraz systemu Red Hat Enterprise z systemem Linux 7.4 dla systemu SAP HANA, którego można użyć do wdrożenia nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Można użyć jednego z szablonów szybkiego startu, które znajdują się w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablon bazy danych][template-multisid-db] w witrynie Azure portal.
1. Wprowadź następujące parametry:
    * **Sap System ID**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla zasobów, które są wdrażane.
    * **Typ systemu operacyjnego:** Wybierz jedną z dystrybucji Linuksa. W tym przykładzie wybierz **RHEL 7**.
    * **Typ bazy:** Wybierz **HANA**.
    * **Rozmiar systemu SAP:** Wprowadź liczbę SAPS, które nowy system zamierza zapewnić. Jeśli nie masz pewności, ile saps system wymaga, zapytaj partnera technologii SAP lub integratora systemu.
    * **Dostępność systemu**: Wybierz **HA**.
    * **Nazwa użytkownika administratora, hasło administratora lub klucz SSH:** Tworzony jest nowy użytkownik, którego można użyć do zalogowania się na komputerze.
    * **Identyfikator**podsieci: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, do której zdefiniowano podsieć, do której powinna być przypisana maszyna wirtualna, nazwij identyfikator tej określonej podsieci. Identyfikator zwykle wygląda jak **/subskrypcje/\<identyfikator subskrypcji>/resourceGroups/\<nazwa grupy zasobów>/providers/Microsoft.Network/virtualNetworks/nazwa\<sieci wirtualnej>/podsieci/\<nazwa podsieci>**. Pozostaw pustą, jeśli chcesz utworzyć nową sieć wirtualną

### <a name="manual-deployment"></a>Wdrożenie ręczne

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Utwórz zestaw dostępności.  
   Ustaw domenę aktualizacji max.
1. Tworzenie modułu równoważenia obciążenia (wewnętrznego). Zalecamy [standardowy moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).
   * Wybierz sieć wirtualną utworzoną w kroku 2.
1. Utwórz maszynę wirtualną 1.  
   Użyj co najmniej Red Hat Enterprise Linux 7.4 dla SAP HANA. W tym przykładzie użyto red hat enterprise Linux 7.4 dla SAP HANA obrazu <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Wybierz zestaw dostępności utworzony w kroku 3.
1. Utwórz maszynę wirtualną 2.  
   Użyj co najmniej Red Hat Enterprise Linux 7.4 dla SAP HANA. W tym przykładzie użyto red hat enterprise Linux 7.4 dla SAP HANA obrazu <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Wybierz zestaw dostępności utworzony w kroku 3.
1. Dodawanie dysków z danymi.
1. W przypadku korzystania ze standardowego modułu równoważenia obciążenia wykonaj następujące czynności konfiguracyjne:
   1. Najpierw utwórz pulę adresów IP front-end:

      1. Otwórz moduł równoważenia obciążenia, wybierz **pulę adresów IP frontu**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP front-endu (na przykład **hana-frontend).**
      1. Ustaw **przypisanie** na **Statyczne** i wprowadź adres IP (na przykład **10.0.0.13**).
      1. Kliknij przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP front-end należy zwrócić uwagę na adres IP puli.

   1. Następnie utwórz pulę zaplecza:

      1. Otwórz moduł równoważenia obciążenia, wybierz **pule wewnętrznej bazy**danych i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
      1. Wybierz **pozycję Dodaj maszynę wirtualną**.
      1. Wybierz ** Maszynę wirtualną**.
      1. Wybierz maszyny wirtualne klastra SAP HANA i ich adresy IP.
      1. Wybierz pozycję **Dodaj**.

   1. Następnie utwórz sondę kondycji:

      1. Otwórz moduł równoważenia obciążenia, wybierz **sondy kondycji**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp**).
      1. Wybierz **TCP** jako protokół i port 625**03**. Zachowaj wartość **Interwał** ustawioną na 5, a wartość **progu złej** kondycji ustawiona na 2.
      1. Kliknij przycisk **OK**.

   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **hana-lb**).
      1. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend**, **hana-backend** i **hana-hp**).
      1. Wybierz **porty wysokiej haw**.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.

   > [!Note]
   > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [Łączność publiczna dla maszyn końcowych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

1. Alternatywnie, jeśli scenariusz nakazuje użycie podstawowego modułu równoważenia obciążenia, wykonaj następujące kroki konfiguracji:
   1. Skonfiguruj moduł równoważenia obciążenia. Najpierw utwórz pulę adresów IP front-end:

      1. Otwórz moduł równoważenia obciążenia, wybierz **pulę adresów IP frontu**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP front-endu (na przykład **hana-frontend).**
      1. Ustaw **przypisanie** na **Statyczne** i wprowadź adres IP (na przykład **10.0.0.13**).
      1. Kliknij przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP front-end należy zwrócić uwagę na adres IP puli.

   1. Następnie utwórz pulę zaplecza:

      1. Otwórz moduł równoważenia obciążenia, wybierz **pule wewnętrznej bazy**danych i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
      1. Wybierz **pozycję Dodaj maszynę wirtualną**.
      1. Wybierz zestaw dostępności utworzony w kroku 3.
      1. Wybierz maszyny wirtualne klastra SAP HANA.
      1. Kliknij przycisk **OK**.

   1. Następnie utwórz sondę kondycji:

      1. Otwórz moduł równoważenia obciążenia, wybierz **sondy kondycji**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp**).
      1. Wybierz **TCP** jako protokół i port 625**03**. Zachowaj wartość **Interwał** ustawioną na 5, a wartość **progu złej** kondycji ustawiona na 2.
      1. Kliknij przycisk **OK**.

   1. W przypadku systemu SAP HANA 1.0 należy utworzyć reguły równoważenia obciążenia:

      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**15).
      1. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend).**
      1. Zachowaj **protokół** ustawiony na **TCP**i wprowadź port 3**03**15.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.
      1. Powtórz te kroki dla portu 3**03**17.

   1. W przypadku systemu SAP HANA 2.0 należy utworzyć reguły równoważenia obciążenia dla systemowej bazy danych:

      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**13).
      1. Wybierz adres IP front-endu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend).**
      1. Zachowaj **protokół** ustawiony na **TCP**i wprowadź port 3**03**13.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.
      1. Powtórz te kroki dla portu 3**03**14.

   1. W przypadku sap HANA 2.0 najpierw utwórz reguły równoważenia obciążenia dla bazy danych dzierżawy:

      1. Otwórz moduł równoważenia obciążenia, wybierz **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład hana-lb-3**03**40).
      1. Wybierz adres IP wewnętrznej bazy danych, pulę wewnętrznej bazy danych i sondę kondycji utworzoną wcześniej (na przykład **hana-frontend).**
      1. Zachowaj **protokół** ustawiony na **TCP**i wprowadź port 3**03**40.
      1. Wydłuż **limit czasu bezczynnego** do 30 minut.
      1. Upewnij się, że **włączysz pływający adres IP**.
      1. Kliknij przycisk **OK**.
      1. Powtórz te kroki dla portów 3**03**41 i 3**03**42.

Aby uzyskać więcej informacji na temat wymaganych portów dla sap HANA, przeczytaj rozdział [Połączenia z bazami danych dzierżawców](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) w [przewodniku SAP HANA Tenant Databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) lub [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych TCP na maszynach wirtualnych platformy Azure umieszczonych za modułem równoważenia obciążenia platformy Azure. Włączenie sygnatur czasowych TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [Sondy kondycji modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).
> Zobacz też: UWAGA SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

W tych sekcjach należy wykonać następujące prefiksy:

* **[A]**: Krok dotyczy wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra rozrusznika serca.

1. **[A]** Konfigurowanie układu dysku: **Menedżer woluminów logicznych (LVM)**.

   Zaleca się używanie lvm dla woluminów, które przechowują dane i pliki dziennika. W poniższym przykładzie przyjęto założenie, że maszyny wirtualne mają cztery dyski danych dołączone, które są używane do tworzenia dwóch woluminów.

   Wyświetl listę wszystkich dostępnych dysków:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Przykładowe dane wyjściowe:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Tworzenie woluminów fizycznych dla wszystkich dysków, których chcesz użyć:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz grupę woluminów dla plików danych. Użyj jednej grupy woluminów dla plików dziennika i jednej dla udostępnionego katalogu SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Tworzenie woluminów logicznych. Wolumin liniowy jest tworzony `lvcreate` podczas `-i` używania bez przełącznika. Sugerujemy utworzenie woluminu rozłożonego w celu uzyskania lepszej wydajności we/wy i dostosowanie rozmiarów pasków do wartości udokumentowanych w [konfiguracjach pamięci masowej maszyn wirtualnych SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Argument `-i` powinien być liczbą podstawowych woluminów `-I` fizycznych, a argumentem jest rozmiar paska. W tym dokumencie dla woluminu danych są używane `-i` dwa woluminy fizyczne, więc argument przełącznika jest ustawiony na **2**. Rozmiar paska dla woluminu danych to **256KiB**. Jeden wolumin fizyczny jest używany dla `-i` `-I` woluminu dziennika, więc żadne lub przełączniki nie są jawnie używane dla poleceń woluminu dziennika.  

   > [!IMPORTANT]
   > Użyj `-i` przełącznika i ustaw go na liczbę podstawowej woluminu fizycznego, gdy używasz więcej niż jednego woluminu fizycznego dla każdego woluminu danych, dziennika lub udostępnionego woluminu. Użyj `-I` przełącznika, aby określić rozmiar paska podczas tworzenia woluminu rozłożonego.  
   > Zobacz [konfiguracje magazynu maszyn wirtualnych SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) dla zalecanych konfiguracji magazynu, w tym rozmiarów pasków i liczby dysków.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Utwórz katalogi instalacji i skopiuj UUID wszystkich woluminów logicznych:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Tworzenie `fstab` wpisów dla trzech woluminów logicznych:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Wstaw następujący wiersz `/etc/fstab` do pliku:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Zamontuj nowe woluminy:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurowanie układu dysku: **Dyski zwykłe**.

   W przypadku systemów demonstracyjnych można umieścić dane HANA i pliki dziennika na jednym dysku. Utwórz partycję na /dev/disk/azure/scsi1/lun0 i sformatuj ją za pomocą xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Wstaw ten wiersz w pliku /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Utwórz katalog docelowy i zainstaluj dysk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Konfigurowanie rozpoznawania nazw hostów dla wszystkich hostów.

   Można użyć serwera DNS lub zmodyfikować plik /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwa hosta w następujących poleceniach:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze w pliku /etc/hosts. Zmień adres IP i nazwa hosta, aby dopasować go do środowiska:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL dla konfiguracji HANA

   Skonfiguruj RHEL zgodnie z opisem w SAP Note [2292690] i [2455582] oraz <https://access.redhat.com/solutions/2447641>.

1. **[A]** Instalowanie sap HANA

   Aby zainstalować usługę SAP HANA <https://access.redhat.com/articles/3004101>System Replication, wykonaj .

   * Uruchom program **hdblcm** z DYSKU HANA. Wprowadź następujące wartości w wierszu polecenia:
   * Wybierz instalację: Wprowadź **1**.
   * Wybierz dodatkowe komponenty do instalacji: Wprowadź **1**.
   * Wprowadź ścieżkę instalacji [/hana/shared]: Wybierz enter.
   * Wprowadź nazwę hosta lokalnego [..]: wybierz enter.
   * Czy chcesz dodać dodatkowe hosty do systemu? (r/n) [n]: Wybierz enter.
   * Wprowadź identyfikator systemu SAP HANA: Wprowadź identyfikator SID HANA, na przykład: **HN1**.
   * Wprowadź numer wystąpienia [00]: Wprowadź numer wystąpienia HANA. Wprowadź **03,** jeśli użyto szablonu platformy Azure lub po sekcji ręcznego wdrażania tego artykułu.
   * Wybierz tryb bazy danych / Wprowadź indeks [1]: Wybierz Enter.
   * Wybierz użycie systemu / Wprowadź indeks [4]: Wybierz wartość użycia systemu.
   * Wprowadź lokalizację woluminów danych [/hana/data/HN1]: wybierz enter.
   * Wprowadź lokalizację woluminów dziennika [/hana/log/HN1]: wybierz enter.
   * Czy ograniczyć maksymalną alokację pamięci? [n]: Wybierz enter.
   * Wprowadź nazwę hosta certyfikatu dla hosta '..."' [...]: Wybierz enter.
   * Wprowadź hasło użytkownika (sapadm) agenta hosta SAP: wprowadź hasło użytkownika agenta hosta.
   * Potwierdź hasło użytkownika (sapadm) agenta hosta SAP: wprowadź ponownie hasło użytkownika agenta hosta, aby potwierdzić.
   * Wprowadź administratora systemu (hdbadm) Hasło: Wprowadź hasło administratora systemu.
   * Potwierdź hasło administratora systemu (hdbadm): Wprowadź hasło administratora systemu ponownie, aby potwierdzić.
   * Wprowadź katalog macierzysty administratora systemu [/usr/sap/HN1/home]: wybierz enter.
   * Wprowadź powłokę logowania administratora systemu [/bin/sh]: wybierz enter.
   * Wprowadź identyfikator użytkownika administratora systemu [1001]: wybierz enter.
   * Wprowadź identyfikator grupy użytkowników (sapsys) [79]: Wybierz enter.
   * Wprowadź hasło użytkownika bazy danych (SYSTEM): Wprowadź hasło użytkownika bazy danych.
   * Potwierdź hasło użytkownika bazy danych (SYSTEM): Wprowadź hasło użytkownika bazy danych ponownie, aby potwierdzić.
   * Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: Wybierz enter.
   * Czy chcesz kontynuować? (y/n): Sprawdź poprawność podsumowania. Wprowadź **y,** aby kontynuować.

1. **[A]** Uaktualnij agenta hosta SAP.

   Pobierz najnowsze archiwum agenta hosta SAP z [centrum oprogramowania SAP][sap-swcenter] i uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazać pobrany plik:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** Konfigurowanie zapory

   Utwórz regułę zapory dla portu sondy modułu równoważenia obciążenia platformy Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 2.0

W tych sekcjach należy wykonać następujące prefiksy:

* **[A]**: Krok dotyczy wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra rozrusznika serca.

1. **[A]** Konfigurowanie zapory

   Tworzenie reguł zapory w celu umożliwienia replikacji systemu HANA i ruchu klienta. Wymagane porty są wyświetlane w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Następujące polecenia są tylko przykładem, aby umożliwić HANA 2.0 replikacji systemu i ruchu klienta do bazy danych SYSTEMDB, HN1 i NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Tworzenie bazy danych dzierżawy.

   Jeśli używasz SAP HANA 2.0 lub MDC, utwórz bazę danych dzierżawy dla systemu SAP NetWeaver. Wymień **NW1** na identyfikator SID systemu SAP.

   Wykonaj jako <hanasid\>adm następujące polecenie:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Konfigurowanie replikacji systemu w pierwszym węźle:

   Tworzenie kopii zapasowych baz danych jako\><hanasid adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Skopiuj systemowe pliki PKI do lokacji dodatkowej:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Utwórz lokację główną:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurowanie replikacji systemu w drugim węźle:
    
   Zarejestruj drugi węzeł, aby uruchomić replikację systemu. Uruchom następujące polecenie jako <hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** Sprawdź stan replikacji

   Sprawdź stan replikacji i poczekaj, aż wszystkie bazy danych będą zsynchronizowane. Jeśli stan pozostaje NIEZNANY, sprawdź ustawienia zapory.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 1.0

W tych sekcjach należy wykonać następujące prefiksy:

* **[A]**: Krok dotyczy wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra rozrusznika serca.

1. **[A]** Konfigurowanie zapory

   Tworzenie reguł zapory w celu umożliwienia replikacji systemu HANA i ruchu klienta. Wymagane porty są wyświetlane w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Poniższe polecenia są tylko przykładem, aby umożliwić hana 2.0 replikacji systemu. Dostosuj go do instalacji SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** Utwórz wymaganych użytkowników.

   Uruchom następujące polecenie jako katalog główny. Pamiętaj, aby zastąpić pogrubione ciągi (HANA System ID **HN1** i numer wystąpienia **03)** wartościami instalacji SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Utwórz wpis magazynu kluczy.

   Uruchom następujące polecenie jako katalog główny, aby utworzyć nowy wpis magazynu kluczy:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Tworzenie kopii zapasowej bazy danych.

   Tworzenie kopii zapasowych baz danych jako katalogu głównego:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Jeśli używasz instalacji wielodostępnych, również kopii zapasowej bazy danych dzierżawy:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Konfigurowanie replikacji systemu w pierwszym węźle.

   Utwórz lokację główną jako <\>hanasid adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Konfigurowanie replikacji systemu w węźle pomocniczym.

   Zarejestruj witrynę dodatkową jako <hanasid\>adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra rozrusznika serca

Wykonaj kroki opisane w [sekcji Konfigurowanie rozrusznika serca w systemie Red Hat Enterprise Linux na platformie Azure,](high-availability-guide-rhel-pacemaker.md) aby utworzyć podstawowy klaster rozrusznika serca dla tego serwera HANA.

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra SAP HANA

Zainstaluj agentów zasobów SAP HANA we **wszystkich węzłach**. Upewnij się, aby włączyć repozytorium, które zawiera pakiet.

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Następnie utwórz topologię HANA. Uruchom następujące polecenia w jednym z węzłów klastra rozrusznika serca:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
--clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Następnie utwórz zasoby HANA:

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Upewnij się, że stan klastra jest ok i że wszystkie zasoby są uruchamiane. Nie jest ważne, w którym węźle są uruchomione zasoby.

> [!NOTE]
> Limity czasu w powyższej konfiguracji są tylko przykłady i może być konieczne dostosowanie do określonej konfiguracji HANA. Na przykład może być konieczne zwiększenie limitu czasu rozpoczęcia, jeśli trwa dłużej, aby uruchomić bazę danych SAP HANA.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można przetestować konfigurację. Przed rozpoczęciem testu upewnij się, że rozrusznik serca nie ma żadnych nieudanych akcji (za pośrednictwem stanu pcs), nie ma nieoczekiwanych ograniczeń lokalizacji (na przykład resztki testu migracji) i że HANA jest stan synchronizacji, na przykład z systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testowanie migracji

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Węzeł główny SAP HANA można przeprowadzić, wykonując następujące polecenie:

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

Jeśli ustawisz, `AUTOMATED_REGISTER="false"`to polecenie powinno przeprowadzić migrację węzła głównego SAP HANA i grupy zawierającej wirtualny adres IP do hn1-db-1.

Po zakończeniu migracji wyjście "sudo pcs status" wygląda następująco

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Zasób SAP HANA na hn1-db-0 jest zatrzymany. W takim przypadku skonfiguruj wystąpienie HANA jako pomocnicze, wykonując to polecenie:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które należy ponownie usunąć:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitoruj stan zasobu HANA przy użyciu stanu "pcs". Po uruchomieniu HANA na hn1-db-0, wyjście powinno wyglądać tak

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testowanie agenta ogrodzenia platformy Azure

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Można przetestować konfigurację agenta ogrodzenia platformy Azure, wyłączając interfejs sieciowy w węźle, w którym sap HANA jest uruchomiony jako wzorzec.
Opis sposobu symulowania awarii sieci w [artykule 79523](https://access.redhat.com/solutions/79523) bazy wiedzy Red Hat. W tym przykładzie używamy skryptu net_breaker, aby zablokować cały dostęp do sieci.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Maszyna wirtualna powinna teraz zostać ponownie uruchomiona lub zatrzymana w zależności od konfiguracji klastra.
Jeśli `stonith-action` ustawienie zostanie wyłączone, maszyna wirtualna zostanie zatrzymana, a zasoby zostaną przeniesione do uruchomionej maszyny wirtualnej.

> [!NOTE]
> Może upłynąć do 15 minut, aż maszyny wirtualne ponownie będą w trybie online.

Po ponownym uruchomieniu maszyny wirtualnej zasób SAP HANA nie `AUTOMATED_REGISTER="false"`uruchamia się jako pomocniczy, jeśli ustawisz . W takim przypadku skonfiguruj wystąpienie HANA jako pomocnicze, wykonując to polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stan zasobu po teście:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testowanie ręcznego trybu failover

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Ręczne tryb failover można przetestować, zatrzymując klaster w węźle hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po przemierniu awaryjnym można ponownie uruchomić klaster. Jeśli ustawisz `AUTOMATED_REGISTER="false"`, zasób SAP HANA w węźle hn1-db-0 nie może uruchomić jako pomocniczy. W takim przypadku skonfiguruj wystąpienie HANA jako pomocnicze, wykonując to polecenie:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

Stan zasobu po teście:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
* Aby dowiedzieć się, jak ustalić wysoką dostępność i plan odzyskiwania po awarii sap HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md)
